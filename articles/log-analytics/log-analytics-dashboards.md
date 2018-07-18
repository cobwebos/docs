---
title: 在 Azure Log Analytics 中创建自定义仪表板 | Microsoft 文档
description: 本指南可帮助你了解 Log Analytics 仪表板如何直观显示所有已保存的日志搜索，从而提供单个可重用功能区来查看环境。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 7b17b23d30b15fe71dcf031ac67f0c54fc3cfe3e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129306"
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>创建适用于 Log Analytics 的自定义仪表板

本指南可帮助你了解 Log Analytics 仪表板如何直观显示所有已保存的日志搜索，从而提供单个可重用功能区来查看环境。

>[!NOTE]
> 无法再编辑现有的“我的仪表板”。 此功能即将弃用。

![示例仪表板](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

在 OMS 门户中创建的所有自定义面板也可以在 OMS 移动应用中使用。 有关此类应用的详细信息，请参阅以下页面。

* [Microsoft 官方商城中的 OMS 移动应用](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Apple iTunes 中的 OMS 移动应用](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![移动仪表板](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>如何创建我自己的仪表板？
若要开始操作，请转到 OMS 概述页。 将看到左侧的“我的仪表板”磁贴。 单击它以向下钻取到仪表板。

![概述](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>添加磁贴
在仪表板中，磁贴由已保存的日志搜索提供支持。 OMS 附带了许多预制的已保存日志搜索，以便可以立即开始操作。 使用以下概述了如何开始的步骤。

在“我的仪表板”视图中，只需单击“自定义”即可进入自定义模式。

![图示](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 页面右侧打开的面板中会显示你所有的工作区保存的日志搜索。 要以磁贴形式可视化已保存的日志搜索，请将鼠标悬停在保存的搜索上，然后单击**加号**。

![添加磁贴 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

单击**加号**后，新磁贴会显示在“我的仪表板”视图中。

![添加磁贴 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>编辑磁贴
在“我的仪表板”视图中，只需单击“自定义”即可进入自定义模式。 单击要编辑的磁贴。 右侧面板将随着编辑而变化，并提供多个选项以供选择：

![编辑磁贴](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![编辑磁贴](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>磁贴可视化
有三种类型的磁贴可视化可供选择：

| 图表类型 | 作用 |
| --- | --- |
| ![条形图](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |以条形图形式显示已保存日志搜索结果的时间线，或按字段显示结果列表，具体取决于日志搜索是否按字段聚合结果。 |
| ![度量值](./media/log-analytics-dashboards/oms-dashboards-metric.png) |在磁贴中以数字形式显示日志搜索命中数。 度量值磁贴允许设置阈值，当达到该阈值时会突出显示磁贴。 |
| ![折线图](./media/log-analytics-dashboards/oms-dashboards-line.png) |以折线图形式显示已保存日志搜索结果命中数以及值的时间线。 |

### <a name="threshold"></a>阈值
可使用度量值可视化，在磁贴上创建阈值。 选择要在其上创建阈值的磁贴。 选择是否要在值超出或低于所选阈值时突出显示磁贴，并设置以下阈值。

## <a name="organizing-the-dashboard"></a>组织仪表板
若要组织仪表板，则导航到“我的仪表板”视图并单击“自定义”以进入自定义模式。 单击并拖动要移动的磁贴，然后将它移动到所需的位置。

![组织仪表板](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>删除磁贴
若要删除磁贴，则导航到“我的仪表板”视图并单击“自定义”以进入自定义模式。 选择要删除的磁贴，并在右侧面板上选择“删除磁贴”。

![删除磁贴](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>后续步骤
* 在 Log Analytics 中创建[警报](log-analytics-alerts.md)，以生成通知并解决问题。
