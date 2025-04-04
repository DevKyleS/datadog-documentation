---
title: Upgrade the RUM Browser SDK

further_reading:
- link: '/real_user_monitoring/explorer'
  tag: 'Documentation'
  text: 'Visualize your RUM data in the Explorer'
- link: "https://www.datadoghq.com/blog/session-replay-datadog/"
  tag: "Blog"
  text: "Use Datadog Session Replay to view real-time user journeys"
---

## Overview

Follow this guide to migrate between major versions of the Browser RUM and Browser Logs SDKs. See [the SDK documentation][26] for details on its features and capabilities.

## From v5 to v6

The main improvement v6 offers is the bundle size reduction. By dropping support for IE11 and leveraging lazy loading, the size of the RUM bundle has been reduced by 10% and the Logs bundle by nearly 9%.
Additionally, we've changed a few default initialization parameters and prepared for future improvements.

Take notice of the below breaking changes as you upgrade your SDK.

### Breaking changes

#### Browser support

Support for IE11 and other older browsers has been discontinued. Browsers must now support at least ES2018.
To use Datadog on older browsers, you can keep using Browser SDK v5 or earlier.

#### Add tracestate header when using tracecontext propagator

The default `tracecontext` propagator now sends a new `tracestate` header with additional metadata that allows better attribution of your traces. If you are using this propagator, then you need to allow this new header for all traced endpoints, in addition to the existing `traceparent` header:

```
Access-Control-Allow-Headers: traceparent, tracestate
```

#### Strongly type `site` option

The `site` option now has a stronger type definition. If you use TypeScript you might have an error if you use a non-standard value. We recommend using [proxy][27] to send RUM data to a nonstandard URL.

#### Tracking Actions, Resources and LongTask are now enabled by default

User interactions, resources, and long tasks are now tracked by default. This change does not impact billing. To opt-out, set `trackUserInteractions`, `trackResources`, and `trackLongTasks`  [initialization parameters][28] to `false`.

#### Collect Long Animation Frames as Long Tasks

On supported Browsers, [Long Animation Frames][35] are now collected instead of Long Tasks. The event type in the RUM Explorer is still `long_task`, but they will contain information about the long animation frame.

#### Increased cookies expiration date

To support anonymous user tracking, the session cookie (`_dd_s`) expiration is extended to 1 year. To opt-out, set `trackAnonymousUser` [initialization parameters][28] to `false`.

#### Removed useCrossSiteSessionCookie initialization parameter

`useCrossSiteSessionCookie` was deprecated and is now unsupported. Use `usePartitionedCrossSiteSessionCookie` [initialization parameters][28] instead.

#### Lazy load Session Replay

Session Replay module is now lazy-loaded using [dynamic imports][30]. This loads the module only for sessions sampled for Session Replay, reducing the bundle size for others.

**If you're using the SDK through NPM**, ensure your bundler supports dynamic imports. Most modern bundlers support this feature out of the box, but some may require configuration changes. Refer to your bundler's documentation for guidance: [Webpack][31], [Esbuild][32], [Rollup][33], [Parcel][34].

**If you're using the SDK through a CDN**, there are no breaking changes. However, note that in addition to the main script being loaded (for example, 
`datadog-rum.js`), the SDK will dynamically load an additional chunk when needed (for example, 
`recorder-d7628536637b074ddc3b-datadog-rum.js`).

#### Do not inject trace context for non-sampled traces

The default value for the `traceContextInjection` initialization parameter has been updated to `sampled` to ensure backend services' sampling decisions are applied when traces are not sampled in the Browser SDK. See the [Connect RUM and Traces documentation][29] for more information.

**Note**: If you're using a `traceSampleRate` of 100% (default), this change does not have any impact for you.



### Future breaking changes

#### Enabling compression for Datadog intake requests

Compression for Datadog intake requests will be enabled by default in a future major version.
Datadog recommends that you opt-in to compression now using the `compressIntakeRequests` [initialization parameter][28].
Since compression is performed in a Worker thread, configuring the Content Security Policy is necessary. See [CSP guidelines][18] for more information.

