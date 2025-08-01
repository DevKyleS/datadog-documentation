---
app_id: istio
app_uuid: de5b5443-5038-46cf-a052-0484348776d6
assets:
  dashboards:
    Istio Overview: assets/dashboards/overview.json
    Istio Overview 1.5: assets/dashboards/istio_1_5_overview.json
    Istio Overview 1.5 (OpenMetrics): assets/dashboards/istio_1_5_openmetrics_overview.json
    Istio base dashboard: assets/dashboards/istio_overview.json
  integration:
    auto_install: true
    configuration:
      spec: assets/configuration/spec.yaml
    events:
      creates_events: false
    metrics:
      check:
      - istio.mixer.process.cpu_seconds_total
      - istio.mesh.request.count
      - istio.galley.endpoint_no_pod
      metadata_path: metadata.csv
      prefix: istio.
    process_signatures:
    - pilot-agent proxy router
    - envoy envoy-rev0.json
    service_checks:
      metadata_path: assets/service_checks.json
    source_type_id: 10017
    source_type_name: Istio
  monitors:
    Istio Proxy Requests Error Percentage: assets/monitors/request_error_rate.json
    Istio xDS Push Error Rate: assets/monitors/xds_push_error_rate.json
    Number of failed Istio sidecar injection is high: assets/monitors/failed_sidecar_injection.json
  saved_views:
    Istio Error Overview: assets/saved_views/istio_error_overview.json
    Istio Overview: assets/saved_views/istio_overview.json
    Istio Pilot Error Logs: assets/saved_views/istio_pilot_errors.json
    Istio Pilot Logs: assets/saved_views/istio_pilot_logs.json
    Istio Proxyv2 Error Logs: assets/saved_views/istio_proxyv2_errors.json
    Istio Proxyv2 Logs: assets/saved_views/istio_proxyv2_logs.json
author:
  homepage: https://www.datadoghq.com
  name: Datadog
  sales_email: info@datadoghq.com
  support_email: help@datadoghq.com
categories:
- log collection
- network
- tracing
custom_kind: インテグレーション
dependencies:
- https://github.com/DataDog/integrations-core/blob/master/istio/README.md
display_on_public_website: true
draft: false
git_integration_title: istio
integration_id: istio
integration_title: Istio
integration_version: 8.1.0
is_public: true
manifest_version: 2.0.0
name: istio
public_title: Istio
short_description: パフォーマンススキーマメトリクス、クエリスループット、カスタムメトリクスなどを収集。
supported_os:
- linux
- windows
- macos
tile:
  changelog: CHANGELOG.md
  classifier_tags:
  - Category::ログの収集
  - Category::ネットワーク
  - Category::Tracing
  - Supported OS::Linux
  - Supported OS::Windows
  - Supported OS::macOS
  - Submitted Data Type::Metrics
  - Submitted Data Type::Logs
  - Submitted Data Type::Traces
  - Offering::Integration
  configuration: README.md#Setup
  description: パフォーマンススキーマメトリクス、クエリスループット、カスタムメトリクスなどを収集。
  media: []
  overview: README.md#Overview
  resources:
  - resource_type: blog
    url: https://www.datadoghq.com/blog/monitor-istio-with-datadog
  - resource_type: blog
    url: https://www.datadoghq.com/blog/istio-metrics/
  - resource_type: blog
    url: https://www.datadoghq.com/blog/istio-datadog/
  support: README.md#Support
  title: Istio
---

<!--  SOURCED FROM https://github.com/DataDog/integrations-core -->


## 概要

