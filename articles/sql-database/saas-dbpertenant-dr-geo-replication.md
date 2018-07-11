---
title: 使用 Azure SQL 数据库异地复制实现 SaaS 应用的灾难恢复 | Microsoft Docs
description: 了解在发生中断时，如何使用 Azure SQL 数据库异地复制来恢复多租户 SaaS 应用
keywords: sql 数据库教程
services: sql-database
author: AyoOlubeko
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: f2ad92118c00f08e5dcdd4a8a12f007308b3fbd1
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2018
ms.locfileid: "34645787"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>使用数据库异地复制实现多租户 SaaS 应用程序的灾难恢复

本教程探讨如何对使用“租户各有一个数据库”(database-per-tenant) 模型实施的多租户 SaaS 应用程序完全实现灾难恢复。 若要防止应用中断，可以使用[_异地复制_](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)在备用恢复区域中创建目录和租户数据库的副本。 如果发生中断，可以快速故障转移到这些副本，以恢复正常业务运营。 故障转移后，原始区域中的数据库将成为恢复区域中数据库的辅助副本。 这些副本重新联机后，会自动同步到恢复区域中数据库的状态。 解决中断后，故障回复到原始生产区域中的数据库。

本教程探讨故障转移和故障回复工作流。 将了解如何执行以下操作：
> [!div classs="checklist"]

>* 将数据库和弹性池配置信息同步到租户目录中
>* 在备用区域中设置由应用程序、服务器和池构成的恢复环境
>* 使用异地复制将目录和租户数据库复制到恢复区域
>* 将应用程序以及目录和租户数据库故障转移到恢复区域 
>* 稍后解决中断后，将应用程序以及目录和租户数据库故障转移回到原始区域
>* 故障转移每个租户数据库后，更新目录以跟踪每个租户的数据库的主要位置
>* 确保应用程序和主要租户数据库始终共置在同一 Azure 区域，以降低延迟  
 

在开始学习本教程之前，请确保满足以下先决条件：
* 已部署 Wingtip Tickets SaaS“每租户一个数据库”应用。 若要在五分钟内完成部署，请参阅[部署和浏览 Wingtip Tickets SaaS 租户各有数据库应用程序](saas-dbpertenant-get-started-deploy.md)  
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>异地复制恢复模式简介

