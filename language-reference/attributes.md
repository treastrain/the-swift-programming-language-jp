# Attributes \(属性\)

最終更新日: 2021/6/19

Swift には、宣言に適用される属性と型に適用される属性の 2 種類があります。属性は、宣言または型に関する追加情報を提供します。例えば、関数宣言の `discardableResult` 属性は、関数は値を返しますが、戻り値が使用されていない場合に、コンパイラが警告を生成しないことを示します。

`@` 記号に続けて属性の名前と、属性が受け入れる引数を書き込むことにより、属性を指定します。

![属性](./../.gitbook/assets/attributes.png)

一部の宣言属性は、属性とそれが特定の宣言にどのように適用されるかについての詳細情報を指定する引数を受け入れます。これらの attribute arguments は括弧(`()`)で囲まれ、その形式は属する属性によって定義されています。

## Declaration Attributes\(宣言属性\)

宣言属性は宣言にのみ適用できます。

### available

この属性を適用すると、特定の Swift 言語バージョンまたは特定のプラットフォームとオペレーティングシステムのバージョンに関連する宣言のライフサイクルを示します。

`available` 属性は、常に 2 つ以上のカンマ区切り(`,`)の属性引数のリストを伴います。これらの引数は、次のプラットフォーム名または言語名のいずれかで始まります:

* `iOS`
* `iOSApplicationExtension`
* `macOS`
* `macOSApplicationExtension`
* `macCatalyst`
* `macCatalystApplicationExtension`
* `watchOS`
* `watchOSApplicationExtension`
* `tvOS`
* `tvOSApplicationExtension`
* `swift`

アスタリスク(`*`)を使用して、上記の全てのプラットフォーム名で宣言が使用可能なことを示すこともできます。Swift のバージョン番号を使用して availability を指定する `available` 属性では、アスタリスクを使用できません。

残りの引数は任意の順序で記述でき、重要なマイルストーンなど、宣言のライフサイクルに関する追加情報を指定できます。

* `unavailable` 引数は、指定されたプラットフォームで宣言が使用できないことを示します。この引数は、Swift の availability バージョンを指定している場合は使用できません

* `introduced` 引数は、宣言が導入された特定のプラットフォームまたは言語の最初のバージョンを示します。形式は次のとおりです:

![introduced引数](./../.gitbook/assets/introduced.png)

version number は、ピリオド(`.`)で区切られた 1〜3 個の正の整数で構成されます。

* `deprecated` 引数は、宣言が非推奨になった特定のプラットフォームまたは言語の最初のバージョンを示します。形式は次のとおりです:

![deprecated引数](./../.gitbook/assets/deprecated.png)

任意の version number は、ピリオド(`.`)で区切られた 1〜3 個の正の整数で構成されます。バージョン番号を省略すると、deprecated がいつ発生したかについての情報を提供せずに、宣言が現在 deprecated になっていることのみを示します。バージョン番号を省略する場合は、コロン(`:`)も省略してください。

* `obsoleted` 引数は、宣言が廃止された特定のプラットフォームまたは言語の最初のバージョンを示します。宣言が廃止されると、指定されたプラットフォームまたは言語から削除され、使用できなくなります。形式は次のとおりです:

![obsoleted引数](./../.gitbook/assets/obsoleted.png)

version number は、ピリオド(`.`)で区切られた 1〜3 個の正の整数で構成されます。

* `message` 引数は、deprecated または obsoleted された宣言に使用した際に、コンパイラが表示する警告またはエラーのテキストメッセージを提供します。形式は次のとおりです:

![message引数](./../.gitbook/assets/message.png)

message は文字列リテラルで構成されます。

* `renamed` 引数は、名前が変更された宣言の新しい名前を示すテキストメッセージを提供します。コンパイラは、名前が変更された宣言が使用された際にエラーを発行し、新しい名前を表示します。形式は次のとおりです:

![renamed引数](./../.gitbook/assets/renamed.png)

new name は文字列リテラルで構成されます。

下記に示すように、フレームワークまたはライブラリのリリース間で宣言の名前が変更されたことを示すために、`renamed` 引数と `unavailable` 引数と一緒に `available` 属性をタイプエイリアス宣言に適用することができます。この組み合わせにより、宣言の名前が変更されたというコンパイルエラーが発生します。

```swift
// 初回リリース
protocol MyProtocol {
    // プロトコル定義
}

```

```swift
// MyProtocolの名前変更後のリリース
protocol MyRenamedProtocol {
    // プロトコル定義
}

@available(*, unavailable, renamed: "MyRenamedProtocol")
typealias MyProtocol = MyRenamedProtocol
```

1 つの宣言に複数の `available` 属性を適用して、様々なプラットフォームおよび様々なバージョンの Swift の availability を指定できます。属性が現在のターゲットと一致しないプラットフォームまたは言語バージョンを指定している場合、`available` 属性が適用される宣言は無視されます。複数の `available` 属性を使用する場合、有効な availability は、プラットフォームと Swift の availability の組み合わせです。

`available` 属性がプラットフォームまたは言語名の引数に加えて `introduced` 引数を指定するだけの場合は、代わりに次の省略構文を使用できます:

![available属性](./../.gitbook/assets/available.png)

`available` 属性の省略構文は、複数のプラットフォームの availability を簡潔に表しています。2 つの形式は機能的に同等ですが、可能な限り省略形が推奨されます。

```swift
@available(iOS 10.0, macOS 10.12, *)
class MyClass {
    // クラス定義
}
```

Swift バージョン番号を使用して availability を指定する `available` 属性は、宣言のプラットフォームの availability を追加で指定することはできません。代わりに、個別の `available` 属性を使用して、Swift バージョンの availability と 1 つ以上のプラットフォームの availability を指定します。

```swift
@available(swift 3.0.2)
@available(macOS 10.12, *)
struct MyStruct {
    // 構造体定義
}
```

### discardableResult

この属性を関数またはメソッドの宣言に適用すると、値を返す関数またはメソッドがその結果を使用せずに呼び出されたときのコンパイラの警告を抑制します。

