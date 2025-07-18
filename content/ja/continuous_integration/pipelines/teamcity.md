---
aliases:
- /ja/continuous_integration/setup_pipelines/teamcity
further_reading:
- link: /continuous_integration/pipelines
  tag: ドキュメント
  text: パイプラインの実行結果とパフォーマンスを確認する
- link: /continuous_integration/troubleshooting/
  tag: ドキュメント
  text: CI Visibility のトラブルシューティング
title: TeamCity パイプラインでトレースを設定する
---

## Overview

[TeamCity][1] is a continuous integration and delivery server that optimizes and automates software development processes.

Set up tracing in TeamCity to collect data about your pipeline executions, debug performance bottlenecks, address operational issues, and optimize your development workflows.

### Compatibility

| Pipeline Visibility | Platform | Definition |
|---|---|---|
| [Partial retries][14] | Retry build triggers | View partially retried pipeline executions. |
| [Queue time][15] | Queue time | View the amount of time pipeline jobs sit in the queue before processing. |
| [Pipeline failure reasons][16] | Pipeline failure reasons | Identify pipeline failure reasons from error messages. |

The following TeamCity versions are supported:

- TeamCity >= 2021.2 or later

## Configure the Datadog integration

The integration between [TeamCity][1] and Datadog CI Visibility is provided through a TeamCity plugin.
To activate the integration:
1. Download the [Datadog CI Integration plugin][5] on the TeamCity server by going to
**Administration** -> **Plugins** -> **Browse Plugin Repository**.
2. If you don't already have one, add a [TeamCity composite build][6] as the last build of the build chain. This build must have
a dependency on the current last build of the chain and no other builds depending on it. Build chains not having a
composite build as their last build are ignored by the plugin. Below is an example of an expected build chain,
in which `Aggregating Results` is the last composite build:
{{< img src="ci/teamcity_build_chain.png" alt="TeamCity build chain with composite build at the end" style="width:90%;">}}
The final composite build must be properly configured in terms of version control settings, with
the VCS Root attached and the [VCS Trigger][13] configured.
3. The following configuration parameters need to be present for TeamCity projects:
   * **datadog.ci.api.key**: Your [Datadog API Key][2].
   * **datadog.ci.site**: {{< region-param key="dd_site" code="true" >}}.
   * **datadog.ci.enabled**: `true` (`false`
   can be used to disable the plugin for a specific project).

   These configuration parameters should not have type **Password**, as that prevents the plugin from correctly reading their values.
   They can be added either to TeamCity subprojects or to the [TeamCity Root Project][10]. When added to the Root project, they are propagated
   to all its subprojects. For example, to enable the plugin for all projects, add **datadog.ci.enabled** with the
   value `true` to the Root Project. More information on defining configuration parameters
   is available in the [TeamCity Project Hierarchy][9] documentation.
4. To enable the plugin, click on **Enable uploaded plugins** in the **Administration** -> **Plugins** page.
Alternatively, restart the TeamCity server.

## Visualize pipeline data in Datadog

View your data on the [**CI Pipeline List**][3] and [**Executions**][4] pages after the pipelines finish.

The **CI Pipeline List** page shows data for only the [default branch][12] of each repository.

## Configure Git user information

The plugin retrieves the Git author name and email based on the [TeamCity username style][7].
Datadog recommends using either **Author Name and Email** or **Author Email** username styles, as they
provide information about the user email. When one of the other username styles is used (**UserId** or **Author Name**),
the plugin automatically generates an email for the user by appending `@Teamcity` to the username.
For example, if the **UserId** username style is used and the Git author username is `john.doe`,
the plugin generates `john.doe@Teamcity` as the Git author email. The username style is defined for [VCS Roots][11],
and can be modified in the VCS Root settings.

<div class="alert alert-danger"><strong>Note:</strong> The Git author email is used for
<a href="https://www.datadoghq.com/pricing/?product=ci-visibility#ci-visibility" target="_blank">billing</a> purposes,
therefore there might be cost implications when username styles not providing email
(<strong>UserId</strong> or <strong>Author Name</strong>) are used. <a href="https://docs.datadoghq.com/help/" target="_blank">Reach out to the Datadog support team</a> if you have any questions about your use case.
</div>

## プラグインリポジトリ

Datadog CI Integration プラグインの[ソースコード][8]は、Apache 2.0 ライセンスでオープンソース化されています。

## トラブルシューティング

Datadog CI Integration プラグインによって生成されたすべてのログは `teamcity-server.log` ファイルに保存され、TeamCity Server から **Administration** -> **Diagnostic** -> **Server Logs** にアクセスしてアクセスすることが可能です。これらのログを確認することで、プラグインに関する問題についての追加情報を得ることができます。

## 参考資料

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://www.jetbrains.com/teamcity/
[2]: https://app.datadoghq.com/organization-settings/api-keys
[3]: https://app.datadoghq.com/ci/pipelines
[4]: https://app.datadoghq.com/ci/pipeline-executions
[5]: https://plugins.jetbrains.com/plugin/20852-datadog-ci-integration
[6]: https://www.jetbrains.com/help/teamcity/composite-build-configuration.html
[7]: https://www.jetbrains.com/help/teamcity/git.html#General+Settings
[8]: https://github.com/DataDog/ci-teamcity-plugin
[9]: https://www.jetbrains.com/help/teamcity/project.html#Project+Hierarchy
[10]: https://www.jetbrains.com/help/teamcity/project.html#Root+Project
[11]: https://www.jetbrains.com/help/teamcity/vcs-root.html
[12]: https://docs.datadoghq.com/ja/continuous_integration/troubleshooting/#the-default-branch-is-not-correct
[13]: https://www.jetbrains.com/help/teamcity/configuring-vcs-triggers.html#Trigger+build+on+changes+in+snapshot+dependencies
[14]: /ja/glossary/#partial-retry
[15]: /ja/glossary/#queue-time
[16]: /ja/glossary/#pipeline-failure