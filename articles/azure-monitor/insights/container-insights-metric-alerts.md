---
title: 容器 Azure Monitor 的指标警报
description: 本文介绍了公共预览版中适用于容器 Azure Monitor 建议的指标警报。
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 83394faf3d7296522151b815bddd910d47e45d24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619944"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a> (预览从容器 Azure Monitor 预览) 推荐的指标警报

若要在出现系统资源问题时发出警报，以便在使用容器 Azure Monitor 时发出警报，你可以根据存储在 Azure Monitor 日志中的性能数据创建日志警报。 容器 Azure Monitor 现在包含预配置的指标警报规则，适用于 AKS 和启用了 Azure Arc 的 Kubernetes 群集。

本文介绍了经验并指导如何配置和管理这些警报规则。

如果你不熟悉 Azure Monitor 警报，请在开始之前参阅 [Microsoft Azure 中的警报概述](../platform/alerts-overview.md)。 若要了解有关指标警报的详细信息，请参阅 [Azure Monitor 中的指标警报](../platform/alerts-metric-overview.md)。

## <a name="prerequisites"></a>先决条件

在开始之前，请确认以下事项：

* 自定义指标只在一部分 Azure 区域中可用。 支持的区域的列表记录在 [受支持的区域](../platform/metrics-custom-overview.md#supported-regions)中。

* 为了支持指标警报和其他指标的引入，所需的最低代理版本为： Azure Arc enabled Kubernetes 群集的 **microsoft/oms： ciprod05262020** for AKS and **microsoft/oms： ciprod09252020** 。

    若要验证群集是否正在运行较新版本的代理，可以执行以下操作之一：

    * 运行 `kubectl describe <omsagent-pod-name> --namespace=kube-system` 命令。 在返回的状态中，记下输出的“容器”部分中“映像”下 omsagent 的值。 
    * 在“节点”选项卡上选择群集节点，然后在右侧的“属性”窗格中记下“代理映像标记”下的值。  

    为 AKS 显示的值应为 **ciprod05262020** 或更高版本。 为启用 Azure Arc 的 Kubernetes 群集显示的值应为 **ciprod09252020** 或更高版本。 如果群集具有较旧版本，请参阅 [如何升级容器的 Azure Monitor 代理](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) ，以获取最新版本的步骤。

    有关与代理版本相关的详细信息，请参阅 [代理发行历史记录](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。 若要验证是否正在收集指标，可以使用 Azure Monitor 指标资源管理器，并验证是否列出了**insights**的**指标命名空间**。 如果是这样，您可以继续设置警报。 如果看不到任何收集到的指标，则群集服务主体或 MSI 缺少必要的权限。 若要验证 SPN 或 MSI 是否为 " **监视指标发布者** " 角色的成员，请按照使用 Azure CLI 确认和设置角色分配部分的 " [每个群集升级](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) " 一节中所述的步骤进行操作。

## <a name="alert-rules-overview"></a>警报规则概述

若要对重要内容进行警报，Azure Monitor 容器包括 AKS 和启用了 Azure Arc Kubernetes 群集的以下指标警报：

|名称| 说明 |默认阈值 |
|----|-------------|------------------|
|平均容器 CPU 百分比 |计算每个容器使用的平均 CPU 量。|如果每个容器的平均 CPU 使用率大于95%。| 
|容器工作集平均内存百分比 |计算每个容器使用的平均工作集内存。|如果每个容器的平均工作集内存使用率大于95%，则为。 |
|平均 CPU 百分比 |计算每个节点使用的平均 CPU 量。 |当节点平均 CPU 使用率大于80% 时 |
|磁盘平均使用率百分比 |计算节点的平均磁盘使用量。|节点的磁盘使用大于80% 时。 |
|平均工作集内存百分比 |计算节点的平均工作集内存。 |节点的平均工作集内存大于80% 时。 |
|正在重新启动容器计数 |计算重启容器的数目。 | 当容器重启大于0时。 |
|失败的 Pod 计数 |计算是否有任何处于失败状态的 pod。|当处于 "失败" 状态的某个盒箱数大于0时。 |
|节点 NotReady 状态 |计算是否有任何节点处于 NotReady 状态。|如果 NotReady 状态中的多个节点大于0，则为。 |
|OOM 终止的容器 |计算 OOM 终止容器的数目。 |当多个 OOM 终止容器大于0时。 |
|盒就绪% |计算 pod 的平均就绪状态。 |如果 pod 的就绪状态小于80%。|
|已完成作业计数 |计算在超过6小时之前完成的作业数。 |当过期作业数超过6小时时大于0。|

所有这些警报规则都有通用属性：

* 所有警报规则都基于指标。

* 默认情况下，所有警报规则都处于禁用状态。

* 所有警报规则每分钟评估一次，并在最近5分钟的数据中查看。

* 默认情况下，警报规则未分配给它们。 您可以通过在编辑警报规则时选择现有操作组或创建新的操作组，将 [操作组](../platform/action-groups.md) 添加到警报。

* 您可以通过直接编辑警报规则来修改其阈值。 但在修改其阈值之前，请参阅每个警报规则中提供的指导。

以下基于警报的指标相对于其他指标具有独特的行为特征：

* 仅当存在超过6小时前完成的作业时，才会发送*completedJobsCount*指标。

* 仅当有容器重新启动时，才发送*containerRestartCount*指标。

* 仅当存在 OOM 的已终止容器时才发送*oomKilledContainerCount*指标。

* 在默认阈值为 95% )  (默认阈值超出配置的阈值时，将发送*cpuExceededPercentage*、 *memoryRssExceededPercentage*和*memoryWorkingSetExceededPercentage*指标。 这些阈值与为相应的警报规则指定的警报条件阈值不完全相同。 这意味着，如果要从 [指标资源管理器](../platform/metrics-getting-started.md)中收集这些指标并对其进行分析，建议将阈值配置为低于警报阈值的值。 在节下的 ConfigMaps 文件中，可以重写与容器资源利用率阈值的集合设置相关的配置 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` 。 有关与配置 ConfigMap 配置文件相关的详细信息，请参阅 [配置可报警指标 ConfigMaps](#configure-alertable-metrics-in-configmaps) 部分。

## <a name="metrics-collected"></a>收集的指标

除非另行指定，否则将启用和收集以下度量值：

|指标命名空间 |指标 |说明 |
|---------|----|------------|
|见解：容器/节点 |cpuUsageMillicores |主机 millicores 的 CPU 使用率。|
|见解：容器/节点 |cpuUsagePercentage |按节点的 CPU 使用率百分比。|
|见解：容器/节点 |memoryRssBytes |主机的内存 RSS 利用率（以字节为单位）。|
|见解：容器/节点 |memoryRssPercentage |主机的内存使用百分比。|
|见解：容器/节点 |memoryWorkingSetBytes |主机的内存工作集利用率（以字节为单位）。|
|见解：容器/节点 |memoryWorkingSetPercentage |主机的内存工作集用量百分比。|
|见解：容器/节点 |nodesCount |按状态的节点计数。|
|见解：容器/节点 |diskUsedPercentage |节点上的设备使用的磁盘百分比。|
|见解/容器 |podCount |按控制器、命名空间、节点和阶段的盒计数。|
|见解/容器 |completedJobsCount |已完成的作业数 (默认值为6小时) 按控制器、Kubernetes 命名空间计算的用户可配置阈值。 |
|见解/容器 |restartingContainerCount |按控制器、Kubernetes 命名空间的容器重启计数。|
|见解/容器 |oomKilledContainerCount |按控制器、Kubernetes 命名空间的 OOMkilled 容器计数。|
|见解/容器 |podReadyPercentage |按控制器、Kubernetes 命名空间的盒处于就绪状态的百分比。|
|见解：容器/容器 |cpuExceededPercentage | (默认值为 95.0) 按容器名称、控制器名称、Kubernetes 命名空间和 pod 名称列出的容器的 CPU 使用率百分比。<br> 起来  |
|见解：容器/容器 |memoryRssExceededPercentage | (默认值为 95.0) 按容器名称、控制器名称、Kubernetes 命名空间和 pod 名称的容器的内存 RSS 百分比。|
|见解：容器/容器 |memoryWorkingSetExceededPercentage |对于超出用户可配置阈值的容器，内存工作集百分比 (默认值为 95.0) ，按容器名称、控制器名称、Kubernetes 命名空间、pod 名称进行。|

## <a name="enable-alert-rules"></a>启用警报规则

按照以下步骤，从 Azure 门户 Azure Monitor 中启用指标警报。 若要使用资源管理器模板启用，请参阅 [使用资源管理器模板启用](#enable-with-a-resource-manager-template)。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

本部分逐步讲解如何启用 Azure 门户中的容器指标警报 (预览版) Azure Monitor。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 通过从 Azure 门户中的左窗格中选择 " **见解** "，可以直接从 AKS 群集访问 Azure Monitor for 容器指标警报 (预览版) 功能。

3. 在命令栏中，选择 " **推荐的警报**"。

    ![容器 Azure Monitor 中的建议警报选项](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. " **推荐的警报** " 属性窗格会自动显示在页面的右侧。 默认情况下，列表中的所有警报规则都处于禁用状态。 选择 " **启用**" 后，将创建警报规则，并且规则名称将进行更新以包括指向警报资源的链接。

    ![建议的警报属性窗格](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    选择 **启用/禁用** 切换启用警报后，会创建一个警报规则，并且规则名称将进行更新以包括指向实际警报资源的链接。

    ![启用警报规则](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. 警报规则不与 [操作组](../platform/action-groups.md) 相关联，以通知用户已触发了警报。 选择 " **未分配操作组** "，并在 " **操作组** " 页上，通过选择 " **添加** " 或 " **创建**" 来指定现有或创建操作组。

    ![选择操作组](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>使用资源管理器模板启用

可以使用 Azure 资源管理器模板和参数文件在 Azure Monitor 中创建包含的指标警报。

基本步骤如下所示：

1. 下载说明如何从 [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM)创建警报的一个或所有可用模板。

2. 创建和使用 [参数文件](../../azure-resource-manager/templates/parameter-files.md) 作为 JSON，以设置创建警报规则所需的值。

3. 从 Azure 门户、PowerShell 或 Azure CLI 部署模板。

#### <a name="deploy-through-azure-portal"></a>通过 Azure 门户进行部署

1. 下载并保存到本地文件夹、Azure 资源管理器模板和参数文件，使用以下命令创建警报规则：

2. 若要通过门户部署自定义模板，请从[Azure 门户](https://portal.azure.com)中选择 "**创建资源**"。

3. 搜索 " **模板**"，然后选择 " **模板部署**"。

4. 选择“创建”。

5. 你会看到用于创建模板的多个选项，请选择 " **在编辑器中生成自己的模板**"。

6. 在 " **编辑模板" 页**上，选择 " **加载文件** "，然后选择模板文件。

7. 在 " **编辑模板** " 页上，选择 " **保存**"。

8. 在 " **自定义部署** " 页上，指定以下各项，并在完成后选择 " **购买** " 以部署模板并创建警报规则。

    * 资源组
    * 位置
    * 警报名称
    * 群集资源 ID

#### <a name="deploy-with-azure-powershell-or-cli"></a>用 Azure PowerShell 或 CLI 进行部署

1. 下载并保存到本地文件夹、Azure 资源管理器模板和参数文件，使用以下命令创建警报规则：

2. 可以通过 PowerShell 或 Azure CLI 使用模板和参数文件创建指标警报。

    使用 Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    使用 Azure CLI

    ```azurecli
    az login

    az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >虽然可以在与目标资源不同的资源组中创建指标警报，但建议使用与目标资源相同的资源组。

## <a name="edit-alert-rules"></a>编辑警报规则

可以查看和管理容器警报规则 Azure Monitor，以编辑其阈值或为 AKS 群集配置 [操作组](../platform/action-groups.md) 。 尽管可以从 Azure 门户和 Azure CLI 执行这些操作，但也可以直接从容器的 Azure Monitor 中的 AKS 群集执行这些操作。

1. 在命令栏中，选择 " **推荐的警报**"。

2. 若要修改阈值，请在 " **推荐的警报** " 窗格中选择 "启用的警报"。 在 " **编辑规则**" 中，选择要编辑的 **警报条件** 。

    * 若要修改警报规则阈值，请选择该 **条件**。
    * 若要指定现有操作组或创建操作组，请在 "**操作组**" 下选择 "**添加**" 或 "**创建**"

若要查看为启用规则创建的警报，请在 " **推荐的警报** " 窗格中选择 " **在警报中查看**"。 你将被重定向到 AKS 群集的 "警报" 菜单，可在其中查看当前为群集创建的所有警报。

## <a name="configure-alertable-metrics-in-configmaps"></a>在 ConfigMaps 中配置可报警指标

执行以下步骤，将 ConfigMap 配置文件配置为覆盖默认容器资源利用率阈值。 这些步骤仅适用于以下可报警指标。

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*

1. 编辑节中的 ConfigMap yaml 文件 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` 。

2. 若要将 *cpuExceededPercentage* 阈值修改为90%，并在达到或超过该阈值时开始收集此指标，请使用以下示例配置 ConfigMap 文件。

    ```
    container_cpu_threshold_percentage = 90.0
    # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
    container_memory_rss_threshold_percentage = 95.0
    # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
    container_memory_working_set_threshold_percentage = 95.0
    ```

3. 运行以下 kubectl 命令： `kubectl apply -f <configmap_yaml_file.yaml>` 。

    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

## <a name="next-steps"></a>后续步骤

- 请参阅[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。

- 若要详细了解 Azure Monitor 以及如何监视 Kubernetes 群集的其他方面，请参阅 [查看 Kubernetes 群集性能](container-insights-analyze.md)。
