# はじめに
React.jsとRailsを個別で学習しており、そろそろ二つを合わせてアプリケーションを作成したいなと思っております。  
しかし、調べてみるとReact.jsとRailsを一緒にする方法としていくつかあるようでした。  
ひと通りネットで調べて情報をまとめたので、以下にその内容を記載します。  
**自分はReact.js+Railsなアプリを作ったことがないです。各ツールについてもネットで調べただけで実際に使ったことがないので見当違いなことをいっているかもしれないです。ですので、あまり鵜呑みにしないでください。**（ただし、参考資料だけはとてもわかりやすいものばかりでした。）

# ReactとRailsの共存の仕方
## 1.Railsのviewの中にReactを埋め込む
### 概要
- react-railsを利用する
- `react_component`ヘルパーでReactのコンポーネントをレンダリングしてくれる
- `prerender: true`でサーバーサイドレンダリングもできる
- RailsでレンダリングしたViewの中でReactが実行される。
- RactはAjaxリクエストを送る。Railsは jsonを返し、ReactはViertual DOMを組み立てる。
### メリット
- 導入が簡単
- RailsでレンダリングするViewの中にjsonを埋め込んで、サーバーサイドでJSを実行して初期状態を表示することができる。(=サーバーサイドレンダリング)
- サーバーサイドレンダリングにより初期のロード時間を短縮でき、SOにも対応できる。
### デメリット
- React以外のnpmライブラリは自分でなんとかする必要がある

### 実装方法
react-railsを追加する
```
$ vi Gemfile
# ...省略
gem 'react-rails', '~> 1.0' # 追加する

$ bundle install
```
reactを使えるようによしなにやってくれるgeneratorが用意されているのでそれを利用する
```
$ rails g react:install
```

react-railsにはComponent定義を生成してくれるgeneratorがある。これを叩いて最初のComponentを作る。
下記の例では、app/assets/javascripts/components/comment_box.js.jsxというファイルができるので、そこにreactのコードをがしがし書いていく。
```
rails g react:component CommentBox
```

コントローラーの作成
```
$ rails g controller comments
```

railsのviewからreactを呼び出す
```　app/views/comments/index.html.erb
<%= react_component('CommentBox') %>
```

```config/routes.rb
root 'comments#index'
```

## 2.Railsプロジェクトの中にReactのフロントエンドアプリを入れる
### 概要
- webpackなどを利用する(ツールなどは下記に詳細を記述する)
- Railsのasset-pipelineの仕組みには頼らない
### メリット
- 作業としては分かれているがプロジェクトとしては一元管理される
- npmモジュールを使ったりES6で記述できたりできる
- npmのpackage managerを使える
- Modulesを使える
### デメリット
- フロントエンドの開発環境のセットアップが大変

## 3.Rails APIと Reactで分ける
### 概要
- Railsはjsonを返すことに徹底する
- `rails-api`というのがあるのでそれを利用するほうぎいらしい
- サーバーレンダリングとか考える必要がないならよさそう。
### メリット
- 開発を分けることができる
### デメリット
- プロジェクトを別で管理しないといけない。サーバーも別で用意する必要がでてくる
- API認証やCORSなどを考える必要がある
- 別々のプロジェクトで分けられるが開発のオーバーヘッドが高くなる

