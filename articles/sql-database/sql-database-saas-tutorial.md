---
title: "多租户 SaaS 教程 - Azure SQL 数据库 | Microsoft 文档"
description: "部署和浏览 Wingtip SaaS 多租户应用程序，演示使用 Azure SQL 数据库的 SaaS 模式。"
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
ms.date: 07/26/2017
ms.author: sstein
ms.openlocfilehash: 2e6816ce2740b0325c6f59338f0365c6b40539f3
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="deploy-and-explore-a-multi-tenant-application-that-uses-azure-sql-database---wingtip-saas"></a>部署和浏览使用 Azure SQL 数据库的多租户 Wingtip 应用程序 - Wingtip SaaS

本教程中将部署和浏览 Wingtip SaaS 应用程序。 该应用程序使用租户各有数据库（一种 SaaS 应用程序模式）为多个租户提供服务。 该应用程序旨在展示简化启用 SaaS 方案的 Azure SQL 数据库功能。

单击下面的“部署到 Azure”按钮之后五分钟，即可拥有使用 SQL 数据库、已启动且正在云中运行的多租户 SaaS 应用程序。 该应用程序部署了三个示例租户，每个租户都有自己的数据库，并且这三个租户均部署在 SQL 弹性池中。 该应用将部署到 Azure 订阅，提供浏览和使用单个应用程序组件所需的完全访问权限。 WingtipSaaS GitHub 存储库提供了应用程序源代码和管理脚本。


本教程介绍以下内容：

> [!div class="checklist"]

> * 如何部署 Wingtip SaaS 应用程序
> * 获取应用程序源代码和管理脚本的位置
> * 构成该应用的服务器、池和数据库
> * 如何通过目录将租户映射到其数据
> * 如何预配一个新租户
> * 如何监视租户在应用中的活动

若要浏览各种 SaaS 设计和管理模式，可以使用基于此初始部署编写的[一系列相关教程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)。 学习这些教程时，请深入了解提供的脚本，并仔细观察如何实现不同的 SaaS 模式。 请单步执行每个教程中的脚本，更深入地了解如何实现用于简化开发 SaaS 应用程序的许多 SQL 数据库功能。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已完成了以下先决条件：

* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-saas-application"></a>部署 Wingtip SaaS 应用程序

部署 Wingtip SaaS 应用：

1. 单击“部署到 Azure”按钮，在 Azure 门户中打开 Wingtip SaaS 部署模板。 该模板要求两个参数值；新资源组的名称和用于区分该部署和 Wingtip SaaS 应用中其他部署的用户名。 下一个步骤提供了设置这些值的详细信息。

   请务必注意你使用的确切值，因为接下来需要将把它们输入到配置文件。

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 输入该部署所需的参数值：

    > [!IMPORTANT]
    > 出于演示目的，某些身份验证和服务器防火墙已有意取消保护。 请创建新的资源组，不要使用现有资源组、服务器或池。 不要使用该应用程序及其创建的任何资源进行生产。 使用完该应用程序时请删除此资源组，以停止相关计费。

    * **资源组** - 选择“新建”，并为资源组提供一个**名称**。 从下拉列表中选择一个**位置**。
    * 用户 - 某些资源需要全局唯一的名称。 为了确保唯一性，请在每次部署应用程序时提供一个可将你创建的资源与 Wingtip 应用程序其他任何部署创建的资源区别开来的值。 建议使用简短的**用户**名，如姓名的首字母加上一个数字（例如 *bg1*），并将该名称用在资源组名称中（例如 *wingtip-bg1*）。 **User** 参数只能包含字母、数字和连字符（不能有空格）。 第一个字符和最后一个字符必须是字母或数字（建议全部小写）。


1. **部署应用程序**。

    * 单击“我同意上述条款和条件”。
    * 单击“购买”。

1. 通过单击“通知”（搜索框右侧的钟形图标）监视部署状态。 部署 Wingtip SaaS 应用大约需要 5 分钟。

   ![部署成功](media/sql-database-saas-tutorial/succeeded.png)

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>下载和取消阻止 Wingtip SaaS 脚本

在应用程序部署的同时，下载源代码和管理脚本。

> [!IMPORTANT]
> 从外部源下载和提取 zip 文件时，可执行内容（脚本、ddl）可能受 Windows 阻止。 从 zip 文件提取脚本时，在提取前请按以下步骤取消阻止 .zip 文件。 这可确保允许运行该脚本。

