---
algolia:
  tags:
  - Flare del Agent
aliases:
- /es/agent/faq/send-logs-and-configs-to-datadog-via-flare-command
further_reading:
- link: /agent/troubleshooting/debug_mode/
  tag: Documentación
  text: Modo de depuración del Agent
- link: /agent/troubleshooting/agent_check_status/
  tag: Documentación
  text: Consultar el estado de un check del Agent
title: Flare del Agent
---

Un flare permite enviar la información necesaria para que el equipo de asistencia de Datadog pueda solucionar tu problema.

Esta página cubre:
- [Envío de un flare utilizando el comando `flare`](#send-a-flare-using-the-flare-command).
- [Envío de un flare desde el sitio de Datadog](#send-a-flare-from-the-Datadog-site), utilizando la configuración remota.
- [Envío manual](#manual-submission).

Un flare reúne todos los archivos de configuración y registros del Agent en un archivo de almacenamiento. Elimina información confidencial, incluyendo contraseñas, claves de API, credenciales proxy y cadenas de comunidad SNMP. Si APM está habilitado, el flare incluye [logs de depuración del rastreador][4] cuando están disponibles.

El Datadog Agent funciona en su totalidad con código abierto, lo que permite [comprobar el comportamiento del código][1]. Si es necesario, puedes revisar el flare antes de enviarlo, ya que este solicita una confirmación antes de subirlo.

Cuando te pongas en contacto con el servicio de asistencia de Datadog con la configuración remota activada para el Agent, el equipo podrá iniciar un flare desde tu entorno para poder ayudarte mejor y de forma más rápida. Los flares proporcionan información de solución de problemas al servicio de asistencia de Datadog para ayudarte a resolver tu problema. 

## Enviar un flare desde el sitio de Datadog 

{{< site-region region="gov" >}}
<div class="alert alert-warning">El envío de un flare del Agent desde Fleet Automation no es compatible con este sitio.</div>
{{< /site-region >}}

Para enviar un flare desde el sitio de Datadog, asegúrate de haber habilitado la [automatización de flotas][2] y la [configuración remota][3] en el Agent.

{{% remote-flare %}}

{{< img src="agent/fleet_automation/fleet_automation_remote_flare.png" alt="El botón Enviar tique lanza un formulario para eviar un flare para un tique de asistencia nuevo o existente" style="width:70%;" >}}

## Envía un flare utilizando el comando `flare` 

Utiliza el subcomando `flare` para enviar un flare. En los comandos siguientes, sustituye `<CASE_ID>` por tu ID de caso de asistencia Datadog, si tienes uno, y luego introduce la dirección de correo electrónico asociada a él.

Si no tienes un ID de caso, introduce la dirección de correo electrónico que utilizaste para iniciar sesión en Datadog para crear un nuevo caso de asistencia.

**Confirma la carga del archivo para enviarlo inmediatamente al servicio de asistencia de Datadog**.

{{< tabs >}}
{{% tab "Agent" %}}

| Plataforma   | Comando                                                 |
|------------|---------------------------------------------------------|
| AIX        | `datadog-agent flare <CASE_ID>`                         |
| Docker     | `docker exec -it dd-agent agent flare <CASE_ID>`        |
| macOS      | `datadog-agent flare <CASE_ID>` o a través de la [web de GUI][1] |
| CentOS     | `sudo datadog-agent flare <CASE_ID>`                    |
| Debian     | `sudo datadog-agent flare <CASE_ID>`                    |
| Kubernetes | `kubectl exec -it <AGENT_POD_NAME> -- agent flare <CASE_ID>`  |
| Fedora     | `sudo datadog-agent flare <CASE_ID>`                    |
| RedHat     | `sudo datadog-agent flare <CASE_ID>`                    |
| Suse       | `sudo datadog-agent flare <CASE_ID>`                    |
| Fuente     | `sudo datadog-agent flare <CASE_ID>`                    |
| Windows    | `& "$env:ProgramFiles\Datadog\Datadog Agent\bin\agent.exe" flare <CASE_ID>`       |
| Heroku     | Consulta la [documentación de Heroku][3] específica.         |
| PCF     | `sudo /var/vcap/jobs/dd-agent/packages/dd-agent/bin/agent/agent flare <CASE_ID>`             |

## Contenedores exclusivos

Cuando utilizas el Agent v7.19 o posterior y el gráfico de Helm de Datadog con la [última versión][4] o un DaemonSet donde el Datadog Agent y Trace Agent están en contenedores separados, se implementa un pod del Agent que contiene:

* Un contenedor con el proceso del Agent (Agent + Log Agent)
* Un contenedor con el proceso del Agent de proceso
* Un contenedor con el proceso del Agent de rastreo
* Un contenedor con el proceso de system-probe

Para obtener un flare de cada contenedor, ejecuta los siguientes comandos:

### Agent

```bash
kubectl exec -it <AGENT_POD_NAME> -c agent -- agent flare <CASE_ID>
```

### Agent de proceso

```bash
kubectl exec -it <AGENT_POD_NAME> -c process-agent -- agent flare <CASE_ID> --local
```

### Trace Agent

```bash
kubectl exec -it <AGENT_POD_NAME> -c trace-agent -- agent flare <CASE_ID> --local
```

### Agent de seguridad

```bash
kubectl exec -it <AGENT_POD_NAME> -c security-agent -- security-agent flare <CASE_ID>
```

### System-probe

El contenedor system-probe no puede enviar un flare, por lo que obtiene logs del contenedor:

```bash
kubectl logs <AGENT_POD_NAME> -c system-probe > system-probe.log
```

## ECS Fargate

Cuando utilizas la plataforma ECS Fargate v1.4.0, puedes configurar tareas y servicios de ECS para permitir el acceso a contenedores Linux en ejecución, habilitando [Amazon ECS Exec][5]. Después de habilitar Amazon ECS Exec, ejecuta el siguiente comando para enviar un flare:

```bash
aws ecs execute-command --cluster <CLUSTER_NAME> \
    --task <TASK_ID> \
    --container datadog-agent \
    --interactive \
    --command "agent flare <CASE_ID>"
```

**Nota:** ECS Exec solo puede habilitarse para tareas nuevas. Hay que volver a crear las tareas existentes para utilizar ECS Exec.

[1]: /es/agent/basic_agent_usage/#gui
[2]: /es/agent/basic_agent_usage/windows/#agent-v6
[3]: /es/agent/guide/heroku-troubleshooting/#send-a-flare
[4]: https://github.com/DataDog/helm-charts/blob/master/charts/datadog/CHANGELOG.md
[5]: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-exec.html
{{% /tab %}}

{{% tab "Cluster Agent" %}}

| Plataforma      | Comando                                                                     |
|---------------|-----------------------------------------------------------------------------|
| Kubernetes    | `kubectl exec -n <NAMESPACE> -it <CLUSTER_POD_NAME> -- datadog-cluster-agent flare <CASE_ID>` |
| Cloud Foundry | `/var/vcap/packages/datadog-cluster-agent/datadog-cluster-agent-cloudfoundry flare -c /var/vcap/jobs/datadog-cluster-agent/config <CASE_ID>` |

{{% /tab %}}
{{< /tabs >}}

## Envío manual

El protocolo de flares del Agent recopila configuraciones y logs en un archivo de almacenamiento ubicado en primer lugar en el directorio local `/tmp`.
Si tienes algún problema con la conectividad del Agent, recupera este archivo de forma manual y envíalo al servicio de asistencia.

### Kubernetes
Para obtener el archivo de almacenamiento en Kubernetes, utiliza el comando kubectl:
```
kubectl cp datadog-<pod-name>:tmp/datadog-agent-<date-of-the-flare>.zip flare.zip -c agent
```

## Referencias adicionales

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://github.com/DataDog/datadog-agent/tree/main/pkg/flare
[2]: /es/agent/fleet_automation/
[3]: /es/agent/remote_config#enabling-remote-configuration
[4]: /es/tracing/troubleshooting/tracer_debug_logs/?code-lang=dotnet#data-collected