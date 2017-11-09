---
title: "通过 Log Analytics 查看 Azure 活动日志 | Microsoft Docs"
description: "可以使用 Azure 活动日志解决方案，分析并搜索所有 Azure 订阅的 Azure 活动日志。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: 1ad56a54f094f3c314596b3a7c9fecd09647d065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="view-azure-activity-logs"></a>查看 Azure 活动日志

![“Azure 活动日志”符号](./media/log-analytics-activity/activity-log-analytics.png)

Activity Log Analytics 解决方案有助于分析和搜索所有 Azure 订阅的 [Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。 Azure 活动日志是一种日志，可使用户了解对订阅中的资源执行的操作。 “活动日志”此前称为“审核日志”或“操作日志”，因为它报告订阅的各种事件。

通过活动日志，可确定对订阅中的资源进行的任何写入操作（PUT、POST、DELETE）的*内容*、*执行者*和*时间*。 还可以了解操作和其他相关属性的状态。 活动日志不包括读取 (GET) 操作或针对使用经典部署模型的资源的操作。

将 Azure 活动日志连接到 Log Analytics 时，可以：

- 通过预定义视图分析活动日志
- 分析和搜索多个 Azure 订阅中的活动日志
- 将活动日志保留超过 90 天<sup>1</sup>
- 将活动日志与其他 Azure 平台和应用程序数据关联起来
- 查看按状态聚合的操作活动
- 查看在每个 Azure 服务上发生的活动的趋势
- 报告所有 Azure 资源上的授权更改
- 确定影响资源的故障或服务运行状况问题
- 使用日志搜索功能，将用户活动、自动缩放操作、授权更改和服务运行状况与环境中的其他日志或指标关联起来

<sup>1</sup>默认情况下，Log Analytics 将 Azure 活动日志保留 90 天，即使在免费层也是如此。 或者，将工作区保留期设置为少于 90 天。 如果工作区保留期长于 90 天，活动日志会在工作区保留期时段内得到保留。

Log Analytics 免费收集活动日志，并将日志免费存储 90 天。 如果日志存储时间超过 90 天，将对存储超过 90 天的数据收取数据保留费。

当在免费定价层上时，活动日志不适用于日常数据消耗。

## <a name="connected-sources"></a>连接的源

与大多数其他的 Log Analytics 解决方案不同，代理不会为活动日志收集数据。 该解决方案使用的全部数据都直接来自于 Azure。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| [Windows 代理](log-analytics-windows-agents.md) | 否 | 解决方案不会从 Windows 代理收集信息。 |
| [Linux 代理](log-analytics-linux-agents.md) | 否 | 解决方案不会从 Linux 代理收集信息。 |
| [SCOM 管理组](log-analytics-om-agents.md) | 否 | 解决方案不会从连接的 SCOM 管理组中的代理收集信息。 |
| [Azure 存储帐户](log-analytics-azure-storage.md) | 否 | 解决方案不会从 Azure 存储收集信息。 |

## <a name="prerequisites"></a>先决条件

- 若要访问 Azure 活动日志信息，必须拥有 Azure 订阅。

## <a name="configuration"></a>配置

执行以下步骤，为工作区配置 Activity Log Analytics 解决方案。

1. 从 [Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview)或者使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Activity Log Analytics 解决方案。
2. 配置活动日志，以转到 Log Analytics 工作区。
    1. 在 Azure 门户中，选择工作区，并单击“Azure 活动日志”。
    2. 对于每个订阅，单击订阅名称。  
        ![添加订阅](./media/log-analytics-activity/add-subscription.png)
    3. 在“SubscriptionName”边栏选项卡中，单击“连接”。  
        ![连接订阅](./media/log-analytics-activity/subscription-connect.png)

如果使用 OMS 门户添加解决方案，将看到以下磁贴。 登录 Azure 门户，将 Azure 订阅连接到工作区。  
![执行评估](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>使用解决方案

将 Activity Log Analytics 解决方案添加到工作区时，“Azure 活动日志”磁贴将添加到“概述”仪表板。 此磁贴显示该解决方案可访问的 Azure 订阅的 Azure 活动记录计数。

![Azure 活动日志磁贴](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>查看 Azure 活动日志

单击“Azure 活动日志”磁贴，打开“Azure 活动日志”仪表板。 该仪表板包含下表中的边栏选项卡。 每个边栏选项卡按照指定范围和时间范围列出了匹配该边栏选项卡条件的最多 10 个项。 可通过单击边栏选项卡底部的“查看全部”或单击边栏选项卡标题，运行返回所有记录的日志搜索。

活动日志数据仅在配置活动日志以转到解决方案*后*才显示，所以在此之前无法查看数据。

| 边栏选项卡 | 说明 |
| --- | --- |
| Azure 活动日志条目 | 显示所选日期范围内排名前列的 Azure 活动日志条目记录总数的条形图，并显示前 10 个活动调用方的列表。 单击该条形图可针对 <code>Type=AzureActivity</code> 运行日志搜索。 单击某个调用方项，运行日志搜索，从而为该项返回所有活动日志条目。 |
| 按状态分类的活动日志 | 为所选日期范围内的 Azure 活动日志状态显示圆环图。 此外还显示前十个状态记录的列表。 单击该图表可针对 <code>Type=AzureActivity &#124; measure count() by ActivityStatus</code> 运行日志搜索。 单击某个状态项，运行日志搜索，从而为该状态记录返回所有活动日志条目。 |
| 按资源分类的活动日志 | 显示包含活动日志的资源总数，并列出前十个为每个资源显示记录计数的资源。 单击全部区域可针对 <code>Type=AzureActivity &#124; measure count() by Resource</code> 运行日志搜索，这会显示解决方案可以使用的所有 Azure 资源。 单击某个资源以运行日志搜索，从而为该资源返回所有活动记录。 |
| 按资源提供程序分类的活动日志 | 显示生成活动日志的资源提供程序的总数，并列出前十个资源提供程序。 单击全部区域可针对 <code>Type=AzureActivity &#124; measure count() by ResourceProvider</code> 运行日志搜索，这会显示所有 Azure 资源提供程序。 单击某个资源提供程序以运行日志搜索，从而为该提供程序返回所有活动记录。 |

![Azure 活动日志仪表板](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>后续步骤

- 在发生特定活动时创建[警报](log-analytics-alerts-creating.md)。
- 使用[日志搜索](log-analytics-log-searches.md)查看活动日志中的详细信息。
