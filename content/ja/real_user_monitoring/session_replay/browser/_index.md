---
aliases:
- /ja/real_user_monitoring/guide/session-replay-getting-started/
- /ja/real_user_monitoring/session_replay/
description: セッションリプレイでユーザーの Web 閲覧体験をキャプチャし、視覚化する方法について説明します。
further_reading:
- link: https://www.datadoghq.com/blog/session-replay-datadog/
  tag: ブログ
  text: Datadog Session Replay を使用してユーザージャーニーをリアルタイムで表示
- link: https://www.datadoghq.com/blog/reduce-customer-friction-funnel-analysis/
  tag: ブログ
  text: ファネル分析により、主要なユーザーフローを理解し、最適化する
- link: https://www.datadoghq.com/blog/zendesk-session-replay-integration/
  tag: ブログ
  text: Zendesk と Datadog セッションリプレイでユーザーが直面する問題を視覚的に再生する
- link: /real_user_monitoring/explorer
  tag: ドキュメント
  text: RUM データを Explorer で確認
- link: /integrations/content_security_policy_logs
  tag: ドキュメント
  text: Datadog で CSP 違反の検出と集計を行う
title: ブラウザセッションリプレイ
---

## 概要

セッションリプレイは、ユーザーのウェブブラウジング体験をキャプチャして視覚的に再生できるようにすることで、ユーザーエクスペリエンスモニタリングを拡張します。セッションリプレイを RUM パフォーマンスデータと組み合わせると、エラーの識別、再現、解決に有益で、ウェブアプリケーションの使用パターンと設計上の落とし穴に対する洞察をもたらすこともできます。

RUM ブラウザ SDK は[オープンソース][1]であり、オープンソースの [rrweb][2] プロジェクトを活用したものです。

## セッションリプレイレコーダー

セッションリプレイレコーダーは、RUM ブラウザ SDK の一部です。このレコーダーは、Web ページで発生したイベント (DOM の変更、マウスの移動、クリック、入力イベントなど) を、これらのイベントのタイムスタンプとともに追跡して記録することにより、ブラウザの DOM と CSS のスナップショットを取得します。

その後、Datadog は Web ページを再構築し、記録されたイベントをリプレイビューの適切なタイミングで再適用します。セッションリプレイは、通常の RUM セッションと同じ 30 日間の保持ポリシーに従います。

セッションリプレイレコーダーは、IE11 を除き、RUM ブラウザ SDK でサポートされているすべてのブラウザをサポートしています。詳しくは、[ブラウザサポートテーブル][3]を参照してください。

セッションリプレイのネットワークへの影響を軽減し、セッションリプレイレコーダーがアプリケーションのパフォーマンスに与えるオーバーヘッドを最小限に抑えるため、Datadog はデータを送信する前に圧縮を行います。また、Datadog は CPU に負荷のかかる作業 (圧縮など) のほとんどを専用 Web ワーカーに委ねることで、ブラウザの UI スレッドの負荷を軽減しています。ネットワーク帯域幅への影響は 100kB/分未満と予想されます。

## セットアップ

セッションリプレイは、RUM ブラウザ SDK で利用できます。セッションリプレイのデータ収集を開始するには、RUM アプリケーションの作成、クライアントトークン生成、RUM ブラウザ SDK の初期化により、[Datadog RUM ブラウザモニタリング][4]をセットアップしてください。モバイル環境でのセットアップについては、[モバイルセッションリプレイ][5]を参照してください。

<div class="alert alert-info">Session Replay is supported by SDK version 3.6.0 or later.</div>

## 使用方法

RUM ブラウザ SDK v5.0.0 から、`init()` を呼び出した際にセッションリプレイの記録が自動的に開始されます。記録を条件付きで開始する場合は、`startSessionReplayRecordingManually` 初期化パラメータを使用し、`startSessionReplayRecording()` を呼び出してください。

For example, to only record authenticated user sessions:

```javascript
window.DD_RUM.init({
  applicationId: '<DATADOG_APPLICATION_ID>',
  clientToken: '<DATADOG_CLIENT_TOKEN>',
  site: '<DATADOG_SITE>',
  //  service: 'my-web-application',
  //  env: 'production',
  //  version: '1.0.0',
  sessionSampleRate: 100,
  sessionReplaySampleRate: 100,
  startSessionReplayRecordingManually: true,
  ...
});

if (user.isAuthenticated) {
    window.DD_RUM.startSessionReplayRecording();
}
```

