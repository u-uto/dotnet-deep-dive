# 01 Value Types

## 概要

値型は、**値そのものを保持する型**です。  
C# では、コピーの挙動、null の扱い、比較の考え方、パフォーマンスへの影響を理解するうえで、値型の理解が重要です。

---

## 定義

値型とは、変数の中に実体の値を直接持つ型です。  
別の変数に代入すると、基本的にはその値がコピーされます。


### 値型の分類
値型は、大きく分けて**「構造体型」**と**「列挙型」**に分類されます。

#### 1. 構造体型 (Structure types)
* **単純型 (Simple Types)** 
    * C#がキーワード（予約語）として提供している、最も基本的な型です。
    * 実体は .NET の構造体（System.Int32 など） ですが、コード上で `10` や `true` といったリテラルを直接書ける特権を持っています。
    * 例: `int, long, double, bool, char` など
* **ユーザー定義構造体 (Custom Structs)** 
    * 開発者が struct キーワードを使用して、独自のデータ構造を定義したものです。
    * 例: `public struct Point { public int X, Y; }`
* **タプル型 (Tuple Types)** 
    * 複数の値を一時的にまとめるための軽量な構造体です。戻り値などで複数の値を返したい時に便利です。
    * 例: `(int, string) person = (1, "Alice");`
* **null 許容値型 (Nullable Value Types)** 
    * 本来 `null` を持てない値型に、`null` の状態を持たせられるようにした特殊な構造体です。
    * 内部的には `System.Nullable<T>` という構造体でラップされています。
    * 例: `int?, bool?`

#### 2. 列挙型 (Enumeration types) 
「意味のある名前」を持った定数の集合です。
* `enum` キーワードで定義されます。
* 内部的には整数値（デフォルトは `int`）を保持していますが、型システム上は構造体とは明確に区別されます。
* マジックナンバーを避け、コードの意図を明確にするために使用します。
* 例: `enum Season { Spring, Summer, Fall, Winter }`

---

## 特徴

### 1. 値のコピー(Copy by Value)
値型は、別の変数に代入したときに値がコピーされます。

```csharp
int a = 10;
//a->b
int b = a;

b = 20;

Console.WritLine(a)　//10
Console.WritLine(b)　//20
```

* メリット
    * 変更の影響が完全に局所化されるため、マルチスレッド環境や複雑なロジック内でも副作用を気にせず安全に扱える。

* デメリット
    * 巨大な構造体（struct）を頻繁にメソッドに渡すと、コピーのオーバーヘッドが無視できなくなるため、サイズ設計が重要

### 2. メモリ配置 (Memory Allocation)
値型は、多くの場合ヒープではなく「スタック（Stack）」領域に直接配置されます

* メリット
    * **高速な割り当てと解放:** メソッド終了と同時にメモリが消えるため、ガベージコレクション（GC）の負荷がかからない

* デメリット
    * **スタック不足のリスク:** 非常に巨大な値をスタックに積みすぎると StackOverflowException の原因になる（通常は数バイト〜数十バイト程度に収める）

### 3. null 非許容 (Non-Nullable)

通常の値型は null を持ちません。
ただし、Nullable<T> または T? を使うことで null を許容できます。

```csharp
int score = null; // コンパイルエラー
int? nullableScore = null; // Nullable型ならOK

```

* メリット
    * **NullReferenceException の回避:** null チェックをせずに安心して計算や処理に利用できる

* デメリット
    * **未定義状態の表現:** 「データがまだ存在しない」という状態を表したい場合、Nullable<T> を使う手間とメモリの追加消費が発生する。


### 4. ボクシング (Boxing)

値型を object 型やインターフェース型として扱う際、一時的にヒープ領域へ移動する現象です。

```csharp
int i = 123;
object o = i; // Boxing発生（ヒープへのコピー）
```

* メリット
    * **汎用性:** 抽象的な型（object や interface）として扱えるため、汎用的なロジックに組み込める

* デメリット
    * **パフォーマンス低下:** ヒープへの割り当てとコピーが発生し、最終的に GC のスキャン対象になるため、頻繁な発生は避けるべき。

---

## 代表例

`int`

最もよく使う整数型です。

```csharp
int count = 100;
```


`bool`

真偽値です。

```csharp
bool isActive = true;
```


`char`

1文字を表します。

