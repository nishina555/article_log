# はじめに
電卓アプリで学ぶReact/Redux入門(基礎知識編)をお読みください。
create-react-appを利用します。
今回は簡単のために加算のみできる電卓アプリを作成していきます。

## create-react-appについて
create-react-appとは簡単にReact.jsのアプリケーションの雛形を作成するものです。
本来であれば、React.jsはJSX形式で保存され、それをJavaScriptのコードに変換するという必要があります。
JSXとはJavaScriptの中にHTMLタグのような形でReactのComponetを書く形式のものです。
そのため、コンパイルをするためにWebpackやbabelなどの準備が必要なのですが、create-react-appを利用することでその辺は気にしなくてすみます。

# 準備
## create-react-appのインストール
create-react-appが入っている場合はスキップしてください。

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

# アプリの全体像について
ディレクトリは以下のような構成になっています。


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
- React.jsのComponetを格納するフォルダ

## containers
- Componentの中でも、Reduxで管理された状態と直接繋がるComponent
- Storeの状態を読めて、Actionをdispatchできる
ActionとStoreはDispatcherによって連結されていた。
Actionのリクエストを受け取るメソッドとして、Reducerという概念が追加された
ReducerはActionをもとにアプリケーションの状態(state)を変更する
- Reduxで管理された状態と直接繋がるComponent
- storeの状態を読めてActionをdispatchできる
- 基本的に一番外にいる

## reducers
- actionに基づいて、新しい状態を返す

# 実装
## モックの作成

index.jsからcontainerを呼ぶようにします。

```diff:src/index.js
import React from 'react';
 import ReactDOM from 'react-dom';
-import App from './App';
+import CalcuratorContainer from './containers/CalcuratorContainer';
 import './index.css';

 ReactDOM.render(
-  <App />,
+  <CalcuratorContainer />,
   document.getElementById('root')
 );
```

containerに電卓のモックを作成します。

