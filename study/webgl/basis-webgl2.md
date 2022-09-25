# WebGL2 の基礎

## 参考書籍

- [初めてのWebGL 2 第2版](https://www.oreilly.co.jp/books/9784873119373/)
- [Example](https://github.com/PacktPublishing/Real-Time-3D-Graphics-with-WebGL-2)

## WebGLレンダリングパイプライン

WebGLは単なるラスタライゼーションエンジンに過ぎない。  
入力されたコードに従ってポイント、ライン、三角形を描画するだけ。  
それ以上のことをWebGLで実現したければ、目的とする処理を実現するコードを自分で記述する必要がある。

WebGLはマシンのGPU上で動作するため、GPU上で実行されるコードを記述しなければならない。  
コードは組となる2つの関数として与える必要がある。それらは頂点シェーダー(vertex shader)とフラグメントシェーダー(fragment shader)と呼ばれる。  

|用語|用途|
|---|---|
|頂点バッファオブジェクト (VBO)|描画対象のジオメトリを定義するデータを保持する。頂点座標、法線、色、テクスチャ座標など。|
|インデックスバッファオブジェクト (IBO)|このバッファ内の値が頂点バッファ内の各頂点のインデックスとして解釈される。|
|頂点シェーダー|各頂点に対して呼び出されるコード。頂点座標、法線、色、テクスチャ座標などの頂点ごとのデータを操作する。これらのデータは頂点シェーダ内では `attribute` として扱われる。|
|フラグメントシェーダー|描画領域のピクセルごとの色を計算する。|
|フレームバッファ|フラグメントシェーダーによって処理されたフラグメントを保持する2次元のバッファ。レンダリングパイプラインの最終出力先。|
|テクスチャ|シェーダー内からアクセスできる配列状のデータ。画像データが最も一般的だが、実際には単なる数値データで、色の配列以外の値も保持できる。|
|`attribute`|頂点シェーダで使用される入力変数。VBOからどのようにデータを取り出すかが指定されていて、それに基づいて頂点シェーダーにデータを提供する。頂点シェーダは頂点ごとに呼び出されるので、 `attribute` の値は頂点シェーダーが実行されるたびに異なる。|
|`uniform`|頂点シェーダーとフラグメントシェーダーの両方で利用できる入力変数。一度のレンダリングサイクル中に値が変わることはない。|
|`varying`|頂点シェーダーからフラグメントシェーダーにデータを受け渡すために使用する変数。|
|頂点|3Dオブジェクトの角を定義する点。x, y, z座標に対応する3つの浮動小数点数で表現される。頂点はすべてJavaScript配列で記述し、その配列を使用してWebGLの頂点バッファが構築される。|
|インデックス|WebGLに頂点をどのように接続して面を構成するかを伝える。頂点と同様、JavaScript配列で記述し、WebGLのインデックスバッファを通してレンダリングパイプラインに引き渡される。インデックス配列は一般的に三角形を反時計回りで定義する。その順序を利用して面の前後の向きを判断し、それに基づいてカリングや描画を実行することがある。|
|カリング (culling)|オブジェクトのポリゴンが表示可能かどうかを決定する。|

## WebGL2 コンテキストの取得情報

以下のようにする。

```
const gl = canvas.getContext('webgl2');
```

## WebGLバッファの作成・操作

`gl.createBuffer()` で作成する `WebGLBuffer` をバインド (`gl.bindBuffer()`) すると、それ以降のバッファに対する操作は、バインドを解除するか別のバッファがバインドされるまで、バインド中のバッファに対して適用される。

WebGLは常に現在バインドされているバッファを通じてデータを得る。つまり、ジオメトリを処理する操作を呼び出す前に必ずバッファがバインドされていなければならない。バッファがバインドされていない場合は `INVALID_OPERATION` エラーが発生する。 

バッファをバインドすると次にそのバッファに値を指定する必要がある。それには `gl.bufferData()` 関数を利用する。  
WebGLでは `bufferData` メソッドの引数としてJavaScript配列をそのまま使用できず、[型付き配列 (Typed Arrays)](https://developer.mozilla.org/ja/docs/Web/JavaScript/Typed_arrays) に変換する必要がある。  
頂点座標には浮動小数点数を利用できるが、インデックスには必ず整数を使用しなければならない。

```
const positionBuffer = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
gl.bufferData(gl.ARRAY_BUFFER, new FloatArray(vertices), gl.STATIC_DRAW);
```

また、頂点シェーダーの `attribute` を現在バインドされているVBOに関連付ける必要がある。  
WebGL関数 `gl.vertexAttribPointer()` が関連付けを行い、 `gl.enableVertexAttribArray()` が `attribute` の有効化を行う。

```
gl.vertexAttribPointer(index. size, type, normalize, stribe, offset);
gl.enableVertexAttribArray(index);
```

## レンダリング

VBOを定義し、頂点シェーダーを `attribute` に関連付ければ、レンダリングの準備は完了する。   
レンダリングに使用する関数には `drawArrays()` 関数と `drawElements()` 関数がある。

- `drawArrays()` は頂点データをバッファ内で定義されている順番に使用する。
- `drawElements()` はインデックスを使用して頂点データバッファにアクセスしてジオメトリを作成する。
- いずれの関数も有効な頂点シェーダーの `attribute` に関連付けられたVBOだけを使用する。

通常、有効化される配列の数は複数存在する。頂点の色、頂点ごとの法線、テクスチャ座標、など。  
その場合、それぞれ有効な頂点シェーダの `attribute` に関連付けられることになる。

### `drawArrays`

インデックス情報が利用できない場合は `drawArrays()` 関数を使用する。  
通常、三角形や四角形など、ジオメトリが単純でインデックスを使用するまでもない場合に利用されることが多い。  
WebGLはVBO内で定義されている順番で頂点座標を使用してジオメトリを作成するため、連結された三角形がある場合はVBO内で同じ座標を複数定義しなければならない。

### `drawElements`

`drawElements()` 関数を使用する場合は少なくともVBO、IBOの2つのバッファが必要になる。  
頂点シェーダーはVBO内のそれぞれの頂点に対して実行され、レンダリングパイプラインがIBOを使用してトライアングルからジオメトリを構築する。

## 頂点配列オブジェクト (VAO)

頂点配列オブジェクト (Vertex Array Object:VAO) を使用すると、複数のバッファの頂点やインデックスのバインディング情報をすべて一度に保存できる。  
つまり、それぞれの `attribute` が使用するバッファとバッファからデータを取り出す方法の設定をVAOに集約できる。 

VAOはWebGL1では拡張機能として実装されていたが、WebGL2では標準で利用できる。  

VAOは必ず使用するべきである。  
VAOを使用しないとすべての `attribute` がグローバルに保持され、バッファ操作の関数呼び出しがグローバルな状態を操作することになる。そうなると、あらゆるドローコールの前にすべての `attribute` を準備しなければならない。インデックス付されたデータを使用している場合は、さらに `ELEMENT_ARRAY_BUFFER` も設定しなければならず、パフォーマンスの低下を招く。

VAOを使用すれば、初期化時にすべての `attribute` を準備しておけばよく、描画時にはVAOをバインドするだけですべての `attribute` の設定が完了し。パフォーマンスを向上させられる。

## API

|API名|用途|
|---|---|
|`gl.bindBuffer(target, buffer)`|与えられた `WebGLBuffer` を `target` に結合する|
|`gl.bufferData(target, srcData, usage, srcOffset, length)`|バッファオブジェクトのデータストアを初期化、作成する|
|`gl.clear(mask)`|描画領域を指定した色でクリアする|
|`gl.clearColor()`|描画領域のクリアカラーを設定する|
|`gl.createBuffer()`|頂点や色といったデータを格納する `WebGLBuffer` を作成、初期化する|
|`gl.viewport(x, y, width, height)`|描画領域のビューポートを指定する|
|`gl.shaderSource(shader, source)`|`WebGLShader` のソースコードを設定する|
|`gl.compileShader(shader)`|GLSLシェーダーをバイナリへコンパイルする|
|`gl.attachShader(program, shader)`|`WebGLShader` を `WebGLProgram` にアタッチする|
|`gl.linkProgram(program)`|`WebGLProgram` に接続された頂点シェーダーとフラグメントシェーダーをリンクする|
|`gl.useProgram(program)`|指定された `WebGLProgram` を現在の描画ステートの一部として設定する|
|`gl.getAttribLocation(program, name)`|指定された `WebGLProgram` 内の属性の場所を返す|
|`gl.vertexAttribPointer(index, size, type, normalized, stride, offset)`|現在 `gl.ARRAY_BUFFER` に結合されているバッファーを、現在バインドされているVBOの一般的な頂点属性に関連付ける|
|`gl.enableVertexAttribArray(index)`|指定された `index` の頂点情報を有効にする|
|`gl.drawElements(mode, count, type, offset)`|配列データのプリミティブ（`POINTS`, `LINES`, `TRIANGLES` などの型）を描画する|
|`gl.createVertexArray()`|VAOインスタンスを作成する|
|`gl.bindVertexArray(vertexArray)`|VAOインスタンスをバッファにバインドする|
|`gl.getParameter(pname)`|`pname` に指定したパラメータの値を返す|
|`gl.getBufferParameter(target, pname)`|`target` に渡したバッファの情報を返す|
|`gl.isBuffer(buffer)`|引数の値が `WebGLBuffer` かどうかの `boolean` を返す|

## 定数

|定数名|用途|
|---|---|
|`gl.ARRAY_BUFFER`|頂点データ|
|`gl.ELEMENT_ARRAY_BUFFER`|インデックスデータ|
|`gl.COLOR_BUFFER_BIT`|3DCGの描画領域であるカラーバッファを示す|
|`gl.STATIC_DRAW`|`gl.bufferData()`に渡す、データストアの用途。バッファーの内容は何度か使用されてあまり変更されない。バッファーへ書き込めるが、読み出せない。|
|`gl.DYNAMIC_DRAW`|`gl.bufferData()`に渡す、データストアの用途。バッファーの内容はよく使用されて何度か変更される。バッファーへ書き込めるが、読み出せない。|
|`gl.STREAM_DRAW`|`gl.bufferData()`に渡す、データストアの用途。バッファーの内容はよく使用されよく変更される。バッファーへ書き込めるが、読み出せない。|


## 参考リンク

- [WebGLRenderingContext - Web API | MDN](https://developer.mozilla.org/ja/docs/Web/API/WebGLRenderingContext/)
