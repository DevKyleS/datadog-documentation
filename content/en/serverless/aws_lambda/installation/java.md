---
title: Instrumenting Java Serverless Applications
further_reading:
    - link: '/serverless/configuration'
      tag: 'Documentation'
      text: 'Configure Serverless Monitoring'
    - link: '/serverless/guide/troubleshoot_serverless_monitoring'
      tag: 'Documentation'
      text: 'Troubleshoot Serverless Monitoring'
    - link: 'serverless/custom_metrics/'
      tag: 'Documentation'
      text: 'Submitting Custom Metrics from Serverless Applications'
aliases:
    - /serverless/datadog_lambda_library/java/
    - /serverless/installation/java
---

<div class="alert alert-info">Version 67+ of the Datadog Lambda Extension uses an optimized version of the extension. <a href="#minimize-cold-start-duration">Read more</a>.</div>

<div class="alert alert-info">For FIPS compliance, use the Datadog FIPS-compliant extension layer, but note that the Java runtime layer does not require additional configuration. While the FIPS-compliant Lambda components work with any Datadog site, end-to-end FIPS compliance requires using the US1-FED site. See <a href="/serverless/aws_lambda/fips-compliance">AWS Lambda FIPS Compliance</a> for more details.</div>

To fully instrument your serverless application with distributed tracing, your Java Lambda functions must be using the Java 8 Corretto (`java8.al2`), Java 11 (`java11`), Java 17 (`java17`), or Java 21 (`java21`) runtimes with at least 1024 MB of memory.

If your Lambda functions are deployed in a VPC without access to the public internet, you can send data either [using AWS PrivateLink][6] for the `datadoghq.com` [Datadog site][7], or [using a proxy][8] for all other sites.

If you previously set up your Lambda functions using the Datadog Forwarder, see [instrumenting using the Datadog Forwarder][9]. Otherwise, follow the instructions in this guide to instrument using the Datadog Lambda Extension.

If you are using the Datadog Lambda layers `dd-trace-java:4` (or older) and `Datadog-Extension:24` (or older), follow the [special instructions to upgrade][10].

## Installation

<div class="alert alert-info">A sample application is <a href="https://github.com/DataDog/serverless-sample-app/tree/main/src/inventory-service">available on GitHub</a> with instructions on how to deploy with multiple runtimes and infrastructure as code tools.</div>

Datadog offers many different ways to enable instrumentation for your serverless applications. Choose a method below that best suits your needs. Datadog generally recommends using the Datadog CLI. You *must* follow the instructions for "Container Image" if your application is deployed as a container image.

{{< tabs >}}
{{% tab "Datadog CLI" %}}

The Datadog CLI modifies existing Lambda functions' configurations to enable instrumentation without requiring a new deployment. It is the quickest way to get started with Datadog's serverless monitoring.

1. Install the Datadog CLI client

    ```sh
    npm install -g @datadog/datadog-ci
    ```

2. If you are new to Datadog serverless monitoring, launch the Datadog CLI in interactive mode to guide your first installation for a quick start, and you can ignore the remaining steps on this page. To permanently install Datadog for your production applications, skip this step and follow the remaining ones to run the Datadog CLI command in your CI/CD pipelines _after_ your normal deployment.

    ```sh
    datadog-ci lambda instrument -i
    ```

3. Configure the AWS credentials

    The Datadog CLI requires access to the AWS Lambda service and depends on the AWS JavaScript SDK to [resolve the credentials][1]. Ensure your AWS credentials are configured using the same method you would use when invoking the AWS CLI.

4. Configure the Datadog site

    ```sh
    export DATADOG_SITE="<DATADOG_SITE>"
    ```

    Replace `<DATADOG_SITE>` with {{< region-param key="dd_site" code="true" >}} (ensure the correct SITE is selected on the right).

5. Configure your Datadog API key

    Datadog recommends saving the Datadog API key in AWS Secrets Manager for security and easy rotation. The key needs to be stored as a plaintext string (not a JSON blob). Ensure your Lambda functions have the required `secretsmanager:GetSecretValue` IAM permission.

    ```sh
    export DATADOG_API_KEY_SECRET_ARN="<DATADOG_API_KEY_SECRET_ARN>"
    ```

    For quick testing purposes, you can also set the Datadog API key in plaintext:

    ```sh
    export DATADOG_API_KEY="<DATADOG_API_KEY>"
    ```

