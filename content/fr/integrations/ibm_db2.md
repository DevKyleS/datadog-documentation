---
app_id: ibm-db2
app_uuid: e588293a-833f-4888-a7b4-2208e087059a
assets:
  dashboards:
    IBM Db2 Overview: assets/dashboards/overview.json
  integration:
    configuration:
      spec: assets/configuration/spec.yaml
    events:
      creates_events: true
    metrics:
      check: ibm_db2.connection.active
      metadata_path: metadata.csv
      prefix: ibm_db2.
    service_checks:
      metadata_path: assets/service_checks.json
    source_type_name: IBM Db2
  logs:
    source: ibm_db2
author:
  homepage: https://www.datadoghq.com
  name: Datadog
  sales_email: info@datadoghq.com
  support_email: help@datadoghq.com
categories:
- data store
- log collection
dependencies:
- https://github.com/DataDog/integrations-core/blob/master/ibm_db2/README.md
display_on_public_website: true
draft: false
git_integration_title: ibm_db2
integration_id: ibm-db2
integration_title: IBM Db2
integration_version: 1.11.2
is_public: true
custom_kind: integration
manifest_version: 2.0.0
name: ibm_db2
public_title: IBM Db2
short_description: Surveillez les métriques de tablespace et de pool de mémoires tampon
  ainsi que d'autres métriques depuis votre base de données IBM Db2.
supported_os:
- linux
- macos
- windows
tile:
  changelog: CHANGELOG.md
  classifier_tags:
  - Supported OS::Linux
  - Supported OS::macOS
  - Supported OS::Windows
  - Category::Data Store
  - Category::Log Collection
  configuration: README.md#Setup
  description: Surveillez les métriques de tablespace et de pool de mémoires tampon
    ainsi que d'autres métriques depuis votre base de données IBM Db2.
  media: []
  overview: README.md#Overview
  support: README.md#Support
  title: IBM Db2
---



![Dashboard par défaut][1]

## Présentation

Ce check surveille [IBM Db2][2] avec l'Agent Datadog.

## Implémentation

### Installation

Le check IBM Db2 est inclus avec le package de l'[Agent Datadog][3].

#### Dépendances

La bibliothèque client [ibm_db][4] est requise. Pour l'installer, veillez à avoir un compilateur opérationnel et exécutez :

##### Unix

```text
sudo -Hu dd-agent /opt/datadog-agent/embedded/bin/pip install ibm_db==3.0.1
```

##### Windows

Pour les versions <= 6.11 de l'Agent :

```text
"C:\Program Files\Datadog\Datadog Agent\embedded\python.exe" -m pip install ibm_db==3.0.1
```

Pour les versions >= 6.12 et < 7.0 de l'Agent :

```text
"C:\Program Files\Datadog\Datadog Agent\embedded<VERSION_MAJEURE_PYTHON>\python.exe" -m pip install ibm_db==3.0.1
```

Pour le versions >= 7.0 de l'Agent :

```text
"C:\Program Files\Datadog\Datadog Agent\embedded3\python.exe" -m pip install ibm_db==3.1.0
```

Des fonctionnalités XML peuvent être requises sur Linux. Si vous rencontrez des erreurs durant
le processus de création, installez `libxslt-dev` (ou `libxslt-devel` pour RPM).

#### Activer la surveillance

Pour surveiller la santé d'une instance, de ses bases de données et de ses objets de base de données, activez les switches de surveillance du système de base de données pour chacun des objets à surveiller :
* Statement
* Lock
* Tables
* Buffer pool

Basculez sur l'utilisateur principal de l'instance et exécutez ces commandes dans l'invite `db2` :

```text
update dbm cfg using HEALTH_MON on
update dbm cfg using DFT_MON_STMT on
update dbm cfg using DFT_MON_LOCK on
update dbm cfg using DFT_MON_TABLE on
update dbm cfg using DFT_MON_BUFPOOL on
```

Exécutez ensuite `get dbm cfg` pour obtenir un résultat similaire à ce qui suit :

```text
 Default database monitor switches
   Buffer pool                         (DFT_MON_BUFPOOL) = ON
   Lock                                   (DFT_MON_LOCK) = ON
   Sort                                   (DFT_MON_SORT) = OFF
   Statement                              (DFT_MON_STMT) = ON
   Table                                 (DFT_MON_TABLE) = ON
   Timestamp                         (DFT_MON_TIMESTAMP) = ON
   Unit of work                            (DFT_MON_UOW) = OFF
 Monitor health of instance and databases   (HEALTH_MON) = ON
```

