---
title: Java Tests
code_lang: java
type: multi-code-lang
code_lang_weight: 10
aliases:
  - /continuous_integration/setup_tests/java
  - /continuous_integration/tests/java
  - /continuous_integration/tests/setup/java
further_reading:
    - link: "/tests/containers/"
      tag: "Documentation"
      text: "Forwarding Environment Variables for Tests in Containers"
    - link: "/tests/explorer"
      tag: "Documentation"
      text: "Explore Test Results and Performance"
    - link: "/tests/flaky_test_management/early_flake_detection"
      tag: "Documentation"
      text: "Detect test flakiness with Early Flake Detection"
    - link: "/tests/flaky_test_management/auto_test_retries"
      tag: "Documentation"
      text: "Retry failing test cases with Auto Test Retries"
    - link: "/tests/correlate_logs_and_tests"
      tag: "Documentation"
      text: "Correlate logs and test traces"
    - link: "/tests/troubleshooting/"
      tag: "Documentation"
      text: "Troubleshooting Test Optimization"
---

## Compatibility

Supported test frameworks:

| Test Framework | Version |
|---|---|
| JUnit 4 | >= 4.10 |
| JUnit 5 | >= 5.3 |
| TestNG | >= 6.4 |
| Spock | >= 2.0 |
| Cucumber | >= 5.4.0 |
| Karate | >= 1.0.0 |
| Scalatest | >= 3.0.8 |
| Scala MUnit | >= 0.7.28 |
| Scala Weaver | >= 0.8.4 (Only when using SBT as build system) |

If your test framework is not supported, you can try instrumenting your tests using [Manual Testing API][1].

Supported build systems:

| Build System | Version |
|---|---|
| Gradle | >= 2.0 |
| Maven | >= 3.2.1 |

Other build systems, such as Ant, Bazel, or SBT are supported with the following limitations:
- Automatic coverage configuration and reporting is not supported.
- When building a multi-module project, every module is reported in a separate trace.

## Setup

You may follow interactive setup steps on the [Datadog site][2] or the instructions below.

Configuring the Datadog Java Tracer varies depending on your CI provider.

{{< tabs >}}
{{% tab "CI Provider with Auto-Instrumentation Support" %}}
{{% ci-autoinstrumentation %}}
{{% /tab %}}

{{% tab "Other Cloud CI Provider" %}}
{{% ci-agentless %}}
{{% /tab %}}

{{% tab "On-Premises CI Provider" %}}
{{% ci-agent %}}
{{% /tab %}}
{{< /tabs >}}

### Downloading tracer library

You only need to download the tracer library once for each server.

If the tracer library is already available locally on the server, you can proceed directly to running the tests.

Declare `DD_TRACER_FOLDER` variable with the path to the folder where you want to store the downloaded tracer JAR:

{{< code-block lang="shell" >}}
export DD_TRACER_FOLDER=... // e.g. ~/.datadog
{{< /code-block >}}

Run the command below to download the tracer JAR to the specified folder:

{{< code-block lang="shell" >}}
wget -O $DD_TRACER_FOLDER/dd-java-agent.jar 'https://dtdg.co/latest-java-tracer'
{{< /code-block >}}

You can run the `java -jar $DD_TRACER_FOLDER/dd-java-agent.jar` command to check the version of the tracer library.

### Running your tests

{{< tabs >}}
{{% tab "Maven" %}}

Set the following environment variables to configure the tracer:

`DD_CIVISIBILITY_ENABLED=true` (Required)
: Enables the Test Optimization product.

`DD_ENV`
: Environment where the tests are being run (for example: `local` when running tests on a developer workstation or `ci` when running them on a CI provider).

`DD_SERVICE`
: Name of the service or library being tested.

`DD_TRACER_FOLDER` (Required)
: Path to the folder where the downloaded Java Tracer is located.

`MAVEN_OPTS=-javaagent:$DD_TRACER_FOLDER/dd-java-agent.jar` (Required)
: Injects the tracer into the Maven build process.

`DD_TEST_SESSION_NAME`
: Identifies a group of tests (for example: `unit-tests` or `integration-tests`).

