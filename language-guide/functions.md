# Functions \(関数\)

最終更新日: 2020/5/23

関数\(_Functions_\)は、特定のタスクを実行する独立したコードの塊です。何をするものなのかを特定するために名前を与え、必要なときにタスクを実行するためにこの関数が呼び出されるときに名前は使われます。

Swift の統一された関数シンタックスは、シンプルな C 言語スタイルの引数名のない関数から 、Objective-C スタイルの個々の引数に名前とラベルを付けた関数までを表現できる、十分な柔軟性を持っています。引数には、関数の呼び出しをシンプルにするためにデフォルト値を設定できたり、関数の実行が完了すると渡した変数を変更する `in-out` 引数も渡すことができます。

全ての関数は、引数や戻り値の型で構成された自身の型を持っています。他の Swift の型と同じようにこの型を使用することができます。他の関数の引数に関数を渡したり、関数から関数を戻すことも簡単にできます。また、関数の中で、入れ子関数を作成することで、独自のスコープを作成して機能をカプセル化することもできます。

## Defining and Calling Functions\(関数の定義と呼び出し\)

関数を定義するとき、必要なときに、名前と型を持つ引数と呼ばれる 1 つ以上のインプットを定義することができます。また、関数の完了後に呼び出し元に結果を戻す、戻り値と呼ばれるアウトプットの型も必要なときに定義できます。

全ての関数は、実行するタスクを説明する、関数名\(_function name_\)を持っています。関数を使用するには、その関数をその名前で呼び出し\(_call_\)し、関数の引数の型に一致する\(引数と呼ばれる\)入力値を渡します。引数は、関数の引数のリストと常に同じ順序で与えなければなりません。

下記の例の関数は、`greet(person:)` という関数で、名前が示す通り、人の名前を引数に受け取り、その人への挨拶を返します。この関数では、`person` という `String` 型の引数と、その人に対する挨拶を含めた `String` 型の戻り値を返します。

```swift
func greet(person: String) -> String {
    let greeting = "Hello, " + person + "!"
    return greeting
}
```

この情報は全て、関数の定義にまとめられ、定義の前に `func` キーワードを付けます。関数の戻り値の型は、戻り矢印 `->(ハイフンの後に山括弧が続く)` で示し、その後に返す型名が続きます。

定義は、関数が何をするか、何を受け取ることを期待するか、そしてそれが行われたときに何を返すかを説明します。こうすることで、コード内の他の場所から誤解を生むことなく関数を呼び出すことができます。

```swift
print(greet(person: "Anna"))
// "Hello, Anna!"
print(greet(person: "Brian"))
// "Hello, Brian!"
```

`greet(person:)` 関数を呼び出すには、`greet(person: "Anna")` のように、`person` 引数ラベルの後に `String` 型の値を渡します。この関数は `String` 型の値を返すため、上記のように、`greet(person:)` を `print(_:separator:terminator:)` 関数の呼び出しでラップして、その文字列を出力して戻り値を確認できます。

