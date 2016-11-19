## 手順

### モデルの作成

```
$ rails new rails-api --api(rails new アプリ名 -B --api)
$ rails new rails-api-scatfold --api
```

```
$ rails g scaffold events title:string body:text
```
もしくは

```
$ rails g model Event title:string body:text
$ rails g model Comment event_id:integer body:text
```

```
$ rake db:migrate (rails db:migrate)
```
Rails5からrakeコマンドがrailsに統合されたのでご注意を。


seedデータの作成
```db/seeds.rb
モデルクラス名.create(:カラム名1 => 値1, :カラム名2 => 値2, ...)
Event.create({title: 'event1 title', body: 'this is the body of event1'})

$ rake db:seed
```

```
$ rails s
```

### 1:多のモデルを作成



rails g scaffold Post
rails g scaffold Comment post:references


## 参考資料
http://morizyun.github.io/blog/rails-api-5.0.0.beta.2/
http://hkdnet.hatenablog.com/entry/2016/05/22/141553
http://uraway.hatenablog.com/entry/2016/07/11/090206
- slide
  - http://www.slideshare.net/fumiyasakai37/rails5api
- scaffoldにて作成
  - https://www.hommax39.com/archives/170
  - http://qiita.com/master-of-sugar/items/7f76bed026e64ec1cceb
  - https://www.school.ctc-g.co.jp/columns/masuidrive/masuidrive29.html
- shallowってのがあるらしい
  - http://qiita.com/kuboon/items/96bbd227f9497ed81f38


## 認証機能付きAPI用のgem
doorkeeper
device


## タスク
- RailsのAPI開発 & Herokuデプロイ & ローカルのReactからアクセス
- 外部APIを利用したAPI開発
  - twitterの検索結果を表示する
  - 乗り換え情報の結果を表示する
- SNS認証機能のついたRailsアプリをやり直す
-- ここまでをハッカソンまでにやりたい
- React/ReduxとRails APIでの開発
- 認証の基礎知識を理解する
- 認証機能のあるRails API
- 認証機能のあるReactのアプリ
- React + Railsの認証付きアプリ
  - twitterで認証してTODOアプリ
- SNS機能とReact(とRails)をうまく連携する



## わからないこと、調べること
- minimum-omniauth-scaffold
- クッキー
  - サーバーからアプリにHTTPレスポンスのヘッダを利用して情報を送る
  - Cookieには「整理番号」としてのセッションIDを格納しておき、実際の値はサーバー側で管理する方法が広く用いられる。
  - サーバーがブラウザに発行するもの
  - APIではCookieという仕組みが使えない
- セッションid
  - クッキーの中にセッションidとセッション名が保存されている
  - セッションはWebブラウザを閉じるまで保存する
- auth
- CSRF
- トークン
  - ログインに成功したら専用のトークンを発行するのが一番簡単な認証方法
  - ログイン -> トークン発行される -> トークン利用してAPIコール -> レスポンス http://www.bokukoko.info/entry/2015/12/20/%E8%AA%8D%E8%A8%BC%E3%82%92%E5%90%AB%E3%82%80_API_%E9%96%8B%E7%99%BA%E3%81%A7%E6%A4%9C%E8%A8%8E%E3%81%99%E3%81%B9%E3%81%8D%E3%81%93%E3%81%A8
- Doorkeeper
  - OAuth2認証を実装するgem
  - deviseと組み合わせてAPIでの認証の実装などを行う　http://qiita.com/kyonsuke19101/items/407f3cdfec38d1108e9d
  - 認可ライブラリ
  - (まずはdoorkeeperを理解することから他のgemを触るのがよさそう)
- devise
  - アカウント管理できる
  - devise + garageなどで組み合わせる　http://qiita.com/kera/items/bca4515ef59c1725f23e
  - 結構複雑みたい
- sorcery
  - deviseと比較される。sorceryのほうが軽いらしい
  - ログイン認証作成できる。deviseよりも簡単にログインなどが実装できるらしい
- garage
  - 認証を含んだapi開発ができる
  - garage-doorkeeperなどがある
  - Garage は Doorkeeper を用いて認可機能を提供します。
  - (記事少ない)
- OmniAuth
- jwt
- sorceryを使ったapi認証
http://dev.classmethod.jp/server-side/ruby-on-rails/ruby-on-rails_sorcery_auth_no5_1/
- deviseかsorceryのgemで認証できるっぽい。deviseのほうがつらいらしい？

## メモ
- facebookでログインとかはrailsで作った方がよさそう。(react使いたければreact-railsを利用。もしくはwebpackとか利用してrailsにreactを組み込むのもできるかもしれない）
- snsでのログインにこだわらなければ、認証付きAPIをrailsで作成すればよさそう。（react-routerによる処理や、deviseとかsorceryとかdoorkeeperとかの勉強が必要かも）
- フロントとサーバーで役割を分担してログイン機能つけるとなると、うーん。
- 認証はフロント？サーバーサイド？
- ユーザー情報の取得とかはどうすればいいのか
- Rails apiのときの課題 http://sssslide.com/speakerdeck.com/hatappi/rails5-apidehazimeruspakai-fa
  - viewがほしくなったらどうする
  - CORS
  - ユーザー認証
    - RailsApiではcookiesとかsessionがなくなってる-> cookieではなくtokenで管理する
    - 認証処理自体はOAuth2で行って、そのあとにトークンを生成する
    - googleを使わないでOAuthするなら、　　doorkeeper