### Configuration

{{< tabs >}}
{{% tab "Host" %}}

#### Host

Pour configurer ce check lorsque l'Agent est exécuté sur un host :

##### Collecte de métriques

1. Modifiez le fichier `ibm_db2.d/conf.yaml` dans le dossier `conf.d/` à la racine du répertoire de configuration de votre Agent pour commencer à recueillir vos données de performance `ibm_db2`. Consultez le [fichier d'exemple ibm_db2.d/conf.yaml][1] pour découvrir toutes les options de configuration disponibles.

2. [Redémarrez l'Agent][2].

##### Collecte de logs

_Disponible à partir des versions > 6.0 de l'Agent_

1. La collecte de logs est désactivée par défaut dans l'Agent Datadog. Vous devez l'activer dans `datadog.yaml` :

   ```yaml
   logs_enabled: true
   ```

2. Ajoutez ce bloc de configuration à votre fichier `ibm_db2.d/conf.yaml` pour commencer à recueillir vos logs IBM Db2 :

   ```yaml
   logs:
     - type: file
       path: /home/db2inst1/sqllib/db2dump/db2diag.log
       source: ibm_db2
       service: db2sysc
       log_processing_rules:
         - type: multi_line
           name: new_log_start_with_date
           pattern: \d{4}\-(0?[1-9]|[12][0-9]|3[01])\-(0?[1-9]|1[012])
   ```

3. [Redémarrez l'Agent][2].

[1]: https://github.com/DataDog/integrations-core/blob/master/ibm_db2/datadog_checks/ibm_db2/data/conf.yaml.example
[2]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/#start-stop-restart-the-agent
{{% /tab %}}
{{% tab "Environnement conteneurisé" %}}

#### Environnement conteneurisé

Consultez la [documentation relative aux modèles d'intégration Autodiscovery][1] pour découvrir comment appliquer les paramètres ci-dessous à un environnement conteneurisé.

##### Collecte de métriques

| Paramètre            | Valeur                                                                                                         |
| -------------------- | ------------------------------------------------------------------------------------------------------------- |
| `<NOM_INTÉGRATION>` | `ibm_db2`                                                                                                     |
| `<CONFIG_INIT>`      | vide ou `{}`                                                                                                 |
| `<CONFIG_INSTANCE>`  | `{"db": "<NOM_BASEDEDONNÉES>", "username":"<NOMUTILISATEUR>", "password":"<MOTDEPASSE>", "host":"%%host%%", "port":"%%port%%"}` |

##### Collecte de logs

_Disponible à partir des versions > 6.0 de l'Agent_

La collecte des logs est désactivée par défaut dans l'Agent Datadog. Pour l'activer, consultez la section [Collecte de logs Kubernetes][2].

| Paramètre      | Valeur                                                                                                                                                                                                |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<CONFIG_LOG>` | `{"source": "ibm_db2", "service": "<NOM_SERVICE>", "log_processing_rules": {"type":"multi_line","name":"new_log_start_with_date", "pattern":"\d{4}\-(0?[1-9]|[12][0-9]|3[01])\-(0?[1-9]|1[012])"}}` |

[1]: https://docs.datadoghq.com/fr/agent/kubernetes/integrations/
[2]: https://docs.datadoghq.com/fr/agent/kubernetes/log/
{{% /tab %}}
{{< /tabs >}}

### Validation

[Lancez la sous-commande status de l'Agent][5] et cherchez `ibm_db2` dans la section Checks.

## Données collectées

### Métriques
{{< get-metrics-from-git "ibm-db2" >}}


### Événements

- `ibm_db2.tablespace_state_change` se déclenche à chaque changement d'état d'un tablespace.

### Checks de service
{{< get-service-checks-from-git "ibm-db2" >}}


## Dépannage

Besoin d'aide ? Contactez [l'assistance Datadog][6].

## Pour aller plus loin

Documentation, liens et articles supplémentaires utiles :

- [Surveiller IBM DB2 avec Datadog][7]


[1]: https://raw.githubusercontent.com/DataDog/integrations-core/master/ibm_db2/images/dashboard_overview.png
[2]: https://www.ibm.com/analytics/us/en/db2
[3]: https://app.datadoghq.com/account/settings#agent
[4]: https://github.com/ibmdb/python-ibmdb/tree/master/IBM_DB/ibm_db
[5]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/#agent-status-and-information
[6]: https://docs.datadoghq.com/fr/help/
[7]: https://www.datadoghq.com/blog/monitor-db2-with-datadog