---
title: "在使用 Azure SQL 数据库的多租户应用中预配新租户 | Microsoft Docs"
description: "在 Wingtip SaaS 应用中预配和编录新租户"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: cf2fa0950f9f9df833051979b02355236214c4ea
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>预配新租户并在目录中对其进行注册

在本教程中，你将在 Wingtip SaaS 应用程序中预配新租户。 你将创建租户、租户数据库，并将在目录中注册租户。 目录是一个数据库，用于保留 SaaS 应用程序的许多租户与其数据之间的映射。 使用这些脚本可以了解使用的预配和编录模式以及如何实现在目录中注册新租户。 对于将应用程序请求定向到正确的数据库，目录起着重要作用。

本教程介绍如何：

> [!div class="checklist"]

> * 预配单个新租户
> * 预配一批其他租户
> * 逐步了解预配新租户以及将它们注册到目录的详细信息。


若要完成本教程，请确保已完成了以下先决条件：

* Wingtip SaaS 应用已部署。 若要在五分钟内进行部署，请参阅[部署和浏览 Wingtip SaaS 应用程序](sql-database-saas-tutorial.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS 目录模式简介

在支持数据库的多租户 SaaS 应用程序中，了解每个租户的信息存储位置非常重要。 在 SaaS 目录模式下，目录数据库用来保存租户与其数据的存储位置之间的映射。 Wingtip SaaS 应用使用单租户数据库体系结构，但无论使用多租户数据库还是单租户数据库，将租户到数据库的映射存储在编录中的基本模式都适用。

每个租户都分配有一个用于在目录中区分其数据的密钥。 在 Wingtip SaaS 应用程序中，该密钥根据租户名称的哈希构成。 此模式允许应用程序 URL 的租户名称部分用于构造密钥和检索特定租户的连接。 在不影响总体模式的情况下可以使用其他 ID 方案。

应用中的编录是使用[弹性数据库客户端库 (EDCL)](sql-database-elastic-database-client-library.md) 中的“分片管理”技术实现的。 EDCL 负责创建和管理支持数据库的目录，该目录中保留了分片映射。 该目录包含密钥（租户）与其数据库（分片）之间的映射。

> [!IMPORTANT]
> 可以在目录数据库中访问映射数据，但请勿编辑这些数据！ 请仅使用弹性数据库客户端库 API 编辑映射数据。 直接操作映射数据有损坏目录的风险，不受支持。

Wingtip SaaS 应用通过复制黄金数据库来预配新租户。

## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip SaaS 脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存储库中找到。 [下载 Wingtip SaaS 脚本的步骤](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts)。

## <a name="provision-a-new-tenant"></a>预配新租户

如果已在[第一个 Wingtip SaaS 教程](sql-database-saas-tutorial.md)中创建了租户，可以跳到下一部分：[预配一批租户](#provision-a-batch-of-tenants)。

运行 *Demo-ProvisionAndCatalog* 脚本以快速创建租户并将其注册在目录中：

1. 在 PowerShell ISE 中打开 **Demo-ProvisionAndCatalog.ps1**，然后设置以下值：
   * **$TenantName** = 新地点的名称（例如 *Bushwillow Blues*）。
   * **$VenueType** = 预定义的地点类型之一：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer。
   * **$DemoScenario** = 1，将此值保留为 _1_ 以**预配单个租户**。

1. 按 **F5** 运行脚本。

脚本执行完毕后，新租户已预配，并且其“活动”应用将在浏览器中打开：

![新租户](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>预配一批租户

本练习预配一批其他租户。 建议在完成其他 Wingtip SaaS 教程前执行此操作。

1. 在 *PowerShell ISE* 中打开 \\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1*，然后设置以下值：
   * **$DemoScenario** = **3**，设置为**3** 以**预配一批租户**。
1. 按 **F5** 运行脚本。

该脚本部署一批其他租户。 它使用 [Azure Resource Manager 模板](../azure-resource-manager/resource-manager-template-walkthrough.md) 控制该批租户，将每个数据库的预配委托给链接的模板。 以这种方式使用模板允许 Azure Resource Manager 中转脚本的预配过程。 模板尽可能以并行方式预配数据库，并在必要时处理重试，从而优化整个过程。 脚本是幂等的，因此，如果它中断，请再次运行它。

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>验证该批租户是否已成功部署

* 在 [Azure 门户](https://portal.azure.com)中打开 *tenants1* 服务器，然后单击“SQL 数据库”：

   ![数据库列表](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>预配和编录的详细演练

为了更好地了解 Wingtip 应用程序如何实现新租户预配，请再次运行 *Demo-ProvisionAndCatalog* 脚本并预配另一个租户。 这一次，添加断点并单步执行工作流：

1. 打开 ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ 并将以下变量设置为当前目录中不存在的新租户值：
   * **$TenantName** = 设置为新名称（例如 *Hackberry Hitters*）。
   * **$VenueType** = 使用预定义的地点类型之一（例如 *judo*）。
   * **$DemoScenario** = 1，设置为 **1** 以**预配单个租户**。

1. 通过将光标放在以下行的任意位置添加断点：*New-Tenant `*，然后按 **F9**。

   ![断点](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. 按 **F5** 运行脚本。 当达到断点时，按 **F11** 逐语句执行。 使用 **F10** 和 **F11** 逐过程或逐语句执行调用的函数，跟踪脚本的执行。 [有关使用和调试 PowerShell 脚本的提示](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>通过逐步调试脚本详细检查预配和编录实现

脚本通过执行以下步骤预配并编录新租户：

1. **导入 SubscriptionManagement.psm1** 模块，该模块包含用于登录 Azure 和选择所使用 Azure 订阅的函数。
1. **导入 CatalogAndDatabaseManagement.psm1** 模块，该模块通过[分片管理](sql-database-elastic-scale-shard-map-management.md)函数提供目录和租户级抽象。 这是一个重要的模块，它封装了目录模式的大部分，值得浏览。
1. **获取配置详细信息**。 逐语句执行 _Get-Configuration_（使用 **F11**）并了解如何指定应用配置。 资源名称和其他特定于应用的值在此处定义，但请勿更改这些值中的任何一个，除非已熟悉脚本。
1. **获取目录对象**。 逐语句执行 *Get-Catalog* 以了解目录是怎样使用从 **AzureShardManagement.psm1** 导入的分片管理函数进行初始化的。 目录由以下对象组成：
   * $catalogServerFullyQualifiedName 是使用标准主干加上用户名构造而成的：_catalog-\<user\>.database.windows.net_。
   * $catalogDatabaseName 从以下配置检索而来：*tenantcatalog*。
   * $shardMapManager 对象是从目录数据库进行初始化的。
   * $shardMap 对象是从目录数据库中的 *tenantcatalog*分片映射进行初始化的。
   目录对象将被组成并返回，并在更高级别的脚本中使用。
1. **计算新租户密钥**。 哈希函数用于从租户名称创建租户密钥。
1. **检查租户密钥是否已存在**。 目录将受到检查以确保密钥可用。
1. **租户数据库使用 New-TenantDatabase 进行预配。** 使用 **F11** 逐语句执行脚本并了解该数据库是如何使用 Resource Manager 模板进行预配的。
    
数据库名称是从租户名称构造的，可使用户清楚地知道哪个分片属于哪个租户。 （可以方便地使用其他用于数据库命名的策略。）

Resource Manager 模板用于通过**复制目录服务器上的黄金数据库 (baseTenantDB) 创建租户数据库**。  另一种方法可以是创建一个空数据库，然后通过导入 bacpac 对该数据库进行初始化。

Resource Manager 模板位于 ...\\Learning Modules\\Common\\ 文件夹中：*tenantdatabasecopytemplate.json*

租户数据库在创建之后将进一步使用地点（租户）名称和地点连接类型进行初始化。 其他初始化也可以在此处执行。

租户数据库是使用租户密钥通过 *Add-TenantDatabaseToCatalog* 注册到目录中的。 使用 **F11** 可逐步了解详细信息：

* 目录数据库将添加到分片映射（已知数据库的列表）。
* 将密钥值链接到该分片的映射会被创建。
* 有关租户的其他元数据（地点的名称）将被添加。

预配完成后，执行将返回到原始 *Demo-ProvisionAndCatalog* 脚本，新租户的“活动”页会在浏览器中打开：

   ![活动](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>其他预配模式

此教程中未包含的其他预配模式包括：

**预先预配数据库。** 此模式利用了以下事实：弹性池中的数据库不会添加额外成本（计费针对的是弹性池，而不是数据库），以及空闲数据库不消耗任何资源。 通过在池中预先预配数据库，然后在需要时分配它们，即可显著降低租户载入时间。 可以根据需要调整预先预配数据库的数目，以保留适合预期预配率的缓冲区。

**自动预配。** 在此模式下，专用预配服务用于自动根据需要预配服务器、池和数据库，包括在弹性池中预先预配数据库（如果需要）。 并且，如果数据库已停用并已删除，可以根据需要预配服务来填充弹性池中的间隙。 此类服务可简单，也可复杂（例如，处理跨多个地理区域的预配），并且可以自动设置异地复制（如果该策略正用于 DR）。 使用自动预配模式时，客户端应用程序或脚本会将预配请求提交到要由预配服务处理的队列，然后会轮询该服务以确定完成。 如果使用预先预配，系统将使用在后台运行的、管理如何预配替换数据库的服务快速处理请求。


## <a name="stopping-wingtip-saas-application-related-billing"></a>停止与 Wingtip SaaS 应用程序相关的计费

如果不打算继续使用另一个教程，建议删除所有资源以暂停计费。 删除 Wingtip 应用程序部署到的资源组，其所有资源也均将删除。

* 在门户中浏览到该应用程序的资源组，并将其删除以停止与此 Wingtip 部署相关的所有计费。

## <a name="tips"></a>提示

* EDCL 还提供了重要功能，使客户端应用程序可以连接到目录并操作目录。 还可以使用 EDCL 针对给定密钥值检索 ADO.NET 连接，从而使应用程序可以连接到正确的数据库。 客户端会缓存此连接信息以尽量减少到达目录数据库的流量并提高应用程序速度。



## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 预配单个新租户
> * 预配一批其他租户
> * 逐步了解预配新租户以及将它们注册到目录的详细信息。

[性能监视教程](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>其他资源

* 其他[基于 Wingtip SaaS 应用程序编写的教程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [弹性数据库客户端库](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [如何在 Windows PowerShell ISE 中调试脚本](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