6. Instrument your Lambda functions

    **Note**: Instrument your Lambda functions in a dev or staging environment first. If your instrumentation results are unsatisfactory, you can run `uninstrument` with the same arguments to revert the changes.

    To instrument your Lambda functions, run the following command.

    ```sh
    datadog-ci lambda instrument -f <functionname> -f <another_functionname> -r <aws_region> -v {{< latest-lambda-layer-version layer="dd-trace-java" >}} -e {{< latest-lambda-layer-version layer="extension" >}}
    ```

    To fill in the placeholders:
    - Replace `<functionname>` and `<another_functionname>` with your Lambda function names. Alternatively, you can use `--functions-regex` to automatically instrument multiple functions whose names match the given regular expression.
    - Replace `<aws_region>` with the AWS region name.

    Additional parameters can be found in the [CLI documentation][2].

[1]: https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-credentials-node.html
[2]: https://docs.datadoghq.com/serverless/serverless_integrations/cli
{{% /tab %}}
{{% tab "Serverless Framework" %}}

The [Datadog Serverless Plugin][1] automatically configures your functions to send metrics, traces, and logs to Datadog through the [Datadog Lambda Extension][2].

To install and configure the Datadog Serverless Plugin, follow these steps:

1. Install the Datadog Serverless Plugin:

    ```sh
    serverless plugin install --name serverless-plugin-datadog
    ```

2. Update your `serverless.yml`:

    ```yaml
    custom:
      datadog:
        site: <DATADOG_SITE>
        apiKeySecretArn: <DATADOG_API_KEY_SECRET_ARN>
    ```

    To fill in the placeholders:
    - Replace `<DATADOG_SITE>` with {{< region-param key="dd_site" code="true" >}} (ensure the correct SITE is selected on the right).
    - Replace `<DATADOG_API_KEY_SECRET_ARN>` with the ARN of the AWS secret where your [Datadog API key][3] is securely stored. The key needs to be stored as a plaintext string (not a JSON blob). The `secretsmanager:GetSecretValue` permission is required. For quick testing, you can instead use `apiKey` and set the Datadog API key in plaintext.

    For more information and additional settings, see the [plugin documentation][1].

[1]: https://docs.datadoghq.com/serverless/serverless_integrations/plugin
[2]: https://docs.datadoghq.com/serverless/libraries_integrations/extension
[3]: https://app.datadoghq.com/organization-settings/api-keys
{{% /tab %}}
{{% tab "AWS SAM" %}}

The [Datadog CloudFormation macro][1] automatically transforms your SAM application template to install Datadog on your functions using Lambda layers, and configures your functions to send metrics, traces, and logs to Datadog through the [Datadog Lambda Extension][2].

1. Install the Datadog CloudFormation macro

    Run the following command with your [AWS credentials][3] to deploy a CloudFormation stack that installs the macro AWS resource. You only need to install the macro **once** for a given region in your account. Replace `create-stack` with `update-stack` to update the macro to the latest version.

    ```sh
    aws cloudformation create-stack \
      --stack-name datadog-serverless-macro \
      --template-url https://datadog-cloudformation-template.s3.amazonaws.com/aws/serverless-macro/latest.yml \
      --capabilities CAPABILITY_AUTO_EXPAND CAPABILITY_IAM
    ```

    The macro is now deployed and ready to use.

2. Instrument your Lambda functions

    Add the `DatadogServerless` transform **after** the `AWS::Serverless` transform under the `Transform` section in your `template.yml` file for SAM.

    ```yaml
    Transform:
      - AWS::Serverless-2016-10-31
      - Name: DatadogServerless
        Parameters:
          stackName: !Ref "AWS::StackName"
          javaLayerVersion: {{< latest-lambda-layer-version layer="dd-trace-java" >}}
          extensionLayerVersion: {{< latest-lambda-layer-version layer="extension" >}}
          site: "<DATADOG_SITE>"
          apiKeySecretArn: "<DATADOG_API_KEY_SECRET_ARN>"
    ```

    To fill in the placeholders:
    - Replace `<DATADOG_SITE>` with {{< region-param key="dd_site" code="true" >}} (ensure the correct SITE is selected on the right).
    - Replace `<DATADOG_API_KEY_SECRET_ARN>` with the ARN of the AWS secret where your [Datadog API key][4] is securely stored. The key needs to be stored as a plaintext string (not a JSON blob). The `secretsmanager:GetSecretValue` permission is required. For quick testing, you can use `apiKey` instead and set the Datadog API key in plaintext.

    More information and additional parameters can be found in the [macro documentation][1].


