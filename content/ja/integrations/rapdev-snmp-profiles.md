---
algolia:
  subcategory: Marketplace インテグレーション
app_id: rapdev-snmp-profiles
app_uuid: e82be05a-2fd2-44eb-9297-4fec152925a3
assets:
  dashboards:
    RapDev APC PDU Dashboard: assets/dashboards/rapdev_apc_pdu_dashboard.json
    RapDev APC UPS Dashboard: assets/dashboards/rapdev_apc_ups_dashboard.json
    RapDev Arista Switch Dashboard: assets/dashboards/rapdev_arista_switch_dashboard.json
    RapDev Aruba Switch Dashboard: assets/dashboards/rapdev_aruba_switch_dashboard.json
    RapDev AudioCodes Controller: assets/dashboards/rapdev_audiocodes_controller.json
    RapDev AudioCodes Controller Virtual Edition: assets/dashboards/rapdev_audiocodes_controller_virtual_edition.json
    RapDev Barracuda CloudGen Firewall Dashboard: assets/dashboards/rapdev_barracuda_cloudgen_firewall_dashboard.json
    RapDev Bluecoat SG Dashboard: assets/dashboards/rapdev_bluecoat_sg_dashboard.json
    RapDev Brocade VDX Dashboard: assets/dashboards/rapdev_brocade_vdx_dashboard.json
    RapDev Checkpoint SVN Dashboard: assets/dashboards/rapdev_checkpoint_svn_dashboard.json
    RapDev Cisco ASA Dashboard: assets/dashboards/rapdev_cisco_asa_dashboard.json
    RapDev Cisco ASR Dashboard: assets/dashboards/rapdev_cisco_asr_dashboard.json
    RapDev Cisco CUBE Dashboard: assets/dashboards/rapdev_cisco_cube_dashboard.json
    RapDev Cisco Catalyst Dashboard: assets/dashboards/rapdev_cisco_catalyst_dashboard.json
    RapDev Cisco Cube Dashboard: assets/dashboards/rapdev_cisco_cube_dashboard.json
    RapDev Cisco ISE Dashboard: assets/dashboards/rapdev_cisco_ise_dashboard.json
    RapDev Cisco ISR Dashboard: assets/dashboards/rapdev_cisco_isr_dashboard.json
    RapDev Cisco ISR Overview: assets/dashboards/rapdev_cisco_isr_dashboard.json
    RapDev Cisco Meraki Dashboard: assets/dashboards/rapdev_cisco_meraki_dashboard.json
    RapDev Cisco UCM Dashboard: assets/dashboards/rapdev_cisco_ucm_dashboard.json
    RapDev Cisco UCS Dashboard: assets/dashboards/rapdev_cisco_ucs_dashboard.json
    RapDev Cisco WLC Dashboard: assets/dashboards/rapdev_cisco_wlc_dashboard.json
    RapDev Citrix Netscaler Dashboard: assets/dashboards/rapdev_citrix_netscaler_dashboard.json
    RapDev Dell VRTX Dashboard: assets/dashboards/rapdev_dell_vrtx_dashboard.json
    RapDev F5 BigIP Dashboard: assets/dashboards/rapdev_f5_bigip_dashboard.json
    RapDev Fortinet Fortigate Dashboard: assets/dashboards/rapdev_fortinet_fortigate_dashboard.json
    RapDev HP iLO Dashboard: assets/dashboards/rapdev_hpe_ilo_dashboard.json
    RapDev Ironport Mail Dashboard: assets/dashboards/rapdev_ironport_mail_dashboard.json
    RapDev Juniper SSG Dashboard: assets/dashboards/rapdev_juniper_ssg_dashboard.json
    RapDev Kemp LoadMaster Dashboard: assets/dashboards/rapdev_kemp_loadmaster_dashboard.json
    RapDev Kemp Loadmaster Dashboard: assets/dashboards/rapdev_kemp_loadmaster_dashboard.json
    RapDev Netapp NAS Dashboard: assets/dashboards/rapdev_netapp_nas_dashboard.json
    RapDev Palo Alto NextGen Firewall Dashboard: assets/dashboards/rapdev_palo_alto_nextgen_firewall_dashboard.json
    RapDev Printer Dashboard: assets/dashboards/rapdev_printer_dashboard.json
    RapDev SNMP Device Inventory: assets/dashboards/rapdev_snmp_device_inventory.json
    RapDev ServerTech PDU Gen3 Dashboard: assets/dashboards/rapdev_servertech_pdu_gen3_dashboard.json
    RapDev ServerTech PDU Gen4 Dashboard: assets/dashboards/rapdev_servertech_pdu_gen4_dashboard.json
    RapDev Servertech PDU Gen3 Dashboard: assets/dashboards/rapdev_servertech_pdu_gen3_dashboard.json
    RapDev Servertech PDU Gen4 Dashboard: assets/dashboards/rapdev_servertech_pdu_gen4_dashboard.json
    RapDev Sophos XG Firewall Dashboard: assets/dashboards/rapdev_sophos_xg_firewall_dashboard.json
    RapDev Steelhead Riverbed Dashboard: assets/dashboards/rapdev_steelhead_riverbed_dashboard.json
    RapDev Tripplite PDU Dashboard: assets/dashboards/rapdev_tripplite_pdu_dashboard.json
    RapDev iDRAC Dashboard: assets/dashboards/rapdev_idrac_dashboard.json
  integration:
    auto_install: false
    configuration:
      spec: assets/configuration/spec.yaml
    events:
      creates_events: false
    metrics:
      check: snmp.devices_monitored
      metadata_path: metadata.csv
      prefix: snmp.
    service_checks:
      metadata_path: assets/service_checks.json
    source_type_id: 10122
    source_type_name: RapDev SNMP プロファイル
  monitors:
    Barracuda memory usage is high: assets/monitors/rapdev_barracuda-memory.json
    CMOS battery has an issue: assets/monitors/rapdev_idrac-cmos.json
    CPU load is high: assets/monitors/rapdev_barracuda-cpu.json
    CPU utilization is high: assets/monitors/rapdev_cpu_utilization.json
    Controller has an issue: assets/monitors/rapdev_idrac-raidcontrollerstate.json
    Disk has an issue: assets/monitors/rapdev_idrac-diskstatus.json
    Fan has an issue: assets/monitors/rapdev_idrac-fanstatus.json
    LCD has an issue: assets/monitors/rapdev_idrac-lcdstatus.json
    Memory DIMM has an issue: assets/monitors/rapdev_idrac-memorystatus.json
    Memory usage is high: assets/monitors/rapdev_memory_utilization.json
    Network port is down: assets/monitors/rapdev_idrac-nicconnectionstatus.json
    Operation status is in a bad state: assets/monitors/rapdev_interface_operstatus.json
    Power supply Sensor has an issue: assets/monitors/rapdev_idrac-psu-sensorstate.json
    Power supply has an issue: assets/monitors/rapdev_idrac-overallpowerstatus.json
    Power supply state settings has an issue: assets/monitors/rapdev_idrac-psu-statesettings.json
    Printer is in alert: assets/monitors/rapdev_printer_issue.json
    Service state changed: assets/monitors/rapdev_barracuda-servicestate.json
    Storage has an issue: assets/monitors/rapdev_idrac-overallstoragestate.json
    System BIOS has an issue: assets/monitors/rapdev_idrac-biosstatus.json
    Temperature sensor has an issue: assets/monitors/rapdev_idrac-tempsensorstatus.json
    VPN tunnel is downr: assets/monitors/rapdev_fortigate_tunnel.json
    Voltage has an issue: assets/monitors/rapdev_idrac-voltagestatus.json
