4ステップで作成する、DB論理設計のチェックポイントとアンチパターンまとめ

# はじめに
SQLアンチパターン、達人に学ぶDB設計徹底指南書を読んだので、DB設計をする流れとその過程でのチェックポイントをまとめてみました。
本に載っているものの中でも特に重要そうな部分に絞ってみました。
さらに詳しいを知りたい方は本を購入してみてください。個人的には達人に学ぶDB設計徹底指南書のほうがおすすめです。こちらだけあれば十分だと思います。
DB設計には大きく分けて論理設計と物理設計の二つがありますが、今回はアプリケーション開発でメインとなる論理設計の部分に焦点をあてて説明をします。
一番最後にチェックポイントだけをまとめた章を用意したので、ポイントだけ知りたい方は最後だけ見ていただければと思います。

# DB論理設計の流れ
DB論理設計は以下のようなステップで進めていきます。
1. エンティティの抽出
1. エンティティの定義
1. 正規化
1. ER図の作成

以下では各ステップごとに章を分けて説明をします。

# ステップ1: エンティティの抽出
エンティティとは日本語では実体と訳します。エンティティの定義は、ある共通項を持ったデータの集合体のことをいいます。
エンティティは物理的実体を含んでいなくてもいいです。
このステップでは、まずはシステムにどのようなエンティティ(=データ)が必要なのかを洗い出します。いわゆる「要件定義」です。

## 例
- (物理的なもの) 顧客、社員、店舗、車、など
- (概念的なもの) 税、売り上げ、注文履歴、など

## チェックポイント
- 洗い出したエンティティは開発するシステムの要件を満たせるか

# ステップ2: エンティティの定義
このステップでは各エンティティがどのようなデータを保持するかを定義します。
DBにおいてエンティティはテーブルという単位で表現されます。
つまり、エンティティの定義とはテーブルの定義を行うことと同意です。
特に大事なのは以下の二点です。
- キー(特定のデータを取り出すための列の組み合わせのこと)をなににするか
- どのような属性(列)を持たせるか

## 例
テーブルは以下のような形で表現されます。
p76
- 下線は主キー(またはプライマリーキー)を表す
  - 主キーとはキーの種類の一つで、テーブルにおいて必ず一つ存在しなければいけないもの
- 二つのテーブルを紐づけている列を外部キーと呼ぶ
  - 外部キーとはキーの種類の一つで、テーブルの間の列同士で設定するもの
  - 例でいうと「社員」テーブルの「部署」列が外部キーにあたる
  - 外部キーは人間の親子関係のようなもの。親が存在しなければ子は存在できない。親と子の関係は1:多の関係。ここでは、部署が親で社員が子の関係になっている。(部署：社員は1:多の関係、部署テーブルに登録されていない部署名は社員テーブルでは存在できない。)

## 悪い例
以下のような例はテーブルとして適していない、いわゆるアンチパターンなものです。

### 単一参照テーブル
p199

### ダブルミーニング
p196

## チェックポイント
各項目ごとにチェックポイントを整理します。

### テーブル
- テーブル名は英語ならば複数形/複数名詞で書けるか
  - そうでなければそのテーブルのどこかに間違いがあるので再検討する必要がある
- テーブル名は同じドメイン(スキーマ)の範囲で重複していないか
- 日本語を使っていないか
- 最初はアルファベットになっているか
  - 使うのであれば2009_uriageではなく、uriage_2009のようにする
- 複数のテーブルをまとめていないか

### 列
- 列名はハイフンは使っていないか
  - ハイフンは標準SQLで定められていないので、使うのであればアンダーバーを利用するほうがよい
- 日本語を使っていないか
- 最初はアルファベットになっているか
- 全てのテーブルにidという名前を安易につかっていないか
- NOT NULL 制約はなるべくつけているか
- 列の意味は統一されているか

### キー全般について
- 値の変わる列を主キーや外部キーに利用していないか
  - 例えば「山田」という苗字の場合、結婚などの理由で苗字が変わることがある。このような属性をキーにするのは適していない
- キーとなる列にはコードやIDなど表記体系の定まった固定長文字列を利用しているか
  - 可変長文字列(VARCHAR)の場合、例えば「山田太郎」という社員名をキーにしたとすると、「山田 太郎」のようにスペースが入ったデータも存在する可能性があるので、両者は不一致となって正しくデータを取得できなくなる

