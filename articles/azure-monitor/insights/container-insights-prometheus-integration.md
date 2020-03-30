---
title: 为容器为"普罗米图斯集成"配置 Azure 监视器 |微软文档
description: 本文介绍如何为容器代理配置 Azure 监视器，以便使用 Kubernetes 群集从 Prometheus 中刮取指标。
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: b774bf042778ca9118a7bc9f051655b200d87659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931426"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>使用 Azure 监视器为容器配置普罗米杜斯指标的刮擦

[普罗米索斯](https://prometheus.io/)是一个流行的开源指标监测解决方案，是[云原生计算基金会的](https://www.cncf.io/)一部分。 容器的 Azure 监视器提供了无缝的载入体验，用于收集 Prometheus 指标。 通常，要使用 Prometheus，您需要使用存储设置和管理 Prometheus 服务器。 通过与 Azure 监视器集成，不需要普罗米苏斯服务器。 您只需通过导出器或窗格（应用程序）公开 Prometheus 指标终结点，并且容器 Azure 监视器的容器化代理可以为您刮掉指标。 

![Prometheus 的容器监视体系结构](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>用于刮取 Prometheus 指标的最小代理版本为 ciprod07092019 或更高版本，表中用于写入配置和代理错误的`KubeMonAgentEvents`代理版本为 ciprod10112019。 有关代理版本和每个版本中包含的内容的详细信息，请参阅[代理发行说明](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)。 若要验证代理版本，请在“节点”选项卡中选择一个节点，然后在属性窗格中记下“代理映像标记”属性的值。********

在以下位置托管的 Kubernetes 群集支持擦除 Prometheus 指标：

- Azure Kubernetes 服务 (AKS)
- Azure 容器实例
- Azure 堆栈或本地
- Azure Red Hat OpenShift

>[!NOTE]
>对于 Azure 红帽 OpenShift，在*开放移位-azure 日志记录*命名空间中创建模板 ConfigMap 文件。 它未配置为主动从代理刮削指标或数据收集。
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Azure 红帽打开移位先决条件

在开始之前，请确认您是 Azure 红帽 OpenShift 群集的客户群集管理员角色的成员，以配置容器化代理和 Prometheus 刮擦设置。 要验证您是*osa-客户管理员*组的成员，请运行以下命令：

``` bash
  oc get groups
```

输出如下所示：

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

如果您是*osa-客户管理员*组的成员，您应该能够使用以下命令列出`container-azm-ms-agentconfig`ConfigMap：

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

输出如下所示：

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Prometheus 擦除设置

从以下两个角度之一执行从 Prometheus 主动擦除指标的操作：

* 群集范围 - HTTP URL，并从服务列出的终结点发现目标。 例如，k8s 服务（如 kube-dns 和 kube 状态度量）以及特定于应用程序的 pod 注释。 将在 ConfigMap 节 *[Prometheus data_collection_settings.cluster]* 中定义此上下文中收集的指标。
* 节点范围 - 获取 HTTP URL，并从列出的服务终结点发现目标。 将在 ConfigMap 节 *[Prometheus_data_collection_settings.node]* 中定义此上下文中收集的指标。

| 终结点 | 范围 | 示例 |
|----------|-------|---------|
| Pod 批注 | 群集范围 | 批注： <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 服务 | 群集范围 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/终结点 | 单节点和/或群集范围 | `http://myurl:9101/metrics` |

指定 URL 后，用于容器的 Azure Monitor 仅擦除此终结点。 指定 Kubernetes 服务后，将使用群集 DNS 服务器来解析服务名称以获取 IP 地址，然后擦除已解析的服务。

|范围 | 键 | 数据类型 | “值” | 描述 |
|------|-----|-----------|-------|-------------|
| 群集范围 | | | | 指定以下三种方法中的任何一种，以擦除指标的终结点。 |
| | `urls` | String | 逗号分隔的数组 | HTTP 终结点（指定的 IP 地址或有效的 URL 路径）。 例如：`urls=[$NODE_IP/metrics]`。 （$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 必须全部大写。） |
| | `kubernetes_services` | String | 逗号分隔的数组 | 用于从 kube-state-metrics 擦除指标的 Kubernetes 服务数组。 例如：`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | True 或 False | 如果在群集范围设置中将此项设置为 `true`，则容器代理的 Azure Monitor 将在整个群集中擦除以下 Prometheus 批注的 Kubernetes pod：<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | True 或 False | 启用 pod 擦除。 `monitor_kubernetes_pods` 必须设置为 `true`。 |
| | `prometheus.io/scheme` | String | http 或 https | 默认为通过 HTTP 擦除。 必要时设置为 `https`。 | 
| | `prometheus.io/path` | String | 逗号分隔的数组 | 要从中提取指标的 HTTP 资源路径。 如果指标路径不是 `/metrics`，请使用此批注定义它。 |
| | `prometheus.io/port` | String | 9102 | 指定要从其擦除的端口。 如果未设置端口，则默认为 9102。 |
| | `monitor_kubernetes_pods_namespaces` | String | 逗号分隔的数组 | 允许命名空间列表从 Kubernets 窗格中刮取指标。<br> 例如： `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 节点范围 | `urls` | String | 逗号分隔的数组 | HTTP 终结点（指定的 IP 地址或有效的 URL 路径）。 例如：`urls=[$NODE_IP/metrics]`。 （$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 必须全部大写。） |
| 节点范围或群集范围 | `interval` | String | 60s | 收集间隔默认为 1 分钟（60 秒）。 可将 *[prometheus_data_collection_settings.node]* 和/或 *[prometheus_data_collection_settings.cluster]* 的收集间隔设置为 s、m、h 等时间单位。 |
| 节点范围或群集范围 | `fieldpass`<br> `fielddrop`| String | 逗号分隔的数组 | 可以通过设置允许 (`fieldpass`) 和禁止 (`fielddrop`) 列表，来指定要从终结点收集或不收集的特定指标。 必须先设置允许列表。 |

ConfigMap 是一个全局列表，只能将一个 ConfigMap 应用到代理。 不能使用推翻收集规则的其他 ConfigMap。

## <a name="configure-and-deploy-configmaps"></a>使用 ConfigMap 进行配置和部署

执行以下步骤，为库伯奈斯群集配置 ConfigMap 配置文件。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)模板 ConfigMap yaml 文件，并将其保存为 container-azm-ms-agentconfig.yaml。

   >[!NOTE]
   >使用 Azure 红帽 OpenShift 时不需要此步骤，因为群集上已存在 ConfigMap 模板。

2. 使用自定义项编辑 ConfigMap yaml 文件以刮取 Prometheus 指标。 如果要为 Azure 红帽 OpenShift 编辑 ConfigMap yaml 文件，`oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`则首先运行命令以在文本编辑器中打开该文件。

    >[!NOTE]
    >必须在`openshift.io/reconcile-protect: "true"`*容器-azm-ms-代理 ConfigMap*的元数据下添加以下注释，以防止对帐。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 要收集库伯奈斯跨群集服务，请使用以下示例配置 ConfigMap 文件。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 若要跨群集配置从特定 URL 擦除 Prometheus 指标的操作，请使用以下示例来配置 ConfigMap 文件。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 对于群集中的每个单独的节点，若要配置从代理的 DaemonSet 擦除 Prometheus 指标的操作，请在 ConfigMap 中配置以下内容：
    
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
        >$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 它必须是所有大写。 

    - 若要通过指定 Pod 注释来配置擦除 Prometheus 指标的操作，请执行以下步骤：

       1. 在 ConfigMap 中指定以下项：

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. 为 Pod 注释指定以下配置：

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果要将监视限制为具有注释的窗格的特定命名空间，例如仅包括专用于生产工作负载的窗格，请在 ConfigMap 中设置`monitor_kubernetes_pod`到`true`，并添加指定要刮取的命名空间筛选器`monitor_kubernetes_pods_namespaces`。 例如： `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. 对于 Azure 红帽 OpenShift 以外的群集，运行以下库布克`kubectl apply -f <configmap_yaml_file.yaml>`特尔命令： 。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 

    对于 Azure 红帽 OpenShift，请将更改保存在编辑器中。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

您可以通过运行命令 来查看 Azure 红帽 OpenShift 的更新配置映射。 `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 

## <a name="applying-updated-configmap"></a>应用已更新的 ConfigMap

如果已向群集部署了 ConfigMap，并且希望使用较新的配置更新它，则可以编辑以前使用的 ConfigMap 文件，然后使用与以前相同的命令应用。

对于 Azure 红帽 OpenShift 以外的库伯内斯群集，运行`kubectl apply -f <configmap_yaml_file.yaml`命令 。 

对于 Azure 红帽 OpenShift 群集，`oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`运行命令，在默认编辑器中打开文件以修改并保存该文件。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" updated`。

## <a name="verify-configuration"></a>验证配置

要验证配置已成功应用于群集，请使用以下命令查看来自代理窗格的日志： `kubectl logs omsagent-fdf58 -n=kube-system`。 

>[!NOTE]
>此命令不适用于 Azure 红帽 OpenShift 群集。
> 

如果 omsagent pod 存在配置错误，输出中会显示如下所示的错误：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

还可以查看有关应用配置更改的错误。 以下选项可用于对配置更改和报废 Prometheus 指标执行其他故障排除：

- 使用同`kubectl logs`一命令的代理 pod 日志 
    >[!NOTE]
    >此命令不适用于 Azure 红帽 OpenShift 群集。
    > 

- 从实时数据（预览）。 实时数据（预览）日志显示的错误类似于以下内容：

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- 从 Log Analytics 工作区中的 **KubeMonAgentEvents** 表。 每小时发送一次数据，对刮擦错误和配置错误*的错误*严重性进行*警告*严重性。 如果没有错误，表中的条目将包含严重性为“信息”** 的数据，这些数据不会报告错误。 **"标记"** 属性包含有关发生错误的 Pod 和容器 ID 的详细信息，以及发生错误的第一次发生、最后一次发生和计数。

- 对于 Azure 红帽 OpenShift，通过搜索**容器日志**表来检查 omsagent 日志，以验证是否启用了打开 Shift-azure 日志记录的日志集合。

错误阻止了 omsagent 分析文件，导致其重启并使用默认配置。 在 Azure 红帽 OpenShift 以外的群集上更正 ConfigMap 中的错误后，请保存 yaml 文件，并通过运行命令应用于更新的 ConfigMap。 `kubectl apply -f <configmap_yaml_file.yaml` 

对于 Azure 红帽 OpenShift，请通过运行命令：编辑并保存更新的`oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`ConfigMaps。

## <a name="query-prometheus-metrics-data"></a>查询 Prometheus 指标数据

要查看 Azure 监视器刮擦的普罗米图斯指标以及代理报告的任何配置/刮擦错误，请查看[查询 Prometheus 指标数据和](container-insights-log-search.md#query-prometheus-metrics-data)[查询配置或刮擦错误](container-insights-log-search.md#query-config-or-scraping-errors)。

## <a name="view-prometheus-metrics-in-grafana"></a>查看格拉法纳的普罗米苏斯指标

用于容器的 Azure Monitor 支持在 Grafana 仪表板中查看 Log Analytics 工作区中存储的度量值。 我们提供了一个模板。你可以从 Grafana 的[仪表板存储库](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)下载该模板来开始操作，并可通过该模板来了解如何从受监视的群集查询更多数据，以便在自定义 Grafana 仪表板中进行可视化。 

## <a name="review-prometheus-data-usage"></a>查看 Prometheus 数据使用情况

若要确定每个指标大小 (GB) 的每日引入量以了解其是否偏高，请提供以下查询。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
输出将显示类似于以下内容的结果：

![数据引入量的日志查询结果](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

![数据引入量的日志查询结果](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

[使用 Azure Monitor 日志管理使用情况和成本](../platform/manage-cost-storage.md)中提供了有关如何监视数据使用情况和分析成本的更多信息。

## <a name="next-steps"></a>后续步骤

在此处了解有关配置容器工作负载中抖出、粘稳和环境变量的代理集合设置[的更多内容](container-insights-agent-config.md)。 
