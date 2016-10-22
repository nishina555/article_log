# はじめに
自分はMongoDBを利用したアプリケーションの開発・運用をしばらく業務で担当していました。
業務を通じて開発・運用でよく使うであろうクエリのノウハウがある程度たまってきたので今回記事にまとめました。
「こうゆうレコード取得したいなー」「こうゆう操作ってどうやってできるんだ？」というような疑問に少しでも答えられればと思います。
特に、実際の運用でも利用する機会が多いであろう**検索・更新・削除**に焦点を絞って話をしたいと思います。
ですので、今回は既にMongoDBの中にレコードが存在しているという想定で話をします。
もしDockerを使える環境がある場合は[MongoDBで学ぶ、Docker Machineを利用した練習用開発環境の構築手順](http://qiita.com/nishina555/items/b3b8cd3d27a51fef3a3b)にDockerを利用したMongoDBのインストールと今回の記事で使用しているレコード挿入の方法をまとめておきましたので併せて読んでいただければ実際にクエリを試すことができます。

# 使用するレコード
今回、以下のようなレコードを用意し、実際にこれらのレコードを操作することで具体例を示していきたいと思います。
なお、データベース名は `test`、コレクション名は`students` とします。(コレクションとはRDBでいうテーブルに該当します。)

```json
{
  "no": 1,
  "firstName": "Taro",
  "lastName": "Tanaka",
  "age": 15,
  "body": { "height": 165, "weight": 60 },
  "grades": [
    { "semester": "first", "grade": 90 },
    { "semester": "second", "grade": 80 }
  ],
  "insertDate": ISODate("2016-10-20T15:00:00Z")
}

{
  "no": 2,
  "firstName": "Satoshi",
  "lastName": "Takeda",
  "age": 16,
  "body": { "height": 150, "weight": 55 },
  "grades": [
    { "semester": "first", "grade": 90 },
    { "semester": "second", "grade": 70 }
  ],
  "insertDate": ISODate("2016-10-21T15:00:00Z")
}

{
  "no": 3,
  "firstName": "Jiro",
  "lastName": "Suzuki",
  "age": 15,
  "body": { "height": 170,"weight": 65 },
  "grades": [
    { "semester": "first", "grade": 60 },
    { "semester": "second", "grade": 80 }
  ],
  "insertDate": ISODate("2016-10-22T15:00:00Z")
}
```
# 基本的なクエリ
## 検索
### 基本構文
検索には`find`を利用します。条件をなにも記入しないと全件抽出されます。

```
db.コレクション名.find( { 条件 } )
```
### 具体例
`lastName`が`Tanaka`のレコードを表示する。

```
> db.students.find( { "lastName": "Suzuki" } )
{ "_id" : ObjectId("580b29bc24c83b73539eab47"), "no" : 3, "firstName" : "Jiro", "lastName" : "Suzuki", "age" : 15, "body" : { "height" : 170, "weight" : 65 }, "grades" : [ { "semester" : "first", "grade" : 60 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-22T15:00:00Z") }
```
## 更新
### 基本構文
MongoDBの場合、**デフォルトでは条件に引っかかった一番初めのレコードしか更新されません。**
ですので、条件にあてはまる**全てのレコードに対して更新を行いたい場合**は`multi:true`を指定する必要があります。

```
db.コレクション名.update( { 条件 }, { 演算子: { 更新内容 } }, { multi: true } )
```
演算子はいくつかあるのですが、よく使用するものとしてはフィールドを更新する`$set`と、フィールドを削除する`$unset`です。

補足ですが、以下のクエリも上と同意です。
第3引数の`false`は`upsert`(レコードが存在していなければ`INSERT`, 存在すれば`UPDATE`)に対するオプション、第4引数の`true`は`multi`に関するオプションです。
upsertはデフォルトで`false`なので、上記のように`multi`に関するオプションだけでよかったということです。
他のページなどでは以下のクエリを使用している場合があるので、混乱をしないように補足しておきました。

```
db.コレクション名.update( { 条件 }, { 演算子 : { 更新内容 } }, false, true)
```
### 具体例
`lastName`が`Tanaka`のレコードの`age`を15から17に変更する。

```
> db.students.update( { "lastName": "Tanaka" }, { $set: { "age": 17 } } )
WriteResult( { "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 } )
```
結果の見方ですが、`nMatched`は条件に該当した件数、`nModified`は更新した件数を示しています。
1件該当し、1件更新をしているので、更新は正しく完了したことを表しています。

## 削除
### 基本構文
削除には`remove`を利用します。条件をないにも記入しないと全件削除されます。

```
db.コレクション名.remove( { 条件 } )
```
### 具体例
`lastName`が`Tanaka`のレコードを削除する。

```
> db.students.remove( { "lastName": "Tanaka" } )
WriteResult( { "nRemoved" : 1 } )
```
`nRemoved`は削除した件数を表します。

全件削除

```
> db.students.remove( { } )
WriteResult( { "nRemoved" : 3 } )
```
## 件数取得
### 基本構文
件数取得には`count`を利用します。条件をないにも記入しないと全件数を取得できます。

```
db.コレクション名.count( { 条件 } )
```
もしくは`find`と併せることもできます。

```
db.コレクション名.find( { 条件 } ).count()
```
### 具体例
`students`コレクションに存在するレコード数を取得する。

```
> db.students.count()
3
```
`students`コレクションに存在するレコードのうち、`age`が15の件数を取得する。

```
> db.students.find( { "age":15 } ).count()
2
```
# 覚えておくと役に立つクエリ
## 必要なところだけ表示
### 基本構文
MongoDBのレコードを検索するとデフォルトでは全てのフィールドを出力することになります。MongoDBに挿入される時に自動で作成される`_id`については知りたいと思うことも少ないでしょうし、フィールド数が多くなると検索結果が分かりにくくなります。
表示するフィールドの変更は`find`の第2引数で`1`と明記することで実現できます。
逆に`0`と明記するとそのフィールドは表示されません。

```
db.コレクション名.find( { 条件 },{ 表示したいフィールド: 1, ...} )
```
### 具体例
`lastName`のみを出力させる。さらに`_id`は出力しないようにする。

```
> db.students.find( { }, { "_id": 0, "lastName": 1 } )
{ "lastName" : "Tanaka" }
{ "lastName" : "Takeda" }
{ "lastName" : "Suzuki" }
```
## ネストされたオブジェクトに対する処理
### 基本構文
`.`(ドット)を区切り記号として使用することでネストされたオブジェクトに対する操作をすることができます。

```
db.コレクション名.find(オブジェクト.ネストされたオブジェクト)
```
### 具体例
`body`の`height`が150のレコードを取得する。

```
> db.students.find( { "body.height": 150 } )
{ "_id" : ObjectId("580b29bc24c83b73539eab46"), "no" : 2, "firstName" : "Satoshi", "lastName" : "Takeda", "age" : 16, "body" : { "height" : 150, "weight" : 55 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 70 } ], "insertDate" : ISODate("2016-10-21T15:00:00Z") }
```
## 配列に対する更新
### 基本構文
オブジェクトが配列に存在する場合、値を更新するには`.$.`のようにドットとドル記号を利用して配列とフィールドを結合する必要があります。
先ほど説明したように、条件にあてはまる**全てのレコードに対して更新を行いたい場合**は`multi:true`を指定する必要があります。

```
db.コレクション名.update( { 条件 }, { 演算子: { 配列名.$.フィールド } }, { multi: true } )
```
### 具体例
`lastName`が`Tanaka`のレコードに存在する、`grades`の`semester`が`first`のものに対して、`grade`を`95`に変更する。

```
> db.students.update( { "lastName": "Tanaka", "grades.semester": "first" }, { $set: { "grades.$.grade": 95 } }, { multi: true } )
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```
なお、`lastName`が`Tanaka`のレコードに対して操作をする場合、直感的には以下のようなクエリを書くかもしれませんが**配列の条件がない場合エラーになる**ので気をつけてください。

```
> db.students.update( { "lastName": "Tanaka" }, { $set: { "grades.$.grade": 95 } }, { multi: true } )
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 0,
	"nModified" : 0,
	"writeError" : {
		"code" : 16837,
		"errmsg" : "The positional operator did not find the match needed from the query. Unexpanded update: grades.$.grade"
	}
})
```
## AND検索
### 基本構文
AND検索は条件を`,`(カンマ)でつなげることで実現できます。

```
db.students.find( { 条件1, 条件2, ... } )
```
### 具体例
`firstName`が`Jiro`, `lastName`が`Suzuki`のレコードを取得する。

```
> db.students.find( { "firstName": "Jiro", "lastName": "Suzuki" } )
{ "_id" : ObjectId("580b29bc24c83b73539eab47"), "no" : 3, "firstName" : "Jiro", "lastName" : "Suzuki", "age" : 15, "body" : { "height" : 170, "weight" : 65 }, "grades" : [ { "semester" : "first", "grade" : 60 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-22T15:00:00Z") }
```
## OR検索
### 基本構文
OR検索は`$or`演算子を利用することで実現できます。

```
db.コレクション名.find( { $or [ {条件1}, {条件2}, .... ] } )
```
もしくは`$in`演算子を利用することで実現できます。

```
db.コレクション名.find( { フィールド名: { $in: [ 値1, 値2, ...] } } )
```
### 具体例
`lastName`が`Tanaka`もしくは`Suzuki`のレコードを取得する。

```
> db.students.find( { $or: [ {"lastName": "Tanaka"}, {"lastName": "Suzuki"} ] } )
{ "_id" : ObjectId("580b29bc24c83b73539eab45"), "no" : 1, "firstName" : "Taro", "lastName" : "Tanaka", "age" : 15, "body" : { "height" : 165, "weight" : 60 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-20T15:00:00Z") }
{ "_id" : ObjectId("580b29bc24c83b73539eab47"), "no" : 3, "firstName" : "Jiro", "lastName" : "Suzuki", "age" : 15, "body" : { "height" : 170, "weight" : 65 }, "grades" : [ { "semester" : "first", "grade" : 60 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-22T15:00:00Z") }
```
`$in`演算子を利用すると以下のようになります。

```
db.students.find( {  "lastName": { $in: [ "Tanaka", "Suzuki" ] } } )
```
## 部分一致検索(正規表現)
### 基本構文
部分一致検索は、条件を正規表現`//`で囲むことで実現できます。

```
db.students.find( { フィールド: /文字列/ } )
```
### 具体例
`firstName`に`ro`を含むレコードを取得する。

```
> db.students.find( { "firstName": /ro/ } )
{ "_id" : ObjectId("580b29bc24c83b73539eab45"), "no" : 1, "firstName" : "Taro", "lastName" : "Tanaka", "age" : 15, "body" : { "height" : 165, "weight" : 60 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-20T15:00:00Z") }
{ "_id" : ObjectId("580b29bc24c83b73539eab47"), "no" : 3, "firstName" : "Jiro", "lastName" : "Suzuki", "age" : 15, "body" : { "height" : 170, "weight" : 65 }, "grades" : [ { "semester" : "first", "grade" : 60 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-22T15:00:00Z") }
```
補足ですが、先頭文字の一致なども正規表現を利用することで実現できます。
以下の例では`lastName`が`Ta`から始まるレコードを取得しています。

```
> db.students.find( { "lastName": /^Ta/ } )
{ "_id" : ObjectId("580b29bc24c83b73539eab45"), "no" : 1, "firstName" : "Taro", "lastName" : "Tanaka", "age" : 15, "body" : { "height" : 165, "weight" : 60 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-20T15:00:00Z") }
{ "_id" : ObjectId("580b29bc24c83b73539eab46"), "no" : 2, "firstName" : "Satoshi", "lastName" : "Takeda", "age" : 16, "body" : { "height" : 150, "weight" : 55 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 70 } ], "insertDate" : ISODate("2016-10-21T15:00:00Z") }
```
## 「以上」「以下」検索
### 基本構文
「以上」「以下」といった検索条件は比較演算子を利用することで実現できます。
`$gte`は「以上」、`$lte`は「以下」の検索ができます。(eを外すと「より大きい」「未満」になります。)

```
db.コレクション名.find( { フィールド: { 比較演算子: 値 } } )
```
### 具体例
`height`が160のレコードを取り出す。

```
> db.students.find( { "body.height": { $gte: 160 } } )
{ "_id" : ObjectId("580b29bc24c83b73539eab45"), "no" : 1, "firstName" : "Taro", "lastName" : "Tanaka", "age" : 15, "body" : { "height" : 165, "weight" : 60 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-20T15:00:00Z") }
{ "_id" : ObjectId("580b29bc24c83b73539eab47"), "no" : 3, "firstName" : "Jiro", "lastName" : "Suzuki", "age" : 15, "body" : { "height" : 170, "weight" : 65 }, "grades" : [ { "semester" : "first", "grade" : 60 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-22T15:00:00Z") }
```
### 比較演算子一覧
比較演算子には具体例にあげたもの以外に`$ne`や`$exists`などもあり、まとめると以下のようになります。

|演算子|意味|クエリ例|
|:--|:--|:--|
|$lt|より小さい|{"age": {$lt: 15}}|
|$gt|より大きい|{"age": {$gt: 15}}|
|$lte|以上|{"age": {$lte: 15}}|
|$gte|以下|{"age": {$gte: 15}}|
|$ne|等しくない|{"age": {$ne: 15}}|
|$exists|フィールドの存在チェック|{"age": {$exists: true}}|
|$or|OR検索|{ $or: [ { "age": 15 }, { "age": 16} ] }|
## 時間の検索
### MongoDBでの時間の扱いについて
MongoDBにおいて、時間に関するクエリは少し考慮することがあります。
それは、**MongoDBでは協定世界時(UTC)で時間が表現されており、JST(日本時間)とUTCには時差があるということです。**
MongoDBには`ISODate("2016-10-21T15:00:00Z")`のような形で時間が格納されています。`ISODate`というのはMongoDBでデフォルトで利用される日時オブジェクトです。また、`Z`はその時間がUTCであることを示すものであり、`T`は日付と時刻を分ける記号です。`T`の代わりにスペースが使われることもあります。
**JSTはUTCよりも9時間進んでいます。ですので、`ISODate("2016-10-21T15:00:00Z")`はJSTでいうところの2016年10月22日00時00分**にあたります。
また、**JSTの2016年10月22日00時00分は`ISODate`を利用すると`ISODate('2016-10-22T00:00:00+09:00')`のように表現できます。**
つまり、**`ISODate("2016-10-21T15:00:00Z")`と`ISODate('2016-10-22T00:00:00+09:00')`は同じ時間を指していることになります。**
### 基本構文
時間を条件とした検索は以下のように表現できます。

```
db.コレクション名.find( { フィールド: ISODate("時間") } )
```
### 具体例
JSTでの2016年10月22日の間(＝2016年10月22日00時00分から2016年10月23日00時00分よりも前)に挿入されたレコードを取り出す。

```
> db.students.find( { "insertDate": { $gte: ISODate("2016-10-22T00:00:00+09:00") , $lt: ISODate("2016-10-23T00:00:00+09:00") } } )
{ "_id" : ObjectId("580b29bc24c83b73539eab46"), "no" : 2, "firstName" : "Satoshi", "lastName" : "Takeda", "age" : 16, "body" : { "height" : 150, "weight" : 55 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 70 } ], "insertDate" : ISODate("2016-10-21T15:00:00Z") }
```
結果からわかるように、`ISODate("2016-10-21T15:00:00Z")`(=JSTでの2016年10月22日00時00分に該当)のレコードが選択されたのがわかります。
# まとめ
今回は自分の体験をもとに**検索・更新・削除**に焦点を当てて、実際に運用で利用される機会が多いであろうクエリについてまとめてみました。