## From v4 to v5

V5 introduces the following changes and more:

- New configurations and privacy defaults for Session Replay
- Automatic collection of frustration signals
- Updated performance metrics
- Updated SDK parameters and APIs

Take notice of the below breaking changes as you upgrade your SDK. Changes are grouped by area of impact.

### General

#### SDK initialization parameters

**Action to take**: Replace the deprecated parameters with the new equivalent parameters in v5. The old parameter names are no longer available in v5.

| Deprecated parameter name (v4 or earlier) | New parameter name (v5) |
|-------------------------------------------|-------------------------|
| proxyUrl | proxy |
| sampleRate | sessionSampleRate |
| allowedTracingOrigins | allowedTracingUrls |
| tracingSampleRate | traceSampleRate |
| trackInteractions | trackUserInteractions |
| premiumSampleRate | sessionReplaySampleRate |
| replaySampleRate | sessionReplaySampleRate |

#### Public APIs

**Action to take**: Replace the deprecated APIs with the new equivalent APIs. The old APIs are no longer available in v5.

| Deprecated parameter name (v4 or earlier) | New parameter name (v5) |
|-------------------------------------------|-------------------------|
| DD_RUM.removeUser | [DD_RUM.clearUser][7] |
| DD_RUM.addRumGlobalContext | [DD_RUM.setGlobalContextProperty][8] |
| DD_RUM.removeRumGlobalContext | [DD_RUM.removeGlobalContextProperty][9] |
| DD_RUM.getRumGlobalContext | [DD_RUM.getGlobalContext][10] |
| DD_RUM.setRumGlobalContext | [DD_RUM.setGlobalContext][11] |
| DD_LOGS.addLoggerGlobalContext | [DD_LOGS.setGlobalContextProperty][8] |
| DD_LOGS.removeLoggerGlobalContext | [DD_LOGS.removeGlobalContextProperty][9] |
| DD_LOGS.getLoggerGlobalContext | [DD_LOGS.getGlobalContext][12] |
| DD_LOGS.setLoggerGlobalContext | [DD_LOGS.setGlobalContext][13] |
| logger.addContext | [logger.setContextProperty][14] |
| logger.removeContext | [logger.removeContextProperty][15] |

#### Intake domains
V5 sends data to different intake domains than previous versions.

**Action to take**: Update any [Content Security Policy (CSP)][18] `connect-src` entries to use the new domain.

| Datadog site | Domain |
|--------------|--------|
| US1 | `connect-src https://browser-intake-datadoghq.com` |
| US3 | `connect-src https://browser-intake-us3-datadoghq.com` |
| US5 | `connect-src https://browser-intake-us5-datadoghq.com` |
| EU1 | `connect-src https://browser-intake-datadoghq.eu` |
| US1-FED | `connect-src https://browser-intake-ddog-gov.com` |
| AP1 | `connect-src https://browser-intake-ap1-datadoghq.com` |

#### Trusted events
To avoid collecting incorrect or illegitimate data, v5 only listens to events generated by user actions, ignoring events created by scripts. See [trusted events][19] for more details.

**Action to take**: If you rely on any programmatic events and want them to be taken into account by the SDK, add the `__ddIsTrusted` attribute to them, like below:

```javascript
const click = new Event('click')
click.__ddIsTrusted = true
document.dispatchEvent(click)
```

**Action to take**: If you rely heavily on programmatic events, such as in an automated UI test environment, for example, you can allow all untrusted events by setting `allowUntrustedEvents: true`.

#### `beforeSend` return type
`beforeSend` callback functions should return a boolean value:

```javascript
beforeSend(event: any, context?: any) => boolean
```

The implementation has not changed. If no value is returned, the event is not discarded.

**Action to take**: Ensure that `beforeSend` returns `true` to keep the event and `false` to discard it. This resolves related TypeScript compilation errors.

