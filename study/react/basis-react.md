# React に関する基礎情報

## フック (hook)

フック(hook)は React 16.8 で追加された機能。  
React 16.8 の正式版がリリースされたのは 2019 年 2 月 4 日。  
それ以前の React における関数コンポーネントは Stateless なものだったが、hook によって Statefull な関数コンポーネントを作成することができるようになった。

フックは API としては use から始まる関数名で提供されている。(`useState`, `useEffect`など)

## カスタムフック

## 副作用 (effect) フック

プログラミングにおける**副作用 (side effect)**とは、ある機能がデータを変化させた際にそれ以降の演算の結果に影響を与えたり、予測しにくい結果を生じさせることを言う。  
関数型プログラミングでは個々の関数が独立して定義されるため、副作用のリスクが小さくなるメリットがあるとも言われる。

元々ネガティブな意味合いで使われていた副作用だが、React においてはその限りではなく、DOM や state の更新を検知して実行したいコードを指して副作用と呼ぶことがある。  
副作用フックはそのための仕組として存在する。

- クラスコンポーネントの場合 ... `componentDidMount` や `componentDidUpdate` を使う。
- 関数コンポーネントの場合 ... `useEffect` を使う。

`useEffect` はそのコンポーネントがレンダリングされる際に毎回呼び出される。  
コンポーネントの `props` が更新されるとそのコンポーネント自体も再レンダリングされるので、 `props` の更新に合わせてある処理を行いたい場合などに利用できる。(Vue.js における `watch` に似ている。)

`useEffect` は非同期的に実行され、ブラウザによる画面更新をブロックしない。大部分において副作用は同期的に行われる必要がないためにそのようになっている。  
副作用の処理を同期的に行いたい場合(レアケース)は、 `useLayoutEffect` という別のフックを利用する必要がある。

### ESLint の警告

`useEffect` の第一引数の関数内で使用している `state` を第ニ引数の配列に指定しないことで無限ループを回避できるが、そのような記述をすると ESLint が警告を出すことがある。

> React Hook useEffect has a missing dependency: 'count'. Either include it or remove the dependency array. You can also do a functional update 'setCount(c => ...)' if you only need 'count' in the 'setCount'
> call.eslint(react-hooks/exhaustive-deps)

`react-hooks/exhaustive-deps` の警告は `eslint-plugin-react-hooks` に依るもので Create React App の標準設定になっている。
この設定を無視するような定義を eslintrc に上書きして定義すれば回避可能だが、影響範囲が `useEffect` 以外に及ぶ恐れもあるため、以下のコードのように一時的にルールを回避するような指定をしたほうが無難。

```typescript
useEffect(() => {
  setCount(count + 1)
  // eslint-disable-next-line react-hooks/exhaustive-deps
}, [props.count])
```

### `useEffect`の第一引数に渡す関数の返却値

`useEffect`の第一引数に渡す関数から`return`する関数はクリーンアップ関数となる。  
コンポーネントがページから削除（アンマウント）されると、最後に返却したクリーンアップ関数が実行される。  
以下のようにコンポーネントがDOMイベントを用いる場合などに使用するとよい。

```typescript
useEffect(() => {
  const listener = () => {};
  window.addEventListener("resize", listener);
  return () => {
    window.removeEventListener("resize", listener);
  }
}, [])
```

## `useState` と `useReducer`

`useState` は単純な状態管理に用いるフックであるのに対し、`useReducer` はより複雑な状態管理に適したフックである。  
特に、状態が複数の値からなり、状態の更新に複雑なロジックが必要な場合に役立つ。

`useReducer` は実行時、第一引数に Reducer 関数、第二引数に初期の状態、第三引数に初期化関数を受け取る。  
`useReducer` はまた `state` と `dispatch` を返す。`state` は現在の状態を表し、`dispatch` は Reducer 関数を呼び出すための関数である。

```typescript
const initialState = { count: 0 };

const reducer = (state, action) => {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
};

const init = (initialState) => {
  return { count: initialState.count };
};

const [state, dispatch] = useReducer(reducer, initialState, init);
```

## `useMemo` と `useCallback`

`useMemo` と `useCallback` はいずれも基本的にコンポーネントの再レンダリングを最適化するために用意されている。  
純粋なコンポーネントは、何も状態の変更が発生していないように見える場合でも、再レンダリングが行われていることがよくある。  
再レンダリングの最適化には主に次の 2 つの方法がある。

1. 特定のレンダリングで実行する必要がある作業の量を削減する。
2. コンポーネントの再レンダリングが必要になる回数を減らす。

### `useMemo`

`useMemo` はレンダリング内で計算された値を記憶できるようにする。  
小さなキャッシュなようなものであるとも言える。

