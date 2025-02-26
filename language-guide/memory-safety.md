# Memory Safety \(メモリ安全性\)

最終更新日: 2021/6/6

デフォルトでは、Swift はコード内の安全ではない動作の発生を防ぎます。例えば、Swift では、変数が初期化される前に使用されていないこと、メモリの割り当てが解除された後にメモリにアクセスされていないこと、配列インデックスの範囲外エラーがチェックされること、を保証しています。

また、Swift はメモリアドレスを変更するコードへの排他的アクセスを要求することで、メモリの同じ領域へ複数が競合してアクセスしないようにします。Swift はメモリを自動的に管理するため、ほとんどの場合、メモリへのアクセスについて考える必要はまったくありません。ただし、潜在的な競合が発生する可能性がある位置を理解することが重要です。そうすることで、メモリへのアクセスが競合するコードを記述しないようにすることができます。コードに競合が含まれている場合、コンパイル時または実行時エラーが発生します。

## Understanding Conflicting Access to Memory\(メモリへのアクセスの競合を理解する\)

変数の値を設定したり、関数に引数を渡したりするときに、コード内でメモリへのアクセスが発生します。例えば、次のコードには読み取りアクセスと書き込みアクセスの両方が含まれています:

```swift
// 1 が格納されているメモリへの書き込みアクセス
var one = 1

// 1 が格納されているメモリからの読み取りアクセス
print("We're number \(one)!")
```

コードの異なる部分で同じメモリアドレスに同時にアクセスしようとすると、メモリへのアクセスの競合が発生する可能性があります。同じメモリアドレスに同時に複数のアクセスを行うと、予測できない動作や一貫性のない動作が発生する可能性があります。Swift には、数行のコードにまたがってを変更する方法があり、値自体の変更の途中で値へのアクセスを試みることができます。

紙に書かれた予算をどのように更新するかを考えて、同様の問題を見てみます。予算は 2 段階のプロセスの更新されます。最初にアイテムの名前と価格を追加し、次に合計金額を変更して、現在リストにあるアイテムを反映します。更新の前後に、下の図に示すように、予算から任意の情報を読み取り、正しい答えを得ることができます。

