---
title: Reuse Browser Test Journeys Across Your Test Suite

further_reading:
    - link: 'synthetics/browser_tests'
      tag: 'Documentation'
      text: 'Configure a Browser Test'
    - link: '/synthetics/browser_tests/actions'
      tag: 'Documentation'
      text: 'Create Browser Test Steps'
    - link: 'https://www.datadoghq.com/blog/test-creation-best-practices/'
      tag: 'Blog'
      text: 'Best practices for creating end-to-end tests'

---

## Overview

Sometimes, you want to reuse a journey in several different tests. For example:

* If most of your application's functionalities are located behind a login, you can [reuse your login steps](#create-and-reuse-a-login-subtest) at the beginning of each of your tests.
* If you want to monitor your application's functionalities on several different environments, you can create tests for your prod environment and reuse them as subtests for other environments such as dev or staging.
* If running your tests creates database objects, you can create tests that clean up your testing environment and use them as subtests to systematically perform cleanup at the start or end of your tests.

Browser test subtests allow you to reuse your journeys among your test suite, which:
* **Saves you time at test creation.** If you have a login test, call it as a subtest at the beginning of all your test suite, instead of recording the same login steps for each of your tests.
* **Makes your tests easier to understand**, because you make blocks that are meaningful for others reading your tests.
* **Allows for better maintenance**, because if your flows change, you only need to update it once, and not once per test.


## Create and reuse a login subtest

If monitoring your application involves first logging into it, a best practice is to create a single test that contains all your login steps, and have other tests reuse that login test as a subtest. 

To create a login test and use it as a subtest in the rest of your test suite:

1. Create a test, that does nothing but log into your application. Set the **Starting URL** of your test to your pre-login URL.

  {{< img src="synthetics/guide/reusing-browser-test-journeys/login_subtest_recording_2.mp4" alt="Recording the Login subtest" video="true" width="100%">}}

2. Create a second test that monitors a post-login functionality of your application. In the following example, this second test monitors creating a dashboard. Set the **Starting URL** of your test to your pre-login URL also.

  {{< img src="synthetics/guide/reusing-browser-test-journeys/dashboard_test_configuration_2.png" alt="Configuring the parent test" >}}

3. When recording the second test, click **Subtest** and select the login test you created in step 1. 

  {{< img src="synthetics/guide/reusing-browser-test-journeys/dashboard_subtest_2.mp4" alt="Including subtest in parent test" video="true" width="80%">}}

  When you set up this subtest step, all the steps of the login test are played at the beginning of the parent test. Also, the variables in the subtest are imported into the parent test of your second test. By default, the subtest is played in the main tab. This means your subtest steps are played in the same tab as previous and following steps. The subtest starts running using the URL that was set in the parent test (in this example, the pre-login URL), and after all the subtest steps have been executed, the browser test executes the parent's first non-subtest step from the page the subtest was last on. No parent step was created for now.

**Note:** You can choose the tab in which the subtest should execute using [**Subtest Advanced Options**][1].

4. Before you start recording the parent test steps, login to your account with the dedicated credentials in the recorder window. This ensures that the parent test starts from the same state your browser test is in after it goes through the subtest steps.

  {{< img src="synthetics/guide/reusing-browser-test-journeys/dashboard_test_iframe.mp4" alt="Replaying subtest in parent test" video="true" width="100%">}}

5. After you login, click **Start recording** to start recording the parent test's post-login steps you're interested in. Once you're done, click **Save**.

  {{< img src="synthetics/guide/reusing-browser-test-journeys/dashboard_test_recording.mp4" alt="Recording parent test" video="true" width="100%">}}

 In the example above, the login subtest ensures that after logging into a Datadog test account, users can create a timeboard. That timeboard is then associated with the user.

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /synthetics/browser_tests/advanced_options#subtests
