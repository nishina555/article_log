## リクエスト
### rails
- Railsの規則通りなのでRailsに慣れている人なら敷居が低い
- Gemのインストール・設定が特にない

controller配下にAPIディレクトリをつくる
controllers/api/vi/users_controller.rbのように。



### grape
- rubyで簡単にAPIを作成するためのDSL
- 単体、もしくはRailsやSinatraと組み合わせ使う
- API専用のDSLなのでシンプルにかける
- 若干初期設定が多い
- Railsに比べると大きめのプロジェクトに向いている感じ
- REST API の書き方に特化している DSL (domain-specific language) であるため、簡単にAPI が作成できる
- 既にある Rails アプリに組み込める
- Restful な WEB-API が簡単に作成できる


## レスポンス(view)

### rails
- 簡単なものなら事足りるが、複雑なレスポンスは厳しそう。
- controller内にレスポンスの処理が含まれるので、管理が疎かになりがち

### RABL
- Viewのテンプレートなのでcontrollerに記述しなくても良くなる
- JSON以外のフォーマットにも対応している
- API出力は基本的にモデルに紐づく
- 他のRails、Jbuilderに比べて若干遅い
- 記述が独特なので慣れるまで少し時間がかかる
- 特定のモデルに紐づくプロパティを表すのには便利そう
- GrapeのかわりにRablというJSONとかの記述を楽にするDSLを提供するテンプレートエンジンを導入した. 単純にRailsのrender :jsonとかで出力してしまってもいいのだが, コントローラでJSONの構造を構築するのはスマートではないし, InstagramやTwitterのAPIでは, すべてのAPIレスポンスにEnvelopeと呼ばれる, APIのコールにステータスコードやメッセージをのせるケースが多く, そういうのに対応させようと思うとRablがいいかな, と思って導入した.
http://blog.yucchiy.com/2014/08/20/delivery-api-with-rails/



### Jbuilder


### rails-api
- rails-api を利用するには API 用の Rails プロジェクトを一から作る必要があった。ということで、既に Rails アプリを持っていて、その中に組み込むことは困難である。
http://www.bokukoko.info/entry/2015/12/30/Rails_%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B_API_%E9%96%8B%E7%99%BA_%28%E8%A8%AD%E8%A8%88%E3%81%A8Gem%E9%81%B8%E5%AE%9A%E7%B7%A8%29


## 結論
- grape + jbuilderがrailsでのAPI開発には向いているっぽい(rails-apiとの違いってなんなんだろ？)
-