> NOTE  
> `print(_:separator:terminator:)` 関数には最初の引数にラベルがなく、他の引数にはデフォルト値があるため、引数を渡しても渡さなくても呼び出し可能です。関数のシンタックスの種類に関しては[Function Argument Labels and Parameter Names](functions.md#function-argument-labels-and-parameter-names)と[Default Parameter Values](functions.md#default-parameter-values)に記載しています。

`greet(person:)` 関数の本文は、`greeting` と呼ばれる新しい `String` 型の定数を定義し、それにシンプルな挨拶文に設定することから始まります。このメッセージは、`return` キーワードを使用して関数から戻り値を返します。`return greeting` という行で、関数は実行を終了し、`greeting` の現在の値を返します。

本文を短くするために、挨拶の作成と `return` 文を 1 行にまとめることができます。

```swift
func greetAgain(person: String) -> String {
    return "Hello again, " + person + "!"
}
print(greetAgain(person: "Anna"))
// "Hello again, Anna!"
```

## Function Parameters and Return Values\(関数の引数と戻り値\)

Swift では、関数の引数と戻り値は非常に柔軟です。名前のない単一の引数を持つシンプルなユーティリティ関数から、読みやすいように表現できる引数名と様々な引数オプションを持つ複雑な関数まで、あらゆるものを定義できます。

### Functions Without Parameters\(引数なし関数\)

入力引数は関数に必須ではありません。入力引数のない関数は次のとおりです。この関数は、呼び出される度に、常に同じ `String` 型のメッセージを返します:

```swift
func sayHelloWorld() -> String {
    return "hello, world"
}
print(sayHelloWorld())
// "hello, world"
```

関数は、引数を受け取りませんが、関数名の後に括弧が必要です。関数が呼び出されると、関数名の後に空の括弧のペア\(`()`\)も書きます。

### Functions With Multiple Parameters\(複数の引数がある関数\)

関数は、カンマ(`,`\)区切りで複数の引数を持つことができます。

この関数は、人の名前と、その人がすでに挨拶されているかどうかを引数として受け取り、その人に適切な挨拶を返します:

```swift
func greet(person: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return greetAgain(person: person)
    } else {
        return greet(person: person)
    }
}
print(greet(person: "Tim", alreadyGreeted: true))
// "Hello again, Tim!"
```

`person` というラベルの付いた `String` 型の値と、括弧\(`()`\) 内にカンマ\(`,`\)で区切られた `alreadyGreeted` というラベルの付いた `Bool` 型の値の両方を渡して `great(person:alreadyGreeted:)` 関数を呼び出しています。この関数は、 前のセクションで示した `greet(person:)` 関数とは異なることに注意してください。どちらも `greet` という関数名ですが、`greet(person:alreadyGreeted:)` は 2 つの引数を取り、`greet(person:)` 関数は 1 つしか取りません。

### Functions Without Return Values\(戻り値なし関数\)

戻り値の型を定義することも必須ではありません。`greet(person:)` バージョンは `String` 型の値を返すのではなく、その値を出力します。

```swift
func greet(person: String) {
    print("Hello, \(person)!")
}
greet(person: "Dave")
// "Hello, Dave!"
```

値を返す必要がないため、関数の定義には戻り矢印\(`->`\)や戻り値の型は含まれていません。

> NOTE  
> 厳密に言えば、`greet(person:)` は、戻り値が定義されていなくても値を返してます。戻り型が定義されていない関数は、`Void` 型の特別な値を返します。これは単に `()` と書く空のタプルです。

関数の戻り値は、呼び出し元で無視できます:

```swift
func printAndCount(string: String) -> Int {
    print(string)
    return string.count
}
func printWithoutCounting(string: String) {
    let _ = printAndCount(string: string)
}
printAndCount(string: "hello, world")
// "hello, world" and returns a value of 12
printWithoutCounting(string: "hello, world")
// "hello, world" but doesn't return a value
```

最初の関数 `printAndCount(string:)` は文字列を出力し、その文字数を `Int` として返します。2 番目の関数 `printWithoutCounting(string:)` は、最初の関数を呼び出しますが、その戻り値を無視します。2 番目の関数が呼び出されても、メッセージは最初の関数によって出力されますが、戻り値は使われていません。

> NOTE  
> 戻り値は無視できますが、常に関数が値を返すことは示さなければなりません。戻り型が定義されている関数では、値を返さずに関数を使用することはできません。そうしようとすると、コンパイルエラーが発生します。

### Functions with Multiple Return Values\(複数の戻り値がある関数\)

タプル型を関数の戻り値の型として使用して、1 つの複合戻り値として複数の値を返すことができます。

以下の例では、`minMax(array:)` という関数を定義しています。この関数は、`Int` 値の配列内の最小数と最大数を検索します。

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

`minMax(array:)` 関数は、2 つの `Int` 値を含むタプルを返します。これらの値には `min` と `max` のラベルが付いているため、関数の戻り値を使用するときにその名前でアクセスできます。

`minMax(array:)` 関数の本体は、`currentMin` および `currentMax` と呼ばれる 2 つの変数に、配列の最初の整数値を設定することから始まります。次に、この関数は配列内の残りの値を繰り返し処理し、各値をチェックして、それぞれ `currentMin` と `currentMax` の値よりも小さいか大きいかを確認します。最後に、全体の最小値と最大値が 2 つの `Int` 値のタプルとして返されます。

タプルのそれぞれの値は、関数の戻り値の型の一部として名前が付けられているため、ドット\(`.`\)シンタックスでアクセスして、見つかった最小値と最大値を取得できます。

```swift
let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
// "min is -6 and max is 109"
```

タプルの各値は、関数の戻り値の型で名前がすでに指定されているため、関数からタプルが返されるときに名前を付ける必要がないことに注目してください。

#### Optional Tuple Return Types\(オプショナルのタプルの戻り値の型\)

関数から返されるタプル型が、「値が存在しない」可能性がある場合は、オプショナルのタプル型を使用して、タプル全体が `nil` になる可能性があることを示すことができます。`(Int, Int)?` や `(String, Int, Bool)?` のように、タプル型の閉じ括弧\(`)`\)の後に疑問符\(`?`\)を配置して、オプショナルのタプル型を書きます。

> NOTE  
> `(Int, Int)?` などの オプショナルのタプル型は `(Int?、Int?)` などの オプショナルの型を含むタプルとは異なります。オプショナルの型を使用すると、タプル内の個々の値だけでなく、タプル全体が オプショナルになります。

上記の `minMax(array:)` 関数は、2 つの `Int` 値を含むタプルを返します。ただし、この関数は、渡された配列に対して安全性チェックを行なっていません。`array` 引数に空の配列が含まれている場合、上記で定義した `minMax(array:)` 関数は、`array [0]` にアクセスしようとしたときに実行時エラーが発生します。

空の配列を安全に処理するには、オプショナルのタプルの戻り値の型を指定して、配列が空の場合は `nil` を返します。

```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    if array.isEmpty { return nil }
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

オプショナルバインディングを使用して、このバージョンの `minMax(array:)` 関数が、実際のタプル値を返すか `nil` を返すかを確認できます:

```swift
if let bounds = minMax(array: [8, -6, 2, 109, 3, 71]) {
    print("min is \(bounds.min) and max is \(bounds.max)")
}
// "min is -6 and max is 109"
```

### Functions With an Implicit Return\(暗黙的な戻り値がある関数\)

関数の本文全体が、単一の式の場合は、関数は暗黙的にその式の結果を返します。例えば、下記の両方の関数の動作は同じです:

```swift
func greeting(for person: String) -> String {
    "Hello, " + person + "!"
}
print(greeting(for: "Dave"))
// "Hello, Dave!"

func anotherGreeting(for person: String) -> String {
    return "Hello, " + person + "!"
}
print(anotherGreeting(for: "Dave"))
// "Hello, Dave!"
```

`greeting(for:)` 関数の全体の定義は、戻り値の挨拶文です。つまり、この短い形式を使用できます。`anotherGreeting(for:)` 関数は、より複数の式を持つ関数と同様に `return` キーワードを使用して、同じ挨拶文を返しています。1 つの戻り行として記述した関数は、`return` を省略できます。

[Shorthand Getter Declaration](properties.md#shorthand-getter-declaration短縮getプロパティ宣言)でも説明されていますが、get プロパティ\(_property getter_\)でも暗黙的な戻り値を使用することができます。

> NOTE  
> 暗黙の戻り値を使ったコードは、何かしらの値を返す必要があります。例えば、 `fatalError("Oh no!")` または `print(13)` を暗黙の戻り値として使用することはできません。

## Function Argument Labels and Parameter Names\(引数ラベルと引数名\)

各関数の引数には、引数ラベルと引数名の両方持つことができます。引数ラベルは、関数を呼び出すときに使用されます。各引数は、その前に書かれた引数ラベルを使用して関数が呼び出されます。引数名は関数の内部で使用されます。デフォルトでは、引数名を引数ラベルとして使用します。

```swift
func someFunction(firstParameterName: Int, secondParameterName: Int) {
    // 関数の本文では、`firstParameterName`と`secondParameterName`は
    // 最初と 2 番目の引数の値を参照します。
}
someFunction(firstParameterName: 1, secondParameterName: 2)
```

全ての引数には一意の名前を付ける必要があります。複数の引数に同じ引数ラベルを付けることはできますが、一意の引数ラベルを使用すると、コードが読みやすくなります。

### Specifying Argument Labels\(引数ラベルの特定\)

引数名の前に、スペースで区切って引数ラベルを記述します。

```swift
func someFunction(argumentLabel parameterName: Int) {
    // 関数の本文では、`parameterName` で引数の値を参照します
}
```

これは、人の名前と出身地を受け取って挨拶を返す `greet(person:)` 関数の一種です:

```swift
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// "Hello Bill!  Glad you could visit from Cupertino."
```

引数ラベルを使用すると、関数の本文に読みやすく明確な意図を持たせつつ、表現豊かな文章のように関数を呼び出すことができます。

### Omitting Argument Labels\(引数ラベルの省略\)

引数ラベルが必要ない場合は、その引数ラベルの代わりにアンダースコア\(`_`\)を記述します。

```swift
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // 関数の本文では、`firstParameterName`と`secondParameterName`は
    // 最初と 2 番目の引数の値を参照します。
}
someFunction(1, secondParameterName: 2)
```

引数ラベルがある場合、関数を呼び出すときに引数にラベルを付ける必要があります。

### Default Parameter Values\(デフォルト引数値\)

引数の型の後に値を代入することで、関数内の任意の引数のデフォルト値を定義できます。デフォルト値が定義されている場合は、関数を呼び出すときにその引数を省略できます。

```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // 関数を呼び出すときに 2 番目の引数を省略した場合、
    // `parameterWithDefault` の値は 12 になります。
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // `parameterWithDefault` は 6
someFunction(parameterWithoutDefault: 4) // `parameterWithDefault` は 12
```

デフォルト値のない引数は、関数の引数リストでデフォルト値のある引数よりも前に置きましょう。通常、デフォルト値を持たない引数の方が重要です。最初にデフォルト値を持たない引数を置くと、デフォルト引数が省略されているかどうかに関係なく、同じ関数が呼び出されていることを認識しやすくなります。

### Variadic Parameters\(可変長引数\)

可変長引数\(_Variadic Parameters_\)は、指定された型の 0 個以上の値を受け入れます。可変長引数を使用すると、関数が呼び出されたときに、引数に様々な数の入力値を渡すことができます。引数の型名の後に 3 つのピリオド文字\(`...`\)を挿入して、可変長引数を記述します。

可変長引数に渡された値は、適切な型の配列として関数の本文内で使用できるようになります。例えば、`numbers` という `Double...` 型を持つ可変長引数は、`numbers` という `[Double]` 型の定数配列として関数の本文内で使用できます。

下記の例では、任意の長さの数値のリストの算術平均\(いわゆる平均\)を計算しています。

```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// 5 つの数値の平均の 3.0 を返します
arithmeticMean(3, 8.25, 18.75)
// 3 つの数値の平均の 10.0 を返します
```

関数は複数の可変長引数を持つことができます。可変長引数の後に続く最初の引数には、引数ラベルが必要です。引数ラベルにより、どの引数が可変長引数に渡され、どの引数が可変長引数の後に続く引数に渡されているかが明確になります。

### In-Out Parameters\(In-Out 引数\)

関数の引数はデフォルトで定数です。関数の本文から引数の値を変更しようとすると、コンパイルエラーが発生します。つまり、誤って引数の値を変更することはできません。関数で引数の値を変更する必要があり、関数呼び出しが終了した後もそれらの変更を保持したい場合は、代わりに `in-out` 引数として定義します。

引数の型の直前に `inout` キーワードを配置することにより、in-out 引数を書きます。in-out 引数に渡された値は、関数に渡され、変更され、元の値を置き換えて、関数から戻ってきます。in-out 引数の動作および関連するコンパイラの最適化の詳細については、[In-Out Parameters](../language-reference/declarations.md#in-out-parameters)を参照ください。

in-out 引数には、変数のみ渡すことができます。定数とリテラルは変更できないため、渡すことはできません。変数を in-out 引数として渡すときは、変数名の直前にアンパサンド\(`&`\)を付けて、関数で値が変更される可能性があることを示します。

> NOTE  
> in-out 引数にデフォルト値を設定したり、可変長引数を `inout` としてマークすることはできません。

これは、`swapTwoInts(_:_:)` という関数の例です。この関数には、`a` と `b` という 2 つの引数があります。

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

`swapTwoInts(_:_:)` 関数は、`b` の値を `a` にスワップし、`a` の値を `b` にスワップします。この関数は、`a` の値を `temporaryA` と呼ばれる一時定数に格納し、`b` の値を `a` に代入してから、`temporaryA` を `b` に代入することによってこのスワップを実行します。

`Int` 型の 2 つの変数を指定して `swapTwoInts(_:_:)` 関数を呼び出し、それらの値を交換できます。`someInt` と `anotherInt` には、`swapTwoInts(_:_:)` 関数に渡されるときに、アンパサンド\(`&`\)が前に付いていることに注目してください。

```swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// "someInt is now 107, and anotherInt is now 3"
```

上記の例は、`someInt` と `anotherInt` の元の値が、関数の外部で定義されていたとしても、`swapTwoInts(_:_:)` 関数によって変更されることを示しています。

> NOTE  
> in-out 引数は、関数から値を返すことと同じではありません。上記の `swapTwoInts` の例では、戻り値の型を定義して値を返していませんが、`someInt` と `anotherInt` の値を変更しています。in-out 引数は、関数が本文の範囲外に影響を与える代替方法です。

## Function Types\(関数型\)

全ての関数には特定の関数型があり、引数の型と戻り値の型で構成されています。

例えば:

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}
```

