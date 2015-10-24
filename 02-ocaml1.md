# Introduction to OCaml

- Expressions
- Names
- Types
- Functions

## OCaml とは

- [ocaml.org](https://ocaml.org/learn/description.html)
- [Cheat sheets](https://ocaml.org/docs/cheat_sheets.html)
- [Code examples](https://ocaml.org/learn/taste.html)
- [基礎力がつく99のクイズ](https://ocaml.org/learn/tutorials/99problems.html)
- [基本的なチュートリアル (The core languages)](http://caml.inria.fr/pub/docs/manual-ocaml/coreexamples.html): 授業はこのページに沿ってやります。

## Basics

式

```
1+2*3
```

名前の導入(`let`)、浮動小数点数

```
let pi = 4.0 *. atan 1.0
```

関数の定義

```
let square x = x *. x
```

関数呼び出しと標準関数(*sin*, *cos*)

```
square (sin pi) +. square (cos pi)
```

型検査の失敗

```
1.0 * 2
```

再帰的関数も簡単(`let rec`)

```
let rec fib n =
  if n < 2 then n else fib (n-1) + fib (n-2)
  fib 10
```

## Data types

論理値(`true` and `false`)と論理型(`bool`)
```
(1 < 2) = false
```

文字と文字型(`char`)
```
'a'
```

文字列と文字列型(`string`)
```
"Hello world"
```

リストとリスト型(`... list`)

```
let l = ["is"; "a"; "tale"; "told"; "etc."]
```

リスト構成子(`::`)

```
"Life" :: l
```

パターンマッチ、（しかも、いきなり）ソーティング！

```
let rec sort lst =
  match lst with
    [] -> []
  | head :: tail -> insert head (sort tail)
and insert elt lst =
  match lst with
    [] -> [elt]
  | head :: tail -> if elt <= head then elt :: lst else head :: insert elt tail
sort l
```

## Functions as values

`deriv`: 数値微分、`function`式は関数（λ式）を構成します。

```
let deriv f dx = function x -> (f (x +. dx) -. f x) /. dx
```

`sin'`: 正弦の微分、`'`も関数名に使ってよいことに注意しましょう。

```
let sin' = deriv sin 1e-6
sin' pi
```

関数合成(`compose`)は高階関数の典型例です。

```
let compose f g = function x -> f (g x)
```

`cos2`は余弦関数の二乗を意味しています。

```
let cos2 = compose square cos
```

引数に関数を取る関数を高階関数(*higher-order function*)と呼びます。標準関数のList.mapはリストに適用するための関数を取ります。

```
List.map (function n -> n * 2 + 1) [0;1;2;3;4]
```

`map`を時前で定義するのも簡単です。

```
let rec map f l =
match l with
  [] -> []
  | hd :: tl -> f hd :: map f tl
```

# Records

複数の値から構成される複合的な値を表す型としてレコードがあります。（より簡単な型として組(*tuple*)もあるがそれは後述します）

```
type ratio = {num: int; denom: int}
```

レコードの要素を参照するには`.フィールド名`という形式を用います。

```
let add_ratio r1 r2 =
  {num = r1.num * r2.denom + r2.num * r1.denom;
   denom = r1.denom * r2.denom}
```

レコードの値(`{num=1; denom=3}`など)はその場で簡単に作成して、関数に渡せます。

```
add_ratio {num=1; denom=3} {num=2; denom=5}
```

# Variants

Variant型はタグつきunionと呼ばれる。たとえば、以下の定義でタグは`Int`、`Float`、`Error`の三種類で前二者はそれぞれ`int`型と`float`型の引数をとる。ここで定義された`number`型の値としては`Int 7`、`Float 3.14`、`Error`などがあります。

```
type number = Int of int | Float of float | Error
```

引数をとらないVariant型はC言語の列挙型(*enum*)と思って下さい。

```
type sign = Positive | Negative
```

Variantsも普通の値なので、関数の返り値にとることができます。

```
let sign_int n = if n >= 0 then Positive else Negative
```

引数に与えられたVariantを区別するにはパターンマッチを用います。

```
let add_num n1 n2 =
  match (n1, n2) with
    (Int i1, Int i2) ->
      (* Check for overflow of integer addition *)
      if sign_int i1 = sign_int i2 && sign_int (i1 + i2) <> sign_int i1
      then Float(float i1 +. float i2)
      else Int(i1 + i2)
  | (Int i1, Float f2) -> Float(float i1 +. f2)
  | (Float f1, Int i2) -> Float(f1 +. float i2)
  | (Float f1, Float f2) -> Float(f1 +. f2)
  | (Error, _) -> Error
  | (_, Error) -> Error
```

さて、うまく定義できたかな？

```
add_num (Int 123) (Float 3.14159)
```

木構造の定義はほかの言語では面倒なこともありますがOCamlでは以下のように簡単です。

```
type 'a btree = Empty | Node of 'a * 'a btree * 'a btree
```

木構造を扱う関数もパターンマッチで書けば、これまた簡単です。

```
let rec member x btree =
  match btree with
    Empty -> false
  | Node(y, left, right) ->
      if x = y then true else
      if x < y then member x left else member x right
```

```
let rec insert x btree =
  match btree with
    Empty -> Node(x, Empty, Empty)
  | Node(y, left, right) ->
      if x <= y then Node(y, insert x left, right)
                else Node(y, left, insert x right)
```

-----

#Assignments

`assignment1.ml`というファイルに以下の課題の答を記述し、OCW-i を介して提出しなさい。〆切はOCW-iに記載する。

Question should be addressed on [a Github issue page](https://github.com/wakita/fp2015/issues/1) (you need a Github account to do so).

## Assignment 1: Tree manipulation

`btree`型の木について、深さを与える関数`depth`を定義しなさい。

Give a definition of a function `depth` that takes a `btree`-typed tree and gives its depth.

## Assignment 2: Gray code

*n*-bitのGray codeは以下のように定められている。この規則性を見つけ、gray関数を定義しなさい。

A sequence of the *n*-bit gray code is illustrated below.  Figure out the rule that generates this sequence and describe in OCaml.

gray 0 = [[]]
gray 1 = [[0]; [1]]
gray 2 = [[0; 0]; [0; 1]; [1; 1]; [1; 0]]
gray 3 = [[0; 0; 0]; [0; 0; 1]; [0; 1; 1]; [0; 1; 0]; [1; 1; 0]; [1; 1; 1]; [1; 0; 1]; [1; 0; 0]]

## Assignment 3: every2

`btree`に登録された`'a`型の値のうち、小さいものから奇数番目のものを小さい順に並べたリストを与える関数`every2`を定義しなさい。木に登録された値は相異なるものと仮定してよい．

Define a function named `every2` that takes a tree of `'a bteee` and gives a list that consists of the odd positions of the tree leaves.

```
every2: 'a btree -> 'a list
```
