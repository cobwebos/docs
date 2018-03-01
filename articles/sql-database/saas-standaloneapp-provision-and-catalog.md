---
title: "多租户 SaaS 教程 - Azure SQL 数据库 | Microsoft 文档"
description: "使用独立应用程序模式预配和编录新租户"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: SaaS
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billgib
ms.openlocfilehash: a13eeb79320360da078ee19a61cc32a2e1f35354
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>使用“每租户一个应用程序”的 SaaS 模式预配和编录新租户

本文介绍如何使用“每租户一个独立应用”的 SaaS 模式预配和编录新租户。
本文包括两个主要部分：
* 有关新租户预配和编录的概念介绍
* 重点介绍如何使用 PowerShell 代码示例实现预配和编录的教程
    * 该教程使用能够适应“每租户一个独立应用”模式的 Wingtip Tickets SaaS 示例应用程序。

## <a name="standalone-application-per-tenant-pattern"></a>“每租户一个独立应用程序”模式
“每租户一个独立应用”模式是多租户 SaaS 应用程序的诸多模式之一。  在此模式中，将为每个租户预配一个独立应用。 应用程序包含应用程序级组件和 SQL 数据库。  每个租户应用都可在供应商的订阅中进行部署。  或者，应用也可通过 Azure 提供的[托管应用程序计划](https://docs.microsoft.com/en-us/azure/managed-applications/overview)，在租户的订阅中进行部署，并由供应商代表租户进行管理。 

   ![“每租户一个应用”模式](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

为租户部署应用程序时，将在为租户创建的新的资源组中预配应用和数据库。  使用单独的资源组隔离每个租户的应用程序资源，并允许其独立进行管理。 在每个资源组中，每个应用程序实例都会被配置为直接访问其对应的数据库。  此连接模型与使用目录代理应用与数据库之间的连接的其他模式形成对比。  并且由于没有资源共享，因此为每个租户数据库预配的资源必需足够，以处理其峰值负载。 此模式用于具有较少租户的 SaaS 应用程序，其中重点强调租户隔离，而较少强调资源成本。  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>使用具有“每租户一个应用程序”模式的租户目录
尽管每个租户的应用和数据库已完全隔离，但各种管理和分析方案仍可跨租户运行。  例如，为新的应用程序版本应用架构更改需要更改每个租户数据库的架构。 报表和分析方案可能还需要访问所有租户数据库，无论这些数据库部署在何处都一样。

   ![“每租户一个应用”模式](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

租户目录保留租户标识符和租户数据库之间的映射，使标识符能够被解析为服务器和数据库名称。  在 Wingtip SaaS 应用中，租户标识符根据租户名称的哈希确定，但也可使用其他架构。  尽管独立应用程序不需要目录即可管理连接，但目录可用来将其他操作的范围限制为一组租户数据库。 例如，弹性查询可以使用目录确定通过其分配查询实现跨租户报告的一组数据库。

## <a name="elastic-database-client-library"></a>弹性数据库客户端库
在 Wingtip 示例应用程序中，目录通过使用[弹性数据库客户端库 (EDCL)](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) 的“分片管理”功能来实现。  该库允许应用程序创建、管理和使用存储在数据库中的分片映射。 在 Wingtip Tickets 示例中，目录存储在租户目录数据库中。  分片可将租户密钥映射到在其中存储租户数据的分片（数据库）中。  EDCL 函数管理存储在租户目录数据库的表格中的全局分片映射和存储在每个分片中的本地分片映射。

EDCL 函数通过应用程序或 PowerShell 脚本进行调用，以创建和管理分片映射中的条目。 其他 EDCL 函数用于检索分片组或连接到给定租户密钥的正确数据库。 
    
> [!IMPORTANT] 
> 请勿直接编辑目录数据库中的数据或租户数据库中的本地分片映射。 直接更新操作存在较高的数据损坏风险，因此不受支持。 请仅使用 EDCL API 编辑映射数据。

## <a name="tenant-provisioning"></a>租户预配 
每个租户都需要新的 Azure 资源组，必须先创建该资源组，然后才能在其中预配资源。 创建资源组后，可使用 Azure 资源管理模板部署应用程序组件和数据库，然后配置数据库连接。 若要初始化数据库架构，该模板可导入 bacpac 文件。  或者，可将数据库创建为“模板”数据库的副本。  然后，使用初始场地数据进一步更新数据库，并在目录中注册。

## <a name="tutorial"></a>教程

本教程介绍如何执行下列操作：
* 预配目录
* 注册以前在目录中部署的示例租户数据库
* 预配另一个租户并在目录中注册

Azure 资源管理器模板可用于部署和配置应用程序，创建租户数据库，然后导入 bacpac 文件进行初始化。 导入请求可能需要排队等待几分钟，然后才能进行操作。

在本教程末尾，你将具有一组独立的租户应用程序，且已在目录中注册每个数据库。

## <a name="prerequisites"></a>先决条件
若要完成本教程，请确保已完成了以下先决条件： 
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 部署三个示例租户应用。 若要在五分钟内完成这些部署，请参阅[部署和浏览 Wingtip Tickets SaaS 独立应用程序模式](https://docs.microsoft.com/en-us/azure/sql-database/saas-standaloneapp-get-started-deploy)。

## <a name="provision-the-catalog"></a>预配目录
在此任务中，你将了解如何预配用来注册所有租户数据库的目录。 将能够： 
* 使用 Azure 资源管理模板预配目录数据库。 通过导入 bacpac 文件初始化数据库。  
* 注册以前部署的示例租户应用。  每个租户均使用通过租户名称的哈希构造的密钥进行注册。  租户名称也存储在目录的扩展表中。

1. 在 PowerShell ISE 中，打开 ...\Learning Modules\UserConfig.psm 并将 \<user\> 值更新为部署三个示例应用程序时所用的值。  保存文件。  
1. 在 PowerShell ISE 中打开 ...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1 并设置 $Scenario = 1。 部署租户目录并注册预定义的租户。

1. 通过将游标置于显示有“`& $PSScriptRoot\New-Catalog.ps1`”的行中的任意位置来添加断点，并按 F9。

    ![设置断点进行跟踪](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. 按 **F5** 运行脚本。 
1.  脚本执行在断点处停止之后，按 F11 单步执行 New-Catalog.ps1 脚本。
1.  使用“调试”菜单选项（F10 和 F11）逐过程或单步执行调用的函数，跟踪脚本的执行。
    *   有关调试 PowerShell 脚本的详细信息，请参阅[有关使用和调试 PowerShell 脚本的提示](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)。

脚本完成后，目录的创建也随即完成并且将注册所有示例租户。 

现在来看一看创建的资源。

1. 打开 [Azure 门户](https://portal.azure.com/)，并浏览资源组。  打开 wingtip-sa-catalog-\<user\> 资源组并查看目录服务器和数据库。
1. 打开门户中的数据库并从左侧菜单中选择“数据资源管理器”。  单击“登录”命令并输入密码 P@ssword1。


1. 浏览 tenantcatalog 数据库的架构。  
   * `__ShardManagement` 架构中的对象均通过弹性数据库客户端库提供。
   * `Tenants` 表和 `TenantsExtended` 视图是示例（演示如何扩展目录以提供其他值）中添加的扩展。
1. 运行查询 `SELECT * FROM dbo.TenantsExtended`。          

   ![数据资源管理器](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    作为使用数据资源管理器的替代方法，也可通过 SQL Server Management Studio 连接到数据库。 为此，请连接到服务器 wingtip- 

    
    请注意，不可直接编辑目录中的数据（应始终使用分片管理 API）。 

## <a name="provision-a-new-tenant-application"></a>预配新租户应用程序

在此任务中，你将了解如何预配单租户应用程序。 将能够：  
* 为租户创建新的资源组。 
* 使用 Azure 资源管理模板将应用程序和数据库预配到新的资源组中。  此操作包括通过导入 bacpac 文件，初始化具有常见架构和引用数据的数据库。 
* 使用基本租户信息初始化数据库。 此操作包括指定场地类型，以确定用作其事件网站背景的照片。 
* 在目录数据库中注册数据库。 

1. 在 PowerShell ISE 中打开 ...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1 并设置 $Scenario = 2。 部署租户目录并注册预定义的租户

1. 通过将游标置于显示有“`& $PSScriptRoot\New-TenantApp.ps1`”的行 49 中的任意位置来添加断点，并按 F9。
1. 按 **F5** 运行脚本。 
1.  脚本执行在断点处停止之后，按 F11 单步执行 New-Catalog.ps1 脚本。
1.  使用“调试”菜单选项（F10 和 F11）逐过程或单步执行调用的函数，跟踪脚本的执行。

预配租户后，随即会打开新租户的事件网站。

   ![红枫赛车](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

然后可在 Azure 门户中检查创建的新资源。 

   ![红枫赛车资源](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>删除资源组，停止计费 ##

完成了解示例后，删除创建的所有资源组以停止相关计费。

## <a name="additional-resources"></a>其他资源

- 若要了解有关多租户 SaaS 数据库应用程序的详细信息，请参阅[多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 如何部署 Wingtip Tickets SaaS 独立应用程序。
> * 关于构成该应用的服务器和数据库。
> * 如何删除示例资源以停止相关计费。

可使用“每租户一个数据库”版本的 [Wingtip Tickets SaaS 应用程序](https://docs.microsoft.com/en-us/azure/sql-database/saas-dbpertenant-wingtip-app-overview)了解如何使用目录支持各种跨租户方案。  
