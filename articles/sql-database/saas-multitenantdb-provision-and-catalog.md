---
title: "在使用多租户 Azure SQL 数据库 SQL 数据库的 SaaS 应用程序中预配和编录新租户| Microsoft Docs"
description: "了解如何在 Azure SQL 数据库多租户 SaaS 应用中预配和编录新租户"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: e7de7bb545e0ce04dc1b3dd398cc920213d09bae
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>在使用分片多租户 SQL 数据库的 SaaS 应用程序中预配和编录新租户

本教程介绍在使用分片多租户数据库模型时预配和编录租户的模式。 

多租户架构在保存租户数据的表的主键中包含租户 ID，允许将多个租户存储在单个数据库中。 为了支持大量租户，租户数据分布在多个分片或数据库中。 任何一个租户的数据始终完全包含在单个数据库中。  目录用于保存到数据库的租户映射。   

还可选择仅使用单个租户填充某些数据库。 保存多个租户的数据库更倾向牺牲租户隔离来降低每个租户的成本。  只有保存单个租户的数据库更倾向隔离而非降低成本。  具有多个租户和单个租户的数据库可混合在同一个 SaaS 应用程序中，以优化每个租户的成本或隔离。 租户在预配时可以获得自己的数据库，也可以在稍后移动到自己的数据库中。

   ![包含租户目录的分片多租户数据库应用](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>租户目录模式

如果租户数据分布在多个数据库中，请务必了解每个租户的数据的存储位置。 目录数据库保存租户和存储其数据的数据库之间的映射。

每个租户都由目录中的密钥标识。 在 Wingtip Tickets SaaS 应用中，租户密钥由租户名称的哈希代码构成。 这样应用程序就可从网页 URL 中提取租户名称，并使用它连接到相应的数据库。 也可使用其他租户密钥方案。

通过目录，无需中断应用程序即可在预配之后更改租户数据库的名称或位置。  在多租户数据库模型中，这适合于在数据库之间移动租户。  目录还可用于向应用程序指示租户是否处于脱机状态，以便进行维护或其他操作。

可以扩展目录来存储更多租户或数据库元数据，例如数据库的层或版本、数据库架构版本、租户名称和服务计划或 SLA，以及启用应用程序管理、客户支持或 devops 进程的其他信息。  

目录还可用于实现跨租户报告、架构管理和数据提取以用于分析。 

### <a name="elastic-database-client-library"></a>弹性数据库客户端库 
在 Wingtip Tickets SaaS 应用中，目录是通过[弹性数据库客户端库 (EDCL)](sql-database-elastic-database-client-library.md) 的“分片管理”功能在 tenantcatalog 数据库中实现的。 库允许应用程序创建、管理和使用支持数据库的“分片映射”。 分片映射包含分片（数据库）列表以及密钥（租户 Id）和分片之间的映射。  在预配租户的过程中，可从应用程序或 PowerShell 脚本使用 EDCL 函数，以便在分片映射中创建条目，然后再连接到正确的数据库。 库会缓存连接信息以尽量减少到达目录数据库的流量并提高连接速度。 

> [!IMPORTANT]
> 可在目录数据库中访问映射数据，但请勿编辑这些数据！ 请仅使用弹性数据库客户端库 API 编辑映射数据。 直接操作映射数据有损坏目录的风险，不受支持。


## <a name="tenant-provisioning-pattern"></a>租户预配模式

在分片多租户数据库模型中预配新租户时，必须首先确定是将租户预配到共享数据库还是自己的数据库。 如果是共享数据库，则必须确定现有数据库中是否还有空间或是否需要新数据库。 如果需要新数据库，则必须在正确的位置和服务层级预配该数据库、使用适当的架构和参考数据进行初始化，然后在目录中进行注册。 最后，可添加租户映射来引用相应的分片。

通过执行 SQL 脚本、部署 bacpac 或复制模板数据库来预配此数据库。 Wingtip Tickets SaaS 应用程序复制模板数据库来创建新租户数据库。

数据库预配方法必须在总体架构管理策略中进行解释，需要确保使用最新架构预配新数据库。  [架构管理教程](saas-tenancy-schema-management.md)中对此进行了详细探讨。  

本教程中的租户预配脚本包括将租户预配到与其他租户共享的现有数据库中，以及将租户预配到其自己的数据库中。 随后在目录分片映射中初始化和注册租户数据。 在示例应用中，包含多个租户的数据库有一个通用名称，如 tenants1、tenants2 等；而包含单个租户的数据库名称则为租户的名称。 示例中使用的特定命名约定不是模式的主要部分，因为使用目录可以将任何名称分配给数据库。  

## <a name="provision-and-catalog-tutorial"></a>预配和编录教程

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 将租户预配到多租户数据库中
> * 将租户预配到单租户数据库中
> * 将一批租户同时预配到多租户和单租户数据库中
> * 在目录中注册数据库和租户映射

若要完成本教程，请确保已完成了以下先决条件：

* 已部署 Wingtip Tickets SaaS 多租户数据库应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS 多租户数据库应用程序](saas-multitenantdb-get-started-deploy.md)。
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>获取 Wingtip Tickets SaaS 多租户数据库应用程序源代码和脚本

