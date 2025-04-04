---
aliases:
- /fr/logs/dynamic_volume_control
- /fr/logs/indexes/
description: Contrôler le volume de logs indexés par Datadog
further_reading:
- link: /logs/explorer/#visualiser-les-donnees
  tag: Documentation
  text: Effectuer des analyses de logs
- link: /logs/log_configuration/processors
  tag: Documentation
  text: Apprendre à traiter vos logs
- link: /logs/log_configuration/parsing
  tag: Documentation
  text: En savoir plus sur le parsing
- link: https://www.datadoghq.com/blog/logging-without-limits/
  tag: Blog
  text: Logging without Limits*
title: Index
---

Les index de logs offrent un contrôle précis sur le coût de votre gestion de logs. Ils vous permettent de répartir vos données en plusieurs groupes de valeurs, afin d'appliquer différents critères de rétention, de quotas, de surveillance de l'utilisation ou de facturation. Les index se trouvent sur la [page Configuration][1], dans la section Indexes. Cliquez deux fois sur les index ou sur le bouton *edit* pour découvrir le nombre de logs indexés au cours des trois derniers jours, ainsi que la période de rétention de ces logs :

{{< img src="logs/indexes/index_details.jpg" alt="détails de l'index" style="width:70%;">}}

Vous pouvez utiliser des logs indexés pour la [recherche à facettes][2], les [patterns][3], les [analyses][4] et la [surveillance][6].

## Utilisation de plusieurs index

Par défaut, chaque nouveau compte dispose d'un seul index représentant un ensemble monolithique composé de tous vos logs. Datadog recommande d'utiliser plusieurs index selon vos besoins, afin de bénéficier des fonctionnalités suivantes :