Run your tests as you normally do (for example: `mvn test` or `mvn verify`).

{{% /tab %}}
{{% tab "Gradle" %}}

Set the following environment variables to configure the tracer:

`DD_CIVISIBILITY_ENABLED=true` (Required)
: Enables the Test Optimization product.

`DD_ENV`
: Environment where the tests are being run (for example: `local` when running tests on a developer workstation or `ci` when running them on a CI provider).

`DD_SERVICE`
: Name of the service or library being tested.

`DD_TRACER_FOLDER` (Required)
: Path to the folder where the downloaded Java Tracer is located.

`GRADLE_OPTS=-javaagent:$DD_TRACER_FOLDER/dd-java-agent.jar` (Required)
: Injects the tracer into the Gradle launcher process.

Run your tests as you normally do (for example: `./gradlew clean test`).

{{% /tab %}}
{{% tab "SBT" %}}

Set the following environment variables to configure the tracer:

`DD_CIVISIBILITY_ENABLED=true` (Required)
: Enables the Test Optimization product.

`DD_TEST_SESSION_NAME`
: Identifies a group of tests (for example: `unit-tests` or `integration-tests`).

`DD_ENV`
: Environment where the tests are being run (for example: `local` when running tests on a developer workstation or `ci` when running them on a CI provider).

`DD_SERVICE`
: Name of the service or library being tested.

`DD_TRACER_FOLDER` (Required)
: Path to the folder where the downloaded Java Tracer is located.

`SBT_OPTS=-javaagent:$DD_TRACER_FOLDER/dd-java-agent.jar` (Required)
: Injects the tracer into the JVMs that execute your tests.

Run your tests as you normally do (for example: `sbt test`).

{{% /tab %}}
{{% tab "Other" %}}

Set the following environment variables to configure the tracer:

`DD_CIVISIBILITY_ENABLED=true` (Required)
: Enables the Test Optimization product.

`DD_TEST_SESSION_NAME`
: Identifies a group of tests (for example: `unit-tests` or `integration-tests`).

`DD_ENV`
: Environment where the tests are being run (for example: `local` when running tests on a developer workstation or `ci` when running them on a CI provider).

`DD_SERVICE`
: Name of the service or library being tested.

`DD_TRACER_FOLDER` (Required)
: Path to the folder where the downloaded Java Tracer is located.

`JAVA_TOOL_OPTIONS=-javaagent:$DD_TRACER_FOLDER/dd-java-agent.jar` (Required)
: Injects the tracer into the JVMs that execute your tests.

Run your tests as you normally do.

{{% /tab %}}
{{< /tabs >}}

## Configuration

Default configuration values work well in most cases.

However, if there is a need to fine-tune the tracer's behavior, [Datadog Tracer configuration][3] options can be used.

### Collecting Git metadata

{{% ci-git-metadata %}}

## Extensions

The tracer exposes a set of APIs that can be used to extend its functionality programmatically.

### Adding custom tags to tests

{{< tabs >}}
{{% tab "OpenTelemetry API" %}}

To add custom tags, include the [opentelemetry-api][1] library as a compile-time dependency and set `dd.trace.otel.enabled` (system property) or `DD_TRACE_OTEL_ENABLED` (environment variable) to `true`.

You can then add custom tags to your tests by using the active span:

```java
import io.opentelemetry.api.trace.Span;

// ...
// inside your test
Span span = Span.current();
span.setAttribute("test_owner", "my_team");
// test continues normally
// ...
```

For more information about adding tags, see the [Adding Tags][2] section of the Java custom instrumentation documentation.

[1]: https://mvnrepository.com/artifact/io.opentelemetry/opentelemetry-api
[2]: /tracing/trace_collection/custom_instrumentation/java?tab=locally#adding-tags

{{% /tab %}}
{{% tab "OpenTracing API" %}}

To add custom tags, include the [opentracing-util][1] library as a compile-time dependency to your project.

You can then add custom tags to your tests by using the active span:

```java
import io.opentracing.Span;
import io.opentracing.util.GlobalTracer;

// ...
// inside your test
final Span span = GlobalTracer.get().activeSpan();
if (span != null) {
  span.setTag("test_owner", "my_team");
}
// test continues normally
// ...
```

