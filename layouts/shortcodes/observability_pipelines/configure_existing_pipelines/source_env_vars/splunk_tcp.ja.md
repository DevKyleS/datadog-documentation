- Splunk TCP address:
    - The Observability Pipelines Worker listens to this socket address to receive logs from the Splunk Forwarder. For example, `0.0.0.0:9997`.
    - 環境変数 `DD_OP_SOURCE_SPLUNK_TCP_ADDRESS` に格納されます。