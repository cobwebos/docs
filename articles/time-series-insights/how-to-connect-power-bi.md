---
title: 将你的环境连接到 Power BI-Azure 时序见解 |Microsoft Docs
description: 了解如何将 Azure 时序见解连接到 Power BI，以便在整个组织中共享、绘制和显示数据。
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863836"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>可视化 Power BI 中时序见解的数据

Azure 时序见解是一种平台，用于在云中存储、管理、查询和可视化时间序列数据。 [Power BI](https://powerbi.microsoft.com)是一种业务分析工具，提供丰富的可视化功能，使你能够在组织中共享见解和结果。 现在，这两项服务都可以集成，以获得时序见解固有的可视化功能和 Power BI 的最佳方法。

将了解如何执行以下操作：

* 使用云连接器将时序见解连接到 Power BI
* 在 Power BI 中创建数据的视觉对象
* 将报表发布到 Power BI，并与组织中的其他人共享

最后，你将了解如何通过 Azure 时序见解可视化时序数据，并使用 Power BI 的强大数据可视化功能和轻松共享功能增强它。

如果还没有 Azure 订阅，请确保注册[免费订阅](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备组件

* 下载并安装最新版本的[Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* 具有或创建[Azure 时序见解预览版实例](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> 目前，在为**热商店**配置的时序见解预览即*用即付*环境中目前支持 Power BI 连接器。

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>将数据从时序见解连接到 Power BI

若要将时序见解环境连接到 Power BI，请执行以下步骤：

1. 打开时序见解资源管理器
1. 以查询或原始数据形式导出数据
1. 打开 Power BI Desktop
1. 自定义查询加载

### <a name="export-data-into-power-bi-desktop"></a>将数据导出到 Power BI 桌面

开始使用：

1. 打开时序见解预览资源管理器并组织你的数据。
1. 创建满意的视图后，请导航到 "**更多操作**" 下拉菜单，并选择 "**连接到 Power BI**"。

    [![时序见解预览资源管理器导出](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. 在此选项卡中设置参数：

   1. 指定要查看的相对时间范围。 如果你对现有视图满意，请将其保留为**现有时间范围**。
   
   1. 选择**聚合**事件和**原始事件**。 
   
       > [!NOTE]
       > 您可以始终在 Power BI 中聚合您的数据，但不能在聚合后恢复为原始数据。 
       
       > [!NOTE]
       > 原始事件级别数据的事件计数限制为 100-K。

       [![连接](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. 如果尚未为**热商店**配置时序见解实例，则会收到警告。

       [![热存储警告](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > 可以在 Azure 门户中为**热存储**配置现有实例。

1. 选择 "**将查询复制到剪贴板**"。
1. 现在，启动 Power BI Desktop。
1. 在 Power BI Desktop 的 "**主页**" 选项卡上，选择左上角的 "**获取数据**"，然后选择 "**更多**"。

    [![主页下拉列表](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. 搜索**时序见解**，选择 " **Azure 时序见解（Beta）** "，然后单击 "**连接**"。

    [![将 Power BI 连接到时序见解](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    或者，导航到 " **azure** " 选项卡，选择 " **azure 时序见解（Beta）** "，然后单击 "**连接**"。
    
1. 将显示一个消息对话框，要求提供连接到第三方资源的权限。 选择 "**继续**"。

    [![选择 "创建自定义查询"](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. 在 "**数据源**" 下的下拉菜单中，选择 "**创建自定义查询**"。 将剪贴板中的 "粘贴到可选的**自定义查询（可选）** " 字段中，然后按 **"确定"** 。

    [![传入自定义查询，然后选择 "确定"](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. 此时将加载数据表。 按 "**加载**" 以加载到 Power BI。

    [![查看表中已加载的数据，并选择 "加载"](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

如果已完成这些步骤，请跳到下一部分。

## <a name="create-a-report-with-visuals"></a>使用视觉对象创建报表

将数据导入 Power BI 后，便可以使用视觉对象构建报表了。

1. 请确保已选择了 "**报表**" 视图。

    [![选择报表视图](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  在 "**可视化效果**" 列中，选择你选择的视觉对象。 例如，选择 "**折线图**"。 这会将一个空白折线图添加到画布中。
 
1.  在 "**字段**" 列表中，选择 " **Timestamp** " 并将其拖到 "**轴**" 字段以沿 X 轴显示项。

1.  同样，在 "**字段**" 列表中，选择 " **TimeSeriesId** " 并将其拖到 "**值**" 字段，以沿 Y 轴显示项。

    [![创建折线图](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  若要向画布添加另一个图表，请在折线图外的画布上的任意位置选择，并重复此过程。

    [![创建要共享的其他图表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

创建报表后，可以将其发布到 Power BI Reporting Services。

## <a name="advanced-editing"></a>高级编辑

如果已在 Power BI 中加载了一个数据集，但想要修改查询（如日期/时间或环境 ID 参数），则可以通过 Power BI 的高级编辑器功能来执行此操作。 请参阅[Power BI 文档](https://docs.microsoft.com/power-bi/desktop-query-overview)以了解详细信息。

概述：

1. 在 Power BI Desktop 中，选择 "**编辑查询**"。
1. 按**高级编辑器**。

    [![在高级编辑器中编辑查询](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 根据需要修改 JSON 有效负载。
1. 选择 "**完成**"，然后关闭 " **Power Query 编辑器" 窗口**中 **& 应用**。

接口现在将反映所需的更改。  

## <a name="next-steps"></a>后续步骤

* 阅读有关 Azure 时序见解的[Power BI 连接器概念](https://docs.microsoft.com/power-bi/desktop-query-overview)。

* 详细了解[Power BI 桌面](https://docs.microsoft.com/power-bi/desktop-query-overview)。

* 阅读[时序见解 GA 资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)和[时序见解预览资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)。
