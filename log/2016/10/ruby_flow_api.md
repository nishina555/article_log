## APIの設計
・jbuilderを利用する
・



## controllerの作成　
各メソッドごとに、アクセスされた時に返すjsonを定義。
@data のようにjsonデータを持たせる

```app/controllers/api/v1/comments_controller.rb (GET)
class Api::V1::CommentsController < ApplicationController
  def index
    @data = [
      { author: 'Pete Hunt', text: 'This is one comment' },
      { author: 'Jordan Walke', text: 'This is *another* comment' }
    ]
  end
end
```

```app/controllers/api/v1/comments_controller.rb (POST)

```

DBのデータを返す場合
```
class Api::V1::CommentsController < ApplicationController
  def index
    @data = Comment.all
  end
end
```


```app/controllers/api/v1/users_controller.rb
class Api::V1::UsersController < ApplicationController
  def index
    @users = User.where("addr1 = ?", user_params[:addr1])
  end


  private
  # リクエストパラメータのバリデーション
  def user_params
    params.permit(:addr1)
  end
end
```



## viewの作成
jsonを返すviewの定義を行う
```app/views/api/v1/comments/index.json.jbuilder (GET)
json.data(@data) { |d| json.extract!(d, :author, :text) }
```

```app/views/api/v1/comments/show.json.jbuilder (POST)
json.extract!(@comment, :author, :text)
```



## routeの定義
```app/config/routes.rb
namespace :api, format: 'json' do
  namespace :v1  do
    resources :comments
  end
end
```

```
# API用
namespace :api, {format: 'json'} do
  namespace :v1 do
    namespace :users do
      get "/" , :action => "index"
    end
  end
end
```

## テストデータの作成

## テストデータのリフレッシュ
テーブルの初期化
```
$ rake db:migrate:reset
```

## curlでのAPI確認方法
```
$ curl -X POST http://localhost:3000/api/v1/comments.json -H 'content-type: application/json' -d '{"author": "Jordan Walke", "text": "This is *another* comment"}'
```

## データをひっぱてきてページに表示する

## 外部APIを利用してページに表示する

## リクエストからページビューを作成する

## APIのURL
http://localhost:3000/api/v1/comments.json など
