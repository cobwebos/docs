---
title: Azure Monitor 基于工作簿的见解的疑难解答
description: 提供针对 Azure Key Vault、Azure CosmosDB、Azure 存储和适用于 Redis 的 Azure 缓存的服务 Azure Monitor 基于工作簿的见解的疑难解答指南。
services: azure-monitor
ms.author: mbullwin
author: mrbullwinkle
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: c903855dd7a550cfeef845d9c176e7ce7806a0df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84946890"
---
# <a name="troubleshooting-workbook-based-insights"></a>基于工作簿的见解疑难解答

本文将帮助你诊断和排查在使用 Azure Monitor 基于工作簿的见解时可能会遇到的一些常见问题。


## <a name="why-can-i-only-see-200-resources"></a>为什么只能看到200资源

所选资源的数目限制为200，而不考虑所选订阅的数目。

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>在仪表板中单击最近固定的磁贴时，会发生什么情况

* 如果你单击磁贴上的任意位置，则会转到作为磁贴固定来源的选项卡。 例如，如果你在 "概览" 选项卡中固定一个关系图，则当你在仪表板中单击该磁贴时，它将打开该默认视图，但是，如果你从自己保存的副本固定图形，则会打开你保存的副本的视图。
* 单击标题左上角的筛选器图标会打开“配置磁贴设置”选项卡。
* 单击右上角的省略号图标会看到“自定义标题数据”、“自定义”、“刷新”和“从仪表板中删除”选项。

## <a name="what-happens-when-i-save-a-workbook"></a>保存工作簿时，会发生什么情况

* 保存工作簿时，可以通过编辑来创建工作簿的新副本，并更改标题。 保存不会覆盖工作簿，当前工作簿始终为默认视图。
* 未保存的工作簿就是默认视图。

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>为什么我在门户中看不到所有订阅

门户只显示在门户启动时选择的订阅的数据。 若要更改所选订阅，请转到右上角，并单击包含筛选器的笔记本图标。 此选项将显示 "**目录 + 订阅**" 选项卡。

![目录 + 订阅](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>什么是时间范围

时间范围可用于显示特定期限内的数据。 例如，如果时间范围为 24 小时，则显示过去 24 小时的数据。

## <a name="what-is-time-granularity-time-grain"></a>什么是时间粒度（时间粒度）

时间粒度是两个数据点之间的时间差。 例如，如果时间粒度设置为 1 秒，则表示每秒收集一次指标。

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>将工作簿的任何部分固定到仪表板后的时间粒度是多少

默认时间粒度设置为“自动”，暂无法更改。

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>如何实现在我的仪表板上更改工作簿步骤的 timespan/时间范围

默认情况下，仪表板磁贴上的时间跨度/时间范围设置为 24 小时。若要更改，请单击右上角的省略号，选择“自定义磁贴数据”，选中“在标题级别替代仪表板时间设置”框，然后使用下拉菜单选择时间跨度。  

![依次选择磁贴右上角的省略号和“自定义磁贴数据”](./media/storage-insights-overview/fqa-data-settings.png)

![在 "配置磁贴设置" 中，选择 timespan 下拉列表以更改时间跨度/时间范围](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>如何实现更改工作簿的标题或固定到仪表板的工作簿步骤

固定到仪表板的工作簿或工作簿步骤的标题与它在工作簿中的名称相同。 必须保存你自己的工作簿副本，才能更改标题。 然后，你将能够先命名工作簿，再按“保存”。

![选择顶部的“保存”，以保存工作簿副本，并更改它的名称](./media/storage-insights-overview/fqa-change-workbook-name.png)

若要更改已保存工作簿中某个步骤的名称，请在该步骤下选择 "编辑"，然后选择 "设置" 底部的 "齿轮"。

![选择工作簿步骤底部的“编辑”以打开“设置”](./media/storage-insights-overview/fqa-edit.png)
![在“设置”中，选择底部的齿轮，以便能够更改步骤名称](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>后续步骤

查看[使用 Azure Monitor 工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)，了解用于支持的工作簿的详细信息，如何创作新报表和自定义现有报表。