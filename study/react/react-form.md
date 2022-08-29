# Reactを用いてフォームを自作する

## HTML標準の属性の値を要素に渡す

`input` などの各要素に `name` や `placeholder` など、HTML標準の属性を渡す場合、Reactではすべてpropsで処理する。

```
// 親
<InputText name="firstName" placeholder="太郎" />

// 子
export const InputText = ({
  name,
  placeholder,
}: {
  name: string
  placeholder: string
}) => {
  return <input name={name} placeholder={placeholder} />
}
```

## 参考リンク

- [フォーム - React](https://ja.reactjs.org/docs/forms.html)