author:
  homepage: https://www.rapdev.io
  name: RapDev
  sales_email: ddsales@rapdev.io
  support_email: support@rapdev.io
  vendor_id: rapdev
categories:
- marketplace
- ネットワーク
- snmp
custom_kind: インテグレーション
dependencies: []
display_on_public_website: true
draft: false
git_integration_title: rapdev-snmp-profiles
integration_id: rapdev-snmp-profiles
integration_title: SNMP プロファイル
integration_version: ''
is_public: true
legal_terms:
  eula: assets/EULA.pdf
manifest_version: 2.0.0
name: rapdev-snmp-profiles
pricing:
- billing_type: tag_count
  includes_assets: true
  metric: snmp.systemServices
  product_id: snmp-profiles
  short_description: デバイス 1 台あたりの単価
  tag: snmp_device
  unit_label: SNMP デバイス
  unit_price: 6
public_title: SNMP プロファイル
short_description: オートディスカバリーデバイスプロファイルによる SNMP デバイスへの観測性
supported_os:
- linux
- windows
- macos
tile:
  changelog: CHANGELOG.md
  classifier_tags:
  - Category::Marketplace
  - Category::Network
  - Category::SNMP
  - Offering::Integration
  - Supported OS::Linux
  - Supported OS::Windows
  - Supported OS::macOS
  - Submitted Data Type::Metrics
  configuration: README.md#Setup
  description: オートディスカバリーデバイスプロファイルによる SNMP デバイスへの観測性
  media:
  - caption: RapDev SNMP プロファイルの紹介
    image_url: images/video.png
    media_type: ビデオ
    vimeo_id: 630489707
  - caption: Cisco Meraki ダッシュボード
    image_url: images/6.png
    media_type: image
  - caption: Palo Alto Firewalls ダッシュボード
    image_url: images/2.png
    media_type: image
  - caption: Dell iDRAC ダッシュボード
    image_url: images/3.png
    media_type: image
  - caption: サーバーハードウェアタグの例
    image_url: images/5.png
    media_type: image
  - caption: HP iLO3/4 メトリクスの例
    image_url: images/1.png
    media_type: image
  - caption: Dell iDRAC メトリクスの例
    image_url: images/4.png
    media_type: image
  overview: README.md#Overview
  support: README.md#Support
  title: SNMP プロファイル
  uninstallation: README.md#Uninstallation