1. 浏览到 [Wingtip SaaS github 存储库](https://github.com/Microsoft/WingtipSaaS)。
1. 单击“克隆或下载”。
1. 单击“下载 ZIP”并保存文件。
1. 右键单击“WingtipSaaS-master.zip”文件，并选择“属性”。
1. 在“常规”选项卡上，选择“取消阻止”，然后单击“应用”。
1. 单击 **“确定”**。
1. 解压缩文件。

脚本位于 ..\\WingtipSaaS-master\\Learning Modules 文件夹中。

## <a name="update-the-configuration-file-for-this-deployment"></a>更新此部署的配置文件

运行任何脚本之前，在 UserConfig.psm1 中设置“资源组”和“用户”值。 将这些变量设置为部署期间设置的值。

1. 在 *PowerShell ISE* 中打开 ...\\Learning Modules\\*UserConfig.psm1*
1. 使用部署（仅限 10 和 11 行）的特定值，更新 ResourceGroupName 和 Name。
1. 保存更改！

在此处设置后无需在每个脚本中更新这些特定于部署的值。

## <a name="run-the-application"></a>运行应用程序

该应用将展示举办活动的地点，如音乐厅、爵士乐俱乐部、运动俱乐部。 地点将作为 Wingtip 平台的客户（或租户）注册，以便于列出活动和销售票证。 每个地点均获得一个用于管理和列出其活动并销售票证的个性化 Web 应用，每个地点都是独立的，并且与其他租户隔离。 事实上，每个租户均获得一个部署到 SQL 弹性池的 SQL 数据库。

中部的**活动中心**提供特定于部署的租户 URL 列表。

1. 在 web 浏览器中打开“事件中心”：http://events.wtp.&lt;USER&gt;.trafficmanager.net（替换为部署用户名）：

    ![事件中心](media/sql-database-saas-tutorial/events-hub.png)

1. 单击“活动中心”中的“Fabrikam 爵士乐俱乐部”。

   ![事件](./media/sql-database-saas-tutorial/fabrikam.png)


该应用使用 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)来控制传入请求的分配。 租户特定的活动页需要租户名称包含在 URL 中。 所有租户 URL 均包括特定 *User* 值并遵循以下格式：http://events.wtp.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*。 “活动”应用将分析 URL 中的租户名称，并使用它来创建密钥以使用[分片映射管理](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management)访问目录。 该目录将密钥映射到租户的数据库位置。 事件中心使用目录中的扩展元数据检索与每个数据库相关联的租户名称，提供 URL 列表。

在生产环境中，通常要创建一条 CNAME DNS 记录以[将公司 Internet 域指向](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain)流量管理器配置文件。

## <a name="start-generating-load-on-the-tenant-databases"></a>开始在租户数据库上生成负载

现在已部署应用，让我们投入使用！ Demo-LoadGenerator PowerShell 脚本将启动用于针对所有租户数据库运行的工作负荷。 许多 SaaS 应用上的实际加载通常是偶发性的且不可预测。 为了模拟这种加载类型，生成器生成了一种针对所有租户、按随机间隔对每个租户随机喷发的加载分布。 由于负载模式显现需要几分钟，因此在监视该负载之前最好让生成器运行至少 3 或 4 分钟。

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Utilities\\Demo-LoadGenerator.ps1 脚本。
1. 按 **F5** 运行该脚本并启动负载生成器（目前保留默认参数值）。

> [!IMPORTANT]
> 若要运行其他脚本，请打开新的 PowerShell ISE 窗口。 负载生成器在本地 PowerShell 会话中作为一系列作业运行。 Demo-LoadGenerator.ps1 脚本启动实际负载生成器脚本，并作为前台任务加上一系列后台负载生成器作业运行。 为目录中注册的每个数据库调用负载生成器作业。 该作业在本地 PowerShell 会话中运行，因此关闭 PowerShell 会话将停止所有作业。 如果暂停计算机，负载生成器也会暂停并且在启动计算机时恢复。

负载生成器为每个租户调用负载生成器作业后，前台任务将保持在作业调用状态，为随后配置的任何新租户启动其他后台作业。 可以使用“Ctrl-C”或按“停止”按钮来停止前台任务，但现有后台作业将在每个数据库继续生成加载。 如果需要监视和控制后台作业，请使用“Get-Job”、“Receive-Job”和“Stop-Job”。 在前台任务正在运行时，无法使用相同的 PowerShell 会话执行其他脚本。 若要运行其他脚本，请打开新的 PowerShell ISE 窗口。

