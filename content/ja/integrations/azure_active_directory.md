---
app_id: azure-active-directory
app_uuid: 8c4717a8-93f0-4de6-b79b-1e7f52c94895
assets:
  integration:
    auto_install: true
    configuration: {}
    events:
      creates_events: false
    service_checks:
      metadata_path: assets/service_checks.json
    source_type_id: 10106
    source_type_name: Azure Active Directory
author:
  homepage: https://www.datadoghq.com
  name: Datadog
  sales_email: info@datadoghq.com (日本語対応)
  support_email: help@datadoghq.com
categories:
- azure
- クラウド
- ログの収集
- セキュリティ
custom_kind: インテグレーション
dependencies:
- https://github.com/DataDog/integrations-core/blob/master/azure_active_directory/README.md
display_on_public_website: true
draft: false
git_integration_title: azure_active_directory
integration_id: azure-active-directory
integration_title: Microsoft Entra ID
integration_version: ''
is_public: true
manifest_version: 2.0.0
name: azure_active_directory
public_title: Microsoft Entra ID
short_description: Microsoft Entra ID のアクティビティログを分析する
supported_os:
- linux
- windows
- macos
tile:
  changelog: CHANGELOG.md
  classifier_tags:
  - Category::Azure
  - Category::Cloud
  - Category::Log Collection
  - Category::Security
  - Supported OS::Linux
  - Supported OS::Windows
  - Supported OS::macOS
  - Offering::Integration
  configuration: README.md#Setup
  description: Microsoft Entra ID のアクティビティログを分析する
  media: []
  overview: README.md#Overview
  support: README.md#Support
  title: Microsoft Entra ID
---

<!--  SOURCED FROM https://github.com/DataDog/integrations-core -->


## 概要

Microsoft Entra ID はクラウドホスト型のアイデンティティおよびアクセス管理サービスで、ユーザーが外部リソースにアクセスできるようにします。このインテグレーションにより、[Microsoft Entra ID][1] の監査ログとサインインログを Datadog に転送できます。

## セットアップ

### インストール

このインテグレーションにより、 Azure Event Hubs を使用してログが Datadog に転送されます。アクティビティログをイベントハブに転送するように Entra ID を構成します。

### 構成

1. [Datadog への Azure ログの送信][2]ガイドに従って、Event Hub を使用して Azure から Datadog へのログ転送パイプラインをセットアップします。

2. Azure ポータルで、_Microsoft Entra ID > Monitoring > Audit logs_ を選択します。

3. **Export Data Settings** を選択します。

4. 診断設定ペインで、以下を実行します。

   - 既存の設定を変更するには、**Edit setting** を選択します。
   - 新しい設定を追加するには、**Add diagnostics setting** を選択します。最大3つまで設定できます。

5. **Stream to an event hub** チェックボックスを選択します。

6. ログを送信するために作成した Azure サブスクリプションと Event Hubs ネームスペースを選択します。

7. 以下のいずれかまたは両方を実行します。Datadog では両方選択することを推奨します。

   - 監査ログを送信するには、**AuditLogs** チェックボックスを選択します。
   - サインインログを送信するには、**SignInLogs** チェックボックスを選択します。

8. **Save** を選択します。

15 分以内に Datadog はログを受け取り始めます。
セットアップに関する詳細は、[Azure チュートリアル][3]を参照してください。

## 収集データ

#### ログ収集

このインテグレーションにより、Microsoft Entra ID アクティビティログのログ取り込みを設定できます。

これには以下が含まれます。

   - Sign-ins - 管理対象のアプリケーションおよびユーザーのサインインアクティビティの使用状況に関する情報を提供します。

   - Audit logs - Azure AD 内のさまざまな機能が行った変更のログを介してトレーサビリティを提供します。

### メトリクス

Microsoft Entra ID には、メトリクスは含まれません。

### イベント

Datadog は、Azure アプリ登録、Key Vault キー、Key Vault シークレット、Key Vault 証明書の資格情報期限切れを視覚化する資格情報期限切れイベントを送信します。Azure アプリ登録のイベントを受信するには、Microsoft Entra ID インテグレーションをインストールする必要があります。また、Azure からイベントを受信するには、[Azure インテグレーション][4]のインストールが必要です。


- **期限切れイベント**は、資格情報有効期限の 60 日、30 日、15 日、1 日前に送信され、期限切れ後に 1 回送信されます。
- **権限欠落イベント**は 15 日ごとに送信されます。権限欠落イベントは、Datadog に権限が与えられていない Key Vault をリストアップします。前の 15 日間のサイクルで Key Vault 権限に関して変更が行われていない場合、イベント通知は再度送信されません。

これらのイベントは[イベントエクスプローラー][5]で表示できます。

**注**: 

- Azure アプリ登録期限切れイベントを収集するには、[Microsoft Graph API へのアクセスを有効にします][6]。
- 証明書とそれに関連するキーとシークレットがまったく同時に期限切れになる場合、すべてのリソースに対して 1 つの期限切れイベントが送信されます。

## トラブルシューティング

ご不明な点は、[Datadog のサポートチーム][7]までお問い合わせください。

[1]: https://learn.microsoft.com/entra/identity/monitoring-health/overview-monitoring-health
[2]: https://docs.datadoghq.com/ja/logs/guide/azure-logging-guide/
[3]: https://learn.microsoft.com/entra/identity/monitoring-health/howto-stream-logs-to-event-hub
[4]: https://docs.datadoghq.com/ja/integrations/azure/
[5]: https://app.datadoghq.com/event/explorer
[6]: https://docs.datadoghq.com/ja/integrations/guide/azure-graph-api-permissions/
[7]: https://docs.datadoghq.com/ja/help