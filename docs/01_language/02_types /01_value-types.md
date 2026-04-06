# 01 Value Types

値型は、**値そのものを保持する型**です。  
値型の理解は、コピーの発生、メモリ上の振る舞い、null の扱い、性能の考え方に直結します。

---

## 値型とは何か

値型は、変数の中に実体の値が入る型です。  
代入すると、基本的にはその値がコピーされます。

代表例:

- `int`
- `bool`
- `char`
- `decimal`
- `struct`
- `enum`
- `DateTime`
- `TimeSpan`

---

## 値型の特徴

### 1. 代入でコピーされる
値型は、別の変数へ代入すると値がコピーされます。

```csharp
int a = 10;
int b = a;

b = 20;
```
この場合、a は 10 のままで、b だけが 20 になります。

### 2. 変更の影響が局所的

コピーされた先を変更しても、元の値には影響しません。
この性質は安全ですが、大きな構造体ではコピーコストに注意が必要です。

### 3. null をそのまま持てない

通常の値型は null を持ちません。
ただし、Nullable<T> または T? を使うことで null を許容できます。

```csharp
int? score = null;
```

---

## よく使う値型

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

## 値型を使うときの考え方

値型は次のようなときに向いています。

- 小さいデータである
- コピーしても問題ない
- 独立した値として扱いたい
- null が不要、または明示的に nullable にしたい

逆に、次のようなときは慎重に考えます。
- 大きいデータを頻繁にコピーする
- 共有状態を持たせたい
- 複雑なライフサイクル管理が必要
- 参照の共有が必要

---

## 値型の落とし穴

### 1. コピーしたつもりで別物になる

値型はコピーされるため、変更が元へ伝わりません。
「更新したのに反映されない」ように見えることがあります。

### 2. 大きな struct は重い

小さい値型は扱いやすいですが、サイズが大きい struct を多用するとコピーコストが増えます。

### 3. boxing が発生することがある

値型を object や interface として扱うと、boxing が起きる場合があります。
これは性能とメモリに影響することがあります。

---

## 値型とメソッド

値型をメソッドに渡すと、基本的にはコピーされます。
```csharp
static void Update(int x)
{
    x = 999;
}

int value = 10;
Update(value);

Console.Writeline(value);
//結果 10

```

この場合、value は変わりません。
参照渡しにしたい場合は ref を使います。

```csharp
static void Update(ref int x)
{
    x = 999;
}

int value = 10;
Update(value);

Console.Writeline(value);
//結果 999
```

---

## 値型の設計で意識すること
- 小さく保つ
- 不変にできるなら不変にする
- 変更の影響範囲を理解する
- null が必要なら T? を明示する
- 比較やコピーの意味を明確にする

---

## 実務での判断基準

次のように考えると整理しやすいです。
- これは値そのものか
- コピーされても問題ないか
- 参照を共有したいか
- null を持つ必要があるか
- 比較は値比較でよいか

この問いに答えられると、値型の選択が安定します。
