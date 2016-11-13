RailsでWeb APIを作成するとき、皆さんはどのような実装を行っているでしょうか？一番多いのは、Rails標準のControllerにてリクエストを受け付け、結果をjson形式などで返すかたちだと思います。
もちろんこのような実装でも十分なのですが、今回はGrapeを使った実装を紹介したいと思います。
簡単に纏めると
REST-likeなAPIのためのマイクロフレークワークである
Rack上で単体で、もしくはRailsやSinatraなどと組み合わせて動く
RESTful APIを簡単に作るためのDSL(ドメイン固有言語)である
http://dev.classmethod.jp/server-side/ruby-on-rails/ruby-on-rails_create_grape_web-api/


route_param


Rails4から事実上標準になったっぽいJbuilderを使って、Grape+RailsなWebAPIのJSONフォーマットを柔軟に作ってみる。has_manyなどで深い階層のモデルを使ってAPIを作ると面倒くさいことが多いんだけど、Jbuilderならシンプルに書くことが出来る。
他のGemでActiveModel::Serializersっていうのもあったけど、こっちはモデルで出力フォーマットを定義していくタイプっぽくて、複雑なJSONフォーマットにしようとすると面倒。Viewで定義できるJbuilderの方が良さげ。
http://qiita.com/zaru/items/fa64a4e00b76ea6ce36f


参考
http://xoyip.hatenablog.com/entry/2014/05/15/201100
http://dev.classmethod.jp/server-side/ruby-on-rails/ruby-on-rails_create_grape_web-api/
http://xoyip.hatenablog.com/entry/2014/05/12/204748
http://qiita.com/Kaki_Shoichi/items/d4adcf0298ed0b4614a2

jbuilderとgrapeを利用したRails API開発


jbuilderは複雑なjsonの生成も見やすく書けるし、「Railsのバージョンによってmodelをto_jsonした時のキーの有り無しが違う」みたいな事故もなくて安心できそうです。
http://llcc.hatenablog.com/entry/2015/03/07/103121



## RailsでのAPI開発の基本事項

lib/tasks/routes.rakeはapiのルートをrake routesで表示させるようにするもの
http://xoyip.hatenablog.com/entry/2014/05/14/201109


## jbuilderの文法
導入はGemfileにgem 'grape-jbuilderを入れる。そうするとGrape::Formatter::Jbuilderが使える

jbuilderは以下を記述

```
class API < Grape::API
  format :json
  formatter :json, Grape::Formatter::Jbuilder
  default_format :json
```
あとはjbuilderを使うAPIにどのViewを対応させるかを書いてあげれば使えるようになります。
app/views/api/items.jbuilderファイルを使う場合、apiのほうにはjbuilder:'items'と書きます。

http://xoyip.hatenablog.com/entry/2014/05/12/204748
http://llcc.hatenablog.com/entry/2015/03/07/103121


```app/views/api/items.jbuilder
json.items do
  json.array!(@items) do |item|     json.(item, :id, :title, ..などなど..)
  end
end

```


## grapeの文法

/api/items/1/entriesでItem(id=1)のEntryを全て返す
/api/items/1/entries/1でItem(id=1)のEntryのうち(id=1)のものを返す

```
resource :items do
  route_param :item_id do
    resource :entries do
      get '/', jbuilder:'entries' do
        @item = Item.find_by_id(params[:item_id])
        @entries = @item.entries if @item
      end

      get '/:id', jbuilder:'entry' do
        @item = Item.find_by_id(params[:item_id])
        @entry = @item.entries.find_by_id(params[:id]) if @item
      end
    end
  end
end
```
http://xoyip.hatenablog.com/entry/2014/05/15/201100




## 手順
modelの作成


dbseedの作成

rspecのインストール

GrapeとJbuilderの準備

```config/applications.rb
module Samples
  class Application < Rails::Application

    config.paths.add "app/api", glob: "**/*.rb"
    config.autoload_paths += %W(#{config.root}/app/api)

    config.middleware.use(Rack::Config) do |env|
      env['api.tilt.root'] = Rails.root.join 'app', 'views', 'api'
    end

  end
end
```
もしくは

```
# config/application.rb

module GrapeExample
  class Application < Rails::Application
    config.paths.add File.join('app', 'apis'), glob: File.join('**', '*.rb')
    config.autoload_paths += Dir[Rails.root.join('app', 'apis', '*')]

    config.middleware.use(Rack::Config) do |env|
      env['api.tilt.root'] = Rails.root.join 'app', 'views', 'apis'
    end
  end
end
```

## 参考
http://xoyip.hatenablog.com/entry/2014/05/12/204748
http://xoyip.hatenablog.com/entry/2014/05/15/201100


## やること
こいつをベースにしていく
http://kzy52.com/entry/2014/11/07/084023

まず、以下のやりかたにしたがってjbuilderを利用せずにgrapeのルーティングを完成させる.（application.rbの設定もまだいらない）
http://qiita.com/magaya0403/items/f9cd1340960ab997cf63

とりあえず、apiの確認をする。

それが終わったら、jbuilderでフォーマットを変えていく


apiのroutesを確認したい場合は以下を参考にする
http://xoyip.hatenablog.com/entry/2014/05/14/201109

このへんは参考になる
(grape + jbuilderのときのcontrollerの書き方)
http://xoyip.hatenablog.com/entry/2014/05/15/201100

(jbuilderのviewの書き方)
http://xoyip.hatenablog.com/entry/2014/05/12/204748

apiの複数バージョン管理
http://qiita.com/hkusu/items/2ca0323cc276ab31e926

jbuilderの設定について
http://qiita.com/zaru/items/fa64a4e00b76ea6ce36f

jsonbuilderの書き方まとめ
http://blog.livedoor.jp/sasata299/archives/52034616.html
http://samurai.ataglance.jp/jbuilder/
