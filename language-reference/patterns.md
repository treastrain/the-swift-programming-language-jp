# Patterns \(パターン\)

最終更新日: 2021/6/20

パターンは、単一の値または複合値の構造を表します。例えば、タプル `(1, 2)` の構造は、2 つの要素のカンマ区切りのリストです。パターンは特定の値ではなく値の構造を表すため、様々な値と一致させることができます。例えば、パターン `(x, y)` は、タプル `(1, 2)` およびその他の 2 つの要素のタプルと一致します。パターンを値と照合することに加えて、複合値の一部または全てを抽出し、各部分を定数または変数名にバインドできます。

Swift には、2 つの基本的な種類のパターンがあります: 任意の種類の値に正常に一致するパターンと、実行時に指定された値に一致しない可能性のあるパターンです。

最初の種類のパターンは、シンプルな変数、定数、およびオプショナルバインディングの値を破棄するために使用されます。これらには、ワイルドカードパターン、識別子パターン、およびそれらを含む値バインディングまたはタプルパターンが含まれます。これらのパターンの型注釈を指定して、特定の型の値にのみ一致するようにパターンを制約できます。

2 番目の種類のパターンは、完全なパターンマッチングに使用されます。この場合、照合しようとしている値が実行時に存在しない可能性があります。これらには、列挙型のケースパターン、オプショナルパターン、式パターン、および型キャストパターンが含まれます。これらのパターンは、`switch` 文の `case` ラベル、`do` 文の `catch` 句、または `if`、`while`、`guard`、または `for-in` 文の `case` 条件で使用できます。

