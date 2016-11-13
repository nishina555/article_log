create-react-app react-calculator

npm start

http://localhost:3000

コンポーネントの作成

# モックの作成
index.js
```
render() {
  <div>
    <ContainerCalcurator/>
  </div>
}
```
- ContainerCalcurator.jsを呼ぶ
- とりあえずハローワールド確認
- まずは分解せずに電卓の形を作成する

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
