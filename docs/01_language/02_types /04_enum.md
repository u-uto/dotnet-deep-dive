# 04 Enum

## 概要

`enum` は、**限定された選択肢（状態や種別）を表現するための型**です。  
数値で状態を管理するのではなく、意味のある名前で扱うことで可読性と安全性を高めます。

---

## 定義

`enum` は、名前付きの定数集合を定義する値型です。  
内部的には整数型（デフォルトは `int`）として扱われます。

```csharp
public enum OrderStatus
{
    Pending,
    Completed,
    Cancelled
}
```

## 特徴

### 1. 数値に名前を付けられる

```csharp
OrderStatus status = OrderStatus.Pending;
```

数値ではなく意味のある名前で扱えるため、コードが読みやすくなります。

---

### 2. 内部的には数値として扱われる

```csharp
int value = (int)OrderStatus.Completed;
```

### 3. デフォルトは 0 から始まる

```csharp
public enum Status
{
    None,       // 0
    Active,     // 1
    Inactive    // 2
}
```

### 4. 明示的に値を指定できる

```csharp
public enum Status
{
    None = 0,
    Active = 1,
    Inactive = 99
}
```

### 5. フラグとして使える

```csharp
[Flags]
public enum Permission
{
    None = 0,
    Read = 1,
    Write = 2,
    Execute = 4
}
```

ビット演算で複数の状態を組み合わせられます。

---

## 代表例

### ステータス管理

```csharp
public enum OrderStatus
{
    Pending,
    Completed,
    Cancelled
}
```

### 種別管理

```csharp
public enum UserType
{
    Admin,
    General,
    Guest
}
```

### フラグ

```csharp
Permission permission = Permission.Read | Permission.Write;

```

## 使いどころ
- ステータス（状態管理）
- 種別（カテゴリ分類）
- 分岐条件（switch 文）
- フラグ管理（ビット演算）

例:
- 注文状態
- ユーザー種別
- 処理結果
- 権限

---

## 注意点

### 1. 数値として扱えてしまう

```csharp
OrderStatus status = (OrderStatus)999;
```

存在しない値を代入できてしまうため注意が必要です。

---

### 2. 0 の扱いに注意

デフォルト値が 0 のため、意図しない状態になることがあります。
None = 0 を定義するのが一般的です。

---

### 3. フラグの設計ミス

```csharp
Read = 1,
Write = 2,
Execute = 3 // NG（ビットが重複）
```

フラグは必ず 2 の累乗で定義する必要があります。

---

### 4. ビジネスロジックを持たせない

enum は状態表現に使うものであり、振る舞いを持たせるべきではありません。

---

### 5. 増減に弱い

値の追加・変更が既存処理に影響するため、変更時は慎重に扱う必要があります。

---

## 実装例

### 基本的な使用

```csharp
OrderStatus status = OrderStatus.Completed;

if (status == OrderStatus.Completed)
{
    Console.WriteLine("完了");
}
```

switch での使用

```csharp
switch (status)
{
    case OrderStatus.Pending:
        Console.WriteLine("処理中");
        break;
    case OrderStatus.Completed:
        Console.WriteLine("完了");
        break;
}
```

フラグの使用

```csharp
Permission permission = Permission.Read | Permission.Write;

bool canRead = (permission & Permission.Read) != 0;
```

設計指針
- 意味のある名前を付ける
- None = 0 を定義する
- フラグは 2 の累乗で定義する
- 数値に依存したロジックを書かない
- 状態管理に限定して使う
- 複雑になったらクラス化を検討する

---

## 判断基準

次の問いに答えると、enum を使うべきか判断できます。
- 選択肢が限定されているか
- 状態や種別を表したいか
- 数値ではなく意味で扱いたいか
- 将来的な拡張がシンプルか
- 振る舞いは不要か

これらに当てはまる場合、enum は有力な選択肢です。

---

## まとめ

enum は、限定された状態や種別を表現するための型です。
数値に意味を持たせることで、可読性と安全性を向上させます。

適切に使うことで、分岐処理や状態管理が分かりやすくなりますが、
設計を誤ると拡張性や安全性に問題が出るため注意が必要です。