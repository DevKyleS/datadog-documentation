---
categories:
- クラウド
- data stores
- aws
- log collection
custom_kind: integration
dependencies: []
description: Amazon DocumentDB のメトリクスとログを監視
doc_link: https://docs.datadoghq.com/integrations/amazon_documentdb/
draft: false
further_reading:
- link: https://www.datadoghq.com/blog/monitor-documentdb-with-datadog/
  tag: ブログ
  text: Amazon DocumentDB のメトリクスとログを Datadog で収集
git_integration_title: amazon_documentdb
has_logo: true
integration_id: ''
integration_title: Amazon DocumentDB
integration_version: ''
is_public: true
manifest_version: '1.0'
name: amazon_documentdb
public_title: Datadog-Amazon DocumentDB インテグレーション
short_description: Amazon DocumentDB のメトリクスとログを監視
version: '1.0'
---

<!--  SOURCED FROM https://github.com/DataDog/dogweb -->
## 概要

Amazon DocumentDB は、MongoDB のワークロードをサポートする、高速で、スケーラブル、高可用性、フルマネージド型のドキュメントデータベースサービスです。

## セットアップ

### インストール

[Amazon Web Services インテグレーション][1]をまだセットアップしていない場合は、最初にセットアップします。

### メトリクスの収集

1. [AWS インテグレーションページ][2]で、`Metric Collection` タブの下にある `DocumentDB` が有効になっていることを確認します。
2. [Datadog - Amazon DocumentDB インテグレーション][3]をインストールします。

### ログ収集

#### ログの有効化

Amazon DocumentDB から S3 バケットまたは CloudWatch のいずれかにログを送信するよう構成します。

**注**: S3 バケットにログを送る場合は、_Target prefix_ が `amazon_documentdb` に設定されているかを確認してください。

#### ログを Datadog に送信する方法

1. [Datadog Forwarder Lambda 関数][4]をまだセットアップしていない場合は、セットアップします。
2. Lambda 関数がインストールされたら、AWS コンソールから、Amazon DocumentDB ログを含む S3 バケットまたは CloudWatch のロググループに手動でトリガーを追加します。

    - [S3 バケットに手動トリガーを追加][5]
    - [CloudWatch ロググループに手動トリガーを追加][6]

## 収集されるデータ

### メトリクス
{{< get-metrics-from-git "amazon_documentdb" >}}


AWS から取得される各メトリクスには、dbinstanceidentifier、dbclusteridentifier など、AWS コンソールに表示されるタグと同じタグが割り当てられます。

### イベント

Amazon DocumentDB インテグレーションには、イベントは含まれません。

### サービス チェック

Amazon DocumentDB インテグレーションには、サービスのチェック機能は含まれません。

## トラブルシューティング

ご不明な点は、[Datadog のサポートチーム][8]までお問い合わせください。

## その他の参考資料

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://docs.datadoghq.com/ja/integrations/amazon_web_services/
[2]: https://app.datadoghq.com/integrations/amazon-web-services
[3]: https://app.datadoghq.com/integrations/amazon-documentdb
[4]: https://docs.datadoghq.com/ja/logs/guide/forwarder/
[5]: https://docs.datadoghq.com/ja/logs/guide/send-aws-services-logs-with-the-datadog-lambda-function/#collecting-logs-from-s3-buckets
[6]: https://docs.datadoghq.com/ja/logs/guide/send-aws-services-logs-with-the-datadog-lambda-function/#collecting-logs-from-cloudwatch-log-group
[7]: https://github.com/DataDog/dogweb/blob/prod/integration/amazon_documentdb/amazon_documentdb_metadata.csv
[8]: https://docs.datadoghq.com/ja/help/