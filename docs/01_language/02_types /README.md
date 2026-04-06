# 00 Overview

この章では、C# における「型」を正しく理解し、
用途に応じて適切な型を選べるようになることを目指します。

型の理解は、単に文法を覚えるだけでは不十分です。
値がどう渡るのか、参照がどう共有されるのか、null をどう扱うのか、
比較が何を意味するのか、変換で何が起きるのかまで含めて理解する必要があります。

---

## この章のゴール

この章を終えた時に、次のことを説明できる状態を目指します。

- 値型と参照型の違い
- `string` と `object` の性質
- `var` と `dynamic` の違い
- `enum` の使いどころ
- nullable の考え方
- 型変換の種類
- `==` と `Equals` の違い
- boxing / unboxing が起きる理由

---

## この章で扱う範囲

### 1. Value Types
値そのものを保持する型です。

例:
- `int`
- `bool`
- `char`
- `decimal`
- `struct`
- `DateTime`
- `TimeSpan`

### 2. Reference Types
実体への参照を保持する型です。

例:
- `class`
- `string`
- 配列
- `object`

### 3. 誤解されやすい型
特に実務で混乱しやすいものです。

- `string`
- `object`
- `var`
- `dynamic`

### 4. Enum
離散的な状態や種別を表す型です。

### 5. Nullable
null をどう扱うかを整理します。

### 6. Conversions
型変換のルールを整理します。

### 7. Equality and Identity
同じ「値」なのか、同じ「実体」なのかを区別します。

### 8. Boxing / Unboxing
値型と参照型の境界で起きる変換を扱います。

---

## 学習の順番

この章は次の順で読むと理解しやすいです。

1. `01_value-types.md`
2. `02_reference-types.md`
3. `05_nullable-types.md`
4. `06_conversions.md`
5. `07_equality-and-identity.md`
6. `08_boxing-unboxing.md`
7. `03_string-object-var-dynamic.md`
8. `04_enum.md`

---

## この章で大事にする視点

### 値を持つのか、参照を持つのか
まずはここを見ます。

### 変更の影響範囲はどこまでか
コピーされるのか、共有されるのかを意識します。

### null を許すのか、許さないのか
設計の段階で決めます。

### 比較は何を比較しているのか
値なのか、参照なのかを区別します。

### 変換は安全か
暗黙変換でよいのか、明示変換が必要かを考えます。

### 余計な boxing を発生させていないか
性能に影響する場面を見分けます。

---

## この章を読み終えたときの理想像

- コードを見たときに「この型を選んだ理由」を説明できる
- null を雑に扱わない
- 比較ミスを避けられる
- 型変換で落ちる理由を追える
- 性能劣化の原因として boxing を疑える

---

## 関連ファイル

- `01_value-types.md`
- `02_reference-types.md`
- `03_string-object-var-dynamic.md`
- `04_enum.md`
- `05_nullable-types.md`
- `06_conversions.md`
- `07_equality-and-identity.md`
- `08_boxing-unboxing.md`

---

## メモ

この章は、単体で覚えるよりも、
後続の `03_generics` や `05_nullable-types`、
`07_equality-and-identity` に接続させて理解すると強くなります。
