---
title: 在多租户应用中管理 Azure SQL 数据库架构 | Microsoft Docs
description: 在使用 Azure SQL 数据库的多租户应用程序中为多个租户管理架构
keywords: sql 数据库教程
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 474b7842c8295be0dd30c2c03b92dc68ce40630d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>在使用分片多租户 SQL 数据库的 SaaS 应用程序中管理架构

本教程探讨在软件即服务 (SaaS) 应用程序中维护大批量数据库时存在的挑战。 介绍有关跨大批量数据库规划架构更改的解决方案。

与任何应用程序一样，Wingtip Tickets SaaS 应用也会随时间不断改进，并且会不时需要对数据库进行更改。 这些更改可能会影响架构或引用数据，或需要实施数据库维护任务。 如果 SaaS 应用程序使用“每租户一个数据库”模式，则有可能必须在数目庞大的租户数据库中协调更改。 此外，必须将这些更改并入数据库预配过程，以确保新数据库在创建时便包含这些更改。

#### <a name="two-scenarios"></a>两种方案

本教程探讨以下两种方案：
- 为所有租户部署引用数据更新。
- 对包含引用数据的表格重新构建索引。

使用 Azure SQL 数据库的[弹性作业](sql-database-elastic-jobs-overview.md)功能跨租户数据库执行这些操作。 此外，还要针对“模板”租户数据库运行作业。 在 Wingtip Tickets 示例应用中，将复制此模板数据库，用于预配新租户数据库。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建作业代理。
> * 在多个租户数据库上执行 T-SQL 查询。
> * 更新所有租户数据库中的引用数据。
> * 在所有租户数据库中的表上创建索引。

## <a name="prerequisites"></a>先决条件

- 须已部署 Wingtip Tickets 多租户数据库应用：
    - 有关说明，请参阅第一篇教程，其中介绍了 Wingtip Tickets SaaS 多租户数据库应用：<br />[部署和浏览使用 Azure SQL 数据库的分片多租户应用程序](saas-multitenantdb-get-started-deploy.md)。
        - 在五分钟内可以完成部署过程。
    - 必须已安装 Wingtip 的分片多租户版本。 “独立”版本和“基于租户的数据库”版本不支持此教程中的操作。