To create filters or `group by` fields for these tags, you must first create facets.

For more information about adding tags, see the [Adding Tags][2] section of the Java custom instrumentation documentation.

[1]: https://mvnrepository.com/artifact/io.opentracing/opentracing-util
[2]: /tracing/trace_collection/custom_instrumentation/java?tab=locally#adding-tags

{{% /tab %}}
{{< /tabs >}}

### Adding custom measures to tests

Just like tags, you can add custom measures to your tests by using the current active span:

{{< tabs >}}
{{% tab "OpenTelemetry API" %}}

```java
import io.opentelemetry.api.trace.Span;

// ...
// inside your test
Span span = Span.current();
span.setAttribute("test.memory.usage", 1e8);
// test continues normally
// ...
```

{{% /tab %}}
{{% tab "OpenTracing API" %}}

```java
import io.opentracing.Span;
import io.opentracing.util.GlobalTracer;

// ...
// inside your test
final Span span = GlobalTracer.get().activeSpan();
if (span != null) {
  span.setTag("test.memory.usage", 1e8);
}
// test continues normally
// ...
```

{{% /tab %}}
{{< /tabs >}}

For more information about custom measures, see the [Add Custom Measures guide][6].

### Using manual testing API

If you use one of the supported testing frameworks, the Java Tracer automatically instruments your tests and sends the results to the Datadog backend.

If you are using a framework that is not supported, or an ad-hoc testing solution, you can harness the manual testing API, which also reports test results to the backend.

To use the manual testing API, add the [`dd-trace-api`][7] library as a compile-time dependency to your project.

#### Domain model

The API is based around four concepts: test session, test module, test suite, and test.

##### Test session

A test session represents a project build, which typically corresponds to execution of a test command issued by a user or by a CI script.

To start a test session, call `datadog.trace.api.civisibility.CIVisibility#startSession` and pass the name of the project and the name of the testing framework you used.

When all your tests have finished, call `datadog.trace.api.civisibility.DDTestSession#end`, which forces the library to send all remaining test results to the backend.

##### Test module

A test module represents a smaller unit of work within a project build, typically corresponding to a project module. For example, a Maven submodule or Gradle subproject.

To start a test mode, call `datadog.trace.api.civisibility.DDTestSession#testModuleStart` and pass the name of the module.

When the module has finished building and testing, call `datadog.trace.api.civisibility.DDTestModule#end`.

##### Test Suite

A test suite comprises a set of tests that share common functionality.
They can share a common initialization and teardown, and can also share some variables.
A single suite usually corresponds to a Java class that contains test cases.

Create test suites in a test module by calling `datadog.trace.api.civisibility.DDTestModule#testSuiteStart` and passing the name of the test suite.

Call `datadog.trace.api.civisibility.DDTestSuite#end` when all the related tests in the suite have finished their execution.

##### Test

A test represents a single test case that is executed as part of a test suite.
Usually it corresponds to a method that contains testing logic.

Create tests in a suite by calling `datadog.trace.api.civisibility.DDTestSuite#testStart` and passing the name of the test.

Call `datadog.trace.api.civisibility.DDTest#end` when a test has finished execution.

#### Code Example

The following code represents a simple usage of the API:

```java
package com.datadog.civisibility.example;

import datadog.trace.api.civisibility.CIVisibility;
import datadog.trace.api.civisibility.DDTest;
import datadog.trace.api.civisibility.DDTestModule;
import datadog.trace.api.civisibility.DDTestSession;
import datadog.trace.api.civisibility.DDTestSuite;
import java.lang.reflect.Method;

// the null arguments in the calls below are optional startTime/endTime values:
// when they are not specified, current time is used
public class ManualTest {
    public static void main(String[] args) throws Exception {
        DDTestSession testSession = CIVisibility.startSession("my-project-name", "my-test-framework", null);
        testSession.setTag("my-tag", "additional-session-metadata");
        try {
            runTestModule(testSession);
        } finally {
            testSession.end(null);
        }
    }

    private static void runTestModule(DDTestSession testSession) throws Exception {
        DDTestModule testModule = testSession.testModuleStart("my-module", null);
        testModule.setTag("my-module-tag", "additional-module-metadata");
        try {
            runFirstTestSuite(testModule);
            runSecondTestSuite(testModule);
        } finally {
            testModule.end(null);
        }
    }

    private static void runFirstTestSuite(DDTestModule testModule) throws Exception {
        DDTestSuite testSuite = testModule.testSuiteStart("my-suite", ManualTest.class, null);
        testSuite.setTag("my-suite-tag", "additional-suite-metadata");
        try {
            runTestCase(testSuite);
        } finally {
            testSuite.end(null);
        }
    }

    private static void runTestCase(DDTestSuite testSuite) throws Exception {
        Method myTestCaseMethod = ManualTest.class.getDeclaredMethod("myTestCase");
        DDTest ddTest = testSuite.testStart("myTestCase", myTestCaseMethod, null);
        ddTest.setTag("my-test-case-tag", "additional-test-case-metadata");
        ddTest.setTag("my-test-case-tag", "more-test-case-metadata");
        try {
            myTestCase();
        } catch (Exception e) {
            ddTest.setErrorInfo(e); // pass error info to mark test case as failed
        } finally {
            ddTest.end(null);
        }
    }

    private static void myTestCase() throws Exception {
        // run some test logic
    }

    private static void runSecondTestSuite(DDTestModule testModule) {
        DDTestSuite secondTestSuite = testModule.testSuiteStart("my-second-suite", ManualTest.class, null);
        secondTestSuite.setSkipReason("this test suite is skipped"); // pass skip reason to mark test suite as skipped
        secondTestSuite.end(null);
    }
}
```

Always call ``datadog.trace.api.civisibility.DDTestSession#end`` at the end so that all the test info is flushed to Datadog.

## Best practices

### Deterministic test parameters representation

Test Optimization works best when the [test parameters are deterministic][8] and stay the same between test runs.
If a test case has a parameter that varies between test executions (such as a current date, a random number, or an instance of a class whose `toString()` method is not overridden), some of the product features may not work as expected.
For example, the history of executions may not be available, or the test case may not be classified as flaky even if it exhibits flakiness.

The best way to fix this is to make sure that the test parameters are the same between test runs.

In JUnit 5, this can also be addressed by [customizing the string representation of the test parameters][9] without changing their values.
To do so, use `org.junit.jupiter.api.Named` interface or change the `name` parameter of the `org.junit.jupiter.params.ParameterizedTest` annotation:

```java
@ParameterizedTest
@MethodSource("namedArguments")
void parameterizedTest(String s, Date d) {
   // The second parameter in this test case is non-deterministic.
   // In the argument provider method it is wrapped with Named to ensure it has a deterministic name.
}

static Stream<Arguments> namedArguments() {
    return Stream.of(
            Arguments.of(
                    "a string",
                    Named.of("current date", new Date())),
            Arguments.of(
                    "another string",
                    Named.of("a date in the future", new Date(System.currentTimeMillis() + TimeUnit.DAYS.toMillis(1))))
    );
}
```

```java
@ParameterizedTest(name = "[{index}] {0}, a random number from one to ten")
@MethodSource("randomArguments")
void anotherParameterizedTest(String s, int i) {
  // The second parameter in this test case is non-deterministic.
  // The name of the parameterized test is customized to ensure it has a deterministic name.
}

static Stream<Arguments> randomArguments() {
    return Stream.of(
            Arguments.of("a string", ThreadLocalRandom.current().nextInt(10) + 1),
            Arguments.of("another string", ThreadLocalRandom.current().nextInt(10) + 1)
    );
}
```

### Test session name `DD_TEST_SESSION_NAME`

Use `DD_TEST_SESSION_NAME` to define the name of the test session and the related group of tests. Examples of values for this tag would be:

- `unit-tests`
- `integration-tests`
- `smoke-tests`
- `flaky-tests`
- `ui-tests`
- `backend-tests`

If `DD_TEST_SESSION_NAME` is not specified, the default value used is a combination of the:

