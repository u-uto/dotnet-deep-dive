# 06 Conversions

## 概要

型変換は、ある型の値を別の型として扱うための仕組みです。  
C# では、暗黙変換・明示変換・パース・キャスト失敗など、複数の変換パターンがあります。

型変換を正しく理解していないと、例外、データ欠損、意図しない丸め、読みづらいコードにつながります。

---

## 定義

型変換とは、ある型の値を別の型へ移すことです。  
変換には次のような種類があります。

- 暗黙変換
- 明示変換
- 文字列変換
- パース
- キャスト
- ユーザー定義変換

---

## 特徴

### 1. 暗黙変換と明示変換がある

暗黙変換は、コンパイラが安全だと判断して自動で行います。  
明示変換は、開発者がキャストを指定します。

```csharp
int a = 10;
double b = a;     // 暗黙変換
int c = (int)b;   // 明示変換
```

### 2. 安全な変換と危険な変換がある

変換先に情報を失う可能性がある場合は、明示変換が必要になります。
たとえば、double から int への変換では小数部分が失われます。

### 3. パースは文字列からの変換に使う

文字列を数値や日時に変換する場合は、Parse や TryParse を使います。

```csharp
int number = int.Parse("123");
```

### 4. TryParse は失敗を安全に扱える

```csharp
if (int.TryParse("123", out int number))
{
    Console.WriteLine(number);
}
```

### 5. キャスト失敗は例外になり得る

```csharp
object value = "text";
int number = (int)value; // 実行時例外
```

---

## 代表例

### 数値同士の変換
```csharp
int a = 10;
double b = a;
```

### 明示変換
```csharp
double x = 12.5;
int y = (int)x;
```

### 文字列から数値へ
```csharp
string text = "100";
int value = int.Parse(text);
```

### 安全な文字列変換
```csharp
string text = "100";
if (int.TryParse(text, out int value))
{
    Console.WriteLine(value);
}
```

### 型の変換確認
```csharp
object obj = 123;
if (obj is int number)
{
    Console.WriteLine(number);
}
```

---

## 使いどころ

型変換は次のような場面で使います。
-入力値を扱うとき
- API のレスポンスを変換するとき
- 文字列を数値や日時に変えるとき
- 型の境界を越えるとき
- 外部システムとのやり取りをするとき

たとえば、画面入力、CSV、JSON、DB、HTTP リクエストなどは変換が頻出します。

---

## 注意点

### 1. 情報が失われる変換がある
```csharp
double value = 12.9;
int number = (int)value;
```

小数点以下が切り捨てられます。
意図しない丸めや切り捨てに注意が必要です。

### 2. Parse は失敗すると例外になる
```csharp
int number = int.Parse("abc");
```

不正な入力があり得る場合は、TryParse を使うほうが安全です。

### 3. キャストは型安全ではない場合がある
```csharp
object value = "text";
int number = (int)value;
```

実行時に失敗する可能性があります。

### 4. 変換ロジックを散らしすぎない

各所でバラバラに変換を書くと、ルールが崩れやすくなります。
境界でまとめて変換するほうが扱いやすいです。

### 5. ユーザー入力は必ず失敗を想定する

入力値は信頼できない前提で扱う必要があります。
Parse より TryParse のほうが自然な場面が多いです。

---

## 実装例

### 暗黙変換
```csharp
int a = 10;
double b = a;
```

### 明示変換
```csharp
double x = 15.7;
int y = (int)x;

Console.WriteLine(y);
```

### TryParse
```csharp
string input = "42";

if (int.TryParse(input, out int value))
{
    Console.WriteLine(value);
}
else
{
    Console.WriteLine("invalid input");
}
```

### 型チェックして変換
```csharp
object obj = 123;

if (obj is int number)
{
    Console.WriteLine(number);
}
```

### null を含む変換
```csharp
string? text = null;
int number = int.TryParse(text, out var value) ? value : 0;
```

---

## 設計指針
-変換は境界でまとめる
- 失敗する可能性があるなら TryParse を優先する
- 情報損失がある変換は明示する
- キャストで済ませず、意味を確認する
- 入力を信頼しない
- 変換ルールを共通化する

特に、画面入力や外部データの変換は、各所で個別実装しないほうが安全です。
変換の責務を1か所に寄せると、保守しやすくなります。

---

## 判断基準

次の問いに答えると、どの変換を使うべきか判断しやすくなります。
- 安全に自動変換できるか
- 情報が失われないか
- 失敗する可能性があるか
- 入力は信頼できるか
- 例外を許容できるか
- 型の意味を保てるか

これらを考えると、暗黙変換・明示変換・Parse・TryParse の使い分けがしやすくなります。

---

## まとめ

型変換は、型と型の境界を越えるための仕組みです。
暗黙変換、明示変換、Parse、TryParse を適切に使い分けることで、安全で読みやすいコードになります。

変換は便利ですが、失敗や情報損失が起こりやすい領域でもあります。
だからこそ、変換の場所と方法を意識して設計することが重要です。

```csharp

```