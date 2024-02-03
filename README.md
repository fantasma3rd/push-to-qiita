# Qiitaの記事をGitHubリポジトリで管理する方法

Qiitaでは、Qiita CLIを使うことで、Qiitaの記事をGitHubリポジトリで管理できます。
また、Qiita CLIを使うと普段お使いのエディタで記事を執筆できます！

Qiita CLIの詳細は[Qiita CLIのREADME](https://github.com/increments/qiita-cli)をご確認ください。

https://github.com/increments/qiita-cli


## Qiita CLIを使って記事をGitHubリポジトリで管理する方法

:::note warn
Qiita CLIを使うにはNode.jsが必要です。
Node.jsのサポートバージョンは[Qiita CLIのREADME](https://github.com/increments/qiita-cli)をご確認ください。
:::

### 1. リポジトリを作成する
GitHubでリポジトリを作成します。
リポジトリの各種設定は、お好きな設定でOKです。
詳しいリポジトリの設定方法は、[GitHub Docs](https://docs.github.com/ja/get-started/quickstart/create-a-repo#create-a-repository)をご確認ください。

![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/88/cddf8ee0-91ae-746f-b279-1db3e45af56a.png)

### 2. QiitaのトークンをGitHub ActionsのSecretsに設定する
#### 2-1. Qiitaの個人用アクセストークンを発行する
Qiitaの[設定](https://qiita.com/settings/tokens/new?read_qiita=1&write_qiita=1&description=qiita-cli) にある`アプリケーション > 新しくトークンを発行する` から個人用アクセストークンを発行します。
すでに発行されているトークンを使う場合は不要です。

トークンを発行する際は、
アクセストークンの説明には、好きなテキストを設定し、
**スコープは `read_qiita` と `write_qiita`にチェックを入れてください。**

![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/88/4bded249-6676-f329-5651-93bfc6d4f743.png)


　
アクセストークンを発行したら、以下の画像の赤枠部分のある文字列をコピーしてください。

発行したアクセストークンは、`Qiita CLIのログイン`と`GitHub ActionsのSecrets`の設定で使います。
そのため、発行したアクセストークンは、忘れずに保存しておいてください。

![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/88/bfc267fd-1af4-89a0-2c7c-dc11079d94c2.png)

#### 2-2. GitHub ActionsのSecretsを設定する
作成したリポジトリの設定からGitHub ActionsのSecretsを設定します。
作成したリポジトリの `Settings > Secrets and variables > Actions > New repository secret` から設定します。

詳しいSecretsの設定方法は、[GitHub Docs](https://docs.github.com/ja/actions/security-guides/encrypted-secrets)をご確認ください。

GitHub ActionsのSecretsを設定する際は、
**Nameには、`QIITA_TOKEN` を設定し**、
**Secretには、発行したQiitaのアクセストークンを設定してください。**

![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/88/4da2c3b6-61ec-744d-46a2-6bd664865dc1.png)

### 3. Qiita CLIをセットアップする
Qiita CLIのセットアップをします。
セットアップをすると、Qiitaの記事をGitHubリポジトリで管理するためのGitHub Actionsのワークフローファイルが生成されます。

セットアップ方法の詳細は[Qiita CLIのREADME](https://github.com/increments/qiita-cli)をご確認ください。

https://github.com/increments/qiita-cli

### 4. Qiita CLIをリポジトリにプッシュする
Qiita CLIを作成したリポジトリにプッシュします。

```
git remote add origin [リポジトリのURL]
git push -u origin main
```

作成したリポジトリにプッシュをすると、自動でGitHub Actionsが実行され、Qiitaに記事が投稿・更新されます。

![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/88/948a2dde-a226-9ed0-19b7-26108b6b30a4.png)


## 記事の内容を更新する

内容を変更するには`.md`ファイルを編集し、再度プッシュすると更新できます。
Qiita上との差分がある`.md`ファイルのみQiitaに反映されます。

## 記事の削除について

Qiita CLIを使った記事の管理では、安全のため記事の削除はできません。
Qiita上から記事の削除をお願いします。

## Qiita CLIの改善について

Qiita CLIへのご意見・ご要望がございましたら、[Qiita Discussions](https://github.com/increments/qiita-discussions/discussions)へお願いします。

https://github.com/increments/qiita-discussions/discussions

また、Qiita CLIはOSSとして公開されていますので、[このリポジトリ](https://github.com/increments/qiita-cli)にPull Requestを送っていただくことも可能です。

https://github.com/increments/qiita-cli

（引用: https://qiita.com/Qiita/items/32c79014509987541130 ）