---
algolia:
  tags:
  - uninstall
  - uninstalling
aliases:
- /ja/guides/basic_agent_usage/deb/
- /ja/agent/basic_agent_usage/install_debian_5/
- /ja/agent/basic_agent_usage/debian
further_reading:
- link: /logs/
  tag: Documentation
  text: ログの収集
- link: /infrastructure/process/
  tag: Documentation
  text: プロセスの収集
- link: /tracing/
  tag: Documentation
  text: トレースの収集
- link: /agent/basic_agent_usage/#agent-architecture
  tag: Documentation
  text: Agent のアーキテクチャを詳しく見る
- link: /agent/configuration/network#configure-ports
  tag: Documentation
  text: インバウンドポートの構成
platform: Debian
title: Debian 用 Agent の基本的な使用方法
---

## 概要

このページでは、Debian 用 Datadog Agent の基本的な機能について説明します。Agent をまだインストールしていない場合は、[Datadog Agent インテグレーションに関するドキュメント][1]で手順を参照してください。

64-bit x86 および Arm v8 アーキテクチャ用のパッケージをご用意しています。その他のアーキテクチャについては、ソースインストールをご利用ください。

**注**:
- 64 ビット x86 アーキテクチャでは、Debian 7 (wheezy) 以上は Agent < 6.36.0/7.36.0 でサポートされています。Debian 8 (jessie) 以上は、Agent >= 6.36.0/7.36.0 でサポートされています。SysVinit は Agent v6.6.0+ でサポートされています。
- 64 ビット Arm v8 アーキテクチャでは、Debian 9 (stretch) 以降がサポートされています。

## コマンド

Agent v6 & v7 では、オペレーティングシステムから提供されるサービスマネージャーが Agent のライフサイクルを担う一方で、他のコマンドは Agent バイナリから直接実行する必要があります。Agent v5 では、ほぼすべてがサービスマネージャーによって実行されます。

| 説明                        | コマンド                                                |
|------------------------------------|--------------------------------------------------------|
| Agent をサービスとして起動           | `sudo service datadog-agent start`                     |
| サービスとして実行中の Agent の停止    | `sudo service datadog-agent stop`                      |
| サービスとして実行中の Agent の再起動 | `sudo service datadog-agent restart`                   |
| Agent サービスのステータス            | `sudo service datadog-agent status`                    |
| 実行中の Agent のステータスページ       | `sudo datadog-agent status`                            |
| フレアの送信                         | `sudo datadog-agent flare`                             |
| コマンドの使用方法の表示              | `sudo datadog-agent --help`                            |
| チェックの実行                        | `sudo -u dd-agent -- datadog-agent check <CHECK_NAME>` |

**注**: ご使用のシステムで `service` ラッパーを使用できない場合は、以下を使用してください。

* `upstart` ベースのシステムの場合: `sudo start/stop/restart/status datadog-agent`
* `systemd` ベースのシステムの場合: `sudo systemctl start/stop/restart/status datadog-agent`

## 構成

Agent の構成ファイルおよびフォルダーの場所

* `/etc/datadog-agent/datadog.yaml`

[インテグレーション][5]用コンフィギュレーションファイルの場所

* `/etc/datadog-agent/conf.d/`

## Agent のアンインストール

Agent をアンインストールするには、次のコマンドを実行します。

```shell
sudo apt-get remove datadog-agent -y
```

このコマンドでは、Agent は削除されますが以下は削除されません。

* `datadog.yaml` コンフィギュレーションファイル
* `/etc/datadog-agent` コンフィギュレーションフォルダ内のユーザー作成ファイル
* `/opt/datadog-agent` フォルダ内のユーザー作成ファイル
* `dd-agent` ユーザー
* Datadog ログファイル

これらの要素も削除したい場合は、Agent 削除後に次のコマンドを実行します。

```shell
sudo apt-get remove --purge datadog-agent -y
```


{{% apm-ssi-uninstall-linux %}}

## トラブルシューティング

[Agent のトラブルシューティングに関するドキュメント][3]を参照してください。

## 埋め込み Agent の使用

Agent には、埋め込み Python 環境が `/opt/datadog-agent/embedded/` に含まれています。`python`、`pip` などの共通バイナリは `/opt/datadog-agent/embedded/bin/` に含まれています。

詳細については、[埋め込み Agent へのパッケージの追加方法][4]の手順を参照してください。

## その他の参考資料

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://app.datadoghq.com/account/settings/agent/latest?platform=debian
[2]: /ja/agent/faq/agent-v6-changes/?tab=linux#service-lifecycle-commands
[3]: /ja/agent/troubleshooting/
[4]: /ja/developers/guide/custom-python-package/
[5]: /ja/integrations/