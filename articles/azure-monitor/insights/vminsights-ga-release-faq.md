---
title: 虚拟机 （GA） 常见问题的 Azure 监视器 |微软文档
description: 用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文回答了有关 GA 版本的常见问题。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283882"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>适用于 VM 的 Azure 监视器（GA） 常见问题
此通用可用性常见问题解答涵盖 2019 年第 4 季度和 2020 年第 1 季度我们为 GA 做好准备时所做的更改。

## <a name="updates-for-azure-monitor-for-vms"></a>VM Azure 监视器的更新
2020 年 1 月，我们在 GA 发布之前发布了新版本的 VM Azure 监视器。 启用 VM Azure 监视器的客户现在将收到 GA 版本，但使用 2019 年第 4 季度和更早版本的 VM Azure 监视器的现有客户将提示升级。 如果跨多个工作区部署大型部署，此常见问题解答提供了大规模执行升级的指导。


通过此升级，VM 性能数据的 Azure 监视器与[容器的 Azure 监视器](container-insights-overview.md)存储在相同的*InsightsMetrics*表中，这样您就更容易地查询这两个数据集。 此外，您还可以存储我们无法存储在以前使用的表中的更多不同的数据集。 

我们的性能视图现在使用我们在*InsightsMetrics*表中存储的数据。  如果您尚未升级以在工作区上使用最新的 VMInsights 解决方案，则图表将不再显示信息。  您可以通过以下说明从我们的**入门**页面进行升级。


## <a name="what-is-changing"></a>有什么变化？
我们发布了名为 VMInsights 的新解决方案，其中包括用于数据收集的其他功能，以及用于在日志分析工作区中存储此数据的新位置。 

过去，我们在工作区启用了 ServiceMap 解决方案，并在日志分析工作区中设置性能计数器，将数据发送到*Perf*表。 此新解决方案将数据发送到名为*InsightsMetrics 的*表，该表也由 Azure 监视器用于容器。 此表架构允许我们存储与*Perf*表格式不兼容的其他指标和服务数据集。

我们更新了性能图表，以使用我们在*InsightsMetrics*表中存储的数据。 您可以升级以使用"从我们的**入门"页面的***"见解指标"* 表，如下所述。


## <a name="how-do-i-upgrade"></a>如何升级？
当日志分析工作区升级到最新版本的 Azure 监视器到 VM 时，它将升级附加到该工作区的每个 VM 上的依赖项代理。 每个需要升级的 VM 都将在 Azure 门户中 VM 的 Azure 监视器中的"**入门"** 选项卡中标识。 当您选择升级 VM 时，它将升级该 VM 的工作区以及连接到该工作区的任何其他 VM。 您可以选择单个 VM 或多个 VM、资源组或订阅。 

使用以下命令使用 PowerShell 升级工作区：

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>如果我安装 VMInsights 解决方案，我应该如何处理工作区中的性能计数器？

以前为 VM 启用 Azure 监视器的方法在工作区中使用了性能计数器。 当前版本将此数据存储在名为 的`InsightsMetrics`表中。 如果您不再需要使用这些性能计数器，则可以选择在工作区中禁用这些性能计数器。 

>[!NOTE]
>如果表中具有引用这些计数器的`Perf`警报规则，则需要更新它们以引用`InsightsMetrics`表中存储的新数据。 请参阅我们的文档，例如可以使用的日志查询，请参阅此表。
>

如果您决定保持启用性能计数器，将根据 [日志分析定价] 对在`Perf`表中引入和存储的数据进行计费。https://azure.microsoft.com/pricing/details/monitor/)

## <a name="how-will-this-change-affect-my-alert-rules"></a>此更改将如何影响我的警报规则？

如果已创建查询针对工作区中启用的性能计数器`Perf`的表的`InsightsMetrics`[日志警报](../platform/alerts-unified-log.md)，则应更新这些规则以引用该表。 `ServiceMapComputer_CL`本指南也适用于使用`ServiceMapProcess_CL`和 的任何日志搜索规则，因为这些数据集正在移动到`VMComputer`和`VMProcess`表。

我们将更新此常见问题解答和文档，以包括我们收集的数据集的示例日志搜索警报规则。

## <a name="how-will-this-affect-my-bill"></a>这将如何影响我的账单？

计费仍基于日志分析工作区中引入和保留的数据。

我们收集的机器级性能数据相同，大小与`Perf`表中存储的数据类似，并且成本大致相同。