この例では、`addTwoInts` および `multiplyTwoInts` と呼ばれる 2 つのシンプルな数学関数を定義します。これらの関数はそれぞれ 2 つの `Int` 値を取り、適切な数学演算を実行した結果の `Int` 値を返します。

これらの関数の両方の型は、`(Int, Int) -> Int` です。これは次のように読むことができます:

「両方とも `Int` 型の 2 つの引数を持ち、`Int` 型の値を返す関数です」

引数や戻り値のない関数の例を次に示します。

```swift
func printHelloWorld() {
    print("hello, world")
}
```

この関数の型は、`() -> Void` で「引数を持たず、`Void` を返す関数」です。

### Using Function Types\(関数型の使用\)

Swift の他の型と同じように関数型を使用します。例えば、定数または変数を関数型として定義し、その変数に適切な関数を代入することができます:

```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
```

これは次のように読むことができます:

「"2 つの `Int` 値を取り、`Int` 値を返す関数"の型を持つ `mathFunction` という変数を定義します。この新しい変数が、`addTwoInts` という関数を参照するように設定します」

`addTwoInts(_:_:)` 関数は `mathFunction` 変数と同じ型なため、この代入は Swift の型チェックを通ります。

代入した関数を `mathFunction` という名前で呼び出すことができます。

