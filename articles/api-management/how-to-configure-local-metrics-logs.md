---
title: 为 Azure API 管理自承载网关配置本地指标和日志 | Microsoft Docs
description: 了解如何为 Azure API 管理自承载网关配置本地指标和日志
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: ac147863fe54be3343eda653fc863ebd08dac54d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254497"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>为 Azure API 管理自承载网关配置本地指标和日志

本文详细介绍如何为[自承载网关](./self-hosted-gateway-overview.md)配置本地指标和日志。 若要配置云指标和日志，请参阅[本文](how-to-configure-cloud-metrics-logs.md)。 

## <a name="metrics"></a>指标
自承载网关支持 [StatsD](https://github.com/statsd/statsd)，它已成为指标收集和聚合的统一协议。 本部分介绍将 StatsD 部署到 Kubernetes 的完整步骤，包含配置网关以通过 StatsD 发出指标，以及使用 [Prometheus](https://prometheus.io/) 监视指标。 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>将 StatsD 和 Prometheus 部署到群集

下面是一个示例 YAML 配置，用于将 StatsD 和 Prometheus 部署到已部署自承载网关的 Kubernetes 群集。 它还会为每个群集创建一个[服务](https://kubernetes.io/docs/concepts/services-networking/service/)。 自承载网关会将指标发布到 StatsD 服务。 我们将通过 Prometheus 的服务访问其仪表板。   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: prom/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

将配置保存到名为 `metrics.yaml` 的文件中，并使用以下命令将所有内容部署到群集：

```console
kubectl apply -f metrics.yaml
```

部署完成后，运行以下命令以检查 Pod 是否正在运行。 请注意，你的 pod 名称与此不同。 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

运行以下命令来检查服务是否正在运行。 记下 StatsD 服务的 `CLUSTER-IP` 和 `PORT`，稍后需要用到。 可以使用 Prometheus 的 `EXTERNAL-IP` 和 `PORT` 访问其仪表板。

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>配置自承载网关以发出指标

部署了 StatsD 和 Prometheus 后，便可以更新自承载网关的配置，以开始通过 StatsD 发出指标。 可以通过其他选项使用自承载网关部署的 ConfigMap 中的 `telemetry.metrics.local` 密钥来启用或禁用此功能。 下面是可用选项的详细信息：

| 字段  | 默认 | 说明 |
| ------------- | ------------- | ------------- |
| telemetry.metrics.local  | `none` | 通过 StatsD 启用日志记录。 值可以是 `none`、`statsd`。 |
| telemetry.metrics.local.statsd.endpoint  | 不适用 | 指定 StatsD 终结点。 |
| telemetry.metrics.local.statsd.sampling  | 不适用 | 指定指标采样率。 值可以介于 0 和 1。 例如，`0.5`|
| telemetry.metrics.local.statsd.tag-format  | 不适用 | StatsD 导出程序[标记格式](https://github.com/prometheus/statsd_exporter#tagging-extensions)。 值可以是 `none`、`librato`、`dogStatsD`、`influxDB`。 |

下面是示例配置：

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

通过上述配置更新自承载网关部署的 YAML 文件，并使用以下命令应用更改： 

```console
kubectl apply -f <file-name>.yaml
 ```

若要选取最新的配置更改，请使用以下命令重启网关部署：

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>查看指标

现在，我们已部署并配置了所有内容，自承载网关应通过 StatsD 报告指标。 Prometheus 将从 StatsD 中选取指标。 使用 Prometheus 服务的 `EXTERNAL-IP` 和 `PORT` 转到 Prometheus 仪表板。 

通过自承载网关进行一些 API 调用，如果所有内容都已正确配置，则应该能够查看以下指标：

| 指标  | 说明 |
| ------------- | ------------- |
| 请求  | 期间内的 API 请求数 |
| DurationInMS | 从网关收到请求到响应全部发送出去经过的时间（毫秒） |
| BackendDurationInMS | 花在整个后端 IO（连接、发送和接收字节）上的时间（毫秒）  |
| ClientDurationInMS | 花在整个客户端 IO（连接、发送和接收字节）上的时间（毫秒）  |

## <a name="logs"></a>日志

默认情况下，自承载网关会将日志输出到 `stdout` 和 `stderr`。 可以使用以下命令轻松查看日志：

```console
kubectl logs <pod-name>
```

如果自承载网关部署在 Azure Kubernetes 服务中，则可以启用[适用于容器的 Azure Monitor](../azure-monitor/insights/container-insights-overview.md)，以便从工作负载收集 `stdout` 和 `stderr` 并查看 Log Analytics 中的日志。 

自承载网关还支持多种协议，包括 `localsyslog`、`rfc5424` 和 `journal`。 下表汇总了所有支持的选项。 

| 字段  | 默认 | 说明 |
| ------------- | ------------- | ------------- |
| telemetry.logs.std  | `text` | 启用到标准流的日志记录。 值可以是 `none`、`text`、`json` |
| telemetry.logs.local  | `none` | 启用本地日志记录。 值可以是 `none`、`auto`、`localsyslog`、`rfc5424`、`journal`  |
| telemetry.logs.local.localsyslog.endpoint  | 不适用 | 指定 localsyslog 终结点。  |
| telemetry.logs.local.localsyslog.facility  | 不适用 | 指定 localsyslog [设备代码](https://en.wikipedia.org/wiki/Syslog#Facility)。 例如，`7` 
| telemetry.logs.local.rfc5424.endpoint  | 不适用 | 指定 rfc5424 终结点。  |
| telemetry.logs.local.rfc5424.facility  | 不适用 | 指定每个 [rfc5424](https://tools.ietf.org/html/rfc5424) 的设备代码。 例如，`7`  |
| telemetry.logs.local.journal.endpoint  | 不适用 | 指定日志终结点。  |

下面是本地日志记录的示例配置：

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 了解如何[在云中配置和保存日志](how-to-configure-local-metrics-logs.md)
