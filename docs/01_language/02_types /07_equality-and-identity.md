# 07 Equality and Identity

## 概要

等価性と同一性は、**「同じ値か」** と **「同じ実体か」** を区別するための考え方です。  
C# では、`==`、`Equals`、`ReferenceEquals` に加えて、`record` の等価性の振る舞いも理解しておく必要があります。

特に `record` は、見た目は `class` に近いのに、既定で値ベースの等価性を持つため、`class` と同じ感覚で扱うと混乱しやすいです。  
この章では、比較の基本から `record` の特徴までまとめて整理します。

---

## 定義

### 等価性

等価性は、**値として同じかどうか** を表します。  
中身が同じか、意味が同じかを判断する考え方です。

### 同一性

同一性は、**同じ実体を指しているかどうか** を表します。  
参照先が同じかどうかを判断する考え方です。

### `==`

`==` は比較演算子です。  
型によって意味が変わることがあり、値比較になる場合もあれば、参照比較に近い挙動になる場合もあります。

### `Equals`

`Equals` は等価性を判定するためのメソッドです。  
型ごとに「何を同じとみなすか」を定義できます。

### `ReferenceEquals`

`ReferenceEquals` は、2つの参照が同じ実体を指しているかを判定します。  
参照型の同一性確認に使います。

### `record`

`record` は、値ベースの等価性を扱いやすくした型です。  
既定で、プロパティやフィールドの値をもとに比較されます。  
「同じ値なら同じもの」と考えたいデータ表現に向いています。

---

## 特徴

### 1. 値の比較と参照の比較は別

```csharp
var a = new PersonClass { Name = "Alice", Age = 20 };
var b = new PersonClass { Name = "Alice", Age = 20 };

Console.WriteLine(a == b);
Console.WriteLine(a.Equals(b));
Console.WriteLine(object.ReferenceEquals(a, b));
```

`a` と `b` は内容が同じでも、別の実体です。  
`class` では、何も実装しなければ参照ベースで比較されることが多いです。

---

### 2. `record` は値ベースの等価性を持つ

```csharp
public record PersonRecord(string Name, int Age);

var a = new PersonRecord("Alice", 20);
var b = new PersonRecord("Alice", 20);

Console.WriteLine(a == b);
Console.WriteLine(a.Equals(b));
Console.WriteLine(object.ReferenceEquals(a, b));
```

この場合、`a == b` と `a.Equals(b)` は `true` になり、`ReferenceEquals` は `false` になります。  
同じ値を持つ別インスタンスでも、`record` は等価と判定します。

---

### 3. `class` と `record` で意味が変わる

```csharp
public class PersonClass
{
    public string Name { get; set; } = string.Empty;
    public int Age { get; set; }
}

public record PersonRecord(string Name, int Age);
```

`class` は参照として扱うことが自然で、`record` は値として扱うことが自然です。  
どちらを使うかで、比較の意味が変わります。

---

### 4. `record` はコピーしやすい

`record` は `with` 式と相性がよく、既存の値を保ったまま一部だけ変更できます。

```csharp
var a = new PersonRecord("Alice", 20);
var b = a with { Age = 21 };

Console.WriteLine(a);
Console.WriteLine(b);
```

これは、値オブジェクト的な扱いをしたいときに便利です。

---

### 5. 比較の基準を統一しないと混乱する

同じデータでも、`class` なのか `record` なのかで比較結果の意味が変わります。  
設計で「これは同一性を見るのか、値の等価性を見るのか」を決めておかないと、バグの原因になります。

---

## 代表例

### 値型の比較

```csharp
int a = 10;
int b = 10;

Console.WriteLine(a == b);
Console.WriteLine(a.Equals(b));
```

### class の比較

```csharp
public class PersonClass
{
    public string Name { get; set; } = string.Empty;
    public int Age { get; set; }
}

var a = new PersonClass { Name = "Alice", Age = 20 };
var b = new PersonClass { Name = "Alice", Age = 20 };

Console.WriteLine(a == b);
Console.WriteLine(a.Equals(b));
```

### record の比較

```csharp
public record PersonRecord(string Name, int Age);

var a = new PersonRecord("Alice", 20);
var b = new PersonRecord("Alice", 20);

Console.WriteLine(a == b);
Console.WriteLine(a.Equals(b));
```

### 同一性の確認

```csharp
var a = new PersonClass { Name = "Alice", Age = 20 };
var b = a;

Console.WriteLine(object.ReferenceEquals(a, b));
```

---

## 使いどころ

等価性と同一性は、次のような場面で使います。

- 重複判定
- 検索
- キャッシュのキー比較
- オブジェクトの再利用確認
- テストのアサーション
- 値オブジェクトの設計
- `record` を使うか `class` を使うかの判断

たとえば、以下のような判断が必要になります。

- 「顧客情報は同じ ID なら同じものとみなすのか」
- 「入力値が同じなら同一と扱うのか」
- 「DTO は値として比較したいのか」
- 「Entity は参照として区別したいのか」

