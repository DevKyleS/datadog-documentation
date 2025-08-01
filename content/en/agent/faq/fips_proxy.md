---
title: Agent FIPS proxy
disable_toc: false
private: true
further_reading:
- link: "/agent/configuration/fips-compliance"
  tag: "Documentation"
  text: "FIPS Compliance"
---

<div class="alert alert-danger">The Datadog FIPS Proxy is no longer the recommended solution for FIPS-compliant encryption of the Datadog Agent. Use the <a href="/agent/configuration/fips-compliance">Datadog FIPS Agent</a> instead.</div>

The Datadog Agent FIPS Proxy ensures that communication between the Datadog Agent and Datadog uses FIPS-compliant encryption.

The Datadog Agent FIPS Proxy is a separately distributed component that you deploy on the same host as the Datadog Agent. The proxy acts as an intermediary between the Agent and Datadog intake. The Agent communicates with the Datadog Agent FIPS Proxy, which encrypts payloads using a FIPS 140-2 validated cryptography and relays the payloads to Datadog. The Datadog Agent and the Agent FIPS Proxy must be configured in tandem to communicate with one another.

<div class="alert alert-warning">FIPS compliance is not retained if the Datadog Agent FIPS Proxy and the Datadog Agent are not on the same host.
<br>Similarly, FIPS compliance is not retained if the <code>fips.enabled</code> option is not set to <code>true</code> in <code>datadog.yaml</code>.</div>

## Supported platforms and limitations

The Datadog Agent FIPS Proxy's compliance is based on its use of the FIPS 140-2 validated [Cryptographic Module - Certificate #4282][1]. See the related [security policy][2] for information about validated operating environments and restrictions.

**It is your responsibility to ensure operating environment compliance with the security policy and wider FIPS guidance.**

Supported platforms:

|||
| ---  | ----------- |
| Bare metal and VMs | RHEL >= 7<br>Debian >= 8<br>Ubuntu >= 14.04<br>SUSE >= 12|
| Cloud and container| Amazon ECS<br>AWS EKS (Helm)|

Supported products (Agent 7.45+):

- Metrics
- Logs
- APM traces
- APM profiles
- Processes
- Orchestrator Explorer
- Runtime Security

The Datadog Agent FIPS Proxy does **not** support the following:

- Serverless Monitoring
- Communication between Cluster Agent and Node Agents
- Agent integrations
- Outbound communication to anything other than the US1-FED region site

## Prerequisites

- TCP port range available: 9803 to 9818
- Datadog Agent >= v7.45

## Install the Agent with FIPS support

{{< tabs >}}
{{% tab "Host or VM" %}}

### Install the Agent on a new host

To install the Datadog Agent with the Datadog Agent FIPS Proxy, add `DD_FIPS_MODE=1` to the one-step install instructions on the [Datadog Agent Integration][1] page. For example:

```shell
DD_API_KEY=<DD_API_KEY> \
DD_SITE="ddog-gov.com" \
DD_FIPS_MODE=1 \
bash -c "$(curl -L \
   https://install.datadoghq.com/scripts/install_script_agent7.sh)"
```

Setting the `DD_FIPS_MODE` environment variable installs the FIPS package along with the Agent, and configures the Agent to use the proxy. There are no additional configuration steps if you're using this method, but you should [verify the installation](#verify-your-installation).

### Add the Datadog Agent FIPS proxy to an existing Agent

Follow the steps below to add the Datadog Agent FIPS proxy to an existing Agent installation.

#### Install the Datadog Agent FIPS Proxy package

1. Run the following commands to install the Datadog Agent FIPS Proxy:

   Debian:
   ```shell
   apt-get update && apt-get install datadog-fips-proxy
   ```
   RHEL and Fedora:
   ```shell
   yum makecache && yum install datadog-fips-proxy
   ```
   SLES:
   ```shell
   zypper refresh datadog && zypper install datadog-fips-proxy
   ```

1. The first time you perform an upgrade, copy the example configuration file to the appropriate location and restart the proxy. You do not need to copy the configuration in subsequent upgrades unless there are significant changes in the upstream proxy configuration:
   ```shell
   sudo cp /etc/datadog-fips-proxy/datadog-fips-proxy.cfg.example \
      /etc/datadog-fips-proxy/datadog-fips-proxy.cfg
   sudo chown dd-agent:dd-agent \
      /etc/datadog-fips-proxy/datadog-fips-proxy.cfg
   sudo chmod 640 /etc/datadog-fips-proxy/datadog-fips-proxy.cfg
   sudo systemctl restart datadog-fips-proxy
   ```

