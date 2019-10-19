---
title: 为容器配置 Azure Monitor 代理数据收集 |Microsoft Docs
description: 本文介绍如何配置容器代理的 Azure Monitor，以控制 stdout/stderr 和环境变量日志收集。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/08/2019
ms.openlocfilehash: 2b72252c5c85679c1c65fa2dcf9c5acc6c54003c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554203"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>为容器 Azure Monitor 配置代理数据收集

容器 Azure Monitor 用于从容器化代理中收集从容器工作负载部署到 Azure Kubernetes 服务（AKS）上托管的 Kubernetes 群集的 stdout、stderr 和环境变量。 此代理还可以使用容器化代理从 Prometheus 收集时序数据（也称为指标），而无需设置和管理 Prometheus 服务器和数据库。 可以通过创建自定义 Kubernetes ConfigMaps 来配置代理数据收集设置，以控制此体验。 

本文演示如何基于要求创建 ConfigMap 和配置数据收集。

>[!NOTE]
>目前，对 Prometheus 的支持是公共预览版中的一项功能。
>

## <a name="configmap-file-settings-overview"></a>ConfigMap 文件设置概述

提供了一个模板 ConfigMap 文件，可让你轻松地使用自定义项对其进行编辑，而无需从头开始创建。 在开始之前，应查看有关[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)的 Kubernetes 文档，并熟悉如何创建、配置和部署 ConfigMaps。 这将允许你筛选每个命名空间或整个群集中的 stderr 和 stdout，以及在群集中的所有 pod/节点上运行的任何容器的环境变量。

>[!IMPORTANT]
>从容器工作负载收集 stdout、stderr 和环境变量所支持的最低代理版本为 ciprod06142019 或更高版本。 抓取 Prometheus 指标支持的最低代理版本为 ciprod07092019 或更高版本。 若要验证您的代理版本，请从 "**节点**" 选项卡中选择一个节点，然后在 "**代理图像标记**" 属性的 "属性" 窗格中说明值。  

### <a name="data-collection-settings"></a>数据收集设置

下面是可以配置以控制数据收集的设置。

|密钥 |数据类型 |Value |描述 |
|----|----------|------|------------|
|`schema-version` |String （区分大小写） |v1 |这是分析此 ConfigMap 时代理使用的架构版本。 当前支持的架构版本为 v1。 在计算 ConfigMap 时，不支持修改此值，将拒绝此值。|
|`config-version` |字符串 | | 支持在源代码管理系统/存储库中跟踪此配置文件的版本。 允许的最大字符数为10，所有其他字符都将被截断。 |
|`[log_collection_settings.stdout] enabled =` |布尔 | true 或 false | 这会控制是否启用 stdout 容器日志收集。 如果设置为 `true` 并且没有为 stdout 日志收集排除命名空间（`log_collection_settings.stdout.exclude_namespaces` 设置为），则将从群集中的所有 pod/节点上的所有容器中收集 stdout 日志。 如果未在 ConfigMaps 中指定，则默认值为 `enabled = true`。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|字符串 | 逗号分隔的数组 |将不收集其 stdout 日志的 Kubernetes 命名空间的数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true` 时，此设置才有效。 如果未在 ConfigMap 中指定，则默认值为 `exclude_namespaces = ["kube-system"]`。|
|`[log_collection_settings.stderr] enabled =` |布尔 | true 或 false |这会控制是否已启用 stderr 容器日志收集。 如果设置为 `true` 并且没有为 stdout 日志收集（`log_collection_settings.stderr.exclude_namespaces` 设置）排除命名空间，则将从群集中的所有 pod/节点上的所有容器中收集 stderr 日志。 如果未在 ConfigMaps 中指定，则默认值为 `enabled = true`。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |字符串 |逗号分隔的数组 |将不收集 stderr 日志的 Kubernetes 命名空间的数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true` 时，此设置才有效。 如果未在 ConfigMap 中指定，则默认值为 `exclude_namespaces = ["kube-system"]`。 |
| `[log_collection_settings.env_var] enabled =` |布尔 | true 或 false | 此设置控制群集中的所有 pod/节点上的环境变量集合，如果 ConfigMaps 中未指定，则默认为 `enabled = true`。 如果已全局启用了环境变量的集合，则可以通过将环境变量设置 `AZMON_COLLECT_ENV` 为 False，将环境变量设置为**False** ，方法是使用 Dockerfile 设置，[或者在](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) **env：** 部分。 如果已全局禁用环境变量的集合，则无法为特定容器启用收集（也就是说，可在容器级别应用的唯一重写是在全局启用时禁用集合）。 |

### <a name="prometheus-scraping-settings"></a>Prometheus 抓取设置

