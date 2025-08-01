---
aliases:
- /es/tracing/compatibility_requirements/php
- /es/tracing/setup_overview/compatibility_requirements/php
code_lang: php
code_lang_weight: 50
description: 'Requisitos de compatibilidad del rastreador de PHP '
further_reading:
- link: tracing/trace_collection/dd_libraries/php
  tag: Documentación
  text: Instrumentar tu aplicación
title: Requisitos de compatibilidad de PHP
type: multi-code-lang
---
<div class="alert alert-info">Esta documentación es para el rastreador de PHP v1.x. Si buscas la documentación del rastreador de PHP v0.x, consulta la documentación de <a href="/tracing/trace_collection/compatibility/php_v0/">Requisitos de compatibilidad de PHP</a> heredada.</div>

## Política de soporte técnico de tiempo de ejecución de PHP APM

La biblioteca de PHP Datadog Trace es de código abierto - consulta el [repositorio GitHub][1] para obtener más información.

Datadog APM para PHP se basa en dependencias definidas en versiones específicas del sistema operativo host, el tiempo de ejecución de PHP,
determinadas bibliotecas de PHP y el Datadog Agent o API.
Cuando estas versiones dejan de tener soporte técnico de sus encargados de mantenimiento, Datadog APM para PHP limita su soporte técnico a estas también.

#### Niveles de soporte técnico

| **Nivel**                                              | **Asistencia prestada**                                                                                                                         |
|--------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| <span id="support-unsupported">Sin soporte</span>      | Sin implementación. [Ponte en contacto con nuestro equipo de atención al cliente para solicitudes especiales][2].                                                              |
| <span id="support-beta">Vista previa</span>                 | Implementación inicial. Puede que aún no contenga todas las funciones. El soporte técnico para nuevas funciones, correcciones de errores y de seguridad se proporciona en el mejor de los casos. |
| <span id="support-ga">Disponibilidad general (GA)</span> | Implementación completa de todas las funciones. Soporte técnico completo para nuevas funciones, correcciones de errores y de seguridad.                                                 |
| <span id="support-maintenance">Mantenimiento</span>      | Implementación completa de las funciones existentes. No recibe nuevas funciones. Soporte técnico solo para correcciones de errores y seguridad.                            |
| <span id="support-legacy">Heredado</span>                | Implementación heredada. Puede tener función limitada, pero no se proporciona mantenimiento. [Ponte en contacto con el equipo de asistencia][2] para solicitudes especiales.           |
| <span id="support-eol">Final de servicio (EOL)</span>        | Sin soporte técnico. La versión todavía se puede utilizar, pero no se proporcionan correcciones de errores.                                                                     |


PHP APM admite las siguientes versiones de PHP (ZTS y NTS):

<div class="alert alert-info">
<strong>Nota:</strong>
PHP versión 5.x no es compatible a partir de la versión 1.0.0. Si utilizas PHP 5, puedes seguir utilizando el rastreador de PHP hasta la versión <a href="https://github.com/Datadog/dd-rastrear-PHP/releases/etiquetar/0.99.0">0.99</a>.
<br>
Si estás utilizando la versión PHP 5.x en tu aplicación y tienes una solicitud de función que es crítica para tus necesidades de negocio, ponte en contacto con el <a href="https://www.datadoghq.com/support/">servicio de soporte técnico de Datadog </a>.
<br>
Se recomienda utilizar <a href="https://www.PHP.net/supported-versions">versiones con soporte técnico oficial</a> de PHP, especialmente 8.0+.
</div>

| PHP Versión    | Nivel de soporte técnico        | Paquete versión |
|:---------------|:---------------------|:----------------|
| 8.4.x          | Disponibilidad general | > `1.5.0+`     |
| 8.3.x          | Disponibilidad general | > `0.93.0+`     |
| 8.2.x          | Disponibilidad general | anterior a `0.82.0 o posterior`     |
| 8.1.x          | Disponibilidad general | anterior a `0.66.0 o posterior`     |
| 8.0.x          | Disponibilidad general | anterior a `0.52.0 o posterior`     |
| 7.4.x          | Disponibilidad general | Todas             |
| 7.3.x          | Disponibilidad general | Todas             |
| 7.2.x          | Disponibilidad general | Todas             |
| 7.1.x          | Disponibilidad general | Todas             |
| 7.0.x          | Disponibilidad general | Todas             |
| 5.6.x          | EOL                  | < `1.0.0`      |
| 5.5.x          | EOL                  | < `1.0.0`      |
| 5.4.x          | EOL                  | < `1.0.0`      |

PHP APM admite los siguientes SAPI:

