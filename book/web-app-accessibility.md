# Web アプリケーションアクセシビリティ

## 参考

- [Web アプリケーションアクセシビリティ ――今日から始める現場からの改善：書籍案内｜技術評論社](https://gihyo.jp/book/2023/978-4-297-13366-5)

## 第 1 章 Web アクセシビリティとは

- アクセシビリティ：利用可能な状況の幅広さ
- ユーザビリティ：特定のユーザーが目標を達成する度合い

アクセシビリティとユーザビリティは縦軸/横軸の相関関係で説明できる。

### アクセシビリティのレイヤー

- 改変 hackability
- 共有 shareabiolity
- 使用 usability
- 携帯 portability
- 発見 findability
- 堅牢 nobustness

そもそも Web にコンテンツがあること自体がアクセシブルである。

### 障害

- 弱視・ロービジョン
- 色覚異常・色弱
- 全盲
- 上肢障害
- 聴覚障害
- 認知・学習障害

上記障害は加齢によって誰にでも発言しうる。

### WCAG (Web Content Accessibility Guidelines)

W3C が発行する Web アクセシビリティに関するガイドライン。  
2022 年 12 月時点では WCAG 2.1 が最新版で、WCAG 2.2 が勧告候補となっている。

WCAG は以下の 4 つの原則に基づいている。

- 知覚可能
- 操作可能
- 理解可能
- 堅牢

#### 3 つの適合レベル

WCAG にはレベル A、AA、AAA の 3 つの適合レベルがある。

レベル A は最低ラインであり、この基準を満たすことで、ユーザーが支援技術を駆使して Web コンテンツにアクセス可能にものが多くを占める。  
レベル AA は、この基準を満たすことで、ユーザーが支援技術なしでも Web コンテンツにアクセスできるようになるものが多くを占める。  
レベル AAA はレベル A、AA をより強化し発展させたものだが、コンテンツや機能によっては例外なく達成することが難しいことがある。

### アクセシビリティ適合のメリット

- アクセスできない人を減らせる
  - 国民のおよそ 7.6%が何らかの障害を有している
- マシンリーダビリティを向上させ、通常のブラウジング以外の利用方法による情報の提供を可能にする
- ユーザビリティも向上させられる
- 権利と法律を守ることができる
  - 不当な差別的取扱いの禁止
  - 合理的配慮の提供

## 第 2 章 Web アクセシビリティの基礎

- 読み上げられないし、操作できないボタン
  - `alt` 属性や `title` 要素を持たないアイコンのみのボタン
- 選択したかわからないチェックボックス
  - `input` 要素を使わず、`div` 要素や `span` 要素を CSS で装飾してチェックボックスのように見せているもの

### HTML のセマンティクスと、それを補完する WAI-ARIA

HTML には元々定義されているセマンティクスがある。（ネイティブセマンティスクス）
HTML の要素を適切に使用することでセマンティクスを実装できるが、不足がある。（例：タブ UI など）
これらのセマンティクスを補完するのが WAI-ARIA という仕様。

WAI-ARIA を用いてタブ UI を表現しようとすると、以下のようになる。

```html
<div role="tablist">
  <button role="tab" aria-selected="true" id="example-tab1">タブ1</button>
  <button role="tab" aria-selected="false" id="example-tab2">タブ2</button>
</div>
<div role="tabpanel" aria-labelledby="example-tab1" aria-hidden="false">
  ...
</div>
<div role="tabpanel" aria-labelledby="example-tab2" aria-hidden="true">...</div>
```

- `role` ：オブジェクトの役割。代入された値によって支援技術はユーザーに適切な操作を提示したり、サポートしたりできる。
- `aria-selected` ：`boolean` 型。`true` ならその要素が選択されていることを示す。
- `aria-labelledby` ：`id` 型。`id` 属性の値を持つ要素が、その要素のラベルであることを示す。
- `aria-hidden` ：`boolean` 型。`true` ならその要素が隠されていることを示す。

「読み上げられないし、操作できないボタン」や「選択したかわからないチェックボックス」を WAI-ARIA で補完することもできるが、できるだけネイティブセマンティクスを使うべき。  
WAI-ARIA はあくまでも HTML のセマンティクスのみを補完するものであり、振る舞いまでは再現してくれないため。  
WAI-ARIA を理解できるユーザーエージェントや支援技術が限られていることも、ネイティブセマンティクスを使うべき理由の一つ。

### AOM (Accessibility Object Model)

AOM はアプリケーションのウィンドウをルートノードとしたツリー構造で生成され、そこには個々のインタラクティブなインターフェースやテキストノードが含まれる。  
支援技術は AOM を読み取り操作することで、アプリケーションを操作する。

AOM は DOM と CSS オブジェクトモデルの情報を組み合わせて生成される。  
HTML からは名前や役割、状態などのセマンティクスを取得する。CSS も AOM に影響を及ぼす。  
(`display: none` が指定されている要素は支援技術からも隠す必要があるなど)

AOM は様々なプロパティを持つ。以下はその一例。

- Name: オブジェクトのアクセシブルな名前。
- Role: オブジェクトの役割で、それによって支援技術はユーザーに適した操作やサポートを提示できる。
- Description: オブジェクトの説明で、Name よりも詳細な情報を扱う。
- Expanded: オブジェクトが展開されているかどうかの状態を表す。

### キーボード操作

目や手などに障害を持つユーザーは、主にスクリーンリーダーなどのキーボード操作をメインとする支援技術を用いて Web を操作する。  
UI の操作がキーボードだけで完結できれば、キーボードとマウスを持ち替えることなく効率的かつ正確に入力ができる。

インタラクティブコンテンツに属する HTML の要素は基本的にキーボードで操作が可能だが、そうでない要素は（JavaScript でイベントを付与できたとしても）操作できない。  
WAI-ARIA を用いて`div`要素に`role="button"`を付与しても、`div`要素はフォーカスを受け取ることはできない。  
また、インタラクティブ要素を使用していても、CSS で`pointer-events: none`や`display: none`を指定していると、その要素はフォーカスを受け取ることはできない。

キーボードで操作するためにはフォーカスがどの要素にあたっているかがわかることが重要になる。  
CSS で `outline: none;` を指定するなどしてフォーカスのスタイルを消してしまうと、フォーカスがどの要素にあるかわからなくなってしまう。

### `tabindex` 属性

インタラクティブでない要素がフォーカスを受け取られるようにするには、`tabindex` 属性を使用する。

```html
<div tabindex="0">...</div>
```

`tabindex` 属性は`-1`や`0`、`1`などの値を持つことができる。  
`-1`はフォーカスを受け取れるが、タブキーでフォーカスが移動しない。  
`0`はタブキーでフォーカスが移動する。  
`1`はタブキーでフォーカスが移動する。ただし、`0`よりも優先される。

非インタラクティブ要素はフォーカスを受け取られるだけでは不十分な場合がほとんど。  
`button` のようなインタラクティブ要素は enter キーが押されることでクリックイベントを発火するが、非インタラクティブ要素にはそのような振る舞いがない。
非インタラクティブ要素で同様の振る舞いを行わせるには、keyup イベントを追加するなどの配慮が必要になるが、インタラクティブ要素を使っていれば不要な実装であることには違いない。

### `:focus` 擬似クラス、 `:focus-within` 擬似クラス

`:hover` 擬似クラスに付与する装飾をキーボード操作に対しても適用するためには、`:focus` 擬似クラスや `:focus-within` 擬似クラスを使用する。

```css
/* フォーカスされている要素に対して適用 */
:focus {
  outline: 2px solid blue;
}

/* フォーカスされている要素の子孫要素に対して適用 */
:focus-within {
  outline: 2px solid blue;
}
```

`:focus` 擬似クラスはフォーカスされている要素に対して適用される。  
`:focus-within` 擬似クラスはフォーカスされている要素の子孫要素に対して適用される。

### 非テキストコンテンツのマシンリーダビリティ

マシンリーダビリティを担保するのはテキストデータとセマンティクス。  
コンテンツがマシンリーダブルであることで、ユーザーは様々な方法でコンテンツにアクセスできるようになる。  
画像、アイコン、グラフなどテキストデータではない要素にも、代替テキストを付与することでマシンリーダビリティを担保できる。

以下は悪い例

- 代替テキストがない`img`要素、`svg`要素
- アクセシブルな名前がない`button`要素、`a`要素、`input`要素
- 装飾のための要素に`title`要素が設定されている

以下は改善策の例

- `img`要素に`alt`属性、`svg`要素に`title`要素や`aria-label`属性を用いる
- ユーザーが画像をアップロードできる場合は、画像の説明を入力するためのテキストフィールドを用意する
- グラフやチャートに対して代替となるコンテンツ（グラフのもとになるテーブルなど）を提供する

#### Visually Hidden

AOM に影響のないプロパティのみを用いて、視覚的には表示されないがマシンリーダビリティは確保するテクニック。  
`display: none;`や`visibility: hidden;`を用いると、AOM にも影響が出てしまうため、使用できない。

```css
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

### コンテンツ構造のマシンリーダビリティ

見出しを見出しとしてマークアップする。  
スクリーンリーダーには見出しジャンプ機能が備わっているため、見出しを適切にマークアップすることで、ユーザーは本文全体を順番に読み進めるだけでなく目的の箇所にすばやく移動することができるようになる。

```html
<h1>見出し</h1>
```

`label`要素の`for`属性、`input`要素の`id`属性を用いて、マークアップで情報を関連付ける。  
ラベルと入力フィールドのマークアップの順番が連続していなくても情報の関連付けができる。

```html
<label for="input">名前</label>
...
<input id="input" type="text" />
```

## 第 3 章 フォームの改善

### ラベルと説明

- `placeholder` 属性をラベルの代わりに使用しない。
- ラベルと説明はまとめて `label` 要素でマークアップする。
- `label` 要素が使用できない場合は `aria-labelledby` 属性と `aria-describedby` 属性を使用する。

```html
<span id="username">名前</span>
<span id="description">説明</span>
<input type="text" aria-labelledby="username" aria-describedby="description" />
```

- `title` 属性や `aria-label` 属性を使用して不可視のラベルを付ける。
  - 使用はラベルがなくても入力すべき値が明らかな場合に限定すべき。
- `fieldset` 要素と `legend` 要素を使用してグループ化する。
  - `legend` 要素は `fieldset` 要素の最初の子要素として配置する。

```html
<fieldset>
  <legend>グループ名</legend>
  <label><input type="radio" name="group" /> 選択肢 1</label>
  <label><input type="radio" name="group" /> 選択肢 2</label>
  <label><input type="radio" name="group" /> 選択肢 3</label>
</fieldset>
```

- `role="group"` 属性を使用してグループ化する。

```html
<div id="group-name">グループ名</div>
<div role="group" aria-labelledby="group-name">
  <label><input type="radio" name="group" /> 選択肢 1</label>
  <label><input type="radio" name="group" /> 選択肢 2</label>
  <label><input type="radio" name="group" /> 選択肢 3</label>
</div>
```

- 入力必須項目には `required` 属性や `aria-required="true"` 属性を使用する。

### 入力の支援

入力の支援として大きく 3 つの方法が挙げられる。

1. 入力を減らす。必要性の薄いフォームコントロールを削減する。
2. 入力を自動補完する。
3. 入力しやすいフォームコントロールを設ける。

`autocomplete` 属性を使用することで、ブラウザの自動補完をサポートしやすくなる。  
`autocomplete` 属性には `name`, `email`, `url` などの期待される入力補完に応じた値を指定することができる。  
入力できる値は [HTML Living Standard](https://momdo.github.io/html/form-control-infrastructure.html#autofilling-form-controls:-the-autocomplete-attribute) に記載されている。
`autocomplete` 属性とともに `name` 属性にも適切な値を設定する必要がある。

```html
<form>
  <label>
    クレジットカード名義
    <input type="text" name="ccname" autocomplete="cc-name" />
  </label>
    <label>
    クレジットカード番号
    <input type="text" name="cardnumber" autocomplete="cc-number" />
  </label>
  <label>
    CVC
    <input type="text" name="cvc" autocomplete="cc-csc" />
  </label>
</form>
```

`input` 要素の `type` 属性には、入力値に応じた適切な値を指定する。  
それによってもたらせるメリットには以下のようなものがある。

- フォームコントロールがより入力しやすいUIに変化する。
- キーボードや支援技術による操作がサポートされる。
- モバイルデバイスでの入力時に適切なソフトウェアキーボードが選択される。

ソフトウェアキーボードの種類を指定するには、 `inputmode` 属性と `pattern` 属性を使う方法もある。

### 制約の検証とエラー

入力の制約とエラーの表示方法について、以下のような方法が挙げられる。

1. HTML標準の制約検証を利用する。( `max`、`min`、`step`、`pattern` など )
2. 独自のエラー（1を採用できない場合）をわかりやすくデザインする。
3. 独自のエラーをマークアップする。

独自のエラーをフォームコントロールに表示する際は、適切なマークアップを行い、エラーメッセージをフォームコントロールに関連付けられるようにする。

- エラーメッセージを `label` 要素に含める。
- エラーメッセージにIDを割り振り、フォームコントロールの `aria-describedby` 属性にそのIDを指定する。

```html
<label>
  メールアドレス
  <input type="email" required aria-describedby="email-error" />
</label>
<div id="email-error">メールアドレスを入力してください。</div>
```

グループにも同様の方法でエラーメッセージを関連付けることができる。

```html
<fieldset>
  <legend>
    <span>グループ名</span>
    <span>エラー：項目が選択されていません。</span>
  </legend>
  <label><input type="radio" name="group" /> 選択肢 1</label>
  <label><input type="radio" name="group" /> 選択肢 2</label>
  <label><input type="radio" name="group" /> 選択肢 3</label>
</fieldset>
```

```html
<div id="group-name">グループ名</div>
<div id="group-error">エラー：項目が選択されていません。</div>
<div role="group" aria-labelledby="group-name" aria-describedby="group-error">
  <label><input type="radio" name="group" /> 選択肢 1</label>
  <label><input type="radio" name="group" /> 選択肢 2</label>
  <label><input type="radio" name="group" /> 選択肢 3</label>
</div>
```

`aria-live` 属性を使用することで、エラーメッセージを動的に変更したときに、エラーメッセージをスクリーンリーダーで読み上げることができる。

```html
<span area-live="assertive">エラーメッセージ</span>
```

### カスタムコンポーネント

カスタムコンポーネントとは、HTMLの標準的なフォームコントロールだけでは表現できないUIを実現するために、複数のHTML要素とJavaScriptを組み合わせて作成するコンポーネントを指す。  
カスタムコンポーネントをマシンリーダブルにするためにはWAI-ARIAに対する深い理解が必要になる。

カスタムコンポーネントのマシンリーダビリティを阻害するよくある事例として以下のようなものが挙げられる。

- カスタムコンポーネントの必要性を吟味していない
- 既存のカスタムコンポーネントサンプルを参考にしていない
- キーボード操作が適切に設計されていない
- WAS-ARIAの属性が適切に設定されていない
- WAI-ARIAの仕様に従ったプロパティ・ステートが設定されていない
- 支援技術で検証していない

アクセシビリティの高いカスタムコンポーネントの実現には、デザイン・実装ともに高いコストがかかる。  
カスタムコンポーネントの利用がコストパフォーマンスに見合うものかどうかをまず検討する必要がある。

また、参考にできるカスタムコンポーネントサンプルの一例として、[ARIA Authoring Practices Guide(APG)](https://www.w3.org/WAI/ARIA/apg/) がある。  
APGではカスタムコンポーネントごとに主に以下の内容が解説されている。

- 推奨されるWAI-ARIAのロール・ステート・プロパティ
- 推奨されるキーボード操作
- サンプルコード

キーボード操作の設計では主に以下の点に注意する。

- 標準的なキーボード操作のやり方に従う
- フォーカスが破綻しないようにする
  - フォーカスリセット：フォーカスがbody要素に移動してやり直しになってしまう
  - フォーカストラップ：フォーカスが一定の要素に閉じ込められてしまう
  - 無限スクロール：対象の要素よりあとにある要素にフォーカスできなくなる
- 不適切な要素にフォーカスできないようにする

