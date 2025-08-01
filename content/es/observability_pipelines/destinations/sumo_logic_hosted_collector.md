---
disable_toc: false
title: Destino Sumo Logic Hosted Collector
---

Utiliza el destino Sumo Logic de Observability Pipelines para enviar logs a tu recopilador alojado de Sumo Logic.

## Configuración

Configura el destino Sumo Logic y sus variables de entorno cuando [configures un pipeline][1]. La siguiente información se configura en la interfaz de usuario del pipeline.

### Configurar el destino

{{% observability_pipelines/destination_settings/sumo_logic %}}

### Configurar las variables de entorno

{{% observability_pipelines/configure_existing_pipelines/destination_env_vars/sumo_logic %}}

## Cómo funciona el destino

### Procesamiento de eventos por lotes

Un lote de eventos se descarga cuando se cumple uno de estos parámetros. Consulta [procesamiento de eventos por lotes][2] para obtener más información.

| Eventos máximos     | Bytes máximos       | Tiempo de espera (segundos)   |
|----------------|-----------------|---------------------|
| Ninguno           | 10,000,000      | 1                   |

[1]: https://app.datadoghq.com/observability-pipelines
[2]: /es/observability_pipelines/destinations/#event-batching