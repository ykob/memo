# ウェブページのパフォーマンス計測

## ツールの候補

| ツール | 概要 |
| --- | --- |
| [PageSpeed Insights](https://pagespeed.web.dev/) | ウェブページの読み込み時間に特化した診断ツール。Googleが提供する。 |
| [Lighthouse](https://chromewebstore.google.com/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk) | Googleが提供する総合診断ツール。パフォーマンス計測以外にも利用できる。 |
| [New Relic APM](https://docs.newrelic.com/jp/docs/apm/new-relic-apm/getting-started/introduction-apm/) | NewRelicが提供する機能の一つ。 |

## PageSpeed Insights

ウェブページの読み込み時間に特化した診断ツール。Googleが提供する。

## Lighthouse

Googleが提供する総合診断ツール。  
SEO、アクセシビリティ、セキュリティなど、パフォーマンス計測以外にも利用できる。  
ChromeのDevToolsからも利用できる。

### LighthouseをCIで定期実行したい

[Lighthouse CI Action](https://github.com/marketplace/actions/lighthouse-ci-action)がマーケットプレイスで提供されている。  

[lighthouse-ci](https://github.com/GoogleChrome/lighthouse-ci)というモジュールもある。  
GoogleChrome純正のモジュールであり、npm modulesの名称は `@lhci/cli` となっている。  
このモジュールをGitHub Actionsのworkflowsに配置するyamlでインストールして実行するようにできれば、GitHub Actions上で使用できる。

## NewRelic APM

APM(Application Performance Monitoring)

## 参考リンク

- [GoogleによるWebサイトパフォーマンス測定ツール「Lighthouse」入門 | さくらのナレッジ (sakura.ad.jp)](https://knowledge.sakura.ad.jp/21477/)
- [Lighthouse CI Action · Actions · GitHub Marketplace](https://github.com/marketplace/actions/lighthouse-ci-action)
- [GoogleChrome/lighthouse-ci: Automate running Lighthouse for every commit, viewing the changes, and preventing regressions](https://github.com/GoogleChrome/lighthouse-ci)