---

## 注意点

### 1. `==` と `Equals` を同じ意味だと思い込まない

型によっては同じ結果になりますが、常にそうとは限りません。  
`record` では値比較として動きやすく、`class` ではそうならないことが多いです。

---

### 2. `ReferenceEquals` は値比較ではない

```csharp
var a = new PersonRecord("Alice", 20);
var b = new PersonRecord("Alice", 20);

Console.WriteLine(object.ReferenceEquals(a, b));
```

`ReferenceEquals` は、内容ではなく実体の同一性だけを見ます。  
`record` のような値ベース設計では、これだけを使うと意図とズレることがあります。

---

### 3. `record` は便利だが、何でも record にすればよいわけではない

`record` は値ベースの等価性が便利ですが、更新頻度が高く、同一性が重要なオブジェクトには向かないことがあります。  
Entity のような「同じ ID の同じ実体」を扱う場面では、`class` のほうが自然なこともあります。

---

### 4. `record` の等価性は設計上の意味を持つ

`record` は「見た目の値が同じなら等しい」という前提で使われることが多いです。  
そのため、プロパティを増やすと比較結果が変わる可能性があります。  
比較対象に含める値を意識して設計する必要があります。

---

### 5. `Equals` と `GetHashCode` の整合性が重要

`class` で独自に等価性を定義する場合、`Equals` だけでなく `GetHashCode` との整合性も必要です。  
`record` はこのあたりを扱いやすくしていますが、理解せずに使うと誤解が起きます。

---

## 実装例

### class の参照比較

```csharp
public class PersonClass
{
    public string Name { get; set; } = string.Empty;
    public int Age { get; set; }
}

var a = new PersonClass { Name = "Alice", Age = 20 };
var b = new PersonClass { Name = "Alice", Age = 20 };

Console.WriteLine(a == b);             // False
Console.WriteLine(a.Equals(b));        // False
Console.WriteLine(object.ReferenceEquals(a, b)); // False
```

---

### record の値比較

```csharp
public record PersonRecord(string Name, int Age);

var a = new PersonRecord("Alice", 20);
var b = new PersonRecord("Alice", 20);

Console.WriteLine(a == b);             // True
Console.WriteLine(a.Equals(b));        // True
Console.WriteLine(object.ReferenceEquals(a, b)); // False
```

---

### `with` 式

```csharp
public record PersonRecord(string Name, int Age);

var a = new PersonRecord("Alice", 20);
var b = a with { Age = 21 };

Console.WriteLine(a);
Console.WriteLine(b);
```

---

### `Equals` を自前で定義する class

```csharp
public class Product
{
    public string Code { get; set; } = string.Empty;
    public string Name { get; set; } = string.Empty;

    public override bool Equals(object? obj)
    {
        return obj is Product other && Code == other.Code;
    }

    public override int GetHashCode()
    {
        return Code.GetHashCode();
    }
}
```

この場合、「Code が同じなら同じ Product」とみなします。  
どの値を比較対象にするかは、設計で明確にする必要があります。

---

### コレクションでの比較

```csharp
public record PersonRecord(string Name, int Age);

var list = new List<PersonRecord>
{
    new PersonRecord("Alice", 20),
    new PersonRecord("Bob", 30)
};

bool exists = list.Contains(new PersonRecord("Alice", 20));
Console.WriteLine(exists); // True
```

`record` は値ベースの比較が効くため、こうした用途で扱いやすいです。

---

## 設計指針

- 「同じ」の意味を先に決める
- 値の比較か参照の比較かを明示する
- 値オブジェクトには `record` を検討する
- Entity には参照としての意味を持たせる
- `Equals` と `GetHashCode` の整合性を保つ
- `record` の比較対象に含める値を意識する
- null を含む可能性も考慮する

特に、`record` は「値として扱いたいデータ」に非常に向いています。  
一方で、「同じ実体を追跡したいオブジェクト」には、`class` のほうが自然な場合があります。

---

## 判断基準

次の問いに答えると、どの比較を使うべきか判断しやすくなります。

- 値として同じかを見たいか
- 同じ実体かを見たいか
- 値が同じなら同一と見なしたいか
- `record` の自動比較が自然か
- Entity なのか Value Object なのか
- null を含む可能性があるか
- コレクション内で使うか

これらに応じて、`class`、`record`、`==`、`Equals`、`ReferenceEquals` を使い分けます。

---

## まとめ

等価性と同一性は、C# で比較を正しく行うための基本です。  
`==` は演算子、`Equals` は値の等価性、`ReferenceEquals` は同一の実体かどうかを見ます。

さらに `record` は、値ベースの等価性を持つため、比較の意味を簡潔に表現できます。  
ただし、`class` と `record` では設計上の意味が異なるため、用途を分けて考えることが重要です。

この違いを理解しておくと、オブジェクト比較、コレクション操作、テスト、設計判断が安定します。