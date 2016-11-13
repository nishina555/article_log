## 準備

```
$ rails new rails-grape-sample -B --api
$ rails g model Event title:string body:text
$ rails g model Comment event_id:integer body:text
$ rake db:migrate
```

```Gemfile
group :development, :test do
   gem 'rspec-rails', '~> 3.0.0'
   gem 'factory_girl_rails', '~> 3.0'
end

gem 'grape'
gem 'grape-jbuilder'
gem 'hashie-forbidden_attributes'
```

```
$ bundle
```

```Gemfile
group :development, :test do
   gem 'rspec-rails', '~> 3.0.0'
end
```

```
$ rails g rspec:install
```

```app/models/event.rb
class Event < ApplicationRecord
  has_many :comments
end
```

## APIの実装

```
$ mkdir -p app/apis/api
$ mkdir -p app/apis/entity
```

app/apis/ 下の Ruby ファイルが読み込まれるようにします。

```config/application.rb
config.paths.add File.join('app', 'apis'), glob: File.join('**', '*.rb')
config.autoload_paths += Dir[Rails.root.join('app', 'apis', '*')]
```

基底となるAPIクラスを作成し、ここで各バージョンのAPIをマウントする

```app/apis/api/base.rb
module API
  class Base < Grape::API
    mount V1::Base
    # mount V2::Base
  end
end
```

各バージョンの中で、基底となるAPIクラスを作成する

```apis/api/v1/base.rb
module API
  module V1
    class Base < Grape::API
      format :json
      prefix :api # /apiというパスになる
      version 'v1', using: :path # /api/v1というパスになる
      mount V1::Events
      mount V1::Comments
    end
  end
end
```

とりあえずGETできるか確認

```apis/api/v1/events.rb
module API
  module V1
    class Events < Grape::API
      resource :events do
        desc 'GET /api/v1/events'
        get do
          Event.all
        end

        desc 'GET /api/v1/events/:id'
        params do
          requires :id, type: Integer, desc: 'Event id'
        end
        get ':id' do
          Event.find(params[:id])
        end
      end
    end
  end
end
```

```apis/api/v1/comments.rb
module API
  module V1
    class Comments < Grape::API
      resource :events do
        route_param :event_id do
          resource :comments do
            desc 'GET /api/v1/events/:event_id/comments'
            get do
              Event.find(params[:event_id]).comments
            end
          end
        end
      end
    end
  end
end
```

seedデータの作成
```db/seeds.rb
モデルクラス名.create(:カラム名1 => 値1, :カラム名2 => 値2, ...)
Events.create({title: 'event1 title', body: 'this is the body of event1'})

$ rake db:seed
```

```http://localhost:3000/api/v1/events
[{"id":1,"title":"event1 title","body":"this is the body of event1","created_at":"2016-11-10T14:24:58.807Z","updated_at":"2016-11-10T14:24:58.807Z"}]
```

```http://localhost:3000/api/v1/events/1
{"id":1,"title":"event1 title","body":"this is the body of event1","created_at":"2016-11-10T14:24:58.807Z","updated_at":"2016-11-10T14:24:58.807Z"}
```

```http://localhost:3000/api/v1/events/1/comments
[{"id":1,"event_id":1,"body":"this is the body of comment1","created_at":"2016-11-10T14:24:58.822Z","updated_at":"2016-11-10T14:24:58.822Z"}]
```


## jbuilderによるAPIのレスポンスの整形

自動読み込み対象に/api/ディレクトリを追加。そして、JbuilderのViewパスを設定する。
http://qiita.com/zaru/items/fa64a4e00b76ea6ce36f
```application.rb
config.middleware.use(Rack::Config) do |env|
  env['api.tilt.root'] = Rails.root.join 'app', 'views', 'apis'
end
```

formatterをJbuilder指定して、各アクションにテンプレートファイルを指定してあげればOK。
```
formatter :json, Grape::Formatter::Jbuilder
```

app/views/api/items.jbuilderファイルを使う場合、apiのほうにはjbuilder:'items'と書きます。
単数、複数気をつける
```

```
## jbuilderの文法など

### index
```
# view
json.events @events do |event|
  json.(event, :id, :title, :body)
end

# response
{"events":[{"id":1,"title":"event1 title","body":"this is the body of event1"}]}
```

```
# view
json.extract! @events, :id, :title, :body
```

```
# view
json.array!(@events) do |event|
  json.extract! event, :id, :title, :body
end

# response
[{"id":1,"title":"event1 title","body":"this is the body of event1"}]
```


### show

```
# view
json.evnet do |json|
  json.title @event.title
end

# response
{"evnet":{"title":"event1 title"}}
```

```
# view
json.extract! @event, :id, :body

# response
{"id":1,"body":"this is the body of event1"}
```


```
# view
json.event do |json|
  json.title @event.title
end

# response
{"event":{"title":"event1 title"}}
```
