---
title: 在使用 Azure SQL 数据库的多租户应用中预配新租户 | Microsoft Docs
description: 了解如何在 Azure SQL 数据库多租户 SaaS 应用中预配和编录新租户
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 7c526f65be5e4a3ea50de4603441e6184abf8edd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643611"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>了解如何预配新租户并将其注册到目录中

本教程介绍如何预配和编录 SaaS 模式。 此外，介绍如何在 Wingtip Tickets SaaS database-per-tenant 应用程序中实现这些模式。 我们将创建并初始化新的租户数据库，并在应用程序的租户目录中注册它们。 目录是一个数据库，用于保留 SaaS 应用程序的许多租户与其数据之间的映射。 在将应用程序和管理请求定向到正确的数据库的过程中，目录起着重要的作用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 预配单个新租户。
> * 预配一批其他租户。


若要完成本教程，请确保已完成了以下先决条件：

* 已部署了 Wingtip Tickets SaaS“每租户一个数据库”应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS database-per-tenant 应用程序](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS 目录模式简介

在支持数据库的多租户 SaaS 应用程序中，了解每个租户的信息存储位置非常重要。 在 SaaS 目录模式下，目录数据库用来保存每个租户与存储其数据的数据库之间的映射。 此模式适用于租户数据分布在多个数据库的情况。

每个租户由目录中的密钥标识，该密钥映射到其数据库的位置。 在 Wingtip Tickets 应用中，该密钥根据租户名称的哈希构成。 此方案允许应用从应用程序 URL 中包含的租户名构造键。 可使用其他租户密钥方案。  

目录允许更改数据库的名称或位置，而对应用程序产生极小影响。 在多租户数据库模型中，此功能也适合于在数据库之间移动租户。 目录还可用于指示租户或数据库是否处于脱机状态，以便进行维护或其他操作。 [还原单个租户教程](saas-dbpertenant-restore-single-tenant.md)中对此功能进行了探讨。

该目录还存储其他租户或数据库元数据，如架构版本、服务计划或提供给租户的 SLA。 该目录可以存储其他在进行应用程序管理、客户支持或 DevOps 时所需的信息。 

除了 SaaS 应用程序，该目录还可以启用数据库工具。 在 Wingtip Tickets SaaS database-per-tenant 示例中，目录用于启用跨租户查询，这在[临时报表教程](saas-tenancy-cross-tenant-reporting.md)中进行过探讨。 [架构管理](saas-tenancy-schema-management.md)和[租户分析](saas-tenancy-tenant-analytics.md)教程中探讨了跨数据库作业管理。 

在 Wingtip Tickets SaaS 示例中，目录通过使用[弹性数据库客户端库 (EDCL)](sql-database-elastic-database-client-library.md) 的“分片管理”功能来实现。 EDCL 在 Java 和 .NET Framework 中可用。 EDCL 允许应用程序创建、管理和使用支持数据库的分片映射。 

分片映射包含分片（数据库）列表和密钥（租户）与分片之间的映射。 EDCL 函数用于在预配租户的过程中在分片映射中创建条目。 在运行时，应用程序使用这些函数连接到正确的数据库。 EDCL 会缓存连接信息以尽量减少到达目录数据库的流量并提高应用程序速度。 

> [!IMPORTANT]
> 可在目录数据库中访问映射数据，但请勿编辑这些数据。 请仅使用弹性数据库客户端库 API 编辑映射数据。 直接操作映射数据有损坏目录的风险，不受支持。


## <a name="introduction-to-the-saas-provisioning-pattern"></a>SaaS 预配模式简介

在使用单个租户数据库模型的 SaaS 应用程序中添加新租户时，必须预配新租户数据库。 该数据库必须在正确的位置和服务层级创建。 它还必须使用适当的架构和参考数据初始化。 它必须在相应租户密钥下的目录中进行注册。 

可以使用不同方法预配数据库。 可以执行 SQL 脚本、部署 bacpac 或复制模板数据库。 

数据库预配需成为架构管理策略的一部分。 必须确保使用最新架构预配新数据库。 [架构管理教程](saas-tenancy-schema-management.md)中对此要求进行了探讨。 

Wingtip Tickets 的“每租户一个数据库”应用通过复制在目录服务器上部署的名为 _basetenantdb_ 的模板数据库来预配新租户。 可使用脚本将预配集成到应用程序作为注册体验的一部分。 也可以使用脚本对它进行脱机支持。 本教程将探讨如何使用 PowerShell 进行预配。 

预配脚本将复制 _basetenantdb_ 数据库，以在弹性池中创建新的租户数据库。 将在映射到 _newtenant_ DNS 别名的租户服务器中创建租户数据库。 此别名维持对用于预配新租户的服务器的引用，并且已更新以指向灾难恢复教程（[使用异地还原进行灾难恢复](saas-dbpertenant-dr-geo-restore.md)，[使用 异地复制进行灾难恢复](saas-dbpertenant-dr-geo-replication.md)）中的恢复租户服务器。 然后通过特定于租户的信息初始化该数据库，并在目录分片映射中注册该数据库。 租户数据库的名称基于租户名称。 此命名方案不是该模式的关键部分。 目录会将租户密钥映射到数据库名，因此可以使用任何命名约定。 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>获取 Wingtip Tickets SaaS“每租户一个数据库”应用程序脚本

[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 存储库提供了 Wingtip Tickets SaaS 脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。


## <a name="provision-and-catalog-detailed-walkthrough"></a>预配和编录的详细演练

若要了解 Wingtip Tickets 应用程序如何实现新租户预配，请在预配租户时添加断点并单步执行工作流。

1. 在 PowerShell ISE 中，打开 ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_，并设置以下参数：

   * **$TenantName** = 新地点的名称（例如 *Bushwillow Blues*）。
   * **$VenueType** = 预定义的地点类型之一：_blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer_。
   * **$DemoScenario** = **1**，预配单租户。

2. 通过将游标置于显示有“New-Tenant `”的行中的任意位置来添加断点。 然后按 F9。

   ![断点](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. 若要运行脚本，请按 F5。

4. 脚本执行在断点处停止之后，按 F11 单步执行代码。

   ![调试](media/saas-dbpertenant-provision-and-catalog/debug.png)



使用“调试”菜单选项跟踪脚本的执行。 按 F10 和 F11 逐步执行调用的函数， 有关调试 PowerShell 脚本的详细信息，请参阅[有关使用和调试 PowerShell 脚本的提示](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)。


无需显式遵循此工作流。 此文档解释了如何调试脚本。

* **导入 CatalogAndDatabaseManagement.psm1 模块。** 该模块通过[分片管理](sql-database-elastic-scale-shard-map-management.md)函数提供目录和租户级抽象。 此模块封装目录模式的大部分，值得浏览。
* **导入 SubscriptionManagement.psm1 模块。** 该模块包含用于登录 Azure 和选择所使用 Azure 订阅的函数。
* **获取配置详细信息。** 使用 F11 逐步执行 Get-Configuration 并了解如何指定应用配置。 资源名称和其他特定于应用的值在此处定义。 请勿更改这些值，除非已熟悉脚本。
* **获取目录对象。** 逐步执行 Get-Catalog，其撰写并返回在更高级别的脚本中使用的目录对象。 此函数使用从“AzureShardManagement.psm1”导入的分片管理功能。 目录对象由以下元素组成：

   * $catalogServerFullyQualifiedName 是使用标准主干加上用户名构造而成的：_catalog-\<user\>.database.windows.net_。
   * $catalogDatabaseName 从以下配置检索而来：*tenantcatalog*。
   * $shardMapManager 对象是从目录数据库进行初始化的。
   * $shardMap 对象是从目录数据库中的 _tenantcatalog_分片映射进行初始化的。 目录对象会被撰写并返回， 并在更高级别的脚本中使用。
* **计算新租户密钥。** 哈希函数用于从租户名称创建租户密钥。
* **检查租户密钥是否已存在。** 目录将受到检查以确保密钥可用。
* **租户数据库使用 New-TenantDatabase 进行预配。** 使用 F11 逐步执行脚本并了解该数据库是如何使用 [Azure 资源管理器模板](../azure-resource-manager/resource-manager-template-walkthrough.md)进行预配的。

    数据库名称是从租户名称构造的，可使用户清楚地知道哪个分片属于哪个租户。 也可使用其他数据库命名约定。 资源管理器模板通过复制目录服务器上的模板数据库 (_baseTenantDB_) 来创建租户数据库。 另一种方法是创建一个数据库，并通过导入 bacpac 对该数据库进行初始化。 或者从已知的位置执行初始化脚本。

    资源管理器模板位于 …\Learning Modules\Common\ 文件夹中：tenantdatabasecopytemplate.json

* **租户数据库进一步初始化。** 已添加地点（租户）名称和地点类型。 其他初始化也可以在此处执行。

* **租户数据库已在目录中注册。** 它是使用租户密钥通过 *Add-TenantDatabaseToCatalog* 注册的。 使用 F11 逐步了解详细信息：

    * 目录数据库将添加到分片映射（已知数据库的列表）。
    * 将密钥值链接到该分片的映射会被创建。
    * 有关租户的其他元数据（地点的名称）会添加到目录中的“租户”表。 “租户”表不是分片管理架构的一部分，且不由 EDCL 安装。 此表说明了如何扩展目录数据库以支持其他特定于应用程序的数据。


预配完成后，执行将返回到原始 *Demo-ProvisionAndCatalog* 脚本。 新租户的“事件”页会在浏览器中打开。

   ![“事件”页](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>预配一批租户

本练习预配一批 17 个租户。 我们建议在开始其他 Wingtip Tickets SaaS database-per-tenant 教程之前预配此批租户。 要使用的数据库很多。

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*。 将 *$DemoScenario* 参数更改为 3：

   * **$DemoScenario** = **3**，预配一批租户。
2. 若要运行脚本，请按 F5。

该脚本部署一批其他租户。 它使用 [Azure 资源管理器模板](../azure-resource-manager/resource-manager-template-walkthrough.md)控制该批租户，将每个数据库的预配委托给链接的模板。 以这种方式使用模板允许 Azure 资源管理器中转脚本的预配过程。 这些模板并行预配数据库，并在需要时进行重试。 该脚本是幂等的，因此如果由于任何原因，它失败或停止，请重新运行它。

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>验证该批租户是否已成功部署

* 在 [Azure 门户](https://portal.azure.com)中，浏览到服务器列表并打开 tenants1 服务器。 选择“SQL 数据库”，并验证一批 17 个额外数据库现已位于列表中。

   ![数据库列表](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>其他预配模式

此教程中未包含的其他预配模式：

**预先预配数据库**：预配模式利用了“弹性池中的数据库不会添加额外费用”这一事实。 计费针对弹性池而不是数据库。 空闲数据库不使用任何资源。 通过在池中预先预配数据库，然后在需要时分配它们，可以显著降低租户加入时间。 可以根据需要调整预先预配数据库的数目，以保留适合预期预配率的缓冲区。

**自动预配**：在自动预配模式下，某个预配服务会自动根据需要预配服务器、池和数据库。 如果需要，可以包括弹性池中的预先预配数据库。 如果数据库已停用并已删除，则可以通过预配服务来填充弹性池中的间隙。 此类服务可简单，也可复杂（例如，处理跨多个地理区域的预配），并且可以设置异地复制以用于灾难恢复。 

使用自动预配模式时，客户端应用程序或脚本会将预配请求提交到要由预配服务处理的队列。 然后它会轮询该服务以确定完成。 如果使用预先预配，则请求将能够快速得到处理。 服务会在后台预配替换数据库。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 预配单个新租户。
> * 预配一批其他租户。
> * 逐步了解预配租户以及将它们注册到目录的详细信息。

试着学习[性能监视教程](saas-dbpertenant-performance-monitoring.md)。

## <a name="additional-resources"></a>其他资源

* 其他[基于 Wingtip Tickets SaaS database-per-tenant 应用程序制作的教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [弹性数据库客户端库](sql-database-elastic-database-client-library.md)
* [在 Windows PowerShell ISE 中调试脚本](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
