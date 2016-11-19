# はじめに

もし、React.jsやReduxについて基本的な知識を学習したい場合は
電卓アプリで学ぶReact/Redux入門(基礎知識編)をお読みください。

# 準備
## create-react-appが入っていない場合

注: Node.js (version 4以上)がインストールされている必要があります。

```
npm install -g create-react-app
```

## 雛形の作成

新しくredux-calculatorのディレクトリが作成されて、React.jsでアプリを作成するための雛形が作られる

```
create-react-app redux-calculator
```

## Hello worldの実行

```
cd redux-calculator
npm start
```
http://localhost:3000 にアクセスしてアプリが起動できていることを確認

## reduxのインストール

```diff:package.json
},
 "dependencies": {
   "react": "^15.3.2",
-    "react-dom": "^15.3.2"
+    "react-dom": "^15.3.2",
+    "react-redux": "^4.4.5",
+    "redux": "^3.6.0"
 },
 "scripts": {
   "start": "react-scripts start",

```

```
npm install
```

# アプリの構成について
以下のようなアプリを作成します。

```
cd src
tree -L 2

├── actions
│   └── index.js
├── components
│   ├── ClearBtn.js
│   ├── MinusBtn.js
│   ├── NumBtn.js
│   ├── PlusBtn.js
│   └── Result.js
├── containers
│   └── CalculatorContainer.js
├── index.js
├── logo.svg
├── reducers
│   ├── calculator.js
│   └── index.js
└── utils
    └── actionTypes.js

```

## 全体的な説明


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
-



## dispather
- サーバー
- actionを受けて、reducerを通じて、storeを変更する
- 実装するものは特にない

## store
- データベース
- storeが同じならviewも同じ
- storeをどうやって作るか、storeからviewをどうやって作るかがReactアプリのミソ


# モックの作成

index.jsからcontainerを呼ぶようにします。

```diff:index.js
import React from 'react';
 import ReactDOM from 'react-dom';
-import App from './App';
+import ContainerCalcurator from './containers/ContainerCalcurator';
 import './index.css';

 ReactDOM.render(
-  <App />,
+  <ContainerCalcurator />,
   document.getElementById('root')
 );
```

containerに電卓のモックを作成します。(以降、containerにあるソースを分解してcomponentを作成していきます。)

```ContainerCalcurator.js
import React, { Component } from 'react';

class CounterContainer extends Component {
  render() {
    return (
      <div>
        <div>
          <button>1</button>
          <button>2</button>
          <button>3</button>
        </div>
        <div>
          <button>4</button>
          <button>5</button>
          <button>6</button>
        </div>
        <div>
          <button>7</button>
          <button>8</button>
          <button>9</button>
        </div>
        <div>
          <button>0</button>
          <button>+</button>
        </div>
        <div>
          Result: <span>some value</span>
        </div>
      </div>
    );
  }
}

export default CounterContainer;

```

この状態で`npm start`を実行すると電卓が作成されると思います。


# componentの作成
- 各componentを呼んで分解する
- actionはまだ未定義でいい

## component/NumBtn
- onClickの実装はreducerで行う

## component/PlusBtn
- onClickの実装はreducerで行う

## component/Result
- resultの実装もまだ


# actionの定義
- actionTypeは別で作成しておく
```
export const onNumClick = (number) => ({
  type: actionTypes.INPUT_NUMBER,
  number,
});
export const onPlusClick = () => ({
  type: actionTypes.PLUS,
});

```

# reducerの実装
reducers/calcurator.js(appReducer)の実装
先ほど作成したactionのロジックを作成
- 保存しておくstateの定義とかプロパティの要素などを記述する
- const initialAppState
```
const initialAppState = {
  inputValue: 0,
  resultValue: 0,
  showingResult: false,
};

const calculator = (state = initialAppState, action) => {
  if (action.type === 'INPUT_NUMBER') {
    return {
      ...state,
      inputValue: state.inputValue * 10 + action.number,
      showingResult: false,
    };
  } else if (action.type === 'PLUS') {
    return {
      ...state,
      inputValue: 0,
      resultValue: state.resultValue + state.inputValue,
      showingResult: true,
    };
  } else {
    return state;
  }
};
```

# storeの作成
index.js
- provider store={store}
- const store = configureStore();


# containerの作成(reactとreduxの結合)
containers.CalcuratoerContainer.js
- connectする
- それぞれのcomponentを呼び出す
```
import * as actionCreators from './actionCreators'
import { bindActionCreators } from 'redux'

function mapDispatchToProps(dispatch) {
  return { actions: bindActionCreators(actionCreators, dispatch) }
}
```

```
render() {
  return (
    <div>
      <>
    </div>
    )
}
```
https://github.com/reactjs/react-redux/blob/master/docs/api.md

# indexの編集
- CalcuratoerContainerを呼び出すようにする

参考

```
function mapStateToProps(state) {
  return {
    value: state.value,
  };
}
function mapDispatchToProps(dispatch) {
  return {
    onClick(value) {
      dispatch(send(value));
    },
  };
}
const AppContainer = connect(
  mapStateToProps,
  mapDispatchToProps
)(FormApp);
```

```
const mapState = (state, ownProps) => ({
  result: state.showingResult,
  resultValue: state.resultValue,
  inputValue: state.inputValue,
});

const mapDispatchToProps =  ({
  onTodoClick: toggleTodo
})

export default connect(mapState, mapDispatch)(CounterContainer);

```

# componentにアクションを追加
# 呼び出しをcomponentからcontainerに変更
```
onClick={actions.onNumClick}
```
calculator.resultValue

Result: <span>{calculator.resultValue}</span>

http://mae.chab.in/archives/2885



## メモ
- packageを最新版にアップデートするにはどうすればいいのか
- packageで管理されているライブラリのバージョンを確認するにはどうすればいいのか
  - npm list --depth=0
- npmのアップデート方法
-