![恢复体系结构](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
不管是出于合规还是业务连续性原因，灾难恢复 (DR) 都是许多应用程序的一个重要考虑因素。 如果发生长时间的服务中断，精心准备的灾难恢复计划可以最大程度地减轻对业务造成的干扰。 使用异地复制可将 RPO 和 RTO 降到最低，因为它在恢复区域中维护数据库副本，并且可以在短时间内将应用程序故障转移到该区域。

基于异地复制的灾难恢复计划包括三个不同的组成部分：
* 设置 - 创建和维护恢复环境
* 恢复 - 发生中断时将应用和数据库故障转移到恢复环境
* 遣返 - 解决应用程序问题后，将应用和数据库故障转移回到原始区域 

必须仔细考虑所有组成部分，尤其是大规模操作时。 在总体上，该计划必须实现多个目标：

* 设置
    * 在恢复区域中建立和维护镜像映像环境。 在此恢复环境中创建弹性池和复制任何独立数据库可以在恢复区域中预留容量。 维护此环境的工作包括预配新租户数据库时复制这些数据库。  
* 恢复
    * 必须使用缩减的恢复环境来尽量降到日常成本，则必须扩展池和独立数据库，以便在恢复区域中获得完整的操作容量
    * 尽快在恢复区域中启用新租户预配  
    * 按优先顺序对租户还原进行优化
    * 优化为在可行的情况下同时执行多个步骤，以尽快让租户联机
    * 弹性应对故障、可重启且幂等
    * 如果原始区域恢复联机，可中途取消过程。
* 遣返 
    * 将数据库从恢复区域故障转移到原始区域中的副本，同时尽量减轻对租户的影响：不会发生数据丢失，且将每个租户的脱机持续时间减至最少。   

在本教程中，这些难题是使用 Azure SQL 数据库和 Azure 平台的功能解决的：

* [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)：尽快预留全部所需容量。 Azure 资源管理器模板用于在恢复区域中预配生产服务器和弹性池的镜像映像。
* [异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)：为所有数据库创建以异步方式复制的只读辅助副本。 在中断期间，故障转移到恢复区域中的副本。  解决中断后，故障回复到原始生产区域中的数据库，且不会丢失任何数据。
* [异步](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)故障转移操作按租户的优先顺序，以最大程度地减少大量数据库的故障转移时间。
* [分片管理恢复功能](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager)：在恢复和遣返期间更改目录中的数据库条目。 无需重新配置应用，这些功能就能让应用连接到租户数据库，而不管位置如何。
* [SQL Server DNS 别名](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)：实现新租户的无缝预配，不管应用在哪个区域中运行。 使用 DNS 别名还能让目录同步进程连接到活动目录，而不管该目录位于哪个位置。

## <a name="get-the-disaster-recovery-scripts"></a>获取灾难恢复脚本 

> [!IMPORTANT]
> 与所有 Wingtip Tickets 管理脚本一样，灾难恢复脚本的质量仅适合用于演示，而不能在生产环境中使用。 

[Wingtip Tickets SaaS“租户各有一个数据库”GitHub 存储库](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)中提供了本教程使用的恢复脚本和 Wingtip 应用程序源代码。 请查看[通用指南](saas-tenancy-wingtip-app-guidance-tips.md)，获取下载和取消阻止 Wingtip Tickets 管理脚本的步骤。

## <a name="tutorial-overview"></a>教程概述
本教程先使用异地复制在不同的区域中创建 Wingtip Tickets 应用程序及其数据库的副本。 然后故障转移到此区域，以模拟中断后进行恢复的操作。 完成上述过程后，该应用程序将在恢复区域中完全正常运行。

在稍后的单独遣返步骤中，我们将恢复区域中的目录和租户数据库故障转移到原始区域。 在整个遣返过程中，应用程序和数据库都会保持可用。 完成上述过程后，该应用程序将在原始区域中完全正常运行。

> [!Note]
> 应用程序将恢复到部署该应用程序的区域的_配对区域_中。 有关详细信息，请参阅 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

## <a name="review-the-healthy-state-of-the-application"></a>查看应用程序的正常状态

启动恢复进程前，请查看应用程序的健康状态。
1. 在 Web 浏览器中打开 Wingtip Tickets 事件中心（http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net - 请将 &lt;user&gt; 替换为部署的用户值）。
    * 滚动到页面底部，注意页脚中的目录服务器名称和位置。 该位置是部署应用的区域。
    *提示：将鼠标悬停在位置上可以放大显示内容。*
    ![原始区域中的事件中心正常状态](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. 单击 Contoso Concert Hall 租户并打开其事件页。
    * 在页脚中，注意租户服务器名称。 位置与目录服务器的位置相同。

3. 在 [Azure 门户](https://portal.azure.com)中，打开部署应用的资源组
    * 注意服务器所部署到的区域。 

## <a name="sync-tenant-configuration-into-catalog"></a>将租户配置同步到目录中

此任务启动一个过程，将服务器、弹性池和数据库的配置同步到租户目录中。 该过程能在目录中使此信息保持最新。  该过程将处理原始区域或恢复区域中的活动目录。 恢复过程中将使用配置信息来确保恢复环境与原始环境保持一致，并且在稍后的遣返过程中，确保原始区域与恢复环境中所做的任何更改保持一致。 目录还可用于跟踪租户资源的恢复状态

> [!IMPORTANT]
> 为简单起见，同步过程以及其他长时间运行的恢复和遣返过程在这些教程中作为客户端用户登录后运行的本地 Powershell 作业或会话来实施。 在若干小时后，登录时颁发的身份验证令牌将会过期，因而作业将会失败。 在生产场景中，长时间运行的过程应该实施为某种以服务主体运行的可靠 Azure 服务。 请参阅[使用 Azure PowerShell 创建具有证书的服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)。

1. 在 PowerShell ISE 中，打开 ...\Learning Modules\UserConfig.psm1 文件。 将第 10 行和第 11 行中的 `<resourcegroup>` 和 `<user>` 替换为部署应用时使用的值。  保存该文件！

2. 在 *PowerShell ISE* 中，打开 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 脚本，并设置：
    * **$DemoScenario = 1**：启动一个后台作业，将租户服务器和池配置信息同步到目录中

3. 按 **F5** 运行同步脚本。 此时会打开一个新的 PowerShell 会话用于同步租户资源的配置。
![同步过程](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

让 PowerShell 窗口在后台保持运行，并继续学习本教程的余下部分。 

> [!Note]
> 同步过程通过 DNS 别名连接到目录。 此别名在还原和遣返期间将修改为指向活动目录。 同步过程会使目录与恢复区域中所做的任何数据库或池配置更改保持同步。  在遣返期间，这些更改将应用到原始区域中的对应资源。

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>在恢复区域中创建辅助数据库副本

此任务启动一个过程来部署重复的应用实例，并将目录和所有租户数据库复制到恢复区域。

> [!Note]
> 本教程将在 Wingtip Tickets 示例应用程序中添加异地复制保护。 在生产场景中，对于使用异地复制的应用程序，每个租户从一开始就预配了异地复制的数据库。 请参阅[使用 Azure SQL 数据库设计高可用性服务](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. 在 *PowerShell ISE* 中，打开 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 脚本，并设置以下值：
    * **$DemoScenario = 2**：创建镜像映像恢复环境，并复制目录和租户数据库

2. 按 **F5** 运行脚本。 此时会打开一个新 PowerShell 会话用于创建副本。
![同步过程](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>查看正常的应用程序状态
此时，应用程序在原始区域中正常运行，并受异地复制的保护。  恢复区域中存在所有数据库的只读辅助副本。 
1. 在 Azure 门户中查看资源组，可以看到，在恢复区域中创建的某个资源组带有-recovery 后缀。 

1. 浏览恢复资源组中的资源。  

1. 单击 _tenants1-dpt-&lt;user&gt;-recovery_ 服务器上的 Contoso Concert Hall 数据库。  单击左侧的“异地复制”。 

    ![Contoso Concert 异地复制链接](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

在 Azure 区域地图中，注意原始区域中主数据库与恢复区域中辅助数据库之间的异地复制链接。  

## <a name="fail-over-the-application-into-the-recovery-region"></a>将应用程序故障转移到恢复区域中

### <a name="geo-replication-recovery-process-overview"></a>异地复制恢复过程概述

恢复脚本执行以下任务：

1. 在原始区域中禁用 Web 应用的流量管理器终结点。 禁用该终结点可以防止在恢复期间当原始区域恢复联机时，用户连接到处于无效状态的应用。

1. 在恢复区域中使用目录数据库强制故障转移来使其成为主数据库，并将 _activecatalog_ 别名更新为指向恢复目录服务器。

1. 将 _newtenant_ 别名更新为指向恢复区域中的租户服务器。 更改此别名可确保在恢复区域中预配任何新租户的数据库。 

1. 将恢复目录中的所有现有租户标记为脱机，以防止在故障转移租户数据库之前对其进行访问。

1. 在恢复区域中更新所有弹性池和复制的独立数据库的配置，以便在原始区域中镜像其配置。 （仅当在正常操作期间缩减了恢复环境中的池或复制的数据库以降低成本时，才需要执行此任务）。

1. 为恢复区域中的 Web 应用启用流量管理器终结点。 启用此终结点可让应用程序预配新租户。 在此阶段，现有的租户仍处于脱机状态。

1. 提交请求批，以强制按优先顺序故障转移数据库。
    * 合理地组织分批，使数据库跨所有池并行故障转移。
    * 使用异步操作提交故障转移请求，以便快速提交这些请求，并可以同时处理多个请求。

   > [!Note]
   > 在中断场景中，原始区域中的主数据库处于脱机状态。  在辅助区域中强制故障转移会断开与主要区域的连接，且不会尝试应用任何剩余的排队事务。 在类似于本教程所述的灾难恢复演练场景中，故障转移时出现任何更新活动都可能会导致丢失某些数据。 稍后在遣返期间，将恢复区域中的数据库故障转移回到原始区域时，会使用正常故障转移来确保数据不会丢失。

1. 监视 SQL 数据库服务，确定数据库何时已故障转移。 租户数据库在故障转移后，会更新目录，以记录租户数据库的恢复状态，并将租户标记为联机。
    * 在目录中将租户数据库标记为联机后，应用程序立即就能访问这些数据库。
    * 租户数据库中 rowversion 值的总和存储在目录中。 此值充当一个指纹，可让遣返过程确定数据库是否已在恢复区域中更新。

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>运行脚本来故障转移到恢复区域

现在，我们假设部署应用程序的区域发生中断。请运行恢复脚本：

1. 在 *PowerShell ISE* 中，打开 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 脚本，并设置以下值：
    * **$DemoScenario = 3**：通过故障转移到副本，将应用恢复到恢复区域

2. 按 **F5** 运行脚本。  
    * 该脚本将在新 PowerShell 窗口中打开，然后启动一系列并行运行的 PowerShell 作业。 这些作业将租户数据库故障转移到恢复区域。
    * 恢复区域是部署应用程序的 Azure 区域相关联的配对区域。 有关详细信息，请参阅 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

3. 在 PowerShell 窗口中监视恢复过程的状态。
    ![故障转移过程](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> 若要浏览恢复作业的代码，请查看 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs 文件夹中的 PowerShell 脚本。

### <a name="review-the-application-state-during-recovery"></a>在恢复期间查看应用程序状态
在流量管理器中禁用应用程序终结点时，应用程序不可用。 将目录故障转移到恢复区域并将所有租户标记为脱机后，应用程序会重新联机。 尽管应用程序可用，但在故障转移每个租户的数据库之前，这些租户在事件中心会显示脱机状态。 必须将应用程序设计为能够处理脱机租户数据库。

1. 恢复目录数据库后的短时间内，请在 Web 浏览器中刷新 Wingtip Tickets 事件中心。
    * 在页脚中，注意目录服务器名称现在带有 _-recovery_ 后缀，并且位于恢复区域中。
    * 注意租户尚未还原，标记为脱机且不可选择。  

    > [!Note]
    > 如果只是恢复少量的几个数据库，则在完成恢复之前可能无法刷新浏览器，因此看不到脱机的租户。 
 
    ![事件中心脱机](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * 如果直接打开脱机租户的“事件”页，将会看到“租户脱机”通知。 例如，如果 Contoso Concert Hall 处于脱机状态，请尝试打开 http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall ![Contoso 脱机页](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>在恢复区域中预配新租户
即使是在故障转移所有现有的租户数据库之前，也可以在恢复区域中预配新租户。  

1. 在 *PowerShell ISE* 中，打开 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 脚本，并设置以下属性：
    * **$DemoScenario = 4**：在恢复区域中预配新租户

2. 按 **F5** 运行脚本并预配新租户。 

3. 完成时，浏览器中会打开 Hawthorn Hall 事件页。 在页脚中可以看到，恢复区域中预配了 Hawthorn Hall 数据库。
    ![Hawthorn Hall 事件页](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. 在浏览器中刷新 Wingtip Tickets 事件中心页，可以看到其中包含了 Hawthorn Hall。 
    * 如果预配了 Hawthorn Hall 但没有等待其他租户还原，其他租户可能仍处于脱机状态。


## <a name="review-the-recovered-state-of-the-application"></a>查看应用程序的恢复状态

恢复进程完成后，应用程序和所有租户将在恢复区域中完全正常运行。 

1. PowerShell 控制台窗口中的显示内容指示所有租户都已恢复后，请刷新事件中心。  所有租户（包括新租户 Hawthorn Hall）都将显示为联机。

    ![事件中心内的已恢复租户和新租户](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. 在 [Azure 门户](https://portal.azure.com)中，打开资源组列表。  
    * 注意已部署的资源组，以及带有 -recovery 后缀的恢复资源组。  恢复资源组包含恢复进程运行期间创建的所有资源，以及中断期间创建的新资源。  

3. 打开恢复资源组，并注意以下各项：
    * 目录和带有 -recovery 后缀的 tenants1 服务器的恢复版本。  这些服务器上的还原目录和租户数据库全部使用在原始区域中使用的名称。

    * tenants2-dpt-&lt;user&gt;-recovery SQL 服务器。  此服务器用于在中断期间预配新租户。
    *   名为 _events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt_; 的应用服务，即“事件”应用的恢复实例。 

    ![Azure 恢复资源 ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. 打开 _tenants2-dpt-&lt;user&gt;-recovery_ SQL 服务器。  请注意，其中包含数据库 hawthornhall 和弹性池 Pool1。  _hawthornhall_ 数据库配置为 _Pool1_ 弹性池中的弹性数据库。

5. 导航回到资源组，单击 _tenants1-dpt-&lt;user&gt;-recovery_ 服务器上的 Contoso Concert Hall 数据库。 单击左侧的“异地复制”。
    
    ![故障转移后的 Contoso 数据库](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>更改租户数据 
此任务更新租户数据库之一。 

1. 在浏览器中，找到 Contoso Concert Hall 的事件列表并记下最后一个事件名称。
2. 在 *PowerShell ISE* 中的 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 脚本内设置以下值：
    * **$DemoScenario = 5**：从恢复区域中的某个租户删除事件
3. 按 **F5** 执行脚本
4. 刷新 Contoso Concert Hall 事件页（http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall - 请将 &lt;user&gt; 替换为部署的用户值），注意最后一个事件已删除。

## <a name="repatriate-the-application-to-its-original-production-region"></a>将应用程序遣返到其原始生产区域

此任务将应用程序遣返到其原始区域。 在实际场景中，请在解决中断后启动遣返。

### <a name="repatriation-process-overview"></a>遣返过程概述

![遣返体系结构](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

遣返过程：
1. 取消所有未完成的或正在进行的数据库还原请求。
2. 将 _newtenant_ 别名更新为指向源区域中的租户服务器。 更改此别名可确保现在会在源区域中预配任何新租户的数据库。
3. 将所有更改的租户数据植入原始区域
4. 按优先顺序故障转移租户数据库。

故障转移能够有效地将数据库移到原始区域。 故障转移数据库时，将会删除所有打开的连接，数据库将有几秒钟不可用。 应使用重试逻辑编写应用程序，确保它们能够再次连接。  可以选择在下班后遣返数据库，不过这种短暂的连接断开通常不容易察觉到。 


### <a name="run-the-repatriation-script"></a>运行遣返脚本
现在，我们假设中断已解决，接下来运行遣返脚本。

1. 在 *PowerShell ISE* 中，打开 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 脚本。

2. 检查目录同步进程是否仍在其 PowerShell 实例中运行。  必要时，可通过进行如下设置将其重启：
    * **$DemoScenario = 1**：开始将租户服务器、池和数据库配置信息同步到目录中
    * 按 **F5** 运行脚本。

3.  然后，进行如下设置以启动遣返进程：
    * **$DemoScenario = 6**：将应用遣返到其原始区域
    * 按 **F5** 在新 PowerShell 窗口中运行恢复脚本。  遣返过程需要花费几分钟时间，可在 PowerShell 窗口中监视其进度。
    ![遣返过程](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. 在脚本运行期间，刷新事件中心页 (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net)
    * 请注意，所有租户都为联机状态，并且可通过此进程访问。

5. 遣返完成后，刷新事件中心并打开 Hawthorn Hall 的事件页。 请注意，此数据库已遣返到原始区域。
    ![已遣返事件中心](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>妥善设计应用程序，确保将应用和数据库共置在一起 
设计应用程序，使其始终从租户数据库所在区域中的实例进行连接。 此设计可降低应用程序与数据库之间的延迟。 此优化有望使“应用到数据库”交互比“用户到应用”交互更加密切。  

在遣返期间的某些时段，租户数据库可能遍布在恢复区域和原始区域。 对于每个数据库，应用会通过对租户服务器名称执行 DNS 查找来查找数据库所在的区域。 在 SQL 数据库中，服务器名称是别名。 使用别名的服务器名称包含区域名称。 如果应用程序不与数据库位于相同区域，它将重定向到数据库服务器所在区域中的实例。  重定向到数据库所在区域中的实例可将应用与数据库之间的延迟降到最低。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：
> [!div classs="checklist"]

>* 将数据库和弹性池配置信息同步到租户目录中
>* 在备用区域中设置由应用程序、服务器和池构成的恢复环境
>* 使用异地复制将目录和租户数据库复制到恢复区域
>* 将应用程序以及目录和租户数据库故障转移到恢复区域 
>* 解决中断后，将应用程序、目录和租户数据库故障回复到原始区域

可以在[业务连续性概述](sql-database-business-continuity.md)文档中详细了解 Azure SQL 数据库为实现业务连续性而提供的技术。

## <a name="additional-resources"></a>其他资源

* [其他基于 Wingtip SaaS 应用程序编写的教程](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
