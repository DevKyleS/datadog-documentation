---
description: Datadog AWS インテグレーションを手動でセットアップする手順
further_reading:
- link: https://docs.datadoghq.com/integrations/amazon_web_services/
  tag: ドキュメント
  text: AWS インテグレーション
- link: https://docs.datadoghq.com/serverless/libraries_integrations/forwarder/
  tag: ドキュメント
  text: Datadog Forwarder Lambda 関数
- link: https://docs.datadoghq.com/logs/guide/send-aws-services-logs-with-the-datadog-kinesis-firehose-destination/
  tag: ガイド
  text: Datadog Amazon Data Firehose の宛先を使用して AWS サービスログを送信する
- link: https://docs.datadoghq.com/integrations/guide/aws-integration-troubleshooting/
  tag: ガイド
  text: AWS インテグレーションのトラブルシューティング
- link: https://docs.datadoghq.com/integrations/guide/aws-cloudwatch-metric-streams-with-kinesis-data-firehose/
  tag: ガイド
  text: Amazon Data Firehose を使用した AWS CloudWatch メトリクスストリーム
- link: https://www.datadoghq.com/blog/aws-monitoring/
  tag: ブログ
  text: AWS 監視のための主要なメトリクス
- link: https://www.datadoghq.com/blog/monitoring-ec2-instances-with-datadog/
  tag: ブログ
  text: Datadog で EC2 インスタンスを監視する方法
- link: https://www.datadoghq.com/blog/monitoring-aws-lambda-with-datadog/
  tag: ブログ
  text: Datadog を使用した AWS Lambda の監視
- link: https://www.datadoghq.com/blog/cloud-security-posture-management/
  tag: ブログ
  text: Datadog クラウドセキュリティポスチャ管理
- link: https://www.datadoghq.com/blog/datadog-workload-security/
  tag: ブログ
  text: Datadog クラウドワークロードセキュリティでリアルタイムにインフラストラクチャーを保護する
- link: https://www.datadoghq.com/blog/announcing-cloud-siem/
  tag: ブログ
  text: Datadog セキュリティモニタリングが新登場
- link: https://www.datadoghq.com/blog/tagging-best-practices/#aws
  tag: ブログ
  text: インフラストラクチャーとアプリケーションにタグを付けるためのベストプラクティス
title: AWS マニュアルセットアップガイド
---

## 概要

このガイドを使用して、Datadog [AWS インテグレーション][1]を手動でセットアップします。

{{< tabs >}}
{{% tab "Role delegation" %}}

AWS インテグレーションを手動で設定するには、AWS アカウントで IAM ポリシーと IAM ロールを作成し、Datadog アカウントで生成された AWS External ID でそのロールを構成します。これにより、Datadog の AWS アカウントは、自動的に AWS API をクエリし、Datadog アカウントにデータをプルすることができます。以下のセクションでは、これらの各コンポーネントを作成し、Datadog アカウントでセットアップを完了するための手順を詳しく説明します。

{{< site-region region="gov" >}}
<div class="alert alert-warning">
<em>ロール委任を使用した S3 ログアーカイブの設定は現在限定的に利用可能です。Datadog for Government アカウントでこの機能をリクエストするには、<a href="https://docs.datadoghq.com/help/">Datadog サポート</a>にお問い合わせください</em>。
</div>
{{< /site-region >}}

## セットアップ

### 外部 ID を生成する

1. [AWS インテグレーション構成ページ][1]で、**Add AWS Account** をクリックし、** Manually** を選択します。
2. ご利用の AWS アカウントが属するパーティションを選択してください。パーティションには、商用リージョン用の `aws`、中国*向けの `aws-cn`、または GovCloud 向けの `aws-us-gov` があります。詳しくは、AWS ドキュメントの [Partitions][9] を参照してください。
{{< site-region region="us,us3,us5,eu,ap1" >}}
3. アクセス種別として `Role Delegation` を選択してください。ロール委譲は、AWS の商用リージョンに属するアカウントでのみサポートされます。
{{< /site-region >}}
{{< site-region region="gov" >}}
3. アクセス種別として `Role Delegation` を選択してください。ロール委譲は、AWS の商用リージョンまたは AWS GovCloud リージョンに属するアカウントでのみサポートされます。
{{< /site-region >}}
4. `AWS External ID` をコピーしてください。External ID の詳細については、[IAM ユーザーガイド][2]を参照してください。
  **注**: 外部 ID は、ユーザーにより明示的に変更されたり、別の AWS アカウントが Datadog に追加されたりしない限り、48 時間は利用可能な状態となり、再生成されません。その時間内に **Add New AWS Account** ページに戻り、アカウントの追加プロセスを完了すれば、外部 ID が変更されることはありません。