### Session Replay

#### Session Replay masking

The default Session Replay masking setting `defaultPrivacyLevel` has been changed from `mask-user-input` to `mask`. This hides all data in Session Replay recordings by default, making recordings less sensitive to view. For more information, see [Session Replay Browser Privacy Options][20].

**Action to take**: If you want to see more unmasked data in Session Replay, like non-sensitive HTML content or user-entered text, set `defaultPrivacyLevel` to `mask-user-input` or `allow`.

#### Automatic recording of sessions sampled for Session Replay
Sessions that are sampled for Session Replay using [`sessionReplaySampleRate`][21] are automatically recorded at the start of the session. This means that you don't have to call the [`startSessionReplayRecording()`][22] method to capture a recording. In other words, you won't accidentally miss any recordings.

**Action to take**: If you want to continue using the old recording behavior and customize when your recording starts, set `startSessionReplayRecordingManually` to `true`.

#### Only pay for Session Replay when the session captures a recording
In previous SDK versions, sessions are determined to be Session Replay sessions through the sampling mechanism. In v5, sessions are only counted as Session Replay sessions if a recording is captured during the session. This makes it easier to track your Session Replay usage.

**No action needed**: This behavior automatically takes effect in v5.

#### Default Session Replay sampling rate
In v5, the default `sessionReplaySampleRate` is 0 instead of 100. If you don't include a sampling rate, no replays are recorded.

**Action to take**: To use Session Replay, set a sampling rate explicitly with `sessionReplaySampleRate: 100` (or another sampling rate).

### RUM

### APM integration

To promote the support and usage of OpenTelemetry, the default propagator types have been changed to include `tracecontext` in addition to `datadog`.

**Action to take**: If you are not already specifying the desired propagator on the `allowedTracingUrls` initialization parameter, configure your server Access-Control-Allow-Headers to also accept the `traceparent` header. For more information, see [connect RUM and Traces][25].

### Session plan field

In relation to Session Replay changes, the `session.plan` field is only available for session events.

**Action to take**: Update any monitor or dashboard queries you have saved to exclude the `session.plan` field for non-session events.

#### Frustration signals are collected automatically
You only need to set `trackUserInteractions: true` to collect all user interactions, including frustration signals. You no longer need to set the `trackFrustrations` parameter separately.

**Action to take**: To track frustration signals, set `trackUserInteractions: true`. The `trackFrustrations` parameter can be removed.

#### Resource durations are omitted for frozen pages
Resource collection omits durations of resources that were extended due to the page going into the background, for example, when the user clicks on a separate tab while the page is loading.

**No action needed**: This behavior automatically takes effect in v5.

#### Resources and long task tracking
When using `sessionReplaySampleRate` instead of `replaySampleRate` or `premiumSampleRate` (both deprecated), you must configure resources and long tasks explicitly.

**Action to take**: To collect these events, ensure that `trackResources` and `trackLongTasks` are set to `true`.

#### Resource method names are in uppercase
In order to avoid having different values for the same method name depending on the case (POST vs post), method names are now consistently sent in uppercase.

**Action to take**: Update monitor or dashboard queries to use the `resource.method` field with uppercase values.

#### `beforeSend` action event
The `beforeSend` API allows access to contextual information of the collected events (see [Enrich and control RUM data][23]).

With the introduction of frustration signals, an action event can be associated with several DOM events.

Along with this update, the `context.event` attribute has been removed in favor of the `context.events` attribute.

**Action to take**: Update `beforeSend` code to use `context.events` instead of `context.event`.

```javascript
beforeSend: (event, context) => {
  if (event.type === 'action' && event.action.type === 'click') {
    // accessing browser events related to the action event
    // before, single event: context.event
    // now, multiple events: context.events
  }
}
```

#### `beforeSend` in foreground periods
The `view.in_foreground_periods` attribute is computed directly from the backend, not sent by the SDK.

