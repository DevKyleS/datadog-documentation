---
categories:
- cloud
- google cloud
- ログの収集
custom_kind: integration
dependencies: []
description: Google Cloud Tasks のキーメトリクスを追跡
doc_link: https://docs.datadoghq.com/integrations/google_cloud_tasks/
draft: false
git_integration_title: google_cloud_tasks
has_logo: true
integration_id: google-cloud-tasks
integration_title: Google Cloud Tasks
integration_version: ''
is_public: true
manifest_version: '1.0'
name: google_cloud_tasks
public_title: Datadog-Google Cloud Tasks インテグレーション
short_description: Google Cloud Tasks のキーメトリクスを追跡
version: '1.0'
---

<!--  SOURCED FROM https://github.com/DataDog/dogweb -->
## 概要

Google Cloud Tasks は、大量の分散タスクの実行、ディスパッチ、配布を管理できるフルマネージド型のサービスです。

Datadog Google Cloud Platform インテグレーションを使用して、Google Cloud Tasks からメトリクスを収集できます。

## セットアップ

### インストール

[Google Cloud Platform インテグレーション][1]をまだセットアップしていない場合は、最初にセットアップします。それ以上のインストール手順はありません。

### 収集データ

Google Cloud Tasks のログは Google Cloud Logging で収集され、Cloud Pub/Sub トピックを通じて Dataflow ジョブに送信されます。まだの場合は、[Datadog Dataflow テンプレートでロギングをセットアップしてください][2]。

これが完了したら、Google Cloud Tasks のログを Google Cloud Logging から Pub/Sub トピックへエクスポートします。

1. [Google Cloud Logging のページ][3]に移動し、Google Cloud Tasks のログを絞り込みます。
2. **Create Export** をクリックし、シンクに名前を付けます。
3. 宛先として "Cloud Pub/Sub" を選択し、その目的で作成された Pub/Sub トピックを選択します。**注**: Pub/Sub トピックは別のプロジェクトに配置できます。
4. **作成**をクリックし、確認メッセージが表示されるまで待ちます。

## 収集データ

### メトリクス
{{< get-metrics-from-git "google-cloud-tasks" >}}


### イベント

Google Cloud Tasks インテグレーションには、イベントは含まれません。

### サービスチェック

Google Cloud Tasks インテグレーションには、サービスのチェック機能は含まれません。

## トラブルシューティング

ご不明な点は、[Datadog のサポートチーム][5]までお問い合わせください。

[1]: https://docs.datadoghq.com/ja/integrations/google_cloud_platform/
[2]: https://docs.datadoghq.com/ja/integrations/google_cloud_platform/#log-collection
[3]: https://console.cloud.google.com/logs/viewer
[4]: https://github.com/DataDog/dogweb/blob/prod/integration/google_cloud_tasks/google_cloud_tasks_metadata.csv
[5]: https://docs.datadoghq.com/ja/help/
