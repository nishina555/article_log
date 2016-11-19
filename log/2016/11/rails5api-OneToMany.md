## 概要
- 親 -> 子の順で作成する
- 親
  - Event
  - title:string, body:text
- 子
  - Comment
  - event_id:integer, body:text

## 手順

### 親の作成

```
$ rails new railsAPI-OneToMany --api
```

```
$ rails g scaffold Event title:string body:text
```

```
$ rake db:migrate
```
もしくは

```
$ rails db:migrate
```
Rails5からrakeコマンドがrailsに統合されたのでご注意を。

```
$ rails dbconsole (設定したdbに接続)
sqlite> .tables
sqlite> .schema #全ての定義が見れる
```


seedデータの作成
```db/seeds.rb
モデルクラス名.create(:カラム名1 => 値1, :カラム名2 => 値2, ...)
Event.create({title: 'event1 title', body: 'this is the body of event1'})

$ rails db:seed
```

```
$ rails s
```


### 子の作成

```
rails g scaffold Comment event:references body:text
```
もしくは

```
$ rails g model Comment event_id:integer body:text
```

DB migrate

```
rails db:migrate
```
アクセス確認

```
localhost:3000/comments
```

routes.rbの変更

```config/routes.rb
Rails.application.routes.draw do
  resources :comments do
    resources :events
end
```

子(Comment)のcontrollerの編集

- index
- create
- set_user


```
Comment.create({name: 'comment name', profile: 'comment profile', event_id: 1})
```

## DBのやり直し

データの入れ直し(テーブルにあるデータを削除し、seedをインサートする)
```
rake db:reset
```

データベースを削除->データベース作成->テストデータ挿入(rakeのかわりにrailsでもOK)
モデル自体変更した場合はこっちでやる
```
rake db:migrate VERSION=0
rake db:migrate
rake db:seed
```




## 参考

- scaffoldにて作成
  - http://sil.hatenablog.com/entry/rails-nested-resource-by-scaffold
  - http://qiita.com/weed/items/676e47e20bfe04f0e442
- shallowってのがあるらしい
  - http://qiita.com/kuboon/items/96bbd227f9497ed81f38
