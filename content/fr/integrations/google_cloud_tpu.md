---
categories:
  - cloud
  - google cloud
  - log collection
ddtype: crawler
dependencies: []
description: "Surveillez des métriques clés de Google\_TPU."
doc_link: https://docs.datadoghq.com/integrations/google_cloud_tpu/
draft: false
git_integration_title: google_cloud_tpu
has_logo: true
integration_id: google-cloud-tpu
integration_title: "Google\_Cloud\_TPU"
integration_version: ''
is_public: true
custom_kind: integration
manifest_version: '1.0'
name: google_cloud_tpu
public_title: "Intégration Datadog/Google\_Cloud\_TPU"
short_description: "Surveillez des métriques clés de Google\_TPU."
version: '1.0'
---
## Présentation

Avec Google Cloud TPU, tous les chercheurs, ingénieurs et développeurs en ML, ainsi que tous les data scientists exécutant des modèles de ML novateurs, peuvent profiter des avantages offerts par les Tensor Processing Units (TPU) grâce à des ressources de cloud computing évolutives et faciles à utiliser.

Utilisez l'intégration Datadog/Google Cloud Platform pour recueillir des métriques de Google Cloud TPU.

## Configuration

### Installation

Si vous ne l'avez pas déjà fait, configurez d'abord [l'intégration Google Cloud Platform][1]. Aucune autre procédure d'installation n'est requise.

### Collecte de logs

Les logs Google Cloud TPU sont recueillis avec Google Cloud Logging et envoyés à un Cloud Pub/Sub via un forwarder Push HTTP. Si vous ne l'avez pas déjà fait, configurez un [Cloud Pub/Sub à l'aide d'un forwarder Push HTTP][2].

Une fois cette opération effectuée, exportez vos logs Google Cloud TPU depuis Google Cloud Logging vers le Pub/Sub :

1. Accédez à la [page Google Cloud Logging][3] et filtrez les logs Google Cloud TPU.
2. Cliquez sur **Create Export** et nommez le récepteur.
3. Choisissez Cloud Pub/Sub comme destination et sélectionnez le Pub/Sub créé à cette fin. **Remarque** : le Pub/Sub peut se situer dans un autre projet.
4. Cliquez sur **Create** et attendez que le message de confirmation s'affiche.

## Données collectées

### Métriques
{{< get-metrics-from-git "google-cloud-tpu" >}}


### Événements

L'intégration Google Cloud TPU n'inclut aucun événement.

### Checks de service

L'intégration Google Cloud TPU n'inclut aucun check de service.

## Dépannage

Besoin d'aide ? Contactez [l'assistance Datadog][5].

[1]: https://docs.datadoghq.com/fr/integrations/google_cloud_platform/
[2]: https://docs.datadoghq.com/fr/integrations/google_cloud_platform/#log-collection
[3]: https://console.cloud.google.com/logs/viewer
[4]: https://github.com/DataDog/dogweb/blob/prod/integration/google_cloud_tpu/google_cloud_tpu_metadata.csv
[5]: https://docs.datadoghq.com/fr/help/