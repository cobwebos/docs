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
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: bf003a3677ed27bc833de59ef61f7637a6899d37
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>对所有 Wingtip SaaS 租户运行即席分析查询

在本教程中，将创建即席分析数据库并对所有租户运行多个查询。 这些查询可以提取隐藏在 Wingtip SaaS 应用的日常操作数据中的信息。

为了运行即席分析查询（对多个租户），Wingtip SaaS 应用使用[弹性查询](sql-database-elastic-query-overview.md)以及分析数据库。


本教程介绍以下内容：

> [!div class="checklist"]

> * 每个数据库中用于跨租户查询的全局视图
> * 如何部署即席分析数据库
> * 如何对所有租户数据库运行分布式查询



若要完成本教程，请确保已完成了以下先决条件：

* Wingtip SaaS 应用已部署。 若要在五分钟内进行部署，请参阅[部署和浏览 Wingtip SaaS 应用程序](sql-database-saas-tutorial.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>即席分析模式

SaaS 应用程序提供的大好机会之一是使用集中存储在云中的大量租户数据。 使用这些数据来深入了解应用程序的操作和使用情况、租户、租户的用户及其首选项和行为等。这些见解可指导应用和服务的功能开发、可用性改进和其他投资。

在单个多租户数据库中访问此数据很简单，但当数据大规模分布在可能数千个数据库中时便不那么容易了。 一种方法是使用弹性查询，这样可以对具有常见架构的一组分布式数据库进行即席查询。 弹性查询使用单个头数据库，其中定义的外部表会镜像分布式（租户）数据库中的表或视图。 提交到此头数据库的查询将进行编译以生成分布式查询计划，其中的部分查询将根据需要向下推送到租户数据库。 弹性查询在目录数据库中使用分片映射提供租户数据库的位置。 设置和查询直接使用标准 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) 进行，并支持从 Power BI 和 Excel 等工具进行即席查询。

## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip SaaS 脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存储库中找到。 [下载 Wingtip SaaS 脚本的步骤](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts)。


## <a name="explore-the-global-views"></a>浏览全局视图

Wingtip SaaS 应用程序使用“一数据库一租户”模型生成，因此租户数据库架构是从单租户角度定义的。 特定于租户的信息存在于名为“Venue”的单个表中，该表始终只有一行，并且被进一步设计为堆栈，没有主键。  架构中的其他表不需与 Venue 表相关联，因为在正常使用时，数据属于哪个租户是没有任何疑问的。  但在跨所有数据库进行查询时，将数据库中表的数据关联到特定的租户变得很重要。 为了简化这一情况，可以向租户数据库添加一组视图，即提供每个租户的“全局”视图。 这些全局视图将一个租户 ID 投影到每个表中，以便进行全局查询。 这样就可以轻松标识来自每个租户的数据。 为了方便，已在所有租户数据库中预先创建了这些视图（此外还在 golden DB 中创建，以便在预配新租户时可以使用这些全局视图）。

1. 打开 SSMS 并[连接到 tenants1-&lt;USER&gt; 服务器](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
1. 展开“数据库”，右键单击“contosoconcerthall”，然后选择“新建查询”。
1. 运行以下查询，了解单租户表和全局视图的差异：

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

在示例数据库中，我们根据地点名称的哈希计算出了一个整数 ID，但你可以使用任何方法来引入唯一值。 这类似于在目录中创建租户密钥的方法，但不要求 adhocanalytics 数据库中的目录密钥和租户 ID 保持相同。

若要检查某个视图并查看其创建方式，请执行以下操作：

1. 在“对象资源管理器”中，展开“contosoconcethall” > “视图”：

   ![视图](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. 右键单击“dbo.Venues”。
1. 选择“将视图脚本编写为” > “CREATE TO” > “新建查询编辑器窗口”

对任何视图执行此操作即可检查其创建方式。

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>部署用于即席分析查询的数据库

本练习部署 adhocanalytics 数据库。 该数据库包含用于跨所有租户数据库进行查询的架构。 该数据库部署到现有的编录服务器中，后者是包含所有管理相关数据库的服务器。

1. 打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\Deploy-AdhocAnalyticsDB.ps1
1. 向下滚动到将 `$commandText` 分配给 SQL 脚本的节。 查看脚本，注意以下内容：

   1. 弹性查询使用数据库范围的凭据来访问每个租户数据库。 该凭据需要能够在所有数据库中使用，通常应向其授予启用这些即席查询所需的最小权限。
   1. 外部数据源，已定义为在编录数据库中使用租户分片映射。  用此作为外部数据源，就可以在发出查询时将查询分发到在编录中注册的所有数据库。
   1. 外部表，引用在上一部分介绍的全局视图。
   1. 已创建并填充的本地表“VenueTypes”。  由于此引用数据表在所有租户数据库中很常见，因此可以在这里将其作为本地表的代表。对于某些查询来说，它可以减少在租户数据库和 adhocanalytics 数据库之间移动的数据量。


1. 此时请在 PowerShell ISE 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\Demo-AdhocAnalytics.ps1 并设置以下值：
   * **$DemoScenario** = 2，**部署即席分析数据库**。

1. 按 F5 运行脚本并创建 adhocanalytics 数据库。

   可以忽略此处的“RPC 服务器不可用”的警告。

现在已有 *adhocanalytics* 数据库，可以使用它运行分布式查询，并对所有租户收集信息！

![adhocanalytics 数据库](media/sql-database-saas-tutorial-adhoc-analytics/adhocanalytics.png)

## <a name="run-ad-hoc-analytics-queries"></a>运行即席分析查询

设置 adhocanalytics 数据库后，请运行一些即席查询：

1. 在 SSMS 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql*。
1. 确保已连接到 **adhocanalytics** 数据库
1. 选择要运行的单个查询，并按 **F5**：

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 部署即席分析数据库
> * 对所有租户数据库运行分布式查询

现在尝试[“租户分析”教程](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>其他资源

* 其他[基于 Wingtip SaaS 应用程序编写的教程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [弹性查询](sql-database-elastic-query-overview.md)