# ビルドツールのまとめ
react, railsで調べているとwebpackとかbabelとかよくわからないのがたくさん出てくるのである程度体系的にまとめる。
## モジュールハンドラー(ファイル生成系)
JSビルドツールのこと。フロントエンドのJSファイルを生成する。JSファイルのコーディングの部分で開発者の手助けをする
### Webpack
- コード分割できる
- pluginによる拡張できる
- Loaderという仕組みがあり、ソースコードに適用する処理を柔軟に設定できる
- babel-loaderを使うことでes2015やJSxで記述したJSファイルを変換することができる。babelはJSXファイルをコンパイルするもの。(reactのチュートリアルのコードも<script type="text/babel">で囲まれていた)
- ビルドやコードの配置まではwebpack, ファイルへのフィンガープリントはSprocketsに任せるという運用
- 機能がたくさんあるので、gulpなどを使わなくても設定ファイルを変更するだけで大体のことは実現できる。
### Browserify
- 単一ファイル作成に特化している
- browserify-railsを利用すると、RailsのSprocketsでbrowserifyを利用できるようになる。ただ、browserify-railsはビルドがすごい遅い。
- ビルドに時間かかる
- browserify-railsを導入するとnpmのモジュールとgemとして提供されているライブラリの両方がsprocketsに載せてasset precompileできるようになる。
## タスクランナー(実行管理系)
- gulpとGruntで人気を二分している。
- JSXやLESSやSCSSなど変換する必要があるファイルをどうゆう処理で変換して、どこに出力するか「タスク」として管理してくれるもの。
- Webpackなどを呼び出してントリポイント毎にJSファイルを一つにしたりなどする。
- Railsのassetのコンパイルの仕組みを完全に捨てている。自由度が高いが環境構築に時間がかかる。(タスクランナーを利用せず、Browserifyなどを直接呼ぶのであれば、Rails側と住み分けができる。)
- 「タスクランナー」の中で「モジュールハンドラー」が呼ばれるの関係性
### gulp
- 現時点で開発コミュニティの活動が一番活発。シンプルな設定ファイルと、実行速度に定評あり。プラグインをpipeして連続実行できるため、中間ファイルの生成が不要。
- コーラみたいなアイコン
### Grunt
- 遅い! 設定ファイルが分かりにくい! とか言われつつもデファクト感のあるのがGrunt。プラグインが最多
### (Sprockets)
- gemのひとつでJavaScriptやCSSをコンパイルする仕組みが提供されている。Sprocketsはasset管理を行う。gulpとかとは対立の関係にあるものといってよい(デフォルトのコンパイラーに任せるか、フロントと切り離してフロント側のコンパイラーに作業を委譲するか、みたいな対立。)
## ライブラリ管理系
バージョン管理などでよく利用される。フロントとバックエンドで管理ツールを分けるとWebpackが取り扱うライブラリを細かくしていできるのでメリットがある
### npm
- node.js側
### Bower
- フロントエンド側

# おわりに
自分の短期目標としては、「ハッカソンでチームに貢献できるメンバーになる」、「バックエンドエンジニアとして開発と設計ができる」ことなので、色々調べたけれどもRailsはAPI作成に専念してもらって、React.jsにフロントを全て任せるというスタンスを中心にプログラミングやっていこうと思います。  
とはいえ、せっかく調べたので、react-railsやwebpackについても触りくらいは学習しておこうと思います。

# 参考資料
http://qiita.com/jwako/items/195e2e85793716fb1d53
http://techlife.cookpad.com/entry/2015/12/14/130041
http://techlife.cookpad.com/entry/2016/07/27/101015
http://qiita.com/derui@github/items/5233b4ef8fbde1e80d33
http://qiita.com/takayukishmz@github/items/2498ef5eb2de7fa28658
http://qiita.com/kmszk/items/45fb4690ace32216ca25
http://qiita.com/cognitom/items/db88babebeb869a4a198

## その他
- reactのチュートリルはjsファイルに書いてコンパイルとかしなかったけど、そもそもreactのソースは<script type="text/babel">で囲まれてたから、その中でよろしくjsxをjsファイルに変換してくれてたみたい。babelと書く前はjsxって書いてたらしい。
- react-railsはjsxファイルをコンパイルしてjsにするものだとおもう。webpackを入れるとreactの中でnpmのモジュール使える。railsはjsx呼ぶ。で、そのjsxにはnpmとかのモジュールがあるからwebpackとかも使ってコンパイルしてサーバサイドレンダリング（サーバーサイドでHTML形式にしてフロントに返却してフロントはサーバーで変換されたやつをただ表示させるだけ）を実装しているんだと思われる。
