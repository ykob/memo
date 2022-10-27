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
| `I.seeElement(element)` | `element` の要素が存在しているかどうかを確認する。<br>`element` は CSS のセレクタ、もしくは XPath の形式で記述する。 |
| `I.click(label, element?)` | `label` で指定した要素をクリックする。<br>`label` の文字列は特定の属性値を指すが、HTML要素の種類によって参照する属性が異なる。 `button` の場合は `value` や `name` が、 `img` の場合は `alt` が対象となる。 CSS のセレクタ、もしくは XPath の形式で記述することもできる。 |