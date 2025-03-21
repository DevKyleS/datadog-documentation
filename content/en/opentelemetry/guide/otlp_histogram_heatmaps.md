---
title: Visualize OTLP Histograms as Heatmaps
further_reading:
- link: "/metrics/open_telemetry/otlp_metric_types"
  tag: "Documentation"
  text: "OTLP Metric Types"
- link: "/opentelemetry/"
  tag: "Documentation"
  text: "OpenTelemetry Support in Datadog"
---

## Overview

The OpenTelemetry Protocol (OTLP) supports sending OTLP Histograms, a type of metric that compresses information about a set of measurements by providing aggregate statistics, like sum, count, min, and max. OTLP Histograms also count how many of these measurements fall into user-configurable buckets.

You can visualize this datatype as a [heatmap][5] in Datadog by following the steps on this page.

**Note**: The related OTLP Exponential Histogram type can also be displayed as a heatmap, since it is converted to a distribution. Read more about distributions on the [dedicated Distributions page][4].

## Setup

This guide assumes you already have a [functioning setup for sending OpenTelemetry metrics to Datadog][1].

### OpenTelemetry SDK configuration

If you are producing metrics from an OpenTelemetry SDK, take the following steps to configure them:

1. [Configure the SDK you are sending OTLP Histograms with delta temporality][2]. This makes the minimum and maximum available for the heatmap widget.
2. Check if you want to override the [default bucket boundaries][3] from your aggregation. **Note**: Each additional bucket is considered a separate custom metric.

For metrics coming from other sources, ensure if possible that these come as delta OTLP Histograms with the minimum and maximum fields set.

### Datadog Exporter or Datadog Agent configuration

Set the histogram mode and enable aggregation metrics on your Datadog Exporter or Datadog Agent.

{{< tabs >}}
{{% tab "Datadog Exporter (OpenTelemetry Collector)" %}}

In the `collector.yaml` file for the Datadog Exporter, configure the histogram mode to `counters` and enable aggregation metrics with the `send_aggregation_metrics` flag.

```yaml
exporters:
  datadog:
    metrics:
      histograms:
        mode: counters
        send_aggregation_metrics: true
```

**Note**: `send_aggregation_metrics` is available starting with Datadog Exporter v0.75.0. If you are using an earlier version, use the `send_count_sum_metrics` flag instead. The minimum and maximum are missing in earlier versions.

{{% /tab %}}
{{% tab "Datadog Agent" %}}

On the `otlp_config` section, configure the histogram mode to `counters` and enable aggregation metrics with the `send_aggregation_metrics` flag.

```yaml
otlp_config:
  metrics:
    histograms:
      mode: counters
      send_aggregation_metrics: true
```

**Note**: `send_aggregation_metrics` is available starting on the Datadog Agent v6.45.0/v7.45.0. If you are using an earlier version, use the `send_count_sum_metrics` flag instead. The minimum and maximum are missing in earlier versions.

{{% /tab %}}
{{< /tabs >}}


### Heatmap widget configuration

The [heatmap widget][5] uses the set of `<YOUR METRIC NAME>.bucket` metrics generated by the Datadog Exporter or Datadog Agent, each corresponding to a different histogram bucket. To visualize your histogram as a heatmap:

1. Select `<YOUR METRIC NAME>.bucket` as the metric to visualize.
2. Choose the `pre-binned data` option on the `distributions of` menu.

You can now see your OTLP Histogram as a heatmap widget.

## OpenMetrics compatibility

The [Datadog Agent OpenMetrics check][6] is also compatible with the pre-binned data heatmap widget option. If you want to send metrics to the OpenMetrics check directly without converting to OpenTelemetry, enable the `collect_histogram_buckets` and `non_cumulative_histogram_buckets` flags on your instance to ensure the data is sent in a compatible way to Datadog.

## Further reading

{{< partial name="whats-next/whats-next.html" >}}


[1]: /opentelemetry/otel_metrics
[2]: /opentelemetry/guide/otlp_delta_temporality
[3]: https://opentelemetry.io/docs/reference/specification/metrics/sdk/#explicit-bucket-histogram-aggregation
[4]: /metrics/distributions
[5]: /dashboards/widgets/heatmap
[6]: /integrations/openmetrics
