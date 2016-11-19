
## プロジェクトの作成
```
rails new rails-project
cd rails-project
```

API作成の場合(rails5からはapiを作るときはオプションで--apiを選択する)

```
rails new app_name -B --api
```

## gem ファイルの追加と反映
```
$ vim Gemfile
gem 'hogehoge', '~> 1.0'
$ bundle install (bundleだけでもOKっぽそう)
$ bundle list
```

## テーブルの作成

1対多のテーブルを作るときは、多側(子供)にXXXX_idという外部キーを作成する
http://ruby-rails.hatenadiary.com/entry/20141203/1417601540

```
rails g model Customer name:string
rake db:migrate

rails g model Order customer_id:integer order_date:date
rake db:migrate
```


## モデルの作成

モデル作成
```
$ rails g model article title:string author_id:integer status:boolean
$ rails g model author name:string
$ rake db:migrate
```

データベースの中身の確認(sqliteで説明)

```
$ rails dbconsole (設定したdbに接続)
sqlite> .tables
sqlite> .schema #全ての定義が見れる
sqlite> .schema articles
CREATE TABLE "articles" ("id" INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, "title" varchar, "author_id" integer, "status" boolean, "created_at" datetime NOT NULL, "updated_at" datetime NOT NULL);
```

## データの作成

初期データのインサート(コンソール)

```
$ rails c
irb(main):001:0> Author.create!({name: 'name-test'})
irb(main):001:0> Article.create!({title: 'title-test',author_id: 1, status: true})
```

初期データのインサート(seeds.rbを利用した場合)
seedデータの作成
```db/seeds.rb
モデルクラス名.create(:カラム名1 => 値1, :カラム名2 => 値2, ...)
Article.create({title: 'title-test',author_id: 1, status: true})

$ rake db:seed
```


データの確認

```
$ rails dbconsole
sqlite> select * from articles;
1|title-test|1|t|2016-11-07 12:08:52.984617|2016-11-07 12:08:52.984617
sqlite> select * from authors;
1|name-test|2016-11-07 12:08:44.898740|2016-11-07 12:08:44.898740
```



## テストデータの作成
fixturesを利用する


### controllerの作成
```
$ rails g controller comments
```

### viewを作成する
``` app/views/comments/index.html.erb

```

## ルーティングを設定する
```config/routes.rb
root 'comments#index'
```

ルーティングの例
```
root 'comments#index'
```


## rbenv
rubyのバージョン確認&切り替え

```
rbenv versions
rbenv local(もしくはlocal) 2.2.5
ruby --version
```

bundle command not found対応

```
bundle -v
gem install bundler
bundle -v
```

# rspec
gemfileへの追加
http://qiita.com/h7kayama/items/72cf82625dfed1466947

```
group :development, :test do
  gem 'rspec-rails', '~> 3.0.0'
  gem 'factory_girl_rails', '~> 3.0'
end
```

gemのインストール

```
$ bundle install
```

Rspecを初期化

```
$ rails g rspec:install
```

dbを初期化
```
$ rake db:migrate RAILS_ENV=test
```

## dbのリセットなど

データの入れ直し(テーブルにあるデータを削除し、seedをインサートする)
```
rake db:reset
```

db:migrateをやりなおす
```
rake db:rollback
```

db migrateを整理して、0の状態からmigrateしなおしたい(作成したテーブルもなくなる)
つまり、データベースを削除->データベース作成->テストデータ挿入
Rilas5の場合、rakeじゃなくてrailsコマンドでもいける
```
rake db:migrate VERSION=0
rake db:migrate
rake db:seed
```

auto incrementの値をリセットする
```
delete from sqlite_sequence where name='TABLE_NAME(eventsなど)';
```