```typescript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### `useCallback`

`useCallback` は `useMemo` と同じ役割を果たすが、関数専用のものとして用意されている。  
子コンポーネントのイベントハンドラに関数を渡す場合、依存関係のある `state` がなくとも親が再レンダリングされると子も再レンダリングされてしまう。  
`useCallback` で関数を包含することで関数をメモ化し、依存している値に変更がない場合に子の再レンダリングを防ぐ事ができる。

```typescript
const memoizedCallback = React.useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

## コンポーネントのメモ化 `React.memo`

React では基本的に親コンポーネントが再レンダリングされると子コンポーネントも再レンダリングされてしまう。  
親コンポーネントの `state` が更新されると、それに関係しない子コンポーネントも再レンダリングされてしまうということになる。  
 `React.memo` で包含されたコンポーネントは、そのような無関係な更新による再レンダリングから保護される。

```tsx
export const ChildComponent: React.FC<Props> = memo((props) => {
 // content
})
```

### 配列が `props` で渡されているコンポーネントのメモ化

通常値が参照渡しされる配列などが `props` に代入されている場合は、コンポーネントをメモ化していてかつ中身が更新されていないとしても、親コンポーネントが再レンダリングされると子も再レンダリングされてしまう。  
配列が `props` で渡されている場合、React はコンポーネントの関数が実行されるたびに新しい配列を作成するため。

これを避けるには、 `React.memo` でコンポーネントを包含するやり方ではなく、配列を `useMemo` を介して生成されるようにすればよい。

```typescript
const [num, setNum] = React.useState(0);

const numArray = React.useMemo(() => {
  return [num, num + 1, num + 2]
}, [num]);
```

## コンポジション

ネストされた JSX を使って、コンポーネントからコンポーネントへ任意の子要素を渡すことが出来る。これをコンポジションと呼ぶ。  
Vue.js における `slot` に近い。

`props` には標準で `children` というキーが含まれていて、これによって任意の子要素を出力する。

```tsx
function ComponentA(props) {
  return (
    <div>{ props.children }</div>
  )
}

function ComponentB() {
  return (
    <ComponentA>
      <div>It's props.children element.</div>
    </ComponentA>
  )
}
```

`children` 以外にも任意の要素を渡したい場合は、独自の `props` を定義して JSX を渡せばよい。

## コンテクスト

コンテクストは主にグローバルな状態の管理に利用される。Store や Global State と呼ばれるものと同等のもの。  
React Hooks においては `useContext` という標準のフックが用意されている。

コンテクストを使用する手順は以下のようになる。

1. `React.createContext` を用いてコンテクストオブジェクトを作成する。
2. `useContext` を用いてコンテクストオブジェクトを受け取り、そのコンテクストの現在値を取得する。  
   コンテクストの現在値は、ツリー内でこのフックを呼んだコンポーネントの直近にある `<MyContext.Provider>` の `value` の値によって決定される。

## ポータル

`createPortal` によって、コンポーネントのツリー構造に関わらず指定したノードに JSX をレンダリングすることができる。  
`z-index` の制約から脱却しやすいため、モーダル、ダイアログ、ツールチップ、ドロップダウンメニューなどに用いられることが多い。

```tsx
<div>
  <SomeComponent />
  {createPortal(children, domNode, key?)}
</div>
```

## 非難ハッチ

> コンポーネントによっては、React外のシステムに対して制御や同期を必要とする場合があります。例えば、ブラウザAPIを使用して入力フィールドにフォーカスを当てる、Reactを使用せずに実装されたビデオプレーヤの再生や一時停止を行う、あるいはリモートサーバに接続してメッセージをリッスンする、といったものです。

https://ja.react.dev/learn/escape-hatches

### `ref`

コンポーネントに情報を記憶させたいが、情報の更新時に再レンダリングを実行させないようにしたい場合に使う。  
数値や文字列など単純な値を保持させることもできるし、DOMのAPIを利用したい場合にDOM要素を保持しておくこともできる。

`ref`にDOM要素を格納したい場合は、以下のようにしてJSXの要素の`ref`属性に`useRef()`の値を渡す。

```tsx
function Component() {
  const ref = useRef(null);
  return <div ref={ref}>コンポーネント</div>
}
```

`ref`は他のコンポーネントのDOMノードにはアクセスできない。  
特定のコンポーネントが内部のDOMノードをアクセス可能にしたい場合は`forwardRef`を使う必要がある。

https://ja.react.dev/reference/react/forwardRef

## 参考リンク

- [フックの導入 - React](https://ja.reactjs.org/docs/hooks-intro.html)
- [【React Hooks】useEffect のよくある間違い 4 選とそれらを回避する方法](https://tyotto-good.com/blog/avoid-useeffect-mistakes)
- [コンポジション vs 継承 - React](https://ja.reactjs.org/docs/composition-vs-inheritance.html)
