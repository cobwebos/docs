---
title: 在多租户 SaaS 应用中监视分片多租户 Azure SQL 数据库的性能 | Microsoft 文档
description: 在多租户 SaaS 应用中监视和管理分片多租户 Azure SQL 数据库的性能
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 75431715b5948525e92c99b778842d26a684da82
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753434"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>在多租户 SaaS 应用中监视和管理分片多租户 Azure SQL 数据库的性能

本教程将探讨 SaaS 应用程序中使用的几个关键性能管理方案。 使用负载生成器来模拟分片多租户数据库间的活动，演示 SQL 数据库内置的监视和警报功能。

Wingtip Tickets SaaS 多租户数据库应用使用分片多租户数据模型，其中场地（租户）数据是通过可能跨多个数据库的租户 ID 来分布的。 与许多 SaaS 应用程序一样，预期的租户工作负荷模式为不可预测的偶发型。 换言之，票证销售可能会发生在任意时刻。 若要利用此典型的数据库使用模式，将可对数据库进行纵向扩展或缩小，以优化解决方案的成本。 使用此类模式时，必须监视数据库资源的使用情况，以确保实现合理的可能跨多个数据库的负载均衡。 此外还需确保各个数据库有足够的资源，且没有达到其 [DTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus) 限制。 本教程探讨如何通过多种方式来监视和管理数据库，以及如何采取纠正措施来响应工作负载的变化。

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 通过运行提供的负载生成器，模拟分片多租户数据库上的使用情况
> * 在数据库响应负载增加时对其进行监视
> * 通过扩展数据库来响应数据库负载增加的情况
> * 将租户预配置为单租户数据库

若要完成本教程，请确保已完成了以下先决条件：

* 已部署 Wingtip Tickets SaaS 多租户数据库应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS 多租户数据库应用程序](saas-multitenantdb-get-started-deploy.md)。
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>SaaS 性能管理模式简介

数据库性能管理包括：编译和分析性能数据，并对该数据作出反应，即通过调整参数，使应用程序的响应时间维持在可接受的范围。 

### <a name="performance-management-strategies"></a>性能管理策略

* 若不希望手动监视性能，那么最有效的方式就是设置警报，在数据库的性能不在正常范围内时触发该警报。
* 可以上下缩放 DTU 级别，以应对数据库性能级别的短期波动。 如果该波动定期发生或者发生时间可以预见，则可让数据库按计划自动缩放。 例如，如果知道工作负荷在夜间或周末会减轻，则可向下缩放。
* 若要应对较长期的波动，或者应对租户中的变化，可将单个租户移至其他数据库。
* 若要应对单个租户负载的短期增加，可将单个租户移出数据库，并为其分配单独的性能级别。 一旦负载降低，则可让该租户返回多租户数据库中。 如果预先知道这种情况，则可提前移动租户，以确保数据库的资源始终可满足需求，同时避免对多租户数据库中的其他租户造成影响。 如果此类需求是可以预测的（例如某个地点因举行热门活动而导致售票剧增），则可将这种管理行为集成到应用程序中。

