# WebGL2 の基礎

## WebGL2 コンテキストの取得情報

以下のようにする。

```
const gl = canvas.getContext('webgl2');
```

## API

|API名|用途|
|---|---|
|`gl.clear(mask)`|描画領域を指定した色でクリアする|
|`gl.clearColor()`|描画領域のクリアカラーを設定する|
|`gl.viewport(x, y, width, height)`|描画領域のビューポートを指定する|

## 定数

|定数名|用途|
|---|---|
|`gl.COLOR_BUFFER_BIT`|3DCGの描画領域であるカラーバッファを示す|

## 参考リンク

- [WebGLRenderingContext - Web API | MDN](https://developer.mozilla.org/ja/docs/Web/API/WebGLRenderingContext/)