**Action to take**: Remove `view.in_foreground_periods` from `beforeSend` code. If you were relying on this attribute for a specific use case, reach out to [Support][24] for assistance.

#### `beforeSend` performance entry
The `beforeSend` context `performanceEntry` attribute has been updated from the JSON representation to include the performance entry object directly.

The exported `PerformanceEntryRepresentation` type has been removed in favor of the standard `PerformanceEntry` type.

**Action to take**: In `beforeSend` code, use the `PerformanceEntry` type directly instead of the `PerformanceEntryRepresentation` type.

### Logs
#### Remove console error prefix
The "`console error:`" prefix in log messages has been removed. This information can be found in the `origin` attribute.

**Action to take**: Update monitor or dashboard queries using the `"console error:"` prefix to use `@origin:console` instead.

#### Remove `error.origin`

Since the introduction of the `origin` attribute on all logs, `error.origin` was redundant and has been removed.

**Action to take**: Update monitor or dashboard queries using `error.origin` to use `origin` instead.

#### Decouple main logger
When the SDK collects runtime errors or network, report, or console logs, it does not append the context specific to the main logger (`DD_LOGS.logger`), and it does not use the level or handler set for that logger.

**Action to take**: If you relied on the main logger level to exclude non-logger logs, use dedicated initialization parameters instead.

**Action to take**: If you relied on the main logger context to add context to non-logger logs, use global context instead.

## From v3 to v4

Several breaking changes were made to the RUM and Logs Browser SDK with the v4 version.

### Changes

#### Intake URLs

The URLs for where the RUM Browser SDK data is sent has changed. Ensure that your [Content Security Policy is up to date][1].

#### Minimal Typescript version support

The RUM Browser SDK v4 is not compatible with TypeScript earlier than v3.8.2. If you use TypeScript, ensure that the version is at least v3.8.2.

#### Tags syntax

The `version`, `env`, and `service` initialization parameters are sent as tags to Datadog. The RUM Browser SDK slightly sanitizes them to ensure that they don't generate multiple tags, and prints a warning if those values don't meet the tag requirements syntax.

#### Stricter initialization parameters typing

TypeScript types representing initialization parameters are stricter and may reject previously accepted unsupported parameters. If you get type-checking errors, ensure you are providing supported initialization parameters.

#### Privacy options precedence

When multiple privacy options are specified on the same element, Datadog applies the most restrictive option to avoid unexpectedly leaking sensitive data. For example, if both `dd-privacy-allow` and `dd-privacy-hidden` classes are specified on the same element, it is hidden instead of allowed.

#### Action names computation

When computing action names, the RUM Browser SDK removes text of child elements with the `data-dd-action-name` attribute from inner text.

For example, for the following `container` element, where previously the computed action name would be `Container sensitive data`, in v4, the computed action name is `Container`:
```html
<div id="container">
  Container
  <div data-dd-action-name="sensitive">sensitive data</div>
</div>
```

### Removals

#### XHR `_datadog_xhr` field

The RUM Browser SDK previously used a `_datadog_xhr` property on `XMLHttpRequest` objects representing its internal state. This property has been removed without replacement as it wasn't intended to be used externally.

#### `proxyHost` initialization parameter

The `proxyHost` initialization parameter has been removed. Use the `proxyUrl` initialization parameter instead.

#### Privacy options support

The privacy options `input-ignored` and `input-masked` are no longer valid. Instead, use the `mask-user-input` privacy option.

Specifically, replace:

* `dd-privacy-input-ignored` and `dd-privacy-input-masked` class names with `dd-privacy-mask-user-input`
* `dd-privacy="input-masked"` and `dd-privacy="input-ignored"` attribute values with `dd-privacy="mask-user-input"`

## From v2 to v3

The Browser SDK v3 introduces [Session Replay][2]. With this major version update, several breaking changes were made to the RUM and Logs Browser SDKs.

### Changes
#### RUM errors

The RUM Browser SDK no longer issues [RUM errors][3] for failed XHR and Fetch calls. These failed network requests are still collected as [RUM resources][4], which contain the status code attribute.

