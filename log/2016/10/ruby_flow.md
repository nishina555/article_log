
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

## テストデータのインサート
```
$ rails c
irb(main):001:0> Comment.create!({author: 'Pete Hunt',text: 'This is one comment'})
```

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