---

<!--  SOURCED FROM https://github.com/DataDog/marketplace -->

## 概要

[{{< img src="marketplace/rapdev-snmp-profiles/images/video.png" alt="RapDev SNMP プロファイルの紹介" >}}](https://www.youtube.com/watch?v=SVT9hqV7aD4&list=PLa2zzueYDhHrjODIXryBX_RakQIL6nmOh)

RapDev SNMP プロファイルパッケージは 150 以上のデバイスプロファイルをネイティブでサポートしています。事前組み込み型のダッシュボードもサポート対象のすべてのハードウェアデバイスに対応しており、迅速にデバイスを監視することができます。プロファイルのチューニングには数百時間を費やし、シリアルナンバー、ファームウェアのバージョン、ハードウェアのバージョンなど、必要なタグに関連するメトリクスをすべて収集できることを徹底的に確認しました。このインテグレーションのデプロイは数分で完了するため、モニタリング、可視化、アラートの生成をすばやくはじめることができます。

このインテグレーションでは数百種類の YAML プロファイルにアクセスし、多数の新しいダッシュボードをインスタンス上に自動デプロイすることができます。その後、ネイティブの Datadog SNMP オートディスカバリーを使用してサポート対象のハードウェアを自動検知し、ネイティブの Datadog SNMP インテグレーションで OID のポーリングを開始します。
お使いの Datadog Agent や YAML で SNMP プロファイルを管理、編集、修正、更新する必要はありません。これらの作業はすべてインテグレーションにより自動で行われるため、モニタリングとアラートの生成をすぐに開始することができます。

### サポートされているデバイス
以下は現在サポートされているデバイスの一覧です。すべてのデバイスについての最新リストは[ウェブサイト](https://www.rapdev.io/products/datadog-snmp-profiles)を参照してください。

| メーカー | モデル                 | バージョン       |
| ------------ | --------------------- | ------------- |
| APC          | Smart UPS             | All           |
| APC          | SmartCard             | All           |
| Arista       | Switch                | 7xxx          |
| Aruba        | Switch                | All           |
| AudioCodes   | Mediant SBC           | All           |
| Barracuda    | CloudGen Firewall     | 6、7、8         |
| Brocade      | VDX                   | All           |
| チェックポイント   | Gaia/Cloud Firewall   | 77+           |
| Cisco        | ASA                   | 5xxx          |
| Cisco        | ASR                   | All           |
| Cisco        | Catalyst              | All           |
| Cisco        | CUBE                  | IOS           |
| Cisco        | Nexus                 | 2k            |
| Cisco        | Nexus                 | 3k            |
| Cisco        | Nexus                 | 4k            |
| Cisco        | Nexus                 | 5k            |
| Cisco        | Nexus                 | 6k            |
| Cisco        | Nexus                 | 7k            |
| Cisco        | ISE                   | All           |
| Cisco        | ISR                   | 38XX、44xx    |
| Cisco        | Nexus                 | All           |
| Cisco        | UCM                   | All           |
| Cisco        | UCS                   | M2、M3、M4    |
| Cisco        | WLC                   | All           |
| Citrix       | Netscaler             | All           |
| Dell         | iDRAC                 | 7、8、9         |
| Dell         | Powerconnect          | OS10          |
| Dell         | Powerconnect          | 3000          |
| F5           | Big-IP                | 9.4.x - 15.x |
| FortiNet     | FortiGate             | All           |
| HPE          | ProLiant Gen8〜10      | iLO4、iLO5     |
| HPE          | Switch                |               |
| Ironport     | Mail                  | C3、C6、X1070   |
| Juniper      | SSG                   | All           |
| Kemp         | Loadmaster            | All           |
| Meraki       | CloudController       | CC            |
| Meraki       | Switch                | MR、MS、MX、Z |
| Nasuni       | Filer                 | All           |
| Palo Alto    | NextGen Firewall      | 9.x           |
| ServerTech   | PDU                   | Gen3、Gen4    |
| Sharp        | プリンタ               | All           |
| Steelhead    | Riverbed              | CX、EX        |
| VMware       | ESXi                  | 6.x           |

## サポート
サポートまたは機能リクエストについては、以下のチャンネルで RapDev.io までお問い合わせください。

 - メール: support@rapdev.io 
 - チャット: [rapdev.io](https://www.rapdev.io/#Get-in-touch)
 - 電話: 855-857-0222 

---
ボストンより ❤️ を込めて

*お探しのインテグレーションが見つかりませんか？組織に役立つ重要なツールの導入をお考えですか？[こちら](mailto:support@rapdev.io)からメッセージをお送りいただければ、導入をサポートいたします！*

---
このアプリケーションは Marketplace から入手でき、Datadog テクノロジーパートナーによってサポートされています。利用するには、<a href="https://app.datadoghq.com/marketplace/app/rapdev-snmp-profiles" target="_blank">Marketplace でこのアプリケーションを購入してください</a>。