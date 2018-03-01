---
title: "租户各有数据库 SaaS 教程 - Azure SQL 数据库 | Microsoft 文档"
description: "部署和浏览 Wingtip Tickets SaaS 多租户应用程序，该应用程序使用 Azure SQL 数据库演示“每个租户各有数据库”和其他 SaaS 模式。"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: cbe8a04abbf2dada7cc43e57e823c3a41bf83fe7
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>部署和探索多租户 SaaS 应用程序，该应用程序通过“每个租户各有数据库”模式使用 Azure SQL 数据库

本教程中，将部署和浏览 Wingtip Tickets SaaS 租户各有数据库应用程序 (Wingtip)。 该应用使用“租户各有数据库”模式存储多个租户的数据。 该应用旨在展示简化启用 SaaS 方案的 Azure SQL 数据库功能。

单击标记为“部署到 Azure”的蓝色按钮，五分钟后即生成一个多租户 SaaS 应用程序。 该应用包括 Microsoft Azure 云中运行的一个 Azure SQL 数据库。 该应用部署有三个示例租户，每个租户具有其自己的数据库。 所有数据库都会部署到 SQL 弹性池。 该应用会部署到 Azure 订阅。 用户具有完全访问权限，可以浏览并处理该应用的各个组件。 [WingtipTicketsSaaS-DbPerTenant GitHub 存储库][github-wingtip-dpt] 提供了应用程序 C# 源代码和管理脚本。

本教程介绍以下内容：

> [!div class="checklist"]
> - 如何部署 Wingtip SaaS 应用程序。
> - 获取应用程序源代码和管理脚本的位置。
> - 关于构成该应用的服务器、池和数据库。
> - 如何通过目录将租户映射到其数据。
> - 如何预配新租户。
> - 如何在应用中监视租户的活动。

可通过观看[相关教程系列](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)，了解各种 SaaS 设计和管理模式。 除了初始部署相关内容，这些教程还提供其他丰富的内容延展。
使用这些教程时，通过检查所提供的脚本，可看到不同 SaaS 模式的实现方式。
脚本演示 SQL 数据库的功能如何简化 SaaS 应用程序的开发。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已完成了以下先决条件：

* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

## <a name="deploy-the-wingtip-tickets-saas-application"></a>部署 Wingtip Tickets SaaS 应用程序

#### <a name="plan-the-names"></a>对名称进行计划

通过执行本部分中的步骤，提供用户值，用于确保资源名称为全局唯一，以及一个资源组的名称，该资源组包含部署应用时创建的所有资源。 如果一个人名叫 Ann Finley，则建议输入以下名称：
- 用户：af1（首字母缩写加数字。如果是第二次部署应用，则使用不同的值，例如 af2。）
- 资源组：wingtip-dpt-af1（wingtip-dpt 指示这是“每租户一个数据库”应用*。追加用户名 af1 会将资源组名称与它包含的资源的名称相关联。）*

现在请选择自己的名称，并将其写下来。 

#### <a name="steps"></a>Steps

1. 通过单击蓝色“部署到 Azure”按钮，在 Azure 门户中打开 Wingtip Tickets SaaS 租户各有数据库部署模板。

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 在模板中，输入必需参数的值：

    > [!IMPORTANT]
    > 出于演示目的，某些身份验证和服务器防火墙已有意取消保护。 建议新建资源组。 不要使用现有资源组、服务器或池。 不要将此应用程序、脚本或部署的任何资源用于生产。 使用完该应用程序时请删除此资源组，以停止相关计费。

    - **资源组** - 选择“新建”，并为资源组提供之前所选的唯一名称。 
    - 位置 - 从下拉列表中选择一个“位置”。
    - **User** - 使用之前所选的用户名值。

1. 部署应用程序。

    - 单击“我同意上述条款和条件”。
    - 单击“购买”。

