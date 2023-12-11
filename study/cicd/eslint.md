# ESLint

ESLintは、JavaScriptやTypeScriptのコードで一貫性を保ち、バグを防ぎ、コーディング規約に準拠するための静的解析ツール。  
https://eslint.org/

- あらかじめ設定したLintルールに従って、自動的にコードの一貫性を維持することができる。
- Lintルールは`.eslintrc`ファイルに記述する。
- Lintルールはゼロから手書きで定義することも出来るが、`.eslintrc`の可読性を維持するためには別途プラグインを用いて定義するほうがよい。

## Plugins

| プラグイン名 | 用途 |
| --- | --- |
| [eslint-plugin-import](https://github.com/import-js/eslint-plugin-import) | ES2015+のimport/export構文に関するリンティングのルールを提供する |
| [eslint-plugin-unicorn](https://github.com/sindresorhus/eslint-plugin-unicorn) | Lintルールのプリセット集 |

### [eslint-plugin-import](https://github.com/import-js/eslint-plugin-import)

ES2015+のimport/export構文に関するリンティングのルールを提供する。

### [eslint-plugin-unicorn](https://github.com/sindresorhus/eslint-plugin-unicorn)

Lintルールのプリセット集。  
使うと便利そうなルールは以下。

- `unicorn/better-regex`: よりよい正規表現を使うようにする。
- `unicorn/filename-case`: ファイルの命名ルールを定義できる。
- `unicorn/no-for-loop`: 添字forループの使用を制限する。
- `unicorn/prefer-spread`: 配列の操作にはスプレッド演算子の使用を優先する。
- `unicorn/prefer-switch`: 複数の`else-if`文よりも`switch`文を優先する。
- `unicorn/prefer-ternary`: 単純な`if-else`文よりも参考演算子を優先する。