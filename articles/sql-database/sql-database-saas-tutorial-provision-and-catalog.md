---
title: "在使用 Azure SQL 数据库的多租户应用中预配新租户 | Microsoft Docs"
description: "了解如何在 Azure SQL 数据库多租户 SaaS 应用中预配和编录新租户"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: febf9d9f8a32a95ae61734cc2d3632bb74f6c8f5
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---
# <a name="learn-how-to-provision-and-catalog-new-tenants-and-register-them-in-the-catalog"></a>了解如何预配和编录新租户并将其注册在目录中

在本教程中，你将学习预配和目录 SaaS 模式，以及如何在 Wingtip SaaS 应用程序中实现它们。 创建并初始化新的租户数据库，并在应用程序的租户目录中注册它们。 目录是一个数据库，用于保留 SaaS 应用程序的许多租户与其数据之间的映射。 对于将应用程序请求定向到正确的数据库，目录起着重要作用。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 预配单个新租户，包括分步介绍如何实现此操作
> * 预配一批其他租户


若要完成本教程，请确保已完成了以下先决条件：

* Wingtip SaaS 应用已部署。 若要在五分钟内进行部署，请参阅[部署和浏览 Wingtip SaaS 应用程序](sql-database-saas-tutorial.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS 目录模式简介

在支持数据库的多租户 SaaS 应用程序中，了解每个租户的信息存储位置非常重要。 在 SaaS 目录模式下，目录数据库用来保存每个租户与其数据的存储位置之间的映射。 Wingtip SaaS 应用按数据库体系结构使用单租户，但无论使用多租户数据库还是单租户数据库，将租户到数据库的映射存储在目录中的基本模式都适用。

每个租户都分配有一个用于在目录中对其进行标识的密钥，该密钥映射到相应数据库的位置。 在 Wingtip SaaS 应用中，该密钥根据租户名称的哈希构成。 这允许应用程序 URL 的租户名称部分用于构造密钥。 也可以使用其他租户密钥方案。  

目录允许更改数据库的名称或位置，而对应用程序产生极小影响。  在多租户数据库模型中，这也适合于在数据库之间“移动”租户。  目录还可用于指示租户或数据库是否处于脱机状态，以便进行维护或其他操作。 [还原单个租户教程](sql-database-saas-tutorial-restore-single-tenant.md)中对此进行了探讨。

此外，目录（实际为 SaaS 应用程序的管理数据库）可以存储更多租户或数据库元数据，例如数据库的层或版本、架构版本、服务计划或提供给租户的 SLA，以及启用应用程序管理、客户支持或 devops 过程的其他信息。  

除了 SaaS 应用程序，该目录还可以启用数据库工具。  在 Wingtip SaaS 示例中，目录用于启用跨租户查询，这在[临时分析教程](sql-database-saas-tutorial-adhoc-analytics.md)中进行了探讨。 [架构管理](sql-database-saas-tutorial-schema-management.md)和[租户分析](sql-database-saas-tutorial-tenant-analytics.md)教程中探讨了跨数据库作业管理。 

在 Wingtip SaaS 应用中，目录通过使用[弹性数据库客户端库 (EDCL)](sql-database-elastic-database-client-library.md) 的“分片管理”功能来实现。 EDCL 允许应用程序创建、管理和使用支持数据库的分片映射。 分片映射包含分片（数据库）列表和密钥（租户）与数据库之间的映射。  在预配租户的过程中，可从应用程序或 PowerShell 脚本使用 EDCL 函数，以便在分片映射中创建条目，并且从应用程序使用 EDCL 函数有效连接到正确数据库。 EDCL 会缓存连接信息以尽量减少到达目录数据库的流量并提高应用程序速度。  

> [!IMPORTANT]
> 可以在目录数据库中访问映射数据，但请勿编辑这些数据！ 请仅使用弹性数据库客户端库 API 编辑映射数据。 直接操作映射数据有损坏目录的风险，不受支持。


## <a name="introduction-to-the-saas-provisioning-pattern"></a>SaaS 预配模式简介

在使用单个租户数据库模型的 SaaS 应用程序中载入新租户时，必须预配新租户数据库。  该数据库必须在正确的位置和服务层级创建，必须使用适当的架构和引用数据初始化，然后必须在相应租户密钥下的目录中进行注册。  

可使用不同方法预配数据库，这些方法可能包括执行 SQL 脚本、部署 bacpac 或复制“金色”模板数据库。  

你所使用的预配方法必须在总体架构管理策略中进行了解释，必须确保使用最新架构预配新数据库。  [架构管理教程](sql-database-saas-tutorial-schema-management.md)中对此进行了探讨。  

Wingtip SaaS 应用通过复制在目录服务器上部署的名为 basetenantdb 的金色数据库来预配新租户。  可使用脚本将预配集成到应用程序作为注册体验和/或支持的脱机的一部分。 本教程将探讨使用 PowerShell 进行预配。 预配脚本将复制 basetenantdb 以在弹性池中创建新的租户数据库，然后通过特定于租户的信息初始化该数据库，并在目录分片映射中注册该数据库。  在示例应用中，数据库是基于租户名称的给定名称，但这并非模式的关键部分 - 使用目录允许向数据库分配任何名称。+ 


## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip SaaS 脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存储库中找到。 [下载 Wingtip SaaS 脚本的步骤](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。


## <a name="provision-and-catalog-detailed-walkthrough"></a>预配和编录的详细演练

若要了解 Wingtip 应用程序如何实现新租户预配，请在预配租户时添加断点并单步执行工作流：

1. 打开 ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_，并设置以下参数：
   * **$TenantName** = 新地点的名称（例如 *Bushwillow Blues*）。
   * **$VenueType** = 预定义的地点类型之一：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer。
   * **$DemoScenario** = **1**，设置为 **1** 以*预配单个租户*。

1. 通过将光标放在 48 行（该行显示：New-Tenant `）的任意位置来添加断点，并按 **F9**。

   ![断点](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. 若要运行脚本，请按 **F5**。

1. 脚本执行在断点处停止之后，按 **F11** 单步执行代码。

   ![断点](media/sql-database-saas-tutorial-provision-and-catalog/debug.png)



使用“调试”菜单选项 **F10** 和 **F11** 逐过程或逐语句执行调用的函数，跟踪脚本的执行。 有关调试 PowerShell 脚本的详细信息，请参阅[有关使用和调试 PowerShell 脚本的提示](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)。


以下不是要明确执行的步骤，而是在调试脚本期间逐步执行的工作流的说明：

1. **导入 SubscriptionManagement.psm1** 模块，该模块包含用于登录 Azure 和选择所使用 Azure 订阅的函数。
1. **导入 CatalogAndDatabaseManagement.psm1** 模块，该模块通过[分片管理](sql-database-elastic-scale-shard-map-management.md)函数提供目录和租户级抽象。 这是一个重要的模块，它封装了目录模式的大部分，值得浏览。
1. **获取配置详细信息**。 逐语句执行 Get-Configuration（使用 F11）并了解如何指定应用配置。 资源名称和其他特定于应用的值在此处定义，但请勿更改这些值中的任何一个，除非已熟悉脚本。
1. **获取目录对象**。 单步执行 Get-Catalog，其撰写并返回在更高级别的脚本中使用的目录对象。  此函数使用从“AzureShardManagement.psm1”导入的分片管理功能。 目录对象由以下内容组成：
   * $catalogServerFullyQualifiedName 是使用标准主干加上用户名构造而成的：_catalog-\<user\>.database.windows.net_。
   * $catalogDatabaseName 从以下配置检索而来：*tenantcatalog*。
   * $shardMapManager 对象是从目录数据库进行初始化的。
   * $shardMap 对象是从目录数据库中的 *tenantcatalog*分片映射进行初始化的。
   目录对象会被组成并返回，并在更高级别的脚本中使用。
1. **计算新租户密钥**。 哈希函数用于从租户名称创建租户密钥。
1. **检查租户密钥是否已存在**。 目录将受到检查以确保密钥可用。
1. **租户数据库使用 New-TenantDatabase 进行预配。** 使用 **F11** 逐语句执行脚本并了解该数据库是如何使用 [Azure 资源管理器模板](../azure-resource-manager/resource-manager-template-walkthrough.md)进行预配的。

数据库名称是从租户名称构造的，可使用户清楚地知道哪个分片属于哪个租户。 （可轻松使用用于数据库命名的其他策略）+资源管理器模板用于通过复制目录服务器上的黄金数据库 (baseTenantDB) 创建租户数据库。 另一种方法可以是创建一个空数据库，并通过导入 bacpac 对该数据库进行初始化，或从众所周知的位置执行初始化脚本。  

资源管理器模板位于 …\Learning Modules\Common\ 文件夹中：tenantdatabasecopytemplate.json

租户数据库在创建之后将进一步“使用地点（租户）名称和地点连接类型进行初始化”。 其他初始化也可以在此处执行。

“租户数据库”是使用租户密钥通过 Add-TenantDatabaseToCatalog 注册到目录中的。 使用 **F11** 可逐步了解详细信息：

* 目录数据库将添加到分片映射（已知数据库的列表）。
* 将密钥值链接到该分片的映射会被创建。
* 有关租户的其他元数据（地点的名称）会被添加到目录中的“租户”表。  “租户”表不是 ShardManagement 架构的一部分，且不由 EDCL 安装。  此表说明了如何扩展目录数据库以支持其他特定于应用程序的数据。   


预配完成后，执行将返回到原始 Demo-ProvisionAndCatalog 脚本，这将在浏览器中打开新租户的“事件”页：

   ![活动](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>预配一批租户

本练习预配一批 17 个租户。 建议在开始其他 Wingtip SaaS 教程之前预配此批租户，以便可以使用更多数据库。

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\ProvisionAndCatalog\\Demo-ProvisionAndCatalog.ps1，并将 $DemoScenario 参数更改为 3：
   * **$DemoScenario** = **3**，更改为 **3** 以*预配一批租户*。
1. 按 **F5** 运行脚本。

该脚本部署一批其他租户。 它使用 [Azure 资源管理器模板](../azure-resource-manager/resource-manager-template-walkthrough.md) 控制该批租户，将每个数据库的预配委托给链接的模板。 以这种方式使用模板允许 Azure 资源管理器中转脚本的预配过程。 模板尽可能以并行方式预配数据库，并在必要时处理重试，从而优化整个过程。 该脚本是幂等的，因此如果由于任何原因，它失败或停止，请重新运行它。

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>验证该批租户是否已成功部署

* 通过浏览到 [Azure 门户](https://portal.azure.com)中的服务器列表打开 *tenants1* 服务器，单击“SQL 数据库”，并验证该批 17 个附加数据库现在是否在列表中：

   ![数据库列表](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>其他预配模式

此教程中未包含的其他预配模式包括：

**预先预配数据库。** 预配模式利用了“弹性池中的数据库不会添加额外费用”这一事实。 计费针对弹性池（而不是数据库），空闲数据库不使用任何资源。 通过在池中预配数据库，然后在需要时分配它们，即可显著降低租户加入时间。 可以根据需要调整预先预配数据库的数目，以保留适合预期预配率的缓冲区。

**自动预配。** 在自动预配模式下，专用预配服务用于自动根据需要预配服务器、池和数据库，包括在弹性池中预配数据库（如果需要）。 并且，如果数据库已停用并已删除，可以根据需要预配服务来填充弹性池中的间隙。 此类服务可简单，也可复杂（例如，处理跨多个地理区域的预配），并且可以自动设置异地复制（如果该策略正用于灾难恢复）。 使用自动预配模式时，客户端应用程序或脚本会将预配请求提交到要由预配服务处理的队列，并会轮询该服务以确定完成。 如果使用预先预配，系统将使用在后台运行的、管理如何预配替换数据库的服务快速处理请求。



## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 预配单个新租户
> * 预配一批其他租户
> * 逐步了解预配租户以及将它们注册到目录的详细信息

试着学习[性能监视教程](sql-database-saas-tutorial-performance-monitoring.md)。

## <a name="additional-resources"></a>其他资源

* 其他[基于 Wingtip SaaS 应用程序编写的教程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [弹性数据库客户端库](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [如何在 Windows PowerShell ISE 中调试脚本](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