#### 主キーについて
- 主キーは定義したか
  - もし主キーとして適切な属性が存在しないようであれば代理キー(1,2,3,4など、それ自体に意味はないが一意性を保証するためのキー)を追加する
- 主キーはNULLを含んではいないか
- 主キーにidという名前を利用していないか
  - 代理キーなどを主キーにする場合、idという名前をつけがちだが、複数のテーブルでidという名前があると混乱を招く原因になるので避けた方がよい。例えば、order_id のようにするほうがよい
- 主キーは重複してしまう可能性はないか
  - 例えば「苗字」「名前」という属性を主キーにした場合、主キーは一意でなければいけないため、同姓同名の人はテーブルに入れることができなくなってしまう

#### 外部キーについて
- 子に対して複数の親が存在していないか
  - 存在している場合は設計が間違っている可能性があるので、親子関係などを再考してみる。(ポリモーフィックというアンチパターン)
  - 中間テーブル(後述)を作成するなどして解決する
- 参照整合性制約をつけているか
  - 制約をつけることで、親に存在しない値は子のテーブルで作成できなくなるので、間違ったデータを入れることを防ぐことができる
  - 別にレスポンス遅くならない(TODO: なんで参照整合性制約嫌うんだっけ?たしか柔軟に削除できない（絶対子から削除する）とか、アップデートが基本できないからだった気がする。)
  - もし外部キー制約をつけないと列を削除するときに子テーブルも正しく更新できるかなど考える必要がある p44

# ステップ3: 正規化
正規化とはざっくりいうとテーブルを分解し、データの冗長性をなくすことです。分解の粒度によって呼び方が変わり第1正規形から第5正規形まで存在します。
このステップではテーブルを分解する作業を行います。
一般的には第３正規形まで達成できれば十分なので、第３正規形まで説明をします。
正規化の中で重要なキーワードとして従属というものがあります。XによってYが一意に定まることを「YはXに従属する」と表現し、{X} -> {Y} と書きます。
以下では従属という言葉を用いて説明をします。

## 第1正規形
ひとつのセルに1つの値が存在しているテーブルのことをいいます。

### 正規化前
p８７
- カラムに複数データが入ると主キーによって一意にデータを識別できない(=列が主キーに従属でない)

### 正規化後
p８６、p８７
- 第1正規形であるが実はこのテーブルは列持ち(マルチカラムアトリビュート)と呼ばれるあまり好ましくない設計の例である
  - 列持ちの場合、要素をさらに追加したいときにカラムを増やさないといけないためよくない設計とされている

### チェックポイント
- ひとつのセルに1つだけか
  - ただし、基本的には列持ちのときのように第1正規化だけでは不十分なことが多いので、さらなる分解を検討する
- カンマ区切りのフォーマットで格納していないか（一つのカラムに要素を詰め込んでしまうケース）
  - 第1正規形ではあるが、カンマ区切りなどで複数のデータを一つのセルにいれることは好ましくない設計なので分解することを検討する

## 第2正規形
主キーに対して列が従属しているテーブルのことをいいます。

### 正規化前(第1正規形)
p93
- 会社名は社員ID(主キー)がどのような値であっても会社コード(主キー)によってその値が一意に定まってしまう。これを解消するのが第2正規化
  - 社員がいない(社員情報がNULL)会社を登録したい場合、このテーブルに会社を登録できない
  - {C0001, A商事}というレコードの他に{C0001, A商社}というレコードを間違えて入れてしまっても防ぐ手段がない

### 正規化後
- 主キーの一部と従属だった列を新たなテーブルに切り離すことで第2正規形が成立する

### チェックポイント
- 主キーの一部だけしか利用していないのに一意に定まる列が存在していないか
  - 存在している場合、それは第1正規形になる。主キーの一部と従属している列がある場合、例で説明したデメリットが存在しているため、その列を新たなテーブルとして分解する必要性がある

## 第3正規形
主キーに対して全ての属性が従属であり、かつ推移的関数従属(後述)も存在していないテーブルのことです。

### 正規化前(再掲: 第2正規形)
p99
- 主キーに対して全ての属性が従属である
- しかし、{社員ID} -> {部署コード}は従属であり、{部署コード} -> {部署名}は従属である。つまり、{社員ID} -> {部署コード} -> {部署名}という関係が成立している。テーブル内部に存在する段階的な従属関係を推移的関数従属と呼び、これを解消するのが第３正規化である

