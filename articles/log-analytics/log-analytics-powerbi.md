---
title: 将 Azure Log Analytics 数据导入 Power BI | Microsoft 文档
description: Power BI 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  本文介绍如何配置并将 Log Analytics 数据导入 Power BI 且将其配置为自动刷新。
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>将 Azure Log Analytics 数据导入 Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  你可以将 Log Analytics 日志搜索结果导入 Power BI 数据集，以便利用其各项功能，例如合并不同来源的数据以及在 Web 和移动设备上共享报表。

## <a name="overview"></a>概述
若要将 Log Analytics 工作区的数据导入到 Power BI，可以基于 Log Analytics 中的日志搜索查询在 Power BI 中创建数据集。  每次刷新数据集时都会运行查询。  随后你即可生成使用数据集中的数据的 Power BI 报表。  若要在 Power BI 中创建数据集，你可以将查询从 Log Analytics 导出为 [Power Query (M) 语言](https://msdn.microsoft.com/library/mt807488.aspx)。  然后，你可以使用其在 Power BI Desktop 中创建查询，再将其作为数据集发布到 Power BI。  此过程的详细信息如下所述。

![Log Analytics 到 Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>导出查询
首先创建一个[日志搜索](log-analytics-log-search-new.md)，其从 Log Analytics 返回要填充 Power BI 数据集的数据。  随后将该查询导出为 Power BI Desktop 可以使用的 [Power Query (M) 语言](https://msdn.microsoft.com/library/mt807488.aspx)。

1. 在 Log Analytics 中创建日志搜索以便为数据集提取数据。
2. 如果你正在使用日志搜索门户，请单击“Power BI”。  如果你正在使用 Analytics 门户，请选择“导出” > “Power BI Query (M)”。  这两种选项都会将查询导出到一个名为“PowerBIQuery.txt”的文本文件。 

    ![导出日志搜索](media/log-analytics-powerbi/export-logsearch.png) ![导出日志搜索](media/log-analytics-powerbi/export-analytics.png)

3. 打开该文本文件并复制其内容。

## <a name="import-query-into-power-bi-desktop"></a>将查询导入到 Power BI Desktop
Power BI Desktop 是一个桌面应用程序，用于创建可发布到 Power BI 的数据集和报表。  另外，你还可以使用它创建使用从 Log Analytics 导出的 Power Query 语言的查询。 

1. 请安装 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)（如果你尚未安装），然后打开该应用程序。
2. 选择“获取数据” > “空查询”，以打开一个新查询。  然后选择“高级编辑器”并将导出文件的内容粘贴到查询中。 单击“Done”（完成） 。

    ![Power BI Desktop 查询](media/log-analytics-powerbi/desktop-new-query.png)

5. 查询将运行并显示其结果。  系统可能会提示你输入凭据以连接到 Azure。  
6. 为查询键入一个描述性的名称。  默认值为“Query1”。 单击“关闭并应用”，以向报表添加数据集。

    ![Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>发布到 Power BI
当你发布到 Power BI 时，将创建数据集和报表。  如果你是在 Power BI Desktop 中创建报表，则发布的报表将包含你的数据。  如果不是，则会创建一个空白报表。  你可以在 Power BI 中修改报表或基于数据集创建新报表。

8. 基于你的数据创建报表。  请使用 [Power BI Desktop 文档](https://docs.microsoft.com/power-bi/desktop-report-view)（如果你不熟悉此应用程序）。  如果你已准备好将其发送到 Power BI，请单击“发布”。  出现提示时，请在 Power BI 帐户中选择目标。  除非你有特定的目标，否则请使用“我的工作区”。

    ![Power BI Desktop 发布](media/log-analytics-powerbi/desktop-publish.png)

3. 发布完成后，单击“在 Power BI 中打开”，以在 Power BI 中打开新数据集。


### <a name="configure-scheduled-refresh"></a>配置计划的刷新
在 Power BI 中创建的数据集中包含的数据与你以前在 Power BI Desktop 中看到的数据一样。  你需要定期刷新数据集以再次运行查询，并使用 Log Analytics 中的最新数据进行填充。  

1. 单击要在其中上传报表的工作区并选择“数据集”菜单。 选择你的新数据集旁边的上下文菜单，然后选择“设置”。 在“数据源凭据”下，应显示一条消息，提示凭据无效。  这是因为你尚未提供在刷新数据时要使用的数据集的凭据。  单击“编辑凭据”，然后指定有权访问 Log Analytics 的凭据。

    ![Power BI 计划](media/log-analytics-powerbi/powerbi-schedule.png)

5. 在“计划的刷新”下，开启“使你的数据保持为最新”选项。  你可以视情况更改“刷新频率”和运行刷新的一个或多个特定时间。

    ![Power BI 刷新](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>后续步骤
* 了解有关[日志搜索](log-analytics-log-searches.md)的信息以生成可导出到 Power BI 的查询。
* 了解有关 [Power BI](http://powerbi.microsoft.com) 的详细信息以基于 Log Analytics 导出内容生成可视化结果。
