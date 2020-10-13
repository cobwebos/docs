---
title: 用于 VM 的 Azure Monitor 中的更改分析
description: 用于 VM 的 Azure Monitor 与应用程序更改分析集成集成，你可以查看对虚拟机所做的任何更改，这些更改可能影响到 it 性能。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711224"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 中的更改分析
用于 VM 的 Azure Monitor 与 [应用程序更改分析](../app/change-analysis.md) 集成集成，你可以查看对虚拟机所做的任何更改，这些更改可能影响到 it 性能。

## <a name="overview"></a>概述
假设你有一个运行速度缓慢的 VM，希望调查最近对其配置所做的更改是否会影响其性能。 你可以使用用于 VM 的 Azure Monitor 查看 VM 的性能，并发现过去一小时内存使用量增加。 更改分析可以帮助您确定在这段时间内所做的任何配置更改是否导致这一增长。

应用程序更改分析服务聚合了 [Azure 资源关系图](../../governance/resource-graph/how-to/get-resource-changes.md) 中的更改以及嵌套的属性更改，如来自 Azure 资源管理器的网络安全规则。 

## <a name="enabling-change-analysis"></a>启用更改分析
若要在用于 VM 的 Azure Monitor 中载入更改分析，必须注册 *ChangeAnalysis* 资源提供程序。 首次在 Azure 门户中启动用于 VM 的 Azure Monitor 或应用程序更改分析时，将自动注册此资源提供程序。 应用程序更改分析是一种免费服务，对资源没有性能开销。

## <a name="view-change-analysis"></a>查看更改分析
通过选择 "**更改**" 选项，可以从用于 VM 的 Azure Monitor 的 "**性能**" 或 "**映射**" 选项卡中选择 "更改分析"。 

[![调查更改](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


单击 " **调查更改** " 按钮，启动为 VM 筛选的 "应用程序更改分析" 页。 您可以查看列出的更改，以确定是否有可能导致此问题的问题。 如果您不确定具体的更改，则可以引用 " **更改者** " 列来确定进行更改的人员。

[![更改详细信息](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>后续步骤
- 了解有关 [应用程序更改分析](../app/change-analysis.md)的详细信息。
- 了解有关 [Azure 资源关系图](../../governance/resource-graph/how-to/get-resource-changes.md)的详细信息。 