* Plusieurs [périodes de rétention](#mettre-a-jour-la-retention-des-logs)
* Plusieurs [quotas journaliers](#definir-un-quota-journalier), pour un meilleur contrôle de votre budget

Le Log Explorer prend en charge l'envoi de [requêtes sur plusieurs index][7].

### Ajouter des index

Cliquez sur le bouton New Index pour créer un index. Une limite de création d'index s'applique à chaque compte (par défaut, 100).

{{< img src="logs/indexes/add-index.png" alt="Ajouter un index" style="width:70%;">}}

**Remarque** : les noms d'index doivent commencer par une lettre et ne peuvent contenir que des lettres minuscules, des nombres ou le caractère '-'.

<div class="alert alert-info">
<a href="/help">Contactez l'assistance Datadog</a> si vous souhaitez augmenter le nombre maximal d'index de votre compte.
</div>

### Supprimer des index

Pour supprimer un index de votre organisation, cliquez sur l'icône de suppression dans le menu d'actions de l'index. Cette option est uniquement disponible pour les utilisateurs disposant de l'autorisation `Logs delete data`.

{{< img src="logs/indexes/delete-index.png" alt="Supprimer un index" style="width:70%;">}}

<div class="alert alert-warning">
Il n'est pas possible de créer un index si son nom est identique à celui d'un index supprimé. 
</div>

**Remarque** : les index supprimés n'acceptent plus les nouveaux logs entrants. Ces logs ne peuvent donc plus être interrogés. Dès que les logs auront dépassé leur période de rétention, l'index ne s'affichera plus sur la page Index.



## Filtres d'index

Grâce aux filtres d'index, vous pouvez choisir de façon dynamique à quels index sont transmis les logs de votre choix. Par exemple, si vous créez un premier index filtré sur l'attribut `status:notice`, un deuxième index filtré sur l'attribut `status:error` et un dernier index sans filtre (équivalent à `*`), tous vos logs `status:notice` sont envoyés au premier index, tous vos logs `status:error` vont dans le deuxième index et tous les autres logs sont rassemblés dans le dernier index.

{{< img src="logs/indexes/multi_index.png" alt="Index multiples" style="width:70%;">}}

**Remarque** : **les logs sont envoyés dans le premier index filtré auquel ils correspondent**. Faites glisser et déposez les index dans la liste pour modifier leur ordre, selon vos besoins.

## Filtres d'exclusion

Par défaut, les index de logs ne possèdent pas de filtre d'exclusion. Ainsi, tous les logs correspondant à leur filtre sont indexés.

Toutefois, puisque vos logs ne sont pas tous utiles, les filtres d'exclusion contrôlent leur transmission dans l'index afin d'identifier les logs à supprimer. Les logs exclus sont supprimés des index, mais continuent à être analysés par la fonctionnalité de [live tailing][8] et peuvent être utilisés pour [générer des métriques][9] et être [archivés][10].

Pour ajouter un filtre d'exclusion, procédez comme suit :

1. Accédez à [Log Indexes][11].
2. Développez le pipeline auquel vous souhaitez ajouter un filtre d'exclusion.
3. Cliquez sur **Add an Exclusion Filter**.

Les filtres d'exclusion sont définis par une requête, une règle d'échantillonnage et un bouton d'activation :

* La **query** (requête) par défaut est `*`. Celle-ci entraîne l'exclusion de tous les logs transmis dans l'index. Réduisez le filtre d'exclusion en définissant uniquement un sous-ensemble de logs [avec une requête de log][12].
* La **sampling rule** (règle d'échantillonnage) par défaut `Exclude 100% of logs` exclut tous les logs correspondant à la requête. Définissez un taux d'échantillonnage entre 0 et 100 %, et choisissez de l'appliquer à chaque log ou à des groupes de logs caractérisés par les valeurs uniques d'un attribut.
* Par défaut, le filtre est activé. Les logs sont donc supprimés de l'index selon les paramètres du filtre. Cliquez sur le bouton pour désactiver le filtre afin de l'ignorer pour les nouveaux logs transmis.

**Remarque** : les filtres d'index pour les logs sont uniquement traités avec le premier filtre d'exclusion **actif** correspondant. Si un log correspond à un filtre d'exclusion (même si le log n'est pas transmis en raison d'un échantillonnage), il ignore tous les filtres d'exclusion suivants.

Faites glisser et déposez les filtres d'exclusion dans la liste pour modifier leur ordre, selon vos besoins.

{{< img src="logs/indexes/reorder_index_filters.png" alt="modifier l'ordre des filtres d'index" style="width:80%;">}}

### Exemples

#### Désactivation et activation d'un filtre

Imaginons que vous souhaitez uniquement utiliser vos logs DEBUG lorsque votre plate-forme souffre d'une défaillance, ou que vous cherchez à surveiller le déploiement d'une version critique de votre application. Configurez un filtre d'exclusion à 100 % sur `status:DEBUG`, et activez-le ou désactivez-le à partir de l'interface Datadog ou via l'[API][13] dès que vous en avez besoin.

{{< img src="logs/indexes/enable_index_filters.png" alt="activer les filtres d'index" style="width:80%;">}}

#### Suivi des tendances

Imaginons que vous n'avez pas besoin de conserver tous les logs liés aux requêtes serveur d'accès Web. Vous pouvez choisir d'indexer tous les logs 3xx, 4xx et 5xx, mais d'exclure 95 % des logs 2xx avec `source:nginx AND http.status_code:[200 TO 299]` pour surveiller les tendances.
**Astuce** : transformez vos logs d'accès Web en KPI utiles en [générant une métrique à partir de vos logs][9]. Celle-ci vous permettra par exemple de compter le nombre de requêtes et de les taguer par code de statut, [navigateur][14] et [pays][15].

{{< img src="logs/indexes/sample_200.png" alt="activer les filtres d'index" style="width:80%;">}}

#### Échantillonnage cohérent avec des entités de plus haut niveau

Des millions d'utilisateurs se connectent à votre site Web chaque jour. Bien que vous n'ayez pas besoin de connaître parfaitement tous vos utilisateurs, il peut être utile de comprendre les caractéristiques de certains d'entre eux. Définissez un filtre d'exclusion qui s'applique à tous les logs de production (`env:production`) et excluez les logs pour 90 % de `@user.email` :

{{< img src="logs/indexes/sample_user_id.png" alt="activer les filtres d'index" style="width:80%;">}}

Vous pouvez utiliser l'APM avec les logs grâce à l'[injection d'ID de trace dans les logs][16]. Vous n'avez pas besoin de garder tous vos logs sur les utilisateurs. Cependant, veillez à ce que les logs que vous conservez représentent tous les aspects d'une trace. Une telle pratique simplifiera grandement votre correction des problèmes.
Configurez un filtre d'exclusion appliqué aux logs à partir de votre service instrumenté (`service:mon_app_python`) et excluez les logs pour 50 % des `Trace ID`. Assurez-vous d'utiliser le [remappeur d'ID de trace][17] en amont dans vos pipelines.

{{< img src="logs/indexes/sample_trace_id.png" alt="activer les filtres d'index" style="width:80%;">}}

Pour garantir un échantillonnage homogène parmi plusieurs index :

1. Créez une règle d'exclusion dans chaque index.
2. Utilisez le **même taux d'échantillonnage** et le **même attribut** pour définir l'entité de niveau supérieur de toutes les règles d'exclusion.
3. Vérifiez bien les règles d'exclusion, les **filtres** et l'**ordre respectif** (seule la première règle d'exclusion correspondante est appliquée aux logs).

Dans les exemples suivants :

{{< img src="logs/indexes/cross-index_sampling.png" alt="activer les filtres d'index" style="width:80%;">}}

* En général, tous les logs avec un `request_id` spécifique sont soit conservés soit exclus (probabilité de 50 %).
* Les logs dotés d'un tag `threat:true` ou `compliance:true` sont tous conservés, quel que soit leur `request_id`.
* Les logs `DEBUG` sont indexés selon la règle d'échantillonnage `request_id`, sauf si le filtre d'exclusion des logs de debugging est activé. Dans ce cas, les logs sont échantillonnés.
* 50 % des logs d'accès Web `2XX` dotés d'un `request_id` sont conservés. Tous les autres logs d'accès Web `2XX` sont échantillonnés selon la règle du filtre d'exclusion à 90 %.

## Mettre à jour la rétention des logs

Le paramètre de rétention des index spécifie la durée pendant laquelle les logs sont stockés et interrogeables dans Datadog. Vous pouvez choisir la durée de rétention de votre choix dans la configuration de votre compte.

Pour permettre l'ajout de rétentions supplémentaires qui ne figurent pas dans votre contrat actuel, contactez le service client à l'adresse suivante : `success@datadoghq.com`. Une fois les rétentions supplémentaires activées, vous devez mettre à jour les périodes de rétention de vos index.

{{< img src="logs/indexes/log_retention.png" alt="détails de l'index" style="width:70%;">}}

**Remarque** : pour utiliser des rétentions qui ne font actuellement pas partie de votre contrat, [l'option correspondante][21] doit être activée dans les paramètres de votre organisation par un administrateur.

## Définir un quota journalier

Il est possible de définir un quota journalier pour limiter le nombre de logs stockés dans un index au cours d'une journée. Ce quota est appliqué à l'ensemble des logs qui auraient dû être stockés (c'est-à-dire une fois les filtres d'exclusion appliqués).
Une fois ce quota atteint, les logs ne sont plus indexés. Ils restent toutefois disponibles pour le [live tailing][18] et continuent à être [envoyés vers vos archives][10] et utilisés pour [générer des métriques][9].

Ce quota peut être configuré ou supprimé à tout moment en modifiant l'index :
- Définissez un quota journalier en millions de logs.
- (Facultatif) Définissez une heure de réinitialisation personnalisée. Par défaut, les quotas d'index journaliers sont automatiquement réinitialisés à [14 heures (UTC)][19].
- (Facultatif) Définissez un seuil d'avertissement correspondant à un pourcentage du quota journalier (minimum : 50 %).

**Remarque** : les modifications apportées aux quotas journaliers et aux seuils d'alerte prennent effet immédiatement.

{{< img src="logs/indexes/daily_quota_config.png" alt="détails d'un index" style="width:70%;">}}

Un événement est généré lorsque le quota journalier ou le seuil d'avertissement est atteint :

{{< img src="logs/indexes/index_quota_event.png" alt="notification de quota pour un index" style="width:70%;">}}

Consultez la rubrique [Surveiller l'utilisation des logs][20] pour découvrir comment surveiller votre utilisation et envoyer des alertes à ce sujet.

## Pour aller plus loin

{{< partial name="whats-next/whats-next.html" >}}
<br>
*Logging without Limits est une marque déposée de Datadog, Inc.

[1]: https://app.datadoghq.com/logs/pipelines/
[2]: /fr/logs/explorer/#visualization
[3]: /fr/logs/explorer/patterns/
[4]: /fr/logs/explorer/analytics/
[6]: /fr/monitors/types/log/
[7]: /fr/logs/explorer/facets/#the-index-facet
[8]: /fr/logs/live_tail/
[9]: /fr/logs/logs_to_metrics/
[10]: /fr/logs/archives/
[11]: https://app.datadoghq.com/logs/pipelines/indexes
[12]: /fr/logs/search_syntax/
[13]: /fr/api/v1/logs-indexes/#update-an-index
[14]: /fr/logs/log_configuration/processors/#user-agent-parser
[15]: /fr/logs/log_configuration/processors/#geoip-parser
[16]: /fr/tracing/other_telemetry/connect_logs_and_traces/
[17]: /fr/logs/log_configuration/processors/#trace-remapper
[18]: /fr/logs/live_tail/#overview
[19]: https://www.timeanddate.com/worldclock/converter.html
[20]: /fr/logs/guide/best-practices-for-log-management/#monitor-log-usage
[21]: /fr/account_management/org_settings/#out-of-contract-retention-periods-for-log-indexes