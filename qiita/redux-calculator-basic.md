電卓アプリで学ぶReact/Redux入門(基礎知識編)
# はじめに
電卓アプリで学ぶReact/Redux入門(実装編)を始める前の導入部分となります。
Reduxの概念を説明します。

# React.jsとは
まずReduxの説明をする前にReact.jsについて説明をします。
React.jsとは、MVCに当たるV(View)の部分の役割に特化している。比較されるAngular.jsはMVC全てを担う。
Viewのみの役割を提供するということもあり、学習コストが低いとされている。
componentという概念で管理される

## Component
React.jsで作られたアプリケーションを構成するもの。アプリケーションのパーツみたいなもの。
(TODO: 簡単なComponentの例)

## Props, state など(必要があれば)

# Reduxとは
Fluxベースのフレームワーク。
Fluxの思想をもとに作成されたJavaScriptアプリの状態管理ライブラリ
もっとも人気のあるFlux実装
フレームワークの1つ。React.jsと組み合わせるフレームワークの中で人気なもの。
データの状態を管理することに重きを置いているフレームワーク。データの状態とは今回の電卓アプリの例で言えば「+ボタンが押された」とか「入力された数値の合計」などである。状態を一元管理してくれる。
React.jsと名前が似ているのでReact.jsのフレームワークと思われがちだが、他のjs(Backbone.jsなど)とReduxを組み合わせることも可能。
Reducerが追加で出てくる
一つのStoreオブジェクトでstateが管理される。


# Fluxベースな状態管理のモデルについて

- データの状態はAction -> Dispatcher -> Store -> Viewの一方向で伝搬していく



## actions
- Actionは電卓でいうところの「+のボタンを押したら保存している値を加算したい」といったリクエストをするところ
- 状態を変えるためのリクエスト部分
- dispatcher(後述)を通じてviewを変更する
- ただのオブジェクト
- 数字を足したい

## dispather
- サーバー
- DispatcherはActionを受け取りStoreにデータを送るところ
- actionを受けて、reducerを通じて、storeを変更する(ただし、reducerという概念はreduxにのみ存在している)
- 実装するものは特にない
- Reduxには存在していない

## store
- データベース
- storeが同じならviewも同じ
- storeをどうやって作るか、storeからviewをどうやって作るかがReactアプリのミソ
- applicationの状態(state)を管理し、actionsとreducersを統合しているオブジェクト
- rootReducerにより初期状態のstoreが生成される。
- また、getState()でstoreにアクセスできる。dispatch(action)でstateを更新でき、subscribe(listener)でListner登録できる。
- Storeはアプリケーションの状態(store)を保存しておくところ。電卓でいうところの「現在の合計値」などの状態を保存するところ。
- Storeの状態がViewに反映されるので、どのようにStoreを変更するか、変更したStoreをどのようにViewで表示させるかが肝になってくる


# Reduxで新しく出てくる概念について
- 基本的にはFluxのようにデータの受け渡しは一方向
- 実際は差分が以下の図のようにある。(手書きの図を入れる)


## reducers
- actionに基づいて、新しい状態を返すメソッド
- reducerはActionで実際に行う実装の部分


## containers
- Reduxで管理された状態と直接繋がるComponent
- storeの状態を読めてActionをdispatchできる
- 基本的に一番外にいる
- Reduxの最上位のレイヤーであり、ここでReduxのstateとcomponentをつなぐ役割を果たす。



# React.jsとRudexの組み合わせるとは
React.jsはViewのみの役割を担うため、データの状態を管理するのは苦手。
フロントでデータの状態を保持し、状態によって表示を変えたい場合などReact.jsだけでは不便なことがある。
そこで、ReduxとReact.jsを組み合わせることでReact.jsでも簡単にデータの状態を管理することができる。



# 注釈
- Fluxではcomponentが呼ばれるが、ReduxではContainerでDispatchされるようになる
- FluxではStoreメソッドでstateを変更するが、ReduxではReducerでのみStoreの値を変更する
- ReduxではReducerという関数が追加された
- ReduxではActionではどういったリクエストがあるのかを書いて、Reducerに具体的な状態の変更を書く、その結果Storeが変更される
厳密には、FluxとReduxではDispatcherやStoreの定義などが異なっております。
ここでは理解のしやすさを優先させるため、多少乱暴な説明となっております。
詳しくは以下の記事などでわかりやすくまとまっているので詳細がきになる人は参考にしていみてください。


# 参考資料
https://blog.kazu69.net/2016/02/04/rebuilding-react-app-to-using-es2015-classes-and-redux/
http://qiita.com/syossan27/items/7e1b2e07ac68b96bdaa7
http://staltz.com/unidirectional-user-interface-architectures.html
http://fukajun.org/66

====

# その他
- providerとconnectは正しくはreact-redux
- reduxではそれぞれのcomponentがstateを管理している
- createStoreの中にアプリ全体のstateが保持されている
http://www.slideshare.net/ssusera7b1a1/reduxflux
- storeはアプリの状態を管理し、actionsとreducersを統合している
https://blog.kazu69.net/2016/02/04/rebuilding-react-app-to-using-es2015-classes-and-redux/
- reduxとfluxの違い
http://staltz.com/unidirectional-user-interface-architectures.html
- ドキュメントの比較
http://fukajun.org/66
- dispatch関数はRedux独自のもの
- dispatch関数はactionを指定してstoreのstateを更新するReduxのメソッド
