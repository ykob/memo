# React に関する基礎情報

## フック (hook)

フック(hook)は React 16.8 で追加された機能。  
React 16.8 の正式版がリリースされたのは2019年2月4日。  
それ以前のReactにおける関数コンポーネントはStatelessなものだったが、hookによってStatefullな関数コンポーネントを作成することができるようになった。

フックはAPIとしてはuseから始まる関数名で提供されている。(`useState`, `useEffect`など)  

## カスタムフック

## 副作用 (effect) フック

プログラミングにおける**副作用 (side effect)**とは、ある機能がデータを変化させた際にそれ以降の演算の結果に影響を与えたり、予測しにくい結果を生じさせることを言う。  
関数型プログラミングでは個々の関数が独立して定義されるため、副作用のリスクが小さくなるメリットがあるとも言われる。  

元々ネガティブな意味合いで使われていた副作用だが、Reactにおいてはその限りではなく、DOMやstateの更新を検知して実行したいコードを指して副作用と呼ぶことがある。  
副作用フックはそのための仕組として存在する。  

- クラスコンポーネントの場合 ... `componentDidMount` や `componentDidUpdate` を使う。
- 関数コンポーネントの場合 ... `useEffect` を使う。

`useEffect` はそのコンポーネントがレンダリングされる際に毎回呼び出される。  
コンポーネントの `props` が更新されるとそのコンポーネント自体も再レンダリングされるので、 `props` の更新に合わせてある処理を行いたい場合などに利用できる。(Vue.jsにおける `watch` に似ている。)

`useEffect` は非同期的に実行され、ブラウザによる画面更新をブロックしない。大部分において副作用は同期的に行われる必要がないためにそのようになっている。  
副作用の処理を同期的に行いたい場合(レアケース)は、 `useLayoutEffect` という別のフックを利用する必要がある。

### ESLintの警告

`useEffect` の第一引数の関数内で使用している `state` を第ニ引数の配列に指定しないことで無限ループを回避できるが、そのような記述をするとESLintが警告を出すことがある。

> React Hook useEffect has a missing dependency: 'count'. Either include it or remove the dependency array. You can also do a functional update 'setCount(c => ...)' if you only need 'count' in the 'setCount'
> call.eslint(react-hooks/exhaustive-deps)

`react-hooks/exhaustive-deps` の警告は `eslint-plugin-react-hooks` に依るもので Create React App の標準設定になっている。
この設定を無視するような定義をeslintrcに上書きして定義すれば回避可能だが、影響範囲が `useEffect` 以外に及ぶ恐れもあるため、以下のコードのように一時的にルールを回避するような指定をしたほうが無難。

```
useEffect(() => {
  setCount(count + 1)
  // eslint-disable-next-line react-hooks/exhaustive-deps
}, [props.count])
```

## `useMemo` と `useCallback`

`useMemo` と `useCallback` はいずれも基本的にコンポーネントの再レンダリングを最適化するために用意されている。  
純粋なコンポーネントは、何も状態の変更が発生していないように見える場合でも、再レンダリングが行われていることがよくある。  
再レンダリングの最適化には主に次の2つの方法がある。

1. 特定のレンダリングで実行する必要がある作業の量を削減する。
2. コンポーネントの再レンダリングが必要になる回数を減らす。

### `useMemo`

`useMemo` はレンダリング内で計算された値を記憶できるようにする。  
小さなキャッシュなようなものであるとも言える。

```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### `useCallback`

`useCallback` は `useMemo` と同じ役割を果たすが、関数専用のものとして用意されている。  
子コンポーネントのイベントハンドラに関数を渡す場合、依存関係のある `state` がなくとも親が再レンダリングされると子も再レンダリングされてしまう。  
`useCallback` で関数を包含することで関数をメモ化し、依存している値に変更がない場合に子の再レンダリングを防ぐ事ができる。

```
const memoizedCallback = React.useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

## コンポーネントのメモ化 `React.memo`

Reactでは基本的に親コンポーネントが再レンダリングされると子コンポーネントも再レンダリングされてしまう。  
親コンポーネントの `state` が更新されると、それに関係しない子コンポーネントも再レンダリングされてしまうということになる。  
 `React.memo` で包含されたコンポーネントは、そのような無関係な更新による再レンダリングから保護される。

 ```
 export const ChildComponent: React.FC<Props> = memo((props) => {
  // content
})
 ```

 ### 配列が `props` で渡されているコンポーネントのメモ化

通常値が参照渡しされる配列などが `props` に代入されている場合は、コンポーネントをメモ化していてかつ中身が更新されていないとしても、親コンポーネントが再レンダリングされると子も再レンダリングされてしまう。  
配列が `props` で渡されている場合、Reactはコンポーネントの関数が実行されるたびに新しい配列を作成するため。  

これを避けるには、 `React.memo` でコンポーネントを包含するやり方ではなく、配列を `useMemo` を介して生成されるようにすればよい。

```
const [num, setNum] = React.useState(0);

const numArray = React.useMemo(() => {
  return [num, num + 1, num + 2]
}, [num]);
```

## コンポジション

ネストされたJSXを使って、コンポーネントからコンポーネントへ任意の子要素を渡すことが出来る。これをコンポジションと呼ぶ。  
Vue.jsにおける `slot` に近い。

`props` には標準で `children` というキーが含まれていて、これによって任意の子要素を出力する。

```
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

`children` 以外にも任意の要素を渡したい場合は、独自の `props` を定義してJSXを渡せばよい。

## コンテクスト

コンテクストは主にグローバルな状態の管理に利用される。Store や Global State と呼ばれるものと同等のもの。  
React Hooksにおいては `useContext` という標準のフックが用意されている。

コンテクストを使用する手順は以下のようになる。

1. `React.createContext` を用いてコンテクストオブジェクトを作成する。
2. `useContext` を用いてコンテクストオブジェクトを受け取り、そのコンテクストの現在値を取得する。  
コンテクストの現在値は、ツリー内でこのフックを呼んだコンポーネントの直近にある `<MyContext.Provider>` の `value` の値によって決定される。

## ポータル

`createPortal` によって、コンポーネントのツリー構造に関わらず指定したノードに JSX をレンダリングすることができる。  
`z-index` の制約から脱却しやすいため、モーダル、ダイアログ、ツールチップ、ドロップダウンメニューなどに用いられることが多い。

```
<div>
  <SomeComponent />
  {createPortal(children, domNode, key?)}
</div>
```

## 参考リンク

- [フックの導入 - React](https://ja.reactjs.org/docs/hooks-intro.html)
- [【React Hooks】useEffectのよくある間違い4選とそれらを回避する方法](https://tyotto-good.com/blog/avoid-useeffect-mistakes)
- [コンポジション vs 継承 - React](https://ja.reactjs.org/docs/composition-vs-inheritance.html)