---
title: "在多租户 SaaS 应用中监视多个 Azure SQL 数据库的性能 | Microsoft 文档"
description: "在多租户 SaaS 应用中监视和管理 Azure SQL 数据库和池的性能"
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
ms.date: 10/31/2017
ms.author: sstein
ms.openlocfilehash: 289f1f99b1661e499fa7132887e2f65e086ad689
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>在多租户 SaaS 应用中监视和管理 Azure SQL 数据库和池的性能

本教程将探讨 SaaS 应用程序中使用的几个关键性能管理方案。 使用负载生成器来模拟所有租户数据库中的活动，演示 SQL 数据库和弹性池内置的监视和警报功能。

Wingtip Tickets SaaS Database Per Tenant 应用使用单租户数据模型，在该模型中，每个地点（租户）都有其自己的数据库。 与许多 SaaS 应用程序一样，预期的租户工作负荷模式为不可预测的偶发型。 换言之，票证销售可能会发生在任意时刻。 为了充分利用此典型的数据库使用模式，可将租户数据库部署到弹性数据库池中。 弹性池可以在多个数据库之间共享资源，从而优化解决方案的成本。 使用此类模式时，必须监视数据库和池的资源使用情况，确保实现合理的跨池负载均衡。 此外还需确保各个数据库有足够的资源，且池没有达到其 [eDTU](sql-database-what-is-a-dtu.md) 限制。 本教程探讨如何通过多种方式来监视和管理数据库和池，以及如何采取纠正措施来响应工作负荷的变化。

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 通过运行提供的负载生成器，模拟租户数据库上的使用情况
> * 在租户数据库对负载增加进行响应时，监视其情况
> * 通过扩展弹性池来响应数据库负载增加的情况
> * 预配第二个弹性池，对数据库活动进行负载均衡


若要完成本教程，请确保已完成了以下先决条件：

