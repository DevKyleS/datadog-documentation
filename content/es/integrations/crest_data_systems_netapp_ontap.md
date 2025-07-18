---
algolia:
  subcategory: Integraciones de Marketplace
app_id: crest-data-systems-netapp-ontap
app_uuid: c744f76a-7d04-4daf-bf7b-0902fbedd76d
assets:
  dashboards:
    NetApp OnTap - Cluster Performance: assets/dashboards/crest_data_systems_netapp_ontap_cluster_performance.json
    NetApp OnTap - Disk: assets/dashboards/crest_data_systems_netapp_ontap_disk.json
    NetApp OnTap - Events: assets/dashboards/crest_data_systems_netapp_ontap_events.json
    NetApp OnTap - LUN: assets/dashboards/crest_data_systems_netapp_ontap_lun.json
    NetApp OnTap - Network: assets/dashboards/crest_data_systems_netapp_ontap_network.json
    NetApp OnTap - Overview: assets/dashboards/crest_data_systems_netapp_ontap_overview.json
    NetApp OnTap - Snapshot: assets/dashboards/crest_data_systems_netapp_ontap_snapshot.json
    NetApp OnTap - Volume: assets/dashboards/crest_data_systems_netapp_ontap_volume.json
  integration:
    auto_install: false
    configuration:
      spec: assets/configuration/spec.yaml
    events:
      creates_events: false
    metrics:
      check: cds.netapp.ontap.cluster_inventory.license_details
      metadata_path: metadata.csv
      prefix: cds.netapp.ontap
    service_checks:
      metadata_path: assets/service_checks.json
    source_type_id: 10325
    source_type_name: crest_data_systems_netapp_ontap
  monitors:
    Aggregate Plex is offline.: assets/monitors/cds_plex_status.json
    Aggregate RAID group is offline: assets/monitors/cds_raid_status.json
    Aggregate over-utilized above 90%.: assets/monitors/cds_aggregate_utilization.json
    Cluster is exhibiting high IOPS: assets/monitors/cds_cluster_iops.json
    Cluster is over-utilized above 90%.: assets/monitors/cds_cluster_used_capacity.json
    Ethernet Port is down: assets/monitors/cds_ethernet_port_status.json
    Fan failed for node: assets/monitors/cds_failed_power_supply.json
    High disk latency: assets/monitors/cds_disk_latency.json
    High latency of cluster: assets/monitors/cds_cluster_latency.json
    LUN is exhibiting high IOPS: assets/monitors/cds_lun_iops.json
    LUN is exhibiting high latency: assets/monitors/cds_lun_latency.json
    Low throughput of cluster: assets/monitors/cds_cluster_throughput.json
    Network Interface is down: assets/monitors/cds_network_interface_status.json
    Node is unhealthy: assets/monitors/cds_node_health.json
    Node temperature is in alert status: assets/monitors/cds_node_temperature.json
    Power supply to node failed: assets/monitors/cds_failed_fan_count.json
    Size used by snapshot exceeds reserved size: assets/monitors/cds_snapshot_size_exceed.json
    The battery status of a node is down: assets/monitors/cds_node_battery.json
    The percentage of CPU disk activity is high: assets/monitors/cds_disk_busy_percent.json
    Volume has low throughput: assets/monitors/cds_volume_throughput.json
    Volume is exhibiting high IOPS: assets/monitors/cds_volume_iops.json
    'Volume is exhibiting high latency ': assets/monitors/cds_volume_latency.json
    Volume is exhibiting high utilization: assets/monitors/cds_volume_utilization.json
    Volume is offline.: assets/monitors/cds_volume_offline.json
author:
  homepage: https://www.crestdata.ai
  name: Crest Data
  sales_email: datadog-sales@crestdata.ai
  support_email: datadog.integrations@crestdata.ai
  vendor_id: crest-data-systems