### Datadog のための AWS IAM ポリシー
Datadog が提供するすべての AWS インテグレーションを利用するために、AWS アカウントの Datadog ロールに[必要な権限](#aws-integration-iam-policy)を持つ IAM ポリシーを作成します。インテグレーションに他のコンポーネントが追加されると、これらの権限は変更される可能性があります。

5. AWS [IAM コンソール][3]で新しいポリシーを作成します。 
6. **JSON** タブを選択し、テキストボックスに[権限ポリシー](#aws-integration-iam-policy)を貼り付けます。<br>
  **注**: オプションで、IAM ポリシーに [Condition][7] 要素を追加できます。例えば、[特定の地域に監視を制限する][8]ために条件を使用できます。
7. **Next: Tags** と **Next: Review** をクリックします。
8. ポリシーに `DatadogIntegrationPolicy`、または自分が選択した名前を付け、適切な説明を入力します。
9. **Create policy** をクリックします。

### Datadog のための AWS IAM ロール
IAM ポリシーで定義された権限を使用するために、Datadog 用の IAM ロールを作成します。

10. AWS [IAM コンソール][4]で新しいロールを作成します。
11. 信頼されるエンティティの種類で **AWS account** を選択し、**Another AWS account** を選択します。
{{< site-region region="us,us3,us5,eu" >}}
12. `Account ID` として、`464622532012` を入力します。これは Datadog のアカウント ID で、Datadog に AWS のデータへのアクセスを許可するものです。
{{< /site-region >}}
{{< site-region region="ap1" >}}
12. `Account ID` として、`417141415827` を入力します。これは Datadog のアカウント ID で、Datadog に AWS のデータへのアクセスを許可するものです。
{{< /site-region >}}
{{< site-region region="gov" >}}
12. 統合したい AWS アカウントが GovCloud アカウントである場合は `Account ID` に `065115117704` を、それ以外の場合は `392588925713` を入力します。これは Datadog のアカウント ID で、Datadog に AWS のデータへのアクセスを許可するものです。
{{< /site-region >}}
13. **Require external ID** を選択し、[外部 ID を生成する](#generate-an-external-id)セクションでコピーした外部 ID を入力します。
`Require MFA` を無効にしたままにしてください。詳しくは、AWS のドキュメント、[第三者にお客様の AWS リソースへのアクセスを許可する際の外部 ID の使用方法][2]をご覧ください。
14. **Next** をクリックします。
15. すでにポリシーを作成している場合は、このページで検索し、選択してください。そうでない場合は、新しいウィンドウで開く **Create Policy** をクリックし、前のセクションの指示に従います。
16. <a href="https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/SecurityAudit" target="_blank">AWS SecurityAudit ポリシー</a>をロールにアタッチして、[リソース収集][5]を有効にしてください。
17. **Next** をクリックします。
18. ロールに `DatadogIntegrationRole` などの名前を付け、適切な説明を入力します。
19. **Create Role** をクリックします。

### Datadog で設定を完了する

20. 別のタブで開いていた Datadog のアカウントを手動で追加するための AWS インテグレーション構成ページに戻ります。チェックボックスをクリックして、Datadog の IAM ロールが AWS アカウントに追加されたことを確認します。
21. アカウント ID を**ダッシュなしで**入力します (例: `123456789012`)。アカウント ID は、Datadog 用に作成されたロールの ARN で確認することができます。
22. 前のセクションで作成したロールの名前を入力し、**Save** をクリックします。
  **注**: インテグレーションタイルに入力する名前は大文字と小文字が区別され、AWS のロール名と完全に一致する必要があります。
23. [Datadog is not authorized to perform sts:AssumeRole][6] エラーが発生した場合は、UI で推奨されているトラブルシューティングの手順を実行するか、[トラブルシューティングガイド][6]を読んでください。
24. データ収集が開始されるまで最大 10 分待ち、すぐに使える <a href="https://app.datadoghq.com/screen/integration/7/aws-overview" target="_blank">AWS 概要ダッシュボード</a>を表示し、AWS サービスやインフラストラクチャーから送信されるメトリクスを確認します。

*\* 中国本土における (または中国本土内の環境に関連する) Datadog サービスの使用はすべて、当社 Web サイトの[サービス制限地域][10]セクションに掲載されている免責事項に従うものとします。*


[1]: https://app.datadoghq.com/integrations/amazon-web-services
[2]: http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user_externalid.html
[3]: https://console.aws.amazon.com/iam/home#/policies
[4]: https://console.aws.amazon.com/iam/home#/roles
[5]: /ja/integrations/amazon_web_services/#resource-collection
[6]: /ja/integrations/guide/error-datadog-not-authorized-sts-assume-role/
[7]: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html
[8]: https://aws.amazon.com/blogs/security/easier-way-to-control-access-to-aws-regions-using-iam-policies/
[9]: https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/partitions.html
[10]: https://www.datadoghq.com/legal/restricted-service-locations/
{{% /tab %}}
{{% tab "アクセスキー" %}}

## セットアップ

### AWS

1. AWS のコンソールで、Datadog インテグレーションで使用する IAM ユーザーを[必要な権限](#aws-integration-iam-policy)で作成します。
2. Datadog インテグレーション IAM ユーザー用のアクセスキーとシークレットキーを生成します。

### Datadog

3. [AWS インテグレーションタイル][1]で、**Add AWS Account** をクリックし、** Manually** を選択します。
4. **Access Keys** タブを選択してください。
5. ご利用の AWS アカウントが属するパーティションを選択してください。パーティションには、商用リージョン用の `aws`、中国*向けの `aws-cn`、または GovCloud 向けの `aws-us-gov` があります。詳しくは、AWS ドキュメントの [Partitions][9] を参照してください。
5. **I confirm that the IAM User for the Datadog Integration has been added to the AWS Account** (Datadog インテグレーション用の IAM ユーザーが AWS アカウントに追加されていることを確認します) チェックボックスをクリックします。
6. `Account ID`、`AWS Access Key`、および `AWS Secret Key` を入力してください。
7. **Save** をクリックします。
8. データ収集が開始されるまで最大 10 分待ち、すぐに使える <a href="https://app.datadoghq.com/screen/integration/7/aws-overview" target="_blank">AWS 概要ダッシュボード</a>を表示し、AWS サービスやインフラストラクチャーから送信されるメトリクスを確認します。


[1]: https://app.datadoghq.com/integrations/amazon-web-services
[2]: https://www.datadoghq.com/legal/restricted-service-locations/
{{% /tab %}}
{{< /tabs >}}

{{% aws-permissions %}}

{{% aws-resource-collection %}}

{{< partial name="whats-next/whats-next.html" >}}

[1]: /ja/integrations/amazon_web_services/