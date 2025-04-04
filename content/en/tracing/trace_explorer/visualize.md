---
title: Span Visualizations
description: 'View spans in a list, or aggregate spans into timeseries, top lists and more.'
aliases:
 - /tracing/trace_search_and_analytics/request_flow_map
 - /tracing/trace_explorer/request_flow_map/
further_reading:
    - link: 'tracing/trace_explorer/'
      tag: 'Documentation'
      text: 'Trace Explorer'
    - link: 'https://www.datadoghq.com/blog/apm-request-flow-map-datadog'
      tag: 'Blog'
      text: 'Learn more about Request Flow Maps'
---

## Overview

Visualizations define how the queried span data is displayed. Select relevant visualizations to surface valuable information, such as a **list** for individual events, or as **timeseries** or **top lists** for aggregates. 

## List view

The list view displays a list of spans that match the selected context, defined by the [search bar query][1] filter and a [time range][2].

In the table, choose which information of interest to display as columns. Manage the columns by either:

- interacting with the table header row to **sort**, **rearrange**, or **remove** columns.
- selecting a facet from the facet panel on the left, or from the trace side panel after clicking on a specific span, to **add** a column for a field. You can also add columns from with the **Options** button.

{{< img src="tracing/trace_explorer/visualize/list_view_table_controls.mp4" alt="Configuring the display table" video=true style="width:80%;">}}

The default sort for spans in the list visualization is by timestamp, with the most recent spans on top. To surface spans with lowest or highest value for a measure first, or to sort your spans lexicographically for the value of a tag, specify that column as the **by** column.


The configuration of the columns is stored alongside other elements of your troubleshooting context in saved views.

The `Latency Breakdown` of the trace might be missing for some spans if the trace is malformed or incomplete. For instance, the error and the rare samplers capture pieces of traces, without the guarantee of capturing the complete trace. In this case, the data is omitted to avoid displaying inconsistent or misleading latency information that would only make sense when the trace is complete.

When the query is filtered on error spans, select the **Group into Issues** option to visualize a list of [Error Tracking][5] issues instead of individual error spans. Click on any issue in the issue list to open the issue panel and access additional information about this group of errors.

{{< img src="tracing/trace_explorer/visualize/trace_explorer_issue_grouping.png" alt="Error Tracking Issue Grouping" style="width:100%;">}}

From the issue details, click `See all errors` to view individual error spans grouped under this issue.

**Note**:Switch back to the `Errors` grouping to view individual errors, including non fingerprinted errors, i.e. errors without associated issue.

## Timeseries

Use timeseries to visualize the evolution of a [measure][3] (or a count of unique tag values) over a selected time frame, and optionally split the data by up to three tags (grouping).

**Note**: The [Live Explorer][4] (15 minutes) allows grouping by only one dimension.

Aggregated views use additional query options, to define the **measured tag dimension**, the dimensions to **group** the query by, and the **aggregation period**. For example:

1. Choose to view the `Duration` measure.

   {{< img src="tracing/trace_explorer/visualize/group_by_measured_dimension.png" alt="Measured Dimension" style="width:100%;">}}

2. Select the aggregation function for the `Duration` measure. Selecting a measure lets you choose the aggregation function whereas selecting a qualitative attribute displays the unique count.

   {{< img src="tracing/trace_explorer/visualize/group_by_aggregation_function.png" alt="Aggregation Function" style="width:100%;">}}

3. Group the query by a dimension, for example, `Resource`.

   {{< img src="tracing/trace_explorer/visualize/group_by_dimension.png" alt="Split Dimension" style="width:100%;">}}

4. Choose to display a number of either top or bottom values according to the selected tag.

    {{< img src="tracing/trace_explorer/visualize/group_by_top_bottom.png" alt="Top Bottom X values" style="width:100%;">}}

5. Choose the rollup period, for example, `10min`.

    {{< img src="tracing/trace_explorer/visualize/group_by_rollup_period.png" alt="Rollup Period" style="width:100%;">}}

The following Trace Explorer timeseries view shows the evolution of the top ten resource names of the service `shopist-web-ui` according to the 95th percentile of `Duration` over the past four hours:

{{< img src="tracing/trace_explorer/visualize/timeseries_view.png" alt="Timeseries view" style="width:100%;">}}

Choose additional display options for timeseries: the **roll-up interval**, whether you **display** results as **bars** (recommended for counts and unique counts), **lines** (recommended for statistical aggregations) or **areas**, and the **colorset**.

## Top list

Use a top list to visualize a span count, a count of unique tag values, or a measure split by one tag dimension.

For example, the following top list shows the top ten website customers that experienced an error at checkout over the past day, based on the span count.

{{< img src="tracing/trace_explorer/visualize/top_list_view.png" alt="Top list view" style="width:100%;">}}

## Table

Use a table to visualize the top values from up to three dimension combinations according to a chosen measure or span count.

**Note**: A table visualization grouped by a single dimension is the same as a Top List, just with a different display.

The following table shows the error spans count by `Env`, `Service`, and `Error type`.

{{< img src="tracing/trace_explorer/visualize/table_view.png" alt="Table view" style="width:100%;">}}

## Request Flow Map

[Request flow maps][6] combine APM's [service map][7] and [live exploring][8] features to illustrate request paths through your stack. Scope your traces to any combination of tags and generate a dynamic map that represents the flow of requests between every service.

{{< img src="tracing/live_search_and_analytics/request_flow_map/Overview.png" style="width:100%; background:none; border:none; box-shadow:none;" alt="Request flow map showing the flow of requests between services, as well as request times and error rates" >}}

For example, you can use request flow maps to identify high-traffic services or track the number of database calls generated by a request to a specific endpoint. If you use [shadow deployments][9] or feature flags set as custom span tags, you can use request flow maps to compare request latencies between requests and anticipate how code changes will impact perforamnce. 

### Navigating the request flow map

- Hover over the edge that connects two services to see metrics for requests, errors, and latencies between those services. **Note**: Highlighted edges represent the highest throughput connections, or the most common paths.

- Click **Export** to save a PNG image of the current request flow map. Use this feature to generate a live architecture diagram or one scoped to a specific user flow.

- Click any service on the map to view health, performance, infrastructure, and runtime metrics for that service.

{{< img src="tracing/live_search_and_analytics/request_flow_map/ServicePanel.png" style="width:100%; background:none; border:none; box-shadow:none;" alt="Request flow map side panel with metrics and metadata for the selected service" >}}

- The map automatically selects an appropriate layout based on the number of services present. Click **Cluster** or **Flow** to switch between the layouts.

- RUM Applications are represented on the request flow map if you have [connected RUM and Traces][10].

{{< img src="tracing/live_search_and_analytics/request_flow_map/RUMService.mp4" alt="A video showing how to navigate into the service details of a RUM application in the request flow map" video=true style="width:100%;">}}


## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /tracing/trace_explorer/query_syntax/#search-syntax
[2]: /tracing/trace_explorer/query_syntax/#time-range
[3]: /tracing/trace_explorer/facets/#quantitative-facets-measures
[4]: /tracing/trace_explorer/?tab=timeseriesview#live-search-for-15-minutes
[5]: /tracing/error_tracking/
[6]: https://app.datadoghq.com/apm/flow-map
[7]: /tracing/services/services_map/
[8]: /tracing/trace_explorer/
[9]: /tracing/services/deployment_tracking/#shadow-deploys
[10]: /real_user_monitoring/correlate_with_other_telemetry/apm?tab=browserrum
