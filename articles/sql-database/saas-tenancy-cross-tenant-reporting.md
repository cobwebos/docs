---
title: "针对多个 Azure SQL 数据库运行报告查询 | Microsoft Docs"
description: "使用分布式查询实现跨租户报告。"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: 531d284798e455622faa1bfe7b0c8f178b3642fd
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>使用分布式查询实现跨租户报告

在本教程中，将在一组完整的租户数据库中运行分布式查询进行报告。 这些查询可提取 Wingtip Tickets SaaS 租户日常操作数据中隐藏的信息。 为此，请再部署一个报告数据库到编录服务器，并使用弹性查询来启用分布式查询。


本教程介绍以下内容：

> [!div class="checklist"]

> * 如何部署报告数据库
> * 如何对所有租户数据库运行分布式查询
> * 每个数据库中的全局视图如何实现跨租户的有效查询


若要完成本教程，请确保已完成了以下先决条件：


* 已部署 Wingtip Tickets SaaS Database Per Tenant 应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS Database Per Tenant 应用程序](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 安装了 SQL Server Management Studio (SSMS)。 若要下载和安装 SSMS，请参阅[下载 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。


## <a name="cross-tenant-reporting-pattern"></a>跨租户报告模式

![跨租户分布式查询模式](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

SaaS 应用程序具有诸多优势，包括可让你使用云端存储的大量租户数据来深入了解应用程序的操作和使用情况。 这些见解可指导应用和服务的功能开发、可用性改进和其他投资。

在单个多租户数据库中访问此数据很简单，但当数据大规模分布在可能数千个数据库中时便不那么容易了。 一种方法是使用[弹性查询](sql-database-elastic-query-overview.md)，这样可以对具有常见架构的一组分布式数据库进行查询。 这些数据库分布在不同资源组和订阅中，但需要共享共同登录。 弹性查询使用单个头数据库，其中定义的外部表会镜像分布式（租户）数据库中的表或视图。 提交到此头数据库的查询将进行编译以生成分布式查询计划，其中的部分查询将根据需要向下推送到租户数据库。 弹性查询在目录数据库中使用分片映射确定所有租户数据库的位置。 可使用标准 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) 轻松设置和查询头像数据库，并可通过 Power BI 和 Excel 等工具进行查询。

通过跨租户数据库的分布式查询，弹性查询可以即时了解实时生产数据。 由于弹性查询从潜在的许多数据库中拉取数据，因此相比于提交到单个多租户数据库的等效查询，此查询的延迟时间更长。 请设计查询，使其尽量减少返回到头像数据库的数据量。 弹性查询通常最适合查询少量实时数据，而不是构建频繁使用的或复杂的分析查询或报告。 如果查询效果不佳，请查看[执行计划](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)，了解要将查询的哪部分推送到远程数据库以及要返回多少数据。 可将租户数据提取到针对分析查询进行优化的数据库或数据仓库中，从而使需要复杂聚合或分析处理的查询效果更佳。 这种模式在[租户分析教程](saas-tenancy-tenant-analytics.md)中进行了介绍。 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>获取 Wingtip Tickets SaaS Database Per Tenant 应用程序的脚本

在 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Github 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="create-ticket-sales-data"></a>创建票证销售数据

要针对更有趣的数据集运行查询，请通过运行票证生成器创建票证销售数据。

1. 在 PowerShell ISE 中，打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\Demo-AdhocReporting.ps1 脚本并设置以下值：
   * $DemoScenario = 1，购买在所有地点举行的活动的票证。
2. 按 F5 运行脚本并生成票证销售。 脚本运行时，请继续执行本教程中的步骤。 票据数据在“运行即席分布式查询”部分中查询，所以请等待票据生成器完成。

## <a name="explore-the-global-views"></a>浏览全局视图

在 Wingtip Tickets SaaS Database Per Tenant 应用程序中，每个租户都分配有一个数据库。 因此，数据库表中包含的数据范围设置为单个租户的透视。 然而，当查询所有数据库时，请注意，弹性查询可以将数据视为由租户划分的单个逻辑数据库分片的一部分。 

若要模拟此模式，请将一组“全局”视图添加到租户数据库中，以将租户 ID 投射到全局查询的每个表中。 例如，VenueEvents 视图将计算出的 VenueId 添加到从 Events 表投射的列中。 同样，VenueTicketPurchases 和 VenueTickets 视图会添加从各自表中投影的 VenueId 计算列。 VenueId 列出现后，弹性查询使用这些视图来并行查询并将其推送到合适的远程租户数据库。 这大大减少了要返回的数据量，从而使许多查询的性能大幅提高。 这些全局视图已经在所有租户数据库中预先创建。

1. 打开 SSMS 并[连接到 tenants1-&lt;USER&gt; 服务器](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
1. 展开“数据库”，右键单击“contosoconcerthall”，并选择“新建查询”。
1. 运行以下查询，了解单租户表和全局视图的差异：

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

在这些视图中，将 VenueId 计算为地点名称的哈希，但可以使用任何方法来引入唯一值。 这种方法类似于在目录中计算租户密钥以进行使用的方式。

若要检查地点视图的定义，请执行以下操作：

1. 在“对象资源管理器”中，展开“contosoconcerthall” > “视图”：

   ![视图](media/saas-tenancy-cross-tenant-reporting/views.png)

2. 右键单击“dbo.Venues”。
3. 选择“将视图脚本编写为” > “CREATE TO” > “新建查询编辑器窗口”

生成任何其他地点视图的脚本，查看它们添加 VenueId 的方式。

## <a name="deploy-the-database-used-for-distributed-queries"></a>部署用于分布式查询的数据库

本练习部署 adhocreporting 数据库。 这是包含用于跨所有租户数据库进行查询的架构的头数据库。 该数据库部署到现有的编录服务器中，后者是用于在示例应用中所有管理相关数据库的服务器。

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1*。 

1. 设置 $DemoScenario = 2，它表示部署特别报告数据库。

1. 按 F5 运行脚本并创建 adhocreporting 数据库。

在下一节中将架构添加到数据库，使其可用于运行分布式查询。

## <a name="configure-the-head-database-for-running-distributed-queries"></a>配置用于运行分布式查询的“头像”数据库

此练习将架构（外部数据源和外部表定义）添加到特别报告数据库，实现针对所有租户数据库进行查询。

1. 打开 SQL Server Management Studio，并连接到在上一步中创建的特别报告数据库。 数据库的名称为 adhocreporting。
2. 在 SSMS 中打开 ...\Learning Modules\Operational Analytics\Adhoc Reporting\Initialize-AdhocReportingDB.sql。
3. 查看 SQL 脚本，并注意以下内容：

   弹性查询使用数据库范围的凭据来访问每个租户数据库。 此凭据必须在所有数据库中可用，且通常情况下需具备启用这些查询所需的最低权限。

    ![创建凭据](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   通过将目录数据库用作外部数据源，在运行查询时将查询分发到在目录中注册的所有数据库。 由于每个部署的服务器名称各不相同，因此该脚本从执行脚本的当前服务器 (@@servername) 中获取目录数据库的位置。

    ![创建外部数据源](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   引用上一节中介绍的全局视图的外部表，通过 DISTRIBUTION = SHARDED(VenueId) 进行定义。 每个 VenueId 映射到单个数据库，这样就可以提升许多方案的性能，如下一节所示。

    ![创建外部表](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   已创建并填充的本地表“VenueTypes”。 此引用数据表常见于所有租户数据库中，因此可以在此表示为本地表并使用通用数据填充。 对于某些查询，在头像数据库中定义的此表可减少需要移动到头像数据库中的数据量。

    ![创建表](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   如果以这种方式加入引用表，请确保在每次更新租户数据库时更新表架构和数据。

4. 按 F5 运行脚本并初始化 adhocreporting 数据库。 

现在就可以运行分布式查询，并收集所有租户的见解了！

## <a name="run-distributed-queries"></a>运行分布式查询

设置 adhocreporting 数据库后，请继续运行一些分布式查询。 其中包括执行计划，以便更好地了解查询进程发生的位置。 

检查执行计划时，将鼠标悬停在计划图标上方可获取详细信息。 

特别要注意是，定义外部数据源时的设置 DISTRIBUTION = SHARDED(VenueId) 可以提升许多方案的性能。 每个 VenueId 映射到单个数据库，这样就可以轻松进行远程筛选，仅返回我们需要的数据。

1. 在 SSMS 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\Demo-AdhocReportingQueries.sql。
2. 确保已连接到 adhocanalytics 数据库。
3. 选择“查询”菜单，然后单击“包括实际的执行计划”
4. 突出显示“当前注册了哪些地点?”查询，然后按 F5。

   该查询会返回整个地点列表，说明跨所有租户进行查询并从每个租户返回数据的速度快慢和难易程度。

   请检查计划，了解是否远程查询总费用。每个数据库都会远程执行查询，并将位置信息返回到头像数据库中。

   ![SELECT * FROM dbo.Venues](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. 选择下一个查询，然后按 F5。

   此查询联接租户数据库和本地 VenueTypes 表（“本地”是因为它是 adhocreporting 数据库中的表）的数据。

   检查计划会发现大部分成本都与远程查询相关。 每个租户数据库都返回其地点信息，并执行与本地 VenueTypes 表的本地联接以显示友好名称。

   ![远程和本地数据联接](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. 现在选择“哪天售票量多大?”查询，然后按 F5。

   此查询执行稍微有些复杂的联接和聚合操作。 大多数处理操作远程执行。  仅向头像数据库返回包含每个位置每天售票数量的单个行。

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 对所有租户数据库运行分布式查询
> * 部署报告数据库，并定义运行分布式查询所需的架构。


现在尝试学习[租户分析教程](saas-tenancy-tenant-analytics.md)，探索如何将数据提取到单独的分析数据库，从而进行更复杂的分析处理。

## <a name="additional-resources"></a>其他资源

* [构建 Wingtip Tickets SaaS Database Per Tenant 应用程序的其他教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [弹性查询](sql-database-elastic-query-overview.md)