在 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。 

## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>在拥有其他租户的共享数据库中预配租户

若要了解 Wingtip Tickets 应用程序如何在共享数据库中实现新租户预配，请添加断点并单步执行工作流：

1. 在 _PowerShell ISE_ 中，打开 ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_，并设置以下参数：
   * **$TenantName** = **Bushwillow Blues**，即新地点的名称。
   * **$VenueType** = **blues**（预定义的地点类型之一）：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer（小写，不含空格）。
   * **$DemoScenario** = **1**，可在拥有其他租户的共享数据库中预配租户。

1. 通过将游标置于 38 行（该行显示：New-Tenant `）的任意位置来添加断点，并按 F9。

   ![断点](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. 若要运行脚本，请按 **F5**。

1. 脚本执行在断点处停止之后，按 F11 单步执行代码。

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

使用“调试”菜单选项（F10 和 F11）逐过程或单步执行调用的函数，跟踪脚本的执行。 有关调试 PowerShell 脚本的详细信息，请参阅[有关使用和调试 PowerShell 脚本的提示](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)。


以下是单步执行的预配工作流的主要元素：

* **计算新租户密钥**。 哈希函数用于从租户名称创建租户密钥。
* **检查租户密钥是否已存在**。 检查目录，确保尚未注册密钥。
* **初始化默认租户数据库中的租户**。 更新租户数据库，添加新租户信息。  
* **在目录中注册租户**新租户密钥和现有 tenants1 数据库之间的映射将添加到目录中。 
* **向目录扩展表添加租户名称**。 向目录中的租户表添加地点名称。  此表说明如何扩展目录数据库来支持其他特定于应用程序的数据。
* **打开新租户的“事件”页**。 在浏览器中打开 Bushwillow Blues 事件页：

   ![活动](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>在其自己的数据库中预配租户

现在演练在自己的数据库中创建租户的过程：

1. 在 ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_ 中设置以下参数：
   * **$TenantName** = **Sequoia Soccer**，即新地点的名称。
   * **$VenueType** = **soccer**（预定义的地点类型之一）：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer（小写，不含空格）。
   * **$DemoScenario** = **2**，用于将租户预配到其自身的数据库。

1. 通过将游标置于 57 行（该行显示：&&nbsp;$PSScriptRoot\New-TenantAndDatabase `）的任意位置来添加新断点，并按 F9。

   ![断点](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. 按 **F5** 运行脚本。

1. 脚本执行在断点处停止之后，按 **F11** 单步执行代码。  使用 F10 和 F11 逐过程或单步执行函数来跟踪执行。

以下是跟踪脚本时所要单步执行的工作流程的主要元素：

* **计算新租户密钥**。 哈希函数用于从租户名称创建租户密钥。
* **检查租户密钥是否已存在**。 检查目录，确保尚未注册密钥。
* **创建新租户数据库**。 通过使用资源管理器模板复制 basetenantdb 数据库来创建此数据库。  新数据库名称取决于租户名称。
* **向目录添加数据库**。 新租户数据库在目录中注册为分片。
* **初始化默认租户数据库中的租户**。 更新租户数据库，添加新租户信息。  
* **在目录中注册租户**新的租户密钥和 sequoiasoccer 数据库之间的映射添加到目录中。
* **将租户名称添加到目录**。 将地点名称添加到目录的租户扩展表中。
* **打开新租户的“事件”页**。 在浏览器中打开 Sequoia Soccer 事件页：

   ![活动](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>预配一批租户

本练习预配一批 17 个租户。 建议在开始其他 Wingtip Tickets 教程之前预配这批租户，以便可以使用更多数据库。


1. 在 *PowerShell ISE* 中，打开 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*，并将 *$DemoScenario* 参数更改为 4：
   * **$DemoScenario** = **4**，可将一批租户预配到共享数据库中。
1. 按 **F5** 运行脚本。


### <a name="verify-the-deployed-set-of-tenants"></a>验证部署的一组租户 
在此阶段，将混合的租户部署到共享数据库中，并将租户部署到各自的数据库中。 Azure 门户可用于检查创建的数据库。 在 [Azure 门户](https://portal.azure.com)中，通过浏览到 SQL Server 列表来打开 tenants1-mt-\<USER\> 服务器。  SQL 数据库列表应包含共享的 tenants1 数据库和各自数据库中的租户的数据库：

   ![数据库列表](media/saas-multitenantdb-provision-and-catalog/Databases.png)

虽然 Azure 门户显示了租户数据库，但是不允许查看共享数据库内的租户。 可以在 Wingtip Tickets 事件中心页查看租户的完整列表，也可以通过浏览目录查看：   

**使用 Wingtip Tickets 事件中心页** <br>
在浏览器中打开事件中心页 (http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

**使用目录数据库** <br>
目录中提供租户及其相应数据库的完整列表。 tenantcatalog 数据库中提供 SQL 视图，该数据库将存储在“租户”表中的租户名称与“分片管理”表中的数据库名称连接起来。 该视图很好地展现了扩展存储在目录中的元数据的价值。

1. 在 *SQL Server Management Studio (SSMS)*，连接到位于 **catalog-mt.\<USER\>.database.windows.net** 的租户服务器，登录名：**developer**，密码：**P@ssword1**

    ![“SSMS 连接”对话框](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

1. 在对象资源管理器中，浏览到 tenantcatalog 数据库中的视图。
1. 右键单击“TenantsExtended”视图，然后选择“选中前 1000 行”。 注意租户名称和不同租户的数据库之间的映射。

    ![SSMS 中的“ExtendedTenants”视图](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>其他预配模式

其他相关设置模式包括：

**在弹性池中预先预配数据库。** 预配模式利用“使用弹性池时，计费针对弹性池而不针对数据库”这一事实。 因此，可以在需要前将数据库添加到弹性池中，而不会产生额外成本。 通过在池中预先预配数据库，然后在需要时分配它们，可显著降低预配租户到数据库中的时间。 可以根据需要调整预先预配数据库的数目，以保留适合预期预配率的缓冲区。

**自动预配。** 在自动预配模式下，专用预配服务用于自动根据需要预配服务器、池和数据库，包括在弹性池中预配数据库。 并且，如果数据库已停用并已删除，则可以根据需要预配服务来填充弹性池中创建的间隙。 此类服务可简单，也可复杂（例如，处理跨多个地理区域的预配），并且可以设置异地复制以用于灾难恢复。 使用自动预配模式时，客户端应用程序或脚本会将预配请求提交到要由预配服务处理的队列，然后再进行轮询以确定完成。 如果使用预先预配，请求能够快速得到处理，同时另一个服务会在后台对替换数据库的预配进行管理。



## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 预配单个新租户到共享的多租户数据库及其自己的数据库中
> * 预配一批其他租户
> * 逐步讲解预配租户以及将它们注册到目录的详细信息

试着学习[性能监视教程](saas-multitenantdb-performance-monitoring.md)。

## <a name="additional-resources"></a>其他资源

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [弹性数据库客户端库](sql-database-elastic-database-client-library.md)
* [如何在 Windows PowerShell ISE 中调试脚本](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