* 已部署 Wingtip Tickets SaaS Database Per Tenant 应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS Database Per Tenant 应用程序](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>SaaS 性能管理模式简介

数据库性能管理包括：编译和分析性能数据，并对该数据作出反应，即通过调整参数，使应用程序的响应时间维持在可接受的范围。 托管多个租户时，如果工作负荷不可预测，则可以使用弹性数据库池这种经济有效的方式，为一组数据库提供资源并进行相应的管理。 就某些工作负荷模式来说，只要有两个 S3 数据库就可以在池中进行管理。

![应用程序关系图](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

应监视池和池中的数据库，确保二者的性能处于可接受的范围内。 请根据所有数据库的聚合工作负荷的需要来调整池配置，确保池 eDTU 适合总体工作负荷。 请调整单个数据库的最小和最大 eDTU 值，使之符合特定的应用程序要求。

### <a name="performance-management-strategies"></a>性能管理策略

* 如不希望手动监视性能，最有效的方式是设置警报，在数据库或池的性能不在正常范围内时触发该警报。
* **池 eDTU 级别可以上下缩放**，以应对池聚合性能级别的短期波动。 如果该波动定期发生或者发生时间可以预见，**则可让池按计划自动缩放**。 例如，如果知道工作负荷在夜间或周末会减轻，则可向下缩放。
* 要应对较长期的波动，或者应对数据库数目的变化，**可将单个数据库移至其他池**。
* 若要应对单个数据库负载的短期增加，可将单个数据库移出池，为其分配单独的性能级别。 一旦负载降低，则可让该数据库返回池中。 如果预先知道这种情况，则可提前移动数据库，确保数据库的资源始终满足需求，同时避免对池中的其他数据库造成影响。 如果此类需求是可以预测的（例如某个地点因举行热门活动而导致售票剧增），则可将这种管理行为集成到应用程序中。

[Azure 门户](https://portal.azure.com)提供内置的监视和警报功能，可以监视大多数资源。 对于 SQL 数据库来说，监视和警报功能可以在数据库和池上使用。 这种内置的监视和警报功能是特定于资源的，因此对于少量资源使用方便，但在处理大量资源时就不是很方便。

对于用来处理大量资源的大容量方案，可使用 [Log Analytics (OMS)](saas-dbpertenant-log-analytics.md)。 这是单独的 Azure 服务，可针对发出的诊断日志以及日志分析工作区中收集的遥测进行分析。 Log Analytics 可收集多个服务的遥测，可查询和设置警报。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>获取 Wingtip Tickets SaaS Database Per Tenant 应用程序的脚本

在 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Github 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="provision-additional-tenants"></a>预配其他租户

虽然池在只有两个 S3 数据库的情况下就可以做到经济有效，但池中的数据库越多，就平均效果来看也就更加经济有效。 为了很好地理解在大规模情况下如何进行性能监视和管理，本教程要求至少部署 20 个数据库。

如果在之前的教程中已预配一批租户，则可跳到[模拟所有租户数据库上的使用情况](#simulate-usage-on-all-tenant-databases)部分。

1. 在 PowerShell ISE 中，打开…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*。 请让该脚本保持打开状态，因为在本教程中，将要运行多个方案。
1. 设置 **$DemoScenario** = **1**，**预配一批租户**
1. 按 **F5** 运行脚本。

脚本会在不到五分钟的时间内部署 17 个租户。

New-TenantBatch 脚本使用嵌套或链接形式的一组[资源管理器](../azure-resource-manager/index.md)模板来创建一批租户。这些租户在默认情况下会通过复制编录服务器上的数据库 basetenantdb 来创建新的租户数据库，然后将这些数据库注册到目录中，最后再使用租户名称和地点类型初始化这些数据库。 这与应用预配新租户的方式是一致的。 对 basetenantdb 所做的任何更改都将应用到此后预配的任何新租户。 请参阅[架构管理教程](saas-tenancy-schema-management.md)，了解如何对现有租户数据库（包括 basetenantdb 数据库）进行架构更改。

## <a name="simulate-usage-on-all-tenant-databases"></a>模拟所有租户数据库上的使用情况

我们提供了 Demo-PerformanceMonitoringAndManagement.ps1 脚本，用于模拟针对所有租户数据库运行的工作负荷。 负载是使用可用负载方案之一生成的：

| 演示 | 方案 |
|:--|:--|
| #N/A | 生成正常强度负载（约 40 DTU） |
| 3 | 生成单个数据库的突发时间更长且频率更高的负载|
| 4 | 生成单个数据库的 DTU 突发更高的负载（约 80 DTU）|
| 5 | 在正常负载的基础上生成单个租户的高负载（约 95 DTU）|
| 6 | 生成跨多个池的不均衡负载|

负载生成器向每个租户数据库应用仅限 CPU 的综合负载。 该生成器为每个租户数据库启动一个作业，以便定期调用生成负载的存储过程。 负载级别（以 eDTU 为单位）、持续时间和间隔在各个数据库之间并不相同，模拟不可预测的租户活动。

1. 在 PowerShell ISE 中，打开…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*。 请让该脚本保持打开状态，因为在本教程中，将要运行多个方案。
1. 设置 $DemoScenario = 2，生成正常强度负载。
1. 按 **F5** 将负载应用到所有租户数据库。

Wingtip Tickets SaaS Database Per Tenant 是一个 SaaS 应用，SaaS 应用上的实际负载通常是偶发的，不可预测。 为了模拟该负载，负载生成器将生成分布在所有租户上的随机化负载。 显现该负载模式需要几分钟，因此让负载生成器运行 3-5 分钟，然后再尝试按以下部分所述监视负载。

> [!IMPORTANT]
> 负载生成器在本地 PowerShell 会话中作为一系列作业运行。 请让“Demo-PerformanceMonitoringAndManagement.ps1”选项卡保持打开状态！ 如果关闭该选项卡或暂停计算机，负载生成器会停止。 负载生成器保持处于作业调用状态，在这种状态下，它在生成器启动后预配的任何新租户上生成负载。 使用 Ctrl-C 停止调用新作业并退出脚本。 负载生成器将继续运行，但只在现有租户上运行。

## <a name="monitor-resource-usage-using-the-azure-portal"></a>通过 Azure 门户监视资源使用情况

若要监视应用负载后的资源使用情况，请打开门户，转到包含租户数据库的池：

1. 打开 [Azure 门户](https://portal.azure.com)，浏览到 tenants1-dpt-&lt;USER&gt; 服务器。
1. 向下滚动并找到弹性池，并单击“Pool1”。 该池包含目前创建的所有租户数据库。

观察“弹性池监控”和“弹性数据库监控”图表。

池资源利用率聚合了池中所有数据库的数据库利用率。 数据库图表显示了五个最繁忙的数据库：

![数据库图表](./media/saas-dbpertenant-performance-monitoring/pool1.png)

由于池中除排名前五的数据库外还有其他数据库，因此池利用率可以显示没有反映在前五数据库图表中的活动。 有关其他详细信息，请单击“数据库资源利用率”：

![数据库资源利用率](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>对池设置性能警报

对池设置一个警报，该警报在利用率为 \>75% 的情况下触发，如下所示：

1. 在 [Azure 门户](https://portal.azure.com)中打开“Pool1”（位于 tenants1-dpt-\<user\> 服务器上）。
1. 单击“警报规则”，并单击“+ 添加警报”：

   ![添加警报](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. 提供一个名称，例如“DTU 过高”。
1. 设置以下值：
   * **指标 = eDTU 百分比**
   * **条件 = 大于**
   * **阈值 = 75**
   * **时间段 = 过去 30 分钟**
1. 将电子邮件地址添加到“其他管理员电子邮件”框中，然后单击“确定”。

   ![设置警报](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>扩展繁忙的池

如果某个池的聚合负载级别增加过多，以至于耗光了池的资源，达到了 100% 的 eDTU 使用率，则会影响单个数据库的性能，可能会拖累池中所有数据库的查询响应时间。

短期可考虑通过扩展池来提供更多的资源，或者从池中删除数据库（将数据库移至其他池，或者从池中移至单独的服务层）。

长期可考虑优化查询或索引使用，从而改进数据库性能。 根据应用程序对性能问题的敏感程度，最佳做法是在池达到 100% eDTU 使用率之前对其进行扩展。 使用警报提前示警。

可以通过增加生成器生成的负载来模拟忙碌的池。 促使数据库更频繁且更长期地突发，在不更改单个数据库需求的情况下，增加池的聚合负载。 可以通过门户或 PowerShell 轻松地扩展池。 本演练使用门户。

1. 设置 *$DemoScenario* = **3**，生成单个数据库的突发时间更长且频率更高的负载，在不更改每个数据库所要求的高峰负载的情况下，增加池的聚合负载强度。
1. 按 **F5** 将负载应用到所有租户数据库。

1. 转到 Azure 门户中的 Pool1。

在上方的图表中监视池 eDTU 使用率增加的情况。 新的较高的负载可能需要数分钟才能进入，但池却会很快达到最大利用率，当负载逐渐形成新的模式时，就会迅速导致池过载。

1. 若要纵向扩展池，请单击 **Pool1** 页顶部的“配置池”。
1. 将“池 eDTU”设置调整为“100”。 更改池 eDTU 不会更改单个数据库的设置（单个数据库的最大 eDTU 仍为 50）。 可在“配置池”页右侧看到单个数据库的设置。
1. 单击“保存”提交扩展池的请求。

回到“Pool1” > “概览”，查看监视图表。 监视为池提供更多资源后的效果（当然，在数据库较少且负载为随机的情况下，除非先运行一段时间，否则很难轻松地看出结果）。 在查看图表时请注意，在上方的图表中，100% 现在代表 100 eDTU，而在下方的图表中，100% 仍代表 50 eDTU，因为单个数据库的最大值仍为 50 eDTU。

在整个过程中，数据库始终联机且完全可用。 在最后时刻，当每个数据库都准备就绪，可以启用新的池 eDTU 时，会断开所有活动连接。 始终应编写在连接断开后进行重试的应用程序代码，以便重新连接到已扩展池中的数据库。

## <a name="load-balance-between-pools"></a>池间的负载平衡

除了对池进行扩展，还可以创建第二个池，将数据库移到该池中，在这两个池之间实现负载均衡。 为此，必须在第一个池所在的服务器上创建新池。

1. 在 [Azure 门户](https://portal.azure.com)中，打开 tenants1-dpt-&lt;USER&gt; 服务器。
1. 单击“+ 新建池”，在当前服务器上创建一个池。
1. 在“弹性数据库池”模板上，执行以下操作：

    1. 将名称设置为 Pool2。
    1. 将定价层保留为“标准池”。
    1. 单击“配置池”。
    1. 将池 eDTU 设置为 50 eDTU。
    1. 单击“添加数据库”，查看服务器上可添加到 Pool2 的数据库的列表。
    1. 任意选择 10 个数据库，将其移动到新池，然后单击“选择”。 如果已经运行了负载生成器，则服务已经了解到性能配置文件需要比默认大小 50 eDTU 更大的池，并建议从 100 eDTU 开始设置。

    ![建议](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. 对于本教程，保留默认值 50 eDTU，再次单击“选择”。
    1. 选择“确定”创建新池，并将选定的数据库移动到该池中。

创建池及移动数据库移需要数分钟的时间。 移动数据库期间，在需关闭任何打开的连接这一最后时刻到来前，数据库将会保持联机状态，其访问完全不受影响。 只要拥有重试逻辑，随后客户端就会连接到新池中的数据库。

浏览到 Pool2（位于 tenants1-dpt-\<user\> 服务器上），打开池并监视其性能。 如果未看到池，请等待新池预置完成。

现在可看到 Pool1 中的资源使用率下降，而 Pool2 的负载情况与之相似。

## <a name="manage-performance-of-a-single-database"></a>管理单一数据库的性能

如果池中的单一数据库遇到负载持续过高的问题，该数据库会占用池中的大部分资源，影响其他数据库（具体取决于池配置）。 如果该活动要持续一段时间，则可暂时将数据库移出池。 这样数据库就能获得需要的其他资源，并将其与其他数据库隔离开来。

本练习模拟 Contoso 音乐厅在销售热门音乐会票时遇到的负载过高的情况。

1. 在 PowerShell ISE 中打开 \\Demo-PerformanceMonitoringAndManagement.ps1 脚本。
1. 设置 **$DemoScenario = 5，在正常负载的基础上生成单个租户的高负载（约 95 DTU）。**
1. 设置 **$SingleTenantDatabaseName = contosoconcerthall**
1. 使用 **F5** 执行该脚本。


1. 在 [Azure 门户](https://portal.azure.com)，浏览到 tenants1-dpt-\<user\> 服务器上的数据库列表。 
1. 单击“contosoconcerthall”数据库。
1. 单击“contosoconcerthall”所在的池。 在“弹性数据库池”部分中找到该池。

1. 检查“弹性池监视”图表，查找增加的池 eDTU 使用率。 一到两分钟后，较高的负载就会开始进入，很快就会看到池达到 100% 利用率。
2. 检查“弹性数据库监视”屏幕，其中会显示过去一小时内最繁忙的数据库。 据屏幕显示，contosoconcerthall 数据库很快就会成为最繁忙的 5 个数据库之一。
3. 单击“弹性数据库监视”图表将打开“数据库资源利用率”页面，可在其中监视任何数据库。 这样即可隔离 contosoconcerthall 数据库的屏幕。
4. 在数据库列表中，单击 contosoconcerthall。
5. 单击“定价层 (缩放 DTU)”打开“配置性能”页，可在其中设置数据库的独立性能级别。
6. 单击“标准”选项卡打开标准层中的缩放选项。
7. 将 DTU 滑块滑至右侧，选择 100 DTU。 请注意，这对应于服务目标 S3。
8. 单击“应用”将数据库移出池，使之成为标准 S3 数据库。
9. 缩放完成后，在弹性池和数据库边栏选项卡上监视对 contosoconcerthall 数据库以及 Pool1 造成的影响。

一旦 contosoconcerthall 数据库上的高负载降低，就应迅速让该数据库返回池中以降低其成本。 如不确定这种情况何时会发生，则可在数据库上设置一个警报，当数据库的 DTU 使用率降至池中单个数据库的最大值以下时将会触发该警报。 练习 5 介绍如何将数据库移至池中。

## <a name="other-performance-management-patterns"></a>其他性能管理模式

**提前缩放** 在上述练习中，探索了如何缩放独立的数据库，知道要查找的数据库。 如果 Contoso 音乐厅的管理层将即将发生的售票事件告知了 Wingtip，则可提前将数据库移出池。 否则就可能需要在池或数据库上设置一个警报，监视所发生的事件。 并不希望从池中其他抱怨性能下降的租户处了解到这些情况。 如果租户可以预测其对额外资源的需求时间，便可以设置一个 Azure 自动化 Runbook，按定义的计划将数据库移出池，然后再移回去。

**租户自助缩放**：由于缩放是一项可以轻松地通过管理 API 调用的任务，可以轻松地构建一项功能，将租户数据库缩放到面向租户的应用程序中，作为 SaaS 服务的功能提供。 例如，可以让租户自行管理上下缩放，也许还可以将其与租户的计费直接关联在一起！

**根据使用模式将池按计划增大或缩小**

只要聚合租户使用率遵循可预测的使用模式，就可以使用 Azure 自动化按计划对池进行上下缩放。 例如，如果知道工作日下午 6 点后资源需求会下降，则可以将池向下缩放，在第二天早晨 6 点前再向上缩放。



## <a name="next-steps"></a>后续步骤

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 通过运行提供的负载生成器，模拟租户数据库上的使用情况
> * 在租户数据库对负载增加进行响应时，监视其情况
> * 通过扩展弹性池来响应数据库负载增加的情况
> * 预配第二个弹性池，对数据库活动进行负载均衡

[“还原单个租户”教程](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>其他资源

* [构建 Wingtip Tickets SaaS Database Per Tenant 应用程序部署的其他教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [SQL 弹性池](sql-database-elastic-pool.md)
* [Azure 自动化](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) -“设置和使用 Log Analytics”教程
