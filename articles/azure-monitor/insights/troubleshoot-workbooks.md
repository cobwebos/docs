---
title: 排查 Azure Monitor 基于工作簿的见解的问题
description: 提供 Azure Monitor 基于工作簿的见解的故障排除指南，帮助你了解诸如 Azure Key Vault、Azure CosmosDB、Azure 存储和 Azure Cache for Redis 之类的服务。
services: azure-monitor
ms.author: mbullwin
author: mrbullwinkle
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 90743a213092aacfa10139c12f47bc11faf0d86d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498706"
---
# <a name="troubleshooting-workbook-based-insights"></a>排查基于工作簿的见解的问题

本部分将帮助你诊断和排查在使用 Azure Monitor 基于工作簿的见解时可能会遇到的一些常见问题。


## <a name="why-can-i-only-see-200-resources"></a>为什么只能看到 200 个资源

无论选择多少个订阅，所选资源的数目都不能超过 200 个。

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>当我单击仪表板中最近固定的磁贴时，会怎么样

* 如果你单击磁贴上的任意位置，则会转到作为磁贴固定来源的选项卡。 例如，如果你固定“概览”选项卡中的一个图，则当你单击仪表板中的该磁贴时，就会打开默认视图，但如果你固定自己保存的副本中的一个图，就会打开所保存副本的视图。
* 单击标题左上角的筛选器图标会打开“配置磁贴设置”选项卡。
* 单击右上角的省略号图标会看到“自定义标题数据”、“自定义”、“刷新”和“从仪表板中删除”选项。

## <a name="what-happens-when-i-save-a-workbook"></a>如果我保存工作簿，会怎么样

* 通过保存工作簿，可以创建包含编辑内容的工作簿的新副本，并更改标题。 保存不会覆盖工作簿，当前工作簿始终为默认视图。
* 未保存的工作簿就是默认视图。

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>为什么我在门户中看不到我的所有订阅

门户只显示在门户启动时选择的订阅的数据。 若要更改所选订阅，请转到右上角，并单击包含筛选器的笔记本图标。 此选项会显示“目录 + 订阅”选项卡。

![目录 + 订阅](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>什么是时间范围

时间范围可用于显示特定期限内的数据。 例如，如果时间范围为 24 小时，则显示过去 24 小时的数据。

## <a name="what-is-time-granularity-time-grain"></a>什么是时间粒度

时间粒度是两个数据点之间的时间差。 例如，如果时间粒度设置为 1 秒，则表示每秒收集一次指标。

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>当我们将工作簿的任何部分固定到仪表板后，时间粒度是多少

默认时间粒度设置为“自动”，暂无法更改。

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>如何更改我的仪表板上的工作簿步骤的时间跨度/时间范围

默认情况下，仪表板磁贴上的时间跨度/时间范围设置为 24 小时。若要更改，请单击右上角的省略号，选择“自定义磁贴数据”，选中“在标题级别替代仪表板时间设置”框，然后使用下拉菜单选择时间跨度。  

![依次选择磁贴右上角的省略号和“自定义磁贴数据”](./media/storage-insights-overview/fqa-data-settings.png)

![在“配置磁贴设置”中，选择“时间跨度”下拉列表来更改时间跨度/时间范围](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>如何更改我固定到仪表板的工作簿或工作簿步骤的标题

固定到仪表板的工作簿或工作簿步骤的标题与它在工作簿中的名称相同。 必须保存你自己的工作簿副本，才能更改标题。 然后，你将能够先命名工作簿，再按“保存”。

![选择顶部的“保存”，以保存工作簿副本，并更改它的名称](./media/storage-insights-overview/fqa-change-workbook-name.png)

若要更改已保存工作簿中某步骤的名称，请先选择此步骤下的“编辑”，再选择设置底部的齿轮。

![选择工作簿步骤底部的“编辑”以打开“设置”](./media/storage-insights-overview/fqa-edit.png)
![在“设置”中，选择底部的齿轮，以便能够更改步骤名称](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>后续步骤

查看[使用 Azure Monitor 工作簿创建交互式报表](../platform/workbooks-overview.md)，详细了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。