### 正規化後
p100
### チェックポイント
- 主キー以外の列同士で従属する列は存在していないか
  - 例でいうと「部署コード」と「部署名」は従属関係にある。このような列の組み合わせが主キー以外で見つかった場合、推移的関数従属が存在する可能性が高いので、テーブルを分解する必要がある

# ステップ4: ER図の作成
このステップでは上記のステップで作成されたテーブル同士の関係を見抜き紐づける作業を行います。
ER図の作成方法としては、主キーに着目し、他のテーブルの列にそのキーが含まれているかをチェックします。存在している場合、テーブル間に関係があるので線で結びます。(ER図自体の説明は今回省略します。)

## 問題のあるテーブル関係の例
作成したテーブル同士の関係を結んだ時に以下のような関係性が見つかった場合は少しER図を疑ったほうがいいです。

### 1:1
1:1とはつまり主キーが一致しているということです。普通に正規化していればこのような関係は存在しません。1:1の場合はテーブルを結合するのが普通です。

### 多:多
p135
システムの要件に従ってテーブルを作成すると多:多の関係のテーブルが作成される時があります。しかし、細かい説明は省きますが、両者を結合した情報を得ることができないので好ましくない設計です。


### 子に対して親が複数
親が二つあって、子のテーブルにある外部キーがどちらかの親に属しているというようなパターンがこれにあたります。ポリモーフィック関連などと呼ばれます。子は複数の親を持ちません。複数の親を持つことのデメリットは以下です
  - 参照制約つかえなくなる
  - 結合すると片方の親はNULLになる

## 改善例
多:多や子に対して親が複数存在する場合は中間テーブルというテーブルを作成することが問題解決に有効な手段となります。
ここでは中間テーブルについて説明をします。

### 中間テーブルの作成
多:多が存在する場合は、関連実体(中間テーブルなどともいう)を作成することでこの問題を解決します。なお、関連実体は多:多の問題を解決するために作られたテーブルであるため、要件定義で出てくるようなエンティティとは違い、あくまで人工的に作られたエンティティとなります。

## チェックポイント
- あるテーブルの主キーが他のテーブルに列として含まれるか
  - 存在していればそれらのテーブルを線で結ぶ
- 1:1という関係のテーブルが存在しないか
  - 存在していたら結合する
- 多：多という関係のテーブルが存在しないか
  - なっているなら中間テーブルを作成し、多:多にならないようにする
- 子に対して親が複数存在していないか
  - 中間テーブルを作成することで解決できるか検討する
  - 本来の関連が逆になっている可能性があるので参照を逆にしてみる

# チェックリスト
今回の各ステップにおけるチェックポイントを以下にまとめました。
DB論理設計をするときはこのリストを参考にして設計に問題がないか確認をしてみるといいかもしれません。

## ステップ1: エンティティの抽出
- 洗い出したエンティティは開発するシステムの要件を満たせるか

## ステップ2: エンティティの定義
- テーブル名は英語ならば複数形/複数名詞で書けるか
- テーブル名は同じドメイン(スキーマ)の範囲で重複していないか
- 日本語を使っていないか
- 最初はアルファベットになっているか
- 複数のテーブルをまとめていないか
- 列名はハイフンは使っていないか
- 日本語を使っていないか
- 最初はアルファベットになっているか
- 全てのテーブルにidという名前を安易につかっていないか
- NOT NULL 制約はなるべくつけているか
- 列の意味は統一されているか
- 値の変わる列を主キーや外部キーに利用していないか
- キーとなる列にはコードやIDなど表記体系の定まった固定長文字列を利用しているか
- 主キーは定義したか
- 主キーはNULLを含んではいないか
- 主キーにidという名前を利用していないか
- 主キーは重複してしまう可能性はないか
- 子に対して複数の親が存在していないか
- 参照整合性制約をつけているか

## ステップ3: 正規化
- ひとつのセルに1つだけか
- カンマ区切りのフォーマットで格納していないか（一つのカラムに要素を詰め込んでしまうケース）
- 主キーの一部だけしか利用していないのに一意に定まる列が存在していないか
- 主キー以外の列同士で従属する列は存在していないか

## ステップ4: ER図の作成
- あるテーブルの主キーが他のテーブルに列として含まれるか p126
- 1:1という関係のテーブルが存在しないか
- 多：多という関係のテーブルが存在しないか
- 子に対して親が複数存在していないか
