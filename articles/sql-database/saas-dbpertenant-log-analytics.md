---
title: 结合使用 Azure Monitor 日志和 SQL 数据库多租户应用 |Microsoft Docs
description: 设置和使用多租户 Azure SQL 数据库 SaaS 应用使用 Azure Monitor 日志
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6380488faa9a4554df5df5ea67e11dbeb8853fff
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455915"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>设置和多租户 SQL 数据库 SaaS 应用与使用 Azure Monitor 日志

在本教程中，将设置和使用[Azure Monitor 日志](/azure/log-analytics/log-analytics-overview)监视弹性池和数据库。 本教程基于[性能监视和管理教程](saas-dbpertenant-performance-monitoring.md)。 它演示如何使用 Azure Monitor 日志来增强的监视和警报提供在 Azure 门户中。 Azure 监视器将记录支持监视数千个弹性池和数十万个数据库。 Azure Monitor 日志提供了单一的监视解决方案，可以集成跨多个 Azure 订阅监视不同应用程序和 Azure 服务。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 安装和配置 Azure Monitor 日志。
> * 使用 Azure 监视器用于监视池和数据库的日志。

若要完成本教程，请确保已完成了以下先决条件：

* 已部署了 Wingtip Tickets SaaS“每租户一个数据库”应用。 若要在五分钟内完成部署，请参阅[部署并探究 Wingtip Tickets SaaS“每租户一个数据库”应用程序](saas-dbpertenant-get-started-deploy.md)。
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

请参阅[性能监视和管理教程](saas-dbpertenant-performance-monitoring.md)，了解 SaaS 方案和模式以及它们对监视解决方案要求有何影响。

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>监视和管理使用 Azure Monitor 日志的数据库和弹性池性能

对于 Azure SQL 数据库，Azure 门户中提供了针对数据库和池的监视和警报功能。 此内置的监视和警报功能非常方便，但它也是特定于资源的。 这意味着它不太适用于监视大型安装或者提供跨资源和订阅的统一视图。

对于大容量方案，你能够使用 Azure Monitor 日志进行监视和警报。 Azure Monitor 是单独的 Azure 服务，可以用来对诊断日志和遥测数据从潜在的许多服务在工作区中收集的分析。 Azure Monitor 日志提供内置的查询语言和数据可视化工具，允许操作数据分析。 SQL Analytics 解决方案提供了多个预定义的弹性池和数据库监视和警报视图与查询。 Azure Monitor 日志还提供了自定义视图设计器。

OMS 工作区现在称为 Log Analytics 工作区。 Log Analytics 工作区和分析解决方案可以在 Azure 门户中打开。 Azure 门户是更新的访问点，但在某些区域中可能会落后于 Operations Management Suite 门户。

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>通过模拟租户上的工作负荷创建性能诊断数据 

1. 在 PowerShell ISE 中，打开 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*。 请将此脚本保持打开状态，因为在此教程中可能需要运行多个负载生成方案。
1. 如果尚未这样做，请预配一批租户，以使监视上下文更为微妙。 此过程需要花费几分钟时间。

   a. 设置 **$DemoScenario = 1**，_预配一批租户_。

   b. 若要运行此脚本并再部署 17 个租户，请按 F5。

1. 现在请启动负载生成器，在所有租户上运行模拟的负载。

    a. 设置 **$DemoScenario = 2**，_生成正常强度负载 (约 30 DTU)_。

    b. 若要运行脚本，请按 F5。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>获取 Wingtip Tickets SaaS“每租户一个数据库”应用程序脚本

在 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Github 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets PowerShell 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>安装和配置 Log Analytics 工作区和 Azure SQL Analytics 解决方案

Azure Monitor 是单独的服务，必须配置。 Azure 监视器日志在 Log Analytics 工作区中收集日志数据、 遥测数据和指标。 与 Azure 中的其他资源一样，必须创建一个 Log Analytics 工作区。 不要求将工作区创建在它监视的应用程序所在的同一资源组中。 但通常情况下，这样做是最合理的。 对于 Wingtip Tickets 应用，请使用单个资源组以确保将工作区与应用程序一起删除。

1. 在 PowerShell ISE 中，打开 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*。
1. 若要运行脚本，请按 F5。

现在可以打开 Azure 门户中的日志 Azure Monitor。 在 Log Analytics 工作区中收集遥测数据并使其可见需要花费几分钟时间。 留给系统收集诊断数据的时间越长，体验越微妙。 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>使用 Log Analytics 工作区和 SQL Analytics 解决方案监视池和数据库


在此练习中，打开以查看为数据库和池收集的遥测数据在 Azure 门户中的 Log Analytics 工作区。

1. 浏览到 [Azure 门户](https://portal.azure.com)。 选择**所有服务**打开 Log Analytics 工作区。 然后搜索“Log Analytics”。

   ![打开 Log Analytics 工作区](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. 选择名为 _wtploganalytics-&lt;user&gt;_ 的工作区。

1. 选择**概述**若要在 Azure 门户中打开 log analytics 解决方案。

   ![概述](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > 可能需要花费数分钟时间才能激活解决方案。 

1. 选择 **Azure SQL Analytics** 磁贴以将其打开。

    ![概览磁贴](media/saas-dbpertenant-log-analytics/overview.png)

1. 解决方案中的视图可以侧向滚动，其自身的内部滚动条位于底部。 如果需要，请刷新页面。

1. 若要浏览摘要页面，请选择磁贴或各个数据库以打开向下钻取式资源管理器。

    ![Log analytics 仪表板](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. 更改筛选器设置来修改时间范围。 对于本教程，请选择“过去 1 小时”。

    ![时间筛选器](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. 选择单个数据库，了解该数据库的查询使用情况和指标。

    ![数据库分析](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 若要查看使用情况指标，请在分析页中滚动到右侧。
 
     ![数据库指标](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 在分析页中向左滚动，并在“资源信息”列表中选择服务器磁贴。  

    ![“资源信息”列表](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    此时会打开一个页面，其中显示了服务器上的池和数据库。

    ![包含池和数据库的服务器](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. 选择一个池。 在打开的池页上滚动到右侧，以便查看池指标。 

    ![池指标](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. 返回到 Log Analytics 工作区，选择“OMS 门户”以在那里打开工作区。

    ![Log Analytics 工作区](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

在 Log Analytics 门户中，可进一步浏览日志和指标数据。 

监视和警报 Azure Monitor 中日志基于工作区中，像在 Azure 门户中的每个资源上定义的警报功能中的数据查询。 通过让警报基于查询，可以定义一个监视所有数据库的警报，而不必每个数据库都定义一个。 查询仅限于工作区中可用的数据。

有关如何使用 Azure Monitor 日志来查询和设置警报的详细信息，请参阅[Azure Monitor 中的警报规则的工作日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)。

Azure SQL 数据库费用是根据工作区中的数据量的监视日志。 在本教程中，你创建了一个免费工作区，其限制是每天 500 MB。 达到该限制后，不会再向工作区添加数据。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 安装和配置 Azure Monitor 日志。
> * 使用 Azure 监视器用于监视池和数据库的日志。

尝试[“租户分析”教程](saas-dbpertenant-log-analytics.md)。

## <a name="additional-resources"></a>其他资源

* [其他基于初始 Wingtip Tickets SaaS“每租户一个数据库”应用程序部署的教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor 日志](../azure-monitor/insights/azure-sql.md)