Datadog は、Istio 環境のあらゆる側面を監視するため、以下を実現できます。
- [ログ](#log-collection)を使用して、Envoy および Istio の Control Plane の健全性を評価。
- [リクエスト、帯域幅、リソース消費のメトリクス](#metrics)でサービスメッシュのパフォーマンスを詳しく確認。
- [Cloud Network Monitoring][1] で、コンテナ、ポッド、サービス間のネットワークコミュニケーションをメッシュ状にマッピング。
- [APM[[2] でメッシュを実行してアプリケーションの分散型トレースの詳細を確認。

Istio 環境での Datadog の使用について、詳細は[モニターのブログ記事をご参照ください][3]。

## セットアップ

コンテナ環境でのインテグレーション構成に関する一般的な説明は、[Kubernetes での Autodiscovery を使用したインテグレーションの構成][4]または [Docker での Autodiscovery を使用したインテグレーションの構成][5]を参照してください。

この OpenMetrics ベースのインテグレーションには、_最新_モード (`use_openmetrics: true`) と_レガシー_モード (`use_openmetrics: false`) があります。すべての最新機能を利用するために、Datadog は_最新_モードを有効にすることを推奨します。詳しくは、[OpenMetrics ベースのインテグレーションにおける最新バージョニングとレガシーバージョニング][6]を参照してください。

Istio メトリクスを収集する Datadog のインスタンスが複数ある場合は、すべてのインスタンスで同じモードを使用していることを確認してください。そうしないと、Datadog サイトでメトリクスデータが変動する可能性があります。

`[OpenMetrics V1]`、`[OpenMetrics V2]`、または `[OpenMetrics V1 and V2]` とマークされたメトリクスは、Istio インテグレーションの対応するモードでのみ利用可能です。`Istio v1.5+` とマークされたメトリクスは Istio バージョン 1.5 以降で収集されます。

### インストール

Istio は Datadog Agent に含まれています。Istio サーバーまたはクラスターに [Datadog Agent をインストール][7]し、Istio で Agent を指定します。

#### Envoy

Envoy プロキシ を Istioで監視する場合は、[Envoy インテグレーション][8]を構成します。

### 構成

#### メトリクスの収集
Istio v1.5+ を監視するには、Prometheus 形式のメトリクスに対応した [Istio アーキテクチャ][9]に基づく 2 つの主要なコンポーネントが必要です。

- **データプレーン**: `istio-proxy` サイドカーコンテナ
- **コントロールプレーン**: プロキシを管理する `istiod` サービス

これらはどちらも `istio` Agent チェックとして実行されますが、それぞれ異なる責任を持ち、別々に構成されます。

##### データプレーン構成

デフォルトの [`istio.d/auto_conf.yaml`][10] ファイルは、`istio-proxy` サイドカーコンテナごとに監視を自動的にセットアップします。Agent は、自動検出した各サイドカーコンテナに対してこのチェックを初期化します。この構成により、これらの各サイドカーコンテナが公開するデータの `istio.mesh.*` メトリクスを報告できるようになります。

インテグレーションのデータプレーン部分をカスタマイズするには、カスタム Istio コンフィギュレーションファイル `istio.yaml` を作成してください。このファイルを作成する方法については、[Kubernetes 上でのインテグレーションの構成][4]または [Docker でのオートディスカバリーを利用したインテグレーションの構成][5]を参照してください。

このファイルには、以下の内容を記載する必要があります。

```yaml
ad_identifiers:
  - proxyv2
  - proxyv2-rhel8

init_config:

instances:
  - use_openmetrics: true
    send_histograms_buckets: false
    istio_mesh_endpoint: http://%%host%%:15020/stats/prometheus
    tag_by_endpoint: false
```

追加の構成が必要な場合は、このファイルをカスタマイズしてください。利用可能なすべての構成オプションについては、[サンプルの istio.d/conf.yaml][11] を参照してください。

##### コントロールプレーン構成
Istio のコントロールプレーンを監視し、`mixer`、`galley`、`pilot`、`citadel` のメトリクスをレポートするには、`istiod` デプロイメントを監視するように Agent を構成する必要があります。Istio v1.5 以降では、`istio-system` ネームスペースにある `istiod` デプロイメントの以下のポッドアノテーションを適用してください。

```yaml
ad.datadoghq.com/discovery.checks: |
  {
    "istio": {
      "instances": [
        {
          "istiod_endpoint": "http://%%host%%:15014/metrics",
          "use_openmetrics": "true"
        }
      ]
    }
  }
```
**注**: Autodiscovery Annotations v2 の構文は Agent v7.36 以降でサポートされています。

このアノテーションは、このポッド内にある Istio コンテナのデフォルトコンテナ名に一致するコンテナ `discovery` を指定します。もし異なる場合は、このアノテーション `ad.datadoghq.com/<CONTAINER_NAME>.checks` を、お使いの Istio コンテナの名前 (`.spec.containers[i].name`) に置き換えてください。

これらのアノテーションの適用方法は、使用する [Istio のデプロイメント戦略 (Istioctl、Helm、Operator)][12] によって異なります。ポッドアノテーションの適用方法については、Istio のドキュメントを参照してください。利用可能なすべての構成オプションについては、[サンプルの istio.d/conf.yaml][11] を参照してください。

#### Datadog Agent ポッドのサイドカーインジェクションを無効化

[コンテナに Datadog Agent][13] をインストールする場合は、その前に Istio のサイドカーインジェクションを無効にすることをお勧めします。

_Istio バージョン >= 1.10:_

`sidecar.istio.io/inject: "false"` **ラベル**を `datadog-agent` DaemonSet に追加します。

```yaml
# (...)
spec:
  template:
    metadata:
      labels:
        sidecar.istio.io/inject: "false"
    # (...)
```

これは、`kubectl patch` コマンドでも実行できます。

```shell
kubectl patch daemonset datadog-agent -p '{"spec":{"template":{"metadata":{"labels":{"sidecar.istio.io/inject":"false"}}}}}'
```

_Istio バージョン <= 1.9:_

`sidecar.istio.io/inject: "false"` **アノテーション**を `datadog-agent` DaemonSet に追加します。

```yaml
# (...)
spec:
  template:
    metadata:
      annotations:
        sidecar.istio.io/inject: "false"
    # (...)
```

`kubectl patch` コマンドを使用します。

```shell
kubectl patch daemonset datadog-agent -p '{"spec":{"template":{"metadata":{"annotations":{"sidecar.istio.io/inject":"false"}}}}}'
```

#### ログ収集

_Agent バージョン 6.0 以降で利用可能_

まず、 Kubernetes でログ収集を実行するために Datadog Agent を有効にします。[Kubernetes ログ収集][14]を参照してください。

#### Istio のログ

コントロールプレーン (`istiod`) から Istio のログを収集するには、`istio-system` ネームスペース内のデプロイメント `istiod` に対して、以下のポッドアノテーションを適用してください。

```yaml
ad.datadoghq.com/discovery.logs: |
  [
    {
      "source": "istio",
      "service": "<SERVICE_NAME>"
    }
  ]
```

このアノテーションは、このポッド内にある Istio コンテナのデフォルトコンテナ名に一致するコンテナ `discovery` を指定します。もし異なる場合は、このアノテーション `ad.datadoghq.com/<CONTAINER_NAME>.logs` を、お使いの Istio コンテナの名前 (`.spec.containers[i].name`) に置き換えてください。

`<SERVICE_NAME>` を、希望する Istio サービス名に置き換えてください。

#### Envoy のアクセスログ

データプレーン (`istio-proxy`) から Envoy のアクセスログを収集するには、以下の手順を実行してください。

1. [Istio 内での Envoy アクセスロギング][15]を有効にします。
2. `istio-proxy` コンテナが注入されたポッドに、以下のアノテーションを適用してください。

```yaml
ad.datadoghq.com/istio-proxy.logs: |
  [
    {
      "source": "envoy",
      "service": "<SERVICE_NAME>"
    }
  ]
```

このアノテーションは、注入された Istio サイドカーコンテナのデフォルトコンテナ名に一致するコンテナ `istio-proxy` を指定します。もし異なる場合は、このアノテーション `ad.datadoghq.com/<CONTAINER_NAME>.logs` を、お使いの Istio サイドカーコンテナの名前 (`.spec.containers[i].name`) に置き換えてください。

`<SERVICE_NAME>` を、希望する Istio プロキシサービス名に置き換えてください。

### 検証

[Agent の `info` サブコマンドを実行][16]し、Checks セクションで `istio` を探します。

## 収集データ

### メトリクス
{{< get-metrics-from-git "istio" >}}


### イベント

Istio チェックには、イベントは含まれません。

### サービスチェック
{{< get-service-checks-from-git "istio" >}}


## トラブルシューティング

### 無効なチャンク長のエラー
Istio インテグレーション (Istio インテグレーションバージョン `3.13.0` 以前) のレガシーモードで以下のエラーが表示された場合

```python
  Error: ("Connection broken: InvalidChunkLength(got length b'', 0 bytes read)",
  InvalidChunkLength(got length b'', 0 bytes read))
```

このエラーを解決するには、OpenMetrics ベースの Istio インテグレーションの最新モードを使用できます。

最低でも Agent `7.31.0` と Python 3 にアップグレードする必要があります。OpenMetrics を有効にするには、[構成](#configuration)のセクションを参照してください。

### Istio のデプロイメントで一般的な OpenMetrics インテグレーションを使用する

Istio のプロキシサイドカーインジェクションが有効な場合、`istio_mesh_endpoint` と同じメトリクスエンドポイントで [OpenMetrics インテグレーション][19]を使用して他の Prometheus メトリクスを監視すると、カスタムメトリクス使用量が多く、メトリクス収集が二重になることがあります。

OpenMetrics の構成により、メトリクスの収集が重複しないようにするためには、以下のどちらかを行います。

1. 構成オプション `metrics` で特定のメトリクスのマッチングを使用するか、または
2. `metrics` にワイルドカード `*` 値を使用する場合、以下の OpenMetrics インテグレーションオプションを使用して、Istio および Envoy インテグレーションで既にサポートされているメトリクスを除外することを検討します。

#### 汎用的なメトリクス収集を使用した OpenMetrics の最新モード構成

カスタムメトリクスの高額請求を避けるため、必ず Istio と Envoy のメトリクスを構成から除外してください。`openmetrics_endpoint` が有効になっている場合は `exclude_metrics` を使用します。

```yaml
## 各インスタンスは、他のインスタンスとは独立してスケジュールされます。
#
instances:
  - openmetrics_endpoint: <OPENMETRICS_ENDPOINT>
    metrics:
    - '.*'
    exclude_metrics:
      - istio_.*
      - envoy_.*

```

#### 汎用的なメトリクス収集を使用した OpenMetrics のレガシーモード構成

カスタムメトリクスの高額請求を避けるため、必ず Istio と Envoy のメトリクスを構成から除外してください。`prometheus_url` が有効になっている場合は `ignore_metrics` を使用します。

```yaml
instances:
  - prometheus_url: <PROMETHEUS_URL>
    metrics:
      - '*'
    ignore_metrics:
      - istio_*
      - envoy_*
```

ご不明な点は、[Datadog のサポートチーム][20]までお問い合わせください。

## その他の参考資料

お役に立つドキュメント、リンクや記事:

- [Datadog を使用した Istio サービスメッシュの監視][21]
- [Datadog でキーメトリクスを収集して Istio を監視する方法][22]
- [Datadog を使用した Istio の監視方法][3]

[1]: https://www.datadoghq.com/blog/monitor-istio-with-npm/
[2]: https://docs.datadoghq.com/ja/tracing/setup_overview/proxy_setup/?tab=istio
[3]: https://www.datadoghq.com/blog/istio-datadog/
[4]: https://docs.datadoghq.com/ja/containers/kubernetes/integrations/
[5]: https://docs.datadoghq.com/ja/containers/docker/integrations/
[6]: https://docs.datadoghq.com/ja/integrations/guide/versions-for-openmetrics-based-integrations
[7]: https://app.datadoghq.com/account/settings/agent/latest
[8]: https://github.com/DataDog/integrations-core/tree/master/envoy#istio
[9]: https://istio.io/latest/docs/ops/deployment/architecture/
[10]: https://github.com/DataDog/integrations-core/blob/master/istio/datadog_checks/istio/data/auto_conf.yaml
[11]: https://github.com/DataDog/integrations-core/blob/master/istio/datadog_checks/istio/data/conf.yaml.example
[12]: https://istio.io/latest/docs/setup/install/
[13]: https://docs.datadoghq.com/ja/agent/kubernetes/
[14]: https://docs.datadoghq.com/ja/agent/kubernetes/log/
[15]: https://istio.io/latest/docs/tasks/observability/logs/access-log/
[16]: https://docs.datadoghq.com/ja/agent/guide/agent-commands/#agent-status-and-information
[17]: https://github.com/DataDog/integrations-core/blob/master/istio/metadata.csv
[18]: https://github.com/DataDog/integrations-core/blob/master/istio/assets/service_checks.json
[19]: https://docs.datadoghq.com/ja/integrations/openmetrics/
[20]: https://docs.datadoghq.com/ja/help/
[21]: https://www.datadoghq.com/blog/monitor-istio-with-datadog
[22]: https://www.datadoghq.com/blog/istio-metrics/