To continue seeing the failed network requests as RUM errors, Datadog recommends intercepting the resource with the [beforeSend API][5], checking the `status_code` property, and manually sending an error with the [addError API][6].

```javascript
beforeSend: (event) => {
    if (event.type === 'resource' && event.resource.status_code >= 500) {
        datadogRum.addError(`${event.resource.method} ${event.resource.url} ${event.resource.status_code}`); // "GET https://www.example.com/ 504"
    }
}
```

#### RUM error source attribute

The RUM Browser SDK no longer lets you specify the source of an error collected with the [addError API][6]. All errors collected with this API have their source attribute set to `custom`. The [addError API][6] accepts a context object as its second parameter, which should be used to pass extra context about the error.

### Removals
#### RUM API

| Old API       | New API   |
| ------------- | --------- |
| addUserAction | addAction |

#### Initialization options

| Old options        | New options |
| ------------------ | ----------- |
| publicApiKey       | clientToken |
| datacenter         | site        |
| resourceSampleRate | NONE        |

#### TypeScript types

| Old types                    | New types                    |
| ---------------------------- | ---------------------------- |
| RumUserConfiguration         | RumInitConfiguration         |
| RumRecorderUserConfiguration | RumRecorderInitConfiguration |
| LogsUserConfiguration        | LogsInitConfiguration        |

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /real_user_monitoring/faq/content_security_policy
[2]: /real_user_monitoring/session_replay
[3]: /real_user_monitoring/browser/collecting_browser_errors/
[4]: /real_user_monitoring/browser/monitoring_resource_performance/
[5]: /real_user_monitoring/browser/advanced_configuration/?tab=npm#enrich-and-control-rum-data
[6]: /real_user_monitoring/browser/collecting_browser_errors/?tab=npm#collect-errors-manually
[7]: /real_user_monitoring/browser/advanced_configuration/?tab=npm#clear-user-session-property
[8]: /real_user_monitoring/browser/advanced_configuration/?tab=npm#add-global-context-property
[9]: /real_user_monitoring/browser/advanced_configuration/?tab=npm#remove-global-context-property
[10]: /real_user_monitoring/browser/advanced_configuration/?tab=npm#read-global-context
[11]: /real_user_monitoring/browser/advanced_configuration/?tab=npm#replace-global-context
[12]: /api/latest/rum/
[13]: /api/latest/rum/
[14]: /api/latest/rum/
[15]: /api/latest/rum/
[16]: /api/latest/rum/
[17]: /api/latest/rum/
[18]: /integrations/content_security_policy_logs/?tab=firefox#use-csp-with-real-user-monitoring-and-session-replay
[19]: https://developer.mozilla.org/en-US/docs/Web/API/Event/isTrusted
[20]: /real_user_monitoring/session_replay/browser/privacy_options/#configuration
[21]: /real_user_monitoring/guide/sampling-browser-plans/#setup
[22]: /real_user_monitoring/session_replay/browser/#usage
[23]: /real_user_monitoring/browser/advanced_configuration/?tab=npm#enrich-and-control-rum-data
[24]: /help/
[26]: /real_user_monitoring/browser/
[25]: /real_user_monitoring/correlate_with_other_telemetry/apm#opentelemetry-support
[27]: /real_user_monitoring/guide/proxy-rum-data
[28]: /real_user_monitoring/browser/setup/#initialization-parameters
[29]: /real_user_monitoring/correlate_with_other_telemetry/apm?tab=browserrum#:~:text=configure%20the%20traceContextInjection
[30]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import
[31]: https://webpack.js.org/guides/code-splitting/#dynamic-imports
[32]: https://esbuild.github.io/api/#splitting
[33]: https://rollupjs.org/tutorial/#code-splitting
[34]: https://parceljs.org/features/code-splitting
[35]: https://developer.chrome.com/docs/web-platform/long-animation-frames#long-frames-api