```swift
mathFunction = multiplyTwoInts
print("Result: \(mathFunction(2, 3))")
// "Result: 6"
```

他の型と同様に、定数または変数に関数を代入するときに、関数型を推論することができます。

```swift
let anotherMathFunction = addTwoInts
// anotherMathFunction は (Int, Int) -> Int と推論されます。
```

### Function Types as Parameter Types\(引数の型としての関数型\)

`(Int, Int) -> Int` などの関数型を別の関数の引数の型として使用できます。これにより、関数の呼び出し元で関数の一部の機能を決めることができます。

下記は、上記の数学関数の結果を出力する例です:

```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// "Result: 8"
```

この例では、3 つの引数を持つ `printMathResult(_:_:_:)` という関数を定義します。最初の引数は `mathFunction` と呼ばれ、型 `(Int, Int) -> Int` です。この最初の引数として、その型の任意の関数を渡すことができます。2 番目と 3 番目の引数は `a` と `b` と呼ばれ、どちらも `Int` 型です。これらは、提供されている数学関数の 2 つの入力値として使用されています。

`printMathResult(_:_:_:)` が呼び出されると、`addTwoInts(_:_:)` 関数、および整数値 `3` と `5` が渡されます。引数で与えられた関数を値 `3` と `5` で呼び出し、次の結果 `8` を出力します。