1. 通过单击“通知”（搜索框右侧的钟形图标）监视部署状态。 部署 Wingtip Tickets SaaS 应用大约需要 5 分钟。

   ![部署成功](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>下载和取消阻止 Wingtip Tickets管理脚本

在应用程序部署的同时，下载源代码和管理脚本。

> [!IMPORTANT]
> 从外部源下载和提取 .zip 文件时，可执行内容（脚本、DLL）可能受 Windows 阻止。 从 zip 文件提取脚本时，请按照以下步骤取消阻止 .zip 文件，然后再进行提取。 取消阻止的操作可确保允许运行该脚本。

1. 浏览到 [WingtipTicketsSaaS-DbPerTenant GitHub 存储库][github-wingtip-dpt]。
2. 单击“克隆或下载”。
3. 单击“下载 ZIP”，然后保存文件。
4. 右键单击“WingtipTicketsSaaS-DbPerTenant-master.zip”文件，然后选择“属性”。
5. 在“常规”选项卡上，选择“取消阻止”，然后单击“应用”。
6. 单击“确定”。
7. 解压缩文件。

脚本位于 *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules* 文件夹中。

## <a name="update-the-user-configuration-file-for-this-deployment"></a>更新此部署的用户配置文件

运行任何脚本之前，在 UserConfig 文件中更新“资源组”和“用户”值。 将这些变量设置为部署期间使用的值。

1. 在 PowerShell ISE 中，打开 ...\\Learning Modules\\**UserConfig.psm1** 
2. 使用部署（仅限 10 和 11 行）的特定值，更新 ResourceGroupName 和 Name。
3. 保存更改！

几乎每个脚本中都会引用这些值。

## <a name="run-the-application"></a>运行应用程序

举办活动的应用展示场所。 场所类型包括音乐厅、爵士乐俱乐部和运动俱乐部。 在 Wingtip Tickets 中，场所注册为租户。 注册为租户可让场所轻松列出活动，以及向其客户销售门票。 每个场所有个性化的网站用于列出其活动和售票。

在应用内部，每个租户均获得一个部署到 SQL 弹性池的 SQL 数据库。

中间的“事件中心”页提供你的部署中租户的链接列表。

1. 在 Web 浏览器中打开“事件中心”：http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net（&lt;&gt;user 替换为部署的用户值）：

    ![事件中心](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. 单击“活动中心”中的“Fabrikam 爵士乐俱乐部”。

    ![活动](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure 流量管理器

Wingtip 应用程序使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)控制传入请求的分配。 特定租户的事件页的 URL 使用以下格式：

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

下表中对前述格式的各部分进行了说明。

| URL 部分        | 说明       |
| :-------------- | :---------------- |
| http://events.wingtip-dpt | Wingtip 应用的事件部分。<br /><br /> “-dpt”部分将 Wingtip Tickets 的“每租户数据库”实现方式与其他实现方式区分开来。 例如，独立的“每租户应用”(-sa)实现方式 ，或多租户数据库 (-mt) 实现方式。 |
| .*&lt;user&gt;* | 示例中的 af1。 |
| .trafficmanager.net/ | Azure 流量管理器、基 URL。 |
| fabrikamjazzclub | 标识名为“Fabrikam 爵士俱乐部”的租户。 |
| &nbsp; | &nbsp; |

1. 租户名称是由事件应用通过该 URL 分析得到的。
2. 租户名称用于创建密钥。
3. 密钥用于访问目录，以获取租户数据库的位置。
    - 目录是通过使用分片映射管理实现的。
4. 事件中心在目录中使用扩展元数据为每个租户构造事件页 URL 的列表。

在生产环境中，通常要创建一条 CNAME DNS 记录，[将公司 Internet 域指向](../traffic-manager/traffic-manager-point-internet-domain.md)流量管理器 DNS 名称。

## <a name="start-generating-load-on-the-tenant-databases"></a>开始在租户数据库上生成负载

现在已部署应用，让我们投入使用！
Demo-LoadGenerator PowerShell 脚本将启动用于针对所有租户数据库运行的工作负荷。
许多 SaaS 应用上的实际负载是偶发性的且不可预测。
为模拟此类型的负载，生成器会在每个租户上生成负载，这些负载具有随机峰值或激增的活动区间。
这些活动激增区间按随机的时间间隔发生。
负载模式需要几分钟的时间才会显现出来。 因此，最好让生成器先运行至少 3 或 4 分钟，然后再进行负载监测。

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Utilities\\Demo-LoadGenerator.ps1 脚本。
1. 按 F5 运行脚本并启动负载生成器。 （暂时保留默认参数值。）
1. 系统将提示登录 Azure 帐户，且如有必要，还需选择要使用的订阅。

负载生成器脚本为目录中的每个数据库启动一个后台作业，然后停止该作业。  如果重新运行该负载生成器脚本，它会先停止任何正在运行的后台作业，然后再启动新的作业。

#### <a name="monitor-the-background-jobs"></a>监视后台作业

如果要控制和监视后台作业，可使用以下 cmdlet：

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 操作

Demo-LoadGenerator.ps1 模拟客户事务的活动工作负载。 以下步骤描述 Demo-LoadGenerator.ps1 所启动的操作的顺序：

1. Demo-LoadGenerator.ps1 在前台启动 LoadGenerator.ps1。
    - 这两个 .ps1 文件均存储在“学习模块\\实用工具\\”文件夹下。

1. LoadGenerator.ps1 循环访问目录中的所有租户数据库。

1. LoadGenerator.ps1 为每个租户数据库启动一个后台 PowerShell 作业： 
    - 后台作业默认运行 120 分钟。
    - 每个作业通过执行 sp_CpuLoadGenerator，在一个租户数据库上引发一个基于 CPU 的负载。  负载的强度和持续时间会有所不同，具体取决于 `$DemoScenario`。 
    - *sp_CpuLoadGenerator* 循环访问 SQL SELECT 语句，该语句引发高 CPU 负载。 SELECT 问题之间的时间间隔会根据参数值而有所不同，以此创建可控的 CPU 负载。 负载级别和间隔是随机的，旨在更加真实地模拟负载。
    - 此 .sql 文件存储在 WingtipTenantDB\\dbo\\StoredProcedures\\ 下。

1. 如果为 `$OneTime = $false`，负载生成器启动后台作业，然后继续运行，每隔 10 秒监视一次是否预配了任何新租户。 如果设置 `$OneTime = $true`，负载生成器将启动后台作业，然后停止在前台运行。 在本教程中，保留为 `$OneTime = $false`。

  如果要停止或重启负载生成器，可使用 Ctrl+C 或停止操作 Ctrl-Break。 

  如果要让负载生成器在前台继续运行，可使用另一个 PowerShell ISE 实例来运行其他 PowerShell 脚本。

&nbsp;

继续下一节之前，让负载生成器在作业调用状态下运行。

## <a name="provision-a-new-tenant"></a>预配新租户

初始部署会创建三个示例租户。 现在，请创建另一个租户，看看这对已部署的应用程序的影响。 在 Wingtip 应用中，用于预配新租户的工作流在[预配和目录教程](saas-dbpertenant-provision-and-catalog.md)中进行了说明。 在此阶段，要创建新的租户，此过程不超过 1 分钟。

1. 新打开一个 PowerShell ISE。
1. 打开 ...\\Learning Modules\Provision and Catalog\\Demo-ProvisionAndCatalog.ps1。
2. 按 **F5** 运行脚本。 （暂时保留默认值。）

   > [!NOTE]
   > 许多 Wingtip SaaS 脚本都使用 $PSScriptRoot 来导航文件夹，以调用其他脚本中的函数。 只有按“F5”执行完整脚本时，才能计算该变量。  通过 F8 突出显示并运行所选项可能会发生错误。 因此按 F5 运行脚本。

新的租户数据库：

- 创建于 SQL 弹性池中。
- 已初始化。
- 已在目录中注册。

成功预配后，新租户的“事件”站点会显示在浏览器中：

![新租户](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

刷新事件中心，使新租户出现在列表中。

## <a name="explore-the-servers-pools-and-tenant-databases"></a>浏览服务器、池以及租户数据库

既然已开始针对该组租户运行加载，让我们看看一些已部署的资源：

1. 在 [Azure 门户](http://portal.azure.com)中，浏览到 SQL 服务器列表，然后打开catalog-dpt-&lt;USER&gt; 服务器。
    - 目录服务器包含两个数据库：tenantcatalog 和 basetenantdb（为了创建新租户而复制的模板数据库）。

   ![数据库](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. 返回到 SQL 服务器列表。

3. 打开保存租户数据库的 tenants1-dpt-&lt;USER&gt; 服务器。

4. 查看以下项：
    - 每个租户数据库都是 50 eDTU 标准池中的弹性标准数据库。
    - Red Maple Racing 数据库（以前预配的租户数据库）。

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>监视池

LoadGenerator.ps1 运行几分钟后，可提供足够的数据，用于开始查看某些监视功能。 这些功能内置于池和数据库中。

浏览到服务器 tenants1-dpt-&lt;user&gt;，并单击“Pool1”查看池的资源利用率。 在以下图表中，负载生成器已运行一个小时。

   ![监视池](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- 第一个图表标记为“资源利用率”，显示池 eDTU 利用率。
- 第二个图表显示池中最活跃的前五个数据库的 eDTU 利用率。

这两个图表说明了弹性池和 SQL 数据库非常适合用于不可预测的 SaaS 应用程序工作负载。
这些图表显示 4 个数据库中的每个数据库都激增到高达 40 eDTU，但所有数据库均可由一个 50 eDTU 池轻松提供支持。 50 eDTU 池甚至还可支持更大的工作负载。
如果将它们预配为独立数据库，则每一个都需要为 S2 (50 DTU) 级以支持突发情况。
4 个独立 S2 数据库的成本几乎是池价格的 3 倍。
在实际情况下，SQL 数据库客户在 200 eDTU 的池中最多可运行 500 个数据库。
有关详细信息，请参阅[性能监视教程](saas-dbpertenant-performance-monitoring.md)。

## <a name="additional-resources"></a>其他资源

- 其他[构建 Wingtip Tickets SaaS 租户各有数据库应用程序的教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)。
- 若要了解弹性池，请参阅[什么是 Azure SQL 弹性池](sql-database-elastic-pool.md)
- 若要了解弹性作业，请参阅[管理扩大的云数据库](sql-database-elastic-jobs-overview.md)
- 若要了解多租户 SaaS 应用程序，请参阅[多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> - 如何部署 Wingtip Tickets SaaS 应用程序
> - 构成该应用的服务器、池和数据库
> - 租户通过目录映射到其数据
> - 如何预配新租户
> - 如何查看池使用率来监视租户活动
> - 如何删除示例资源以停止相关计费

接下来，请尝试[预配和编录教程](saas-dbpertenant-provision-and-catalog.md)。



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

