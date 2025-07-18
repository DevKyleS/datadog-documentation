---
title: Ruby Tests
code_lang: ruby
type: multi-code-lang
code_lang_weight: 40
aliases:
  - /continuous_integration/setup_tests/ruby
  - /continuous_integration/tests/ruby
  - /continuous_integration/tests/setup/ruby
further_reading:
    - link: "/continuous_integration/tests/containers/"
      tag: "Documentation"
      text: "Forwarding Environment Variables for Tests in Containers"
    - link: "/continuous_integration/tests"
      tag: "Documentation"
      text: "Explore Test Results and Performance"
    - link: "/tests/troubleshooting/"
      tag: "Documentation"
      text: "Troubleshooting Test Optimization"
---

## Compatibility

Supported languages:

| Language | Version |
|---|---|
| Ruby | >= 2.7 |
| JRuby | >= 9.4 |

Supported test frameworks:

| Test Framework | Version |
|---|---|
| RSpec | >= 3.0.0 |
| Minitest | >= 5.0.0 |
| Cucumber | >= 3.0 |

Supported test runners:

| Test runner | Version |
|---|---|
| Knapsack Pro | >= 7.2.0 |
| parallel_tests | >= 4.0.0 |
| ci-queue | >= 0.53.0 |

## Configuring reporting method

To report test results to Datadog, you need to configure the `datadog-ci` gem:

{{< tabs >}}
{{% tab "CI Provider with Auto-Instrumentation Support" %}}
{{% ci-autoinstrumentation %}}

<div class="alert alert-warning">
  <strong>Note</strong>: Auto-instrumentation is not supported for JRuby. Follow the <a href="/tests/setup/ruby/?tab=ciproviderwithautoinstrumentationsupport#manually-instrumenting-your-tests">manual instrumentation steps</a> instead.
</div>

{{% /tab %}}

{{% tab "Cloud CI provider (Agentless)" %}}

{{% ci-agentless %}}

{{% /tab %}}
{{% tab "On-Premises CI Provider (Datadog Agent)" %}}

{{% ci-agent %}}

{{% /tab %}}
{{< /tabs >}}

## Installing the Ruby test optimization library

To install the [Ruby test optimization gem][10] run:

```bash
bundle add datadog-ci --group "test"
```
Alternatively, add it to your Gemfile manually:

1. Add the `datadog-ci` gem to your `Gemfile`:

{{< code-block lang="ruby" filename="Gemfile" >}}
gem "datadog-ci", "~> 1.0", group: :test
{{< /code-block >}}

2. Install the gem by running `bundle install`

## Instrumenting your tests

