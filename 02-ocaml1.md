# 関数型プログラミングへの招待(2)

## OCaml とは

- [ocaml.org](https://ocaml.org/learn/description.html)
- [Cheat sheets](https://ocaml.org/docs/cheat_sheets.html)
- [Code examples](https://ocaml.org/learn/taste.html)
- [基礎力がつく99のクイズ](https://ocaml.org/learn/tutorials/99problems.html)
- [基本的なチュートリアル (The core languages)](http://caml.inria.fr/pub/docs/manual-ocaml/coreexamples.html): 授業はこのページに沿ってやります。

## Basics

- 式
        1+2*3
- 名前の導入(`let`)、浮動小数点数
        let pi = 4.0 *. atan 1.0
- 関数の定義
        let square x = x *. x
- 関数呼び出しと標準関数(*sin*, *cos*)
        square (sin pi) +. square (cos pi)
- 型検査の失敗
        1.0 * 2
- 再帰的関数も簡単(`let rec`)
        let rec fib n =
          if n < 2 then n else fib (n-1) + fib (n-2)
          fib 10

## Data types

- 論理値(`true` and `false`)と論理型(`bool`)

    (1 < 2) = false

- 文字と文字型(`char`)
        'a'
- 文字列と文字列型(`string`)
        "Hello world"
- リストとリスト型(`... list`)
        let l = ["is"; "a"; "tale"; "told"; "etc."]
- リスト構成子(`::`)
        "Life" :: l
- パターンマッチ、（しかも、いきなり）ソーティング
        let rec sort lst =
          match lst with
            [] -> []
          | head :: tail -> insert head (sort tail)
        and insert elt lst =
          match lst with
            [] -> [elt]
          | head :: tail -> if elt <= head then elt :: lst else head :: insert elt tail
        sort l

## Functions as values

- `deriv`: 数値微分、`function`式は関数（λ式）を構成します。
        let deriv f dx = function x -> (f (x +. dx) -. f x) /. dx
- `sin'`: 正弦の微分、`'`も関数名に使ってよいことに注意
        let sin' = deriv sin 1e-6
        sin' pi
- 関数合成(`compose`)
        let compose f g = function x -> f (g x)
- `cos2`は余弦関数の二乗
        let cos2 = compose square cos
- 引数に関数を取る関数を高階関数(*higher-order function*)と呼びます。標準関数のList.mapはリストに適用するための関数を取ります。
        List.map (function n -> n * 2 + 1) [0;1;2;3;4]
- `map`を時前で定義するのも簡単
        let rec map f l =
          match l with
            [] -> []
        | hd :: tl -> f hd :: map f tl

# Records and variants
