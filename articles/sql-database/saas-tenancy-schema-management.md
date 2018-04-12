---
title: 在多租户应用中管理 Azure SQL 数据库架构 | Microsoft Docs
description: 在使用 Azure SQL 数据库的多租户应用程序中为多个租户管理架构
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 12d8f3cc9fa4ec75f247b6ed602e120b0302be0e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>通过将“租户各有数据库”模式与 Azure SQL 数据库配合使用，在 SaaS 应用程序中管理架构

随着数据库应用程序的发展，不可避免地需要对数据库架构或引用数据进行更改。  此外，也需要定期执行数据库维护任务。 管理使用“租户各有数据库”模式的应用程序时，需要对一系列租户数据库应用这些更改或维护任务。

本教程探讨两个方案 - 为所有租户部署引用数据更新，以及重新生成包含引用数据的表的索引。 可以使用[弹性作业](sql-database-elastic-jobs-overview.md)功能，在所有租户数据库上以及用于创建新租户数据库的模板数据库上执行这些操作。

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 创建作业代理
> * 使 T-SQL 作业在所有租户数据库上运行
> * 更新所有租户数据库中的引用数据
> * 在所有租户数据库中的表上创建索引


若要完成本教程，请确保满足以下先决条件：

* 已部署 Wingtip Tickets SaaS Database Per Tenant 应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS 租户各有数据库应用程序](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 已安装最新版的 SQL Server Management Studio (SSMS)。 [下载并安装 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> 本教程使用的是有限预览版（弹性数据库作业）中 SQL 数据库服务的功能。 如果希望完成本教程，请将订阅 ID 提供给 SaaSFeedback@microsoft.com，并在邮件主题中注明“Elastic Jobs Preview”。 收到订阅已启用的确认邮件后，即可[下载并安装最新的预发行作业 cmdlet](https://github.com/jaredmoo/azure-powershell/releases)。 此预览版的功能有限，如果有相关问题或者需要支持，请联系 SaaSFeedback@microsoft.com。

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 架构管理模式简介

“租户各有数据库”模式可以对租户数据进行有效的隔离，但会增加需要管理和维护的数据库的数目。 [弹性作业](sql-database-elastic-jobs-overview.md)有利于 SQL 数据库的管理。 可以使用作业安全可靠地针对一组数据库运行任务（T-SQL 脚本）。 作业可以跨应用程序中的所有租户数据库部署架构和常见的引用数据更改。 此外还可以通过弹性作业来维护数据库用来创建新租户的模板，确保其架构和引用数据始终为最新。

![屏幕](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>弹性作业有限预览版

有一个新版的弹性作业，该作业现为 Azure SQL 数据库的集成功能。 此新版弹性作业目前为有限预览版。 此有限预览版目前支持使用 PowerShell 创建作业代理，同时支持使用 T-SQL 创建和管理作业。

> [!NOTE]
> 本教程使用的是有限预览版（弹性数据库作业）中 SQL 数据库服务的功能。 如果希望完成本教程，请将订阅 ID 提供给 SaaSFeedback@microsoft.com，并在邮件主题中注明“Elastic Jobs Preview”。 收到订阅已启用的确认邮件后，即可[下载并安装最新的预发行作业 cmdlet](https://github.com/jaredmoo/azure-powershell/releases)。 此预览版的功能有限，如果有相关问题或者需要支持，请联系 SaaSFeedback@microsoft.com。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>获取 Wingtip Tickets SaaS“租户各有数据库”应用程序脚本

[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 存储库提供了应用程序源代码和管理脚本。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="create-a-job-agent-database-and-new-job-agent"></a>创建作业代理数据库和新的作业代理

本教程要求使用 PowerShell 来创建作业代理及其充当支持的作业代理数据库。 作业代理数据库存储作业定义、作业状态和历史记录。 创建作业代理及其数据库后，即可立刻创建和监视作业。

1. 在 PowerShell ISE 中打开 …\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1。
1. 按 **F5** 运行脚本。

Demo-SchemaManagement.ps1 脚本调用 Deploy-SchemaManagement.ps1 脚本，目的是在编录服务器上创建名为 *osagent* 的 SQL 数据库。 然后该脚本创建作业代理，将数据库用作参数。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>创建一个将新的引用数据部署到所有租户的作业

在 Wingtip Tickets 应用中，每个租户数据库都包含一组支持的地点类型。 每个地点都有一个具体的地点类型，用于定义可以主持的活动的种类，并且决定了在应用中使用的背景图像。 如果需要应用程序支持新的活动类型，必须更新此引用数据并添加新的地点类型。  在本练习中，将一个更新部署到所有租户数据库，以便添加两种额外的地点类型：“赛车”和“游泳俱乐部”。

首先，查看每个租户数据库中包含的地点类型。 连接 SQL Server Management Studio (SSMS) 中的一个租户数据库，并检查 VenueTypes 表。  还可在通过数据库页访问的 Azure 门户的查询编辑器中查询此表。 

1. 打开 SSMS 并连接到租户服务器：*tenants1-dpt-&lt;user&gt;.database.windows.net*
1. 浏览到 tenants1-dpt-&lt;user&gt; 服务器上的 contosoconcerthall 数据库，查询 VenueTypes 表以确认“赛车”和“游泳俱乐部”不在结果列表中。

现在请创建一个作业，对所有租户数据库中的“VenueTypes”表进行更新，以便添加新的地点类型。

若要创建新的 作业，请使用一组作业系统存储过程，这些过程是在创建作业代理时，在 _jobagent_ 数据库中创建的。

1. 在 SSMS 中连接到编录服务器：*catalog-dpt-&lt;user&gt;.database.windows.net* 服务器 
1. 在 SSMS 中，打开 …\\Learning Modules\\Schema Management\\DeployReferenceData.sql 文件
1. 修改语句：SET @wtpUser = &lt;user&gt;，并将其替换为部署 Wingtip Tickets SaaS Database Per Tenant 应用时所用的“用户”值
1. 确保已连接到 _jobagent_ 数据库，然后按 **F5** 运行该脚本

在 *DeployReferenceData.sql* 脚本中观察以下元素：
* **sp\_add\_target\_group** 创建目标组名称 DemoServerGroup。
* **sp\_add\_target\_group\_member** 用于定义目标数据库集。  首先添加 _tenants1-dpt-&lt;user&gt;_ 服务器。  将服务器添加为目标后，就会在执行作业时将该服务器中的数据库包括到作业中。 然后会将 _basetenantdb_ 数据库和 *adhocreporting* 数据库（在后面的教程中使用）作为目标添加。
* **sp\_add\_job** 创建名为“引用数据部署”的新作业。
* **sp\_add\_jobstep** 创建包含 T-SQL 命令文本的作业步骤，该文本用于更新引用表 VenueTypes。
* 脚本中的剩余视图显示存在的对象以及监视作业执行情况。 使用这些查询查看“生命周期”列中的状态值，确定何时作业在所有目标数据库上完成。

完成脚本后，可以验证引用数据是否已更新。  在 SSMS 中浏览到 *tenants1-dpt-&lt;user&gt;* 服务器上的 *contosoconcerthall* 数据库，然后查询 *VenueTypes* 表。  查看“赛车”和“游泳俱乐部”现在是否存在。


## <a name="create-a-job-to-manage-the-reference-table-index"></a>创建管理引用表索引的作业

本练习通过一个作业根据引用表主键重建索引。  这是一项典型的数据库维护操作，可以在加载大量数据后执行。

使用相同的作业“系统”存储过程创建作业。

1. 打开 SSMS 并连接到 _catalog-dpt-&lt;user&gt;.database.windows.net_ 服务器
1. 打开 _…\\Learning Modules\\Schema Management\\OnlineReindex.sql_ 文件
1. 通过右键单击选择“连接”，连接到 _catalog-dpt-&lt;user&gt;.database.windows.net_ 服务器（如果尚未连接）
1. 确保已连接到 _jobagent_ 数据库，然后按 **F5** 运行该脚本

在 _OnlineReindex.sql_ 脚本中观察以下元素：
* sp\_add\_job 创建一个名为“Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”的新作业
* sp\_add\_jobstep 创建包含 T-SQL 命令文本的作业步骤，以更新索引
* 脚本监视器作业执行中的剩余视图。 使用这些查询查看“生命周期”列中的状态值，确定何时作业成功地在目标组会员上完成。



## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 创建作业代理跨多个数据库运行 T-SQL 作业
> * 更新所有租户数据库中的引用数据
> * 在所有租户数据库中的表上创建索引

接下来，请试用[临时报表教程](saas-tenancy-cross-tenant-reporting.md)，了解如何跨租户数据库运行分布式查询。


## <a name="additional-resources"></a>其他资源

* [构建 Wingtip Tickets SaaS Database Per Tenant 应用程序部署的其他教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [管理扩大的云数据库](sql-database-elastic-jobs-overview.md)
* [创建和管理扩大的云数据库](sql-database-elastic-jobs-create-and-manage.md)