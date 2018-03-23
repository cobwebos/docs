---
title: 针对多个 Azure SQL 数据库运行特别报告查询 | Microsoft Docs
description: 在多租户应用示例中针对多个 SQL 数据库运行特别报告查询。
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 11/13/2017
ms.author: AyoOlubeko
ms.openlocfilehash: d33b95cf4dc05f4eb9f79509cda56e8ab51b7701
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>针对多个 Azure SQL 数据库运行即席分析查询

在本教程中，可以在一组完整的租户数据库中运行分布式查询，启用特别交互式报告。 这些查询可以提取隐藏在 Wingtip Tickets SaaS 应用的日常操作数据中的信息。 若要执行这些提取，请部署一个额外的分析数据库到目录服务器，并使用弹性查询来启用分布式查询。


本教程介绍以下内容：

> [!div class="checklist"]

> * 如何部署特别报告数据库
> * 如何对所有租户数据库运行分布式查询


若要完成本教程，请确保已完成了以下先决条件：

* 已部署 Wingtip Tickets SaaS 多租户数据库应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS 多租户数据库应用程序](saas-multitenantdb-get-started-deploy.md)。
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 安装了 SQL Server Management Studio (SSMS)。 若要下载和安装 SSMS，请参阅[下载 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。


## <a name="ad-hoc-reporting-pattern"></a>特别报告模式

![特别报告模式](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS 应用程序可分析大量集中存储在云中的租户数据。 此分析可显示出应用程序的运行和使用情况。 这些见解可指导应用和服务的功能开发、可用性改进和其他投资。

在单个多租户数据库中访问此数据很简单，但当数据大规模分布在可能数千个数据库中时便不那么容易了。 一种方法是使用[弹性查询](sql-database-elastic-query-overview.md)，这样可以对具有常见架构的一组分布式数据库进行查询。 这些数据库分布在不同资源组和订阅中。 但是，公用登录名必须具有访问从所有数据库提取的数据的权限。 弹性查询使用单个头数据库，其中定义的外部表会镜像分布式（租户）数据库中的表或视图。 提交到此头数据库的查询将进行编译以生成分布式查询计划，其中的部分查询将根据需要向下推送到租户数据库。 弹性查询在目录数据库中使用分片映射确定所有租户数据库的位置。 设置和查询直接使用标准 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) 进行，并支持从 Power BI 和 Excel 等工具进行即席查询。

通过跨租户数据库的分布式查询，弹性查询可以即时了解实时生产数据。 但是，由于弹性查询从潜在的许多数据库中拉取数据，因此查询延迟有时可能高于提交到单个多租户数据库的等效查询的延迟。 请确保设计查询以最小化返回的数据。 弹性查询通常最适合查询少量实时数据，而不是构建频繁使用的或复杂的分析查询或报告。 如果查询效果不佳，请查看[执行计划](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)，了解将查询的哪部分推送到远程数据库。 并评估返回多少数据。 将已提取的租户数据保存到针对分析查询进行了优化的数据库，从而使需要进行复杂分析处理的查询获取到更好的服务。 SQL 数据库和 SQL 数据仓库可以托管此类分析数据库。

这种分析模式在[租户分析教程](saas-multitenantdb-tenant-analytics.md)中进行了介绍。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>获取 Wingtip Tickets SaaS 多租户数据库应用程序源代码和脚本

在 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="create-ticket-sales-data"></a>创建票证销售数据

要针对更有趣的数据集运行查询，请通过运行票证生成器创建票证销售数据。

1. 在 PowerShell ISE 中，打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\Demo-AdhocReporting.ps1 脚本并设置以下值：
   * $DemoScenario = 1，购买在所有地点举行的活动的票证。
2. 按 F5 运行脚本并生成票证销售。 脚本运行时，请继续执行本教程中的步骤。 票证数据在“运行即席分布式查询”部分中查询，因此请等待票证生成器完成。

## <a name="explore-the-tenant-tables"></a>浏览租户表 

在 Wingtip Tickets SaaS 多租户数据库应用程序中，在混合租户管理模型下存储租户，租户数据存储在多租户数据库或单个租户数据库中，并且可以在这两个数据库之间移动。 当查询所有租户数据库时，弹性查询必须可将数据视为由租户分片的单个逻辑数据库的一部分。 

为了实现此模式，所有租户表都包含一个 VenueId 列，用于标识数据所属的租户。 将 VenueId 计算为地点名称的哈希，但可以使用任何方法来引入此列的唯一值。 这种方法类似于在目录中计算租户密钥以进行使用的方式。 弹性查询使用包含 VenueId 的表来并行执行查询，并将其推送到相应的远程租户数据库。 这将显著减少返回的数据量，从而提高性能，尤其是在有多个租户的数据存储在单个租户数据库中时。

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>部署用于即席分布式查询的数据库

本练习部署 adhocreporting 数据库。 这是包含用于跨所有租户数据库进行查询的架构的头数据库。 该数据库部署到现有的编录服务器中，后者是用于在示例应用中所有管理相关数据库的服务器。

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\Demo-AdhocReporting.ps1 并设置以下值：
   * $DemoScenario = 2，部署即席分析数据库。

2. 按 F5 运行脚本并创建 adhocreporting 数据库。

在下一节中将架构添加到数据库，使其可用于运行分布式查询。

## <a name="configure-the-head-database-for-running-distributed-queries"></a>配置用于运行分布式查询的“head”数据库

此练习将架构（外部数据源和外部表定义）添加到特别报告数据库，实现针对所有租户数据库进行查询。

1. 打开 SQL Server Management Studio，并连接到在上一步中创建的特别报告数据库。 数据库的名称为 adhocreporting。
2. 在 SSMS 中打开 ...\Learning Modules\Operational Analytics\Adhoc Reporting\Initialize-AdhocReportingDB.sql。
3. 查看 SQL 脚本，注意以下内容：

   弹性查询使用数据库范围的凭据来访问每个租户数据库。 该凭据需要能够在所有数据库中使用，通常应向其授予启用这些即席查询所需的最小权限。

    ![创建凭据](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   将此目录数据库用作外部数据源，这样就可以在运行查询时将查询分发到在目录中注册的所有数据库。 由于每个部署的服务器名称均不相同，因此，此初始化脚本通过检索执行脚本的当前服务器 (@@servername) 来获取目录数据库的位置。

    ![创建外部数据源](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   引用租户表的外部表通过 DISTRIBUTION = SHARDED(VenueId) 定义。 这可将针对特定 VenueId 的查询路由到相应的数据库，并提升许多方案的性能，如下一节所示。

    ![创建外部表](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   已创建并填充的本地表“VenueTypes”。 此引用数据表常见于所有租户数据库中，因此可以在此表示为本地表并使用通用数据填充。 对于某些查询，这可能会减少在租户数据库和 adhocreporting 数据库之间移动的数据量。

    ![创建表](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   如果以这种方式加入引用表，请确保在每次更新租户数据库时更新表架构和数据。

4. 按 F5 运行脚本并初始化 adhocreporting 数据库。 

现在就可以运行分布式查询，并收集所有租户的见解了！

## <a name="run-ad-hoc-distributed-queries"></a>运行即席分布式查询

设置 adhocreporting 数据库后，请继续运行一些分布式查询。 其中包括执行计划，以便更好地了解查询进程发生的位置。 

检查执行计划时，将鼠标悬停在计划图标上方可获取详细信息。 

1. 在 SSMS 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql*。
2. 确保已连接到 adhocanalytics 数据库。
3. 选择“查询”菜单，然后单击“包括实际的执行计划”
4. 突出显示“当前注册了哪些地点?”查询，然后按 F5。

   该查询会返回整个地点列表，说明跨所有租户进行查询并从每个租户返回数据的速度快慢和难易程度。

   检查计划会发现所有成本都与远程查询相关，因为我们只是转到每个租户数据库并选择地点信息。

   ![SELECT * FROM dbo.Venues](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. 选择下一个查询，然后按 F5。

   此查询联接租户数据库和本地 VenueTypes 表（“本地”是因为它是 adhocreporting 数据库中的表）的数据。

   检查计划会发现大部分成本是远程查询，因为我们查询每个租户的地点信息 (dbo.Venues)，然后与本地 VenueTypes 表进行快速本地联接，以显示友好名称。

   ![远程和本地数据联接](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. 现在选择“哪天销售的票证最多?”查询，然后按 F5。

   此查询执行稍微有些复杂的联接和聚合操作。 需要注意的是，大部分处理过程远程完成，并且再强调一次，我们只返回需要的行，即每天每个地点的总票证销售量仅返回一行内容。

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 对所有租户数据库运行分布式查询
> * 部署特别报告数据库并将架构添加到该数据库，以运行分布式查询。

现在尝试学习[租户分析教程](saas-multitenantdb-tenant-analytics.md)，探索如何将数据提取到单独的分析数据库，从而进行更复杂的分析处理。

## <a name="additional-resources"></a>其他资源

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [弹性查询](sql-database-elastic-query-overview.md)