```src/containers/CalcuratorContainer.js
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



## componentの作成

Containerから呼ばれるcomponentを作成します。
まずは先ほど作成した`<botton>`タグをcomponentに置き換えます。

```diff:src/containers/CalcuratorContainer.js
import React, { Component } from 'react';
+import NumBtn from '../components/NumBtn';
+import PlusBtn from '../components/PlusBtn';
+import Result from '../components/Result';


 class CounterContainer extends Component {
   render() {
     return (
       <div>
         <div>
-          <button>1</button>
-          <button>2</button>
-          <button>3</button>
+          <NumBtn n={1} />
+          <NumBtn n={2} />
+          <NumBtn n={3} />
         </div>
         <div>
-          <button>4</button>
-          <button>5</button>
-          <button>6</button>
+          <NumBtn n={4} />
+          <NumBtn n={5} />
+          <NumBtn n={6} />
         </div>
         <div>
-          <button>7</button>
-          <button>8</button>
-          <button>9</button>
+          <NumBtn n={7} />
+          <NumBtn n={8} />
+          <NumBtn n={9} />
         </div>
         <div>
-          <button>0</button>
-          <button>+</button>
+          <NumBtn n={0} />
+          <PlusBtn />
         </div>
         <div>
-           Result: <span>some value</span>
+           <Result />
```

```src/components/NumBtn.js
import React from 'react'

const NumBtn = ({n}) => (
  <button>{n}</button>
)

export default NumBtn
```

```src/components/PlusBtn.js
import React from 'react'

const PlusBtn = () => (
  <button>+</button>
)

export default PlusBtn
```

```src/components/Result.js
import React from 'react'

const Result = () => (
  <div>
    Result: <span>some value</span>
  </div>
)

export default Result
```


## actionの作成
Componetで実行されるactionの定義をしていきます。
電卓アプリでは「番号を押したとき」と「プラスボタンを押したとき」がそれにあたります。

```src/actions/index.js
export const onNumClick = (number) => ({
  type: actionTypes.INPUT_NUMBER,
  number,
});
export const onPlusClick = () => ({
  type: actionTypes.PLUS,
});

```

```src/utils/actionTypes.js
export const INPUT_NUMBER = 'INPUT_NUMBER';
export const PLUS = 'PLUS';
```

## reducerの実装
先ほど作成したactionのロジックにあたるreducerを実装していきます。
電卓アプリにおいて保存しておくべきデータの状態には「押されたボタンの値(inputValue)」「合計値(resultValue)」「計算結果を出すかどうか(showingResult)」の3つがあります。
ここでは、各状態が各アクションによってどのように変更されるのかを記述します。
`initialAppState`は初期状態です。
なお、`...state`というドットが3つ出てくる記法はSpread AttributesというJSXの記法となります。

```src/reducers/calculator.js
import * as actionTypes from '../utils/actionTypes';

const initialAppState = {
  inputValue: 0,
  resultValue: 0,
  showingResult: false,
};

const calculator = (state = initialAppState, action) => {
  if (action.type === actionTypes.INPUT_NUMBER) {
    return {
      ...state,
      inputValue: state.inputValue * 10 + action.number,
      showingResult: false,
    };
  } else if (action.type === actionTypes.PLUS) {
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

export default calculator;
```

今回はreducerは`calculator.js`というものしかありませんが、reducerは複数組み合わせて使うことができます。
`src/reducers/index.js`は複数reducerが存在したときにそれらを組み合わせるためのものです。

```src/reducers/index.js
import { combineReducers } from 'redux';
import calculator from './calculator';

const reducer = combineReducers({
  calculator,
});

export default reducer;
```

# storeの作成
データの状態を表すstoreを作成します。
作成するためには、以下のように`src/index.js`を変更します。
ここではreducerによって変更された状態(store)をcontainerに渡すという動作を行なっています。
これで、Reduxで作成されたデータの状態がReact.jsに渡るようになります。

```src/index.js
import React from 'react';
import { render } from 'react-dom'
import { createStore } from 'redux'
import { Provider } from 'react-redux'
import CalculatorContainer from './containers/CalculatorContainer';
import reducer from './reducers'

const store = createStore(reducer)

render(
  <Provider store={store}>
    <CalculatorContainer />
  </Provider>,
  document.getElementById('root')
)
```


# containerの作成(reactとreduxの結合)
ここで先ほどまでで実装したcomponentとactionをcontainerの中で連携させます。
つまり、componentが`onClick()`されたときのアクションを定義します。
さらに、新しく`mapState`と `mapDispatch(dispatch)`を追加します。
そして、`CalculatorContainer`としてexportしていたものを`connect(mapState, mapDispatch)(CalculatorContainer);`とします。
`mapState`はcomponentのプロパティとデータの状態をバインドするものです。
`mapDispatch`は`bindActionCreators`という`action`を登録するたびに動的に追加してくれるメソッドを呼び出します。
そして、`react-redux`の`connect`を利用して`connect(mapState, mapDispatch)(CalculatorContainer);`とすることで、containerからactionが発行されてstateが変更になるという、ReactとReduxの結合ができるようになります。


```
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';

import * as actions from '../actions';
import NumBtn from '../components/NumBtn';
import PlusBtn from '../components/PlusBtn';
import Result from '../components/Result';

class CalculatorContainer extends Component {
  render() {
    const { calculator, actions } = this.props;
    return (
      <div>
        <div>
          <NumBtn n={1} onClick={() => actions.onNumClick(1)} />
          <NumBtn n={2} onClick={() => actions.onNumClick(2)} />
          <NumBtn n={3} onClick={() => actions.onNumClick(3)} />
        </div>
        <div>
          <NumBtn n={4} onClick={() => actions.onNumClick(4)} />
          <NumBtn n={5} onClick={() => actions.onNumClick(5)} />
          <NumBtn n={6} onClick={() => actions.onNumClick(6)} />
        </div>
        <div>
          <NumBtn n={7} onClick={() => actions.onNumClick(7)} />
          <NumBtn n={8} onClick={() => actions.onNumClick(8)} />
          <NumBtn n={9} onClick={() => actions.onNumClick(9)} />
        </div>
        <div>
          <NumBtn n={0} onClick={() => actions.onNumClick(0)} />
          <PlusBtn onClick={actions.onPlusClick} />
        </div>
        <Result result={calculator.showingResult ? calculator.resultValue : calculator.inputValue} />
      </div>
    );
  }
}

const mapState = (state, ownProps) => ({
  calculator: state.calculator,
});

function mapDispatch(dispatch) {
  return {
    actions: bindActionCreators(actions, dispatch),
  }
}

export default connect(mapState, mapDispatch)(CalculatorContainer);
```

component自身の定義も変更します。

```src/components/NumBtn.js
import React, { PropTypes } from 'react';

const NumBtn = ({n, onClick}) => (
  <button onClick={onClick}>{n}</button>
)

NumBtn.propTypes = {
  onClick: PropTypes.func.isRequired,
};

export default NumBtn
```

```src/components/PlusBtn.js
import React, { PropTypes } from 'react';

const PlusBtn = ({ onClick }) => (
  <button onClick={ onClick }>+</button>
)

PlusBtn.propTypes = {
  onClick: PropTypes.func.isRequired,
};

export default PlusBtn
```

```src/components/Result.js
import React from 'react'


const Result = ({ result }) => (
  <div>
    Result: <span>{result}</span>
  </div>
)

export default Result
```

# 終わりに
フィードバックください



======
# indexの編集
- CalcuratoerContainerを呼び出すようにする

https://github.com/reactjs/react-redux/blob/master/docs/api.md

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

store.dispatch(action)にactionを渡すことでactionが発行される。
connectを使うとactionが発行できる
bindcreatorsでactionwお追加するたびに自動的にマッピングできるようになる