如果想要重启负载生成器会话（例如使用不同的参数），可以停止前台任务然后重新运行 Demo-LoadGenerator.ps1 脚本。 重新运行 Demo-LoadGenerator.ps1 首先应停止当前运行的任何作业，然后重启生成器，使用当前的参数开始设置新作业。

现在，让负载生成器在作业调用状态下运行。


## <a name="provision-a-new-tenant"></a>预配新租户

最初的部署创建了三个示例租户，让我们来创建其他租户，看看这将如何影响部署的应用程序。 [预配和目录教程](sql-database-saas-tutorial-provision-and-catalog.md)中详细介绍了 Wingtip SaaS 预配租户工作流。 在此步骤中将快速创建新租户。

1. 在 *PowerShell ISE* 中打开 ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*。
1. 按“F5”运行该脚本（目前保留默认值）。

   > [!NOTE]
   > 许多 Wingtip SaaS 脚本使用 $PSScriptRoot 来允许导航文件夹调用其他脚本中的函数。 只有按“F5”执行脚本的情况下，才能计算该变量。  突出显示和运行某个选择的内容 (F8) 时，可能会生成错误，因此请在运行脚本时按 F5。

新租户数据库在 SQL 弹性池中创建， 在目录中初始化和注册。 成功预配后，新租户的票证销售事件站点将显示在浏览器中：

![新租户](./media/sql-database-saas-tutorial/red-maple-racing.png)

刷新事件中心，新租户将出现在列表中。


## <a name="explore-the-servers-pools-and-tenant-databases"></a>浏览服务器、池以及租户数据库

既然已开始针对该组租户运行加载，让我们看看一些已部署的资源：

1. 在 [Azure 门户](http://portal.azure.com)中，浏览到 SQL 服务器列表并打开 **catalog-&lt;USER&gt;** 服务器。 该目录服务器包含两个数据库。 tenantcatalog 和 basetenantdb（为了创建新租户而复制的空的黄金或模板数据库）。

   ![数据库](./media/sql-database-saas-tutorial/databases.png)

1. 返回到 SQL 服务器列表并打开保存租户数据库的 **tenants1-&lt;USER&gt;** 服务器。 每个租户数据库都是 50 eDTU 标准池中的弹性标准数据库。 另外还要注意有一个 Red Maple Racing 数据库（以前预配的租户数据库）。

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>监视池

如果负载生成器已运行几分钟，那么应有足够的数据可用于开始了解一些内置于池和数据库中的监视功能。

1. 浏览服务器 tenants1-&lt;用户&gt;，然后单击“Pool1”，查看池的资源利用率（负载生成器一小时运行值如以下图表所示）：

   ![监视池](./media/sql-database-saas-tutorial/monitor-pool.png)

这两个图表恰当地说明了弹性池和 SQL 数据库多么适合用于 SaaS 应用程序工作负荷。 一个 50 eDTU 的池轻松支持了四个数据库（每个数据库消耗的资源峰值达到 40 eDTU）。 如果将它们预配为独立数据库，则每一个都需要为 S2 (50 DTU) 级以支持突发情况。 4 个独立 S2 数据库的花费几乎是池价格的 3 倍，而该池仍有充足的空间用于更多数据库。 在实际情况下，SQL 数据库客户当前在 200 eDTU 的池中最多可运行 500 个数据库。 有关详细信息，请参阅[性能监视教程](sql-database-saas-tutorial-performance-monitoring.md)。


## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]

> * 如何部署 Wingtip SaaS 应用程序
> * 构成该应用的服务器、池和数据库
> * 租户通过目录映射到其数据
> * 如何预配新租户
> * 如何查看池使用率来监视租户活动
> * 如何删除示例资源以停止相关计费

现在，请尝试[预配和编录教程](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>其他资源

* 其他[基于 Wingtip SaaS 应用程序编写的教程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* 若要了解弹性池，请参阅[什么是 Azure SQL 弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* 若要了解弹性作业，请参阅[管理扩大的云数据库](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* 若要了解多租户 SaaS 应用程序，请参阅[多租户 SaaS 应用程序的设计模式](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)
