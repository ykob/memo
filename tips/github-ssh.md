# GitHub SSH接続 覚書

## ポート番号 `22` による接続ができない。

GitHub
wifi環境によってはポート番号 `22` で `git@github.com` にアクセスできないことがある。  
その場合、接続時に以下エラーが返る。

```
ssh: connect to host github.com port 22: Operation timed out
```

打開策として、ポート番号 `443` で `git@github.com` に接続するという方法がある。  
`~/.ssh/config` に以下を記述する。
（ファイル自体が存在しない場合は新規作成する。）

```
Host github.com
   User git
   Hostname github.com
   Port 443
   IdentityFile ~/.ssh/id_rsa
```

`IdentityFile` は省略可能で、省略した場合は既定のファイル名にアクセスしようとする。  
使用する鍵ファイルの名前が規定から外れる命名になっている場合は `IdentityFile` を追加して鍵ファイルの参照先を記述する必要がある。

詳細な内容は以下デバッグコマンドを実行することで確認できる。

```
ssh -vT git@github.com
```

参照しようとしているファイルは `identity file` から始まる行で内容を確認できる。  
例えば以下のような内容が返る。

```
debug1: identity file C:\\Users\\username/.ssh/id_rsa type 0
debug1: identity file C:\\Users\\username/.ssh/id_rsa-cert type -1
debug1: identity file C:\\Users\\username/.ssh/id_dsa type -1
debug1: identity file C:\\Users\\username/.ssh/id_dsa-cert type -1
debug1: identity file C:\\Users\\username/.ssh/id_ecdsa type -1
debug1: identity file C:\\Users\\username/.ssh/id_ecdsa-cert type -1
debug1: identity file C:\\Users\\username/.ssh/id_ed25519 type -1
debug1: identity file C:\\Users\\username/.ssh/id_ed25519-cert type -1
debug1: identity file C:\\Users\\username/.ssh/id_xmss type -1
debug1: identity file C:\\Users\\username/.ssh/id_xmss-cert type -1
```

次に、以下コマンドを試す。

```
ssh -T -p 443 git@ssh.github.com
```

以下のようにメッセージが返れば接続は成功している。

```
Hi [username]! You've successfully authenticated, but GitHub does not provide shell access.
```

### SourceTree

SourceTree付属のPuTTYgenで生成した公開鍵だとポート番号 `443` による接続に失敗することがあった。  
(詳細な原因は追い切れていないので、可能な方法もあるかもしれない。)

その場合、以下コマンドによってSSH接続可能な鍵ファイルを生成することができた。

```
ssh-keygen -t rsa
```

## 参考リンク

- [SSH について - GitHub Docs](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh/about-ssh)