categories:
- almacenes de datos
- marketplace
custom_kind: integración
dependencies: []
display_on_public_website: true
draft: false
git_integration_title: crest_data_systems_netapp_ontap
integration_id: crest-data-systems-netapp-ontap
integration_title: NetApp OnTap
integration_version: ''
is_public: true
legal_terms:
  eula: assets/EULA.pdf
manifest_version: 2.0.0
name: crest_data_systems_netapp_ontap
pricing:
- billing_type: tag_count
  includes_assets: true
  metric: datadog.marketplace.crest_data_systems.netapp_ontap
  product_id: netapp-ontap
  short_description: Por clúster al mes
  tag: cds_netapp_ontap_instance
  unit_label: Instancia de NetApp ONTAP
  unit_price: 495.0
public_title: NetApp OnTap
short_description: Monitorización del rendimiento y el uso del clúster de NetApp ONTAP
supported_os:
- linux
- windows
- macos
tile:
  changelog: CHANGELOG.md
  classifier_tags:
  - Category::Data Stores
  - Category::Marketplace
  - Offering::Integration
  - Submitted Data Type::Metrics
  - Supported OS::Linux
  - Supported OS::Windows
  - Supported OS::macOS
  configuration: README.md#Setup
  description: Monitorización del rendimiento y el uso del clúster de NetApp ONTAP
  media:
  - caption: 'NetApp OnTap: descripción general'
    image_url: images/crest_data_systems_netapp_ontap_overview.png
    media_type: imagen
  - caption: 'NetApp OnTap: disco'
    image_url: images/crest_data_systems_netapp_ontap_disk.png
    media_type: imagen
  - caption: 'NetApp OnTap: LUN'
    image_url: images/crest_data_systems_netapp_ontap_lun.png
    media_type: imagen
  - caption: 'NetApp OnTap: volumen'
    image_url: images/crest_data_systems_netapp_ontap_volume.png
    media_type: imagen
  overview: README.md#Overview
  support: README.md#Support
  title: NetApp OnTap
  uninstallation: README.md#Uninstallation
---

<!--  EXTRAÍDO DE https://github.com/DataDog/marketplace -->


## Información general

Esta integración monitoriza el rendimiento y el uso de los nodos y clústeres de NetApp OnTap. Captura métricas cruciales y proporciona información sobre el almacenamiento y el rendimiento del clúster de NetApp OnTap.

## Agent

Para solicitar asistencia o funciones, ponte en contacto con Crest Data a través de los siguientes canales:

- Correo electrónico de asistencia: [datadog.integrations@crestdata.ai][9]
- Correo electrónico de ventas: [datadog-sales@crestdata.ai][10]
- Página web: [crestdata.ai][3]
- FAQ: [Crest Data Datadog Marketplace Integrations FAQ][8]

[1]: https://docs.datadoghq.com/es/agent/guide/agent-commands/?tab=agentv6v7#start-stop-and-restart-the-agent
[2]: https://docs.datadoghq.com/es/agent/guide/agent-commands/#agent-status-and-information
[3]: https://www.crestdata.ai/
[4]: https://docs.datadoghq.com/es/agent/guide/agent-configuration-files/?tab=agentv6v7
[5]: https://docs.crestdata.ai/datadog-integrations-readme/NetApp_OnTap.pdf
[6]: https://docs.datadoghq.com/es/agent/?tab=Linux
[7]: https://docs.datadoghq.com/es/account_management/api-app-keys/
[8]: https://docs.crestdata.ai/datadog-integrations-readme/Crest_Data_Datadog_Integrations_FAQ.pdf
[9]: mailto:datadog.integrations@crestdata.ai
[10]: mailto:datadog-sales@crestdata.ai

---
Esta aplicación está disponible a través de Datadog Marketplace y cuenta con el apoyo de un socio tecnológico de Datadog. Para utilizarla, <a href="https://app.datadoghq.com/marketplace/app/crest-data-systems-netapp-ontap" target="_blank">adquiere esta aplicación en el Marketplace</a>.