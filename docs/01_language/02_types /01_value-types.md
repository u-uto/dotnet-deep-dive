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

## コード例

### 単純型 (Simple Types)

C# がキーワードとして提供している基本的な構造体型です。  
コード上ではリテラルをそのまま書けます。

```csharp
int count = 10;
long total = 1000L;
double rate = 0.25;
bool isActive = true;
char grade = 'A';
```

`int` のような型は、.NET では `System.Int32` に対応しています。

```csharp
System.Int32 number = 42;
int sameNumber = 42;
```

---

### ユーザー定義構造体 (Custom Structs)

`struct` を使って独自の値型を定義できます。  
小さくて、値として扱いたいデータに向いています。

```csharp
public struct Point
{
    public int X;
    public int Y;

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }
}
```

使用例:

```csharp
Point p1 = new Point(1, 2);
Point p2 = p1;

p2.X = 10;

Console.WriteLine(p1.X); // 1
Console.WriteLine(p2.X); // 10
```



---

### タプル型 (Tuple Types)

複数の値をまとめて一時的に返したいときに便利です。  
戻り値として使うと、意味のある名前付きで受け取れます。

```csharp
(int id, string name) person = (1, "Alice");

Console.WriteLine(person.id);
Console.WriteLine(person.name);
```

メソッドの戻り値として使う例:

```csharp
static (int min, int max) GetRange()
{
    return (1, 100);
}

var range = GetRange();
Console.WriteLine(range.min);
Console.WriteLine(range.max);
```

---

### null 許容値型 (Nullable Value Types)

本来 null を持てない値型に、null を持たせるための型です。  
内部的には `System.Nullable<T>` を使っています。

```csharp
int? score = null;
bool? isReady = true;
```

値があるかどうかを確認する例:

```csharp
int? score = 10;

if (score.HasValue)
{
    Console.WriteLine(score.Value);
}
else
{
    Console.WriteLine("null");
}
```

`??` を使って既定値を与える例:

```csharp
int? score = null;
int result = score ?? 0;

Console.WriteLine(result);
```

---

### 列挙型 (Enumeration Types)

意味のある名前を持った定数の集合です。  
状態や種別を分かりやすく表せます。

```csharp
public enum Season
{
    Spring,
    Summer,
    Fall,
    Winter
}
```

使用例:

```csharp
Season season = Season.Summer;

if (season == Season.Summer)
{
    Console.WriteLine("夏です");
}
```

内部的には整数として扱われるため、明示的に変換することもできます。

```csharp
Season season = Season.Winter;
int value = (int)season;

Console.WriteLine(value); // 3
```

---

### フラグとしての enum

複数の状態を組み合わせたい場合は `[Flags]` を使います。

```csharp
[Flags]
public enum Permission
{
    None    = 0,
    Read    = 1,
    Write   = 2,
    Execute = 4
}
```

使用例:

```csharp
Permission permission = Permission.Read | Permission.Write;

bool canRead = (permission & Permission.Read) != 0;
bool canWrite = (permission & Permission.Write) != 0;
bool canExecute = (permission & Permission.Execute) != 0;

Console.WriteLine(canRead);    // True
Console.WriteLine(canWrite);   // True
Console.WriteLine(canExecute); // False
```

---

## 注意点

### 1.  「値のコピー」による更新漏れ

値型はコピーされるため、変更が元へ伝わりません。
「更新したのに反映されない」ように見えることがあります。

#### 対策1: readonlyを使用して不変(Immutable)として定義する
**Bad**
```csharp
public struct MutablePoint { public int X; }

void Process() {
    var p = new MutablePoint { X = 10 };
    
    // プロパティから取得した瞬間に「コピー」が作られる
    var copyP = p; 
    copyP.X = 99; // コピーを書き換えただけ
    
    Console.WriteLine(p.X); // 結果: 10 (元は変わっていない)
    // 💡 「あれ？99にしたはずなのに反映されていない」という事故が起きる
}

```

**Good**

```csharp
// 型自体を readonly にして、外部からの書き換えを禁止する
public readonly struct ImmutablePoint {
    public int X { get; init; }
    public ImmutablePoint(int x) => X = x;
}

void Process() {
    readonly var p = new ImmutablePoint(10);
    
    // p.X = 99; // コンパイルエラーになるため、事故が起きない
    
    // 値を変えたい場合は「新しいインスタンス」を代入し直す
    var newP = new ImmutablePoint(99); 
    Console.WriteLine(newP.X); // 結果: 99
}
```
#### 対策2: refを利用して参照する

**Bad**
```csharp
void Move(Point p) {
    p.X += 10; // 引数の時点でコピーされているため、この変更は呼び出し元に伝わらない
}

Point myPoint = new Point(0, 0);
Move(myPoint);
Console.WriteLine(myPoint.X); // 結果: 0 (更新されていない！)

```

**Good**

```csharp
// ref を付けることで、コピーではなく「変数の場所（参照）」を渡す
void Offset(ref Point p) {
    p.X += 10; // 呼び出し元の実体を直接書き換える
}

Point myPoint = new Point(0, 0);
// 呼び出し側も ref を明示する必要がある（「書き換わる可能性がある」という合図）
Offset(ref myPoint); 

Console.WriteLine(myPoint.X); // 結果: 10 (正しく更新されている)
```

### 2. ボックス化（Boxing）によるパフォーマンス劣化
値型が object 型やインターフェース型にキャストされるとき、スタックからヒープへデータを移す「ボックス化」が発生します

#### 対策1： ジェネリクス <T> を活用し、型を保ったまま扱う

**Bad** 
```csharp
// object型を受け取るため、intを渡すとボックス化が発生する
void PrintObject(object value)
{
    Console.WriteLine(value.ToString());
}

int score = 100;
PrintObject(score); // 実行時にスタックからヒープへコピーされる

```
**Good** 
```csharp
// ジェネリクスを使うと、intのままメソッド内に持ち込める
void PrintGeneric<T>(T value)
{
    // valueがintの場合、int.ToString() が直接呼ばれる
    Console.WriteLine(value?.ToString());
}

int score = 100;
PrintGeneric(score); // ボックス化なし（型が保たれる）

```

#### 対策2: 文字列補完を利用

**Bad** 
```csharp
int score = 100;
// string.Concat(object, object) が呼ばれ、内部で int が object に変換される
Console.WriteLine("Score: " + score); 

```
**Good** 
```csharp
int score = 100;
// 文字列補間を使用すると、内部的に最適な処理が行われボックス化を回避できる
Console.WriteLine($"Score: {score}"); 

```

### 3. 巨大な構造体によるコピー負荷

値型は基本的に引数に渡すたびに全データがコピーされます。
構造体の中に多くのプロパティを持たせると、メソッド呼び出しのたびにメモリのコピーが発生し、逆に動作が重くなります。

#### 対策1: 構造体の合計サイズが(一般的に)32バイトを超える場合は、classとして定義します。

**Bad** 
```csharp
// 160バイト（16byte * 10）の巨大な構造体
public struct LargeStruct { public decimal V1, V2, V3, V4, V5, V6, V7, V8, V9, V10; }

// 呼ぶたびに 160バイトがコピーされ、CPUキャッシュを圧迫する
void Process(LargeStruct data) { /* ... */ }

```

**Good** 
```csharp
// 対策1: 32バイトを超えるなら class（参照型）にする
public class LargeData { /* ... */ }

// 対策2: 構造体のまま 'in' 引数（読み取り専用参照渡し）を使い、コピーを防ぐ
void Process(in LargeStruct data) { /* ... */ }
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
