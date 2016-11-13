...state,
Spread operator
複数の引数 (関数呼び出しのため) または複数の要素 (配列リテラルのため)、あるいは複数の値 (分割代入のため) が置かれるところで式が展開されます。

{}の意味
shorthand property names
http://yutapon.hatenablog.com/entry/2015/06/04/150000
export する側で default がついているものは波カッコなしで import し、 default がついていないものは波カッコありで import する
http://blog.kyanny.me/entry/2015/08/12/231125
波カッコが付いていないのはfromの左側の変数名にfromの右側のモジュールを読み込んで代入していて、波カッコが付いている方は、波カッコの中の名称を変数名としてモジュールが持っている波カッコの中の名称のオブジェクトを代入している→{}はキーとバリューが一致しているときに省略できるってものだから、多分インポートするモジュールの名前と一致してたら波カッコでいいんじゃないのか。
https://intheweb.io/react-redux-todos/

export default connect(mapState, mapDispatch)(CounterContainer);  



----
action
- countを増やしたい
dispatcher
- 誰が処理する？
reducer
- それなら新しい状態はこれだ
store
- その状態を保存しておきます。

index.jsx
- RootContainerを呼び出している, storeの引数を渡している。storeの定義は./store/configureStoreに書かれている
- stateを変化させることができるのはreducerのみ
- dispatchはactionをうけて、reducerを通じて、storeを変更するもの
- store.dispatch(action);

store/configureStore.js
- アプリケーションの状態を保持している
- storeをどうやって作るか、storeからviewをどうやって作るかがキモ
- storeにactionを入力すると、reducerによってstateが更新されます。 また、storeからstateを取り出すことができます。
- reducerを作成して、それをもとにstoreを作成します。
- createStoreでstoreを作成している。作成にはrootReducerを引数として利用している。rootReducerはreducersディレクトリをimportして定義している

reducers/index.js
- Actionに基づいて新しい状態を返す関数
- reducerはstateとactionを入力とし、新しいstateを返す関数です
- 複数のReducerを入れ子にしたりして組み合わせる
- reducerは、actionを受けてstateを変更するの為のメソッドです
- reducers/counterをインポートしている
- const reducer = combineReducers({counter,});

reducers/counter.js
- initial stateはdeducersに書く
- counterの定義をしている。引数にはstateとactionを持つ。
- action.typeによってstateをどのよに更新するか処理が書かれている
- stateのプロパティが定義されている。ここではcountという属性を持っている。
- statenの状態を変更する。
- actionTypesは../utils/actionTypesのものをインポート

containers/RootContainer.jsx
- Reduxで管理された状態と直接繋がるcomponent
- storeの状態をよめて、actionをdispatchできる
- RootContainer = ({store})でindex.jsxから渡された引数を受け取っている
- CounterContainerにを呼び出している。

containers/CountContainer.jsx
- Counterにレンダリング？している。コールバック関数(onClickなど)を引数にしている。コールバックの処理はactionsで定義されているものを利用
- const { counter, actions } = this.props;などでコンストラクタ？を渡している。多分this.propsは親のRootから渡されたstoreの中身だと思う→これがよくわからん。。。これはdestructuring assignmentという記法
```
const obj = { first: 'Redux', last: 'JS' };
const { first, last } = obj;
// first === 'Redux', last === 'JS'
```
- mapStateを定義
- mapDispatchを定義

comonents/Counter.jsx
- 画面の表示に関わる部分
- 状態(count)やメソッドが引数として渡される

actions/index.js
- 状態を変えるためのリクエスト
- actionを生成するActionCreatorとセット
- typeキーをもったただのオブジェクト
- dispatchはactionをうけて、reducerを通じて、storeを変更するもの
- アクションの定義。各アクションメソッドのtypeはなんなのか。
- アクションメソッドはcontainersで呼ばれている。


componentにactionを起こさせるメソッドやstoreの状態を表示させる部分などを宣言
actionに実行したいメソッドを定義
reducerに実際のメソッドの内容をかく。stateの内容(countオブジェクトをもつなど)を記述
containerにreducerやactionと、componentを紐づける




todo app
http://redux.js.org/docs/basics/ExampleTodoList.html

jsとかjsxは？？