- CI job name
- Command used to run the tests (such as `mvn test`)

The test session name needs to be unique within a repository to help you distinguish different groups of tests.

#### When to use `DD_TEST_SESSION_NAME`

There's a set of parameters that Datadog checks to establish correspondence between test sessions. The test command used to execute the tests is one of them. If the test command contains a string that changes for every execution, such as a temporary folder, Datadog considers the sessions to be unrelated to each other. For example:

- `mvn test --temp-dir=/var/folders/t1/rs2htfh55mz9px2j4prmpg_c0000gq/T`

Datadog recommends using `DD_TEST_SESSION_NAME` if your test commands vary between executions.

## Troubleshooting

### The tests are not appearing in Datadog after enabling Test Optimization in the tracer

Verify that the tracer is injected into your build process by examining your build's logs.
If the injection is successful, you can see a line containing `DATADOG TRACER CONFIGURATION`.
If the line is not there, make sure that the environment variables used to inject and configure the tracer are available to the build process.
A common mistake is to set the variables in a build step and run the tests in another build step. This approach may not work if the variables are not propagated between build steps.

Ensure that you are using the latest version of the tracer.

Verify that your build system and testing framework are supported by Test Optimization. See the list of [supported build systems and test frameworks](#compatibility).

Ensure that the `dd.civisibility.enabled` property (or `DD_CIVISIBILITY_ENABLED` environment variable) is set to `true` in the tracer arguments.

Try running your build with tracer debug logging enabled by setting the `DD_TRACE_DEBUG` environment variable to `true`.
Check the build output for any errors that indicate tracer misconfiguration, such as an unset `DD_API_KEY` environment variable.

### Tests or source code compilation fails when building a project with the tracer attached

By default, Test Optimization runs Java code compilation with a compiler plugin attached.

The plugin is optional, as it only serves to reduce the performance overhead.

Depending on the build configuration, adding the plugin can sometimes disrupt the compilation process.

If the plugin interferes with the build, disable it by adding `dd.civisibility.compiler.plugin.auto.configuration.enabled=false` to the list of `-javaagent` arguments
(or by setting `DD_CIVISIBILITY_COMPILER_PLUGIN_AUTO_CONFIGURATION_ENABLED=false` environment variable).

### Builds fails because dd-javac-plugin-client artifact cannot be found

It is possible that the Java compiler plugin injected into the build is not available if the build uses a custom artifactory storage or if it is run in offline mode.

If this is the case, you can disable plugin injection by adding `dd.civisibility.compiler.plugin.auto.configuration.enabled=false` to the list of `-javaagent` arguments
(or by setting the `DD_CIVISIBILITY_COMPILER_PLUGIN_AUTO_CONFIGURATION_ENABLED` environment variable to false).

The plugin is optional, as it only serves to reduce the performance overhead.

### Tests fail when building a project with the tracer attached

In some cases attaching the tracer can break tests, especially if they run asserts on the internal state of the JVM or instances of third-party libraries' classes.

While the best approach is such cases is to update the tests, there is also a quicker option of disabling the tracer's third-party library integrations.

The integrations provide additional insights into what happens in the tested code and are especially useful in integration tests, to monitor things like HTTP requests or database calls.
They are enabled by default.

To disable a specific integration, refer to the [Datadog Tracer Compatibility][10] table for the relevant configuration property names.
For example, to disable `OkHttp3` client request integration, add `dd.integration.okhttp-3.enabled=false` to the list of `-javaagent` arguments.

To disable all integrations, augment the list of `-javaagent` arguments with `dd.trace.enabled=false` (or set `DD_TRACE_ENABLED=false` environment variable).

## Further reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: #using-manual-testing-api
[2]: https://app.datadoghq.com/ci/setup/test?language=java
[3]: /tracing/trace_collection/library_config/java/?tab=containers#configuration
[6]: /tests/guides/add_custom_measures/?tab=java
[7]: https://mvnrepository.com/artifact/com.datadoghq/dd-trace-api
[8]: /tests/#parameterized-test-configurations
[9]: https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-display-names
[10]: /tracing/trace_collection/compatibility/java#integrations