```csharp
char c = 'A';
```


`decimal`

金額計算でよく使います。
浮動小数点誤差を避けたい場面で有効です。

```csharp
decimal price = 1200.50m;
```


`DateTime`

日時を表します。

```csharp
DateTime now = DateTime.Now;
```


`TimeSpan`

時間差を表します。

```csharp
TimeSpan duration = TimeSpan.FromMinutes(30);
```


`struct`

自分で定義できる値型です。

```csharp
public struct Point
{
    public int X;
    public int Y;
}

Point p1 = new Point { X = 1, Y = 2 };
Point p2 = p1;

p2.X = 10;

//このとき p1 と p2 は別の値です。
//p2.X を変えても p1.X は変わりません。
```

---

## 使いどころ

値型は次のようなときに向いています。

- 小さくて単純な値を表したい
- コピーされても問題ない
- 値そのものとして扱いたい
- null が不要、または明示的に nullable にしたい
- 状態の共有を避けたい

たとえば、個数、フラグ、日付、時間差、状態コードなどは値型と相性がよいです。

逆に、次のような場面では慎重に考えます。

- 大きいデータを頻繁に扱う
- 参照を共有したい
- 変更を複数箇所で反映したい
- 複雑なライフサイクル管理が必要

---

## 注意点

### 1. コピーしたつもりで別物になる

値型はコピーされるため、変更が元へ伝わりません。
「更新したのに反映されない」ように見えることがあります。

### 2. 大きな struct は重い

小さい値型は扱いやすいですが、サイズが大きい struct を多用するとコピーコストが増えます。

### 3. null の扱いを明示しないと混乱する

値型はそのままでは null を持てません。
null を許容するのか、しないのかを設計段階で決める必要があります。

### 4. boxing / unboxing に気づきにくい

一見普通の代入に見えても、内部で boxing が発生することがあります。
性能が気になる場面では特に注意が必要です。

### 5. struct は何でも値型にすればよいわけではない

「値型にしたいから struct にする」という判断は危険です。
サイズ、変更頻度、利用方法を踏まえて選ぶ必要があります。

---

## 実装例

### 値型の代入
```csharp
int a = 10;
int b = a;

b = 20;

Console.WriteLine(a); // 10
Console.WriteLine(b); // 20
//結果 10

```

### 自作 struct
```csharp
public struct Point
{
    public int X;
    public int Y;
}

Point p1 = new Point { X = 1, Y = 2 };
Point p2 = p1;

p2.X = 10;

Console.WriteLine(p1.X); // 1
Console.WriteLine(p2.X); // 10
```

### nullable 値型
```csharp
int? score = null;

if (score.HasValue)
{
    Console.WriteLine(score.Value);
}
else
{
    Console.WriteLine("score is null");
}
```

### メソッドに渡した場合
```csharp
static void Update(int x)
{
    x = 999;
}

int value = 10;
Update(value);

Console.WriteLine(value); // 10
```

### ref で渡した場合
```csharp
static void Update(ref int x)
{
    x = 999;
}

int value = 10;
Update(ref value);

Console.WriteLine(value); // 999
```

---

## 設計指針
値型を設計するときは、次の点を意識します。
- 小さく保つ
- 意味が明確な単位にする
- コピーされても安全であることを前提にする
- null が必要なら T? を使う
- 比較の意味を明確にする
- 大きく複雑なものは無理に struct にしない
- 共有状態を持たせたいなら参照型を検討する

特に struct は、軽い値の表現に向いています。
業務ロジックの中心概念を何でも struct にするのは避けたほうがよいです。

## 判断基準
次の問いに答えると、値型を選ぶべきか判断しやすくなります。
これは値そのものか

- コピーされても問題ないか
- 参照を共有する必要があるか
- null を許容する必要があるか
- 比較は値ベースでよいか
- サイズが小さいか
- 頻繁にコピーされても問題ないか

これらに「はい」と言えるなら、値型は有力な選択肢です。
逆に、共有や変更の伝播が重要なら参照型を検討します。

---

## まとめ

値型は、値そのものを保持する型です。
コピーされること、null をそのまま持てないこと、boxing が起きることなどに特徴があります。

値型を正しく理解すると、null の扱い、比較、メソッド引数、性能面の判断が安定します。
C# の型設計の土台になる重要な概念です。