[1]: https://docs.datadoghq.com/serverless/serverless_integrations/macro
[2]: https://docs.datadoghq.com/serverless/libraries_integrations/extension
[3]: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html
[4]: https://app.datadoghq.com/organization-settings/api-keys
{{% /tab %}}
{{% tab "AWS CDK" %}}

<div class="alert alert-info">Instrumenting Java functions through the Datadog CDK construct is only available for AWS CDK apps written in Node.js and Python.</div>

The [Datadog CDK construct][1] automatically installs Datadog on your functions using Lambda layers. It configures your functions to send metrics, traces, and logs to Datadog through the Datadog Lambda Extension.

1. Install the Datadog CDK constructs library

    **Node.js**:
    ```sh
    # For AWS CDK v1
    npm install datadog-cdk-constructs --save-dev

    # For AWS CDK v2
    npm install datadog-cdk-constructs-v2 --save-dev
    ```

    **Python**:
    ```sh
    # For AWS CDK v1
    pip install datadog-cdk-constructs

    # For AWS CDK v2
    pip install datadog-cdk-constructs-v2
    ```

2. Instrument your Lambda functions

    **Node.js**:
    ```javascript
    // For AWS CDK v1
    import { Datadog } from "datadog-cdk-constructs";

    // For AWS CDK v2
    import { Datadog } from "datadog-cdk-constructs-v2";

    const datadog = new Datadog(this, "Datadog", {
        javaLayerVersion: {{< latest-lambda-layer-version layer="dd-trace-java" >}},
        extensionLayerVersion: {{< latest-lambda-layer-version layer="extension" >}},
        site: "<DATADOG_SITE>",
        apiKeySecretArn: "<DATADOG_API_KEY_SECRET_ARN>"
    });
    datadog.addLambdaFunctions([<LAMBDA_FUNCTIONS>])
    ```

    **Python**:
    ```python
    # For AWS CDK v1
    from datadog_cdk_constructs import Datadog

    # For AWS CDK v2
    from datadog_cdk_constructs_v2 import Datadog

    datadog = Datadog(self, "Datadog",
        java_layer_version={{< latest-lambda-layer-version layer="dd-trace-java" >}},
        extension_layer_version={{< latest-lambda-layer-version layer="extension" >}},
        site="<DATADOG_SITE>",
        api_key_secret_arn="<DATADOG_API_KEY_SECRET_ARN>",
      )
    datadog.add_lambda_functions([<LAMBDA_FUNCTIONS>])
    ```

    To fill in the placeholders:
    - Replace `<DATADOG_SITE>` with {{< region-param key="dd_site" code="true" >}} (ensure the correct SITE is selected on the right).
    - Replace `<DATADOG_API_KEY_SECRET_ARN>` with the ARN of the AWS secret where your [Datadog API key][2] is securely stored. The key needs to be stored as a plaintext string (not a JSON blob). Ensure your Lambda execution role has the `secretsmanager:GetSecretValue` IAM permission in order to read the secret value. For quick testing, you can use `apiKey` instead and set the Datadog API key in plaintext.

    More information and additional parameters can be found on the [Datadog CDK documentation][1].

[1]: https://github.com/DataDog/datadog-cdk-constructs
[2]: https://app.datadoghq.com/organization-settings/api-keys
{{% /tab %}}
{{% tab "Container image" %}}

1. Install the Datadog Lambda Extension

    ```dockerfile
    COPY --from=public.ecr.aws/datadog/lambda-extension:<TAG> /opt/. /opt/
    ```

    Replace `<TAG>` with either a specific version number (for example, `{{< latest-lambda-layer-version layer="extension" >}}`) or with `latest`. Alpine is also supported with specific version numbers (such as `{{< latest-lambda-layer-version layer="extension" >}}-alpine`) or with `latest-alpine`. You can see a complete list of possible tags in the [Amazon ECR repository][1].

2. Install the Datadog Java APM client

    ```dockerfile
    RUN yum -y install tar wget gzip
    RUN wget -O /opt/java/lib/dd-java-agent.jar 'https://dtdg.co/latest-java-tracer'
    ```

