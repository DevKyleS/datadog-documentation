---
aliases:
- /ja/account_management/audit_logs/
further_reading:
- link: /account_management/audit_trail/events/
  tag: ドキュメント
  text: 監査証跡イベントについて
- link: /account_management/org_settings/
  tag: ドキュメント
  text: 組織設定について
- link: /data_security/pci_compliance/
  tag: ドキュメント
  text: PCI 準拠の Datadog 組織をセットアップする
- link: https://www.datadoghq.com/blog/compliance-governance-transparency-with-datadog-audit-trail/
  tag: ブログ
  text: Datadog Audit Trail で、チーム全体のコンプライアンス、ガバナンス、透明性を構築します
- link: https://www.datadoghq.com/blog/audit-trail-best-practices/
  tag: ブログ
  text: 監査証跡で重要な Datadog のアセットと構成を監視する
title: Datadog Audit Trail（監査証跡）
---

## 概要

管理者またはセキュリティチームのメンバーは、[Datadog 監査証跡][1]を使用して組織内で Datadog を使用しているユーザーとそのコンテキストを確認できます。個人として、自身のアクションをストリームで確認することも可能です。

監査証跡内で発生するイベントには、Datadog API に要求されたすべてのリクエストを顧客リクエストに変換する**リクエストイベント**と、**製品特化イベント**の 2 種類があります。

たとえば、**リクエストイベント**を追跡すると、そのイベントに到達する API 呼び出しを確認できます。または、組織または請求担当の管理者の場合は、監査証跡イベントを使用してインフラストラクチャーの状態を変更したユーザーイベントを追跡できます。

この環境では、以下のような製品特有のイベントについて確認する場合に監査証跡が便利です。

  -  ログのボリュームが変化し、毎月の請求額が変化したためインデックスの保持期間が変更されたとき。

  - ダッシュボードまたはモニターが壊れていて修復が必要な場合に、プロセッサまたはパイプラインを変更したユーザーおよびその変更日時を確認する。

  - インデックス化のボリュームが増減し、ログが見つからないまたは請求額が増加したため、除外フィルターを変更したユーザーを確認する。

セキュリティ管理者またはInfoSecチームには、監査証跡イベントはコンプライアンスチェックや Datadog リソースにおける監査証跡（だれがいつ何をしたか）の管理に便利です。たとえば、以下のような監査証跡を管理できます。

- 重要なダッシュボード、モニター、その他の Datadog リソースの更新または削除があった場合の日時と実行者。

- 組織内のユーザーログイン、アカウント、ロール変更。

**注**: PCI 準拠の Datadog 組織をセットアップするための情報は、[PCI DSS 準拠][2]をご覧ください。

## セットアップ

Datadog Audit Trail を有効にするには、[組織設定][3]で *COMPLIANCE* の *Audit Trail Settings* を選択し、**Enable** ボタンをクリックします。

{{< img src="account_management/audit_logs/audit_trail_settings.png" alt="監査証跡の設定ページで無効になっている様子" style="width:85%;">}}

監査証跡を有効にした人を確認するには
1. [Events Explorer][4] に移動します。
2. 検索バーに `Datadog Audit Trail was enabled by` と入力します。イベントをキャプチャするために、より広い時間範囲を選択する必要がある場合があります。
3. "A user enabled Datadog Audit Trail" というタイトルの最新イベントには、最後に監査証跡を有効にしたのが誰であるかが表示されます。

## 構成


### 権限
Audit Trail を有効または無効にできるのは、`Audit Trail Write` 権限を持つユーザーのみです。さらに、Audit Explorer で監査イベントを表示するには、`Audit Trail Read` 権限が必要です。

### アーカイブ

アーカイブは、監査証跡のオプション機能です。Amazon S3、Google Cloud Storage、Azure Storage への書き込みと、これらの場所から SIEM システムによりイベントを読み取るためにアーカイブを使用できます。アーカイブ構成を作成または更新してから次にアーカイブのアップロードが試行されるまで、数分かかることがあります。イベントは 15 分ごとにアーカイブにアップロードされるので、**15 分待ってストレージバケットをチェックし**、Datadog アカウントからアーカイブが正常にアップロードされたことを確認してください。

Audit Trail のアーカイブを有効にするには、[組織設定][3]で *Compliance* の *Audit Trail Settings* を選択し、Archiving までスクロールダウンして Store Events のトグルボタンをクリックしてオンにします。

### 保持

