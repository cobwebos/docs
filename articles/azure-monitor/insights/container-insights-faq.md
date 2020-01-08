---
title: 用于容器的 Azure Monitor 常见问题解答 | Microsoft Docs
description: 用于容器的 Azure Monitor 是用于监视 Azure 中的 AKS群集和容器实例的运行状况的解决方案。 本文将解答一些常见问题。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 0984de51221c506bb1824e4dcfd93eef56453a4d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405069"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>用于容器的 Azure Monitor 常见问题解答

本 Microsoft 常见问题解答列出了用于容器的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>我看不到在查询 ContainerLog 表时填充的图像和名称属性值。

对于代理版本 ciprod12042019 和更高版本，默认情况下，不会为每个日志行填充这两个属性，以最大程度地减少收集日志数据时产生的成本。 有两个选项可用于查询表，其中包含这些属性及其值：

### <a name="option-1"></a>选项 1 

联接其他表以在结果中包含这些属性值。

通过联接到 ContainerID 属性，将查询修改为包含 ```ContainerInventory``` 表中的 Image 和 ImageTag 属性。 通过在 ContainerID 属性上联接，可以在 KubepodInventory 表的 ContaineName 字段中包含 Name 属性（与以前在 ```ContainerLog``` 表中显示的名称相同）。建议使用此选项。

下面的示例是一个示例详细查询，说明如何使用联接获取这些字段的值。

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>选项 2

为每个容器日志行的这些属性重新启用收集。

如果第一个选项因涉及到查询更改而不是很方便，则可以通过启用 "[数据收集配置设置](./container-insights-agent-config.md)" 中所述的 "代理配置" 映射中的设置 ```log_collection_settings.enrich_container_logs``` 来重新启用收集这些字段。

> [!NOTE]
> 对于包含50个以上节点的大型群集，不建议使用第二个选项，因为它将从群集中的每个节点 > 生成 API 服务器调用以执行此扩充。 此选项还会增加收集的每个日志行的数据大小。

## <a name="can-i-view-metrics-collected-in-grafana"></a>能否查看在 Grafana 中收集的指标？

容器 Azure Monitor 支持在 Grafana 仪表板中查看 Log Analytics 工作区中存储的度量值。 我们提供了一个模板，你可以从 Grafana 的 "[仪表板" 存储库](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)下载该模板，以帮助你了解如何从受监视的群集查询其他数据以在自定义 Grafana 仪表板中进行可视化。 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>是否可以监视包含 Azure Monitor 容器的 AKS-engine 群集？

容器 Azure Monitor 支持监视部署到 Azure 上托管的 AKS （以前称为 ACS 引擎）群集的容器工作负荷。 有关为此方案启用监视所需步骤的详细信息和概述，请参阅将[Azure Monitor 用于 AKS 的容器](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>为什么我在 Log Analytics 工作区中看不到数据？

如果在特定时间每天都无法查看 Log Analytics 工作区中的任何数据，则可能已达到默认的 500 MB 限制或指定的每日上限，以控制每日收集的数据量。 当当天的限制达到限制时，数据收集将停止并仅在下一天恢复。 若要查看数据使用情况并根据预期的使用模式更新到不同的定价层，请参阅[日志数据使用情况和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定了哪些容器状态？

ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何实现解决*缺少订阅注册*错误？

如果收到错误 "为 microsoft.operationsmanagement" 中**缺少订阅注册**，可以通过在其中定义了工作区的订阅中注册资源提供程序**为 microsoft.operationsmanagement**来解决此错误。 可以在[此处](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到介绍如何执行此操作的文档。

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>支持 RBAC 的 AKS 群集是否支持？

容器监视解决方案不支持 RBAC，但对于容器 Azure Monitor 支持。 在显示这些群集的数据的边栏选项卡上，解决方案详细信息页可能不会显示正确的信息。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>如何通过 Helm 为 kube-system 命名空间中的容器启用日志收集？

默认情况下，kube-system 命名空间中的容器的日志收集被禁用。 可以通过在 omsagent 上设置一个环境变量来启用日志收集。 有关详细信息，请参阅[容器](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)GitHub 的 Azure Monitor 页。 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何将 omsagent 更新为最新发布的版本？

若要了解如何升级代理，请参阅[代理管理](container-insights-manage-agent.md)。

## <a name="how-do-i-enable-multi-line-logging"></a>如何启用多行日志记录？

当前，用于容器的 Azure Monitor 不支持多行日志记录，但有可用的变通方法。 你可以将所有服务配置为以 JSON 格式进行写入，然后 Docker/Moby 会在单个行上写入它们。

例如，可以将你的日志包装为一个 JSON 对象，如示例 node.js 应用程序的以下示例中所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

查询日志时，此数据将如以下示例 Azure Monitor 中所示：

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有关此问题的详细信息，请查看以下[GitHub 链接](https://github.com/moby/moby/issues/22920)。

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>启用实时日志时如何实现解决 Azure AD 错误？ 

你可能会看到以下错误：**请求中指定的回复 url 与为应用程序配置的回复 url "< 应用程序 ID\>" 不匹配**。 解决方法的解决方案可以在[如何与 Azure Monitor 容器的实时查看容器数据](container-insights-livedata-setup.md#configure-ad-integrated-authentication)一文中找到。 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>为什么在加入后无法升级群集？

如果为 AKS 群集启用容器 Azure Monitor，将删除群集发送其数据的 Log Analytics 工作区，尝试升级群集时，它将失败。 若要解决此情况，你必须禁用监视，然后重新启用它以引用订阅中的不同有效工作区。 当你尝试再次执行群集升级时，它应进行处理并成功完成。  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>需要为代理打开/允许列表的端口和域？

请参阅 Azure、Azure 美国政府和 Azure 中国云容器代理所需的代理和防火墙配置信息的[网络防火墙要求](container-insights-onboard.md#network-firewall-requirements)。

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何载入用于容器的 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。 
