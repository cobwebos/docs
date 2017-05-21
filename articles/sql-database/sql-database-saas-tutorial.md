---
title: "部署和浏览 Wingtip 票证平台 (WTP) 应用程序（使用 Azure SQL 数据库的示例 SaaS 应用程序）| Microsoft Docs"
description: "部署和浏览使用 Azure SQL 数据库的示例 SaaS 应用程序"
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
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ff4dc19bb6e24ea9ceeed9721cfb3a85b4d10965
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>部署和浏览使用 Azure SQL 数据库的多租户 SaaS 应用程序

本教程中将部署和浏览 Wingtip 票证平台 (WTP) SaaS 应用程序。 该应用程序使用租户各有数据库（一种 SaaS 应用程序模式）为多个租户提供服务。 该应用程序旨在展示支持 SaaS 方案和 SaaS 设计及管理模式的 Azure SQL 数据库功能。

单击下面的“部署到 Azure”按钮之后五分钟，即可拥有使用 SQL 数据库、已启动且正在云中运行的多租户 SaaS 应用程序。 该应用程序随三个示例租户一起部署，每个租户都有自己的数据库，并且这三个租户均部署在 SQL 弹性池中。 该应用将部署到 Azure 订阅，提供检查和使用单个应用程序组件所需的完全访问权限。

本教程介绍以下内容：

> [!div class="checklist"]

> * 如何部署 WTP 应用程序
> * 构成该应用的服务器、池和数据库
> * 租户通过目录映射到其数据
> * 如何预配新租户
> * 如何查看池使用率来监视租户活动
> * 如何删除示例资源以停止相关计费

若要浏览各种 SaaS 设计和管理模式，可以使用基于此初始部署编写的[一系列相关教程](sql-database-wtp-overview.md)。 学习这些教程时，请深入了解提供的脚本，并仔细观察如何实现不同的 SaaS 模式。 请单步执行每个教程中的脚本，更深入地了解如何实现用于简化开发 SaaS 应用程序的许多 SQL 数据库功能。

若要完成本教程，请确保已完成以下先决条件：

* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>部署 Wingtip 票证 (WTP) SaaS 应用程序

在五分钟内部署 Wingtip 票证平台：

1. 单击以部署：

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 输入该部署所需的参数值：

    > [!IMPORTANT]
    > 出于演示目的，某些身份验证和服务器防火墙已有意取消保护。 **创建新的资源组**，不要使用现有资源组、服务器或池，并且不要将此应用程序或其创建的任何资源用于生产。 使用完该应用程序时请删除此资源组，以停止相关计费。

    * **资源组** - 选择“新建”，然后提供**名称**和**位置**。
    * **用户** - 某些资源需要在所有 Azure 订阅中均唯一的名称。 若要确保唯一性，请提供一个可将你创建的资源与部署 Wingtip 应用程序的其他用户创建的资源区分开来的值。 建议使用简短的**用户**名，如姓名的首字母加上一个数字（例如 *bg1*），然后将该名称用在资源组名称中（例如 *wingtip-bg1*）。 **User** 参数只能包含字母、数字和连字符。 第一个字符和最后一个字符必须是字母或数字（建议全部小写）。

     ![模板](./media/sql-database-saas-tutorial/template.png)

1. **部署应用程序**。

    * 如果同意条款和条件，请单击“我同意上述条款和条件”。
    * 选择“固定到仪表板”。
    * 单击“购买”。

1. 通过单击“通知”（搜索框右侧的钟形图标）监视部署状态。 部署 WTP 应用大约需要四分钟。

   ![部署成功](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>了解应用程序

该应用将展示举办活动的地点，如音乐厅、爵士乐俱乐部、运动俱乐部。 地点将作为 Wingtip 票证平台 (WTP) 的客户（或租户）注册，以便于列出活动和销售票证。 每个地点均获得一个用于管理和列出其活动并销售票证的个性化 Web 应用，每个地点都是独立的，并且与其他租户隔离。 事实上，每个租户均获得一个部署到 SQL 弹性池的 SQL 数据库。

中部的**活动中心**提供特定于你的部署的租户 URL 列表。 所有租户 URL 均包括你的特定 *User* 值并遵循以下格式：http://events.wtp.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*。 

1. 打开“活动中心”：http://events.wtp.&lt;USER&gt;.trafficmanager.net（替换为你的用户名）：

    ![活动中心](media/sql-database-saas-tutorial/events-hub.png)

1. 单击“活动中心”中的“Fabrikam 爵士乐俱乐部”。

   ![活动](./media/sql-database-saas-tutorial/fabrikam.png)

1. 单击“票证”并浏览活动的票证购买情况。

WTP 应用程序使用 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)来控制传入流量的分配。 租户特定的活动页需要租户名称包含在 URL 中。 “活动”应用将分析 URL 中的租户名称，并使用它来创建密钥以使用[分片映射管理](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management)访问目录。 该目录将密钥映射到租户的数据库位置。 **活动中心**使用目录中的扩展元数据来检索与每个数据库相关联的租户名称。

在生产环境中，通常要创建一条 CNAME DNS 记录以[将公司 Internet 域指向](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain)流量管理器配置文件。

## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip 票证脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 存储库中找到。 脚本文件位于 [Learning Modules 文件夹](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)中。 将 **Learning Modules** 文件夹下载到本地计算机，保持其文件夹结构不变。

## <a name="provision-a-new-tenant"></a>预配新租户

