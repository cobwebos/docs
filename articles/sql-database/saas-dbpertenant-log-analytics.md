---
title: 为 SQL 数据库多租户应用程序使用 Log Analytics | Microsoft Docs
description: 通过多租户 Azure SQL 数据库 SaaS 应用设置和使用 Log Analytics (OMS)
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: b141ca521ae9c4d9bf6a4be620bc8e5432c52f83
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>通过多租户 Azure SQL 数据库 SaaS 应用设置和使用 Log Analytics (OMS)

在本教程中，将设置和使用 *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* 来监视弹性池和数据库。 本教程基于[性能监视和管理教程](saas-dbpertenant-performance-monitoring.md)。 它演示如何使用 Log Analytics 加强 Azure 门户中提供的监视和警报。 Log Analytics 支持监视数千个弹性池和数十万个数据库。 Log Analytics 提供单个监视解决方案，该方案可以集成跨多个 Azure 订阅监视不同应用程序和 Azure 服务的功能。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 安装和配置 Log Analytics (OMS)
> * 使用 Log Analytics 监视池和数据库

若要完成本教程，请确保已完成了以下先决条件：

* 已部署 Wingtip Tickets SaaS Database Per Tenant 应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS Database Per Tenant 应用程序](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

请参阅[“性能监视和管理”教程](saas-dbpertenant-performance-monitoring.md)，了解 SaaS 方案和模式及其对解决方案监视要求的影响。

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>使用 Log Analytics 或 Operations Management Suite (OMS) 监视和管理数据库和弹性池性能

对于 SQL 数据库来说，可以在 Azure 门户中对数据库和池使用监视和警报功能。 这种内置的监视和警报功能使用起来很方便，但却是特定于资源的，因此不是很适合监视大型安装，也不是很适合跨资源和订阅提供统一的视图。

对于大容量方案，可以将 Log Analytics 用于监视和警报。 Log Analytics 是一项单独的 Azure 服务，可以用来对诊断日志和遥测数据进行分析，这些日志和数据是在工作区中从数量可能是许多的服务收集的。 Log Analytics 提供内置的查询语言和数据可视化工具，适用于操作性的数据分析。 SQL Analytics 解决方案提供多个预定义的弹性池和数据库监视和警报视图与查询。 OMS 还提供自定义视图设计器。

Log Analytics 工作区和分析解决方案在 Azure 门户和 OMS 中都可以打开。 Azure 门户是更新的访问点，但在某些区域可能会在时间上落后于 OMS 门户。

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>通过模拟租户上的工作负荷创建性能诊断数据 

1. 在 **PowerShell ISE** 中打开 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1***。 请让此脚本保持打开状态，因为可能需要运行此教程中的多个负载生成方案。
1. 如果尚未这样做，请预配一批租户，以便提供更微妙的监视上下文。 这需要几分钟时间：
   1. 设置 **$DemoScenario = 1**，预配一批租户
   1. 若要运行此脚本并再部署 17 个租户，请按 **F5**。  

1. 现在请启动负载生成器，在所有租户上运行模拟的负载。  
    1. 设置 $DemoScenario = 2，生成正常强度负载（约 30 DTU）。
    1. 若要运行脚本，请按 F5。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>获取 Wingtip Tickets SaaS Database Per Tenant 应用程序的脚本

在 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Github 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets PowerShell 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>安装和配置 Log Analytics 和 Azure SQL Analytics 解决方案

Log Analytics 是一种需要配置的单独服务。 Log Analytics 在日志分析工作区中收集日志数据、遥测数据和指标。 Log Analytics 工作区是一种资源，就像 Azure 中的其他资源一样，必须创建。 虽然不要求创建的工作区和其所监视的应用程序位于同一资源组中，但通常情况下，将二者置于同一资源组中是最合理的。 对于 Wingtip Tickets 应用来说，使用单个资源组可以确保将工作区与应用程序一起删除。

1. 在 **PowerShell ISE** 中打开 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***。
1. 若要运行脚本，请按 F5。

此时应可在 Azure 门户（或 OMS 门户）中打开 Log Analytics。 在 Log Analytics 工作区中收集遥测以及让其变得可见需要数分钟。 系统收集诊断数据的时间越长，体验越微妙。 现在不妨休息一下，喝喝饮料 - 只需确保负载生成器仍在运行即可！

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>使用 Log Analytics 和 SQL Analytics 解决方案监视池和数据库


在本练习中，打开 Log Analytics 和 OMS 门户，查看为数据库和池收集的遥测数据。

1. 浏览到 [Azure 门户](https://portal.azure.com)，通过单击“所有服务”并搜索“Log Analytics”来打开 Log Analytics：

   ![打开 Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. 选择名为 _wtploganalytics-&lt;user&gt;_ 的工作区。

1. 选择“概览”，在 Azure 门户中打开 Log Analytics 解决方案。

   ![overview-link](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > 可能需要数分钟才能激活解决方案。 请耐心等待！

1. 单击“Azure SQL Analytics”磁贴将其打开。

    ![概览](media/saas-dbpertenant-log-analytics/overview.png)

    ![分析](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. 解决方案中的视图可以侧向滚动，其自身的内部滚动条位于底部（请根据需要刷新该页面）。

1. 浏览摘要页面，只需单击磁贴或单个数据库以打开向下钻取式的资源管理器即可。

1. 通过更改筛选器设置来修改时间范围 - 就本教程来说，请选取“过去 1 小时”

    ![时间筛选器](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. 选择一个数据库，对该数据库的查询使用情况和指标进行了解。

    ![数据库分析](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 若要查看使用情况指标，请在分析页中滚动到右侧。
 
     ![数据库指标](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 在分析页中向左滚动，单击“资源信息”列表中的服务器磁贴。 此时会打开一个页面，显示服务器上的池和数据库。 

     ![资源信息](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![包含池和数据库的服务器](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. 在已打开的服务器页上（其中显示了服务器上的池和数据库），单击该池。  在打开的池页上滚动到右侧，以便查看池指标。  

     ![池指标](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. 回到 Log Analytics 工作区，此时请选择 **OMS 门户**，在其中打开工作区。

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

在 OMS 门户中，可以在工作区中进一步浏览日志和指标数据。  

Log Analytics 和 OMS 中的监视和警报功能基于工作区中的数据查询，不像在 Azure 门户的每个资源上定义的警报功能。 通过让警报基于查询，可以定义一个监视所有数据库的警报，而不必每个数据库都定义一个。 查询仅受工作区中提供的数据的限制。

若要详细了解如何使用 OMS 来查询和设置警报，请参阅[使用 Log Analytics 中的警报规则](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)。

适用于 SQL 数据库的 Log Analytics 按工作区中的数据量计费。 在本教程中，创建了一个免费的工作区，其限制是每天 500 MB。 达到该限制后，不再向工作区添加数据。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 安装和配置 Log Analytics (OMS)
> * 使用 Log Analytics 监视池和数据库

[“租户分析”教程](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>其他资源

* [构建初始 Wingtip Tickets SaaS Database Per Tenant 应用程序部署的其他教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
