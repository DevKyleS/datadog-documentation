---
title: Watchdog Explains
aliases:
    - /graphing/correlations/
    - /dashboards/correlations/
further_reading:
- link: "/watchdog/insights/"
  tag: "Documentation"
  text: "Learn more about Watchdog Insights"
- link: "https://www.datadoghq.com/blog/ai-powered-metrics-monitoring/"
  tag: "Blog"
  text: Anomaly detection, predictive correlations - Using AI-assisted metrics monitoring
---

## Overview

<div class="alert alert-info">Watchdog Explains is available for <a href="https://docs.datadoghq.com/dashboards/widgets/timeseries/">Timeseries widgets</a> with the <strong>Metric</strong> data source.</div>

Watchdog Explains is an investigation assistant that guides you to the root cause of anomalies on timeseries graphs.

Watchdog Explains makes investigations more efficient by automatically showing which individual tags could be responsible for a given spike. This allows you to focus your investigation on problematic areas of your infrastructure or software stack.

To disable Watchdog Explains, see [Disabling anomaly detection](#disabling-anomaly-detection).

## Detecting anomalies with seasonality awareness

Watchdog Explains applies anomaly detection to graphs on your dashboard by analyzing both the shape and value of the underlying timeseries. It identifies deviations from historical patterns, flagging spikes, dips, or gradual drifts that don't align with expected behavior.

To account for seasonality, the algorithm looks back up to three weeks in time. For example, if a spike appears on a Monday at 9:00 a.m., Watchdog compares that datapoint against previous Mondays at the same hour. If similar patterns appear consistently, the spike is treated as **seasonal** and not flagged as an anomaly. This helps reduce false positives and ensures that only unexpected deviations are surfaced.

Anomalies can be sharp spikes or drops, but may also be more subtle trends like step changes or slope shifts.

<div class="alert alert-info">Anomaly detection in Watchdog Explains works with <strong>Metrics data</strong> (avg, sum, min, and max aggregation).</div>

## Investigating anomalies

{{< img src="/dashboards/graph_insights/watchdog_explains/investigate_anomaly.png" alt="Watchdog Explains investigation view showing anomaly detection on a dashboard" style="width:100%;" >}}

You can start your investigation from any timeseries graph that uses metric data. When Watchdog Explains detects an anomaly, it highlights the affected region with a pink box. To begin investigating, click **Investigate Anomaly**.

This opens a full-screen investigation view. Watchdog analyzes the anomaly and surfaces any tag groups that significantly contributed to the shape or scale of the anomaly. Click on a tag to see how removing or isolating that dimension affects the graph. Use this to identify root causes like specific customers, services, or environments.

## Isolating the cause with tag breakdown

{{< img src="/dashboards/graph_insights/watchdog_explains/tag_breakdown.png" alt="Tag breakdown visualization in Watchdog Explains" style="width:100%;" >}}

Watchdog Explains analyzes the tag dimensions of the query to determine what may have caused the shift. It re-runs the same graph query filtered by each tag group (such as service, availability zone, or customer tier) to isolate the segments most likely responsible.

By comparing the shape of the filtered timeseries with the overall graph, Watchdog Explains can infer which tag group had the greatest impact.

When a tag is flagged, it means filtering by that tag significantly reduced or reshaped the anomaly. Watchdog compares the filtered timeseries to the original graph to pinpoint which tags contributed to the anomaly most. Sometimes, an anomaly may not have an obvious root cause. If no tag group meaningfully contributes to the spike or dip, Watchdog Explains may surface a general anomaly without tag-level attribution.

## Example: Surge in traffic from a single tenant

A sudden spike in traffic is detected in the `shopist-returns` service. Watchdog surfaces a sharp increase in request volume, highlighted on the graph. Upon investigation, it attributes the anomaly to a single tag group: `org_id:17728`

{{< img src="/dashboards/graph_insights/watchdog_explains/investigating_surge_traffic.mp4" alt="Video showing Watchdog Explains investigating a surge in traffic from a single tenant" video=true >}}

Further analysis reveals:
* The spike originated entirely from that one customer.
* The traffic volume overwhelmed the `rdv.shopist.io` data center.
* This incident exposed a gap in **tenant isolation** and **traffic governance** mechanisms.

In this example, Watchdog's tag breakdown makes it clear that this was not a general usage trend, but rather an infrastructure risk caused by one high-volume tenant.

## Disabling anomaly detection

<div class="alert alert-info">You can disable anomaly scanning on any dashboard. This only affects your view, other dashboard viewers still see anomalies unless they turn it off.
</div>

{{< img src="/dashboards/graph_insights/watchdog_explains/disable_anomaly_detection.png" alt="Disabling anomaly detection in Watchdog Explains" style="width:100%;" >}}

To disable anomaly detection on a dashboard, open **Anomalies** at the top of the dashboard and click **Turn Off**.

## Further reading

{{< partial name="whats-next/whats-next.html" >}}