初始部署将创建三个示例租户，但我们需要创建更多租户来提供最佳教程体验。 在此步骤中将快速创建新租户。 稍后，应在[预配和编录教程](sql-database-saas-tutorial-provision-and-catalog.md)中深入了解有关预配新租户的详细信息，在该教程中可以看到，在应用程序中实现注册组件以及在客户登录时自动预配租户是如何简单。

### <a name="initialize-the-user-config-file-for-your-deployment"></a>初始化用户配置文件以用于部署

由于这是要在初始 WTP 应用程序部署后运行的第一个脚本，因此必须更新用户配置文件。 请使用来自部署的特定值更新变量。

   1. 在 *PowerShell ISE* 中打开 ...\\Learning Modules\\*UserConfig.psm1*
   1. 将 _$userConfig.ResourceGroupName_ 修改为在部署该应用时设置的_资源组_。
   1. 将 _$userConfig.Name_ 修改为在部署该应用时设置的_用户_名。

### <a name="provision-the-new-tenant"></a>预配新租户

1. 在 *PowerShell ISE* 中打开 ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*。
1. 按 **F5** 运行该脚本（目前保留脚本的默认值）。

新租户将注册到目录中。 其数据库将创建并添加到 SQL 弹性池。 成功预配后，新租户的票证销售站点将显示在浏览器中：

![新租户](./media/sql-database-saas-tutorial/red-maple-racing.png)

刷新活动中心并验证新租户是否在列表中。

## <a name="start-generating-load-on-the-tenant-databases"></a>开始在租户数据库上生成负载

既然有了多个租户数据库，让我们将其投入使用！ 我们提供了一个 PowerShell 脚本，用于模拟针对所有租户数据库运行的工作负荷。 默认情况下该负载运行 60 分钟。 “Wingtip 票证”是一个 SaaS 应用，而 SaaS 应用上的实际负载通常是偶发的，不可预测。 为了模拟该负载，负载生成器将生成分布在所有租户上的随机化负载。 该模式显现需要几分钟，因此请让负载生成器运行大约 5-10 分钟，然后再尝试按以下部分所述监视负载。

1. 在 **PowerShell ISE** 中打开 ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1*
1. 按 **F5** 运行该脚本并启动负载生成器（目前保留默认参数值）。

> [!IMPORTANT]
> 负载生成器作为一系列作业在本地 PowerShell 会话中运行，因此请将 PowerShell 会话保持打开状态！ 如果关闭 PowerShell，或者关闭启动负载生成器的选项卡，或者暂停计算机，那么作业将停止。


## <a name="explore-the-servers-pools-and-tenant-databases"></a>浏览服务器、池以及租户数据库

既然已浏览了应用程序、预配了新租户并开始针对该组租户运行负载，让我们看看一些已部署的资源。

1. 在 [Azure 门户](http://portal.azure.com)中，打开 **catalog-&lt;USER&gt;** 服务器。 该目录服务器包含两个数据库。 **tenantcatalog** 和 **basetenantdb**（为了创建新租户而复制的空的黄金数据库）。

   ![数据库](./media/sql-database-saas-tutorial/databases.png)

1. 打开保存租户数据库的 **tenants1-&lt;USER&gt;** 服务器。 请注意，每个租户数据库都是 50 eDTU 标准池中的弹性标准数据库。 另外还要注意有一个 _Red Maple Racing_ 数据库（以前预配的租户）。

   ![服务器](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>监视池

如果负载生成器已运行几分钟，那么应有足够的数据可用于开始了解一些内置于池和数据库中的监视功能。

1. 浏览到服务器 *tenants1-&lt;USER&gt;*，然后单击 **Pool1** 查看池的资源利用率：

   ![监视池](./media/sql-database-saas-tutorial/monitor-pool.png)

这两个图表恰当地说明了弹性池和 SQL 数据库多么适合用于 SaaS 应用程序工作负荷。 一个 50 eDTU 的池轻松支持了四个数据库（每个数据库消耗的资源峰值达到 40 eDTU）。 如果每个数据库均已预配为独立数据库，那么每个都需要成为 S2 (50 DTU) 才能支持资源消耗量达到此峰值，但 4 个独立 S2 数据库的成本几乎是池价格的 3 倍。 而该池仍有充足的空间用于更多数据库。 在实际情况下，客户当前在 200 eDTU 的池中最多可运行 500 个数据库。 有关详细信息，请参阅[性能监视教程](sql-database-saas-tutorial-performance-monitoring.md)。


## <a name="deleting-the-resources-created-with-this-tutorial"></a>删除通过本教程创建的资源

完成浏览和使用 WTP 应用时，请在门户中浏览到应用程序的资源组，并删除该资源组以停止与此部署相关的所有计费。 如果使用了任何随附的教程，那么它们创建的任何资源也将随应用程序一起删除。


## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]

> * 如何部署 WTP 应用程序
> * 构成该应用的服务器、池和数据库
> * 租户通过目录映射到其数据
> * 如何预配新租户
> * 如何查看池使用率来监视租户活动
> * 如何删除示例资源以停止相关计费

现在，请尝试[预配和编录教程](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>其他资源

* [基于初始 Wingtip 票证平台 (WTP) 应用程序部署编写的其他教程](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* 若要了解弹性池，请参阅[什么是 Azure SQL 弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* 若要了解弹性作业，请参阅[管理扩大的云数据库](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* 若要了解多租户 SaaS 应用程序，请参阅[多租户 SaaS 应用程序的设计模式](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