> GRAMMAR OF A PATTERN  
> pattern → [wildcard-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_wildcard-pattern)  [type-annotation](https://docs.swift.org/swift-book/ReferenceManual/Types.html#grammar_type-annotation)<sub>*opt*</sub>  
> pattern → [identifier-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_identifier-pattern)  [type-annotation](https://docs.swift.org/swift-book/ReferenceManual/Types.html#grammar_type-annotation)<sub>*opt*</sub>  
> pattern → [value-binding-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_value-binding-pattern)  
> pattern → [tuple-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_tuple-pattern)  [type-annotation](https://docs.swift.org/swift-book/ReferenceManual/Types.html#grammar_type-annotation)<sub>*opt*</sub>  
> pattern → [enum-case-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_enum-case-pattern)  
> pattern → [optional-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_optional-pattern)  
> pattern → [type-casting-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_type-casting-pattern)  
> pattern → [expression-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_expression-pattern)

## Wildcard Pattern\(ワイルドカードパターン\)

ワイルドカードパターンは、全ての値に一致して無視し、アンダースコア(`_`)で構成されます。一致する値を気にしない場合は、ワイルドカードパターンを使用します。例えば、次のコードは、ループの各反復での範囲の現在の値を無視して、Closed 範囲 `1...3` を反復します。

```swift
for _ in 1...3 {
     // 何かを 3 回行います。
}
```

> GRAMMAR OF A WILDCARD PATTERN  
> wildcard-pattern → `_`

## Identifier Pattern\(識別子パターン\)

識別子パターンは任意の値と一致し、一致した値を変数名または定数名にバインドします。例えば、次の定数宣言では、`someValue` は `Int` 型の値 `42` に一致する識別子パターンです。

```swift
let someValue = 42
```

一致すると、値 `42` が定数名 `someValue` にバインド(代入)されます。

変数または定数宣言の左側のパターンが識別子パターンの場合、識別子パターンは暗黙的に値バインドパターンのサブパターンになります。

> GRAMMAR OF AN IDENTIFIER PATTERN  
> identifier-pattern → [identifier](https://docs.swift.org/swift-book/ReferenceManual/LexicalStructure.html#grammar_identifier)

## Value-Binding Pattern\(値バインディングパターン\)

値バインディングパターンは、一致した値を変数名または定数名にバインドします。例えば、一致した値を定数の名前にバインドする値バインドパターンは、`let` キーワードで始まります。変数の名前にバインドするものは、`var` キーワードで始まります。

値バインドパターン内の識別子パターンは、一致する値を新しい名前付き変数または定数ににバインドします。例えば、タプルの要素を分解し、各要素の値を対応する識別子パターンにバインドできます。

```swift
let point = (3, 2)
switch point {
     // x と y をポイントの要素にバインドします
case let (x, y):
    print("The point is at (\(x), \(y)).")
}
// "The point is at (3, 2)."
```

上記の例では、タプルパターン `(x, y)` の各識別子パターンに `let` が適用されます。このため、`switch` ケースの `case let (x, y):` と `case (let x, let y):` は同じ値に一致します。

> GRAMMAR OF A VALUE-BINDING PATTERN  
> value-binding-pattern → `var` [pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_pattern) \|  `let` [pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_pattern)

## Tuple Pattern\(タプルパターン\)

タプルパターンは、括弧内に囲まれた 0 個以上のパターンのカンマ区切りのリストです。タプルパターンに対応するタプル型の値を一致させます。

型注釈を使用して、特定の種類のタプル型を一致させるために、タプルパターンを制約することができます。例えば、タプルパターン `(x, y): (Int, Int)` は、定数宣言 `let (x, y): (Int, Int) = (1, 2)` の両方の要素が `Int` 型のタプルにのみ一致します。

タプルパターンが `for-in` 文のパターンとして、変数または定数宣言のパターンとして使用される場合は、ワイルドカードパターン、識別子パターン、オプショナルパターン、またはそれらを含む他のタプルパターンのみを含めることができます。例えば、タプルパターン `(x, 0)` の要素 `0` が式パターンのため、次のコードは有効ではありません。

```swift
let points = [(0, 0), (1, 0), (1, 1), (2, 0), (2, 1)]
// このコードは無効です
for (x, 0) in points {
    /* ... */
}
```

単一の要素を含むタプルパターンの括弧は効果がありません。パターンはその単一要素の型の値と一致します。例えば、下記は同等です:

```swift
let a = 2        // a: Int = 2
let (a) = 2      // a: Int = 2
let (a): Int = 2 // a: Int = 2
```

> GRAMMAR OF A TUPLE PATTERN  
> tuple-pattern → `(` [tuple-pattern-element-list](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_tuple-pattern-element-list)<sub>*opt*</sub> `)`  
> tuple-pattern-element-list → [tuple-pattern-element](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_tuple-pattern-element) \|  [tuple-pattern-element](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_tuple-pattern-element)  `,` [tuple-pattern-element-list](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_tuple-pattern-element-list)  
> tuple-pattern-element → [pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_pattern) \|  [identifier](https://docs.swift.org/swift-book/ReferenceManual/LexicalStructure.html#grammar_identifier)  `:` [pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_pattern)

## Enumeration Case Pattern\(列挙型のケースパターン\)

列挙型ケースパターンは、既存の列挙型のケースと一致します。列挙型ケースパターンは、`switch` 文のケースラベル、および `if`、`guard`、および `for-in` 文の条件で使用できます。

一致させようとしている列挙ケースに関連値がある場合、対応する列挙型ケースパターンは、関連値に対して 1 つの要素を含むタプルパターンを指定する必要があります。関連値を含む列挙ケースを一致させるために `switch` 文を使用する例については、[Associated Values](./../language-guide/enumerations.md#associated-values関連値)を参照ください。

列挙型ケースパターンは、オプショナルでラップされたケースの値にも一致します。この単純化された構文を使用すると、オプショナルのパターンを省略できます。なお、`Optional` は列挙型として実装されているため、`.none`、`.some` は、列挙型のケースと同じ switch の中で使用できます。

```swift
enum SomeEnum { case left, right }
let x: SomeEnum? = .left
switch x {
case .left:
    print("Turn left")
case .right:
    print("Turn right")
case nil:
    print("Keep going straight")
}
// "Turn left"
```

> GRAMMAR OF AN ENUMERATION CASE PATTERN  
> enum-case-pattern → [type-identifier](https://docs.swift.org/swift-book/ReferenceManual/Types.html#grammar_type-identifier)<sub>*opt*</sub> `.` [enum-case-name](https://docs.swift.org/swift-book/ReferenceManual/Declarations.html#grammar_enum-case-name)  [tuple-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_tuple-pattern)<sub>*opt*</sub>

## Optional Pattern\(オプショナルパターン\)

オプショナルパターンは、`Optional<Wrapped>` 列挙型の `some(Wrapped)` ケースにラップされた値と一致します。オプショナルパターンは識別子パターンの直後に疑問符を置くことで構成され、列挙型ケースパターンと同じ場所に使用できます。

オプショナルパターンはオプショナルの列挙型ケースパターンの糖衣構文(シンタックスシュガー)のため、次のものは同等です:

```swift
let someOptional: Int? = 42
// 列挙型ケースパターンを使用して一致します
if case .some(let x) = someOptional {
    print(x)
}

// オプショナルのパターンを使用して一致します
if case let x? = someOptional {
    print(x)
}
```

オプショナルパターンは、`for-in` 文内のオプショナルの配列を繰り返し処理する便利な方法を提供し、`nil` 以外の要素に対してのみループの本文を実行します。

```swift
let arrayOfOptionalInts: [Int?] = [nil, 2, 3, nil, 5]
// nil ではない値にのみ一致します
for case let number? in arrayOfOptionalInts {
    print("Found a \(number)")
}
// Found a 2
// Found a 3
// Found a 5
```

> GRAMMAR OF AN OPTIONAL PATTERN  
> optional-pattern → [identifier-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_identifier-pattern)  `?`

## Type-Casting Patterns\(型キャストパターン\)

2 つのタイプの型キャストパターン、`is` パターンと `as` パターンがあります。`is` パターンは `switch` 文のケースラベルにのみ使用できます。`is` と `as` パターンの形式は次のとおりです。

![型キャストパターン](./../.gitbook/assets/type-casting_patterns.png)

`is` パターンは、実行時の値の型が、`is` パターンの右側で指定された型、またはその型のサブクラスと同じ型の場合に、値と一致します。`is` パターンは、両方とも型キャストを実行しますが、返された型を破棄するという点で、`is` 演算子のように動作します。

`as` パターンは、実行時の値の種類が、`as` パターンの右側に指定された型、またはその型のサブクラスと同じ型の場合に、値と一致します。一致した場合、値の種類は、`as` パターンの右側に指定されたパターンにキャストされます。

`switch` 文を使用して `is` パターンを一致させる例については、[Type Casting for Any and AnyObject](./../language-guide/type-casting.md#type-casting-for-any-and-anyObjectAnyおよびAnyObjectの型キャスト)を参照ください。

> GRAMMAR OF A TYPE CASTING PATTERN  
> type-casting-pattern → [is-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_is-pattern) \|  [as-pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_as-pattern)  
> is-pattern → `is` [type](https://docs.swift.org/swift-book/ReferenceManual/Types.html#grammar_type)  
> as-pattern → [pattern](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_pattern)  `as` [type](https://docs.swift.org/swift-book/ReferenceManual/Types.html#grammar_type)

## Expression Pattern\(式パターン\)

式パターンは式の値を表します。式パターンは `switch` 文のケースにのみ使用できます。

式パターンで表される式は、Swift 標準ライブラリ `~=` 演算子を使用して入力式の値と比較されます。`~=` 演算子が `true` を返すと、一致します。デフォルトでは、`~=` 演算子は `==` 演算子を使用して同じ型の 2 つの値を比較します。次の例に示すように、値が範囲内に含まれているかどうかを確認することによって、範囲内の値と一致させることもできます。

```swift
let point = (1, 2)
switch point {
case (0, 0):
    print("(0, 0) is at the origin.")
case (-2...2, -2...2):
    print("(\(point.0), \(point.1)) is near the origin.")
default:
    print("The point is at (\(point.0), \(point.1)).")
}
// "(1, 2) is near the origin."
```

`~=` 演算子をオーバーロードして、独自の式マッチングの動作を提供できます。例えば、`point` 式を point の文字列表現と比較するために上記の例を書き換えることができます。

```swift
// 整数を含む文字列と一致するように ~= 演算子をオーバーロードします
func ~= (pattern: String, value: Int) -> Bool {
    return pattern == "\(value)"
}
switch point {
case ("0", "0"):
    print("(0, 0) is at the origin.")
default:
    print("The point is at (\(point.0), \(point.1)).")
}
// "The point is at (1, 2)."
```

> GRAMMAR OF AN EXPRESSION PATTERN  
> expression-pattern → [expression](https://docs.swift.org/swift-book/ReferenceManual/Expressions.html#grammar_expression)