- 必须已安装最新版本的 SQL Server Management Studio (SSMS)。 [下载并安装 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。

- 必须已安装 Azure PowerShell。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

> [!NOTE]
> 本教程使用的是有限预览版（[弹性数据库作业](sql-database-elastic-database-client-library.md)）中 Azure SQL 数据库服务的功能。 如果希望完成本教程，请将订阅 ID 提供给 *SaaSFeedback@microsoft.com*，并在邮件主题中注明“弹性作业预览版”。 收到订阅已启用的确认邮件后，即可[下载并安装最新的预发行作业 cmdlet](https://github.com/jaredmoo/azure-powershell/releases)。 此预览版的功能有限，如果有相关问题或者需要支持，请联系 *SaaSFeedback@microsoft.com*。

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 架构管理模式简介

此示例中使用的分片多租户数据库模型使租户数据库能够包含一个或多个租户。 此示例探索将多租户和单租户数据库混用，实现混合租户管理模式的潜在可能性。 管理这些数据库的更改可能会比较复杂。 [弹性作业](sql-database-elastic-jobs-overview.md)有利于大批量数据库的管理。 可以使用作业，以任务的形式安全可靠地针对一组租户数据库运行 T-SQL 脚本。 这些任务不需要用户交互或输入。 可以使用此方法跨应用程序中的所有租户部署对架构和常见引用数据所做的更改。 此外，还可以使用弹性作业来维护数据库的黄金模板副本。 该模板用于创建新租户，始终确保使用最新的架构和引用数据。

![屏幕](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>弹性作业有限预览版

有一个新版的弹性作业，该作业现为 Azure SQL 数据库的集成功能。 此新版弹性作业目前为有限预览版。 有限预览版目前支持使用 PowerShell 创建作业代理，同时支持使用 T-SQL 创建和管理作业。
> [!NOTE] 
> 本教程使用的是有限预览版（弹性数据库作业）中 SQL 数据库服务的功能。 如果希望完成本教程，请将订阅 ID 提供给 SaaSFeedback@microsoft.com，并在邮件主题中注明“Elastic Jobs Preview”。 收到订阅已启用的确认邮件后，即可下载并安装最新的预发行作业 cmdlet。 此预览版的功能有限，如果有相关问题或者需要支持，请联系 SaaSFeedback@microsoft.com。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>获取 Wingtip Tickets SaaS 多租户数据库应用程序源代码和脚本

可在 GitHub 上的 [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) 存储库中查看 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。 

## <a name="create-a-job-agent-database-and-new-job-agent"></a>创建作业代理数据库和新的作业代理

本教程要求使用 PowerShell 来创建作业代理数据库和作业代理。 与 SQL 代理使用的 MSDB 一样，作业代理使用 Azure SQL 数据库来存储作业定义、作业状态和历史记录。 创建作业代理后，即可立刻创建和监视作业。

1. 在 **PowerShell ISE** 中打开 *...\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1*。
2. 按 **F5** 运行脚本。

Demo-SchemaManagement.ps1 脚本调用 Deploy-SchemaManagement.ps1 脚本，目的是在编录服务器上创建名为 _jobagent_ 的数据库。 然后该脚本创建作业代理，将 _jobagent_ 数据库作为参数传递。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>创建一个将新的引用数据部署到所有租户的作业

#### <a name="prepare"></a>准备

每个租户数据库在 VenueTypes 表中包含一组地点类型。 每个地点类型用于定义可在某个地点托管的事件种类。 这些地点类型对应于在租户事件应用中看到的背景图。  在本练习中，将一个更新部署到所有数据库，以便添加两种额外的地点类型：“赛车”和“游泳俱乐部”。 

首先，查看每个租户数据库中包含的地点类型。 连接 SQL Server Management Studio (SSMS) 中的一个租户数据库，并检查 VenueTypes 表。  还可在通过数据库页访问的 Azure 门户的查询编辑器中查询此表。 

1. 打开 SSMS 并连接到租户服务器：*tenants1-dpt-&lt;user&gt;.database.windows.net*
1. 浏览到 tenants1-dpt-&lt;user&gt; 服务器上的 contosoconcerthall 数据库，查询 VenueTypes 表以确认“赛车”和“游泳俱乐部”不在结果列表中。



#### <a name="steps"></a>步骤

现在请创建一个作业，以通过添加两个新的地点类型来更新每个租户数据库中的 **VenueTypes** 表。

若要创建新作业，可以使用在 _jobagent_ 数据库中创建的作业集系统存储过程。 这些存储过程是在创建作业代理时创建的。

1. 在 SSMS 中，请连接到租户服务器：tenants1-mt-&lt;user&gt;.database.windows.net

2. 浏览到 tenants1 数据库。

3. 查询 *VenueTypes* 表以确认“赛车”和“游泳俱乐部”是否不在结果列表中。

4. 连接到目录服务器：*catalog-mt-&lt;user&gt;.database.windows.net*。

5. 连接到目录服务器中的 _jobagent_ 数据库。

6. 在 SSMS 中，打开 *...\\Learning Modules\\Schema Management\\DeployReferenceData.sql* 文件

7. 修改语句：set @User = &lt;user&gt;，并将其替换为部署 Wingtip 票证 SaaS 多租户数据库应用程序时所用的用户值。

8. 按 **F5** 运行脚本。

#### <a name="observe"></a>观察

在 *DeployReferenceData.sql* 脚本中观察以下项：

- **sp\_add\_target\_group** 创建目标组名称 *DemoServerGroup*，并将目标成员添加到该组。

- **sp\_add\_target\_group\_member** 添加以下项：
    - *server* 目标成员类型。
        - 这是包含租户数据库的 *tenants1-mt-&lt;user&gt;* 服务器。
        - 包含服务器指包括作业执行时存在的租户数据库。
    - 位于 catalog-mt-&lt;user&gt; 服务器上的模板数据库 (basetenantdb) 的 database 目标成员类型，
    - *database* 目标成员类型，用于包含本教程稍后使用的 *adhocreporting* 数据库。

- **sp\_add\_job** 创建名为“引用数据部署”的新作业。

- **sp\_add\_jobstep** 创建包含 T-SQL 命令文本的作业步骤，该文本用于更新引用表 VenueTypes。

- 脚本中的剩余视图显示存在的对象以及监视作业执行情况。 使用这些查询查看“生命周期”列中的状态值，确定何时作业完成。 该作业将更新租户数据库，并更新包含引用表的其他两个数据库。

在 SSMS 中，浏览到 *tenants1-mt-&lt;user&gt;* 服务器上的租户数据库。 查询 *VenueTypes* 表以确认“赛车”和“游泳俱乐部”现在是否已添加到表中。 地点类型的总计数应已增加两个。

## <a name="create-a-job-to-manage-the-reference-table-index"></a>创建管理引用表索引的作业

本练习在所有租户数据库上创建了一个作业用于根据引用表主键重建索引。 索引重建是一个典型的数据库管理操作，载入大量数据负载后，管理员可以运行该操作来提高性能。

1. 在 SSMS 中，连接到 catalog-mt-&lt;User&gt;.database.windows.net 服务器中的 _jobagent_ 数据库。

2. 在 SSMS 中，打开 *...\\Learning Modules\\Schema Management\\OnlineReindex.sql*。

3. 按 **F5** 运行脚本。

#### <a name="observe"></a>观察

在 *OnlineReindex.sql* 脚本中观察以下项：

* **sp\_add\_job** 创建一个名为 *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885* 的新作业。

* sp\_add\_jobstep 创建包含 T-SQL 命令文本的作业步骤，该文本用于更新索引。

* 脚本监视器作业执行中的剩余视图。 使用这些查询查看“生命周期”列中的状态值，确定何时作业成功地在目标组会员上完成。

## <a name="additional-resources"></a>其他资源

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [管理扩大的云数据库](sql-database-elastic-jobs-overview.md)
* [创建和管理扩大的云数据库](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
。
> * 创建作业代理跨多个数据库运行 T-SQL 作业
> * 更新所有租户数据库中的引用数据
> * 在所有租户数据库中的表上创建索引

接下来，试用 [临时报表教程] (saas-multitenantdb-adhoc-reporting.md) 了解如何跨租户数据库运行分布式查询。

