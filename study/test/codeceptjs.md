# CodeceptJS

End 2 End (E2E) Testing フレームワーク。

- [CodeceptJS](https://codecept.io/)

## E2E テストとは

E2Eテストでは、アプリケーションをエンドユーザーが操作するのと同じようにテストする。  
ユーザーインターフェーステスト (UIテスト) と呼ばれることもある。  
End to End とは、UI層から、アプリケーション層、データ層までのすべてを動かすことを意味している。  
E2Eテストにおいては、ユーザーとしてUI上でどのように振る舞うのか（どのページに遷移し、表示されるどの要素をどのように操作するのか）がそのままテストの手順となる。

## よく使用する API

| API | 用途 |
| --- | --- |
| `I.amOnPage(url)` | `url` のページに遷移する。 |
| `I.see(text, context?)` | `text` の文字列が存在しているかどうかを確認する。<br>`context` の要素で検索対象をスコープできる。 |
| `I.seeElement(context)` | `context` の要素が存在しているかどうかを確認する。<br>`context` は CSS のセレクタ、もしくは XPath の形式で記述する。 |
| `I.click(locator, context?)` | `locator` で指定した要素をクリックする。<br>`locator` の文字列は特定の属性値を指すが、HTML要素の種類によって参照する属性が異なる。 `button` の場合は `value` や `name` が、 `img` の場合は `alt` が対象となる。 CSS のセレクタ、もしくは XPath の形式で記述することもできる。 |
| `I.fillField(field, value)` | `field` で指定した要素に `value` を入力する。`field` は要素の `name` または `label` の属性値を参照する。CSS のセレクタ、もしくは XPath の形式で記述することもできる。 |
