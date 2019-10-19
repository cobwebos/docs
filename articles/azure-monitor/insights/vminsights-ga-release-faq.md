---
title: 用于 VM 的 Azure Monitor （GA）常见问题 |Microsoft Docs
description: 用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文解答了有关 GA 版本的常见问题。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 523fb2d3a3b148afc9219e666c2fbe7fa40d58ad
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553801"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>用于 VM 的 Azure Monitor 公开发布（GA）常见问题

我们最近在[Azure 更新](https://azure.microsoft.com/blog/)博客上公布了10月和11月2019的一些计划的更改。 有关这些更改的更多详细信息，请阅读此公开发布常见问题解答。

## <a name="updates-for-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 更新

我们发布了11月用于 VM 的 Azure Monitor 的新版本。 在此版本后为 Vm 启用 Azure 监视器的客户会自动收到新版本，但会提示已在使用用于 VM 的 Azure Monitor 的现有客户进行升级。  如果跨多个工作区进行大型部署，则此 FAQ 和我们的文档将提供有关执行批量升级的指导。

通过此升级，用于 VM 的 Azure Monitor 性能数据集现在与[容器 Azure Monitor](container-insights-overview.md)存储在同一 `InsightsMetrics` 表中，并使您可以更轻松地查询这两个数据集。 此外，还可以存储我们在以前使用的表中无法存储的多个不同的数据集。  我们还将更新性能视图以使用此新表。

我们正在迁移到新的连接数据集数据类型。 目前存储在使用自定义日志表的 `ServiceMapComputer_CL` 和 `ServiceMapProcess_CL` 中的数据将移到名为 `VMComputer` 和 `VMProcess` 的专用数据类型。  通过迁移到专用数据类型，我们可以为数据引入提供这些优先级，表架构将在所有客户之间进行标准化。

我们认识到，请求现有客户升级会导致工作流中断，这就是我们在公共预览版中（而不是在以后到达 GA 之后）完成此操作的原因。

## <a name="what-will-change"></a>会发生哪些变化？

目前，当你完成用于 VM 的 Azure Monitor 的载入过程时，将在你选择用于存储监视数据的工作区上启用服务映射解决方案，然后为我们从 Vm 收集的数据配置性能计数器。 在未来的几周内，我们将发布名为**VMInsights**的新解决方案，其中包含用于数据收集的其他功能，以及用于在 Log Analytics 中存储此数据的新位置。

当前在工作区中使用性能计数器的过程将数据发送到 Log Analytics 中的 Perf 表。  这一新的解决方案会将数据发送到名为 `InsightsMetrics` 的表，该表也由容器 Azure Monitor 使用。 此表架构使我们可以存储与 Perf 表格式不兼容的其他度量值和服务数据集。

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>如果安装 VMInsights 解决方案，我应该如何处理我的工作区中的性能计数器？

启用用于 VM 的 Azure Monitor 的当前方法在工作区中使用性能计数器。 新方法将此数据存储在名为 `InsightsMetrics` 的新表中。

更新用户界面以使用 InsightsMetrics 中的数据后，我们将更新文档，并通过多个渠道传达此公告，包括在 Azure 门户中显示横幅。 此时，如果不想再使用它们，则可以选择在工作区中禁用这些[性能计数器](vminsights-enable-overview.md#performance-counters-enabled)。 

[!NOTE]
>如果有在 Perf 表中引用这些计数器的警报规则，则需要将其更新为引用 `InsightsMetrics` 表中的新数据。  请参阅我们的文档，以了解可用于引用此表的示例日志查询。

如果决定保持启用性能计数器，则将根据 [Log Analytics 定价 [（ https://azure.microsoft.com/pricing/details/monitor/) ，为数据引入计费，并将其保留到 Perf 表中。

## <a name="how-will-this-change-affect-my-alert-rules"></a>此更改如何影响我的警报规则？

如果你创建了[日志警报](../platform/alerts-unified-log.md)来查询针对工作区上启用的性能计数器的 `Perf` 表，则应更新这些规则以改为引用 `InsightsMetrics` 表。 本指南还适用于使用 `ServiceMapComputer_CL` 和 `ServiceMapProcess_CL` 的任何日志搜索规则，因为这些数据集将移到 `VMComputer` 和 `VMProcess` 表。

我们将更新此 FAQ 和我们的文档，以包含我们收集的数据集的示例日志搜索警报规则。

## <a name="will-there-be-any-changes-to-billing"></a>对计费是否有任何更改？

计费仍基于数据引入，并保存在 Log Analytics 工作区中。

我们收集的计算机级别性能数据是相同的，它的大小与我们在 Perf 表中存储的数据大小相同，并且成本大致相同。

## <a name="what-if-i-only-want-to-use-service-map"></a>如果我只想使用服务映射，该怎么办？

这是正常的。  查看即将推出的更新用于 VM 的 Azure Monitor 时，你将看到 Azure 门户中的提示。 发布后，你会收到一条提示，要求你将更新为新版本。 如果你只想使用 "[地图](vminsights-maps.md)" 功能，则可以选择不升级并继续使用 "地图" 功能用于 VM 的 Azure Monitor 和从你的工作区或仪表板磁贴访问服务映射解决方案。

如果选择手动启用工作区中的性能计数器，则可能会在 Azure Monitor 查看的某些性能图表中看到数据。 新解决方案发布后，我们将更新性能图表以查询存储在 `InsightsMetrics` 表中的数据。 如果要在这些图表中看到该表中的数据，则需要升级到用于 VM 的 Azure Monitor 的新版本。

从 `ServiceMapComputer_CL` 和 `ServiceMapProcess_CL` 移动数据所做的更改将同时影响服务映射和用于 VM 的 Azure Monitor，因此仍需要规划此更新。

如果你选择不升级到**VMInsights**解决方案，我们将继续提供引用 `Perf` 表中的数据的旧版本的性能工作簿。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>服务映射数据集也会存储在 InsightsMetrics 中吗？

如果同时使用这两种解决方案，则将不会复制数据集。 这两种产品都共享将存储在 `VMComputer` （以前称为 ServiceMapComputer_CL）、`VMProcess` （以前称为 ServiceMapProcess_CL）、`VMConnection` 和 `VMBoundPort` 表中的数据集，以存储我们收集的地图数据集。  

@No__t_0 表将用于存储收集的 VM、进程和服务数据集，并且仅当你使用用于 VM 的 Azure Monitor 时才会填充这些数据集。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>如果我的工作区中有服务映射和 VMInsights 解决方案，是否会向我收费？

不是，这两个解决方案共享存储在 `VMComputer` （以前称为 ServiceMapComputer_CL）、`VMProcess` （以前称为 ServiceMapProcess_CL）、`VMConnection` 和 `VMBoundPort` 的地图数据集。  如果工作区中同时具有两个解决方案，则不会对此数据收费。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>如果删除服务映射或 VMInsights 解决方案，它将在 Log Analytics 中删除我的数据？

不是，这两个解决方案共享存储在 `VMComputer` （以前称为 ServiceMapComputer_CL）、`VMProcess` （以前称为 ServiceMapProcess_CL）、`VMConnection` 和 `VMBoundPort` 的地图数据集。  如果删除其中一个解决方案，则这些数据集会注意到，仍存在一个使用数据的解决方案，并且该解决方案仍处于 Log Analytics 中。  需要从工作区中删除这两个解决方案，以便将数据从 Log Analytics 工作区中删除。

## <a name="when-will-this-update-be-released"></a>何时会发布此更新？

我们预计会在11月中旬发布用于 VM 的 Azure Monitor 的更新。 随着发布日期的发布，我们将在此处发布更新，并在你导航到 Azure Monitor 时在 Azure 门户中显示通知。

## <a name="health-feature-to-enter-limited-public-preview"></a>用于进入受限公共预览的运行状况功能

我们从客户那里收到了有关 VM 健康功能集的大量出色反馈。  这项功能与对支持监视工作流的可能会有多大意义。 我们计划做出一系列的更改来添加功能并处理我们收到的反馈。 为了最大限度地减少对新客户的这些更改的影响，我们将此功能转移到有限的公共预览版中。

此转换将于10月早些时间开始，并应在当月结束时完成。

我们将重点介绍以下方面：

- 更好地控制运行状况模型及其阈值
- 大规模创作适用于 Vm 范围的运行状况模型
- 用于管理基于运行状况的警报规则的警报平台的本机使用情况
- 能够查看更广泛范围内的运行状况，例如一个或多个订阅
- 降低了运行状况转换和警报通知的延迟

## <a name="how-do-existing-customers-access-the-health-feature"></a>现有客户如何访问运行状况功能？

使用运行状况功能的现有客户将继续具有访问权限，但不会提供给新客户。  

若要访问该功能，可以将以下功能标志添加 `feature.vmhealth=true` 到门户 URL [https://portal.azure.com](https://portal.azure.com)。 示例 `https://portal.azure.com/?feature.vmhealth=true`。

你还可以使用此短 url，它会自动设置功能标志： [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)。

作为现有客户，你可以继续在连接到使用运行状况功能的现有工作区设置的 Vm 上使用运行状况功能。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>现在，我将 VM 运行状况用于一个环境，并想要将其部署到新的环境中

如果你是使用运行状况功能的现有客户，并想要将其用于新的推出，请在 vminsights@microsoft.com 与我们联系以请求说明。

## <a name="next-steps"></a>后续步骤

请参阅[部署用于 VM 的 Azure Monitor](vminsights-enable-overview.md)，了解有助于监视虚拟机的要求和方法。