![&#x30E1;&#x30E2;&#x30EA;&#x30A2;&#x30AF;&#x30BB;&#x30B9;&#x306E;&#x7D19;&#x306E;&#x4E88;&#x7B97;&#x306E;&#x4F8B;](../.gitbook/assets/memory_shopping_2x.png)

予算にアイテムを追加している間は、新しく追加されたアイテムを反映するまで合計金額が更新されていないため、一時的に不整合な状態になります。アイテムを追加するプロセス中に合計金額を読むと、間違った情報が表示されます。

この例は、メモリへのアクセスの競合を修正するときに発生する可能性のある問題も示しています。競合を修正するには複数の方法があり、異なる回答があり、どちらの回答が正しいかは必ずしも明らかではありません。この例では、元の合計金額または更新された合計金額のどちらが必要かに応じて、5 ドルまたは 320 ドルが正しい答えになる可能性があります。競合するアクセスを修正する前に、それが何を意図していたかを判断する必要があります。

> NOTE  
> 同時実行またはマルチスレッドコードを作成したことがある場合、メモリへのアクセスの競合はよくある問題かもしれません。ただし、ここで説明するアクセスの競合はシングルスレッドでも発生する可能性があり、同時実行またはマルチスレッド化されたコードは関係しません。
>
> シングルスレッド内からメモリへのアクセスが競合している場合、Swift はコンパイル時または実行時にエラーを確実に投げします。マルチスレッドコードの場合は、[Thread Sanitizer](https://developer.apple.com/documentation/xcode/diagnosing-memory-thread-and-crash-issues-early)を使用して、スレッド間で競合するアクセスを検出します。

### Characteristics of Memory Access\(メモリアクセスの性質\)

競合するアクセスの文脈上で考慮すべきメモリアクセスの 3 つの特性があります: アクセスが読み取りか書き込みか、アクセスの期間、およびアクセスされるメモリアドレスです。具体的には、次の条件の全てを満たす 2 つのアクセスがある場合、競合が発生します。

* 少なくとも 1 つは、書き込みアクセスまたは非アトミックなアクセス
* 同じメモリアドレスにアクセス
* メモリへのアクセス期間が重複

通常、読み取りと書き込みアクセスの違いは明らかです。書き込みアクセスはメモリアドレスを変更しますが、読み取りアクセスは変更しません。メモリアドレスは、アクセスされているもの\(変数、定数、プロパティなど\)を参照します。メモリアクセスの持続時間は、瞬時\(_instantaneous_\)または長期\(_long-term_\)です。

C 言語のアトミック操作を使用する場合のみ、操作はアトミックです。それ以外の場合は非アトミックです。これらの関数のリストについては、`stdatomic(3)` のマニュアルページを参照してください。

アクセスが開始されてから終了する前に他のコードを実行できない場合、アクセスは瞬時に行われます。その性質上、2 つの瞬間的なアクセスは同時に発生することはありません。ほとんどのメモリアクセスは瞬時に行われます。例えば、下記のコードリストの全ての読み取りおよび書き込みアクセスは瞬時に行われます:

```swift
func oneMore(than number: Int) -> Int {
    return number + 1
}

var myNumber = 1
myNumber = oneMore(than: myNumber)
print(myNumber)
// "2"
```

ただし、長期アクセス\(_long-term access_\)と呼ばれる、他のコードの実行にまたがってメモリにアクセスする方法がいくつかあります。瞬時アクセスと長期アクセスの違いは、長期アクセスは開始から終了するまでの間に、他のコードが実行される可能性があることです。これをオーバーラップ\(_overlap_\)と呼びます。長期アクセスは、他の長期アクセスや瞬時アクセスと重複する可能性があります。

オーバーラップアクセスは、主に、関数とメソッド、または構造体の変更メソッドで `inout` 引数を使用するコードで発生します。長期アクセスを使用する特定の種類の Swift コードについては、次のセクションで説明します。

## Conflicting Access to In-Out Parameters\(In-Out引数のアクセスの競合\)

関数には、全ての in-out 引数へ長期書き込みアクセスできます。in-out 引数への書き込みアクセスは、全ての in-out 以外の引数が評価された後に開始され、その関数呼び出しの全期間にわたって続きます。複数の in-out 引数がある場合、書き込みアクセスは引数が現れるのと同じ順序で開始されます。

この長期書き込みアクセスの問題の 1 つは、スコープルールとアクセス制御で許可されていても、in-out として渡された元の変数にアクセスできないことです。元の変数にアクセスすると、競合が発生します。例えば:

```swift
var stepSize = 1

func increment(_ number: inout Int) {
    number += stepSize
}

increment(&stepSize)
// エラー: stepSize へのアクセスが競合しています
```

上記のコードでは、`stepSize` はグローバル変数で、通常は `increment(_:)` 内からアクセスできます。ただし、`stepSize` への読み取りアクセスは、`number` への書き込みアクセスと重複します。次の図に示すように、`number` と `stepSize` は両方メモリ内の同じ位置を参照します。読み取りと書き込みアクセスは同じメモリを参照し、それらが重複して競合が発生します。

![in-out&#x5F15;&#x6570;&#x306E;&#x30E1;&#x30E2;&#x30EA;&#x30A2;&#x30AF;&#x30BB;&#x30B9;&#x306E;&#x7AF6;&#x5408;](../.gitbook/assets/memory_increment_2x.png)

この競合を解決する 1 つの方法は、`stepSize` の明示的なコピーを作成することです。

```swift
// 明示的なコピーを作成します
var copyOfStepSize = stepSize
increment(&copyOfStepSize)

// オリジナルを更新します
stepSize = copyOfStepSize
// stepSize は 2 になりました
```

`increment(_:)` を呼び出す前に `stepSize` のコピーを作成すると、`copyOfStepSize` の値が現在の `stepSize` によって増分されることが明らかです。書き込みアクセスが開始される前に読み取りアクセスが終了するため、競合はありません。

in-out 引数への長期書き込みアクセスの別の問題として、同じ関数の複数の in-out 引数に 1 つの変数を共通して渡すと、競合が発生します。例えば:

```swift
func balance(_ x: inout Int, _ y: inout Int) {
    let sum = x + y
    x = sum / 2
    y = sum - x
}
var playerOneScore = 42
var playerTwoScore = 30
balance(&playerOneScore, &playerTwoScore)  // OK
balance(&playerOneScore, &playerOneScore)
// エラー: playerOneScore へのアクセスが競合しています
```

上記の `balance(_:_:)` 関数は、その 2 つの引数を変更して、合計値をそれらの間で均等に分割します。`playerOneScore` と `playerTwoScore` を引数として呼び出しても競合は発生しません。時間的に重複する 2 つの書き込みアクセスがありますが、メモリ内の異なる位置にアクセスします。対照的に、両方の引数の値として `playerOneScore` を渡すと、同じメモリアドレスへの 2 つの書き込みアクセスを同時に実行しようとするため、競合が発生します。

> NOTE  
> 演算子は関数であるため、in-out 引数に長期アクセスすることもできます。例えば、`balance(_:_:)` が `<^>` という演算子である場合、`playerOneScore <^> playerOneScore` と記述すると、`balance(&playerOneScore, &playerOneScore)` と同じ競合が発生します。

## Conflicting Access to self in Methods\(メソッド内のselfのアクセスの競合\)

構造体の mutating メソッドは、メソッド呼び出しの間、`self` への書き込みアクセス権を持ちます。例えば、各プレイヤーがダメージを受けると減少する活力値と、特殊能力を使用すると減少するエネルギー量を持つゲームを考えてみましょう。

```swift
struct Player {
    var name: String
    var health: Int
    var energy: Int

    static let maxHealth = 10
    mutating func restoreHealth() {
        health = Player.maxHealth
    }
}
```

上記の `restoreHealth()` メソッドでは、`self` への書き込みアクセスはメソッドの最初から戻り値を返すまで続きます。この場合、`restoreHealth()` 内には、`Player` インスタンスのプロパティへのアクセスが重複する可能性のある他のコードはありません。下記の `shareHealth(with:)` メソッドは、別の `Player` インスタンスを in-out 引数として受け取り、アクセスが重複する可能性があります。

```swift
extension Player {
    mutating func shareHealth(with teammate: inout Player) {
        balance(&teammate.health, &health)
    }
}

var oscar = Player(name: "Oscar", health: 10, energy: 10)
var maria = Player(name: "Maria", health: 5, energy: 10)
oscar.shareHealth(with: &maria)  // OK
```

上記の例では、Oscar のプレーヤーが Maria のプレーヤーと活力を共有するために `shareHealth(with:)` メソッドを呼び出しても、競合は発生しません。`oscar` は mutating メソッドの `self` の値のため、メソッド呼び出し中に `oscar` への書き込みアクセスがあり、`maria` が in-out 引数として渡されるため、同じ期間、`maria` への書き込みアクセスがあります。次の図に示すように、これらは異なるメモリアドレスにアクセスします。2 つの書き込みアクセスは時間的に重複していますが、競合しません。

![in-out&#x306E;&#x30E1;&#x30E2;&#x30EA;&#x30A2;&#x30AF;&#x30BB;&#x30B9;&#x7AF6;&#x5408;OK&#x4F8B;](../.gitbook/assets/memory_share_health_maria_2x.png)

ただし、`oscar` を `shareHealth(with:)` の引数として渡すと、競合が発生します。

```swift
oscar.shareHealth(with: &oscar)
// エラー: オスカーへのアクセスが競合しています
```

mutating メソッドは、メソッド実行中に `self` への書き込みアクセスを必要とし、in-out 引数は、同じ期間中に `teammate` への書き込みアクセスを必要とします。メソッド内では、下の図に示すように、`self` と `teammate` の両方がメモリ内の同じ位置を参照します。2 つの書き込みアクセスは同じメモリを参照し、それらが重複して競合が発生します。

![in-out&#x306E;&#x30E1;&#x30E2;&#x30EA;&#x30A2;&#x30AF;&#x30BB;&#x30B9;&#x7AF6;&#x5408;NG&#x4F8B;](../.gitbook/assets/memory_share_health_oscar_2x.png)

## Conflicting Access to Properties\(プロパティのアクセスの競合\)

構造体、タプル、列挙型などの型は、構造体のプロパティやタプルの要素など、個々の構成値で構成されます。これらは値型のため、値の一部を変更すると値全体が変更されます。つまり、いずれかのプロパティへの読み取りまたは書き込みアクセスには、値全体への読み取りまたは書き込みアクセスが必要です。例えば、タプルの要素への書き込みアクセスが重複すると、競合が発生します。

```swift
var playerInformation = (health: 10, energy: 20)
balance(&playerInformation.health, &playerInformation.energy)
// エラー: playerInformation のプロパティへのアクセスが競合しています
```

上記の例では、タプルの要素で `balance(_:_:)` を呼び出すと、`playerInformation` への書き込みアクセスが重複しているため、競合が発生します。`playerInformation.health` と `playerInformation.energy` の両方が in-out 引数として渡されます。つまり、`balance(_:_:)` は、関数呼び出しの間、それらへの書き込みアクセスを必要とします。どちらの場合も、タプル要素へ書き込みアクセスするには、タプル全体への書き込みアクセスが必要です。これは、同じ期間に `playerInformation` への 2 つの書き込みアクセスがあり、競合が発生していることを意味します。

下記のコードは、グローバル変数に格納されている構造体のプロパティへの書き込みアクセスが重複している場合に同じエラーが発生することを示しています。

```swift
var holly = Player(name: "Holly", health: 10, energy: 10)
balance(&holly.health, &holly.energy)  // Error
```

実際には、構造体のプロパティへのほとんどのアクセスは安全にオーバーラップできます。例えば、上記の例の変数 `holly` がグローバル変数ではなくローカル変数に変更された場合、コンパイラは、構造体の格納されたプロパティへの重複アクセスが安全なことを証明できます:

```swift
func someFunction() {
    var oscar = Player(name: "Oscar", health: 10, energy: 10)
    balance(&oscar.health, &oscar.energy)  // OK
}
```

上記の例では、Oscar の活力とエネルギーが、`balance(_:_:)` への 2 つの in-out 引数として渡されます。2 つの格納プロパティは相互作用しないため、コンパイラはメモリの安全性が保たれていることを証明できます。

構造体のプロパティへの重複アクセスに対する制限は、メモリの安全性を維持するために必ずしも必要ではありません。メモリ安全性は保証されていることが期待されますが、排他的アクセスはメモリ安全性よりも厳しい要件で、一部のコードは、メモリへの排他的アクセスに違反している場合でも、メモリ安全性を維持します。コンパイラがメモリへの非排他的アクセスをしていても安全なことを証明できる場合、Swift はこのメモリ安全なコードを許可します。具体的には、次の条件が当てはまる場合、構造体のプロパティへの重複アクセスが安全なことを証明できます:

* 計算プロパティやクラスの型プロパティではなく、インスタンスの格納プロパティにのみアクセス
* グローバル変数ではなくローカル変数の構造体
* 構造体がどのクロージャにもキャプチャされないか、非エスケープクロージャによってのみキャプチャされている

コンパイラがアクセスがメモリ安全性を証明できない場合、アクセスは許可されません。

