# MLFlow

[MLFlow](https://mlflow.org/) is an open source platform to manage the ML lifecycle, including experimentation, reproducibility, deployment, and a central model registry. 

## Introduction

This chart bootstraps a [MLflow](https://mlflow.org/) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

By default, MLflow uses local file system as backend and artifact store. This chart provides integration of Postgres as backend store and AWS S3 as artifact store.

## Installation
You are required to create a Secret that contains Postgres password using the following template:
```yaml
apiVersion: v1
kind: Secret
metadata:
  creationTimestamp: null
  name: postgres-credentials
  namespace: mlflow
stringData:
  password: YourPGPassword
```

To install the chart with the release name `my-release`, clone this repo and:

```console
$ helm install my-release charts/mlflow
```

These commands deploy MLFlow on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

The following tables lists the configurable parameters of this chart and their default values.

| Parameter                            | Description                                                                                                                     | Default                                                 |
|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------|
| `replicaCount`                       | Number of nodes to deploy                                                                                                       | `1`                                                     |
| `image.repository`                   | Image name                                                                                                                      | `jianshen92/mlflow`                                     |
| `image.tag`                          | Image tag                                                                                                                       | `1.13.1`                                                |
| `image.pullPolicy`                   | Image pull policy                                                                                                               | `Always`                                                |
| `backendStore.filepath`              | Using File Storage as backend store, enabling this will disable Postgres                                                        | `nil`                                                   |
| `backendStore.postgres.username`     | Postgres Username                                                                                                               | `mlflow`                                                |
| `backendStore.postgres.host`         | Postgres Hostname                                                                                                               | (Required) your postgres hostname                       |
| `backendStore.postgres.port`         | Postgres Port                                                                                                                   | (Required) your postgres port                           |
| `backendStore.postgres.database`     | Postgres Database                                                                                                               | `mlflow`                                                |
| `backendStore.postgres.secretName`   | Postgres SecretName created (as mentioned above)                                                                                | `postgres-credentials`                                  |
| `defaultArtifactRoot`                | S3 Bucket URI                                                                                                                   | (Required) your s3 bucket URI                           |
| `metrics.enabled`                    | Enable Prometheus endpoint at /metric                                                                                           | `false`                                                 |
| `metrics.metricsStoreDir`            | Local Directory that stores Prometheus Metrics                                                                                  | `prometheus/metrics`                                    |
| `metrics.serviceMonitor.enabled`     | ServiceMonitor Resources using [Prometheus Operator](https://github.com/prometheus-operator/kube-prometheus)                    | `false`                                                 |
| `metrics.serviceMonitor.labels`      | Labels of Service Monitor (Used to select Prometheus within k8)                                                                 | `[]`                                                    |
| `imagePullSecrets`                   | Global Docker registry secret names as an array                                                                                 | `[]` (does not add image pull secrets to deployed pods) |
| `nameOverride`                       | String to partially override common.names.fullname template with a string (will prepend the release name)                       | `nil`                                                   |
| `fullnameOverride`                   | String to fully override common.names.fullname template with a string                                                           | `nil`                                                   |
| `serviceAccount.create`              | Specifies whether a ServiceAccount should be created (Service account is needed to allow connection to AWS)                     | `false`                                                 |
| `serviceAccount.name`                | The name of the ServiceAccount to create                                                                                        | `""`                                                    |
| `serviceAccount.annotations`         | Annotations of the ServiceAccount created                                                                                       | `{}` (evaluated as a template)                          |
| `service.type`                       | Kubernetes Service type                                                                                                         | `ClusterIP`                                             |
| `service.port`                       | Kubernetes Service port                                                                                                         | `80`                                                    |
| `ingress.enabled`                    | Enabling Ingress Resource                                                                                                       | `false`                                                 |
| `ingress.annotations`                | Ingress Annotation to select Ingress Controller                                                                                 | `{}` (evaluated as a template)                          |
| `ingress.hosts`                      | Ingress Hosts                                                                                                                   | `[]`                                                    |
| `ingress.tls`                        | Ingress TLS                                                                                                                     | `[]`                                                    |
| `resources`                          | The [resources] to allocate for the container                                                                                   | `{}`                                                    |
| `autoscaling.enabled`                | Enables Horizonal Pod Autoscaling                                                                                               | `false`                                                 |
| `autoscaling.minReplicas`            | Min Replica for HPA                                                                                                             | `1`                                                     |
| `autoscaling.maxReplicas`            | Max Replica for HPA                                                                                                             | `3`                                                     |
| `autoscaling.targetCPUUtilizationPercentage` | Target CPU Utilization % for HPA                                                                                        | `80`                                                    |
| `podAnnotations`                     | Pod annotations                                                                                                                 | `{}` (evaluated as a template)                          |
| `securityContext`                    | Security Context for annotations                                                                                                | `{}` (evaluated as a template)                          |
| `affinity`                           | Affinity for pod assignment                                                                                                     | `{}` (evaluated as a template)                          |
| `nodeSelector`                       | Node labels for pod assignment                                                                                                  | `{}` (evaluated as a template)                          |
| `tolerations`                        | Tolerations for pod assignment                                                                                                  | `[]` (evaluated as a template)                          |


Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
$ helm install my-release \
  --set metrics.enabled=true charts/mlflow
```

The above command expose Prometheus metric endpoints at /metrics.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```console
$ helm install my-release -f values.yaml charts/mlflow
```

> **Tip**: You can use the default [values.yaml](values.yaml) and add in the required Paramaters