3. Set the required environment variables

    - Set `AWS_LAMBDA_EXEC_WRAPPER` to `/opt/datadog_wrapper`.
    - Set `DD_SITE` to {{< region-param key="dd_site" code="true" >}} (ensure the correct SITE is selected on the right).
    - Set `DD_API_KEY_SECRET_ARN` to the ARN of the AWS secret where your [Datadog API key][2] is securely stored. The key needs to be stored as a plaintext string (not a JSON blob). The `secretsmanager:GetSecretValue` permission is required. For quick testing, you can use `DD_API_KEY` instead and set the Datadog API key in plaintext.

[1]: https://gallery.ecr.aws/datadog/lambda-extension
[2]: https://app.datadoghq.com/organization-settings/api-keys
{{% /tab %}}
{{% tab "Terraform" %}}

The [`lambda-datadog`][1] Terraform module wraps the [`aws_lambda_function`][2] resource and automatically configures your Lambda function for Datadog Serverless Monitoring by:

- Adding the Datadog Lambda layers
- Redirecting the Lambda handler
- Enabling the collection and sending of metrics, traces, and logs to Datadog

```tf
module "lambda-datadog" {
  source  = "DataDog/lambda-datadog/aws"
  version = "3.2.0"

  environment_variables = {
    "DD_API_KEY_SECRET_ARN" : "<DATADOG_API_KEY_SECRET_ARN>"
    "DD_ENV" : "<ENVIRONMENT>"
    "DD_SERVICE" : "<SERVICE_NAME>"
    "DD_SITE": "<DATADOG_SITE>"
    "DD_VERSION" : "<VERSION>"
  }

  datadog_extension_layer_version = {{< latest-lambda-layer-version layer="extension" >}}
  datadog_java_layer_version = {{< latest-lambda-layer-version layer="dd-trace-java" >}}

  # aws_lambda_function arguments
}
```

1. Replace the `aws_lambda_function` resource with the `lambda-datadog` Terraform module. Then, specify the `source` and `version` of the module.

2. Set the `aws_lambda_function` arguments:

   All of the arguments available in the `aws_lambda_function` resource are available in this Terraform module. Arguments defined as blocks in the `aws_lambda_function` resource are redefined as variables with their nested arguments.

   For example, in `aws_lambda_function`, `environment` is defined as a block with a `variables` argument. In the `lambda-datadog` Terraform module, the value for the `environment_variables` is passed to the `environment.variables` argument in `aws_lambda_function`. See [inputs][3] for a complete list of variables in this module.

3. Fill in the environment variable placeholders:

   - Replace `<DATADOG_API_KEY_SECRET_ARN>` with the ARN of the AWS secret where your Datadog API key is securely stored. The key needs to be stored as a plaintext string (not a JSON blob). The `secretsmanager:GetSecretValue` permission is required. For quick testing, you can instead use the environment variable `DD_API_KEY` and set your Datadog API key in plaintext.
   - Replace `<ENVIRONMENT>` with the Lambda function's environment, such as `prod` or `staging`
   - Replace `<SERVICE_NAME>` with the name of the Lambda function's service
   - Replace `<DATADOG_SITE>` with {{< region-param key="dd_site" code="true" >}}. (Ensure the correct [Datadog site][4] is selected on this page).
   - Replace `<VERSION>` with the version number of the Lambda function

4. Select the versions of the Datadog Extension Lambda layer and Datadog Java Lambda layer to use. If left blank the latest layer versions will be used.

```
  datadog_extension_layer_version = {{< latest-lambda-layer-version layer="extension" >}}
  datadog_java_layer_version = {{< latest-lambda-layer-version layer="dd-trace-java" >}}
```

[1]: https://registry.terraform.io/modules/DataDog/lambda-datadog/aws/latest
[2]: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lambda_function
[3]: https://github.com/DataDog/terraform-aws-lambda-datadog?tab=readme-ov-file#inputs
[4]: /getting_started/site/
{{% /tab %}}
{{% tab "Custom" %}}

1. Install the Datadog Tracer

    [Configure the layers][1] for your Lambda function using the ARN in the following format:

    ```sh
    # Use this format for Lambda deployed in AWS commercial regions
    arn:aws:lambda:<AWS_REGION>:464622532012:layer:dd-trace-java:{{< latest-lambda-layer-version layer="dd-trace-java" >}}

    # Use this format for Lambda deployed in AWS GovCloud regions
    arn:aws-us-gov:lambda:<AWS_REGION>:002406178527:layer:dd-trace-java:{{< latest-lambda-layer-version layer="dd-trace-java" >}}
    ```

    Replace `<AWS_REGION>` with a valid AWS region, such as `us-east-1`.

