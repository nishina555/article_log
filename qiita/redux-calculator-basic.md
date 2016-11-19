電卓アプリで学ぶReact/Redux入門(基礎知識編)
# はじめに
電卓アプリで学ぶReact/Redux入門(実装編)を始める前の導入部分となります。

# React.jsとは
MVCに当たるV(View)の部分の役割に特化している。比較されるAngular.jsはMVC全てを担う。
Viewのみの役割を提供するということもあり、学習コストが低いとされている。

# Reduxとは
Fluxベースのフレームワーク。
もっとも人気のあるFlux実装
フレームワークの1つ。React.jsと組み合わせるフレームワークの中で人気なもの。
データの状態を管理することに重きを置いているフレームワーク。データの状態とは今回の電卓アプリの例で言えば「+ボタンが押された」とか「入力された数値の合計」などである。状態を一元管理してくれる。
React.jsと名前が似ているのでReact.jsのフレームワークと思われがちだが、他のjs(Backbone.jsなど)とReduxを組み合わせることも可能。
Reducerが追加で出てくる

# Fluxのデータフローについて
Redux

## actions
- 状態を変えるためのリクエスト部分
- dispatcher(後述)を通じてviewを変更する
- ただのオブジェクト
- 数字を足したい

## components
-

## containers
- Reduxで管理された状態と直接繋がるComponent
- storeの状態を読めてActionをdispatchできる
- 基本的に一番外にいる

## reducers
- actionに基づいて、新しい状態を返す
- 以前はstoresという

## dispather
- サーバー
- actionを受けて、reducerを通じて、storeを変更する
- 実装するものは特にない
- Reduxには存在していない

## store
- データベース
- storeが同じならviewも同じ
- storeをどうやって作るか、storeからviewをどうやって作るかがReactアプリのミソ

# React.jsとRudexの組み合わせる意味について
React.jsはViewのみの役割のため、データの状態によって振りまいを変えたい場合など不都合が生じる。
そこで、ReduxとReact.jsを組み合わせることでReact.jsでも簡単にデータの状態を管理することができる。


# その他
- providerとconnectは正しくはreact-redux
- reduxではそれぞれのcomponentがstateを管理している
- createStoreの中にアプリ全体のstateが保持されている
http://www.slideshare.net/ssusera7b1a1/reduxflux
-