Follow these steps if your CI Provider is not supported for auto-instrumentation (see [Configuring reporting method](#configuring-reporting-method)).

1. Set the following environment variables to configure the tracer:

`DD_CIVISIBILITY_ENABLED=true` (Required)
: Enables the Test Optimization product.

`DD_TEST_SESSION_NAME`
: Use this to identify a group of tests (for example: `unit-tests` or `integration-tests`).

`DD_ENV` (Required)
: Environment where the tests are being run (for example: `local` when running tests on a developer workstation or `ci` when running them on a CI provider).

`DD_SERVICE` (Optional)
: Name of the service or library being tested.

2. Prepend your test command with this datadog-ci CLI wrapper:

```bash
bundle exec ddcirb exec bundle exec rake test
```

Alternatively, set `RUBYOPT` environment variable to `"-rbundler/setup -rdatadog/ci/auto_instrument"` and don't modify your test command.

### Adding custom tags to tests

You can add custom tags to your tests by using the current active test:

```ruby
require "datadog/ci"

# inside your test
Datadog::CI.active_test&.set_tag("test_owner", "my_team")
# test continues normally
# ...
```

To create filters or `group by` fields for these tags, you must first create facets. For more information about adding tags, see the [Adding Tags][2] section of the Ruby custom instrumentation documentation.

### Adding custom measures to tests

Like tags, you can add custom measures to your tests by using the current active test:

```ruby
require "datadog/ci"

# inside your test
Datadog::CI.active_test&.set_metric("memory_allocations", 16)
# test continues normally
# ...
```

For more information on custom measures, see the [Add Custom Measures Guide][3].

## Configuration settings

The following is a list of the most important configuration settings that can be used with the test optimization library, either in code by using a `Datadog.configure` block, or using environment variables:

`service`
: Name of the service or library under test.<br/>
**Environment variable**: `DD_SERVICE`<br/>
**Default**: `$PROGRAM_NAME`<br/>
**Example**: `my-ruby-app`

`env`
: Name of the environment where tests are being run.<br/>
**Environment variable**: `DD_ENV`<br/>
**Default**: `none`<br/>
**Examples**: `local`, `ci`

For more information about `service` and `env` reserved tags, see [Unified Service Tagging][4].

The following environment variable can be used to configure the location of the Datadog Agent:

`DD_TRACE_AGENT_URL`
: Datadog Agent URL for trace collection in the form `http://hostname:port`.<br/>
**Default**: `http://localhost:8126`

All other [Datadog Tracer configuration][5] options can also be used.

## Using additional instrumentation

It can be useful to have rich tracing information about your tests that includes time spent performing database operations or other external calls, as seen in the following flame graph:

{{< img src="continuous_integration/tests/setup/ci-ruby-test-trace-with-redis.png" alt="Test trace with Redis instrumented" >}}

To achieve this, configure additional instrumentation in your `configure` block:

```ruby
if ENV["DD_ENV"] == "ci"
  Datadog.configure do |c|
    #  ... ci configs and instrumentation here ...
    c.tracing.instrument :redis
    c.tracing.instrument :pg
    # ... any other instrumentations supported by datadog gem ...
  end
end
```

Alternatively, you can enable automatic APM instrumentation in `test_helper/spec_helper`:

```ruby
require "datadog/auto_instrument" if ENV["DD_ENV"] == "ci"
```

**Note**: In CI mode, these traces are submitted to Test Optimization, and they do **not** show up in Datadog APM.

For the full list of available instrumentation methods, see the [tracing documentation][6]

## Collecting Git metadata

{{% ci-git-metadata %}}

## Manually instrumenting your tests

<div class="alert alert-info">
<strong>Attention</strong>: when using manual instrumentation, run your tests like you normally do:
don't change `RUBYOPT` env variable and don't prepend `bundle exec ddcirb exec` to your test command
</div>

Auto-instrumentation adds additional performance overhead at the code loading stage. It can be especially noticeable for
large repositories with a lot of dependencies. If your project takes 20+ seconds to start, you are likely
to benefit from manually instrumenting your tests.

{{< tabs >}}
{{% tab "RSpec" %}}

The RSpec integration traces all executions of example groups and examples when using the `rspec` test framework.

To activate your integration, add this to the `spec_helper.rb` file:

```ruby
require "rspec"
require "datadog/ci"

# Only activates test instrumentation on CI
if ENV["DD_ENV"] == "ci"
  Datadog.configure do |c|
    # enables test optimization
    c.ci.enabled = true

    # The name of the service or library under test
    c.service = "my-ruby-app"

    # Enables the RSpec instrumentation
    c.ci.instrument :rspec
  end
end
```

Run your tests as you normally do, specifying the environment where tests are being run in the `DD_ENV` environment variable.

You could use the following environments:

* `local` when running tests on a developer workstation
* `ci` when running them on a CI provider

For example:

```bash
DD_ENV=ci bundle exec rake spec
```

{{% /tab %}}

{{% tab "Minitest" %}}

The Minitest integration traces all executions of tests when using the `minitest` framework.

To activate your integration, add this to the `test_helper.rb` file:

```ruby
require "minitest"
require "datadog/ci"

# Only activates test instrumentation on CI
if ENV["DD_ENV"] == "ci"
  Datadog.configure do |c|
    # enables test optimization
    c.ci.enabled = true

    # The name of the service or library under test
    c.service = "my-ruby-app"

    c.ci.instrument :minitest
  end
end
```

Run your tests as you normally do, specifying the environment where tests are being run in the `DD_ENV` environment variable.

You could use the following environments:

* `local` when running tests on a developer workstation
* `ci` when running them on a CI provider

For example:

```bash
DD_ENV=ci bundle exec rake test
```

<div class="alert alert-warning">
<strong>Note:</strong> When using `minitest/autorun`, ensure that `datadog/ci` is required before `minitest/autorun`.
</div>

Example configuration with `minitest/autorun`:

```ruby
require "datadog/ci"
require "minitest/autorun"

if ENV["DD_ENV"] == "ci"
  Datadog.configure do |c|
    c.ci.enabled = true

    c.service = "my-ruby-app"

    c.ci.instrument :minitest
  end
end
```

{{% /tab %}}

{{% tab "Cucumber" %}}

The Cucumber integration traces executions of scenarios and steps when using the `cucumber` framework.

To activate your integration, add the following code to your application:

```ruby
require "cucumber"
require "datadog/ci"

# Only activates test instrumentation on CI
if ENV["DD_ENV"] == "ci"
  Datadog.configure do |c|
    # enables test optimization
    c.ci.enabled = true

    # The name of the service or library under test
    c.service = "my-ruby-app"

    # Enables the Cucumber instrumentation
    c.ci.instrument :cucumber
  end
end
```

Run your tests as you normally do, specifying the environment where tests are being run in the `DD_ENV` environment variable.
You could use the following environments:

* `local` when running tests on a developer workstation
* `ci` when running them on a CI provider

For example:

```bash
DD_ENV=ci bundle exec rake cucumber
```

{{% /tab %}}
{{< /tabs >}}

## Using library's public API for unsupported test frameworks

If you use RSpec, Minitest, or Cucumber, **do not use the manual testing API**, as Test Optimization automatically instruments them and sends the test results to Datadog. The manual testing API is **incompatible** with already supported testing frameworks.

Use the manual testing API only if you use an unsupported testing framework or have a different testing mechanism.
Full public API documentation is available on [YARD site][8].

### Domain model

The API is based around four concepts: test session, test module, test suite, and test.

#### Test session

A test session represents a test command run.

To start a test session, call `Datadog::CI.start_test_session` and pass the Datadog service and tags (such as the test framework
you are using).

When all your tests have finished, call `Datadog::CI::TestSession#finish`, which closes the session and sends the session
trace to the backend.

#### Test module

A test module represents a smaller unit of work within a session.
For supported test frameworks, test module is always same as test session.
For your use case, this could be a package in your componentized application.

To start a test module, call `Datadog::CI.start_test_module` and pass the name of the module.

When the module run has finished, call `Datadog::CI::TestModule#finish`.

#### Test suite

A test suite comprises a set of tests that test similar functionality.
A single suite usually corresponds to a single file where tests are defined.

Create test suites by calling `Datadog::CI#start_test_suite` and passing the name of the test suite.

Call `Datadog::CI::TestSuite#finish` when all the related tests in the suite have finished their execution.

#### Test

A test represents a single test case that is executed as part of a test suite.
Usually it corresponds to a method that contains testing logic.

Create tests in a suite by calling `Datadog::CI#start_test` or `Datadog::CI.trace_test` and passing the name of the test and name of the test suite. Test suite name must be the same as name of the test suite started in previous step.

Call `Datadog::CI::Test#finish` when a test has finished execution.

### Code example

The following code represents example usage of the API:

```ruby
require "datadog/ci"

Datadog.configure do |c|
  c.service = "my-test-service"
  c.ci.enabled = true
end

def run_test_suite(tests, test_suite_name)
  test_suite = Datadog::CI.start_test_suite(test_suite_name)

  run_tests(tests, test_suite_name)

  test_suite.passed!
  test_suite.finish
end

def run_tests(tests, test_suite_name)
  tests.each do |test_name|
    Datadog::CI.trace_test(test_name, test_suite_name) do |test|
      test.passed!
    end
  end
end

Datadog::CI.start_test_session(
  tags: {
    Datadog::CI::Ext::Test::TAG_FRAMEWORK => "my-framework",
    Datadog::CI::Ext::Test::TAG_FRAMEWORK_VERSION => "0.0.1",
  }
)
Datadog::CI.start_test_module("my-test-module")

run_test_suite(["test1", "test2", "test3"], "test-suite-name")

Datadog::CI.active_test_module&.passed!
Datadog::CI.active_test_module&.finish

Datadog::CI.active_test_session&.passed!
Datadog::CI.active_test_session&.finish
```

## Best practices

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
- Command used to run the tests (such as `yarn test`)

The test session name needs to be unique within a repository to help you distinguish different groups of tests.

#### When to use `DD_TEST_SESSION_NAME`

There's a set of parameters that Datadog checks to establish correspondence between test sessions. The test command used to execute the tests is one of them. If the test command contains a string that changes for every execution, such as a temporary folder, Datadog considers the sessions to be unrelated to each other. For example:

- `yarn test --temp-dir=/var/folders/t1/rs2htfh55mz9px2j4prmpg_c0000gq/T`
- `pnpm vitest --temp-dir=/var/folders/t1/rs2htfh55mz9px2j4prmpg_c0000gq/T`

Datadog recommends using `DD_TEST_SESSION_NAME` if your test commands vary between executions.

## Further reading

{{< partial name="whats-next/whats-next.html" >}}

[2]: /tracing/trace_collection/custom_instrumentation/ruby?tab=locally#adding-tags
[3]: /tests/guides/add_custom_measures/?tab=ruby
[4]: /getting_started/tagging/unified_service_tagging
[5]: /tracing/trace_collection/library_config/ruby/?tab=containers#configuration
[6]: /tracing/trace_collection/dd_libraries/ruby/#integration-instrumentation
[7]: https://github.com/bblimke/webmock
[8]: https://datadoghq.dev/datadog-ci-rb/Datadog/CI.html
[9]: https://github.com/vcr/vcr
[10]: https://github.com/DataDog/datadog-ci-rb
