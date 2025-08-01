---
aliases:
- /fr/integrations/azure_customerinsights
categories:
- cloud
- azure
custom_kind: integration
dependencies: []
description: Surveillez des métriques clés d'Azure Customer Insights.
doc_link: https://docs.datadoghq.com/integrations/azure_customer_insights/
draft: false
git_integration_title: azure_customer_insights
has_logo: true
integration_id: azure-customerinsights
integration_title: Microsoft Azure Customer Insights
integration_version: ''
is_public: true
manifest_version: '1.0'
name: azure_customer_insights
public_title: Intégration Datadog/Microsoft Azure Customer Insights
short_description: Surveillez des métriques clés d'Azure Customer Insights.
version: '1.0'
---

<!--  SOURCED FROM https://github.com/DataDog/dogweb -->
## Section Overview

Azure Customer Insights permet aux organisations de toute taille de consolider divers jeux de données et de générer des informations et des statistiques pour bénéficier d'une vue globale sur leurs clients.

Utilisez l'intégration Datadog/Azure pour recueillir des métriques de Customer Insights.

## Configuration

### Installation

Si vous ne l'avez pas déjà fait, configurez d'abord [l'intégration Microsoft Azure][1]. Aucune autre procédure d'installation n'est requise.

## Données collectées

### Métriques
{{ get-metrics-from-git "azure-customerinsights" }}


### Événements

L'intégration Azure Customer Insights n'inclut aucun événement.

### Checks de service

L'intégration Azure Customer Insights n'inclut aucun check de service.

## Dépannage

Besoin d'aide ? Contactez [l'assistance Datadog][3].

[1]: https://docs.datadoghq.com/fr/integrations/azure/
[2]: https://github.com/DataDog/dogweb/blob/prod/integration/azure_customer_insights/azure_customer_insights_metadata.csv
[3]: https://docs.datadoghq.com/fr/help/