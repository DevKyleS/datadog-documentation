---
further_reading:
- link: https://www.datadoghq.com/blog/collect-traces-logs-from-cloud-run-with-datadog/
  tag: Blog
  text: Recopilar trazas, logs y métricas personalizadas de servicios de Cloud Run
title: Google Cloud Run con serverless-init
---

## Información general

<div class="alert alert-info">Si estás ejecutando varios contenedores por aplicación de Google Cloud Run, Datadog recomienda utilizar el auxiliar de Datadog: <a href="/serverless/google_cloud_run">Instrumentar Google Cloud Run</a>.</div>

Google Cloud Run es una plataforma serverless totalmente gestionada que sirve para desplegar y escalar aplicaciones basadas en contenedores. Datadog ofrece monitorización y recopilación de logs para Cloud Run a través de la [integración de Google Cloud][1]. Datadog también brinda una solución para instrumentar las aplicaciones de Cloud Run con un Agent especialmente diseñado para habilitar el rastreo, las métricas personalizadas y la recopilación directa de logs.

### Requisitos previos

Asegúrate de que dispones de una [clave de la API de Datadog][7] y de que utilizas un lenguaje de programación [compatible con una biblioteca de rastreo de Datadog][2].

## Instrumentar tu aplicación

Puedes instrumentar tu aplicación en una de dos maneras: [archivo de Docker](#dockerfile) o [paquete de compilación](#buildpack).

### Archivo de Docker

Datadog publica nuevas versiones de la imagen de contenedor `serverless-init` en gcr.io de Google, AWS ECR y Docker Hub:

| dockerhub.io | gcr.io | public.ecr.aws |
| ------------ | ------ | -------------- |
| datadog/serverless-init | gcr.io/datadoghq/serverless-init | public.ecr.aws/datadog/serverless-init |

Las imágenes se etiquetan a partir de versiones semánticas y cada nueva versión recibe tres etiquetas:

* `1`, `1-alpine`: utilízalos para realizar un seguimiento de las últimas versiones secundarias, sin cambios importantes.
* `1.x.x`, `1.x.x-alpine`: utilízalos para fijar una versión precisa de la biblioteca
* `latest`, `latest-alpine`: utilízalos para seguir la última versión, que puede incluir cambios de última hora.

## Cómo funciona `serverless-init` 

La aplicación `serverless-init` envuelve tu proceso y lo ejecuta como un subproceso. Inicia un agente de escucha de DogStatsD para las métricas y un agente de escucha de Trace Agent para las trazas (traces). Envuelve los flujos stdout/stderr de tu aplicación para recuperar los logs. Después del arranque, serverless-init lanza tu comando como un subproceso.

Para obtener una instrumentación completa, asegúrate de llamar a `datadog-init` como el primer comando que se ejecuta dentro de tu contenedor de Docker. Puedes hacerlo si configuras datadog-init como el punto de entrada o como el primer argumento en el CMD.

{{< programming-lang-wrapper langs="nodejs,python,java,go,dotnet,ruby,php" >}}
{{< programming-lang lang="nodejs" >}}

{{% svl-init-nodejs %}}

{{< /programming-lang >}}
{{< programming-lang lang="python" >}}

{{% svl-init-python %}}

{{< /programming-lang >}}
{{< programming-lang lang="java" >}}

{{% svl-init-java %}}

{{< /programming-lang >}}
{{< programming-lang lang="go" >}}

{{% svl-init-go %}}

{{< /programming-lang >}}
{{< programming-lang lang="dotnet" >}}

{{% svl-init-dotnet %}}

{{< /programming-lang >}}
{{< programming-lang lang="ruby" >}}

{{% svl-init-ruby %}}

{{< /programming-lang >}}
{{< programming-lang lang="php" >}}

{{% svl-init-php %}}

{{< /programming-lang >}}
{{< /programming-lang-wrapper >}}

### Paquete de compilación

[`Pack Buildpacks`][3] ofrece una manera conveniente de empaquetar un contenedor sin utilizar un archivo Docker.

En primer lugar, instala manualmente tu rastreador:
- [Node.JS][14]
- [Python][13]
- [Java][15]
- [Go][12]
- [.NET][18]
- [Ruby][16]
- [PHP][17]

A continuación, compila tu aplicación ejecutando el siguiente comando:

```shell
pack build --builder=gcr.io/buildpacks/builder \
--buildpack from=builder \
--buildpack datadog/serverless-buildpack:latest \
gcr.io/<YOUR_PROJECT>/<YOUR_APP_NAME>
```

**Nota**: La instrumentación del paquete de compilación no es compatible con imágenes de Alpine.

## Configurar tu solicitud

Una vez que el contenedor se ha creado y enviado al registro, el último paso consiste en configurar las variables de entorno obligatorias del Datadog Agent:
- `DD_API_KEY`: la clave de la API de Datadog, utilizada para enviar datos a tu cuenta de Datadog. Debe configurarse como un [secreto de Google Cloud][11] por cuestiones de privacidad y seguridad.
- `DD_SITE`: el endpoint y el sitio web de Datadog. Selecciona tu sitio en la parte derecha de esta página. Tu sitio es: {{< region-param key="dd_site" code="true" >}}.

Para conocer más variables de entorno y su función, ve [Variables de entorno](#environment-variables).

El siguiente comando despliega el servicio y permite que cualquier conexión externa lo alcance. En este ejemplo, la escucha del servicio se establece en el puerto 8080. Asegúrate de que este número de puerto coincide con el puerto expuesto dentro de tu archivo de Docker.

```
shell
gcloud run deploy <APP_NAME> --image=gcr.io/<YOUR_PROJECT>/<APP_NAME> \
  --port=8080 \
  --update-env-vars=DD_API_KEY=$DD_API_KEY \
  --update-env-vars=DD_SITE=$DD_SITE \
```

Consulta [todos los argumentos e indicadores para `gcloud run deploy`][26].

## Resultados

Una vez completado el despliegue, tus métricas y trazas se envían a Datadog. En Datadog, navega a **Infrastructure > Serverless** (Infraestructura > Sin servidor) para ver tus métricas y trazas sin servidor.

## Configuraciones adicionales

- **Rastreo avanzado:** el Datadog Agent ya proporciona un rastreo básico para los marcos más populares. Sigue la [guía de rastreo avanzado][2] para obtener más información.

- **Logs:** si utilizas la [integración de Google Cloud][1], tus logs ya se recopilan. Como alternativa, puedes definir la variable de entorno `DD_LOGS_ENABLED` como `true` para capturar los logs de aplicación directamente a través de la instrumentación serverless.

- **Métricas personalizadas:** puedes enviar métricas personalizadas mediante un [cliente de DogStatsD][4]. Para monitorizar Cloud Run y otras aplicaciones serverless, utiliza métricas de [distribución][9]. Las distribuciones ofrecen las agregaciones predeterminadas `avg`, `sum`, `max`, `min` y `count`. En la página Metric Summary (Resumen de métrica), puedes habilitar las agregaciones percentiles (p50, p75, p90, p95, p99) y también gestionar etiquetas. Para monitorizar una distribución para un tipo de métrica gauge, utiliza `avg` tanto para las [agregaciones temporales como espaciales][11]. Para monitorizar una distribución para un tipo de métrica count, utiliza `sum` tanto para las agregaciones temporales como espaciales.

### Variables de entorno

| Variable | Descripción |
| -------- | ----------- |
|`DD_API_KEY`| [Clave de API de Datadog][7]: **obligatorio**|
| `DD_SITE` | [Sitio de Datadog][5]: **obligatorio** |
| `DD_LOGS_ENABLED` | Cuando es true, envía logs (stdout y stderr) a Datadog. Por defecto, es false. |
| `DD_LOGS_INJECTION`| Cuando es true, enriquece todos los logs con datos de trazas para los registradores admitidos en [Java][19], [Node.js][20], [.NET][21] y [PHP][22]. Consulta la documentación adicional de [Python][23], [Go][24] y [Ruby][25]. |
| `DD_SERVICE`      | Consulta [Etiquetado de servicios unificado][6].                                  |
| `DD_VERSION`      | Consulta [Etiquetado de servicios unificado][6].                                  |
| `DD_ENV`          | Consulta [Etiquetado de servicios unificado][6].                                  |
| `DD_SOURCE`       | Consulta [Etiquetado de servicios unificado][6].                                  |
| `DD_TAGS`         | Consulta [Etiquetado de servicios unificado][6].                                  |

## Solucionar problemas

Esta integración depende de que tu tiempo de ejecución tenga una implementación SSL completa. Si estás usando una imagen slim, es posible que debas añadir el siguiente comando a tu Dockerfile para incluir certificados.

```
RUN apt-get update && apt-get install -y ca-certificates
```


## Referencias adicionales

{{< partial name="whats-next/whats-next.html" >}}


[1]: /es/integrations/google_cloud_platform/#log-collection
[2]: /es/tracing/trace_collection/#for-setup-instructions-select-your-language
[3]: https://buildpacks.io/docs/tools/pack/
[4]: /es/metrics/custom_metrics/dogstatsd_metrics_submission/
[5]: /es/getting_started/site/
[6]: /es/getting_started/tagging/unified_service_tagging/
[7]: /es/account_management/api-app-keys/#api-keys
[8]: https://github.com/DataDog/crpb/tree/main
[9]: /es/metrics/distributions/
[10]: /es/metrics/#time-and-space-aggregation
[11]: https://cloud.google.com/run/docs/configuring/secrets
[12]: /es/tracing/trace_collection/library_config/go/
[13]: /es/tracing/trace_collection/dd_libraries/python/?tab=containers#instrument-your-application
[14]: /es/tracing/trace_collection/dd_libraries/nodejs/?tab=containers#instrument-your-application
[15]: /es/tracing/trace_collection/dd_libraries/java/?tab=containers#instrument-your-application
[16]: /es/tracing/trace_collection/dd_libraries/ruby/?tab=containers#instrument-your-application
[17]: /es/tracing/trace_collection/dd_libraries/php/?tab=containers#install-the-extension
[18]: /es/tracing/trace_collection/dd_libraries/dotnet-core/?tab=linux#custom-instrumentation
[19]: /es/tracing/other_telemetry/connect_logs_and_traces/java/?tab=log4j2
[20]: /es/tracing/other_telemetry/connect_logs_and_traces/nodejs
[21]: /es/tracing/other_telemetry/connect_logs_and_traces/dotnet?tab=serilog
[22]: /es/tracing/other_telemetry/connect_logs_and_traces/php
[23]: /es/tracing/other_telemetry/connect_logs_and_traces/python
[24]: /es/tracing/other_telemetry/connect_logs_and_traces/go
[25]: /es/tracing/other_telemetry/connect_logs_and_traces/ruby
[26]: https://cloud.google.com/sdk/gcloud/reference/run/deploy