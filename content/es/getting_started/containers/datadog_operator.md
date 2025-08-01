---
further_reading:
- link: /containers/datadog_operator
  tag: documentación
  text: Datadog Operator
- link: https://github.com/DataDog/datadog-operator/blob/main/docs/installation.md
  tag: Código fuente
  text: 'Datadog Operator: instalación avanzada'
- link: https://github.com/DataDog/datadog-operator/blob/main/docs/configuration.v2alpha1.md
  tag: Código fuente
  text: 'Datadog Operator: configuración'
title: Empezando con el Datadog Operator
---

El [Datadog Operator][1] es un [operador de Kubernetes][2] de código abierto que te permite desplegar y configurar el Datadog Agent en un entorno de Kubernetes. Esta guía te explica cómo debe utilizarse el Operator para desplegar el Datadog Agent.

## Requisitos previos

- Kubernetes v1.20.X o posterior
- [Helm][3] para desplegar el Datadog Operator
- La herramienta de línea de comandos de Kubernetes, kubectl][4], para instalar el Datadog Agent

## Instalación y despliegue

1. Instala el Datadog Operator con Helm:
   ```bash
   helm repo add datadog https://helm.datadoghq.com
   helm install my-datadog-operator datadog/datadog-operator
   ```

2. Crea un secreto de Kubernetes con tu clave de API:
   ```bash
   kubectl create secret generic datadog-secret --from-literal api-key=<DATADOG_API_KEY>
   ```
   Sustituye `<DATADOG_API_KEY>` por tu [clave de API Datadog][5].

   **Nota**: Añade una clave de aplicación para el autoescalado mediante el servidor de métricas externo, añadiendo `--from-literal app-key=<DATADOG_APP_KEY>`.

3. Crea un archivo `datadog-agent.yaml` con las especificaciones de la configuración de despliegue del `DatadogAgent`. La configuración del siguiente ejemplo activa las métricas, los logs y la herramienta APM:
   ```yaml
   apiVersion: datadoghq.com/v2alpha1
   kind: DatadogAgent
   metadata:
     name: datadog
   spec:
     global:
       site: datadoghq.com
       credentials:
         apiSecret:
           secretName: datadog-secret
           keyName: api-key
     features:
       apm:
         enabled: true
       logCollection:
         enabled: true
   ```
   **Nota**: Asegúrate de configurar el `site` con el sitio Datadog que estés utilizando (por ejemplo, `datadoghq.eu`). 

   Para conocer todas las opciones de configuración, consulta la [especificación de configuración del Operator][6].

4. Despliega el Datadog Agent:
   ```bash
   kubectl apply -f /path/to/your/datadog-agent.yaml
   ```

### Ejecutar Agents en un único contenedor

<div class="alert alert-warning">Disponible en Operator v1.4.0 o posterior</div>

De manera predeterminada, Datadog Operator crea un Agent DaemonSet con pods que ejecutan diferentes contenedores del Agent. Datadog Operator v1.4.0 introduce una configuración que permite a los usuarios ejecutar Agents en un único contenedor. Para evitar la elevación de privilegios de todos los Agents en ese contenedor, esto solo se puede hacer cuando `system-probe` o `security-agent` no son obligatorios. Para obtener más información, consulta [Ejecutar como usuario sin privilegios][7] en la página Seguridad de datos del Agent.

Para habilitar esta característica, añade `global.containerStrategy: single` al manifiesto `DatadogAgent`:

{{< highlight yaml "hl_lines=7" >}}
  apiVersion: datadoghq.com/v2alpha1
  kind: DatadogAgent
  metadata:
    name: datadog
  spec:
    global:
      containerStrategy: single
      credentials:
        apiSecret:
          secretName: datadog-secret
          keyName: api-key
    features:
      apm:
        enabled: true
      logCollection:
        enabled: true
{{< /highlight >}}
Con la configuración anterior, los pods del Agent se ejecutan como contenedores individuales con tres procesos del Agent. El valor por defecto de `global.containerStrategy` es `optimized` y ejecuta cada proceso del Agent en un contenedor diferente.

**Nota**: No se recomienda ejecutar varios procesos del Agent en un contenedor único en entornos orquestados como Kubernetes. Los pods que ejecutan varios procesos necesitan que un administrador de procesos gestione sus ciclos de vida, lo cual Kubernetes no puede controlar de manera directa y podría generar incoherencias o conflictos en la administración del ciclo de vida del contenedor.

## Validación

Utiliza `kubectl get daemonset` y `kubectl get pod -owide` para validar tu instalación.

En un clúster con dos nodos worker, deberías ver los pods de Agent que se han creado en cada nodo:

```bash
$ kubectl get daemonset
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
datadog-agent   2         2         2       2            2           <none>          5m30s

$ kubectl get pod -owide
NAME                                         READY   STATUS    RESTARTS   AGE     IP            NODE
agent-datadog-operator-d897fc9b-7wbsf        1/1     Running   0          1h      10.244.2.11   kind-worker
datadog-agent-k26tp                          1/1     Running   0          5m59s   10.244.2.13   kind-worker
datadog-agent-zcxx7                          1/1     Running   0          5m59s   10.244.1.7    kind-worker2
```

## Limpieza

Los siguientes comandos sirven para eliminar todos los recursos de Kubernetes que hemos creado en esta guía:

```bash
kubectl delete datadogagent datadog
helm delete my-datadog-operator
```

## Referencias adicionales

{{< partial name="whats-next/whats-next.html" >}}


[1]: /es/containers/datadog_operator
[2]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[3]: https://helm.sh/
[4]: https://kubernetes.io/docs/tasks/tools/install-kubectl/
[5]: https://app.datadoghq.com/organization-settings/api-keys
[6]: https://github.com/DataDog/datadog-operator/blob/main/docs/configuration.v2alpha1.md
[7]: https://docs.datadoghq.com/es/data_security/agent/#running-as-an-unprivileged-user