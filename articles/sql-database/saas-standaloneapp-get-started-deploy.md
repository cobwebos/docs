---
title: 多租户 SaaS 教程 - Azure SQL 数据库 | Microsoft 文档
description: 部署和浏览使用 Azure SQL 数据库的独立单租户 SaaS 应用程序。
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 32cfa2e9bd48dd4e27da5c4010391c032d67d96b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644716"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>部署和浏览使用 Azure SQL 数据库的独立单租户应用程序

在本教程中，你将部署和浏览使用独立应用程序或每租户应用模式开发的 Wingtip 票证 SaaS 示例应用程序。  该应用程序旨在展示简化启用多租户 SaaS 方案的 Azure SQL 数据库的功能。

独立应用程序或每租户应用模式将为每个租户部署应用程序实例。  为特定租户配置了每个应用程序并在单独的 Azure 资源组中部署了这些应用程序。 预配了应用程序的多个实例，提供多租户解决方案。 租户数较少且最需要优先考虑租户隔离时，最适合采用此模式。 Azure 具有合作伙伴计划，这些计划允许将资源部署到租户的订阅中，由服务提供商代表租户进行管理。 

在本教程中，三个租户的三个独立应用程序将会部署到 Azure 订阅中。  用户具有完全访问权限，可以浏览并处理各个应用程序组件。

[WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub 存储库提供了应用程序源代码和管理脚本。


本教程介绍以下内容：

> [!div class="checklist"]
> * 如何部署 Wingtip Tickets SaaS 独立应用程序。
> * 获取应用程序源代码和管理脚本的位置。
> * 关于构成该应用的服务器和数据库。

我们将发布更多教程。 参考这些教程可以探索基于此应用程序模式的一系列管理方案。   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>部署 Wingtip 票证 SaaS 独立应用程序

为提供的三个租户部署应用：

1. 单击每个蓝色的“部署到 Azure”按钮，在 [Azure 门户](https://portal.azure.com)中打开部署模板。 每个模板要求两个参数值；新资源组的名称和用于区分该部署和应用中其他部署的用户名。 下一个步骤提供了设置这些值的详细信息。<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso 音乐厅**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam 爵士乐俱乐部**

2. 输入每个部署所需的参数值。

    > [!IMPORTANT]
    > 出于演示目的，某些身份验证和服务器防火墙已有意取消保护。 为每个应用程序部署创建新的资源组。  不要使用现有资源组。 不要使用该应用程序及其创建的任何资源进行生产。 使用完该应用程序时请删除所有资源组，停止相关计费。

    最好在资源名称中仅使用小写字母、数字和连字符。
    * 对于“资源组”，请选择“新建”，然后为资源组提供一个小写的名称。 **wingtip-sa-\<venueName\>-\<user\>** 是建议的模式。  对于 \<venueName\>，请替换为场所名称，不留空格。 对于 \<user\>，请替换为以下的用户值。  使用此模式，资源组名称可能是 wingtip-sa-contosoconcerthall-af1、wingtip-sa-dogwooddojo-af1、wingtip-sa-fabrikamjazzclub-af1。
    * 从下拉列表中选择一个**位置**。

    * 对于“用户”，建议使用较短的用户值，例如在缩写后加上数字：af1。


3. **部署应用程序**。

    * 单击“我同意上述条款和条件”。
    * 单击“购买”。

4. 通过单击“通知”（搜索框右侧的钟形图标）监视所有三个部署的状态。 部署应用大约需要五分钟。


## <a name="run-the-applications"></a>运行应用程序

举办活动的应用展示场所。  场所是应用程序的租户。 每个场所有个性化的网站用于列出活动和售票。 场所类型包括音乐厅、爵士乐俱乐部和运动俱乐部。 在示例中，场所的类型决定了场所网站上所示的背景照片。   在独立应用模型中，每个场所都具有单独的应用程序实例及其自身的独立 SQL 数据库。

1. 在不同的浏览器选项卡中打开三个租户各自的活动页面：

    - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

    （在每个 URL 中，请将 &lt;user&gt; 替换为自己的部署 user 值。）

   ![活动](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

该应用使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)来控制传入请求的分配。 每个租户特定的应用实例将租户名称用作 URL 中域名的一部分。 所有租户 URL 包含特定的 **User** 值。 URL 采用以下格式：
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

每个租户的数据库**位置**包含在部署的相应应用的应用设置中。

在生产环境中，通常要创建一条 CNAME DNS 记录，[*将公司 Internet 域指向*](../traffic-manager/traffic-manager-point-internet-domain.md)流量管理器配置文件的 URL。


## <a name="explore-the-servers-and-tenant-databases"></a>浏览服务器和租户数据库

请看看一些已部署的资源：

1. 在 [Azure 门户](http://portal.azure.com)中，浏览到资源组列表。
2. 应能看到三个租户资源组。
3. 打开 wingtip-sa-fabrikam-&lt;user&gt; 资源组，其中包括“Fabrikam 爵士乐俱乐部”这一部署的资源。  fabrikamjazzclub-&lt;user&gt; 服务器包括 fabrikamjazzclub 数据库。

每个租户数据库都是一个 50 DTU 的 独立数据库。

## <a name="additional-resources"></a>其他资源

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- 若要了解多租户 SaaS 应用程序，请参阅[多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。

 
## <a name="delete-resource-groups-to-stop-billing"></a>删除资源组，停止计费 ##

完成使用示例后，删除创建的所有资源组以停止相关计费。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 如何部署 Wingtip Tickets SaaS 独立应用程序。
> * 关于构成该应用的服务器和数据库。
> * 如何删除示例资源以停止相关计费。

接下来，请尝试[预配和目录](saas-standaloneapp-provision-and-catalog.md)教程，你将在其中了解租户目录的用途，该目录可实现大量跨租户方案，如架构管理和租户分析。
 

