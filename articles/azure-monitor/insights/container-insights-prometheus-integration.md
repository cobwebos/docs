---
title: 配置 Prometheus 集成的容器 Azure Monitor |Microsoft Docs
description: 本文介绍如何配置容器代理的 Azure Monitor，以擦除 Prometheus 与 Kubernetes 群集的指标。
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: f5a9b364bc3e51307bd44d8338485f482bda6e1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971366"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>使用用于容器的 Azure Monitor 配置 Prometheus 指标的抓取

[Prometheus](https://prometheus.io/) 是一种常用的开源指标监视解决方案，属于 [云本机计算基础](https://www.cncf.io/)。 容器 Azure Monitor 提供无缝载入体验来收集 Prometheus 指标。 通常，若要使用 Prometheus，需要使用存储设置和管理 Prometheus 服务器。 与 Azure Monitor 集成后，不需要 Prometheus 服务器。 你只需通过导出程序或 pod (应用程序) 公开 Prometheus 指标终结点，容器的容器化 Azure Monitor 代理可为你擦除这些指标。 

![Prometheus 的容器监视体系结构](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>抓取 Prometheus 指标支持的最低代理版本为 ciprod07092019 或更高版本，并且在表中编写配置和代理错误所支持的代理版本 `KubeMonAgentEvents` 为 ciprod10112019。 对于 Azure Red Hat OpenShift 和 Red Hat OpenShift v4，代理版本 ciprod04162020 或更高版本。 
>
>有关代理版本和每个版本中包含的内容的详细信息，请参阅 [代理发行说明](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)。 
>若要验证代理版本，请在“节点”选项卡中选择一个节点，然后在属性窗格中记下“代理映像标记”属性的值。

在上托管的 Kubernetes 群集支持抓取的 Prometheus 指标：

- Azure Kubernetes 服务 (AKS)
- Azure Stack 或本地
- Azure Red Hat OpenShift 版本3。x
- Azure Red Hat OpenShift 和 Red Hat OpenShift 版本4。x

### <a name="prometheus-scraping-settings"></a>Prometheus 擦除设置

从以下两个角度之一执行从 Prometheus 主动擦除指标的操作：

* 群集范围内的 HTTP URL，并从所列出的服务终结点中发现目标。 例如，k8s services （如 kube）和 kube，以及特定于应用程序的 pod 注释。 将在 ConfigMap 节 *[Prometheus data_collection_settings.cluster]* 中定义此上下文中收集的指标。
* 节点范围 - 获取 HTTP URL，并从列出的服务终结点发现目标。 将在 ConfigMap 节 *[Prometheus_data_collection_settings.node]* 中定义此上下文中收集的指标。

| 端点 | 范围 | 示例 |
|----------|-------|---------|
| Pod 批注 | 群集范围 | 批注： <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 服务 | 群集范围 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/终结点 | 单节点和/或群集范围 | `http://myurl:9101/metrics` |

指定 URL 后，用于容器的 Azure Monitor 仅擦除此终结点。 指定 Kubernetes 服务后，将使用群集 DNS 服务器来解析服务名称以获取 IP 地址，然后擦除已解析的服务。

|范围 | 密钥 | 数据类型 | 值 | 说明 |
|------|-----|-----------|-------|-------------|
| 群集范围 | | | | 指定以下三种方法中的任何一种，以擦除指标的终结点。 |
| | `urls` | String | 逗号分隔的数组 | HTTP 终结点（指定的 IP 地址或有效的 URL 路径）。 例如： `urls=[$NODE_IP/metrics]`。 （$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 必须全部大写。） |
| | `kubernetes_services` | String | 逗号分隔的数组 | 用于从 kube-state-metrics 擦除指标的 Kubernetes 服务数组。 例如：`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | 布尔 | True 或 False | 如果在群集范围设置中将此项设置为 `true`，则容器代理的 Azure Monitor 将在整个群集中擦除以下 Prometheus 批注的 Kubernetes pod：<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | 布尔 | True 或 False | 启用 pod 擦除。 `monitor_kubernetes_pods` 必须设置为 `true`。 |
| | `prometheus.io/scheme` | 字符串 | http 或 https | 默认为通过 HTTP 擦除。 必要时设置为 `https`。 | 
| | `prometheus.io/path` | String | 逗号分隔的数组 | 要从中提取指标的 HTTP 资源路径。 如果指标路径不是 `/metrics`，请使用此批注定义它。 |
| | `prometheus.io/port` | 字符串 | 9102 | 指定要从其擦除的端口。 如果未设置端口，则默认为 9102。 |
| | `monitor_kubernetes_pods_namespaces` | String | 逗号分隔的数组 | 允许从 Kubernetes pod 擦除指标的命名空间列表。<br> 例如 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 节点范围 | `urls` | String | 逗号分隔的数组 | HTTP 终结点（指定的 IP 地址或有效的 URL 路径）。 例如： `urls=[$NODE_IP/metrics]`。 （$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 必须全部大写。） |
| 节点范围或群集范围 | `interval` | 字符串 | 60s | 收集间隔默认为 1 分钟（60 秒）。 可将 *[prometheus_data_collection_settings.node]* 和/或 *[prometheus_data_collection_settings.cluster]* 的收集间隔设置为 s、m、h 等时间单位。 |
| 节点范围或群集范围 | `fieldpass`<br> `fielddrop`| String | 逗号分隔的数组 | 可以通过设置允许 (`fieldpass`) 和禁止 (`fielddrop`) 列表，来指定要从终结点收集或不收集的特定指标。 必须先设置允许列表。 |

ConfigMap 是一个全局列表，只能将一个 ConfigMap 应用到代理。 不能使用推翻收集规则的其他 ConfigMap。

## <a name="configure-and-deploy-configmaps"></a>使用 ConfigMap 进行配置和部署

执行以下步骤，为以下群集配置 ConfigMap 配置文件：

* Azure Kubernetes 服务 (AKS)
* Azure Stack 或本地
* Azure Red Hat OpenShift 版本4.x 和 Red Hat OpenShift 版本4。x

1. [下载](https://aka.ms/container-azm-ms-agentconfig)模板 ConfigMap yaml 文件，并将其保存为 container-azm-ms-agentconfig.yaml。

   >[!NOTE]
   >使用 Azure Red Hat OpenShift 时，此步骤不是必需的，因为群集中已存在 ConfigMap 模板。

2. 编辑 ConfigMap yaml 文件，并将自定义设置为擦除 Prometheus 指标。

    >[!NOTE]
    >如果正在编辑 ConfigMap yaml file for Azure Red Hat OpenShift，请首先运行命令， `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 在文本编辑器中打开该文件。

    >[!NOTE]
    >`openshift.io/reconcile-protect: "true"`必须在*azm agentconfig* ConfigMap 的元数据下添加以下批注，以防止对帐。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 若要在群集范围内收集 Kubernetes 服务，请使用以下示例配置 ConfigMap 文件。

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
        >$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 它必须全部大写。 

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
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果要将监视限制为具有批注的 pod 的特定命名空间，例如仅包含专用于生产工作负荷的 pod，请将设置 `monitor_kubernetes_pod` 为， `true` 并将指定命名空间的命名空间筛选器添加到 `monitor_kubernetes_pods_namespaces` 要擦除的 ConfigMap。 例如 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. 运行以下 kubectl 命令： `kubectl apply -f <configmap_yaml_file.yaml>` 。
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>配置和部署 ConfigMaps-Azure Red Hat OpenShift v3

本部分包括成功为 Azure Red Hat OpenShift v3 群集配置 ConfigMap 配置文件的要求和步骤。

>[!NOTE]
>对于 Azure Red Hat OpenShift v3. x，会在 *OpenShift-* ConfigMap 命名空间中创建一个模板文件。 它未配置为主动擦除指标或从代理收集数据。

### <a name="prerequisites"></a>必备条件

在开始之前，请确认你是 Azure Red Hat OpenShift 群集的 "客户群集管理员" 角色的成员，以配置容器化代理和 Prometheus 抓取设置。 若要验证你是否为 *osa 客户* 组的成员，请运行以下命令：

``` bash
  oc get groups
```

输出如下所示：

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

如果你是 ConfigMap 组 *的* 成员，则可以 `container-azm-ms-agentconfig` 使用以下命令列出此：

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

输出如下所示：

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>启用监视

执行以下步骤，为 Azure Red Hat OpenShift v3. x 群集配置 ConfigMap 配置文件。

1. 编辑 ConfigMap yaml 文件，并将自定义设置为擦除 Prometheus 指标。 Red Hat OpenShift v3 群集上已存在 ConfigMap 模板。 运行命令 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` ，在文本编辑器中打开文件。

    >[!NOTE]
    >`openshift.io/reconcile-protect: "true"`必须在*azm agentconfig* ConfigMap 的元数据下添加以下批注，以防止对帐。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 若要在群集范围内收集 Kubernetes 服务，请使用以下示例配置 ConfigMap 文件。

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
        >$NODE_IP 是容器参数的特定 Azure Monitor，可以使用它来代替节点 IP 地址。 它必须全部大写。 

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
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果要将监视限制为具有批注的 pod 的特定命名空间，例如仅包含专用于生产工作负荷的 pod，请将设置 `monitor_kubernetes_pod` 为， `true` 并将指定命名空间的命名空间筛选器添加到 `monitor_kubernetes_pods_namespaces` 要擦除的 ConfigMap。 例如 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. 保存在编辑器中所做的更改。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

可以通过运行命令来查看更新的 ConfigMap `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 。 

## <a name="applying-updated-configmap"></a>应用已更新的 ConfigMap

如果已将 ConfigMap 部署到群集，并且想要使用较新的配置对其进行更新，则可以编辑以前使用的 ConfigMap 文件，然后使用与之前相同的命令进行应用。

对于以下 Kubernetes 环境：

- Azure Kubernetes 服务 (AKS)
- Azure Stack 或本地
- Azure Red Hat OpenShift 和 Red Hat OpenShift 版本4。x

运行命令 `kubectl apply -f <configmap_yaml_file.yaml` 。 

对于 Azure Red Hat OpenShift 1.x 群集，运行命令， `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 在默认编辑器中打开该文件以修改然后保存该文件。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" updated`。

## <a name="verify-configuration"></a>验证配置

若要验证是否已成功将配置应用到群集，请使用以下命令从代理 pod 查看日志： `kubectl logs omsagent-fdf58 -n=kube-system` 。 

>[!NOTE]
>此命令不适用于 Azure Red Hat OpenShift 1.x 群集。
> 

如果 omsagent pod 存在配置错误，输出中会显示如下所示的错误：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

还可以查看有关应用配置更改的错误。 以下选项可用于对配置更改和抓取 Prometheus 指标执行额外的故障排除：

- 使用相同命令从代理 pod 日志 `kubectl logs` 
    >[!NOTE]
    >此命令不适用于 Azure Red Hat OpenShift 群集。
    > 

- 从实时数据 (预览) 。 实时数据 (预览) 日志显示类似于以下内容的错误：

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- 从 Log Analytics 工作区中的 **KubeMonAgentEvents** 表。 数据每小时发送一次， *并在出现配置错误的情况* 中擦除错误和 *错误* 严重性。 如果没有错误，表中的条目将包含严重性为“信息”的数据，这些数据不会报告错误。 **Tags**属性包含有关在其上发生错误的 pod 和容器 ID 的详细信息，以及最后一个小时内的第一个匹配项、最后一个匹配项和计数。

- 对于 Azure Red Hat OpenShift 1.x 和 v4. x，请通过搜索 **ContainerLog** 表来检查 omsagent 日志，以验证是否已启用 OpenShift 日志收集。

错误阻止了 omsagent 分析文件，导致其重启并使用默认配置。 更正了除 Azure Red Hat OpenShift v3. x 以外的群集上的 ConfigMap 中的错误 (s) ，请通过运行以下命令保存 yaml 文件并应用更新的 ConfigMaps： `kubectl apply -f <configmap_yaml_file.yaml` 。 

对于 Azure Red Hat OpenShift v3. x，请运行以下命令编辑并保存更新的 ConfigMaps： `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 。

## <a name="query-prometheus-metrics-data"></a>查询 Prometheus 指标数据

若要查看 prometheus 指标擦除 by Azure Monitor 和代理报告的任何配置/抓取错误，请查看 [Query prometheus 度量值数据](container-insights-log-search.md#query-prometheus-metrics-data) 和 [查询配置或抓取错误](container-insights-log-search.md#query-config-or-scraping-errors)。

## <a name="view-prometheus-metrics-in-grafana"></a>在 Grafana 中查看 Prometheus 指标

用于容器的 Azure Monitor 支持在 Grafana 仪表板中查看 Log Analytics 工作区中存储的指标。 我们提供了一个模板，你可从 Grafana 的[仪表板存储库](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)中下载以供入门和参考，它可帮助你了解如何从受监视的群集查询其他数据，来在自定义 Grafana 仪表板中直观显示。 

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

![屏幕截图显示数据引入卷的日志查询结果](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

若要详细了解如何在 [此处](container-insights-agent-config.md)为容器工作负载的 stdout、stderr 和环境变量配置代理收集设置。 