#### Configure the Agent to use the Datadog Agent FIPS proxy

The Datadog Agent FIPS Proxy package comes pre-configured for use with the US1-FED datacenter. If you're upgrading an existing Datadog Agent, you **must** configure the Agent to use the proxy.

To configure the Agent to use the proxy, set `fips.enabled` to `true` and `fips.https` to `false` in the [Agent configuration file][2]:

```yaml
fips:
  enabled: true
  https: false
```

The `fips` setting is available in Agent versions >= 7.41. When the setting is enabled, the Datadog Agent redirects all of its communications to the Datadog Agent FIPS Proxy for supported products. This setting ignores custom URL options, such as `dd_url`.

The `https` option is set to `false` because the Agent uses HTTP to communicate with the proxy. The Datadog Agent FIPS Proxy runs on the same host as the Agent and relies on the host's security for protection of that communication.

**Host security and hardening are your responsibilities.**

<div class="alert alert-warning">The <code>fips.enabled</code> setting defaults to <code>false</code> in the Agent. It must be set to <code>true</code> to ensure all communications are forwarded through the Datadog Agent FIPS Proxy.<br><br><strong>If <code>fips.enabled</code> is not set to <code>true</code>, the Agent is not FIPS Compliant</strong>.</div>

### Verify your installation

Verify that metrics, traces, and logs are correctly reported in the app.

For metrics, run the connectivity diagnostic command and verify that all checks pass:

```shell
sudo -u dd-agent datadog-agent diagnose --include connectivity-datadog-core-endpoints
# For Agent version < 7.48, run the following command:
# sudo -u dd-agent datadog-agent diagnose datadog-connectivity
```

If you don't see metrics, traces, or logs reported in the app, see the [Troubleshooting](#troubleshooting-a-bare-metal-or-vm-installation) section.

### View logs

```shell
sudo journalctl -u datadog-fips-proxy
```

#### journald logs configuration

If you use [Log Management][3] and want to send the Datadog Agent FIPS Proxy logs to Datadog, set up the Datadog Agent to read logs from journald.

1. In the Agent's [configuration file][2], set `logs_enabled` to `true` to activate the Logs Agent. In the [configuration directory][4], create a file at `fips_proxy.d/conf.yaml` with the following content:

   ```yaml
   logs:
     - type: journald
       source: datadog-fips-proxy
       include_units:
         - datadog-fips-proxy.service
   ```

1. Make sure that the `dd-agent` user is in the `systemd-journal` group. For more information, see the [journald integration][5] documentation.
1. [Restart the Agent][6].

[1]: https://app.datadoghq.com/account/settings/agent/latest
[2]: /agent/configuration/agent-configuration-files/#agent-main-configuration-file
[3]: /logs/
[4]: /agent/configuration/agent-configuration-files/#agent-configuration-directory
[5]: /integrations/journald/#configuration
[6]: /agent/configuration/agent-commands/#start-stop-and-restart-the-agent
{{% /tab %}}

{{% tab "Helm on Amazon EKS" %}}
Set the following values in your `values.yaml` file:

```yaml
fips:
  enabled: true
  use_https: false
```

The `fips` setting is available in Agent versions >= 7.41. When the setting is enabled, the Datadog Agent redirects all of its communications to the Datadog Agent FIPS Proxy for supported products. This setting ignores custom URL options, such as `dd_url`.

The `use_https` option is set to `false` because the Agent uses HTTP to communicate with the proxy. The Datadog Agent FIPS Proxy runs on the same host as the Datadog Agent and relies on the host's security for protection of that communication.

**Host security and hardening are your responsibilities.**

<div class="alert alert-warning">The <code>fips.enabled</code> setting defaults to <code>false</code> in the Agent. It must be set to <code>true</code> to ensure all communications are forwarded through the Datadog Agent FIPS Proxy.<br><br><strong>If <code>fips.enabled</code> is not set to <code>true</code>, the Agent is not FIPS Compliant</strong>.</div>


{{% /tab %}}