[Azure 门户](https://portal.azure.com)提供内置的监视和警报功能，可以监视大多数资源。 对于 SQL 数据库，在数据库上提供了监视和警报功能。 这种内置的监视和警报功能是特定于资源的，因此，对于使用少量资源的方案比较方便，但在处理大量资源时就不太适用了。

对于使用许多资源的大容量应用场景，可以使用 [Log Analytics](https://azure.microsoft.com/services/log-analytics/)。 这是单独的 Azure 服务，可针对发出的诊断日志以及日志分析工作区中收集的遥测进行分析。 Log Analytics 可收集多个服务的遥测，可查询和设置警报。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>获取 Wingtip Tickets SaaS 多租户数据库应用程序源代码和脚本

在 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="provision-additional-tenants"></a>预配其他租户

为了更好地理解性能监视和管理在缩放中的工作方式，本教程需要在分片多租户数据库中具有多个租户。

如果在之前的教程中已预配一批租户，则可跳到[模拟所有租户数据库上的使用情况](#simulate-usage-on-all-tenant-databases)部分。

1. 在 PowerShell ISE 中，打开…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*。 请让该脚本保持打开状态，因为在本教程中，将要运行多个方案。
1. 设置 **$DemoScenario** = **1**，_预配一批租户_
1. 按 **F5** 运行脚本。

该脚本可在几分钟内将 17 位租户部署到多租户数据库。 

New-TenantBatch 脚本在分片多租户数据库内使用唯一的租户密钥创建新租户，并使用租户名称和场地类型对其进行初始化。 这与应用预配新租户的方式是一致的。 

## <a name="simulate-usage-on-all-tenant-databases"></a>模拟所有租户数据库上的使用情况

我们提供了 Demo-PerformanceMonitoringAndManagement.ps1 脚本，用于模拟针对多租户数据库运行的工作负载。 负载是使用可用负载方案之一生成的：

| 演示 | 场景 |
|:--|:--|
| 2 | 生成正常强度的负载（约 30 DTU） |
| 3 | 生成单个租户的突发时间更长的负载|
| 4 | 生成单个租户的 DTU 突发个数更高的负载（约 70 个 DTU）|
| 5 | 在单个租户上生成高强度负载（约 90 个 DTU）和在所有其他租户上生成正常强度负载 |

负载生成器向每个租户数据库应用仅限 CPU 的综合负载。 该生成器为每个租户数据库启动一个作业，以便定期调用生成负载的存储过程。 负载级别（以 DTU 计）、持续时间和间隔在各个数据库之间都是不同的，用以模拟不可预测的租户活动。

1. 在 PowerShell ISE 中，打开…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*。 请让该脚本保持打开状态，因为在本教程中，将要运行多个方案。
1. 设置 **$DemoScenario** = **2**，生成正常强度负载
1. 按 **F5** 将负载应用到所有租户。

Wingtip Tickets SaaS 多租户数据库是一个 SaaS 应用，SaaS 应用上的实际负载通常是偶发的，且不可预测。 为了模拟该负载，负载生成器将生成分布在所有租户上的随机化负载。 显现该负载模式需要几分钟，因此让负载生成器运行 3-5 分钟，然后再尝试按以下部分所述监视负载。

> [!IMPORTANT]
> 负载生成器在新的 PowerShell 窗口中作为一系列作业运行。 如果关闭会话，负载生成器将停止运行。 负载生成器保持处于作业调用状态，在这种状态下，它在生成器启动后预配的任何新租户上生成负载。 使用 Ctrl-C 停止调用新作业并退出脚本。 负载生成器将继续运行，但只在现有租户上运行。

## <a name="monitor-resource-usage-using-the-azure-portal"></a>通过 Azure 门户监视资源使用情况

若要监视应用负载后的资源使用情况，请打开多租户数据库 tenants1 的门户，其中包含租户：

1. 打开 [Azure 门户](https://portal.azure.com)，并浏览到服务器 *tenants1-mt-&lt;USER&gt;*。
1. 向下滚动并找到数据库，然后单击“tenants1”。 此分片多租户数据库包含目前创建的所有租户。

![数据库图表](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

观察 DTU 图表。

## <a name="set-performance-alerts-on-the-database"></a>对数据库设置性能警报

对数据库设置一个警报，以在利用率为 \>75% 的情况下触发，如下所示：

1. 在 [Azure 门户](https://portal.azure.com)中，打开 tenants1 数据库（在 *tenants1-mt-&lt;USER&gt;* 服务器上）。
1. 单击“警报规则”，并单击“+ 添加警报”：

   ![添加警报](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. 提供一个名称，例如“DTU 过高”。
1. 设置以下值：
   * **指标 = DTU 百分比**。
   * **条件 = 大于**
   * **阈值 = 75**。
   * **时间段 = 过去 30 分钟**
1. 将电子邮件地址添加到“其他管理员电子邮件”框中，然后单击“确定”。

   ![设置警报](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>扩展繁忙的数据库

如果某个数据库的负载级别增加过多，以至于耗光了数据库的资源，达到了 100% 的 DTU 使用率，则会影响数据库的性能，进而可能会拖累查询响应时间。

对于短期的情况，可考虑扩展数据库以提供其他资源，或删除多租户数据库中的租户（将其从多租户数据库移动到独立数据库）。

长期可考虑优化查询或索引使用，从而改进数据库性能。 根据应用程序对性能问题的敏感程度，最佳做法是在数据库达到 100% DTU 使用率之前就对其进行扩展。 使用警报提前示警。

你可以通过增加生成器生成的负载来模拟忙碌的数据库。 促使租户数据库突发更频繁且更长时间地负载，在不更改单个租户需求的情况下，增加多租户数据库的负载。 可以通过门户或 PowerShell 轻松地扩展数据库。 本演练使用门户。

1. 设置 *$DemoScenario* = **3**，生成单个数据库突发时间更长且频率更高的负载，以在不更改每个租户所要求的高峰负载的情况下，增加数据库的聚合负载强度。
1. 按 **F5** 将负载应用到所有租户数据库。
1. 在 Azure 门户中转到“tenants1”数据库。

在上方的图表中监视数据库 DTU 使用率增加的情况。 出现新的较高负载可能需要数分钟的时间，但数据库却会很快达到最大利用率，并且当负载逐渐稳定到新的模式时，就会迅速导致数据库过载。

1. 若要扩展数据库，请单击“设置”边栏选项卡中的“定价层(缩放 DTU)”。
1. 将 **DTU** 设置调整为 **100**。 
1. 单击“应用”提交扩展数据库的请求。

返回到“tenants1” > “概述”，查看监视图表。 监视为数据库提供更多资源后的效果（当然，在租户较少且负载为随机的情况下，除非先运行一段时间，否则并不是很容易就能看出结果）。 在查看图表时请注意，在上方的图表中，100% 现在代表 100 个 DTU，而在下方的图表中，100% 仍代表 50 个 DTU。

在整个过程中，数据库始终联机且完全可用。 应始终编写在连接断开后进行重试的应用程序代码，以便重新连接到数据库。

## <a name="provision-a-new-tenant-in-its-own-database"></a>在其自己数据库中预配新租户 

分片多租户模型允许选择是与其他租户一起在多租户数据库中预配新租户，还是在其自己的数据库中预配租户。 通过在其自己的数据库中预配租户，可受益于单独数据库中固有的隔离，可让你独立于其他租户管理该租户的性能、独立于其他租户还原该租户等。例如，可以选择将免费试用版或常规客户放入多租户数据库，将高级客户放在单独数据库中。  如果创建了独立的单租户数据库，则仍可在弹性池中集中管理它们，以优化资源成本。

如果已在其自己的数据库中预配了新租户，则可跳过以下几个步骤。

1. 在 PowerShell ISE 中，打开…\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*。 
1. 修改 $TenantName = "Salix Salsa" 和 $VenueType  = "dance"
1. 设置 **$Scenario** = **2**，在新的单租户数据库中预配租户
1. 按 **F5** 运行脚本。

脚本将在单独的数据库中预配此租户，在目录中注册数据库和租户，然后在浏览器中打开租户的“事件”页。 刷新“事件中心”页，将看到已将“Salix Salsa”添加为场地。

## <a name="manage-performance-of-a-single-database"></a>管理单一数据库的性能

如果在多租户数据库中的单个租户遇到持续的高负载，则它可能会占用数据库资源并影响同一数据库中的其他租户。 如果该活动可能要持续一段时间，则可暂时将该租户从数据库移动到其自己的单租户数据库。 这样租户就能获得其需要的额外资源，并将其与其他租户完全隔离开来。

本练习模拟 Salix Salsa 在销售热门活动票时遇到的负载过高的情况。

1. 打开 …\\Demo-PerformanceMonitoringAndManagement.ps1 脚本。
1. 设置 $DemoScenario = 5，在正常负载的基础上生成单个租户的高负载（约 90 个 DTU）。
1. 设置 $SingleTenantName = Salix Salsa
1. 使用 **F5** 执行该脚本。

转到门户并导航到“salixsalsa” > “概述”，以查看监视图表。 

## <a name="other-performance-management-patterns"></a>其他性能管理模式

**租户自助服务扩展**

由于缩放是一项可以通过管理 API 轻松调用的任务，因此，可以很容易地构建这项能力，将租户数据库缩放到面向租户的应用程序中，并作为 SaaS 服务的功能提供。 例如，可以让租户自行管理上下缩放，也许还可以将其与租户的计费直接关联在一起！

**根据使用模式将数据库按计划进行纵向缩放**

只要聚合租户使用率遵循可预测的使用模式，就可以使用 Azure 自动化按计划对数据库进行纵向缩放。 例如，如果知道工作日下午 6 点后资源需求会下降，则可以将数据库纵向减小，在第二天早晨 6 点前再纵向扩展。

## <a name="next-steps"></a>后续步骤

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 通过运行提供的负载生成器，模拟分片多租户数据库上的使用情况
> * 在数据库响应负载增加时对其进行监视
> * 通过扩展数据库来响应数据库负载增加的情况
> * 将租户预配置为单租户数据库

## <a name="additional-resources"></a>其他资源

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure 自动化](../automation/automation-intro.md)