---
title: 配置用于容器的 Azure Monitor 的代理数据收集 | Microsoft Docs
description: 本主题介绍如何配置用于容器的 Azure Monitor 代理，以控制 stdout/stderr 和环境变量日志收集。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: 2b601825a58fe5739a43df607067acc8d629c5f4
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558896"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>配置用于容器的 Azure Monitor 的代理数据收集

用于容器的 Azure Monitor 通过容器化的代理，从托管在 Azure Kubernetes 服务 (AKS) 上的托管 Kubernetes 群集中部署的容器工作负荷收集 stdout、stderr 和环境变量。 此代理还可以使用容器化代理从 Prometheus 收集时序数据 (也称为指标), 而无需设置和管理 Prometheus 服务器和数据库。 可以创建一个自定义的 Kubernetes ConfigMap 用于控制此体验，以配置代理数据收集设置。 

本文演示如何根据要求创建 ConfigMap 和配置数据收集。

>[!NOTE]
>目前，对 Prometheus 的支持是公共预览版中的一项功能。
>

## <a name="configmap-file-settings-overview"></a>ConfigMap 文件设置概述

我们已提供一个模板 ConfigMap 文件，你可以使用自定义内容轻松编辑此文件，而无需从头开始创建。 在开始之前，请查看有关 [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 的 Kubernetes 文档，并熟悉如何创建、配置和部署 ConfigMap。 这样，就可以按命名空间或者在整个群集中筛选 stderr 和 stdout，以及筛选在所有群集 pod/节点中运行的任何容器的环境变量。

>[!IMPORTANT]
>支持从容器工作负荷收集 stdout、stderr 和环境变量的最低代理版本为 ciprod06142019 或以上。 支持擦除 Prometheus 指标的最低代理版本为 ciprod07092019 或以上。 若要验证代理版本，请在“节点”选项卡中选择一个节点，然后在属性窗格中记下“代理映像标记”属性的值。  

### <a name="data-collection-settings"></a>数据收集设置

下面是可以配置的用于控制数据收集的设置。

|Key |数据类型 |ReplTest1 |描述 |
|----|----------|------|------------|
|`schema-version` |字符串（区分大小写） |v1 |这是代理在分析 ConfigMap 时使用的架构版本。 当前支持的架构版本为 v1。 不支持修改此值，评估 ConfigMap 时会拒绝修改的值。|
|`config-version` |String | | 支持在源代码管理系统/存储库中跟踪此配置文件的版本。 允许的最大字符数为 10，所有其他字符将会截掉。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | true 或 false | 此设置控制是否启用 stdout 容器日志收集。 如果设置为 `true` 且未在 stdout 日志收集中排除任何命名空间（下面的 `log_collection_settings.stdout.exclude_namespaces` 设置），则会从所有群集 pod/节点中的所有容器收集 stdout 日志。 如果未在 ConfigMap 中指定，默认值为 `enabled = true`。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | 逗号分隔的数组 |不收集其 stdout 日志的 Kubernetes 命名空间数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true` 时，此设置才会生效。 如果未在 ConfigMap 中指定，默认值为 `exclude_namespaces = ["kube-system"]`。|
|`[log_collection_settings.stderr] enabled =` |Boolean | true 或 false |此设置控制是否启用 stderr 容器日志收集。 如果设置为 `true` 且未在 stdout 日志收集中排除任何命名空间（`log_collection_settings.stderr.exclude_namespaces` 设置），则会从所有群集 pod/节点中的所有容器收集 stderr 日志。 如果未在 ConfigMap 中指定，默认值为 `enabled = true`。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |逗号分隔的数组 |不收集其 stderr 日志的 Kubernetes 命名空间数组。 仅当 `log_collection_settings.stdout.enabled` 设置为 `true` 时，此设置才会生效。 如果未在 ConfigMap 中指定，默认值为 `exclude_namespaces = ["kube-system"]`。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | true 或 false | 此设置控制是否启用环境变量收集。 如果设置为 `false`，则不会收集所有群集 pod/节点中运行的任何容器的环境变量。 如果未在 ConfigMap 中指定，默认值为 `enabled = true`。 |

### <a name="prometheus-scraping-settings"></a>Prometheus 抓取设置

![Prometheus 的容器监视体系结构](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

容器 Azure Monitor 提供无缝体验, 可通过以下表中所示的多个抓取 Prometheus 度量值的集合。 通过在单个 ConfigMap 文件中指定的一组设置收集指标, 该文件与用于配置容器工作负荷中的 stdout、stderr 和环境变量的集合的文件相同。 

从 Prometheus 中的抓取度量值是从以下两个角度之一执行的:

* 群集范围 - 获取 HTTP URL，并从列出的服务终结点、k8s 服务（例如 kube-dns 和 kube-state-metrics）以及特定于应用程序的 pod 批注发现目标。 将在 ConfigMap 节 *[Prometheus data_collection_settings.cluster]* 中定义此上下文中收集的指标。
* 节点范围 - 获取 HTTP URL，并从列出的服务终结点发现目标。 将在 ConfigMap 节 *[Prometheus_data_collection_settings.node]* 中定义此上下文中收集的指标。

| 终结点 | 范围 | 示例 |
|----------|-------|---------|
| Pod 批注 | 群集范围 | 批注 <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Kubernetes 服务 | 群集范围 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/端点 | 每节点和/或群集范围 | `http://myurl:9101/metrics` |

如果指定了 URL, 则容器 Azure Monitor 仅擦除终结点。 指定 Kubernetes 服务时, 服务名称将与群集 DNS 服务器一起解析, 以获取 IP 地址, 然后将解析的服务擦除。

|范围 | Key | 数据类型 | ReplTest1 | 描述 |
|------|-----|-----------|-------|-------------|
| 群集范围 | | | | 指定以下三种方法中的任何一种，以擦除指标的终结点。 |
| | `urls` | String | 逗号分隔的数组 | HTTP 终结点（指定的 IP 地址或有效的 URL 路径）。 例如：`urls=[$NODE_IP/metrics]`。 （$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 必须全部大写。） |
| | `kubernetes_services` | String | 逗号分隔的数组 | 用于从 kube-state-metrics 擦除指标的 Kubernetes 服务数组。 例如 `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | true 或 false | 如果在群集范围设置中将此项设置为 `true`，则容器代理的 Azure Monitor 将在整个群集中擦除以下 Prometheus 批注的 Kubernetes pod：<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true 或 false | 启用 pod 擦除。 `monitor_kubernetes_pods` 必须设置为 `true`。 |
| | `prometheus.io/scheme` | String | http 或 https | 默认为通过 HTTP 擦除。 如有必要, 将`https`设置为。 | 
| | `prometheus.io/path` | String | 逗号分隔的数组 | 要从中提取指标的 HTTP 资源路径。 如果指标路径不是 `/metrics`，请使用此批注定义它。 |
| | `prometheus.io/port` | String | 9102 | 指定要侦听的端口。 如果未设置端口，则默认为 9102。 |
| 节点范围 | `urls` | String | 逗号分隔的数组 | HTTP 终结点（指定的 IP 地址或有效的 URL 路径）。 例如：`urls=[$NODE_IP/metrics]`。 （$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 必须全部大写。） |
| 节点范围或群集范围 | `interval` | String | 60s | 收集间隔默认为 1 分钟（60 秒）。 可以将 *[prometheus_data_collection_settings]* 和/或 *[prometheus_data_collection_settings]* 的集合修改为时间单位, 例如 ns、us (或Âμs)、ms、s、m、h。 |
| 节点范围或群集范围 | `fieldpass`<br> `fielddrop`| String | 逗号分隔的数组 | 可以通过设置允许 (`fieldpass`) 和禁止 (`fielddrop`) 列表，来指定要从终结点收集或不收集的特定指标。 必须先设置允许列表。 |

ConfigMap 是一个全局列表，只能将一个 ConfigMap 应用到代理。 不能使用推翻收集规则的其他 ConfigMap。

## <a name="configure-and-deploy-configmaps"></a>使用 ConfigMap 进行配置和部署

执行以下步骤，以配置 ConfigMap 配置文件并将其部署到群集。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)模板 ConfigMap yaml 文件，并将其保存为 container-azm-ms-agentconfig.yaml。  
1. 使用自定义内容编辑 ConfigMap yaml 文件。

    - 若要排除特定命名空间的 stdout 日志收集，可以参考以下示例配置键/值：`[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 若要禁用特定容器的环境变量收集，请设置键/值 `[log_collection_settings.env_var] enabled = true` 以全局启用变量收集，然后遵循[此处](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)所述的步骤完成特定容器的配置。
    
    - 若要在群集范围禁用 stderr 日志收集，请参考以下示例配置键/值：`[log_collection_settings.stderr] enabled = false`。
    
    - 下面的示例演示如何从 URL 群集范围、代理的 DameonSet 节点范围以及指定 pod 批注配置 ConfigMap 文件度量值

        - 从群集中的特定 URL 擦除 Prometheus 指标。

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - 在群集中的每个节点上运行的擦除 Prometheus 指标。

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - 通过指定 pod 批注擦除 Prometheus 指标。

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. 运行以下 kubectl 命令创建 ConfigMap：`kubectl apply -f <configmap_yaml_file.yaml>`。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 
    
    配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

若要验证是否已成功应用配置，请使用以下命令查看代理 pod 的日志：`kubectl logs omsagent-fdf58 -n=kube-system`。 如果 omsagent pod 存在配置错误，输出中会显示如下所示的错误：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

还可以查看有关对 Prometheus 应用配置更改的错误。  使用相同的 `kubectl logs` 命令从代理 pod 中的日志收集，或者从实时日志收集。 实时日志显示类似于以下内容的错误:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

错误阻止了 omsagent 分析文件，导致其重启并使用默认配置。 更正 ConfigMap 中的错误后，保存 yaml 文件，并运行以下命令应用已更新的 ConfigMap：`kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>应用已更新的 ConfigMap

如果已将 ConfigMap 部署到群集, 并且想要使用较新的配置对其进行更新, 则可以编辑以前使用的 ConfigMap 文件, 然后使用与之前`kubectl apply -f <configmap_yaml_file.yaml`相同的命令来应用。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>验证架构版本

omsagent pod 上以 pod 注释 (schema-versions) 的形式提供了支持的配置架构版本。 可以使用以下 kubectl 命令查看版本：`kubectl describe pod omsagent-fdf58 -n=kube-system`

输出中会显示如下所示的包含注释架构版本的内容：

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

## <a name="review-prometheus-data-usage"></a>查看 Prometheus 数据使用情况

若要确定每个指标大小 (GB) 的每日引入量以了解其是否偏高，请提供以下查询。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
输出将显示类似于以下内容的结果：

![数据引入量的日志查询结果](./media/container-insights-agent-config/log-query-example-usage-03.png)

若要估算月份中的每个指标大小 (GB) 以了解工作区中收到的引入数据量是否偏高，请提供以下查询。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

输出将显示类似于以下内容的结果：

![数据引入量的日志查询结果](./media/container-insights-agent-config/log-query-example-usage-02.png)

[使用 Azure Monitor 日志管理使用情况和成本](../platform/manage-cost-storage.md)中提供了有关如何监视数据使用情况和分析成本的更多信息。

## <a name="next-steps"></a>后续步骤

用于容器的 Azure Monitor 不包含预定义的警报集。 请查看[使用用于容器的 Azure Monitor 创建性能警报](container-insights-alerts.md)，了解如何针对高 CPU 和内存利用率创建建议的警报以支持 DevOps 或操作流程和过程。

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 请参阅[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。