{{% tab "Amazon ECS" %}}
To install the FIPS proxy on Amazon ECS, follow the [Datadog Agent ECS Integrations guide](/containers/amazon_ecs/#fips-proxy-for-govcloud-environments). 

Ensure you add the `fips-proxy` sidecar container to your ECS task definition and open the required container ports (9803–9818) to enable communication for all [supported features](#supported-platforms-and-limitations).

**Note**: You must also ensure that the sidecar container is configured with applicable network settings and IAM permissions.

```json
 {
   "containerDefinitions": [
     (...)
          {
            "name": "fips-proxy",
            "image": "datadog/fips-proxy:1.1.14",
            "portMappings": [
                {
                    "containerPort": 9803,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9804,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9805,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9806,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9807,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9808,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9809,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9810,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9811,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9812,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9813,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9814,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9815,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9816,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9817,
                    "protocol": "tcp"
                },
                {
                    "containerPort": 9818,
                    "protocol": "tcp"
                }
            ],
            "essential": true,
            "environment": [
                {
                    "name": "DD_FIPS_PORT_RANGE_START",
                    "value": "9803"
                },
                {
                    "name": "DD_FIPS_LOCAL_ADDRESS",
                    "value": "127.0.0.1"
                }
            ]
        }
   ],
   "family": "datadog-agent-task"
}
```

You must also update the environment variables of the Datadog Agent's container to enable sending traffic through the FIPS proxy:

```json
{
    "containerDefinitions": [
        {
            "name": "datadog-agent",
            "image": "public.ecr.aws/datadog/agent:latest",
            (...)
            "environment": [
              (...)
                {
                    "name": "DD_FIPS_ENABLED",
                    "value": "true"
                },
                {
                    "name": "DD_FIPS_PORT_RANGE_START",
                    "value": "9803"
                },
                {
                    "name": "DD_FIPS_HTTPS",
                    "value": "false"
                },
             ],
        },
    ],
   "family": "datadog-agent-task"
}
```
[1]: https://docs.datadoghq.com/agent/configuration/fips-compliance/?tab=helmonamazoneks#supported-platforms-and-limitations
{{% /tab %}}

{{< /tabs >}}

## Security and hardening

You, the Datadog customer, are responsible for **host** security and hardening.

**Security considerations:**
- While the Datadog images provided are constructed with security in mind, they have not been evaluated against CIS benchmark recommendations or DISA STIG standards.
- If you rebuild, reconfigure, or modify the Datadog Agent FIPS Proxy to fit your deployment or testing needs, you might end up with a technically working setup, but Datadog cannot guarantee FIPS compliance if the Datadog Agent FIPS Proxy is not used exactly as explained in the documentation.
- If you did not follow the installation steps listed above exactly as documented, Datadog cannot guarantee FIPS compliance. Correct configuration includes having your Datadog Agent configured to communicate to the Datadog Agent FIPS Proxy by setting the `fips.enabled` option, and having a running Datadog Agent FIPS Proxy.

### Communication between the Agent and the FIPS Proxy

The Datadog Agent FIPS Proxy only secures communication originating from the Agent targeting the Datadog intake API endpoints. This means that other forms of communication terminating at the Agent or originating from the Agent are not made FIPS-compliant by this solution.

### Communication between the Cluster Agent and Node Agents

The Datadog Agent FIPS Proxy only secures communication originating from the Cluster Agent targeting the Datadog intake API endpoints. This means that other forms of communication terminating at the Cluster Agent or originating from the Cluster Agent are not made FIPS-compliant by this solution.

### Release versions

Datadog Agent FIPS Proxy releases are decoupled from Datadog Agent releases. Use the latest versions of both the Datadog Agent and Datadog Agent FIPS Proxy versions to ensure the Datadog Agent and FIPS proxy support all available products.

## Troubleshooting a host or VM installation

To troubleshoot the Datadog Agent FIPS Proxy, verify the following:
- The Datadog Agent and Datadog Agent FIPS Proxy are running.
- The Datadog Agent can communicate with the Datadog Agent FIPS Proxy.
- The Datadog Agent FIPS Proxy can communicate with Datadog intake endpoints.

### Check the proxy status

To get information about the state of the Datadog Agent FIPS Proxy, run the following command:

```shell
sudo systemctl status datadog-fips-proxy
```

If the proxy is running, the output should look similar to the following:
```text
- datadog-fips-proxy.service - Datadog FIPS Proxy
  Loaded: loaded
    (/lib/systemd/system/datadog-fips-proxy.service;
      enabled; vendor preset: enabled)
  Active: active (running) since Tue 2022-07-19 16:21:15 UTC; 1min 6s ago
```

If the proxy status is `inactive (dead)`, launch the Datadog Agent FIPS Proxy:

```shell
sudo systemctl start datadog-fips-proxy
```

If the proxy status is `failed`, the Datadog Agent FIPS Proxy could not be launched due to an error. Run the following command and search the proxy logs for errors:

```shell
sudo journalctl -u datadog-fips-proxy --no-pager
```

### Proxy cannot bind socket

If the proxy logs show a `bind socket` error, the proxy is trying to use a port that is already in use on the host. The Datadog Agent FIPS Proxy uses the TCP port range from 9803 up to and including 9818. Ports in this range must be available on the host and not used by other services.

In the following example, the Datadog Agent FIPS Proxy is unable to bind a socket on port `9804` because the port is already in use:

```text
[ALERT] (4518) : Starting frontend metrics-forwarder: cannot bind socket (Address already in use) [0.0.0.0:9804]
[ALERT] (4518) : [/opt/datadog-fips-proxy/embedded/sbin/haproxy.main()] Some protocols failed to start their listeners! Exiting.
```

### Agent is unable to connect to the proxy

To check for network issues, check the logs at `/var/log/datadog/agent.log`, or run:

```shell
datadog-agent diagnose --include connectivity-datadog-core-endpoints
# For Agent version < 7.48, run the following command:
# datadog-agent diagnose datadog-connectivity
```

Look for errors such as:
```text
connect: connection refused, context deadline exceeded (Client.Timeout exceeded while awaiting headers), or connection reset by peer
```

- Follow the steps in [Check the proxy status](#check-the-proxy-status) to verify that the Datadog Agent FIPS Proxy is running.
- Verify that the port range from the proxy matches the one from the Agent.

If the proxy is running and the port range is correct, a local firewall on the machine may be blocking the Agent's access to the proxy. Set your firewall to allow connections to TCP ports from 9804 to 9818.

You can use `curl` to verify that the proxy is accessible:

```shell
curl http://localhost:9804/
```

For further assistance, see [Agent Troubleshooting][3].

### Datadog Agent FIPS Proxy is unable to connect to Datadog intake

If there are HTTP errors such as `502`, `503`, or if the proxy returns an empty response, the Datadog Agent FIPS Proxy might not be able to forward traffic to the Datadog backend.

Verify the Datadog Agent FIPS Proxy logs with:

```shell
sudo journalctl -u datadog-fips-proxy --no-pager
```

Check the logs for errors such as:

```text
haproxy[292759]: [WARNING] (292759) : Server
datadog-api/mothership3 is DOWN, reason: Layer4 timeout, vcheck duration: 2000ms. 0 active and 0 backup servers left. 0 sessions active, 0 requeued, 0 remaining in queue.
[ALERT] (292759) : backend 'datadog-api' has no server available!
```

or

```text
haproxy[1808]: [WARNING] (1808) : Server
datadog-metrics/mothership2 is DOWN, reason: Layer4
connection problem, info: "Connection refused", check duration: 0ms. 0 active and 0 backup servers left. 0
sessions active, 0 requeued, 0 remaining in queue.
haproxy[1808]: [ALERT] (1808) : backend 'datadog-metrics' has no server available!
```

These errors indicate that the Datadog Agent FIPS Proxy is not able to contact backend systems, possibly due to being blocked by a firewall or due to another network issue. Datadog Agent FIPS Proxy requires internet access to the Datadog intake endpoints. You can find the IP addresses for these endpoints [through the API][4].

For more information about outbound connections from the Agent, see the [Network Traffic][5] guide.

### Name resolver configuration for the FIPS Proxy
Replace `<Your DNS Resolver>:<Port>` with the appropriate DNS Server IP addresses and port for your network. These IP addresses must correspond to your designated name resolver servers to maintain proper functionality and ensure accurate domain name resolution. In the `datadog-fips-proxy.cfg` configuration file, locate the section listed below and configure your DNS accordingly:
```
# This section is to reload DNS Records

# Replace these addresses with your DNS Server IP addresses.

resolvers my-dns

    parse-resolv-conf

    nameserver dns1 <Your DNS IP>:<Port>

    nameserver dns2 <Your DNS IP>:<Port>
```

If you're still unsure about your issue, contact [Datadog support][6].

## Further reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://csrc.nist.gov/projects/cryptographic-module-validation-program/certificate/4282
[2]: https://csrc.nist.gov/CSRC/media/projects/cryptographic-module-validation-program/documents/security-policies/140sp4282.pdf
[3]: /agent/troubleshooting/
[4]: https://ip-ranges.ddog-gov.com/
[5]: /agent/configuration/network/#destinations
[6]: /help/
