##

## プロジェクトの作成
```
rails new rails-project
cd rails-project
```

## gem ファイルの追加と反映
```
$ vim Gemfile
gem 'hogehoge', '~> 1.0'
$ bundle install
```

## データモデルの作成とインサート
```
$ rails g model comment author:string text:text
$ rake db:migrate
```


##  データをひっぱてきてページに表示する

## 外部APIを利用してページに表示する

## リクエストからページビューを作成する

### controllerの作成
```
$ rails g controller comments
```

### viewを作成する
``` app/views/comments/index.html.erb

```

### ルーティングを設定する
```config/routes.rb
root 'comments#index'
```

ルーティングの例
```
root 'comments#index'
```

## APIの設計

### controllerの作成
各メソッドごとに、アクセスされた時に返すjsonを定義。
@data のようにjsonデータを持たせる

```app/controllers/api/v1/comments_controller.rb
class Api::V1::CommentsController < ApplicationController
  def index
    @data = [
      { author: 'Pete Hunt', text: 'This is one comment' },
      { author: 'Jordan Walke', text: 'This is *another* comment' }
    ]
  end
end
```

### viewの作成
jsonを返すviewの定義を行う
```app/views/api/v1/comments/index.json.jbuilder
json.data(@data) { |d| json.extract!(d, :author, :text) }
```

### routeの定義
```app/config/routes.rb
namespace :api, format: 'json' do
  namespace :v1  do
    resources :comments
  end
end
```
