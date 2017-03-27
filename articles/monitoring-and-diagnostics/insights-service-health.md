---
title: "使用 Azure 监视器活动日志跟踪 Azure 服务运行状况 | Microsoft Docs"
description: "在 Azure 遇到性能下降或服务中断及时发现。 "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>使用 Azure 监视器活动日志跟踪 Azure 服务运行状况
每次发生服务中断或性能下降时 Azure 会进行宣传。 可以在 Azure 门户中浏览这些事件，也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式访问完整的事件集。

## <a name="browse-the-service-health-logs-for-your-subscription"></a>浏览有关订阅的服务运行状况日志
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“主页”中，可以看到名为“服务运行状况”的磁贴。 单击该磁贴。
   
    ![主页](./media/insights-service-health/Insights_Home.png)
3. 将看到 Azure 中所有区域的列表。 单击任一区域可打开活动日志查询，该查询显示在过去 24 小时内影响任何订阅的服务事件。
   
    ![活动日志订阅的服务运行状况](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. 单击表中的事件可查看各个事件的详细信息。
5. 更改**时间跨度**可查看更长时间范围内所发生事件。

## <a name="next-steps"></a>后续步骤
* 使用 Application Insights [监视任何网页的可用性和响应能力](../application-insights/app-insights-monitor-web-app-availability.md)，以便可以在页面出现故障时及时发现。




<!--HONumber=Nov16_HO3-->


