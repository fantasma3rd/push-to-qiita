---
title: Minecraft サーバーの立て方備忘録@Mac篇
tags: "Spigot" "インフラ" "Minecraft" "Mac"
author: fantasma3rd
slide: false
---

## はじめに

この記事は、Minecraft サーバーを Spigot を用いて構築する方法を纏めたものです。
インターネット上を調べても、Mac 上で Minecraft サーバーを構築する手段を一意に纏めた記事がなかなか見つからなかったので、この記事の内容としては、あくまで個人的な備忘録として作成しました。その為、ネットワークの知識やスクリプトについての知識についてはあまり深掘りしていません。読者の方のお役に立ちましたら幸いです。

## 目次

- [サーバー構築の大まかな流れ](#サーバー構築の大まかな流れ)

- [サーバー構築 Mac篇](#サーバー構築-mac篇)

  - [Java のダウンロード](#java-のダウンロード)

  - [サーバーラッパーのビルド](#サーバーラッパーのビルド)

    - [Spigot Builder のダウンロード](#spigot-builder-のダウンロード)

    - [Spigot のビルド](#spigot-のビルド)

  - [Spigot サーバーのプレビルド](#spigot-サーバーのプレビルド)

    - [start.command ファイルの作成](#startcommand-ファイルの作成)

    - [start.command の実行](#startcommand-の実行)

  - [Spigot の性能要件を設定](#spigot-の性能要件を設定)

    - [eula.txt の設定](#eulatxt-の設定)

    - [server.properties の設定](#serverporperties-の設定)

  - [Port の開放](#port-の開放)

    - [アプリケーションのダウンロード](#アプリケーションのダウンロード)

    - [開放ポートの設定](#開放ポートの設定)

  - [サーバーのビルド](#サーバーのビルド)

  - [注意](#注意)

## サーバー構築の大まかな流れ

実際のサーバー構築では、マシンの OS や其々の環境によって方法が異なりますが、この章では先に全てのマシンでの構築における大まかな流れを纏めておきます。

1. Java のダウンロード
2. サーバーラッパーをビルドする
3. サーバーのプレビルド
4. サーバーの性能要件を設定
5. Port の開放
6. サーバーのビルド

## サーバー構築 Mac篇

ここで紹介しているサーバーの構築方法は、**Mac 上での実行**を想定しています。

### Java のダウンロード

Minecraft サーバーを起動する為には、Java の導入が必須です。
また、Minecraft サーバーのバージョンごとに、必要とされる Java のバージョンも異なります。希望するバージョンに適当な Java をダウンロードしましょう。

| Minecraft Version | Java Version |
| :--- | :--- |
| - 1.7 | Java 7 |
| 1.7.10 - 1.16.5 | Java 8 |
| 1.17 - 1.17.1 | Java 16 |
| 1.18 - | Java 17 |

### サーバーラッパーのビルド

Minecraft サーバーを構築するにあたって、先ずはサーバーラッパーをビルドする必要があります。
Minecraft のサーバーラッパーには様々な種類があり（2024/02/02 現在）、其々細かい機能や用途、性能などが異なる為、各々の目的にあったものを選びます。
今回は一般的に使用されている Spigot を利用します（尚、近年では Spigot をチューンして軽量化した Paper の使用が盛ん）。

#### Spigot Builder のダウンロード

Spigot は、自分の立てるてたいサーバーのバージョンに合わせて必要なファイルが異なります。
その為、先ずは Spigot のファイルをビルドするツールを入手します。
ビルドツールは[こちら](https://hub.spigotmc.org/jenkins/job/BuildTools/)からダウンロードできます。
上記のサイトへ飛んで、`最新成功ビルドの成果物 / BuildTools.jar`をダウンロードします。
ダウンロードが成功したら、Spigot をビルドする用のディレクトリへ`BuildTools.jar`を移動します。

#### Spigot のビルド

ターミナルを起動し、前節で`BuildTools.jar`を移動したディレクトリへ移動します。
カレントディレクトリで、`java -jar BuildTools.jar --rev ${VERSION}`を実行します。`${VERSION}`には、自分の立てたいサーバーのバージョンを入力してください。
実行するとビルド処理が始まり、Spigot ファイルが生成されます。処理の完了には時間がかかるので、コーヒーでも飲みつつ気長に待ちましょう。

### Spigot サーバーのプレビルド

前節の処理が正常に完了すると、`BuildTools.jar`と同様のディレクトリ下に、`spigot-${VERSION}.jar`というファイルが生成されます。
このファイルが、今後使用するサーバーラッパーとなります。
生成された`spigot-${VERSION}.jar`を、サーバー構築用のディレクトリへ移動します。

#### start.command ファイルの作成

次に、サーバーを構築する為に実行するコマンドファイルを作成します。
`spigot-${VERSION}.jar`を移動させたディレクトリ下に、`start.command`を作成します。`start.command`ファイルの中身は以下の通りに記述します。

```bash
#!/bin/bash

cd /Users/${YOUR_USERNAME}/${SERVER_DIRECTORY}
export JAVA_HOME=`/usr/libexec/java_home -v ${JAVA_VERSION}`
exec java -Xms${MIN_MEMORY}G -Xmx${MAX_MEMORY}G -jar spigot-${VERSION}.jar nogui
```

このコマンドでは、以下のことをしています。

- `#!/bin/bash`で、コンソールにbashで実行することを明示
- `cd /Users/${YOUR_USERNAME}/${SERVER_DIRECTORY}`で、作業ディレクトリをサーバ構築用ディレクトリへ移動
- ``` export JAVA_HOME=`/usr/libexec/java_home -v ${JAVA_VERSION}` ```で、作業ディレクトリで使用する Java のバージョンを指定
- `exec java -Xms${MIN_MEMORY}G -Xmx${MAX_MEMORY}G -jar spigot-${VERSION}.jar nogui`で、指定した条件に倣いサーバーを起動
  - `-Xms${MIN_MEMORY}G -Xmx${MAX_MEMORY}G`で、サーバーの実行に使用する最小メモリと最大メモリを指定
  - `-jar spigot-${VERSION}.jar`で、起動する jar ファイルを指定
  - `nogui`で、起動時に GUI の表示をしないように設定

#### start.command の実行

`start.command`の作成が終わったら、ターミナルで`start.command`を実行するか、`start.command`ファイルをダブルクリックして実行してみましょう。
正常に動作すれば、サーバーのプレビルドが始まります。

### Spigot の性能要件を設定

前節での`start.command`の実行が正常に完了すると、サーバー構築用ディレクトリ下に、`eula.txt`、`logs/`、`server.properties`の3つが生成されます。
サーバーを構築し、動作させる為には、これらのファイルを正しく設定する必要があります。

#### eula.txt の設定

先ず、`eula.txt`を設定します。
EULA とは、ソフトウェア開発側とユーザー側との間に交わされる契約で、ユーザーがソフトウェアを利用する上での免責事項等が記載されています。
Spigot を利用する為には、この[EULA](https://www.minecraft.net/eula)を理解し、承諾する必要があります。
[EULA](https://www.minecraft.net/eula)を読んだ上で、それに同意する場合は`eula.txt`ファイル内の`eula=false`という記述を、`eula=true`に変更してください。

> [!WARNING]
>
> EULA に同意しないと、サーバーを起動することはできません。

#### server.porperties の設定

次に、`server.properties`を設定します。
このファイルでは、サーバー自体の設定や、サーバーが実行する Minecraft ワールドに対する設定などが行えます。
必ずしも設定を変更する必要はありませんが、サーバー運営を快適に行う上で知っておくべき内容なので、各項目を纏めておきます。

| Key | Type | Default | Detail |
| :--- | :--- | :--- | :--- |
| allow-flight | Boolean | false | サバイバルモードで飛行を可能にするか |
| allow-nether | Boolean | true | ネザーへの移動を可能にするか |
| broadcast-console-to-ops | Boolean | true | コンソールでのコマンドの実行をゲーム内のオンラインの管理者に表示するか |
| broadcast-rcon-to-ops | Boolean | true | rcon でのコマンドの実行をゲーム内のオンラインの管理者に表示するか |
| difficulty | String | easy | ゲーム内の難易度 |
| enable-command-block | Boolean | true | コマンドブロックを有効にするか |
| enable-jmx-monitoring | Boolean | false | JMXによるモニターを有効化するか |
| enable-query | Boolean | false | サーバー情報を取得できるプロトコルサーバーリスナーを有効にするか |
| enable-rcon | Boolean | false | rcon を用いたコンソールへのリモートアクセスを許可するか |
| enable-status | Boolean | true | サーバーリスト上でオンラインと表示するか |
| enforce-secure-profile | Boolean | false | Mojang の公開鍵を持つユーザー以外のログインを拒否するか |
| enforce-whitelist | Boolean | false | ホワイトリスト有効時に、登録されていないプレイヤーをキックするか |
| entity-broadcast-range-percentage | Int | 100 | エンティティ情報を送信する距離 |
| force-gamemode | Boolean | false | サーバーログイン時に、プレイヤーをサーバーのデフォルトゲームモードに変更するか |
| function-permission-level | Int | 2 | ゲーム内での function の実行権限 |
| gamemode | String | survival | サーバーのデフォルトゲームモード |
| generator-settings | String | なし | ワールド生成時の設定 |
| generate-structures | Boolean | true | ワールド生成時の構造物を生成するか |
| hardcore | Boolean | false | 一度死亡するとスペクテイターモードにするハードコアモードを有効にするか |
| hide-online-players | Boolean | false | サーバーリスト上でオンラインのプレイヤーを表示するか |
| initial-disabled-packs | String | なし | ワールド生成時の有効化しないデータパック |
| initial-enabled-packs | String | なし | ワールド生成時の有効化しないリソースパック |
| level-name | String | world | ワールド生成時のワールドデータの名前 |
| level-seed | String | なし | ワールド生成時のシード値 |
| level-type | String | default | ワールド生成時のワールドタイプ |
| max-players | Int | 20 | サーバーに一度にログインできる最大人数 |
| max-tick-time | Int | 60000 | サーバーの応答が無いときの、サーバーを強制終了させるまでの時間 |
| max-world-size | Int | 29999984 | ワールド生成時のワールドのサイズ |
| motd | String | A Minecraft Server | サーバーリストに表示されるメッセージ |
| network-compression-threshold | Int | 256 | 圧縮する最低バイト数 |
| online-mode | Boolean | true | 接続ユーザーを Minecraft Account Database に確認するか |
| op-permission-level | Int | 4 | 管理者の実行権限 |
| player-idle-timeout | Int | 0 | 操作の無いプレイヤーをキックするまでの時間 |
| prevent-proxy-connections | Boolean | false | ユーザーのVPNやプロキシの利用を許可しないか |
| pvp | Boolean | true | PvP を有効にするか |
| rate-limit | Int | 0 | プレイヤーがキックされるまでに送信できるパケット数 |
| rcon.password | String | なし | rcon 接続時のパスワード |
| rcon.port | Int | 25575 | rcon 接続時のポート番号 |
| require-resource-pack | Boolean | false | サーバー接続時に、サーバーリソースパックの導入を強制するか |
| resource-pack | String | なし | サーバーリソースパックのURI |
| resource-pack-prompt | String | なし | サーバーリソースパック導入時にプレイヤーに表示されるメッセージ |
| resource-pack-sha1 | String | なし | リソースパックのハッシュ値 |
| server-ip | String | なし | サーバー接続に用いる独自ドメイン |
| server-port | Int | 25565 | サーバー接続時に用いるポート番号 |
| simulation-distance‌ | Int | 10 | エンティティを処理する距離 |
| snooper-enabled | Boolean | true | snoop データを送信するか |
| spawn-animals | Boolean | true | ワールド生成時の動物を召喚するか |
| spawn-monsters | Boolean | true | ワールド生成時のモンスターを召喚するか |
| spawn-npcs | Boolean | true | ワールド生成時の村人を召喚するか |
| spawn-protection | Int | 16 | 初期リスポーン地点の保護半径 |
| sync-chunk-writes | Boolean | true | チャンク書き込みの同期を有効にするか |
| query.port | Int | 25565 | サーバー情報を取得できるプロトコルサーバーリスナーへの接続時に用いるポート番号 |
| text-filtering-config | String | なし | ゲーム内チャットのテキストのフィルタ |
| use-native-transport | Boolean | true | Linux でのパケット送受信の最適化を有効にするか |
| view-distance | Int | 10 | プレイヤーへ送信するワールドデータのチャンク半径 |
| white-list | Boolean | false | ホワイトリストを有効にするか |

### Port の開放

最後に、サーバーを公開する為にポート開放をします。
ポート開放は、サーバーを立てている自分の LAN において、ポートと呼ばれる特定一部分に対する外部からの接続を許可し、LAN 内外での接続を可能にするものです。
Minecraft サーバーに自分以外の他のプレイヤーが接続する為には、ポート開放を行い、サーバーへの外部からの接続を許可する必要があります。

今回は Mac 上でサーバーを立てる為、Port 開放には専用のアプリケーションを使用します（コマンドやシステム環境設定から設定することもできるが、Mac の場合は色々と不便なので）。

#### アプリケーションのダウンロード

個人的に扱いやすいと思ったアプリケーションは、[Port Map](https://www.codingmonkeys.de/portmap/) です。今回はこちらを使用します。

上記のリンクからダウンロードページへ飛び、ページ中部にある`download the application`を押下します。
その後、GitHub のページに飛ばされるので、そこから Latest Release の zip ファイルをダウンロードします。

#### 開放ポートの設定

ダウンロードした zip ファイルを展開し、中に梱包されている `Port Map.app`を起動します。

![image(1).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3535699/14d9261d-b963-4b86-0b4a-33f07f336877.png)

起動後、アプリ画面左下にある`+ボタン`を押下し、[Spigot の性能要件を設定](#spigot-の性能要件を設定)の節で設定した、`server-port`の値と同じ値を Local Port の欄に入力します。
入力が完了したら、画面下部の`Add Mapping`を押下し、ポートの開放が完了します。

![image(2).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3535699/ae263814-4a57-27ad-78ec-5df004cd4ce3.png)

> [!NOTE]
>
> アプリ画面で、自分の追加したポートのステータスが緑色🟢 になっていることを確認してください。赤色🔴 又は黄色🟡 になっている場合は、ポートが正しく開放されていません。画面左の`Active`欄のチェックを入れて、開放してください。

> [!WARNING]
>
> `Active`欄にチェックを入れてもステータスが赤色🔴 又は黄色🟡 になっている場合は、アプリ側からポート開放の処理ができていません。開放するポートの番号を変更するか、ルーターやファイアウォールの設定等を確認してください。

### サーバーのビルド

以上の作業が終了したら、サーバーを起動しましょう。
サーバーは[Spigot サーバーのプレビルド](#spigot-サーバーのプレビルド)と同様に、ターミナルで`start.command`を実行するか、`start.command`ファイルをダブルクリックして起動することができます。
コンソール上に`Done!`と表示されればサーバーの構築は完了です。マルチプレイを楽しみましょう。

## 注意

- 何回もサーバーを構築していると、毎回[サーバーラッパーのビルド](#サーバーラッパーのビルド)を行うことが億劫になってきます。しかし、サーバーラッパーファイルの直接のダウンロードは、[DMCA](https://www.congress.gov/bill/105th-congress/house-bill/2281)という法律に抵触する為禁止されています。毎回必ず自分でビルドを行いましょう。

- サーバーを終了する際は、Minecraft サーバーのコンソール上で `stop` を実行して終了させましょう。<kbd>⌘Q</kbd> や終了ボタンを押しての終了は、サーバーデータが正常に保存されない可能性があります。

- サーバーを起動していない際は、開放した Port は閉じておきましょう。常時の開放は、セキュリティ上の危険が伴います。

- Minecraft サーバーの運営に当たっては、日々 Minecraft 公式や Spigot 等の更新情報を確認しておきましょう。稀に、セキュリティ上の脆弱性が報告されていることがあります（大規模なものだと、[Log4j2](https://www.spigotmc.org/threads/spigot-security-releases-%E2%80%94-1-8-8%E2%80%931-18.537204/) などがあった）。

## 終わりに

個人用として書いたものだったので、説明に至らない点があったかもしれませんがご了承ください。この文章を書き始めた当初は、私が Minecraft サーバーを構築した経験を、Linux (Debian)、Mac、Windows の全ての OS について一つの文章に記していたのですが、逆に読みにくくなってしまったので、OS ごとに別記事にすることにしました。今回は Mac 篇でしたが、また暫くしたら別 OS 篇もあげようと思います。