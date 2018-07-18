---
title: 使用自定义指标在 Azure 中自动缩放
description: 了解如何在 Azure 中根据自定义指标缩放资源。
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 739ef5423f7b1769fa793f0cac5306efa634b781
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262967"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>在 Azure 中根据自定义指标自动缩放入门
本文介绍如何在 Azure 门户中根据自定义指标缩放资源。

Azure Monitor 自动缩放仅适用于虚拟机规模集 (VMSS)、云服务、应用服务计划和应用服务环境。 

# <a name="lets-get-started"></a>入门
本文假定已有一个配了 Application Insights 的 Web 应用。 如果还没有，则可以[为 ASP.NET 网站设置 Application Insights][1]

- 打开 [Azure 门户][2]
- 单击左侧导航窗格中的 Azure Monitor 图标。
  ![启动 Azure Monitor][3]
- 单击“自动缩放设置”可查看自动缩放所适用于的所有资源及其当前的自动缩放状态 ![在 Azure Monitor 中发现自动缩放][4]
- 在 Azure Monitor 中打开“自动缩放”边栏选项卡，并选择要缩放的资源
> 注意：以下步骤使用的应用服务计划与某个已配置 Application Insights 的 Web 应用相关联。
- 在资源的“缩放设置”边栏选项卡中，请注意当前实例计数为 1。 单击“启用自动缩放”。
  ![新 Web 应用的缩放设置][5]
- 提供缩放设置的名称，并单击“添加规则”。 请注意右侧以上下文窗格形式打开的缩放规则选项。 默认情况下，它将选项设置为：如果资源的 CPU 百分比超过 70%，将实例计数缩放 1。 将顶部的指标源更改为“Application Insights”，在“资源”下拉列表中选择 app insights 资源，并选择要基于其缩放的自定义指标。
  ![根据自定义指标缩放][6]
- 与上面的步骤类似，添加这样一个缩放规则：如果自定义指标低于阈值，将缩小规模并将规模计数减 1。
  ![基于 CPU 进行缩放][7]
- 设置实例限制。 例如，如果要根据自定义指标波动在 2-5 个实例之间进行缩放，请将“最小值”设置为“2”，将“最大值”设置为“5”，并将“默认值”设置为“2”
> 注意：如果读取资源指标时出现问题，且当前容量低于默认容量，则要确保资源的可用性，自动缩放将扩大到默认值。 如果当前容量已大于默认容量，则自动缩放不会缩小。
- 单击“保存”

祝贺。 现在已成功创建缩放设置，以基于自定义指标自动缩放 Web 应用。

> 注意：若要开始使用 VMSS 或云服务角色，相同的步骤也适用。

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
