---
title: 在 SaaS 多租户 Azure 中预配 | Microsoft Docs
description: 了解如何在 Azure SQL 数据库多租户 SaaS 应用中预配和编录新租户
keywords: sql 数据库教程
services: sql-database
author: MightyPen
manager: craigg
ms.reviewer: billgib;andrela;genemi
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/01/2018
ms.author: billgib
ms.openlocfilehash: cb0229731d8fd1495173915b3bc95b48538f1e85
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>在使用分片多租户 Azure SQL 数据库的 SaaS 应用程序中预配和编录新租户

本文介绍如何在多租户分片数据库模型或模式中预配和编录新租户。

本文包括两个主要部分：

- 有关新租户预配和编录的[概念介绍](#goto_2_conceptual)。

- 重点介绍如何使用 PowerShell 脚本代码实现预配和编录的[教程](#goto_1_tutorial)。
    - 该教程使用能够适应多租户分片数据库模式的 Wingtip Tickets SaaS 应用程序。

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>数据库模式

本部分加上后续的几个部分介绍多租户分片数据库模式的概念。

在此多租户分片模型中，每个数据库内的表架构在存储租户数据的表的主密钥中包含租户密钥。 每个数据库可以使用租户密钥存储 0 个、 1 个或多个租户。 分片数据库的使用可让应用程序系统轻松支持极大量的租户。 任一租户的所有数据存储在一个数据库中。 大量的租户分布在多个分片数据库之间。 目录数据库将每个租户的映射存储到其数据库中。

#### <a name="isolation-versus-lower-cost"></a>隔离与降低成本

自行保留整个数据库的租户可以享受隔离的优势。 该租户可将数据库还原到以前的某个日期，而不受制对其他租户造成的影响。 可以针对一个租户优化数据库的性能，同样也不会影响到其他租户。 问题在于，相较于与其他租户共享数据库，隔离的成本更高。

预配新租户后，该租户可与其他租户共享数据库，或者，可将该租户放入其自己的新数据库。 以后，你可以改变主意，将数据库移到其他场合。

包含多个租户和单个租户的数据库混合在同一个 SaaS 应用程序中，以优化每个租户的成本或隔离。

   ![包含租户目录的分片多租户数据库应用](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>租户目录模式

如果两个或更多个数据库各自至少包含一个租户，则应用程序必须通过某种方式来发现哪个数据库存储了相关的租户。 目录数据库将存储此映射。

#### <a name="tenant-key"></a>租户密钥

针对每个租户，Wingtip 应用程序可以派生一个唯一密钥，即租户密钥。 应用从网页 URL 中提取租户名称。 应用对该名称进行哈希处理，以获取密钥。 应用使用密钥来访问目录。 该目录交叉引用有关存储租户的数据库的信息。 应用使用数据库信息进行连接。 也可使用其他租户密钥方案。

通过目录，无需中断应用程序即可在预配之后更改租户数据库的名称或位置。 在多租户数据库模型中，该目录适用于在数据库之间移动租户。

#### <a name="tenant-metadata-beyond-location"></a>位置外部的租户元数据

目录还可以指示租户是否处于脱机状态，以便进行维护或其他操作。 此外，可以扩展目录来存储其他租户或数据库元数据，例如以下各项：
- 数据库的服务层或版本。
- 数据库架构的版本。
- 租户名称及其 SLA（服务级别协议）。
- 用于实现应用程序管理、客户支持或 devops 流程的信息。  

目录还可用于实现跨租户报告、架构管理和数据提取以用于分析。 

### <a name="elastic-database-client-library"></a>弹性数据库客户端库 

在 Wingtip 中，目录是在 *tenantcatalog* 数据库中实现的。 *tenantcatalog* 是使用[弹性数据库客户端库 (EDCL)](sql-database-elastic-database-client-library.md) 的“分片管理”功能创建的。 该库允许应用程序创建、管理和使用存储在数据库中的分片映射。 分片映射交叉引用租户密钥及其分片（即其分片数据库）。

在租户预配过程中，可从应用程序或 PowerShell 脚本使用 EDCL 函数，在分片映射中创建条目。 以后，可以使用 EDCL 函数连接到正确的数据库。 EDCL 缓存连接信息以尽量减少目录数据库中的流量并提高连接速度。

> [!IMPORTANT]
> 切勿通过直接访问来编辑目录数据库中的数据！ 直接更新操作存在较高的数据损坏风险，因此不受支持。 请仅使用 EDCL API 编辑映射数据。

## <a name="tenant-provisioning-pattern"></a>租户预配模式

#### <a name="checklist"></a>清单

若要在现有的共享数据库中预配共享数据库的新租户，必须提出以下问题：
- 该数据库是否为新租户留出了足够的空间？
- 该数据库是否为新租户准备了包含所需引用数据的表，或者，是否可以添加数据？
- 该数据库是否为新租户提供基本架构的相应变体？
- 该数据库的相应地理位置是否与新租户靠近？
- 该数据库是否位于新租户的适当服务层？

如果想要在新租户自身的数据库中隔离该租户，可以根据租户的规范创建该租户。

预配完成后，必须在目录中注册该租户。 最后，可添加租户映射来引用相应的分片。

#### <a name="template-database"></a>模板数据库

通过执行 SQL 脚本、部署 bacpac 或复制模板数据库来预配此数据库。 Wingtip 应用复制模板数据库来创建新租户数据库。

与任何应用程序一样，Wingtip 会不变演变。 有时，Wingtip 需要对数据库进行更改。 更改可能包括以下各项：
- 新增或更改架构。
- 新增或更改引用数据。
- 用于确保最佳应用性能的日常数据库维护任务。

使用 SaaS 应用程序时，需要跨数量可能很多的租户数据库以协调的方式部署这些更改。 要使这些更改出现在未来租户数据库中，需要将这些更改合并到预配过程中。 [架构管理教程](saas-tenancy-schema-management.md)中进一步探讨了此难题。

#### <a name="scripts"></a>脚本

本教程中的租户预配脚本支持以下两种方案：
- 在与其他租户共享的现有数据库中预配租户。
- 在租户自身的数据库中预配该租户。

随后在目录分片映射中初始化和注册租户数据。 在示例应用中，为包含多个租户的数据库指定一个通用名称，例如 *tenants1* 或 *tenants2*。 为包含单个租户的数据库指定租户的名称。 示例中使用的特定命名约定不是模式的主要部分，因为使用目录可以将任何名称分配给数据库。  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>教程开始

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将租户预配到多租户数据库中
> * 将租户预配到单租户数据库中
> * 将一批租户同时预配到多租户和单租户数据库中
> * 在目录中注册数据库和租户映射

#### <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已完成了以下先决条件：

- Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- 已部署 Wingtip Tickets SaaS 多租户数据库应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS 多租户数据库应用程序](saas-multitenantdb-get-started-deploy.md)。

- 获取 Wingtip 脚本和源代码：
    - 在 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。
    - 有关下载和取消阻止 Wingtip 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>在与其他租户共享的数据库中预配租户

本部分显示了 PowerShell 脚本执行的、用于预配的主要操作列表。 可以使用 PowerShell ISE 调试程序逐步执行该脚本，以查看代码中的操作。

#### <a name="major-actions-of-provisioning"></a>用于预配的主要操作

以下是单步执行的预配工作流的主要元素：

- **计算新租户密钥**：哈希函数用于从租户名称创建租户密钥。
- **检查租户密钥是否已存在**：检查目录，确保尚未注册密钥。
- **初始化默认租户数据库中的租户**：更新租户数据库，以添加新租户信息。  
- **在目录中注册租户**：新租户密钥和现有 tenants1 数据库之间的映射将添加到目录中。 
- **将租户的名称添加到目录扩展表**：将地点名称添加到目录中的“租户”表。  此项添加内容说明如何扩展目录数据库来支持其他特定于应用程序的数据。
- **打开新租户的“事件”页**：在浏览器中打开 *Bushwillow Blues* 事件页。

   ![活动](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>调试程序步骤

若要了解 Wingtip 应用如何在共享数据库中实现新租户预配，请添加断点并单步执行工作流：

1. 在 *PowerShell ISE* 中，打开 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*，并设置以下参数：
   - **$TenantName** = **Bushwillow Blues**，即新地点的名称。
   - **$VenueType** = **blues**（预定义的地点类型之一）：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer（小写，不含空格）。
   - **$DemoScenario** = **1**，可在拥有其他租户的共享数据库中预配租户。

2. 通过将游标置于 38 行（该行显示：*New-Tenant `*）的任意位置来添加断点，并按 **F9**。

   ![断点](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. 按 **F5** 运行脚本。

4. 脚本执行在断点处停止之后，按 F11 单步执行代码。

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. 使用“调试”菜单选项（F10 和 F11）逐过程或单步执行调用的函数，跟踪脚本的执行。

有关调试 PowerShell 脚本的详细信息，请参阅[有关使用和调试 PowerShell 脚本的提示](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)。

## <a name="provision-a-tenant-in-its-own-database"></a>在租户自身的数据库中预配该租户

#### <a name="major-actions-of-provisioning"></a>用于预配的主要操作

以下是跟踪脚本时所要单步执行的工作流程的主要元素：

- **计算新租户密钥**：哈希函数用于从租户名称创建租户密钥。
- **检查租户密钥是否已存在**：检查目录，确保尚未注册密钥。
- **创建新租户数据库**：通过使用资源管理器模板复制 *basetenantdb* 数据库来创建此数据库。  新数据库名称取决于租户名称。
- **将数据库添加到目录**：新租户数据库在目录中注册为分片。
- **初始化默认租户数据库中的租户**：更新租户数据库，以添加新租户信息。  
- **在目录中注册租户**：新的租户密钥和 *sequoiasoccer* 数据库之间的映射添加到目录中。
- **将租户名称添加到目录**：将地点名称添加到目录的“租户”扩展表中。
- **打开新租户的“事件”页**：在浏览器中打开 *Sequoia Soccer* 事件页。

   ![活动](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>调试程序步骤

现在演练在租户自身的数据库中创建该租户的脚本过程：

1. 在 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* 中设置以下参数：
   - **$TenantName** = **Sequoia Soccer**，即新地点的名称。
   - **$VenueType** = **soccer**（预定义的地点类型之一）：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer（小写，不含空格）。
   - **$DemoScenario** = **2**，用于将租户预配到其自身的数据库。

2. 通过将游标置于 57 行（该行显示：&&nbsp;$PSScriptRoot\New-TenantAndDatabase `）的任意位置来添加新断点，并按 F9。

   ![断点](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. 按 **F5** 运行脚本。

4. 脚本执行在断点处停止之后，按 **F11** 单步执行代码。  使用 F10 和 F11 逐过程或单步执行函数来跟踪执行。

## <a name="provision-a-batch-of-tenants"></a>预配一批租户

本练习预配一批 17 个租户。 建议在开始其他 Wingtip Tickets 教程之前预配这批租户，以便可以使用更多数据库。

1. 在 *PowerShell ISE* 中，打开 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*，并将 *$DemoScenario* 参数更改为 4：
   - **$DemoScenario** = **4**，可将一批租户预配到共享数据库中。

2. 按 **F5** 运行脚本。

### <a name="verify-the-deployed-set-of-tenants"></a>验证部署的一组租户 

在此阶段，将混合的租户部署到共享数据库中，并将租户部署到各自的数据库中。 Azure 门户可用于检查创建的数据库。 在 [Azure 门户](https://portal.azure.com)中，通过浏览到 SQL Server 列表来打开 tenants1-mt-\<USER\> 服务器。  SQL 数据库列表应包含共享的 tenants1 数据库和各自数据库中的租户的数据库：

   ![数据库列表](media/saas-multitenantdb-provision-and-catalog/Databases.png)

虽然 Azure 门户显示了租户数据库，但是不允许查看共享数据库内的租户。 可以在 Wingtip 的“事件中心”网页中查看租户的完整列表，也可以通过浏览目录查看。

#### <a name="using-wingtip-tickets-events-hub-page"></a>使用 Wingtip Tickets 事件中心页

在浏览器中打开事件中心页 (http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

#### <a name="using-catalog-database"></a>使用目录数据库

目录中提供租户的完整列表，以及每个租户的相应数据库。 提供了 SQL 视图，用于将租户名称联接到数据库名称。 该视图很好地展现了扩展存储在目录中的元数据的价值。
- SQL 视图在 tenantcatalog 数据库中提供。
- 租户名称存储在“租户”表中。
- 数据库名称存储在“分片管理”表中。

1. 在 SQL Server Management Studio (SSMS) 中，使用登录名 **developer** 和密码 **P@ssword1** 连接到位于 **catalog-mt.\<USER\>.database.windows.net** 的租户服务器

    ![“SSMS 连接”对话框](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. 在 SSMS 对象资源管理器中，浏览到 *tenantcatalog* 数据库中的视图。

3. 右键单击“TenantsExtended”视图，然后选择“选中前 1000 行”。 注意租户名称和不同租户的数据库之间的映射。

    ![SSMS 中的“ExtendedTenants”视图](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>其他预配模式

本部分介绍其他相关预配模式。

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>在弹性池中预先预配数据库。

预配模式利用“使用弹性池时，计费针对弹性池而不针对数据库”这一事实。 因此，可以在需要前将数据库添加到弹性池中，而不会产生额外成本。 这种预配方法可以大幅减少在数据库中预配租户的时间。 可以根据需要调整预先预配数据库的数目，以保留适合预期预配率的缓冲区。

#### <a name="auto-provisioning"></a>自动预配

在自动预配模式下，专用预配服务用于自动根据需要预配服务器、池和数据库。 这种自动化方法包括在弹性池中预先预配数据库。 此外，如果数据库已停用并已删除，则可以根据需要预配服务来填充弹性池中创建的间隙。

此类自动化服务有时简单，有时复杂。 例如，自动化可以处理跨多个地理区域的预配，并且可以设置异地复制用于灾难恢复。 使用自动预配模式时，客户端应用程序或脚本会将预配请求提交到要由预配服务处理的队列。 然后，脚本会通过轮询来检测完成进度。 如果使用预先预配，请求能够快速得到处理，同时，一个后台服务将对替换数据库的预配进行管理。

## <a name="additional-resources"></a>其他资源

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [弹性数据库客户端库](sql-database-elastic-database-client-library.md)
- [如何在 Windows PowerShell ISE 中调试脚本](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 预配单个新租户到共享的多租户数据库及其自己的数据库中
> * 预配一批其他租户
> * 逐步讲解预配租户以及将它们注册到目录的详细信息

试着学习[性能监视教程](saas-multitenantdb-performance-monitoring.md)。

