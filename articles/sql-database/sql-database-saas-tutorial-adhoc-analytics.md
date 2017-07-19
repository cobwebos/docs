---
title: "针对多个 Azure SQL 数据库运行即席分析查询 | Microsoft Docs"
description: "在 Wingtip SaaS 多租户应用中对多个 SQL 数据库运行即席分析查询。"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c287fe5d6b333c749b0580b5253e7e46ac27232b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>对所有 Wingtip SaaS 租户运行即席分析查询

在本教程中，可以在一组完整的租户数据库中运行分布式查询，以启用即席分析。 弹性查询用于启用分布式查询，这要求部署一个额外的分析数据库（到编录服务器）。 这些查询可以提取隐藏在 Wingtip SaaS 应用的日常操作数据中的信息。


本教程介绍以下内容：

> [!div class="checklist"]

> * 每个数据库中用于跨租户进行有效查询的全局视图
> * 如何部署即席分析数据库
> * 如何对所有租户数据库运行分布式查询



若要完成本教程，请确保已完成了以下先决条件：

* Wingtip SaaS 应用已部署。 若要在五分钟内进行部署，请参阅[部署和浏览 Wingtip SaaS 应用程序](sql-database-saas-tutorial.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 安装了 SQL Server Management Studio (SSMS)。 若要下载和安装 SSMS，请参阅[下载 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。


## <a name="ad-hoc-analytics-pattern"></a>即席分析模式

SaaS 应用程序提供的大好机会之一是使用集中存储在云中的大量租户数据。 使用此数据来深入了解应用程序的操作和使用情况、租户、租户的用户及其首选项和行为等。这些见解可指导应用和服务的功能开发、可用性改进和其他投资。

在单个多租户数据库中访问此数据很简单，但当数据大规模分布在可能数千个数据库中时便不那么容易了。 一种方法是使用[弹性查询](sql-database-elastic-query-overview.md)，这样可以对具有常见架构的一组分布式数据库进行查询。 弹性查询使用单个头数据库，其中定义的外部表会镜像分布式（租户）数据库中的表或视图。 提交到此头数据库的查询将进行编译以生成分布式查询计划，其中的部分查询将根据需要向下推送到租户数据库。 弹性查询在目录数据库中使用分片映射提供租户数据库的位置。 设置和查询直接使用标准 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) 进行，并支持从 Power BI 和 Excel 等工具进行即席查询。

通过跨租户数据库的分布式查询，弹性查询可以即时了解实时生产数据。 但是，由于弹性查询从潜在的许多数据库中拉取数据，因此查询延迟有时可能高于提交到单个多租户数据库的等效查询的延迟。 设计查询以最小化返回的数据时应小心谨慎。 弹性查询通常最适合查询少量实时数据，而不是构建频繁使用的或复杂的分析查询或报告。 如果查询效果不佳，请查看[执行计划](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)，了解将查询的哪部分推送到远程数据库以及返回多少数据。 在某些情况下，可以将租户数据提取到专用数据库或针对分析查询进行优化的数据仓库中，从而使需要进行复杂分析处理的查询获取到更好的服务。 这种模式在[租户分析教程](sql-database-saas-tutorial-tenant-analytics.md)中进行了介绍。 

## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip SaaS 脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存储库中找到。 [下载 Wingtip SaaS 脚本的步骤](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="create-ticket-sales-data"></a>创建票证销售数据

要针对更有趣的数据集运行查询，请通过运行票证生成器创建票证销售数据。

1. 在 PowerShell ISE 中，打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* 脚本并设置以下值：
   * $DemoScenario = 1，购买在所有地点举行的活动的票证。
2. 按 F5 运行脚本并生成票证销售。 脚本运行时，请继续执行本教程中的步骤。 在“运行即席分布式查询”部分查询票证数据，如果你在执行该操作时，票证生成器仍在运行，请等待它完成。

## <a name="explore-the-global-views"></a>浏览全局视图

Wingtip SaaS 应用程序使用“一数据库一租户”模型生成，因此租户数据库架构是从单租户角度定义的。 特定于租户的信息存在于名为“Venue”的单个表中，该表始终只有一行，并且被实现为堆，没有主键。 架构中的其他表不需与 Venue 表相关联，因为在正常使用时，数据属于哪个租户是没有任何疑问的。

然而，当查询所有数据库时，请注意，弹性查询可以将数据视为由租户划分的单个逻辑数据库分片的一部分。 为实现这一点，将一组“全局”视图添加到租户数据库中，该租户数据库将租户 ID 投射到全局查询的每个表中。 例如，VenueEvents 视图将计算出的 VenueId 添加到从 Events 表投射的列中。 通过在 VenueEvents（而不是基础 Events 表）中的头数据库中定义外部表，弹性查询能够根据 VenueId 推送联接，这样它们就可以在每个远程数据库（而不是头数据库）上并行执行。 这大大减少了要返回的数据量，从而使许多查询的性能大幅提高。 这些全局视图已经在所有租户数据库（和 basetenantdb）中预先创建。

1. 打开 SSMS 并[连接到 tenants1-&lt;USER&gt; 服务器](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
2. 展开“数据库”，右键单击“contosoconcerthall”，然后选择“新建查询”。
3. 运行以下查询，了解单租户表和全局视图的差异：

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

1. 在“对象资源管理器”中，展开“contosoconcethall” > “视图”：

   ![视图](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

2. 右键单击“dbo.Venues”。
3. 选择“将视图脚本编写为” > “CREATE TO” > “新建查询编辑器窗口”

生成任何其他地点视图的脚本，查看它们添加 VenueId 的方式。

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>部署用于即席分布式查询的数据库

本练习部署 adhocanalytics 数据库。 这是包含用于跨所有租户数据库进行查询的架构的头数据库。 该数据库部署到现有的编录服务器中，后者是用于在示例应用中所有管理相关数据库的服务器。

1. 在 *PowerShell ISE* 中打开 .\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* 并设置以下值：
   * **$DemoScenario** = 2，**部署即席分析数据库**。

2. 按 F5 运行脚本并创建 adhocanalytics 数据库。

在下一节中将架构添加到数据库，使其可用于运行分布式查询。

## <a name="configure-the-database-for-running-distributed-queries"></a>配置用于运行分布式查询的数据库

此练习将架构（外部数据源和外部表定义）添加到即席分析数据库，实现跨所有租户数据库进行查询。

1. 打开 SQL Server Management Studio，并连接到你在上一步中创建的即席分析数据库。 数据库的名称为 adhocanalytics。
2. 在 SSMS 中打开 ...\Learning Modules\Operational Analytics\Adhoc Analytics\Initialize-AdhocAnalyticsDB.sql。
3. 查看 SQL 脚本，注意以下内容：

   弹性查询使用数据库范围的凭据来访问每个租户数据库。 该凭据需要能够在所有数据库中使用，通常应向其授予启用这些即席查询所需的最小权限。

    ![创建凭据](media/sql-database-saas-tutorial-adhoc-analytics/create-credential.png)

   外部数据源，已定义为在编录数据库中使用租户分片映射。 将此用作外部数据源，这样就可以在运行查询时将查询分发到在目录中注册的所有数据库。 由于每个部署的服务器名称均不相同，因此，此初始化脚本通过检索执行脚本的当前服务器 (@@servername) 来获取目录数据库的位置。

    ![创建外部数据源](media/sql-database-saas-tutorial-adhoc-analytics/create-external-data-source.png)

   引用上一节中介绍的全局视图的外部表，通过 DISTRIBUTION = SHARDED(VenueId) 进行定义。 每个 VenueId 映射到单个数据库，这样就可以提升许多方案的性能，如下一节所示。

    ![创建外部表](media/sql-database-saas-tutorial-adhoc-analytics/external-tables.png)

   已创建并填充的本地表“VenueTypes”。 此引用数据表常见于所有租户数据库中，因此可以在此表示为本地表并使用通用数据填充。 对于某些查询，这可能会减少在租户数据库和 adhocanalytics 数据库之间移动的数据量。

    ![创建表](media/sql-database-saas-tutorial-adhoc-analytics/create-table.png)

   如果以这种方式加入引用表，请确保在每次更新租户数据库时更新表架构和数据。

4. 按 F5 运行脚本并初始化 adhocanalytics 数据库。 

现在就可以运行分布式查询，并收集所有租户的见解了！

## <a name="run-ad-hoc-distributed-queries"></a>运行即席分布式查询

设置 adhocanalytics 数据库后，请继续运行一些分布式查询。 其中包括执行计划，以便更好地了解查询进程发生的位置。 

检查执行计划时，将鼠标悬停在计划图标上方可获取详细信息。 

特别要注意是，定义外部数据源时的设置 DISTRIBUTION = SHARDED(VenueId) 可以提升许多方案的性能。 每个 VenueId 映射到单个数据库，这样就可以轻松地进行远程筛选，仅返回我们需要的数据。

1. 在 SSMS 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql*。
2. 确保已连接到 adhocanalytics 数据库。
3. 选择“查询”菜单，然后单击“包括实际的执行计划”
4. 突出显示“当前注册了哪些地点?”查询，然后按 F5。

   该查询会返回整个地点列表，说明跨所有租户进行查询并从每个租户返回数据的速度快慢和难易程度。

   检查计划会发现所有成本都与远程查询相关，因为我们只是转到每个租户数据库并选择地点信息。

   ![SELECT * FROM dbo.Venues](media/sql-database-saas-tutorial-adhoc-analytics/query1-plan.png)

5. 选择下一个查询，然后按 F5。

   此查询联接租户数据库和本地 VenueTypes 表（“本地”是因为它是 adhocanalytics 数据库中的表）的数据。

   检查计划会发现大部分成本是远程查询，因为我们查询每个租户的地点信息 (dbo.Venues)，然后与本地 VenueTypes 表进行快速本地联接，以显示友好名称。

   ![远程和本地数据联接](media/sql-database-saas-tutorial-adhoc-analytics/query2-plan.png)

6. 现在选择“哪天销售的票证最多?”查询，然后按 F5。

   此查询执行稍微有些复杂的联接和聚合操作。 需要注意的是，大部分处理过程远程完成，并且再强调一次，我们只返回需要的行，即每天每个地点的总票证销售量仅返回一行内容。

   ![query](media/sql-database-saas-tutorial-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 对所有租户数据库运行分布式查询
> * 部署即席分析数据库并将架构添加到该数据库，以运行分布式查询。


现在尝试学习[租户分析教程](sql-database-saas-tutorial-tenant-analytics.md)，探索如何将数据提取到单独的分析数据库，从而进行更复杂的分析处理...

## <a name="additional-resources"></a>其他资源

* 其他[基于 Wingtip SaaS 应用程序编写的教程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [弹性查询](sql-database-elastic-query-overview.md)

