---
categories:
- cloud
- configuration & deployment
- google cloud
- log collection
dependencies: []
description: Surveillez des métriques clés de Google Cloud Composer.
doc_link: https://docs.datadoghq.com/integrations/google_cloud_composer/
draft: false
git_integration_title: google_cloud_composer
has_logo: true
integration_id: google-cloud-composer
integration_title: Google Cloud Composer
integration_version: ''
is_public: true
custom_kind: integration
manifest_version: '1.0'
name: google_cloud_composer
public_title: Intégration Datadog/Google Cloud Composer
short_description: Surveillez des métriques clés de Google Cloud Composer.
version: '1.0'
---

## Présentation

Google Cloud Composer est un service d'orchestration de workflows entièrement géré qui vous permet de créer, de planifier et de surveiller vos pipelines dans plusieurs clouds ou des centres de données sur site.

Utilisez l'intégration Datadog/Google Cloud Platform pour recueillir des métriques de Google Composer.

## Implémentation

### Installation

Si vous ne l'avez pas déjà fait, configurez d'abord [l'intégration Google Cloud Platform][1]. Aucune autre procédure d'installation n'est requise.

### Collecte de logs

Les logs Google Cloud Composer sont recueillis avec Google Cloud Logging et envoyés à un Cloud Pub/Sub via un forwarder Push HTTP. Si vous ne l'avez pas déjà fait, configurez un [Cloud Pub/Sub à l'aide d'un forwarder Push HTTP][2].

Une fois cette opération effectuée, exportez vos logs Google Cloud Composer depuis Google Cloud Logging vers le Pub/Sub :

1. Accédez à la [page Google Cloud Logging][3] et filtrez les logs Google Cloud Composer.
2. Cliquez sur **Create Export** et nommez le récepteur.
3. Choisissez Cloud Pub/Sub comme destination et sélectionnez le Pub/Sub créé à cette fin. **Remarque** : le Pub/Sub peut se situer dans un autre projet.
4. Cliquez sur **Create** et attendez que le message de confirmation s'affiche.

## Données collectées

### Métriques
{{< get-metrics-from-git "google-cloud-composer" >}}


### Événements

L'intégration Google Cloud Composer n'inclut aucun événement.

### Checks de service

L'intégration Google Cloud Composer n'inclut aucun check de service.

## Dépannage

Besoin d'aide ? Contactez [l'assistance Datadog][5].

[1]: https://docs.datadoghq.com/fr/integrations/google_cloud_platform/
[2]: https://docs.datadoghq.com/fr/integrations/google_cloud_platform/#log-collection
[3]: https://console.cloud.google.com/logs/viewer
[4]: https://github.com/DataDog/dogweb/blob/prod/integration/google_cloud_composer/google_cloud_composer_metadata.csv
[5]: https://docs.datadoghq.com/fr/help/