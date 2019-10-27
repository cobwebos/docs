---
title: 将 Azure Log Analytics 数据导入 Power BI | Microsoft 文档
description: Power BI 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  本文介绍如何配置并将 Log Analytics 数据导入 Power BI 且将其配置为自动刷新。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 62a010480dc83561a11c6ee99c76f35b29e808c1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932111"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>导入 Azure Monitor 日志数据到 Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  您可以将 Azure Monitor 日志查询的结果导入到 Power BI 数据集，以便可以利用其功能，例如合并不同源中的数据以及在 web 和移动设备上共享报表。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>概述
若要将数据从 Azure Monitor 中的[Log Analytics 工作区](manage-access.md)导入 Power BI，可以基于 Azure Monitor 中的[日志查询](../log-query/log-query-overview.md)在 Power BI 中创建数据集。  每次刷新数据集时都会运行查询。  随后你即可生成使用数据集中的数据的 Power BI 报表。  若要在 Power BI 中创建数据集，你可以将查询从 Log Analytics 导出为 [Power Query (M) 语言](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)。  然后，你可以使用其在 Power BI Desktop 中创建查询，再将其作为数据集发布到 Power BI。  此过程的详细信息如下所述。

![Log Analytics 到 Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>导出查询
首先创建一个[日志查询](../log-query/log-query-overview.md)，该查询返回要填充 Power BI 数据集的数据。  随后将该查询导出为 Power BI Desktop 可以使用的 [Power Query (M) 语言](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)。

1. [在 Log Analytics 中创建日志查询](../log-query/get-started-portal.md)以提取数据集的数据。
2. 选择 "**导出**" > **Power BI Query （M）** 。  这会将查询导出到名为**PowerBIQuery**的文本文件。 

    ![导出日志搜索](media/powerbi/export-analytics.png)

3. 打开该文本文件并复制其内容。

## <a name="import-query-into-power-bi-desktop"></a>将查询导入到 Power BI Desktop
Power BI Desktop 是一个桌面应用程序，用于创建可发布到 Power BI 的数据集和报表。  你还可以使用它来创建使用从 Azure Monitor 导出的 Power Query 语言的查询。 

1. 请安装 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)（如果你尚未安装），然后打开该应用程序。
2. 选择“获取数据” > “空查询”，以打开一个新查询。  然后选择“高级编辑器”并将导出文件的内容粘贴到查询中。 单击“完成”。

    ![Power BI Desktop 查询](media/powerbi/desktop-new-query.png)

5. 查询将运行并显示其结果。  系统可能会提示你输入凭据以连接到 Azure。  
6. 为查询键入一个描述性的名称。  默认值为“Query1”。 单击“关闭并应用”，以向报表添加数据集。

    ![Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>发布到 Power BI
当你发布到 Power BI 时，将创建数据集和报表。  如果你是在 Power BI Desktop 中创建报表，则发布的报表将包含你的数据。  如果不是，则会创建一个空白报表。  你可以在 Power BI 中修改报表或基于数据集创建新报表。

1. 基于你的数据创建报表。  请使用 [Power BI Desktop 文档](https://docs.microsoft.com/power-bi/desktop-report-view)（如果你不熟悉此应用程序）。  
1. 如果你已准备好将其发送到 Power BI，请单击“发布”。  
1. 出现提示时，请在 Power BI 帐户中选择目标。  除非你有特定的目标，否则请使用“我的工作区”。

    ![Power BI Desktop 发布](media/powerbi/desktop-publish.png)

1. 发布完成后，单击“在 Power BI 中打开”，以在 Power BI 中打开新数据集。


### <a name="configure-scheduled-refresh"></a>配置计划的刷新
在 Power BI 中创建的数据集中包含的数据与你以前在 Power BI Desktop 中看到的数据一样。  需要定期刷新数据集以再次运行查询，并在其中填充 Azure Monitor 的最新数据。  

1. 单击要在其中上传报表的工作区并选择“数据集”菜单。 
1. 选择你的新数据集旁边的上下文菜单，然后选择“设置”。 
1. 在“数据源凭据”下，应显示一条消息，提示凭据无效。  这是因为你尚未提供在刷新数据时要使用的数据集的凭据。  
1. 单击 "**编辑凭据**"，并指定可以访问 Azure Monitor 中的 "Log Analytics" 工作区的凭据。 如果需要双重身份验证，请选择 " **OAuth2** "，以提示**身份验证方法**以凭据登录。

    ![Power BI 计划](media/powerbi/powerbi-schedule.png)

5. 在“计划的刷新”下，开启“使你的数据保持为最新”选项。  你可以视情况更改“刷新频率”和运行刷新的一个或多个特定时间。

    ![Power BI 刷新](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>后续步骤
* 了解有关[日志搜索](../log-query/log-query-overview.md)的信息以生成可导出到 Power BI 的查询。
* 详细了解如何基于 Azure Monitor 日志导出生成可视化[Power BI](https://powerbi.microsoft.com) 。