イベントの保持は、監査証跡のオプション機能です。*Retention* までスクロールし、*Retain Audit Trail Events* のトグルをクリックすると、有効になります。

デフォルトの監査証跡イベント保持期間は 7 日間です。保持期間は 3～90 日間の間で設定できます。

{{< img src="account_management/audit_logs/retention_period.png" alt="Datadog の監査証跡保持期間の設定" style="width:80%;">}}

## 監査イベントの確認

監査イベントを詳しく確認するには、[Audit Trail][1] セクションに移動します。このセクションは Datadog の[組織設定][3]からもアクセス可能です。 

{{< img src="account_management/audit_logs/audit_side_nav.png" alt="組織設定メニューの監査証跡設定" style="width:30%;">}}

Audit Trail イベントは、[Log Explorer][5] 内のログと同様の機能を備えています。

- フィルターを使用して、イベント名 (ダッシュボード、モニター、認証など)、認証属性 (アクター、API キー ID、ユーザーのメールアドレスなど)、`Status` (`Error`、`Warn`、`Info`)、メソッド (`POST`、`GET`、`DELETE`) およびその他のファセット別に監査証跡イベントを確認します。

- イベントを選択してイベント属性タブに移動し、関連する監査証跡イベントを確認します。絞り込みまたは検索から除外する特定の属性を選択します (`http.method`、`usr.email`、`client.ip` など)。

{{< img src="account_management/audit_logs/attributes.png" alt="組織設定メニューの監査証跡" style="width:50%;">}}


### 保存ビュー

効率的なトラブルシューティングには、探索を可能にする適切なスコープにデータがあり、意味のある情報を表示する視覚化オプションにアクセスでき、分析を可能にする関連ファセットがリストされていることが必要です。トラブルシューティングはコンテキストに依存するため、保存ビューを使用すると、あなたとチームメイトが異なるトラブルシューティングコンテキスト間で簡単に切り替えられるようになります。保存ビューは、監査証跡エクスプローラーの左上隅からアクセスできます。

デフォルトのビュー以外のすべての保存ビューは、組織全体で共有されます。

* **インテグレーション保存ビュー**は、監査証跡と同時に提供されます。これらのビューは読み取り専用で、Datadog のロゴが表示されます。
* **カスタム保存ビュー**はユーザーによって作成されます。組織内のユーザーなら誰でも編集可能です ([読み取り専用ユーザー][6]を除く)。また、作成者のアバターによって識別されます。エクスプローラーの現在のコンテンツから新しいカスタム保存ビューを作成するには、**Save** ボタンをクリックします。

Views パネルの保存ビューエントリからは、いつでも以下のアクションが可能です。

* 保存ビューを**ロード**または**リロード** 
* 現在のビューのコンフィギュレーションで保存ビューを**更新**。
* 保存ビューの**名前を変更**または**削除**。
* ショートリンクを使用して保存ビューを**共有**。
* 保存ビューに**スター**を付けて、保存ビューリストの先頭に表示。ナビゲーションメニューから直接アクセス可能になります。

**注**: インテグレーションの保存ビューおよび[読み取り専用ユーザー][6]に対しては、更新、名前変更、および削除のアクションは無効になっています。


### デフォルトのビュー

{{< img src="logs/explorer/saved_views/default.png" alt="デフォルトビュー" style="width:50%;" >}}

デフォルトビュー機能では、監査証跡エクスプローラーを最初に開いたときに常に表示されるクエリやフィルターのデフォルトセットを設定することができます。デフォルトビューに戻るには、Views パネルを開き、リロードボタンをクリックします。

既存の監査証跡エクスプローラーのビューは、デフォルトの保存ビューです。この構成は、自身のみがアクセスおよび閲覧可能であり、この構成を更新しても、組織には何の影響も与えません。UI で何らかのアクションを実行するか、別の構成を埋め込んだ監査証跡エクスプローラーのリンクを開くことで、デフォルトの保存ビューを**一時的**にオーバーライドすることができます。

Views パネルのデフォルト保存ビューエントリからは、いつでも以下のアクションが可能です。

* エントリをクリックして、デフォルトビューを**リロード**。
* 現在のパラメーターでデフォルトビューを**更新**。
* デフォルトビューを Datadog のデフォルトに**リセット**して再起動。

### 注目イベント

注目イベントとは、監査イベントのサブセットで、Datadog が特定した、請求に影響を与える可能性のある、またはセキュリティに影響を与える可能性のある重要な構成変更を示すものです。これにより、組織管理者は、生成された多くのイベントの中から最も重要なイベントに絞り込むことができ、利用可能なすべてのイベントとそのプロパティについて学習する必要がありません。