セッションリプレイの記録を停止するには、`stopSessionReplayRecording()` を呼び出してください。

<div class="alert alert-warning">When using a version of the RUM Browser SDK older than v5.0.0, Session Replay recording does not begin automatically. Call <code>startSessionReplayRecording()</code> to begin recording.</div>

## セッションリプレイを強制する

一部のシナリオでは、開始時にリプレイ対象から除外されていたセッションであっても、後から録画を開始したい場合があります。たとえば、新しくデプロイしたページでセッションリプレイを強制的に有効にしてより詳細にモニタリングしたい場合や、エラーを検出した後に録画を開始して、完全なリプレイデータを確保したい場合などです。

現在のセッションの残りの時間にわたって強制的にセッションリプレイを実行するには、`startSessionReplayRecording({ force: true })` を呼び出してください。

force オプションを使用すると、セッションは最初のサンプリング決定にかかわらず、セッションが終了するまでリプレイ対象として扱われます。

<div class="alert alert-warning">force オプションは、既にサンプリングされているセッションをリプレイ用にアップグレードする場合にのみ機能します。言い換えると、サンプリングがまだ開始されていない状態で force オプションを使用してもサンプリングは開始されず、リプレイも記録されません。</div>

## セッションリプレイを無効にする

セッションの記録を停止するには、`sessionReplaySampleRate` を `0` に設定します。これにより、[Browser RUM & Session Replay プラン][6]のデータ収集が停止します。

<div class="alert alert-warning">RUM Browser SDK のバージョンが v5.0.0 より前の場合は、<code>replaySampleRate</code> を <code>0</code> に設定してください。</div>

## 保持

デフォルトでは、Session Replay データは 30 日間保持されます。

保持期間を 15 か月に延長するには、個別のセッションリプレイごとに _Extended Retention_ を有効にできます。これらのセッションはアクティブではない (ユーザーの操作が終了している) 必要があります。

Extended Retention は Session Replay にのみ適用され、関連イベントは含まれません。15 か月は、セッションが収集されたときではなく、Extended Retention が有効になったときに開始します。

Extended Retention はいつでも無効にできます。Session Replay の保持期間がまだデフォルトの 30 日以内である場合、リプレイは最初の 30 日間のウィンドウの終了時に失効します。30 日を過ぎた Session Replay で Extended Retention を無効にすると、リプレイは直ちに失効します。

{{< img src="real_user_monitoring/session_replay/session-replay-extended-retention.png" alt="Extended Retention を有効にする" style="width:100%;" >}}

保持期間の延長でどのようなデータが保持されるかを理解するには、下図を参照してください。

{{< img src="real_user_monitoring/session_replay/replay-extended-retention.png" alt="保持期間の延長でどのデータが保持されるかを示す図" style="width:100%;" >}}

## 再生履歴

プレーヤーページに表示される **watched** カウントをクリックすると、指定した Session Replay を誰が視聴したかを確認できます。この機能により、記録を共有したい相手がすでに視聴しているかどうかを確認することができます。

{{< img src="real_user_monitoring/session_replay/session-replay-playback-history.png" alt="セッションの記録を誰が見たかを確認" style="width:100%;" >}}

履歴には、プレーヤーページや[ノートブック][8]、サイドパネル内の埋め込みプレーヤーで行われた再生のみが含まれます。含まれる再生は、[監査証跡][7]イベントも生成します。サムネイルプレビューは履歴に含まれません。

自分の再生履歴を見るには、プレイリストの [My Watch History][9] をご覧ください。

## Mobile Session Replay

[モバイル向けセッションリプレイ][5]について詳しくはこちらをご覧ください。

## その他の参考資料

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://github.com/DataDog/browser-sdk
[2]: https://www.rrweb.io/
[3]: https://github.com/DataDog/browser-sdk/blob/main/packages/rum/BROWSER_SUPPORT.md
[4]: /ja/real_user_monitoring/browser/
[5]: /ja/real_user_monitoring/session_replay/mobile/
[6]: https://www.datadoghq.com/pricing/?product=real-user-monitoring--session-replay#real-user-monitoring--session-replay
[7]: https://docs.datadoghq.com/ja/account_management/audit_trail/
[8]: https://docs.datadoghq.com/ja/notebooks/
[9]: https://app.datadoghq.com/rum/replay/playlists/my-watch-history