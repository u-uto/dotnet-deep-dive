# 05 Nullable Types

## 概要

nullable は、**「値を持つことも、持たないこともある」** という状態を表すための考え方です。  
C# では、値型に対する nullable と、参照型に対する nullable を分けて理解することが重要です。

null を雑に扱うと、例外、分岐の増加、設計の不明瞭さにつながります。  
そのため nullable は、単なる文法ではなく、**設計の約束**として扱う必要があります。

---

## 定義

### 値型の nullable

値型はそのままでは null を持てません。  
`T?` または `Nullable<T>` を使うことで null を許容できます。

```csharp
int? score = null;
```

### 参照型の nullable

参照型はもともと null を持てます。
ただし、nullable reference types を有効にすると、null を許容するかどうかを型で明示できます。

```csharp
string? name = null;
```

## 特徴

### 1. 値がない状態を表せる

nullable は、「未設定」「不明」「該当なし」を表現できます。

```csharp
int? age = null;
```

### 2. null の有無を確認できる

値型 nullable には HasValue と Value があります。

```csharp
int? score = 10;

if (score.HasValue)
{
    Console.WriteLine(score.Value);
}
```

### 3. ?? で既定値を与えられる

```csharp
int? score = null;
int actualScore = score ?? 0;
```

### 4. ?. で安全にアクセスできる

```csharp
string? name = null;
int? length = name?.Length;
```

### 5. ! は「大丈夫だと断言する」記法

```csharp
string? name = GetName();
Console.WriteLine(name!.Length);
```

! は null を消すわけではなく、コンパイラへの宣言です。
実際には null の可能性が残るため、使いすぎは危険です。

---

## 代表例

### 値型 nullable

```csharp
int? score = null;
DateTime? createdAt = null;
```

### 参照型 nullable

```csharp
string? name = null;
Person? person = null;
```

### null 合体演算子

```csharp
string? name = null;
string displayName = name ?? "Unknown";
```

### null 条件演算子

```csharp
string? name = null;
int? length = name?.Length;
```

---

## 使いどころ

nullable は、次のような場面で使います。
- 値が未設定であることを表したい
- DB の NULL を表現したい
- 入力が任意であることを表したい
- 値がまだ確定していない
- 「存在しない」ことを型で明示したい

たとえば、以下のようなものは nullable と相性がよいです。
- 生年月日が未入力
- 検索条件が任意
- 終了日時が未設定
- スコアが未採点

---

## 注意点

### 1. null の意味を曖昧にしない

null が何を意味するのかを決めずに使うと、コードの意味が崩れます。

null は状況によって次のような意味を持ちます。
- 未入力
- 不明
- 未処理
- 該当なし

これらを混同すると、設計が不安定になります。

### 2. Value の直接参照は危険

```csharp
int? score = null;
Console.WriteLine(score.Value);
```

HasValue を確認せずに Value を読むと例外になります。

### 3. ! を多用すると安全性が落ちる

! は見た目上は便利ですが、null でない保証がない箇所に使うと危険です。
本当に保証できる場面だけに限定すべきです。

### 4. null を返すか、空を返すかを揃える必要がある

メソッドやプロパティごとに null の返し方がバラバラだと、利用側が扱いにくくなります。

### 5. 参照型 nullable は設計ルールが重要

nullable reference types を有効にしても、設計が雑だと警告だけが増えます。
「null を許可するのか」を明確に決める必要があります。

---

## 実装例

### 値型 nullable の判定

```csharp
int? score = 10;

if (score.HasValue)
{
    Console.WriteLine(score.Value);
}
else
{
    Console.WriteLine("score is null");
}
```

### 既定値を与える

```csharp
int? score = null;
int actualScore = score ?? 0;

Console.WriteLine(actualScore);
```

### null 条件演算子

```csharp
string? name = null;
int? length = name?.Length;

Console.WriteLine(length);
```

### 参照型 nullable

```csharp
string? name = GetName();

if (name is not null)
{
    Console.WriteLine(name.Length);
}
```

## 設計指針
- null の意味を先に決める
- 「未設定」と「空」を混同しない
- null を返す場所を限定する
- null を扱う側で分岐を散らしすぎない
- 必要なら専用の型や状態を使う
- ! は最後の手段にする

nullable は「とりあえず null を許す」ためのものではありません。
状態を正確に表現するための道具として使うのが重要です。

---

## 判断基準

次の問いに答えると、nullable を使うべきか判断しやすくなります。
- 値が未設定であることを表したいか
- その値は必須か任意か
- null に意味があるか
- 空文字や 0 では代替できないか
- 利用側が null を安全に扱えるか
- null を返す設計が自然か

これらに当てはまるなら nullable は有効です。
逆に、null が曖昧さを生むなら、別の表現方法を検討します。

---

## まとめ

nullable は、値が存在しない状態を型で表現するための仕組みです。
値型には T?、参照型には nullable reference types を使って、null の意味を明確にできます。

正しく使うと、未設定や不明といった状態を安全に扱えます。
一方で、曖昧に使うと null 例外や設計崩れの原因になります。

nullable は、C# の型設計の中でも特に重要な領域です。

