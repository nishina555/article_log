# はじめに
開発を進める上で、「作っては壊すことのできる環境」というのは色々なことを試したい時などに重宝します。Dockerを使うことでそのような環境を簡単に構築できます。
今回はMongoDBを例にDockerを利用した開発環境の構築方法の説明をします。
手順についてはMacやWindowsなどの非Linux環境でDocker Machineを利用しているという前提で説明をしていきます。(Docker Machineのインストール方法については省略しております。)
UbuntuなどのLinux環境を利用しており、ローカル環境にDockerがインストールされている場合は適宜必要な部分だけ読んでいただければと思います。
なお、今回作成したテストレコードを利用したMongoDBのクエリ操作のまとめについてはこちらの[こんな時どうする？ MongoDBクエリ操作逆引きリファレンス](http://qiita.com/nishina555/items/9e20211e8d6f12fdb7b7)にまとめておきましたので、もし興味がありましたら併せて読んでいただければと思います。

# Dockerの使える仮想環境の作成
もし、Dockerを使える仮想環境が作成されていない場合はDocker Machineの`create`コマンドにて環境を作成できます。
ここでは、VirtualBox上に`test-docker`という名前の仮想環境を構築します。

```
$ docker-machine create --driver virtualbox test-docker
```
# 作成した仮想環境の環境変数を読み込む
ローカル環境から`docker`コマンドを利用できるようにするため、先ほど作成した仮想環境の環境変数をローカル環境に読み込ませます。

```
$ eval "$(docker-machine env test-docker)"
```
# MongoDBのコンテナを作成&起動する。
`docker`コマンドが利用できるようになったら次にコンテナの作成と起動を行います。
ここでは`some-mongo`という名前でコンテナ名を保存しています。
MongoDBのイメージが存在しない場合は自動でDocker Hubからイメージを取得してくれます。
なお、`mongo:3.0.12`のようにするとMongoDBのバージョンも指定することができます。
配布されているバージョンについては[公式ページ](https://hub.docker.com/_/mongo/)で確認できます。

```
$ docker run --name some-mongo -d mongo

Unable to find image 'mongo:latest' locally
latest: Pulling from library/mongo

6a5a5368e0c2: Pull complete
068577f76f42: Pull complete
40a9ca5f1cfb: Pull complete
```

# 取得したDokerイメージの一覧の確認方法
取得したDockerイメージの一覧は以下の方法で確認できます。
MongoDBのDockerイメージがなかった場合も、`docker run`後にはDockerイメージが取得できていることが分かります。

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mongo                         latest              3dfc45693d00        2 weeks ago         342.5 MB
```
# Docker上で動いているコンテナの確認方法
`docker run `をした後、正しく起動しているか以下のコマンドで確認できます。
以下の通り、`some-mongo`という名前で保存されたMongoDBのコンテナが起動していることがわかります。

```
$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
6d9206300ab1        mongo               "/entrypoint.sh mongo"   6 minutes ago       Up 6 minutes        27017/tcp           some-mongo
```

# テストレコードの準備
今回はMongoDBにサンプルレコードを挿入したいと思います。以下のソースをコピペしてファイルを作成してください。
ファイル名は`import.json`としてください。

```json:import.json
{"no": 1,"firstName": "Taro","lastName": "Tanaka","age": 15,"body": {"height":165 ,"weight":60},"grades": [{ "semester":"first", "grade":90 },{ "semester":"second", "grade":80 }],"insertDate":ISODate("2016-10-20T15:00:00Z")}
{"no": 2,"firstName": "Satoshi","lastName": "Takeda","age": 16,"body": {"height":150 ,"weight":55},"grades": [{ "semester":"first", "grade":90 },{ "semester":"second", "grade":70 }],"insertDate":ISODate("2016-10-21T15:00:00Z")}
{"no": 3,"firstName": "Jiro","lastName": "Suzuki","age": 15,"body": {"height":170 ,"weight":65},"grades": [{ "semester":"first", "grade":60 },{ "semester":"second", "grade":80 }],"insertDate":ISODate("2016-10-22T15:00:00Z")}
```
# テストレコードをコンテナにコピー
先ほど作成した`import.json`をローカル環境から先ほど`some-mongo`と名前をつけたコンテナ内にコピーします。

```
$ docker cp import.json some-mongo:import.json
```
# コンテナにログイン
MongoDBのコンテナに`bash`で入ります。

```
$ docker exec -it some-mongo bash
```
# テストレコードの挿入
`mongoimport`コマンドを利用して先ほどローカル環境からコピーしてきた`import.json`に記載されているレコードを挿入します。
ここでは、DB名が`test`、コレクション名が`students`のものに対してレコードを挿入しています。(コレクションとはRDBでいうテーブルに該当します。)
なお、コンテナ上ではデフォルトですとrootユーザーになっているため、行頭を`#`で表現しています。

```
# mongoimport --db test --collection students --type json --file import.json

2016-10-22T08:13:40.093+0000	connected to: localhost
2016-10-22T08:13:40.103+0000	imported 3 documents
```
# MongoDBに入る
データの準備ができたら実際にMongoDBに入ります。

```
# mongo

MongoDB shell version: 3.2.10
connecting to: test
```

# DBを指定する
クエリ操作をする前に、まずはどのDBを利用するのか指定をする必要があります。
先ほど作成した`test`というDBを指定してくだい。

```
> use test
switched to db test
```
# MongoDBを操作する
使用するDBを指定したら、先ほど挿入したレコードに対してクエリ操作をすることができます。
以下のコマンドで実際にレコードが正しく挿入されているか確認してみてください。

```
> db.students.count()
3
> db.students.find()
{ "_id" : ObjectId("580b1fb4bc40ca240baa778f"), "no" : 1, "firstName" : "Taro", "lastName" : "Tanaka", "age" : 15, "body" : { "height" : 165, "weight" : 60 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-20T15:00:00Z") }
{ "_id" : ObjectId("580b1fb4bc40ca240baa7790"), "no" : 2, "firstName" : "Satoshi", "lastName" : "Takeda", "age" : 16, "body" : { "height" : 150, "weight" : 55 }, "grades" : [ { "semester" : "first", "grade" : 90 }, { "semester" : "second", "grade" : 70 } ], "insertDate" : ISODate("2016-10-21T15:00:00Z") }
{ "_id" : ObjectId("580b1fb4bc40ca240baa7791"), "no" : 3, "firstName" : "Jiro", "lastName" : "Suzuki", "age" : 15, "body" : { "height" : 170, "weight" : 65 }, "grades" : [ { "semester" : "first", "grade" : 60 }, { "semester" : "second", "grade" : 80 } ], "insertDate" : ISODate("2016-10-22T15:00:00Z") }
```
# MongoDBからログアウトする
以下のコマンドでログアウトできます。

```
> quit()
```

# 削除方法
冒頭でも話したように、Dockerのメリットとしては「作っては壊すことのできる環境」が簡単に手に入ることです。
作成したMongoDBのコンテナを削除する場合は以下のようにできます。`-f`オプションを利用することで強制的に削除することができます。

```
docker rm -f some-mongo
```
Dockerイメージ自体を削除する場合は以下のようにできます。

```
docker rmi mongo
```
# おわりに
今回はMongoDBを例にDockerを利用した開発環境の構築方法の説明をしました。
今回の内容からもわかるようにDockerを利用すると簡単に開発環境を構築できますので、Dockerを積極的に使っていきましょう。