`printMathResult(_:_:_:)` の役割は、適切な型の数学関数の結果を出力することです。その関数が実際に何をするかは重要ではありません。関数が正しい型なことが重要です。これにより、`printMathResult(_:_:_:)` は、その機能の一部を関数の呼び出し元に型安全な方法で渡すことができます。

### Function Types as Return Types\(戻り値の型としての関数型\)

関数型を別の関数の戻り値の型として使用できます。これを行うには、関数の戻り矢印\(`->`\)の直後に完全な関数型を書きます。

次の例では、`stepForward(_:)` と `stepBackward(_:)` という 2 つのシンプルな関数を定義します。`stepForward(_:)` 関数は、入力値より 1 つ大きい値を返し、`stepBackward(_:)` 関数は、入力値より 1 つ小さい値を返します。両方の関数の型は `(Int) -> Int` です:

```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}
```

これが `chooseStepFunction(backward:)` という関数で、その戻り値の型は `(Int) -> Int` です。`chooseStepFunction(backward:)` 関数は、`backward` と呼ばれるブール値に基づいて `stepForward(_:)` または `stepBackward(_:)` を返します。

```swift
var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero は stepBackward() 関数を参照しています
```

上記の例では、`currentValue` という変数を徐々にゼロに近づけるために正または負のステップが必要かどうかを判断します。`currentValue` の初期値は `3` です。これは、`currentValue > 0` が `true` を返し、`chooseStepFunction(backward:)` が `stepBackward(_:)` 関数を返すことを意味します。返された関数への参照は、`moveNearerToZero` という定数に格納されます。

`moveNearerToZero` が適切な関数を参照しているので、ゼロまでカウントできます:

```swift
print("Counting to zero:")
// 0 までカウントします:
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!
```

## Nested Functions\(入れ子関数\)

この章でこれまでに見てきた全ての関数は、グローバルスコープで定義されたグローバル関数の例でしたが、入れ子関数\(_nested functions_\)と呼ばれる、他の関数の本文内に関数を定義することもできます。

入れ子関数は、デフォルトでは外から見えませんが、それを囲む関数から呼び出して使用することができます。囲んでいる関数が、入れ子関数内の 1 つを戻り値として、入れ子関数を別のスコープで使用することもできます。

上記の `chooseStepFunction(backward:)` の例を書き直して、入れ子関数を使用することができます。

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero は 入れ子関数の stepForward() を参照しています
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// -4...
// -3...
// -2...
// -1...
// zero!
```