{{< img src="account_management/audit_logs/notable_events.png" alt="監査イベントのファセットパネルで、チェックした注目イベントが表示される" style="width:30%;">}}

以下のクエリにマッチするイベントは、注目イベントとしてマークされます。

| 監査イベントの説明                                          | 監査エクスプローラーのクエリ                           |
| ------------------------------------------------------------------- | --------------------------------------------------|
| ログベースメトリクスの変更 | `@evt.name:"Log Management" @asset.type:"custom_metrics"` |
| ログ管理インデックスの除外フィルターの変更 | `@evt.name:"Log Management" @asset.type:"exclusion_filter"` |
| ログ管理インデックスの変更 | `@evt.name:"Log Management" @asset.type:index` |
| APM 保持フィルターの変更 | `@evt.name:APM @asset.type:retention_filter` |
| APM カスタムメトリクスの変更 | `@evt.name:APM @asset.type:custom_metrics` |
| メトリクスタグの変更 | `@evt.name:Metrics @asset.type:metric @action:(created OR modified)` |
| RUM アプリケーションの作成と削除 | `@evt.name:"Real User Monitoring" @asset.type:real_user_monitoring_application @action:(created OR deleted)` |
| 機密データスキャナーのスキャングループの変更 | `@evt.name:"Sensitive Data Scanner" @asset.type:sensitive_data_scanner_scanning_group` |
| Synthetic テストの作成または削除 | `@evt.name:"Synthetics Monitoring" @asset.type:synthetics_test @action:(created OR deleted)` |

### Inspect Changes (Diff)

監査イベントの詳細パネルにある Inspect Changes (Diff) タブは、行われた構成変更と以前に設定されたものを比較します。ダッシュボード、ノートブック、およびモニターの構成に加えられた変更が表示され、JSON オブジェクトとして表されます。

{{< img src="account_management/audit_logs/inspect_changes.png" alt="監査イベント側のパネルで、複合条件モニター構成の変更を表示。緑でハイライトされたテキストが変更されたもので、赤でハイライトされたテキストは削除されたものです。" style="width:70%;">}}

## リファレンステーブルに基づく監査イベントのフィルター

<div class="alert alert-warning">リファレンステーブルはベータ版です。1,000,000 行を超えるリファレンステーブルは、イベントのフィルタリングには使用できません。リファレンステーブルの作成および管理方法の詳細については、<a href="https://docs.datadoghq.com/integrations/guide/reference-tables/">リファレンステーブルによるカスタムメタデータの追加</a>を参照してください。</div>

リファレンステーブルを使用すると、メタデータと監査イベントを組み合わせることができ、Datadog ユーザーの行動を調査するための詳細な情報を提供できます。リファレンステーブルに基づくクエリフィルターを追加して、検索クエリを実行します。この機能の有効化と管理の詳細については、[リファレンステーブル][2]ガイドを参照してください。

リファレンステーブルを使用したクエリフィルターを適用するには、クエリエディタ横の `+ Add` ボタンをクリックし、**Join with Reference Table** を選択します。以下の例では、認可されていない IP アドレスから Datadog にアクセスしているユーザーによって変更されたダッシュボードを検索するために、リファレンステーブルのクエリフィルターを使用しています。

{{< img src="account_management/audit_logs/reference_tables.png" alt="リファレンステーブル検索オプションがハイライトされた Datadog Audit Trail Explorer" border="true" popup="true" style="width:100%;" >}}

### API キー監査

<div class="alert alert-warning">API キー監査は非公開ベータ版です。</div>

ログ管理ユーザーは、Audit Trail を使用して API キーの使用状況を監査できます。API キー監査では、ログに `datadog.api_key_uuid` タグが付与されており、そのタグにはログ収集に使用された API キーの UUID が含まれています。この情報を基に、以下の点を判断します。
- 組織やテレメトリーソース全体での API キーの使用状況。
- API キーのローテーションと管理。

## モニターを作成

監査証跡イベントの特定のタイプや証跡の属性別にモニターを作成するには、[監査証跡モニターに関するドキュメント][7]をご参照ください。例: 特定のユーザーログが発生したときにトリガーするモニターを設定、ダッシュボードが削除されたとき用のモニターを設定など。

## ダッシュボードやグラフの作成

ダッシュボードを使用すると、監査証跡イベントに視覚的なコンテキストを追加できます。監査ダッシュボードを作成するには:

1. Datadog で[新しいダッシュボード][8]を作成します。
2. 視覚化を選択します。監査イベントを[トップリスト][9]、[時系列][10]、[リスト][11]で視覚化することができます。
3. [データのグラフを作成][12]: 編集で、データソースとして *Audit Events* を選択し、クエリを作成します。監査イベントはカウント別に絞り込まれ、ファセット別にグループ化できます。ファセットと上限を選択します。
{{< img src="account_management/audit_logs/audit_graphing.png" alt="監査証跡をデータソースとして設定しデータからグラフを作成" style="width:100%;">}}
4. 表示設定を完了してグラフにタイトルを付けます。*Save* ボタンをクリックしてダッシュボードを作成します。

## スケジュールレポートの作成

Datadog 監査証跡では、監査分析ビューを定期的にスケジュールされたメールとして送信することができます。これらのレポートは、Datadog プラットフォームの使用状況を定期的に監視するのに便利です。例えば、国別のユニークな Datadog ユーザーログイン数の週次レポートを取得するように選択できます。このクエリにより、異常なログインアクティビティを監視したり、使用状況に関する自動化された洞察を受け取ったりすることができます。

監査分析クエリをレポートとしてエクスポートするには、時系列、トップリスト、またはテーブルクエリを作成し、**More...** > **Export as scheduled report** をクリックして、クエリをスケジュールレポートとしてエクスポートを開始します。

**注**: **List** ビューには、スケジュールされたレポートへのエクスポートオプションはありません。

{{< img src="account_management/audit_logs/scheduled_report_export.png" alt="More… ドロップダウンメニューの Export as scheduled report 機能" style="width:90%;" >}}

1. クエリウィジェットで作成されるダッシュボードの名称を入力します。新しいダッシュボードは、スケジュールされたレポートごとに作成されます。このダッシュボードは、レポートの内容やスケジュールを変更する必要がある場合に、後で参照・変更することができます。
2. レポートの頻度や時間帯をカスタマイズして、メールレポートのスケジュールを設定します。
3. メールを送信したい受信者を追加します。
4. メールレポートの一部として必要なカスタマイズされたメッセージを追加します。
5. **Create Dashboard and Schedule Report** をクリックします。

{{< img src="account_management/audit_logs/export_workflow.png" alt="監査分析ビューをスケジュールメールにエクスポートする" style="width:80%;" >}}

## 監査イベントを CSV 形式でダウンロード

Datadog Audit Trail では、最大 10 万件の監査イベントを CSV ファイルとしてローカルにダウンロードすることができます。これらのイベントは、ローカルで分析したり、別のツールにアップロードしてさらに分析したり、セキュリティおよびコンプライアンス演習の一環として適切なチームメンバーと共有したりすることができます。

監査イベントを CSV 形式でエクスポートするには
1. 興味のあるイベントをキャプチャする適切な検索クエリを実行します
2. CSV の一部として取り入れたいイベントフィールドをビューの列として追加します
3. Download as CSV をクリックします
4. エクスポートするイベント数を選択し、CSV 形式でエクスポートします

## すぐに使えるダッシュボード

Datadog Audit Trail には、インデックス保持の変更、ログパイプラインの変更、ダッシュボードの変更など、さまざまな監査イベントを表示する[すぐに使えるダッシュボード][13]が付属しています。このダッシュボードを複製して、監査ニーズに合わせてクエリや視覚化をカスタマイズすることができます。

{{< img src="account_management/audit_logs/audit_dashboard.png" alt="監査証跡ダッシュボード" style="width:100%;">}}

## その他の参考資料

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://app.datadoghq.com/audit-trail
[2]: /ja/data_security/pci_compliance/
[3]: https://app.datadoghq.com/organization-settings/
[4]: https://app.datadoghq.com/event/explorer
[5]: /ja/logs/explorer/
[6]: https://docs.datadoghq.com/ja/account_management/rbac/permissions/?tab=ui#general-permissions
[7]: /ja/monitors/types/audit_trail/
[8]: /ja/dashboards/
[9]: /ja/dashboards/widgets/top_list/
[10]: /ja/dashboards/widgets/timeseries/
[11]: /ja/dashboards/widgets/list/
[12]: /ja/dashboards/querying/#define-the-metric/
[13]: https://app.datadoghq.com/dash/integration/30691/datadog-audit-trail-overview?from_ts=1652452436351&to_ts=1655130836351&live=true