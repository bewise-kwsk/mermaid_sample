# mermaidのサンプル
markdownで図面を作成する[mermaid](https://www.mermaidchart.com/)を使ってみました。

mermaidはmarkdownのソースコードブロックとしてフローチャートやクラス図などを記述する文書形式です。
mermaidはGitHubで使用できるだけでなく、VisualStudio Codeなどのアドインとして提供されているので、設計図面を簡単に文書化してソースコードとともに管理することができます。

mermaidで記述した図面のサンプルをいくつか示します。

## フローチャート
フローチャートを書いてみます。

```mermaid
---
title: バブルソート
---
flowchart TD

  id1([はじめ])
  id2["1 → i"]
  id3@{shape: f-circ}
  id4{"i < size of a"}
  id5["i → j"]
  id6@{shape: f-circ}
  id7{"j < size of a"}
  id8{"a(j) > a(j + 1)"}
  id9["a(j) → t
       a(j + 1) → a(j)
       t → a(j + 1)"]
  id10@{shape: f-circ}
  id11["j + 1 → j"]
  id12["i + 1 → i"]
  id13([おわり])
  id1 --> id2
  id2 --- id3
  id3 --> id4
  id4 --Y--> id5
  id4 --N----> id13
  id5 --- id6
  id6 --> id7
  id7 --Y--> id8
  id7 --N--> id12
  id8 --Y-->id9
  id8 --N-->id10
  id9 --- id10
  id10 --> id11
  id11 --> id6
  id12 --> id3
```
判断記号のひし形の縦横比が微妙とか、判断の矢印が出る位置が気持ち悪いとか、ループの開始端はあるのに終了端がないとか、流れが両側に振り分けられるレイアウトになるので、主となる流れが分かりにくいとか微妙な点がいろいろとありますが、とりあえずフローチャートを描くことはできます。

## クラス図
クラス図を描いてみます。
```mermaid
---
title: Front Controllerパターンの事例
---
classDiagram
namespace Controller {
class FrontController
class Action
class TransactionListAction
class SummaryAction
class NewTransactionAction
class EditTransactionAction
}
FrontController ..> TransactionListAction
FrontController ..> SummaryAction
FrontController ..> NewTransactionAction
FrontController ..> EditTransactionAction
<<interface>> Action
Action: +execute() String
Action <|-- TransactionListAction
Action <|-- SummaryAction
Action <|-- NewTransactionAction
Action <|-- EditTransactionAction
TransactionListAction ..> TransactionListLogic
TransactionListAction ..> TransactionListView
SummaryAction ..> SummaryLogic
SummaryAction ..> SummaryView
NewTransactionAction ..> NewTransactionLogic
NewTransactionAction ..> NewTransactionView
EditTransactionAction ..> EditTransactionLogic
EditTransactionAction ..> EditTransactionView
namespace BusinessLogic {
class TransactionListLogic
class SummaryLogic
class NewTransactionLogic
class EditTransactionLogic
}
TransactionListLogic ..> TransactionDAO
TransactionListLogic: +findTransactions() List<Transaction>
SummaryLogic ..> TransactionDAO
SummaryLogic: +summaryTransaction(int transactionId) Transaction
NewTransactionLogic ..> TransactionDAO
NewTransactionLogic: +addTransaction(Transaction transaction) void
EditTransactionLogic ..> TransactionDAO
EditTransactionLogic: +editTransaction(Transaction transaction) boolean
namespace DAO {
class TransactionDAO
}
TransactionDAO: +findAll() List<Transaction>
TransactionDAO: +insert(Transaction transaction) void
TransactionDAO: +update(Transaction transaction) Transaction
namespace View {
class TransactionListView
class SummaryView
class NewTransactionView
class EditTransactionView
}
```
関連があまり見やすくないけれど、それなりに使えそうなクラス図ができました。
もちろんここからコード化できるわけではない、単なるお絵描きツールですがソースコードとともに管理できるのできちんと規律が守られていればそれなりに使えると思います。
コード生成やコードからの自動生成もできるといいかもしれないけど。
## シーケンス図
シーケンス図を描いてみます。
```mermaid
---
title: シーケンス図
---
sequenceDiagram
actor ユーザ
participant 画面
participant FrontController
participant TLA as TransactionListAction
participant NTA as NewTransactionAction
participant TLL as TransactionListLogic
participant NTL as NewTransactionLogic
participant TLV as TransactionListView
participant NTV as NewTransactionView
participant TD as TransactionDAO
ユーザ ->> 画面: アクセス指示
画面 ->> FrontController: doGet()
FrontController ->> +TLA: execute()
TLA ->> +TLL: findAll()
TLL ->> +TD: findAll()
TD -->> -TLL: 取引データ
TLL -->> -TLA: 初期表示データ
TLA -->> -FrontController: TransactionListView
FrontController ->> +TLV: redirect
TLV ->> -画面: 初期表示画面
ユーザ ->> 画面: 新規取引ボタン押下
画面 ->> FrontController: doPost()
FrontController ->> +NTA: execute()
NTA -->> -FrontController: NewTransactionView
FrontController ->> +NTV:
NTV ->> -画面: 新規取引画面表示
ユーザ ->> 画面: 取引情報入力、保存ボタン押下
画面 ->> FrontController: doPost()
FrontController ->> +NTA: execute()
NTA ->> +NTL: store(transaction)
NTL ->> +TD: insert(transaction)
TD -->> -NTL:
NTL ->> +TD: findAll()
TD -->> -NTL: 取引データ
NTL -->> -NTA: 取引データ
NTA ->> -FrontController: TransactionListView
FrontController ->> +TLV:
TLV ->> -画面: 取引一覧
```
シーケンス図は記述が少し面倒だけどそれなりの図が描ける。