![Prometheus 的容器监视体系结构](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

容器 Azure Monitor 提供无缝体验，可通过以下表中所示的多个抓取 Prometheus 度量值的集合。 通过在单个 ConfigMap 文件中指定的一组设置收集指标，该文件与用于配置容器工作负荷中的 stdout、stderr 和环境变量的集合的文件相同。 

从 Prometheus 中的抓取度量值是从以下两个角度之一执行的：

* 群集范围内的 HTTP URL，并从列出的服务终结点、k8s 服务（如 kube 和 kube）以及特定于应用程序的 pod 批注发现目标。 将在 ConfigMap 部分 *[Prometheus data_collection_settings]* 中定义在此上下文中收集的指标。
* 整个节点-HTTP URL，并从所列出的服务终结点中发现目标。 将在 ConfigMap 部分 *[Prometheus_data_collection_settings]* 中定义在此上下文中收集的指标。

| 终结点 | 范围 | 示例 |
|----------|-------|---------|
| Pod 批注 | 群集范围 | 批注 <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 服务 | 群集范围 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/端点 | 每节点和/或群集范围 | `http://myurl:9101/metrics` |

如果指定了 URL，则容器 Azure Monitor 仅擦除终结点。 指定 Kubernetes 服务时，服务名称将与群集 DNS 服务器一起解析，以获取 IP 地址，然后将解析的服务擦除。

|范围 | 密钥 | 数据类型 | Value | 描述 |
|------|-----|-----------|-------|-------------|
| 群集范围 | | | | 指定以下三种方法中的任意一种，以擦除指标的终结点。 |
| | `urls` | 字符串 | 逗号分隔的数组 | HTTP 终结点（指定了 IP 地址或有效的 URL 路径）。 例如：`urls=[$NODE_IP/metrics]`。 （$NODE _IP 是容器参数的特定 Azure Monitor，可以使用而不是节点 IP 地址。 必须全部大写。） |
| | `kubernetes_services` | 字符串 | 逗号分隔的数组 | Kubernetes 服务的数组，用于擦除 kube 指标的指标。 例如，`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | 布尔 | true 或 false | 如果设置为 "群集范围内的设置 `true`"，则容器代理的 Azure Monitor 将在整个群集中擦除 Kubernetes pod，以获得以下 Prometheus 批注：<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | 布尔 | true 或 false | 启用抓取的。 `monitor_kubernetes_pods` 必须设置为 `true`。 |
| | `prometheus.io/scheme` | 字符串 | http 或 https | 默认为废弃 over HTTP。 如有必要，将设置为 `https`。 | 
| | `prometheus.io/path` | 字符串 | 逗号分隔的数组 | 要从中提取指标的 HTTP 资源路径。 如果未 `/metrics` 度量值路径，请在此批注中定义它。 |
| | `prometheus.io/port` | 字符串 | 9102 | 指定要从其擦除的端口。 如果未设置端口，则它将默认为9102。 |
| 节点范围 | `urls` | 字符串 | 逗号分隔的数组 | HTTP 终结点（指定了 IP 地址或有效的 URL 路径）。 例如：`urls=[$NODE_IP/metrics]`。 （$NODE _IP 是容器参数的特定 Azure Monitor，可以使用而不是节点 IP 地址。 必须全部大写。） |
| 节点范围或群集范围内的 | `interval` | 字符串 | 60s | 收集间隔默认值为一分钟（60秒）。 可以将 *[prometheus_data_collection_settings]* 和/或 *[prometheus_data_collection_settings]* 的集合修改为时间单位，如 s、m、h。 |
| 节点范围或群集范围内的 | `fieldpass`<br> `fielddrop`| 字符串 | 逗号分隔的数组 | 可以通过设置 "允许（`fieldpass`）" 和 "禁止（`fielddrop`）" 列表来指定要收集或不从终结点收集的某些指标。 必须先设置允许列表。 |

ConfigMaps 是一个全局列表，只能有一个 ConfigMap 应用于代理。 不能将其他 ConfigMaps overruling 到集合中。

## <a name="configure-and-deploy-configmaps"></a>配置和部署 ConfigMaps

执行以下步骤，配置 ConfigMap 配置文件并将其部署到群集。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)模板 ConfigMap yaml 文件并将其另存为容器 azm-agentconfig. yaml。  

2. 编辑包含自定义项的 ConfigMap yaml 文件，以收集 stdout、stderr 和/或环境变量。

    - 若要排除 stdout 日志收集的特定命名空间，可以使用以下示例配置键/值： `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 若要禁用特定容器的环境变量集合，请将键/值 `[log_collection_settings.env_var] enabled = true` 设置为全局启用变量集合，然后按照[此处](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)的步骤完成特定容器的配置。
    
    - 若要禁用 stderr 日志收集群集，可以使用以下示例配置密钥/值： `[log_collection_settings.stderr] enabled = false`。
    
3. 若要在群集范围内配置 Kubernetes 服务的集合，请使用以下示例配置 ConfigMap 文件。

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
    ```

4. 若要从群集中的特定 URL 配置抓取 Prometheus 指标，请使用以下示例配置 ConfigMap 文件。

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
    ```

5. 若要为群集中的每个节点从代理的 DaemonSet 配置抓取 Prometheus 指标，请在 ConfigMap 中配置以下内容：
    
    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings 
    [prometheus_data_collection_settings.node] 
    interval = "1m"  ## Valid time units are s, m, h. 
    urls = ["http://$NODE_IP:9103/metrics"] 
    fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
    fielddrop = ["metric_to_drop"] 
    ```

    >[!NOTE]
    >$NODE _IP 是容器参数的特定 Azure Monitor，可以使用而不是节点 IP 地址。 必须全部大写。 

6. 若要通过指定 pod 批注配置 Prometheus 指标的抓取，请执行以下步骤：

    1. 在 ConfigMap 中，指定以下内容：

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are s, m, h
         monitor_kubernetes_pods = true 
        ```

    2. 为 pod 批注指定以下配置：

        ```
         - prometheus.io/scrape:"true" #Enable scraping for this pod 
         - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
         - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
         - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

7. 通过运行以下 kubectl 命令创建 ConfigMap： `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 
    
    在生效之前，配置更改可能需要几分钟才能完成，并且群集中的所有 omsagent 箱都将重新启动。 重新启动是所有 omsagent pod 的滚动重启，而不是同时重新启动。 重新启动完成后，会显示一条类似于以下内容的消息，其中包括 result： `configmap "container-azm-ms-agentconfig" created`。

若要验证是否已成功应用配置，请使用以下命令从代理 pod 查看日志： `kubectl logs omsagent-fdf58 -n=kube-system`。 如果 omsagent pod 出现配置错误，输出将显示类似于以下内容的错误：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

还可以查看与应用 Prometheus 的配置更改相关的错误。  使用同一个 `kubectl logs` 命令，或从实时日志中的日志中的日志。 实时日志显示类似于以下内容的错误：

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

错误会阻止 omsagent 分析文件，从而导致重新启动并使用默认配置。 更正 ConfigMap 中的错误后，请通过运行以下命令，保存 yaml 文件并应用更新的 ConfigMaps： `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>应用更新的 ConfigMap

如果已将 ConfigMap 部署到群集，并且想要使用较新的配置对其进行更新，则可以编辑以前使用的 ConfigMap 文件，然后使用与之前相同的命令进行应用，`kubectl apply -f <configmap_yaml_file.yaml`。

在生效之前，配置更改可能需要几分钟才能完成，并且群集中的所有 omsagent 箱都将重新启动。 重新启动是所有 omsagent pod 的滚动重启，而不是同时重新启动。 重新启动完成后，会显示一条类似于以下内容的消息，其中包括 result： `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>验证架构版本

支持的配置架构版本在 omsagent pod 上以 pod 注释（架构版本）的形式提供。 可以通过以下 kubectl 命令查看它们： `kubectl describe pod omsagent-fdf58 -n=kube-system`

输出将显示类似于以下内容的注释架构版本：

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="query-prometheus-metrics-data"></a>查询 Prometheus 指标数据

若要按 Azure Monitor 查看 prometheus 指标擦除，请将 "prometheus" 指定为命名空间。 下面是一个示例查询，用于查看 `default` kubernetes 命名空间中的 prometheus 度量值。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

还可以按名称直接查询 Prometheus 数据。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

## <a name="review-prometheus-data-usage"></a>查看 Prometheus 数据使用情况

若要确定每个指标大小的引入量（以 GB 为单位），以了解它是否很高，请提供以下查询。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
输出将显示类似于以下内容的结果：

![数据引入卷的日志查询结果](./media/container-insights-agent-config/log-query-example-usage-03.png)

若要估计每个指标的大小（GB）是一个月来了解工作区中收到的数据引入量是否很高，请提供以下查询。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

输出将显示类似于以下内容的结果：

![数据引入卷的日志查询结果](./media/container-insights-agent-config/log-query-example-usage-02.png)

使用[Azure Monitor 日志管理使用情况和成本](../platform/manage-cost-storage.md)中提供了有关如何监视数据使用情况和分析成本的详细信息。

## <a name="next-steps"></a>后续步骤

容器 Azure Monitor 不包含一组预定义的警报。 查看[使用容器 Azure Monitor 创建性能警报](container-insights-alerts.md)，了解如何为高 CPU 和内存使用率创建建议警报，以支持 DevOps 或操作过程和过程。

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以便对群集进行警报、可视化或分析。