| SAPI           | Tipo de compatibilidad               |
|:---------------|:---------------------------|
| apache2handler | Todas las versiones compatibles de PHP  |
| cli            | Todas las versiones compatibles de PHP  |
| fpm-fcgi       | Todas las versiones compatibles de PHP  |
| cgi-fcgi       | Todas las versiones compatibles de PHP  |
| FrankenPHP     | Todas las versiones compatibles de PHP  |


## Arquitecturas de procesador compatibles

PHP APM es compatible con las siguientes arquitecturas:

| Arquitecturas de procesadores                 | Nivel de compatibilidad     | Versión del paquete | Tipo de soporte técnico               |
|-----------------------------------------|-------------------|---------------|----------------------------|
| Linux GNU amd64 (`x86-64-linux-gnu`)    | [GA](#support-ga) | Todas           | Todas las versiones compatibles de PHP  |
| Linux MUSL amd64 (`x86-64-linux-musl`)  | [GA](#support-ga) | Todas           | Todas las versiones compatibles de PHP  |
| Linux GNU arm64 (`aarch64-linux-gnu`)   | [GA](#support-ga) | > `0.78.0`    | Todas las versiones compatibles de PHP  |
| Linux MUSL arm64 (`aarch64-linux-musl`) | [GA](#support-ga) | > `0.78.0`    | Todas las versiones compatibles de PHP  |
| Windows amd64 (`x86_64-windows`)        | [GA](#support-ga) | > `0.98.0`    | PHP 7.2+                   |

### Integraciones

#### Compatibilidad con marcos web

Por defecto, Datadog **soporta todos los marcos web de PHP** desde el primer momento, ya sea con instrumentación al nivel del marco o con rastreo web genérico.

La instrumentación al nivel del marco incluye el rastreo de métodos internos y el etiquetado de marcos específicos.

El rastreo web genérico incluye un tramo (span) `web.request` para rastrear la latencia y los errores que se originan en la llamada, además de tramos para las bibliotecas con soporte técnico - por ejemplo: base de datos y clientes HTTP.

En la siguiente tabla se enumeran algunos de los marcos y versiones que Datadog rastrea con éxito.

**Marcos web**:

| Módulo         | Versiones                                | Tipo de soporte técnico                | Nivel de instrumentación           |
|:---------------|:----------------------------------------|:----------------------------|:--------------------------------|
| CakePHP        | 2.x, 3.x, 4.x, 5.x                      | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| CodeIgniter    | 2.x                                     | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| CodeIgniter    | 3.x                                     | Todas las versiones compatibles de PHP   | Rastreo web genérico             |
| Drupal         |                                         | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| FuelPHP        | 1.1                                     | Todas las versiones compatibles de PHP   | Rastreo web genérico             |
| Laminas        |                                         | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Laravel        | 4.2, 5.x, 6.x                           | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Laravel 8+     | 8.x, 9.x, 10.x, 11.x (rastreador `0.52.0+`) | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Lumen          | 5.2+                                    | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Magento        | 1                                       | Todas las versiones compatibles de PHP   | Rastreo web genérico             |
| Magento        | 2                                       | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Neos Flow      | 1.1                                     | Todas las versiones compatibles de PHP   | Rastreo web genérico             |
| Phalcon        | 1.3, 3.4                                | Todas las versiones compatibles de PHP   | Rastreo web genérico             |
| RoadRunner     | {{< tooltip text="SDK" tooltip="RoadRunner utiliza dos esquemas de versiones: versión semántica (3.x) para el componente de SDK y versión de calendario (2024.x) para el servidor. Con fines de compatibilidad, consulta la versión de SDK." >}} 2.x, 3.x                           | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Slim           | 2.x, 3.x, 4.x                           | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Symfony        | 2.x, 3.3, 3.4, 4.x, 5.x, 6.x, 7.x       | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| WordPress      | 4.x, 5.x, 6.x                           | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Yii            | 2.0                                     | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Zend Framework | 1.12, 1.21                              | Todas las versiones compatibles de PHP   | Instrumentación al nivel del marco |
| Zend Framework | 2.x                                     | Todas las versiones compatibles de PHP   | Rastreo web genérico             |

Ten en cuenta que aunque no veas tu marco web en esta lista, es compatible con la última versión del rastreador.


Datadog está añadiendo continuamente más soporte técnico para el rastreo en profundidad de marcos web de PHP. Para solicitar soporte técnico para metadatos de tramos adicionales e información interna del marco, ponte en contacto con nuestro impresionante [equipo de soporte][3].

#### Compatibilidad con biblioteca CLI 

El rastreo del CLI SAPI está activado en forma predeterminada. Para desactivar selectivamente el rastreo de los scripts CLI de PHP, configura `DD_TRACE_CLI_ENABLED=false`.

| Módulo          | Versiones            | Tipo de soporte técnico               |
|:----------------|:--------------------|:---------------------------|
| Consola CakePHP | 2.x, 3.x            | Todas las versiones compatibles de PHP  |
| Laravel Artisan | 5.x, 8.x, 9.x, 10.x | Todas las versiones compatibles de PHP  |
| Symfony CLI     | 4.x, 5.x, 6.x, 7.x  | Todas las versiones compatibles de PHP  |

Para solicitar soporte técnico para bibliotecas CLI adicionales, ponte en contacto con nuestro magnífico [equipo de soporte][3].

#### Compatibilidad con almacenes de datos

| Módulo                                                                  | Versiones                   | Tipo de soporte técnico               |
|-------------------------------------------------------------------------|----------------------------|----------------------------|
| Amazon RDS (utilizando PDO o MySQLi)                                        | *(Cualquier PHP compatible)*      | Todas las versiones compatibles de PHP  |
| Elasticsearch                                                           | 1+                         | Todas las versiones compatibles de PHP  |
| Eloquent                                                                | Versiones de Laravel compatibles | Todas las versiones compatibles de PHP  |
| Colas Laravel                                                          | Versiones de Laravel compatibles | Todas las versiones compatibles de PHP  |
| Memcache                                                                | *(Cualquier PHP compatible)*      | Todas las versiones compatibles de PHP  |
| Memcached                                                               | *(Cualquier PHP compatible)*      | Todas las versiones compatibles de PHP  |
| MongoDB - a través de la extensión [mongo][4]                                      | 1.4.x                      | Todas las versiones compatibles de PHP  |
| MySQLi                                                                  | *(Cualquier PHP compatible)*      | Todas las versiones compatibles de PHP  |
| PDO                                                                     | *(Cualquier PHP compatible)*      | Todas las versiones compatibles de PHP  |
| PhpRedis                                                                | 3, 4, 5                    | Todas las versiones compatibles de PHP  |
| Predis                                                                  | 1.1                        | Todas las versiones compatibles de PHP  |
| SQLSRV                                                                  | *(Cualquier PHP compatible)*      | Todas las versiones compatibles de PHP  |

Para solicitar soporte técnico para almacenes de datos adicionales, ponte en contacto con nuestro impresionante [equipo de asistencia][3].

#### Compatibilidad de la biblioteca

| Módulo            | Versiones                   | Tipo de soporte técnico               |
|:------------------|:---------------------------|:---------------------------|
| [php-amqplib][10] | 2.x, 3.x                   | PHP 7.1+                   |
| Curl              | *(Cualquier PHP compatible)*      | Todas las versiones compatibles de PHP  |
| Guzzle            | 5.x, 6.x, 7.x              | Todas las versiones compatibles de PHP  |
| Cola de Laravel     | Versiones de Laravel compatibles | Todas las versiones compatibles de PHP  |
| [OpenAI][11]      | Versiones compatibles con OpenAI  | Todas las versiones compatibles de PHP  |
| Symfony Messenger | 4.4, 5.x, 6.x, 7.x         | Todas las versiones compatibles de PHP  |


Para solicitar soporte técnico para bibliotecas adicionales, ponte en contacto con nuestro magnífico [equipo de asistencia][3].

### PCNTL

Datadog es compatible con el rastreo de procesos de bifurcaciones utilizando [pcntl][7]. Cuando se detecta una llamada a `pcntl_fork`, se crea un tramo dedicado y se instrumenta el proceso de bifurcación. Esto puede desactivarse con `DD_TRACE_FORKED_PROCESS`. Consulta la [página de configuración de bibliotecas][9] para obtener más información.

Si la aplicación invoca `pcntl_unshare(CLONE_NEWUSER);` y el rastreador está instalado, la aplicación se bloquea fatalmente. Esto ocurre porque `unshare` con `CLONE_NEWUSER` requiere que el proceso [no sea encadenado][8], mientras que el rastreador PHP utiliza un subproceso separado para enviar trazas (traces) al Datadog Agent sin bloquear el proceso principal.

## Para leer más

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://github.com/DataDog/dd-trace-php
[2]: https://www.datadoghq.com/support/
[3]: /es/help
[4]: https://pecl.php.net/package/mongo
[7]: https://www.php.net/manual/en/book.pcntl.php
[8]: https://man7.org/linux/man-pages/man2/unshare.2.html
[9]: /es/tracing/trace_collection/library_config/php/#environment-variable-configuration
[10]: https://github.com/php-amqplib/php-amqplib
[11]: https://github.com/openai-php/client