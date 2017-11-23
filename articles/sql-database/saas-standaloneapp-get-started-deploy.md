---
title: "多租户 SaaS 教程 - Azure SQL 数据库 | Microsoft 文档"
description: "部署和浏览使用 Azure SQL 数据库的独立单租户 SaaS 应用程序。"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: 2ca290dfcb23215ffd727500e76076ae8b14fa6f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>部署和浏览使用 Azure SQL 数据库的独立单租户应用程序

本教程中将部署和浏览 Wingtip 票证 SaaS 独立应用程序。 该应用程序旨在展示简化启用 SaaS 方案的 Azure SQL 数据库功能。

独立应用程序模式为每个租户部署包含单租户应用程序和单租户数据库的 Azure 资源组。  可以预配应用程序的多个实例，提供多租户解决方案。

在本教程中，将多个租户的资源组部署到 Azure 订阅中，这一模式允许将资源组部署到租户的 Azure 订阅中。  Azure 具有合作伙伴计划，这些计划允许服务提供商在租户订阅中代表租户以管理员身份管理这些资源组。

在下面的部署部分中，有三个“部署到 Azure”按钮，每一个均部署一个为特定租户自定义的应用程序的不同实例。 按下各按钮时，相应的应用程序在五分钟后完全部署。  应用部署到 Azure 订阅中。  用户具有完全访问权限，可以浏览并处理各个应用程序组件。

[WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub 存储库提供了应用程序源代码和管理脚本。


本教程介绍以下内容：

> [!div class="checklist"]

> * 如何部署 Wingtip 票证 SaaS 独立应用程序
> * 获取应用程序源代码和管理脚本的位置
> * 关于构成该应用的服务器和数据库

将在适当的时候发布其他教程，使你能够浏览基于此应用程序模式的一系列管理方案。   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>部署 Wingtip 票证 SaaS 独立应用程序

为提供的三个租户部署应用：

1. 单击每个“部署到 Azure”按钮，打开 Azure 门户中的部署模板。 每个模板要求两个参数值；新资源组的名称和用于区分该部署和应用中其他部署的用户名。 下一个步骤提供了设置这些值的详细信息。<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso 音乐厅**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp;&nbsp;**Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam 爵士乐俱乐部**

2. 输入每个部署所需的参数值。

    > [!IMPORTANT]
    > 出于演示目的，某些身份验证和服务器防火墙已有意取消保护。 为每个应用程序部署创建新的资源组。  不要使用现有资源组。 不要使用该应用程序及其创建的任何资源进行生产。 使用完该应用程序时请删除所有资源组，停止相关计费。

    最好在资源名称中仅使用小写字母、数字和连字符。
    * 对于“资源组”- 选择“新建”，然后为资源组提供一个名称（区分大小写）。
        * 建议资源组名称全部采用小写字母。
        * 建议追加短横线，后面依次是缩写、数字，例如 _wingtip-sa-af1_。
        * 从下拉列表中选择一个位置。

    * 对于“用户”- 建议选择较短的用户值，例如在缩写后加上数字：_af1_。


1. **部署应用程序**。

    * 单击“我同意上述条款和条件”。
    * 单击“购买”。

1. 通过单击“通知”（搜索框右侧的钟形图标）监视所有三个部署的部署状态。 部署应用大约需要五分钟。


## <a name="run-the-application"></a>运行应用程序

该应用将展示举办活动的地点，如音乐厅、爵士乐俱乐部和运动俱乐部。 这些地点将注册为 Wingtip 票证的客户（或租户），以便于列出活动和销售票证。 每个地点均获得一个用于管理和列出其活动并销售票证的个性化网站，每个网站都是独立的，并且与其他租户隔离。 事实上，每个租户都有单独的应用程序实例和独立的 SQL 数据库。

1. 在不同的浏览器选项卡中打开三个租户各自的活动页面：

    http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net <br>
    http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net<br>
    http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    （用自己的部署的 User 值替换 &lt;USER&gt;）。

   ![事件](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


该应用使用 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)来控制传入请求的分配。 每个租户特定应用将租户名作为 URL 中域名的一部分。 所有租户 URL 均包括特定 *User* 值并遵循以下格式：http://events.&lt;venuename&gt;.&lt;USER&gt;.trafficmanager.net。 每个租户的数据库位置都包含在部署的相应应用的应用设置中。

在生产环境中，通常要创建一条 CNAME DNS 记录，[将公司 Internet 域指向](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain)流量管理器配置文件的 URL。


## <a name="explore-the-servers-and-tenant-databases"></a>浏览服务器和租户数据库

请看看一些已部署的资源：

1. 在 [Azure 门户](http://portal.azure.com)中，浏览到资源组列表。  将看到 wingtip-sa-catalog-&lt;USER&gt; 资源组，其中 catalog-sa-&lt;USER&gt; 服务器已与 tenantcatalog 数据库一起部署。 还将看到三个租户资源组。

1. 打开 wingtip-sa-fabrikam-&lt;USER&gt; 资源组，其中包括“Fabrikam 爵士乐俱乐部”这一部署的资源。  fabrikamjazzclub-&lt;USER&gt; 服务器包括 fabrikamjazzclub 数据库。


每个租户数据库都是一个 50 DTU 的 _独立_数据库。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]

> * 如何部署 Wingtip 票证 SaaS 独立应用程序
> * 关于构成该应用的服务器和数据库
> * 如何删除示例资源以停止相关计费


## <a name="additional-resources"></a>其他资源

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* 若要了解多租户 SaaS 应用程序，请参阅[多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)