2. Install the Datadog Lambda Extension

    [Configure the layers][1] for your Lambda function using the ARN in the following format:

    ```sh
    # Use this format for x86-based Lambda deployed in AWS commercial regions
    arn:aws:lambda:<AWS_REGION>:464622532012:layer:Datadog-Extension:{{< latest-lambda-layer-version layer="extension" >}}

    # Use this format for arm64-based Lambda deployed in AWS commercial regions
    arn:aws:lambda:<AWS_REGION>:464622532012:layer:Datadog-Extension-ARM:{{< latest-lambda-layer-version layer="extension" >}}

    # Use this format for x86-based Lambda deployed in AWS GovCloud regions
    arn:aws-us-gov:lambda:<AWS_REGION>:002406178527:layer:Datadog-Extension:{{< latest-lambda-layer-version layer="extension" >}}

    # Use this format for arm64-based Lambda deployed in AWS GovCloud regions
    arn:aws-us-gov:lambda:<AWS_REGION>:002406178527:layer:Datadog-Extension-ARM:{{< latest-lambda-layer-version layer="extension" >}}
    ```

    Replace `<AWS_REGION>` with a valid AWS region, such as `us-east-1`.

3. Set the required environment variables

    - Set `AWS_LAMBDA_EXEC_WRAPPER` to `/opt/datadog_wrapper`.
    - Set `DD_SITE` to {{< region-param key="dd_site" code="true" >}} (ensure the correct SITE is selected on the right).
    - Set `DD_API_KEY_SECRET_ARN` to the ARN of the AWS secret where your [Datadog API key][2] is securely stored. The key needs to be stored as a plaintext string (not a JSON blob). The `secretsmanager:GetSecretValue` permission is required. For quick testing, you can use `DD_API_KEY` instead and set the Datadog API key in plaintext.

[1]: https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html
[2]: https://app.datadoghq.com/organization-settings/api-keys
{{% /tab %}}
{{< /tabs >}}

## Minimize cold start duration
Version 67+ of [the Datadog Extension][12] is optimized to significantly reduce cold start duration.

To use the optimized extension, disable App and API Protection (AAP), Continuous Profiler for Lambda, and OpenTelemetry based tracing. Set the following environment variables to `false`:

- `DD_TRACE_OTEL_ENABLED`
- `DD_PROFILING_ENABLED`
- `DD_SERVERLESS_APPSEC_ENABLED`

Enabling any of these features cause the extension to default back to the fully compatible older version of the extension. You can also force your extension to use the older version by setting `DD_EXTENSION_VERSION` to `compatibility`. Datadog encourages you to report any feedback or bugs by adding an [issue on GitHub][13] and tagging your issue with `version/next`.

## What's next?

- View metrics, logs, and traces on the [Serverless page][1] in Datadog. By default, the Datadog Lambda extension enables logs.
- Turn on [threat monitoring][11] to get alerted on attackers targeting your service.
- Submit a [custom metric][2] or [APM span][3] to monitor your business logic.
- See the [troubleshooting guide][4] if you have trouble collecting the telemetry
- See the [advanced configurations][5] to
    - connect your telemetry using tags
    - collect telemetry for Amazon API Gateway, SQS, etc.
    - capture the Lambda request and response payloads
    - link errors of your Lambda functions to your source code
    - filter or scrub sensitive information from logs or traces

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://app.datadoghq.com/functions
[2]: https://docs.datadoghq.com/metrics/dogstatsd_metrics_submission/
[3]: /tracing/custom_instrumentation/java/
[4]: /serverless/guide/troubleshoot_serverless_monitoring/
[5]: /serverless/configuration/
[6]: /agent/guide/private-link/
[7]: /getting_started/site/
[8]: /agent/configuration/proxy/
[9]: /serverless/guide/datadog_forwarder_java
[10]: /serverless/guide/upgrade_java_instrumentation
[11]: /security/application_security/serverless/
[12]: https://github.com/DataDog/datadog-lambda-extension
[13]: https://github.com/DataDog/datadog-lambda-extension/issues
