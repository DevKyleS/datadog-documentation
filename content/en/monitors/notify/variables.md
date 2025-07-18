---
title: Variables
description: "Use variables to customize your monitor notifications"
further_reading:
- link: "/monitors/guide/template-variable-evaluation/"
  tag: "Guide"
  text: "Perform arithmetic operations and functions with template variable evaluations"
- link: "/monitors/"
  tag: "Documentation"
  text: "Create monitors"
- link: "/monitors/notify/"
  tag: "Documentation"
  text: "Monitor notifications"
- link: "/monitors/manage/"
  tag: "Documentation"
  text: "Manage monitors"
- link: "https://learn.datadoghq.com/courses/alert-monitor-notifications"
  tag: "Learning Center"
  text: "Take a course to customize alert monitor notifications"
- link: "https://www.datadoghq.com/blog/monitor-notification-rules/"
  tag: "Blog"
  text: "Route your monitor alerts with Datadog monitor notification rules"
---

Use variables in notification messages to display conditional messaging and route notification to different teams using [conditional variables](#conditional-variables), or to enrich its content by using [attribute and tag variables](#attribute-and-tag-variables) and [template variables](#template-variables).

## Conditional variables

Conditional variables use `if-else` logic to display a different message depending on the state of the monitor and the details of how it was triggered. These variables can be used within the subject or body of the notification message.

The following conditional variables are available:

| Conditional Variable       | The text is displayed if                                           |
|----------------------------|--------------------------------------------------------------------|
| `{{#is_alert}}`            | The monitor alerts                                                 |
| `{{^is_alert}}`            | The monitor does not alert                                         |
| `{{#is_match}}`            | The context matches the provided substring. If a numeric value is used, it is converted to a string.|
| `{{^is_match}}`            | The context does not match the provided substring                  |
| `{{#is_exact_match}}`      | The context exactly matches the provided string.<br> If a number is used, the numeric value is considered, regardless of its type. This means that as long as two numbers have the same value, they are considered equal by the function. |
| `{{^is_exact_match}}`      | The context does not exactly match the provided string             |
| `{{#is_no_data}}`          | The monitor is triggered for missing data                          |
| `{{^is_no_data}}`          | The monitor is not triggered for missing data                      |
| `{{#is_warning}}`          | The monitor warns                                                  |
| `{{^is_warning}}`          | The monitor does not warn                                          |
| `{{#is_recovery}}`         | The monitor recovers from `ALERT`, `WARNING`, `UNKNOWN`, or `NO DATA`         |
| `{{^is_recovery}}`         | The monitor does not recover from `ALERT`, `WARNING`, `UNKNOWN`, or `NO DATA` |
| `{{#is_warning_recovery}}` | The monitor recovers from `WARNING` to `OK`                        |
| `{{^is_warning_recovery}}` | The monitor does not recover from `WARNING` to `OK`                |
| `{{#is_alert_recovery}}`   | The monitor recovers from `ALERT` to `OK`                          |
| `{{^is_alert_recovery}}`   | The monitor does not recover from an ALERT to OK                   |
| `{{#is_alert_to_warning}}` | The monitor transitions from `ALERT` to `WARNING`                  |
| `{{^is_alert_to_warning}}` | The monitor does not transition from `ALERT` to `WARNING`          |
| `{{#is_no_data_recovery}}` | The monitor recovers from `NO DATA`                                |
| `{{^is_no_data_recovery}}` | The monitor does not recover from `NO DATA`                        |
| `{{#is_priority 'value'}}` | The monitor has priority `value`. Value ranges from `P1` to `P5`   |
| `{{#is_unknown}}`          | The monitor is in the unknown state                                |
| `{{^is_unknown}}`          | The monitor is not in the unknown state                            |
| `{{#is_renotify}}`         | The monitor is renotifying                                         |
| `{{^is_renotify}}`         | The monitor is not renotifying.                                    |

### Examples

Conditional variable must have an opening and closing pair with the text and **@-notifications** in-between.

{{< tabs >}}
{{% tab "is_alert" %}}

To send a notification message when a monitor alerts, use the format:

```text
{{#is_alert}}
  <ALERT_MESSAGE_TEXT> <@-NOTIFICATION>
{{/is_alert}}
```

{{% /tab %}}
{{% tab "is_warning" %}}

To send a notification message when a monitor warns, use the format:

```text
{{#is_warning}}
  <WARNING_MESSAGE_TEXT> <@-NOTIFICATION>
{{/is_warning}}
```

{{% /tab %}}
{{% tab "is_recovery" %}}

To send a notification message when a monitor recovers, use the format:

```text
{{#is_recovery}}
  <RECOVERY_MESSAGE_TEXT> <@-NOTIFICATION>
{{/is_recovery}}
```

{{% /tab %}}
{{% tab "is_match" %}}

Search for a substring in a [tag variable](#attribute-and-tag-variables) with the format:

```text
{{#is_match "<TAG_VARIABLE>.name" "<COMPARISON_STRING>"}}
  This displays if <COMPARISON_STRING> is included in <TAG_VARIABLE>.
{{/is_match}}
```

To notify your DB team if a triggering host has the tag `role:db_cassandra` or `role:db_postgres`, use the following:

```text
{{#is_match "role.name" "db"}}
  This displays if the host triggering the alert contains `db`
  in the role name. @db-team@company.com
{{/is_match}}
```

The `is_match` condition also supports matching multiple strings:

```text
{{#is_match "role.name" "db" "database"}}
  This displays if the host triggering the alert contains `db` or `database`
  in the role name. @db-team@company.com
{{/is_match}}
```

To send a different notification if the tag doesn't contain `db`, use the negation of the condition as follows:

```text
{{^is_match "role.name" "db"}}
  This displays if the role tag doesn't contain `db`.
  @slack-example
{{/is_match}}
```

Or use the `{{else}}` parameter in the first example:

```text
{{#is_match "role.name" "db"}}
  This displays if the host triggering the alert contains `db`
  in the role name. @db-team@company.com
{{else}}
  This displays if the role tag doesn't contain `db`.
  @slack-example
{{/is_match}}
```
**Note**: To check if a `<TAG_VARIABLE>` does not exist or if it's empty, use `is_exact_match`. See `is_exact_match` tab for more details. 

{{% /tab %}}
{{% tab "is_exact_match" %}}

Search for an exact string in a [tag variable](#attribute-and-tag-variables) with the format:

```text
{{#is_exact_match "<TAG_VARIABLE>.name" "<COMPARISON_STRING>"}}
  This displays if <COMPARISON_STRING> is exactly <TAG_VARIABLE>.
{{/is_exact_match}}
```

To notify your dev team if a triggering host has the name `production`, use the following:

```text
{{#is_exact_match "host.name" "production"}}
  This displays if the host that triggered the alert is exactly
  named production. @dev-team@company.com
{{/is_exact_match}}
```

The `is_exact_match` condition also supports matching multiple strings:

```text
{{#is_exact_match "host.name" "production" "staging"}}
  This displays if the host that triggered the alert is exactly
  named production or staging. @dev-team@company.com
{{/is_exact_match}}
```

The `is_exact_match` conditional variable also supports [`{{value}}` template variables](#template-variables):

```text
{{#is_exact_match "value" "<VALUE>"}}
  This displays if the value that breached the threshold of the monitor is exactly <VALUE>.
{{/is_exact_match}}
```

To notify your dev team if the value that breached the threshold of your monitor is 5 (or 5.0), use the following:

```text
{{#is_exact_match "value" "5"}}
  This displays if the value that breached the threshold of the monitor is 5. @dev-team@company.com
{{/is_exact_match}}
```

The `is_exact_match` conditional variable also supports an empty string for the `<COMPARISON_STRING>` to check if the attribute or tag is empty or does not exist.
```text
{{#is_exact_match "host.datacenter" ""}}
  This displays if the attribute or tag does not exist or if it's empty
{{/is_exact_match}}
```


{{% /tab %}}
{{% tab "is_renotify" %}}

To send an escalation message to a different destination just for the `production` environment:

```text
{{#is_renotify}}
{{#is_match "env" "production"}}
  This is an escalation message sent to @dev-team@company.com
{{/is_match}}
{{/is_renotify}}
```

To send a different escalation message that does not contain the original message details, use a combination of `{{^is_renotify}}` and `{{#is_renotify}}` blocks:

```text
{{^is_renotify}}
This monitor is alerting and sending a first message @dev-team@company.com

To solve this monitor follow the steps:
1. Go there
2. Do this
{{/is_renotify}}

This part is generic and sent both for the first trigger and the escalation message.

{{#is_renotify}}
  This is the escalation message @dev-team@company.com
{{/is_renotify}}

```

On monitor renotification, users will get the following escalation message:

```
This part is generic and sent both for the first trigger and the escalation message.

This is the escalation message @dev-team@company.com
```

{{% /tab %}}
{{< /tabs >}}

If you configure a conditional block for a state transition into `alert` or `warning` conditions with an **@-notifications** handle, it is recommended to configure a corresponding `recovery` condition in order for a recovery notification to be sent to the handle.

**Note**: Any text or notification handle placed **outside** the configured conditional variables is invoked with every monitor state transition. Any text or notification handle placed **inside** of configured conditional variables is only invoked if the monitor state transition matches its condition.

## Attribute and tag variables

Use attribute and tag variables to render alert messages that are customized, informative, and specific to help understand the nature of the alert. See the following sections for examples and use cases:
- [Multi alert variables](#multi-alert-variables)
- [Matching attribute/tag variables](#matching-attributetag-variables)

Tags
: Automatically attached (such as host name, container name, log filename, and serverless function name) or added through custom tags (such as team in charge, environment, application, or version).

Attributes
: Based on the log content and either parsed or added by lookups from reference tables (for example, geoip).

**Note**: If the monitor is configured to recover in no-data conditions (for example, when there are no events matching the query), the recovery message doesn't contain any data. To persist information in the recovery message, group by additional tags, which are accessible by `{{tag.name}}`.

### Multi alert variables

Configure multi alert variables in [multi alert monitors][1] based on the dimension selected in the multi alert group box. Enrich notifications by dynamically including the value associated with the group-by dimension in each alert. 

**Note**: When you use the `group_by` field in aggregation, additional tags and alerts from the monitor may be inherited automatically. This means that any alerts or configurations set on the monitored endpoint could be applied to each group resulting from the aggregation.

{{< tabs >}}
{{% tab "Group by tag" %}}

If a metric is tagged with any tag in the `key:value` format and the monitor query is grouped by this tag, use the variable:

```
{{ key.name }}
```

This variable inserts the `value` associated with the `key` into each alert notification. For example, if your monitor triggers an alert for each `env`, then the variable `{{env.name}}` is available in your notification message.

If a group has multiple `values` associated with the same `key`, the alert message displays a comma-separated string of all values, in lexicographic order.

#### Tag key with period

If your tag's key has a period in it, include brackets around the full key when using a tag variable. For example, if your tag is `dot.key.test:five` and your monitor is grouped by `dot.key.test`, use:

```text
{{[dot.key.test].name}}
```

{{% /tab %}}

{{% tab "Group by facet" %}}

Log monitors, Trace Analytics monitors, RUM monitors and Event monitors can use facets as variables if the monitor is grouped by facet. If a log monitor is grouped by `@facet_key`, use the variable:

```text
{{ @facet_key.name }}
```

**Example**: To include group-specific information in a multi alert log monitor group by `@machine_id`:

```text
This alert was triggered on {{ @machine_id.name }}
```

If your facet has periods, use brackets around the facet, for example:

```text
{{ [@network.client.ip].name }}
```

{{% /tab %}}
{{< /tabs >}}

#### Customize the notification based on the group

When your query is grouped by specific dimensions, you can enrich notifications with dynamic metadata associated with the group. To see a list of tag variables based on your tag selection, click **Use message template variables** in the **Configure notifications & automations** section. See the following examples:

{{% collapse-content title="Query group by host" level="h5" %}}

If your monitor triggers an alert for each `host`, then the tag variables `{{host.name}}` and `{{host.ip}}` are available as well as any host tag that is available on this host.

Specific host metadata variables:

- Agent Version: `{{host.metadata_agent_version}}`
- Machine: `{{host.metadata_machine}}`
- Platform: `{{host.metadata_platform}}`
- Processor: `{{host.metadata_processor}}`
{{% /collapse-content %}}

{{% collapse-content title="Query group by kube_namespace and kube_cluster_name" level="h5" %}}
If your monitor triggers an alert for each `kube_namespace` and `kube_cluster_name`, then you can access any attribute of the namespace.

Namespace metadata variables:

- Cluster name: `{{kube_namespace.cluster_name}}`
- Namespace name: `{{kube_namespace.display_name}}`
- Namespace status: `{{kube_namespace.status}}`
- Namespace labels: `{{kube_namespace.labels}}`

The following table contains all available attributes:

| Variable syntax   | First level attributes |
|-------------------|------------------------|
| `{{kube_namespace.key}}`     | `k8s_namespace_key`, `tags`, `annotations`, `cluster_id`, `cluster_name`, `creation_timestamp`, `deletion_timestamp`, `display_name`, `external_id`, `finalizers`, `first_seen_at`, `group_size`, `labels`, `name`, `namespace`, `status`, `uid`|
{{% /collapse-content %}}

{{% collapse-content title="Query group by pod_name and kube_namespace and kube_cluster_name" level="h5" %}}
If your monitor triggers an alert for each `pod_name` and `kube_namespace` and `kube_cluster_name`, then you can access any attribute of the pod.

Pod metadata variables:
- Cluster name: `{{pod_name.cluster_name}}`
- Pod name: `{{pod_name.name}}`
- Pod phase: `{{pod_name.phase}}`

The following table contains all available attributes:

| Variable syntax   | First level attributes |
|-------------------|------------------------|
| `{{pod_name.key}}`     | `k8s_pod_key`, `tags`, `annotations`, `cluster_id`, `cluster_name`, `conditions`, `container_statuses`, `creation_timestamp`, `deletion_timestamp`, `display_name`, `external_id`, `finalizers`, `first_seen_at`, `host_id`, `host_key`, `hostname`, `init_container_statuses`, `ip`, `labels`, `name`, `namespace`, `node_name`, `nominated_node_name`, `phase`, `pod_scheduled_timestamp`, `priority_class_name`, `qosclass`, `resource_requirements`, `uid`|
{{% /collapse-content %}}


{{% collapse-content title="Query group by service" level="h5" %}}

If your monitor triggers an alert for each `service`, then you can access some attribute of the service, as defined in the [Software Catalog][10].

Service metadata variables:

- Service name: `{{service.name}}`
- Team name: `{{service.team}}`
- Docs: `{{service.docs}}`
- Links: `{{service.links}}`

For Docs and Links you can also access a specific item with the following syntax `[<name>]`. For example, for services that have a definition schema like the one defined in this [example][11], you can access the "Runbook" link using the following syntax

```text
{{service.links[Runbook]}}
```
{{% /collapse-content %}}



### Matching attribute/tag variables

<div class="alert alert-info">Available for
  <a href="/monitors/types/log/">Log monitors </a>,
  <a href="/monitors/types/apm/?tab=analytics">Trace Analytics monitors (APM)</a>,
  <a href="/monitors/types/error_tracking/"> Error Tracking monitors </a>,
  <a href="/monitors/types/real_user_monitoring/">RUM monitors </a>,
  <a href="/monitors/types/ci/">CI monitors </a>, and
  <a href="/monitors/types/database_monitoring/">Database Monitoring monitors</a>.
</div>

To include **any** attribute or tag from a log, a trace span, a RUM event, a CI pipeline, or a CI test event matching the monitor query, use the following variables:

| Monitor type    | Variable syntax                                  |
|-----------------|--------------------------------------------------|
| Log             | `{{log.attributes.key}}` or `{{log.tags.key}}`   |
| Trace Analytics | `{{span.attributes.key}}` or `{{span.tags.key}}` |
| Error Tracking  | `{{issue.attributes.key}}`                         |
| RUM             | `{{rum.attributes.key}}` or `{{rum.tags.key}}`   |
| Audit Trail     | `{{audit.attributes.key}}` or `{{audit.message}}`    |
| CI Pipeline     | `{{cipipeline.attributes.key}}`                  |
| CI Test         | `{{citest.attributes.key}}`                      |
| Database Monitoring | `{{databasemonitoring.attributes.key}}`      |

{{% collapse-content title="Example syntax usage" level="h4" %}}
- For any `key:value` pair, the variable `{{log.tags.key}}` renders `value` in the alert message.
- The `@` that prefixes all attributes is not included. For example, if a log monitor is grouped by `@http.status_code`, you can include the error message or infrastructure tags in the notification message by using the variables:

  ```text
  {{ log.attributes.[error.message] }}
  {{ log.tags.env }}
  ...
  ```

  {{< img src="monitors/notifications/tag_attribute_variables.png" alt="Matching attribute variable syntax" style="width:90%;">}}
- The message renders the `error.message` attribute of a chosen log matching the query, **if the attribute exists**.
- If the tag is on an event, use the following syntax:

  ```text
  {{ event.tags.[dot.key.test] }}
  ```


{{% /collapse-content %}}


#### Important notes

- If the selected event does not include the attribute or tag key, the variable renders empty in the notification message. To prevent missing notifications, avoid using these variables for routing notifications with `{{#is_match}}` handles.
- For monitors using Formulas & Functions in queries, values resolve based on events extracted from the first query.


#### Reserved attributes

Logs, Event Management, spans, RUM, CI Pipeline, and CI Test events have generic reserved attributes, which you can use in variables with the following syntax:

| Monitor type    | Variable syntax   | First level attributes |
|-----------------|-------------------|------------------------|
| Log             | `{{log.key}}`     | `message`, `service`, `status`, `source`, `span_id`, `timestamp`, `trace_id`, `link`, `host` |
| Trace Analytics | `{{span.key}}`    | `env`, `operation_name`, `resource_name`, `service`, `status`, `span_id`, `timestamp`, `trace_id`, `type`, `link` |
| RUM             | `{{rum.key}}`     | `service`, `status`, `timestamp`, `link` |
| Event             | `{{event.key}}`     | `attributes`, `host.name`, `id`, `link`, `title`, `text`, `tags` |
| CI Pipeline             | `{{cipipeline.key}}`     | `service`, `env`, `resource_name`, `ci_level`, `trace_id`, `span_id`, `pipeline_fingerprint`, `operation_name`, `ci_partial_array`, `status`, `timestamp`, `link` |
| CI Test             | `{{citest.key}}`     | `service`, `env`, `resource_name`, `trace_id`, `span_id`, `operation_name`, `status`, `timestamp`, `link` |

If the matching event does not contain the attribute in its definition, the variable is rendered empty.

#### Explorer link

Use `{{log.link}}`, `{{span.link}}`, `{{rum.link}}`, and `{{issue.link}}` to enrich the notification with a link to the Log Explorer, Trace Explorer, RUM Explorer, or Error Tracking, scoped on the events matching the query.

### Check monitor variables

For check monitor variables (custom check and integration check), the variable `{{check_message}}` is available and renders the message specified in the custom check or the integration check.

### Composite monitor variables

Composite monitors can access the value and status associated with the sub-monitors at the time the alert triggers.

For example, if your composite monitor has sub-monitor `a`, you can include the value of `a` with:

```text
{{ a.value }}
```

To retrieve the status of the sub-monitor `a` use:

```text
{{ a.status }}
```

Possible values for the status are: `OK`, `Alert`, `Warn`, and `No Data`.

Composite monitors also support tag variables in the same way as their underlying monitors. They follow the same format as other monitors, provided the underlying monitors are grouped by the same tag or facet.

For instance, assume your composite monitor has a sub-monitor `a`, which is a Logs monitor. You can include the value of any tag or facet of `a` with:

```text
{{ a.log.message }} or {{ a.log.my_facet }}
```

### Character escape

Variable content is escaped by default. To prevent content such as JSON or code from being escaped, use triple braces instead of double braces, for example: `{{{event.text}}}`.

## Template variables

Use template variables to customize your monitor notifications. The built-in variables are:

| Variable                             | Description                                                                   |
|-----------------------------------   |-------------------------------------------------------------------------------|
| `{{value}}`                          | The value that breached the alert for metric based query monitors.            |
| `{{threshold}}`                      | The value of the alert threshold set in the monitor's alert conditions.       |
| `{{warn_threshold}}`                 | The value of the warning threshold set in the monitor's alert conditions.     |
| `{{alert_recovery_threshold}}`       | The value that recovered the monitor from its `ALERT` state.                  |
| `{{warn_recovery_threshold}}`        | The value that recovered the monitor from its `WARN` state.                   |
| `{{ok_threshold}}`                   | The value that recovered the Service Check monitor.                           |
| `{{comparator}}`                     | The relational value set in the monitor's alert conditions.                   |
| `{{first_triggered_at}}`<br>*See section below*         | The UTC date and time when the monitor first triggered.                       |
| `{{first_triggered_at_epoch}}`<br>*See section below*   | The UTC date and time when the monitor first triggered in epoch milliseconds. |
| `{{last_triggered_at}}`<br>*See section below*          | The UTC date and time when the monitor last triggered.                        |
| `{{last_triggered_at_epoch}}`<br>*See section below*    | The UTC date and time when the monitor last triggered in epoch milliseconds.  |
| `{{triggered_duration_sec}}`         | The number of seconds the monitor has been in a triggered state.              |

### Triggered variables

 The `{{first_triggered_at}}`, `{{first_triggered_at_epoch}}`, `{{last_triggered_at}}`, and `{{last_triggered_at_epoch}}` monitor template variables reflect the values when a monitor changes state, **NOT** when a new monitor event occurs. Renotification events show the same template variable if the monitor state has not changed. Use `{{triggered_duration_sec}}` to display the duration at the time of the monitor event.

 `{{first_triggered_at}}` is set when the monitor group goes from `OK` to a non-`OK` state or when a new group appears in a non-`OK` state. `{{last_triggered_at}}` gets set when the monitor group goes to a non-`OK` state independently from its previous state (including `WARN` → `ALERT`, `ALERT` → `WARN`). Additionally, `{{last_triggered_at}}` is set when a new group appears in a non-`OK` state. The difference is that `{{last_triggered_at}}` is independent from its previous state.

 {{< img src="monitors/notifications/triggered_variables.png" alt="Showing four transitions with timestamps A: 1419 OK to WARN, B: 1427 WARN to ALERT, C: 1445 ALERT to NO DATA, D: 1449 NO DATA to OK" style="width:90%;">}}

**Example**: When the monitor transitions from `OK` → `WARN`, the values of `{{first_triggered_at}}` and `{{last_triggered_at}}` both have timestamp A. The table below shows the values until the monitor recovers.

| Transition         | first_triggered_at     | last_triggered_at      | triggered_duration_sec           |
|------------------  |--------------------------------  |--------------------------------  |--------------------------------  |
| `OK` → `WARN`      | A                                | A                                | 0                                |
| `WARN` → `ALERT`   | A                                | B                                | B - A                            |
| `ALERT` → `NO DATA`| A                                | C                                | C - A                            |
| `NO DATA` → `OK`   | A                                | C                                | D - A                            |

### Evaluation

Template variables that return numerical values support operations and functions, which allow you to perform mathematical operations or formatting changes to the value. For full details, see [Template Variable Evaluation][7].

### Local time

Use the `local_time` function to add another date in your notification in the time zone of your choice. This function transforms a date into its local time: `{{local_time 'time_variable' 'timezone'}}`.
For example, to add the last triggered time of the monitor in the Tokyo time zone in your notification, include the following in the notification message:

```
{{local_time 'last_triggered_at' 'Asia/Tokyo'}}
```

The result is displayed in the ISO 8601 format: `yyyy-MM-dd HH:mm:ss±HH:mm`, for example `2021-05-31 23:43:27+09:00`.
See the [list of tz database time zones][8], particularly the TZ database name column, to see the list of available time zone values.

## Advanced

### Dynamic handles

Use [tag variables](#attribute-and-tag-variables) to dynamically build notification handles and route notifications to the right team or service based on the type of issue detected by your monitor.

**Example**: If your monitor queries a metric and groups it by a `service` tag, you can have your notifications routed to different Slack channels depending on the failing service:

```text
@slack-{{service.name}} There is an ongoing issue with {{service.name}}.
```

If your monitor starts failing on the `service:ad-server` group, the notification is sent to the `#ad-server` Slack channel with the following content:

```text
@slack-ad-server There is an ongoing issue with ad-server.
```

When building dynamic handles with attributes that might not always be present, you may encounter issues with notification delivery. If an attribute is missing, the variable renders empty in the notification message, resulting in an invalid handle.

To avoid missed notifications when using dynamic handles with these variables, make sure to add a fallback handle:

```text
{{#is_exact_match "kube_namespace.owner" ""}}
  @slack-example
  // This will notify @slack-example if the kube_namespace.owner variable is empty or does not exist.
{{/is_match}}
```


### Dynamic links

Use [tag variables](#attribute-and-tag-variables) to enable dynamic URL building that links your team to an appropriate resource. For example, you can provide links to pages within Datadog such as dashboards, the host map, and monitors.

{{< tabs >}}
{{% tab "Dashboards" %}}

Use the `{{host.name}}` [tag variable](#attribute-and-tag-variables) to provide a link to a system dashboard:

```text
https://app.datadoghq.com/dash/integration/system_overview?tpl_var_scope=host:{{host.name}}
```

Use the `{{host.name}}` [tag variable](#attribute-and-tag-variables) and an `<INTEGRATION_NAME>` to provide a link to an integration dashboard:

```text
https://app.datadoghq.com/dash/integration/<INTEGRATION_NAME>?tpl_var_scope=host:{{host.name}}
```

Use the `{{last_triggered_at_epoch}}` [template variable](#template-variables) as well as a `<DASHBOARD_ID>` and `<DASHBOARD_NAME>` to link to dashboards with relative time ranges from the moment of the alert:

```text
https://app.datadoghq.com/dashboard/<DASHBOARD_ID>/<DASHBOARD_NAME>?from_ts={{eval "last_triggered_at_epoch-10*60*1000"}}&to_ts={{eval "last_triggered_at_epoch+10*60*1000"}}&live=false
```

{{% /tab %}}
{{% tab "Host map" %}}

Use a [tag variable](#attribute-and-tag-variables) such as `{{service.name}}` to provide a link to the host map:

```text
https://app.datadoghq.com/infrastructure/map?filter=service:{{service.name}}
```

The host map link is customizable with additional parameters. The most common are:

| Parameter | Defined with               | Determines                           |
|-----------|----------------------------|--------------------------------------|
| `fillby`  | `fillby=avg:<METRIC_NAME>` | The fill color of the host hexagons. |
| `groupby` | `groupby=<TAG_KEY>`        | The groups for host hexagons.        |
| `sizeby`  | `sizeby=avg:<METRIC_NAME>` | The size of the host hexagons.       |

{{% /tab %}}
{{% tab "Monitors" %}}

Use the `{{host.name}}` [tag variable](#attribute-and-tag-variables) to provide a link to all monitors related to a specific host:

```text
https://app.datadoghq.com/monitors/manage?q=scope:host:{{host.name}}
```

The monitors link is customizable with additional parameters. The most common are:

| Parameter | Example        | Displays                                                                        |
|-----------|----------------|---------------------------------------------------------------------------------|
| `status`  | `status:Alert` | Monitors in an alert state (additional statuses: `WARN`, `NO DATA`, and `OK`)   |
| `muted`   | `muted: true`  | Muted monitors (use `false` for non-muted monitors)                             |
| `type`    | `type:log`     | Log monitors (see other [monitor types][1])                                     |



[1]: /monitors/types
{{% /tab %}}
{{% tab "Logs" %}}

Use the `{{last_triggered_at_epoch}}` [template variable](#template-variables) to provide a link to all logs happening in the moment of the alert.

```text
https://app.datadoghq.com/logs?from_ts={{eval "last_triggered_at_epoch-10*60*1000"}}&to_ts={{eval "last_triggered_at_epoch+10*60*1000"}}&live=false
```

The logs link is customizable with additional parameters. The most common are:

| Parameter | Defined with               | Determines                             |
|-----------|----------------------------|----------------------------------------|
| `service` | `service=<SERVICE_NAME>`   | Filter on logs of a specific service.  |
| `host`    | `host=<HOST_NAME>`         | Filter on logs of a specific host      |
| `status`  | `status=<STATUS>`          | Status of logs: Error, Warn, Info etc. |


{{% /tab %}}
{{< /tabs >}}

### Comments

To include a comment in the monitor message, use the syntax:

```text
{{!-- this is a comment --}}
{{!-- this is a comment }}
```

### Raw format

If your alert message needs to send double curly braces, such as `{{ <TEXT> }}`, use `{{{{raw}}}}` formatting. For example, the following:

```text
{{{{raw}}}}
{{ <TEXT_1> }} {{ <TEXT_2> }}
{{{{/raw}}}}
```

Outputs:

```text
{{ <TEXT_1> }} {{ <TEXT_2> }}
```

The `^|#` helpers used in [conditional variables](#conditional-variables) cannot be used with `{{{{raw}}}}` formatting and must be removed. For instance, to output raw text with the `{{is_match}}` conditional variable use the following template:

```text
{{{{is_match "host.name" "<HOST_NAME>"}}}}
{{ .matched }} the host name
{{{{/is_match}}}}
```

If `host.name` matches `<HOST_NAME>`, the template outputs:

```text
{{ .matched }} the host name
```

### URL Encode

If your alert message includes information that needs to be encoded in a URL (for example, for redirections), use the `{{ urlencode "<variable>"}}` syntax.

**Example**: If your monitor message includes a URL to the Software Catalog filtered to a specific service, use the `service` [tag variable](#attribute-and-tag-variables) and add the `{{ urlencode "<variable>"}}` syntax to the URL:

```
https://app.datadoghq.com/services/{{urlencode "service.name"}}
```

## Further reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /monitors/configuration/#alert-grouping
[2]: /monitors/types/log/
[3]: /monitors/types/apm/?tab=analytics
[4]: /monitors/types/real_user_monitoring/
[5]: /monitors/types/ci/
[6]: /monitors/types/database_monitoring/
[7]: /monitors/guide/template-variable-evaluation/
[8]: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
[9]: /monitors/types/error_tracking/
[10]: /software_catalog/service_definitions/
[11]: https://docs.datadoghq.com/software_catalog/service_definitions/v2-2/#example-yaml