## <a name="what-if-i-only-want-to-use-service-map"></a>如果我只想使用服务映射，该怎么办？

没关系 在 Azure 监视器中查看有关即将更新的更新的 VM 时，将在 Azure 门户中看到提示。 发布后，您将收到一个提示，请求更新到新版本。 如果只想使用["地图"](vminsights-maps.md)功能，可以选择不升级并继续使用 Azure 监视器中的"地图"功能（用于 VM）以及从工作区或仪表板磁贴访问的服务映射解决方案。

如果选择手动启用工作区中的性能计数器，则可以在 Azure 监视器查看的某些性能图表中查看数据。 发布新解决方案后，我们将更新性能图表以查询`InsightsMetrics`表中存储的数据。 如果要查看这些图表中该表中的数据，则需要升级到新版本的 VM Azure 监视器。

要将数据从`ServiceMapComputer_CL`中`ServiceMapProcess_CL`移动的更改将影响 VM 的服务映射和 Azure 监视器，因此您仍然需要规划此更新。

如果您选择不升级到**VMInsights**解决方案，我们将继续提供我们性能工作簿的旧版本，这些版本引用`Perf`表中的数据。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>服务映射数据集是否会存储在见解指标中？

如果使用这两个解决方案，则数据集将不会重复。 这两个产品`VMComputer`共享将存储在（以前ServiceMapComputer_CL）、（`VMProcess`以前ServiceMapProcess_CL）`VMConnection`的数据集和`VMBoundPort`用于存储我们收集的地图数据集的表。  

该`InsightsMetrics`表将存储我们收集的 VM、进程和服务数据集，并且仅在使用 AZURE 监视器进行 VM 和 VM 见解解决方案时才会填充这些数据集。 服务映射解决方案不会收集或存储`InsightsMetrics`表中的数据。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>如果我的工作区中包含服务映射和 VMInsights 解决方案，我是否会收取双倍费用？

否，这两个解决方案共享我们存储在`VMComputer`中的地图数据集（以前ServiceMapComputer_CL）、（`VMProcess`以前ServiceMapProcess_CL）、`VMConnection`和`VMBoundPort`。 如果工作区中同时包含两个解决方案，则此数据不会收取双重费用。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>如果我删除服务映射或 VMInsights 解决方案，它会删除我的数据吗？

否，这两个解决方案共享我们存储在`VMComputer`中的地图数据集（以前ServiceMapComputer_CL）、（`VMProcess`以前ServiceMapProcess_CL）、`VMConnection`和`VMBoundPort`。 如果删除其中一个解决方案，这些数据集会注意到仍有一个解决方案使用数据，并且它保留在日志分析工作区中。 您需要从工作区中删除这两个解决方案，以便从工作区中删除数据。

## <a name="health-feature-is-in-limited-public-preview"></a>运行状况功能处于有限的公共预览版

我们收到了客户关于 VM 运行状况功能集的很多反馈。 围绕此功能，人们对其支持监控工作流的潜力非常感兴趣。 我们计划进行一系列更改，以添加功能并解决我们收到的反馈。 

为了尽量减少这些更改对新客户的影响，我们已将此功能移到**有限的公共预览版**中。 此更新发生在 2019 年 10 月。

我们计划在 2020 年重新启动此运行状况功能，因为 VM 的 Azure 监视器位于 GA 中。

## <a name="how-do-existing-customers-access-the-health-feature"></a>现有客户如何访问运行状况功能？

使用 Health 功能的现有客户将继续访问它，但不会向新客户提供。  

要访问该功能，可以将以下功能标志`feature.vmhealth=true`添加到 Azure 门户 URL [https://portal.azure.com](https://portal.azure.com)。 示例`https://portal.azure.com/?feature.vmhealth=true`。

您还可以使用此短 URL，自动设置要素标志： [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)。

作为现有客户，您可以继续使用与运行状况功能连接到现有工作区设置的 VM 上的运行状况功能。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>我现在将 VM 运行状况与一个环境一起使用，并希望将其部署到新的环境

如果您是使用 Health 功能的现有客户，并希望将其用于新的推出，请立即vminsights@microsoft.com联系我们，索取说明。

## <a name="next-steps"></a>后续步骤

请参阅[部署用于 VM 的 Azure Monitor](vminsights-enable-overview.md)，了解有助于监视虚拟机的要求和方法。