### dynamicCallable

この属性をクラス、構造体、列挙型、またはプロトコルに適用すると、型のインスタンスを呼び出し可能な関数として扱います。この型は、`dynamicallyCall(withArguments:)` メソッド、`dynamicallyCall(withKeywordArguments:)` メソッド、またはその両方を実装する必要があります。

動的に呼び出し可能な型のインスタンスは、任意の数の引数を取る関数のように呼び出すことができます。

```swift
@dynamicCallable
struct TelephoneExchange {
    func dynamicallyCall(withArguments phoneNumber: [Int]) {
        if phoneNumber == [4, 1, 1] {
            print("Get Swift help on forums.swift.org")
        } else {
            print("Unrecognized number")
        }
    }
}

let dial = TelephoneExchange()

// 動的メソッド呼び出しを使用します
dial(4, 1, 1)
// "Get Swift help on forums.swift.org"

dial(8, 6, 7, 5, 3, 0, 9)
// "Unrecognized number"

// 基になるメソッドを直接呼び出します
dial.dynamicallyCall(withArguments: [4, 1, 1])
```

`dynamicallyCall(withArguments:)` メソッドの宣言には、上記の例の `[Int]` のように、[ExpressibleByArrayLiteral](https://developer.apple.com/documentation/swift/expressiblebyarrayliteral)プロトコルに準拠する単一の引数が必要です。戻り値の型は任意の型にすることができます。

`dynamicallyCall(withKeywordArguments:)` メソッドを実装する場合は、動的メソッド呼び出しにラベルを含めることができます。

```swift
@dynamicCallable
struct Repeater {
    func dynamicallyCall(withKeywordArguments pairs: KeyValuePairs<String, Int>) -> String {
        return pairs
            .map { label, count in
                repeatElement(label, count: count).joined(separator: " ")
            }
            .joined(separator: "\n")
    }
}

let repeatLabels = Repeater()
print(repeatLabels(a: 1, b: 2, c: 3, b: 2, a: 1))
// a
// b b
// c c c
// b b
// a
```

`dynamicallyCall(withKeywordArguments:)` メソッドの宣言には、[ExpressibleByDictionaryLiteral](https://developer.apple.com/documentation/swift/expressiblebydictionaryliteral)プロトコルに準拠する単一の引数が必要で、戻り値の型は任意の型にすることができます。引数の[Key](https://developer.apple.com/documentation/swift/expressiblebydictionaryliteral/2294108-key)は[ExpressibleByStringLiteral](https://developer.apple.com/documentation/swift/expressiblebystringliteral)に準拠する必要があります。前の例では、引数型として[KeyValuePairs](https://developer.apple.com/documentation/swift/keyvaluepairs)を使用しているため、呼び出し元は重複する引数ラベルを含めることができます。つまり、`a` と `b` は、`repeatLabels` の呼び出し時に複数回使用されています。

両方の `dynamiclyCall` メソッドを実装する場合、メソッド呼び出しにキーワード引数が含まれていると、`dynamicallyCall(withKeywordArguments:)` が呼び出されます。他の全ての場合、`dynamicallyCall(withArguments:)` が呼び出されます。

動的に呼び出すことができるインスタンスは、`dynamicCall` メソッドの実装の 1 つで指定した型と一致する引数と戻り値を使用した場合にのみ呼び出すことができます。次の例の呼び出しは、`KeyValuePairs <String、String>` を受け取る `dynamicallyCall(withArguments:)` の実装がないため、コンパイルできません。

```swift
repeatLabels(a: "four") // エラー
```

### dynamicMemberLookup

この属性をクラス、構造体、列挙型、またはプロトコルに適用すると、実行時にメンバを名前で検索できるようになります。型は `subscript(dynamicMember:)`subscript を実装する必要があります。

明示的なメンバ式では、指定されたメンバに対応する宣言がない場合、式は型の `subscript(dynamicMember:)`subscript の呼び出しとして解釈され、メンバに関する情報を引数として渡します。subscript は、KeyPath またはメンバ名のいずれかで引数を受け取ることができます。両方の subscript を実装する場合、KeyPath 引数を取る subscript が使用されます。

`subscript(dynamicMember:)` の実装は、[KeyPath](https://developer.apple.com/documentation/swift/keypath)、[WritableKeyPath](https://developer.apple.com/documentation/swift/writablekeypath)、または [ReferenceWritableKeyPath](https://developer.apple.com/documentation/swift/referencewritablekeypath)の引数を使用して KeyPath を受け取ることができます。[ExpressibleByStringLiteral](https://developer.apple.com/documentation/swift/expressiblebystringliteral)プロトコル(ほとんどの場合、`String`)に準拠する型の引数を使用して、メンバ名を受け入れることができます。subscript の戻り値の型は任意の型にすることができます。

メンバ名による動的なメンバ検索を使用して、他の言語のデータを Swift にブリッジする場合など、コンパイル時に型チェックできないデータのラッパ型を作成できます。例えば:

```swift
@dynamicMemberLookup
struct DynamicStruct {
    let dictionary = ["someDynamicMember": 325,
                      "someOtherMember": 787]
    subscript(dynamicMember member: String) -> Int {
        return dictionary[member] ?? 1054
    }
}
let s = DynamicStruct()

//動的メンバ検索を使用します。
let dynamic = s.someDynamicMember
print(dynamic)
// "325"

// 基になる subscript を直接呼び出します
let equivalent = s[dynamicMember: "someDynamicMember"]
print(dynamic == equivalent)
// "true"
```

KeyPath による動的メンバ検索を使用して、コンパイル時の型チェックをサポートする方法でラッパ型を実装できます。例えば:

```swift
struct Point { var x, y: Int }

@dynamicMemberLookup
struct PassthroughWrapper<Value> {
    var value: Value
    subscript<T>(dynamicMember member: KeyPath<Value, T>) -> T {
        get { return value[keyPath: member] }
    }
}

let point = Point(x: 381, y: 431)
let wrapper = PassthroughWrapper(value: point)
print(wrapper.x)
```

### frozen

この属性を構造体または列挙型宣言に適用すると、型に加えることができる変更の種類を制限します。この属性は、library evolution mode でコンパイルする場合にのみ許可されます。ライブラリの将来のバージョンでは、列挙型のケースまたは構造体の格納インスタンスプロパティを追加、削除、または並べ替えて宣言を変更することはできません。これらの変更は nonfrozen 型で許可されますが、frozen 型の ABI 互換性が損なわれます。

> NOTE  
> コンパイラが library evolution mode でない場合、全ての構造体と列挙型は暗黙的に frozen にされ、この属性は無視されます。

library evolution mode では、nonfrozen の構造体や列挙型のメンバとやり取りするコードは、ライブラリの将来のバージョンでその型のメンバの一部が追加、削除、または並べ替えられた場合でも、再コンパイルせずに作業を継続できるようにコンパイルされます。コンパイラは、実行時に情報を検索したり、間接層を追加したりするなどの手法を使用して、これを可能にします。構造体または列挙型を frozen でマークすると、パフォーマンスを向上させるためにこの柔軟性が失われます。ライブラリの将来のバージョンでは、型に限定的な変更しか加えることができませんが、コンパイラは、型のメンバとやり取りするコードに追加の最適化を行うことができます。

frozen 型、frozen 構造体の格納プロパティの型、および frozen 列挙ケースの関連値は、public か、`usableFromInline` 属性がマークされている必要があります。frozen 構造体のプロパティはプロパティオブザーバを持つことができず、格納インスタンスプロパティの初期値を提供する式は、[inlinable](#inlinable) で説明されているように inlinable 関数と同じ制限に従う必要があります。

コマンドラインで library evolution mode を有効にするには、`-enable-library-evolution` オプションを Swift コンパイラに渡します。Xcode で有効にするには、[Xcode Help](https://help.apple.com/xcode/mac/current/#/dev04b3a04ba)の説明に従って、「Build Libraries for Distribution」のビルド設定(`BUILD_LIBRARY_FOR_DISTRIBUTION`)を Yes に設定します。

frozen 列挙型の switch 文は、[Switching Over Future Enumeration Cases(列挙型の将来のケースのスイッチング)](./attributes.md#switching-over-future-enumeration-cases列挙型の将来のケースのスイッチング)で説明されているように、デフォルトのケースを必要としません。frozen 列挙型を切り替えるときに `default` または `@unknown default` のケースを含めると、そのコードは実行されないため、警告が生成されます。

### GKInspectable

この属性を適用して、独自の GameplayKit コンポーネントプロパティを SpriteKit エディタ UI に公開します。この属性を適用すると、`objc` 属性も暗黙的に追加されます。

### inlinable

この属性を関数、メソッド、計算プロパティ、subscript、convenience イニシャライザ、またはデイニシャライザ宣言に適用すると、モジュールのパブリックインターフェイスの一部としてその宣言の実装を公開します。コンパイラは、inlinable シンボルへの呼び出しを、呼び出し側のシンボルの実装のコピーに置き換えることができます。

inlinable コードは、任意のモジュールで宣言された `public` シンボルとやり取りし、`usableFromInline` 属性がマークされた同じモジュールで宣言された `internal` シンボルとやり取りできます。inline 化できないコードは、`private` シンボルまたは fileprivate シンボルとやり取りできません。

この属性は、関数内にネストされている宣言や、`fileprivate` 宣言または `private` 宣言には適用できません。inlinable 関数内で定義された関数とクロージャは、この属性をマークすることはできませんが、暗黙的に inlinable です。

### main

この属性を構造体、クラス、または列挙型の宣言に適用すると、プログラムフローのトップレベルのエントリポイントが含まれていることを示します。型は、引数をとらずに `Void` を返す `main` 型関数を提供する必要があります。例えば:

```swift
@main
struct MyTopLevel {
    static func main() {
         // トップレベルのコードをここに
    }
}
```

`main` 属性の要件を記述する別の方法は、この属性を追加する型が、次の架空のプロトコルに準拠する型と同じ要件を満たさなければならないということです。

```swift
protocol ProvidesMain {
    static func main() throws
}
```

実行可能ファイルを作成するためにコンパイルする Swift コードには、[Top-Level Code](./declarations.md#top-Level-Codeトップレベルコード)で説明されているように、ただ 1 つのトップレベルのエントリポイントのみを含めます。

### nonobjc

この属性をメソッド、プロパティ、subscript、またはイニシャライザ宣言に適用すると、暗黙の `objc` 属性を抑制します。`nonobjc` 属性は、Objective-C で宣言を使用することができる場合でも、使用できないようにするようコンパイラに指示します。

この属性を extension に適用すると、`objc` 属性が明示的にマークされていないその extension の全てのメンバに適用するのと同じ効果があります。

`nonobjc` 属性を使用して、`objc` 属性がマークされたクラスのブリッジメソッドの循環性を解決し、`objc` 属性がマークされたクラスのメソッドとイニシャライザのオーバーロードを許可します。

`nonobjc` 属性がマークされたメソッドは、`objc` 属性がマークされたメソッドをオーバーライドできません。ただし、`objc` 属性がマークされたメソッドは、`nonobjc` 属性がマークされたメソッドをオーバーライドできます。同様に、`nonobjc` 属性がマークされたメソッドは、`objc` 属性がマークされたメソッドのプロトコル要件を満たすことができません。

### NSApplicationMain

この属性をクラスに適用すると、それがアプリケーションデリゲートなことを示します。この属性を使用することは、`NSApplicationMain(_:_:)` 関数を呼び出すことと同じです。

この属性を使用しない場合は、次のように `NSApplicationMain(_:_:)` 関数を呼び出すトップレベルのコードを `main.swift` ファイルに指定します。

```swift
import AppKit
NSApplicationMain(CommandLine.argc, CommandLine.unsafeArgv)
```

実行可能ファイルを作成するためにコンパイルする Swift コードには、[Top-Level Code](./declarations.md#top-Level-Codeトップレベルコード)で説明されているように、ただ 1 つのトップレベルのエントリポイントのみを含めます。

### NSCopying

この属性をクラスの格納変数プロパティに適用します。この属性により、プロパティの set は、プロパティ自体の値ではなく、`copyWithZone(_:)` メソッドによって返されるプロパティの値のコピーと同期されます。プロパティの型は、`NSCopying` プロトコルに準拠している必要があります。

`NSCopying` 属性は、Objective-C のコピープロパティ属性と同じように動作します。

### NSManaged

この属性を `NSManagedObject` を継承するクラスのインスタンスメソッドまたは格納変数プロパティに適用すると、関連付けられたエンティティの記述に基づいて、CoreData が実行時にその実装を動的に提供することを示します。`NSManaged` 属性がマークされたプロパティの場合、CoreData は実行時にストレージも提供します。この属性を適用すると、`objc` 属性も暗黙的に追加されます。

### objc

この属性は Objective-C で使用することができる全ての宣言に適用します。例えば、ネストされていないクラス、プロトコル、(整数の RawValue 型の)非ジェネリック列挙型、クラスのプロパティとメソッド(get と set を含む)、プロトコル、およびオプショナルのプロトコル、イニシャライザ、および subscript。`objc` 属性は、Objective-C コードで宣言を使用できることをコンパイラに通知します。

この属性を extension に適用すると、`nonobjc` 属性が明示的にマークされていないその extension 内の全てのメンバに適用するのと同じ効果があります。

コンパイラは、Objective-C で定義されたクラスのサブクラスに `objc` 属性を暗黙的に追加します。ただし、サブクラスはジェネリックにはできず、ジェネリッククラスから継承してはなりません。これらの条件を満たすサブクラスに `objc` 属性を明示的に追加して、下記で説明するように、その Objective-C 名を指定できます。`objc` 属性がマークされているプロトコルは、`objc` 属性がマークされていないプロトコルから継承できません。

objc 属性は、次の場合にも暗黙的に追加されます:

* 宣言がサブクラスのオーバーライドで、スーパークラスの宣言に `objc` 属性がある場合
* 宣言が `objc` 属性を持つプロトコルの要件を満たす場合
* 宣言に、`IBAction`、`IBSegueAction`、`IBOutlet`、`IBDesignable`、`IBInspectable`、`NSManaged`、または `GKInspectable` 属性がある場合

`objc` 属性を列挙型に適用すると、各列挙ケースは、列挙型名とケース名を連結して Objective-C コードに公開されます。ケース名の最初の文字は大文字です。例えば、Swift では `Planet` 列挙型内の `venus` という名前のケースは、`PlanetVenus` という名前のケースとして Objective-C コードに公開されます。

`objc` 属性は、任意で、識別子に単一の属性引数を受け取ります。識別子は、`objc` 属性が適用されるエンティティの Objective-C に公開される名前を指定します。この引数を使用して、クラス、列挙型、列挙ケース、プロトコル、メソッド、get、set、およびイニシャライザに名前を付けることができます。クラス、プロトコル、または列挙型に Objective-C 名を指定する場合は、[Programming with Objective-C](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011210)の[Conventions](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Conventions/Conventions.html#//apple_ref/doc/uid/TP40011210-CH10-SW1)で説明されているように、名前に 3 文字のプレフィックスを含めます。下記の例では、`ExampleClass` の `enabled` プロパティの get を、プロパティ自体の名前としてではなく、`isEnabled` として Objective-C コードに公開しています。

```swift
class ExampleClass: NSObject {
    @objc var enabled: Bool {
        @objc(isEnabled) get {
            // 適切な値を返します
        }
    }
}
```

詳細については、Swift の [Importing Swift into Objective-C](https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_swift_into_objective-c)を参照ください。

> NOTE  
> `objc` 属性の引数は、その宣言の実行時名を変更することもできます。`NSClassFromString` などの Objective-C ランタイムとやり取りする関数を呼び出すとき、およびアプリの `Info.plist` ファイルでクラス名を指定するときに、実行時名を使用します。引数を渡して名前を指定すると、その名前が Objective-C コードの名前および実行時名として使用されます。引数を省略すると、Objective-C コードで使用される名前は Swift コードの名前と一致し、実行時名は通常の Swift コンパイラの名前修飾規則に従います。

### objcMembers

この属性をクラス宣言に適用すると、クラスの全ての Objective-C に互換性のあるメンバ、その extension、そのサブクラス、およびそのサブクラスの全ての extension に `objc` 属性を暗黙的に追加します。

ほとんどのコードでは、代わりに `objc` 属性を使用して、必要な宣言のみを公開する必要があります。多くの宣言を公開する必要がある場合は、`objc` 属性を持つ extension でグループ化できます。`objcMembers` 属性は、Objective-C ランタイムのリフレクション機能を多用するライブラリにとって便利です。必要のないときに `objc` 属性を適用すると、バイナリサイズが大きくなり、パフォーマンスに悪影響を与える可能性があります。

### propertyWrapper

この属性をクラス、構造体、または列挙型宣言に適用すると、その型をプロパティラッパとして使用できます。この属性を型に適用すると、型と同じ名前のカスタム属性が作成されます。その新しい属性をクラス、構造体、または列挙型のプロパティに適用して、ラッパ型のインスタンスを介してプロパティへのアクセスをラップします。属性をローカルの格納変数宣言に適用して、変数へのアクセスを同じ方法でラップします。計算変数、グローバル変数、および定数には、プロパティラッパを使用できません。

ラッパは `wrappedValue` インスタンスプロパティを定義する必要があります。プロパティのラップされた値は、このプロパティの get と set が公開する値です。ほとんどの場合、`wrappedValue` は計算値ですが、代わりに格納値にすることもできます。ラッパは、ラップされた値に必要なストレージを定義および管理します。コンパイラは、ラップされたプロパティの名前の前にアンダースコア(`_`)を付けることにより、ラッパ型のインスタンスのストレージを合成します。例えば、`someProperty` のラッパは `_someProperty` として格納されます。ラッパの合成ストレージのアクセス制御レベルは `private` です。

プロパティラッパを持つプロパティには、`willSet` ブロックと `didSet` ブロックを含めることができますが、コンパイラで合成された `get` ブロックまたは `set` ブロックをオーバーライドすることはできません。

Swift は、プロパティラッパを初期化するための 2 つの形式の糖衣構文(シンタックスシュガー)を提供します。ラップされた値の定義で代入構文を使用して、代入の右側にある式を、プロパティラッパのイニシャライザの `wrappedValue` の引数として渡すことができます。属性をプロパティに適用するときに属性に引数を指定することもでき、それらの引数はプロパティラッパのイニシャライザに渡されます。例えば、下記のコードでは、`SomeStruct` は `SomeWrapper` が定義する各イニシャライザを呼び出します。

```swift
@propertyWrapper
struct SomeWrapper {
    var wrappedValue: Int
    var someValue: Double
    init() {
        self.wrappedValue = 100
        self.someValue = 12.3
    }
    init(wrappedValue: Int) {
        self.wrappedValue = wrappedValue
        self.someValue = 45.6
    }
    init(wrappedValue value: Int, custom: Double) {
        self.wrappedValue = value
        self.someValue = custom
    }
}

struct SomeStruct {
    // init() を使用します
    @SomeWrapper var a: Int

    // init(wrappedValue:) を使用します
    @SomeWrapper var b = 10

    // どちらも init(wrappedValue:custom:) を使用します
    @SomeWrapper(custom: 98.7) var c = 30
    @SomeWrapper(wrappedValue: 30, custom: 98.7) var d
}
```

ラップされたプロパティの projected value は、プロパティラッパが追加機能を公開するために使用できる 2 番目の値です。プロパティラッパ型の作成者は、その projected value の意味を決定し、projected value が公開するインターフェイスを定義する責任があります。プロパティラッパから値を projected value を提供するには、ラッパ型で `projectedValue` インスタンスプロパティを定義します。コンパイラは、ラップされたプロパティの名前の前にドル記号(`$`)を付けることにより、projected value の識別子を合成します。例えば、`someProperty` の projected value は `$someProperty` です。projected value は、元のラップされたプロパティと同じアクセス制御レベルを持ちます。

```swift
@propertyWrapper
struct WrapperWithProjection {
    var wrappedValue: Int
    var projectedValue: SomeProjection {
        return SomeProjection(wrapper: self)
    }
}
struct SomeProjection {
    var wrapper: WrapperWithProjection
}

struct SomeStruct {
    @WrapperWithProjection var x = 123
}
let s = SomeStruct()
s.x           // Int 値
s.$x          // SomeProjection 値
s.$x.wrapper  // WrapperWithProjection 値
```

### resultBuilder

この属性をクラス、構造体、列挙型に適用して、その型を Result Builder として使用できます。Result Builder は、ネストされたデータ構造を段階的に構築する型です。Result Builder を使用して、ネストされたデータ構造を自然で宣言的な方法で作成するためのドメイン固有言語(DSL)を実装します。`resultBuilder` 属性の使用方法の例については、[Result Builders](./../language-guide/advanced-operators.md#result-buildersリザルトビルダー)を参照ください。

#### Result-Building Methods

---

Result Builder は、下記で説明する静的メソッドを実装します。Result Builder の全ての機能は静的メソッドを介して公開されるため、その型のインスタンスを初期化することはありません。`buildBlock(_:)` メソッドが必要です。DSL の追加機能を有効にする他の方法は省略可能です。Result Builder 型の宣言には、プロトコルの準拠を含める必要はありません。

静的メソッドの記述では、プレースホルダとして 3 つの型を使用しています。Expression 型は、Result Builder の入力の型のプレースホルダで、`Component` は、部分的な結果の型のプレースホルダで、`FinalResult` は、Result Builder が生成する最終的な結果の型のプレースホルダです。これらの型を、Result Builder が使用する実際の型に置き換えます。Result Builder メソッドで `Expression` または `FinalResult` の型が指定されていない場合、デフォルトで `Component` と同じになります。

Result Builder の作成方法は次のとおりです:

<dt>static func buildBlock(_ components: Component...) -> Component</dt>
<dd>

部分的な結果の配列を単一の部分的な結果に結合します。Result Builder は、このメソッドを実装する必要があります。
</dd>

<dt>static func buildOptional(_ component: Component?) -> Component</dt>
<dd>

`nil` になる可能性のある部分的な結果から部分的な結果を構築します。このメソッドを実装して、`else` 句を含まない `if` 文をサポートします。
</dd>

<dt>static func buildEither(first: Component) -> Component</dt>
<dd>

条件によって値が変化する部分的な結果を作成します。このメソッドと `buildEither(second:)` の両方を実装して、`switch` 文と `else` 句を含む `if` 文をサポートします。
</dd>

<dt>static func buildEither(second: Component) -> Component</dt>
<dd>

条件によって値が変化する部分的な結果を作成します。このメソッドと `buildEither(first:)` の両方を実装して、`switch` 文と `else` 句を含む `if` 文をサポートします。
</dd>

<dt>static func buildArray(_ components: [Component]) -> Component</dt>
<dd>

部分的な結果の配列から部分的な結果を構築します。このメソッドを実装して、ループをサポートします。
</dd>

<dt>static func buildExpression(_ expression: Expression) -> Component</dt>
<dd>

式から部分的な結果を作成します。このメソッドを実装して、前処理(例えば、式を内部型に変換する)を実行したり、使用側で型推論のための追加情報を提供したりできます。
</dd>

<dt>static func buildFinalResult(_ component: Component) -> FinalResult</dt>
<dd>

部分的な結果から最終結果を作成します。このメソッドは、部分結果と最終結果で異なる型を使用する Result Builder の一部として実装したり、結果を返す前に結果に対して後処理を実行したりできます。
</dd>

<dt>static func buildLimitedAvailability(_ component: Component) -> Component</dt>
<dd>

availability チェックを実行するコンパイラ制御文の外部で型情報を伝播または消去する部分的な結果を作成します。これを使用して、条件分岐間で異なる型情報を消去できます。
</dd>

例えば、下記のコードは、整数の配列を作成するシンプルな Result Builder を定義しています。このコードは、`Compontent` と `Expression` をタイプエイリアスとして定義し、下記の例を上記のメソッドのリストに簡単に一致させることができます。

```swift
@resultBuilder
struct ArrayBuilder {
    typealias Component = [Int]
    typealias Expression = Int
    static func buildExpression(_ element: Expression) -> Component {
        return [element]
    }
    static func buildOptional(_ component: Component?) -> Component {
        guard let component = component else { return [] }
        return component
    }
    static func buildEither(first component: Component) -> Component {
        return component
    }
    static func buildEither(second component: Component) -> Component {
        return component
    }
    static func buildArray(_ components: [Component]) -> Component {
        return Array(components.joined())
    }
    static func buildBlock(_ components: Component...) -> Component {
        return Array(components.joined())
    }
}
```

#### Result Transformations

---

次の構文変換は、Result Builder 構文を使用するコードを、Result Builder 型の静的メソッドを呼び出すコードに変換するために再帰的に適用されます。

* Result Builder に `buildExpression(_:)` メソッドがある場合、各式はそのメソッドの呼び出しになります。この変換は常に最初に行われます。例えば、次の宣言は同等です:

```swift
@ArrayBuilder var builderNumber: [Int] { 10 }
var manualNumber = ArrayBuilder.buildExpression(10)
```

* 代入文は式のように変換されますが、`()` に評価されると解釈されます。代入を具体的に処理するために `()` 型の引数を取る `buildExpression(_:)` のオーバーロードを定義できます

* availability 条件をチェックする分岐文は、`buildLimitedAvailability(_:)` メソッドの呼び出しになります。この変換は、`buildEither(first:)`、`buildEither(second:)`、または `buildOptional(_:)` の呼び出しに変換される前に行われます。`buildLimitedAvailability(_:)` メソッドを使用して、取得する分岐に応じて変化する型情報を消去します。例えば、下記の `buildEither(first:)` メソッドと `buildEither(second:)` メソッドは、両方の分岐に関する型情報をキャプチャするジェネリック型を使用します

```swift
protocol Drawable {
    func draw() -> String
}
struct Text: Drawable {
    var content: String
    init(_ content: String) { self.content = content }
    func draw() -> String { return content }
}
struct Line<D: Drawable>: Drawable {
    var elements: [D]
    func draw() -> String {
        return elements.map { $0.draw() }.joined(separator: "")
    }
}
struct DrawEither<First: Drawable, Second: Drawable>: Drawable {
    var content: Drawable
    func draw() -> String { return content.draw() }
}

@resultBuilder
struct DrawingBuilder {
    static func buildBlock<D: Drawable>(_ components: D...) -> Line<D> {
        return Line(elements: components)
    }
    static func buildEither<First, Second>(first: First)
        -> DrawEither<First, Second> {
            return DrawEither(content: first)
    }
    static func buildEither<First, Second>(second: Second)
        -> DrawEither<First, Second> {
            return DrawEither(content: second)
    }
}
```

ただし、このアプローチでは、availability チェックがあるコードで問題が発生します:

```swift
@available(macOS 99, *)
struct FutureText: Drawable {
    var content: String
    init(_ content: String) { self.content = content }
    func draw() -> String { return content }
}
@DrawingBuilder var brokenDrawing: Drawable {
    if #available(macOS 99, *) {
        FutureText("Inside.future")  // Problem
    } else {
        Text("Inside.present")
    }
}
// brokenDrawing の型は Line<DrawEither<Line<FutureText>, Line<Text>>>
```

上記のコードでは、`FutureText` は `DrawEither` ジェネリック型の 1 つのため、`brokenDrawing` の型の一部として使用されています。これにより、`FutureText` が実行時に使用できない場合、その型が明示的に使用されていない場合でも、プログラムがクラッシュする可能性があります。

この問題を解決するには、`buildLimitedAvailability(_:)` メソッドを実装して型情報を消去します。例えば、下記のコードは、availability チェックから `AnyDrawable` 値を作成します。

```swift
struct AnyDrawable: Drawable {
    var content: Drawable
    func draw() -> String { return content.draw() }
}
extension DrawingBuilder {
    static func buildLimitedAvailability(_ content: Drawable) -> AnyDrawable {
        return AnyDrawable(content: content)
    }
}

@DrawingBuilder var typeErasedDrawing: Drawable {
    if #available(macOS 99, *) {
        FutureText("Inside.future")
    } else {
        Text("Inside.present")
    }
}
// typeErasedDrawing の型は Line<DrawEither<AnyDrawable, Line<Text>>>
```

* 分岐文は、`buildEither(first:)` メソッドと `buildEither(second:)` メソッドへの一連のネストされた呼び出しになります。文の条件とケースはバイナリツリーのリーフノードにマッピングされ、文はルートノードからそのリーフノードへのパスをたどる `buildEither` メソッドへのネストされた呼び出しになります

例えば、3 つのケースを持つ `switch` 文を作成する場合、コンパイラは 3 つのリーフノードを持つバイナリツリーを使用します。同様に、ルートノードから 2 番目のケースへのパスは「2 番目の子」の「最初の子」のため、そのケースは `buildEither(first: buildEither(second: ... ))` のようなネストされた呼び出しになります。次の宣言は同等です:

```swift
let someNumber = 19
@ArrayBuilder var builderConditional: [Int] {
    if someNumber < 12 {
        31
    } else if someNumber == 19 {
        32
    } else {
        33
    }
}

var manualConditional: [Int]
if someNumber < 12 {
    let partialResult = ArrayBuilder.buildExpression(31)
    let outerPartialResult = ArrayBuilder.buildEither(first: partialResult)
    manualConditional = ArrayBuilder.buildEither(first: outerPartialResult)
} else if someNumber == 19 {
    let partialResult = ArrayBuilder.buildExpression(32)
    let outerPartialResult = ArrayBuilder.buildEither(second: partialResult)
    manualConditional = ArrayBuilder.buildEither(first: outerPartialResult)
} else {
    let partialResult = ArrayBuilder.buildExpression(33)
    manualConditional = ArrayBuilder.buildEither(second: partialResult)
}
```

* `else` 句のない `if` 文のように、値を生成しない可能性のある分岐文は、`buildOptional(_:)` の呼び出しになります。`if` 文の条件が満たされると、そのコードブロックが変換され、引数として渡されます。それ以外の場合、`buildOptional(_:)` は引数として `nil` を使用して呼び出されます。例えば、次の宣言は同等です:

```swift
@ArrayBuilder var builderOptional: [Int] {
    if (someNumber % 2) == 1 { 20 }
}

var partialResult: [Int]? = nil
if (someNumber % 2) == 1 {
    partialResult = ArrayBuilder.buildExpression(20)
}
var manualOptional = ArrayBuilder.buildOptional(partialResult)
```

コードブロックまたは `do` 文は、`buildBlock(_:)` メソッドの呼び出しになります。ブロック内の各文は一度に 1 つずつ変換され、`buildBlock(_:)` メソッドの引数になります。例えば、次の宣言は同等です:

```swift
@ArrayBuilder var builderBlock: [Int] {
    100
    200
    300
}

var manualBlock = ArrayBuilder.buildBlock(
    ArrayBuilder.buildExpression(100),
    ArrayBuilder.buildExpression(200),
    ArrayBuilder.buildExpression(300)
)
```

* `for` ループは一時変数、`for` ループ、`buildArray(_:)` メソッドの呼び出し、に分かれます。新しい `for` ループはシーケンスを繰り返し処理し、それぞれの部分的な結果をその配列に追加します。一時配列は、`buildArray(_:)` メソッドの引数として渡されます。例えば、次の宣言は同等です:

```swift
@ArrayBuilder var builderArray: [Int] {
    for i in 5...7 {
        100 + i
    }
}

var temporary: [[Int]] = []
for i in 5...7 {
    let partialResult = ArrayBuilder.buildExpression(100 + i)
    temporary.append(partialResult)
}
let manualArray = ArrayBuilder.buildArray(temporary)
```

* Result Builder に `buildFinalResult(_:)` メソッドがある場合、最終結果はそのメソッドの呼び出しになります。この変換は常に最後に行われます

変換の動作は一時変数の観点から説明されていますが、Result Builder を使用しても、コードの残りの部分から見える新しい宣言は実際には作成されません。

Result Builder が変換するコードで、`break`、`continue`、`defer`、`guard`、`return` 文、`while` 文、または `do-catch` 文を使用することはできません。

変換プロセスでは、コード内の宣言は変更されません。これにより、一時的な定数と変数を使用して、1 つずつ式を構築します。また、`throw` 文、コンパイル時の診断文、または `return` 文を含むクロージャも変更しません。

可能な限り、変換は合体します。例えば、式 `4 + 5 * 6` は、その関数への複数の呼び出しではなく、`buildExpression(4 + 5 * 6)` になります。同様に、ネストされた分岐文は、`buildEither` メソッドへの呼び出しの単一のバイナリツリーになります。

#### Custom Result-Builder Attributes

Result Builder 型を作成すると、同じ名前のカスタム属性が作成されます。その属性は、次の場所に適用できます:

* 関数宣言では、Result Builder は関数の本文を作成します
* get を含む変数または subscript の宣言で、Result Builder は get の本文を作成します
* 関数宣言の引数では、Result Builder は対応する引数として渡されるクロージャの本文を作成します

resultBuilder 属性を適用しても、ABI の互換性には影響しません。Result Builder 属性を引数に適用すると、その属性が関数のインターフェースの一部になり、ソースの互換性に影響を与える可能性があります。

### requires_stored_property_inits

この属性をクラス宣言に適用すると、クラス内に格納されている全てのプロパティに、定義の一部としてデフォルト値を提供するように要求します。この属性は、`NSManagedObject` を継承する全てのクラスに対して推論されます。

### testable

モジュールのコードのテストを簡単にするために、この属性をインポート宣言に適用すると、アクセス制御へ変更を加えて、そのモジュールをインポートします。インポートされたモジュール内の `internal` 修飾子がマークされているエンティティは、`public` 修飾子で宣言されているかのようにインポートされます。`internal` または `public` 修飾子がマークされたクラスおよびクラスメンバは、`open` 修飾子で宣言されたかのようにインポートされます。インポートされたモジュールは、テストを有効にしてコンパイルする必要があります。

### UIApplicationMain

この属性をクラスに適用して、それがアプリケーションデリゲートなことを示します。この属性を使用することは、`UIApplicationMain` 関数を呼び出し、このクラスの名前をデリゲートクラスの名前として渡すことと同じです。

この属性を使用しない場合は、[UIApplicationMain(_:_:_:_:)](https://developer.apple.com/documentation/uikit/1622933-uiapplicationmain)関数を呼び出すトップレベルのコードを `main.swift` ファイルに指定します。例えば、アプリが `UIApplication` の独自のサブクラスをメインクラスとして使用している場合、この属性を使用する代わりに `UIApplicationMain(_:_:_:_:)` 関数を呼び出します。

実行可能ファイルを作成するためにコンパイルする Swift コードには、[Top-Level Code](./declarations.md#top-Level-Codeトップレベルコード)で説明されているように、ただ 1 つのトップレベルのエントリポイントのみを含めます。

### usableFromInline

この属性を関数、メソッド、計算プロパティ、subscript、イニシャライザ、またはデイニシャライザの宣言に適用すると、宣言と同じモジュールで定義されているインライン化されたコードでそのシンボルを使用できるようにします。宣言には、`internal` 修飾子が必要です。`usedFromInline` がマークされた構造体またはクラスは、そのプロパティに `public` または `usableFromInline` の型のみを使用できます。`usedFromInline` がマークされた列挙型は、そのケースの Raw Value と関連値に対して、`public` または `usableFromInline` の型のみを使用できます。

`public` 修飾子と同様に、この属性はモジュールの public インターフェイスの一部として宣言が公開されます。`public` とは異なり、コンパイラは、宣言のシンボルがエクスポートされている場合でも、`usableFromInline` がマークされた宣言をモジュール外のコードで参照することを許可しません。ただし、モジュール外のコードは、ランタイム時の動作を使用して宣言のシンボルとやり取りできることもあります。

`inlinable` 属性がマークされた宣言は、inlinable コードから暗黙的に使用できます。`inlinable` または `usableFromInline` のいずれかを `internal` 宣言に適用できますが、両方の属性を適用するとエラーです。

### warn_unqualified_access

この属性をトップレベルの関数、インスタンスメソッド、またはクラスまたは静的メソッドに適用すると、その関数またはメソッドがモジュール名、型名、インスタンス変数または定数などを修飾子なしで使用された場合に警告を出力します。この属性を使用して、同じスコープからアクセスできる同じ名前の関数間のあいまいさを回避できます。

例えば、Swift 標準ライブラリには、トップレベルの[min(_:_:)](https://developer.apple.com/documentation/swift/1538339-min/)関数と、同等の要素を持つ `Sequence` の `min()` メソッドの両方が含まれています。`Sequence` メソッドは `warn_unqualified_access` 属性で宣言されており、`Sequence` extension 内からどちらかを使用しようとするときの混乱を減らすのに役立ちます。

### Declaration Attributes Used by Interface Builder

Interface Builder 属性は、Xcode と同期するために InterfaceBuilder によって使用される宣言属性です。Swift は、次の Interface Builder 属性を提供します: `IBAction`、`IBSegueAction`、`IBOutlet`、`IBDesignable`、および `IBInspectable`。これらの属性は、Objective-C の対応する属性と概念的に同じです。

`IBOutlet` 属性と `IBInspectable` 属性はクラスのプロパティ宣言に適用できます。`IBAction` 属性と `IBSegueAction` 属性はクラスのメソッド宣言に適用でき、`IBDesignable` 属性はクラス宣言に適用できます。

`IBAction`、`IBSegueAction`、`IBOutlet`、`IBDesignable`、または `IBInspectable` 属性を適用すると、`objc` 属性も暗黙的に追加されます。

## Type Attributes

型属性は型にのみ適用できます。

### autoclosure

この属性を適用すると、引数のないクロージャで式を自動的にラップすることにより、式の評価を遅らせます。関数またはメソッド宣言の引数が、引数を受け取らず式の型の値を返す関数型の場合に適用できます。`autoclosure` 属性の使用方法の例については、[Autoclosures](./../language-guide/closures.md#autoclosures自動クロージャ)と[Function Type](./types.md#function-type関数型)を参照ください。

### convention

呼び出し規約を示すために、この属性を関数の型に適用します。

`convention` 属性は、常に次のいずれかの引数で表示されます:

* `swift` 引数は、Swift 関数の参照を示します。これは、Swift の関数値の標準的な呼び出し規約です
* `block` 引数は、Objective-C 互換のブロック参照を示します。関数値は、ブロックオブジェクトへの参照として表されます。ブロックオブジェクトは、その呼び出し関数をオブジェクト内に埋め込む `id` 互換の Objective-C オブジェクトへの参照です。呼び出し関数は C 言語の呼び出し規約を使用します
* `c` 引数は、C 言語関数の参照を示します。関数値はコンテキストを持たず、C 言語の呼び出し規約を使用します

いくつかの例外を除いて、他の呼び出し規約の関数が必要な場合は、任意の呼び出し規約の関数を使用できます。非ジェネリックグローバル関数、ローカル変数をキャプチャしないローカル関数、またはローカル変数をキャプチャしないクロージャは、C 言語の呼び出し規約に変換できます。他の Swift 関数は C 言語の呼び出し規約に変換できません。Objective-C の block 呼び出し規約を持つ関数は、C 言語の呼び出し規約に変換できません。

### escaping

この属性を関数またはメソッド宣言の引数の型に適用すると、引数の値を後で実行するために保持されることがあることを示します。これは、値が呼び出し側の生存期間を超えて存続できることを意味します。`escaping` 属性を持つ関数型引数は、プロパティまたはメソッドに対して `self` を明示的に使用する必要があります。`escaping` 属性の使用方法の例については、[Escaping Closures](./../language-guide/closures.md#escaping-closuresエスケープクロージャ)を参照ください。

## Switch Case Attributes

switch case 属性は、switch 文のケースにのみ適用できます。

### unknown

この属性を switch ケースに適用すると、コードのコンパイル時にわかっているどの列挙ケースとも一致することがない可能性を示します。`unknown` 属性の使用方法の例については、[Switching Over Future Enumeration Cases](./statements.md#switching-over-future-enumeration-cases列挙型の将来のケースのスイッチング)を参照ください。

> GRAMMAR OF AN ATTRIBUTE  
> attribute → `@` [attribute-name](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_attribute-name)  [attribute-argument-clause](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_attribute-argument-clause)<sub>*opt*</sub>  
> attribute-name → [identifier](https://docs.swift.org/swift-book/ReferenceManual/LexicalStructure.html#grammar_identifier)  
> attribute-argument-clause → `(` [balanced-tokens](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_balanced-tokens)<sub>*opt*</sub> `)`  
> attributes → [attribute](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_attribute)  [attributes](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_attributes)<sub>*opt*</sub>   
> balanced-tokens → [balanced-token](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_balanced-token)  [balanced-tokens](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_balanced-tokens)<sub>*opt*</sub>  
> balanced-token → `(` [balanced-tokens](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_balanced-tokens)<sub>*opt*</sub> `)`  
> balanced-token → `[` [balanced-tokens](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_balanced-tokens)<sub>*opt*</sub> `]`  
> balanced-token → `{` [balanced-tokens](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#grammar_balanced-tokens)<sub>*opt*</sub> `}`  
> balanced-token → 任意の識別子、キーワード、リテラル、または演算子  
> balanced-token → `(`,  `)`,  `[`,  `]`,  `{`, または  `}` を除く任意の句読点  
