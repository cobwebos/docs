---
title: 使用 Azure SQL 数据库异地冗余备份对 SaaS 应用进行灾难恢复 | Microsoft Docs
description: 了解发生中断时，应如何使用 Azure SQL 数据库异地冗余备份来恢复多租户 SaaS 应用
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: fc28622ad6d2569d455f82a30a84c47647baa1e2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645328"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>使用异地还原通过数据库备份恢复多租户 SaaS 应用程序

本教程探讨如何对使用“每租户一个数据库”模型实现的多租户 SaaS 应用程序实施完整的灾难恢复方案。 使用[异地还原](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups)，将自动保留的异地冗余备份中的目录和租户数据库还原到备用恢复区域。 中断解决后，使用[异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)将更改后的数据库遣返回原始区域。

![异地还原体系结构](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

异地还原是适用于 Azure SQL 数据库的成本较低的灾难恢复解决方案。 但是，从异地冗余备份恢复可能会导致长达一小时的数据丢失。 此操作可能需要较长时间，具体取决于每个数据库的大小。 

> [!NOTE]
> 若要在恢复应用程序时尽可能将 RPO 和 RTO 降到最低，请使用异地复制，而不是异地还原。

本教程将探讨还原和遣返工作流。 学习如何：
> [!div class="checklist"]

>* 将数据库和弹性池配置信息同步到租户目录中。
>* 在包含应用程序、服务器和池的“恢复”区域中设置镜像环境。   
>* 使用异地还原恢复目录和租户数据库。
>* 中断解决后，使用异地复制遣返租户目录和更改后的租户数据库。
>* 在还原（或遣返）每个数据库后更新目录，以跟踪每个租户数据库的活动副本的当前位置。
>* 确保应用程序和租户数据库始终位于同一 Azure 区域，以降低延迟。 
 

开始本教程之前，需具备以下先决条件：
* 部署 Wingtip Tickets SaaS“每租户一个数据库”应用。 若要在五分钟内完成部署，请参阅[部署并探究 Wingtip Tickets SaaS“每租户一个数据库”应用程序](saas-dbpertenant-get-started-deploy.md)。 
* 安装 Azure PowerShell 中的说明进行操作。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>异地还原恢复模式简介

不管是出于合规还是业务连续性原因，灾难恢复 (DR) 都是许多应用程序的一个重要考虑因素。 如果发生长时间的服务中断，准备充分的 DR 计划可以最大程度地降低业务中断。 基于异地还原的 DR 计划必须实现多个目标：
 * 尽快在所选恢复区域中预留全部所需容量，确保可用于还原租户数据库。
 * 建立可反映原始池和数据库配置的镜像恢复环境。 
 * 如果原始区域恢复联机，可中途取消还原进程。
 * 快速启用租户预配，以便可尽快重新开始新租户加入。
 * 进行优化，以便按优先顺序还原租户。
 * 进行优化，以便在可行情况下同时执行多个步骤以尽快让租户联机。
 * 能够处理故障、可重启和幂等情况。
 * 中断解决后，将数据库遣返回原始区域，同时将对租户的影响降到最低。  

> [!NOTE]
> 将应用程序恢复到部署该应用程序的区域的“配对区域”中。 有关详细信息，请参阅 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。   

本教程使用 Azure SQL 数据库和 Azure 平台的功能解决这些问题：

* [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)可用于尽快预留全部所需容量。 Azure 资源管理器模板用于在恢复区域中预配原始服务器和弹性池的镜像。 预配新租户还需分别创建一个服务器和一个池。
* [弹性数据库客户端库](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL) 可用于创建和维护租户数据库目录。 扩展后的目录包含定期更新的池和数据库配置信息。
* EDCL [分片管理恢复功能](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager)可用于在恢复和遣返期间维护目录中的数据库位置条目。  
* [异地还原](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery)可用于恢复自动维护的异地冗余备份中的目录和租户数据库。 
* [异步还原操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)（按租户的优先顺序发送）将由系统针对每个池进行排队，然后进行批量处理，确保池不会重载。 必要时，可在执行前或执行期间取消这些操作。   
* [异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)可用于在中断后将数据库遣返回原始区域。 使用异地复制可确保不会发生数据丢失，同时可将对租户的影响降到最低。
* [SQL 服务器 DNS 别名](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)可允许目录同步进程连接到位于任何位置的活动目录。  

## <a name="get-the-disaster-recovery-scripts"></a>获取灾难恢复脚本

可从 [Wingtip Tickets SaaS“每租户一个数据库”GitHub 存储库](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)获取本教程使用的 DR 脚本。 请查看[通用指南](saas-tenancy-wingtip-app-guidance-tips.md)，获取下载和取消阻止 Wingtip Tickets 管理脚本的步骤。

> [!IMPORTANT]
> 与所有 Wingtip Tickets 管理脚本一样，DR 脚本的质量仅适合用于演示，不能在生产环境中使用。

## <a name="review-the-healthy-state-of-the-application"></a>查看应用程序的健康状态
启动恢复进程前，请查看应用程序的健康状态。

1. 在 Web 浏览器中打开 Wingtip Tickets 事件中心（http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net - 请将 &lt;user&gt; 替换为部署的用户值）。
    
   滚动到页面底部，注意页脚中的目录服务器名称和位置。 该位置是部署应用的区域。    

   > [!TIP]
   > 将鼠标悬停在该位置上可以放大显示。

   ![原始区域中的事件中心健康状态](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. 选择 Contoso Concert Hall 租户并打开其事件页。

   注意页脚中的租户服务器名称。 该位置与目录服务器的位置相同。

   ![Contoso Concert Hall 原始区域](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. 在 [Azure 门户](https://portal.azure.com)中，查看并打开部署应用的资源组。

   请注意其中部署了应用服务组件和 SQL 数据库服务器的资源和区域。

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>将租户配置同步到目录中

此任务会启动一个进程，用于将服务器、弹性池和数据库的配置同步到租户目录中。 稍后将使用此信息在恢复区域中配置镜像环境。

> [!IMPORTANT]
> 为简单起见，在这些示例中，同步进程以及其他长时间运行的恢复和遣返进程将作为以客户端用户身份运行的本地 PowerShell 作业或会话来实现。 几小时后，登录时颁发的身份验证令牌将会过期，因而作业将会失败。 在生产场景中，长时间运行的进程应作为以服务主体身份运行的某种可靠 Azure 服务来实现。 请参阅[使用 Azure PowerShell 创建具有证书的服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)。 

1. 在 PowerShell ISE 中，打开 ...\Learning Modules\UserConfig.psm1 文件。 将第 10 行和第 11 行中的 `<resourcegroup>` 和 `<user>` 替换为部署应用时使用的值。 保存文件。

2. 在 PowerShell ISE 中，打开 ...\Learning Modules\Business Continuity 和 Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 脚本。

    本教程将运行此 PowerShell 脚本中的每个方案，因此请始终打开此文件。

3. 指定以下设置：

    $DemoScenario = 1：启动一个后台作业，将租户服务器和池配置信息同步到目录中。

4. 选择 F5 运行同步脚本。 

    稍后将使用此信息来确保恢复进程在恢复区域中创建服务器、池和数据库的镜像。  

    ![同步进程](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

使 PowerShell 窗口在后台保持运行，并继续学习本教程的余下部分。

> [!NOTE]
> 同步进程通过 DNS 别名连接到目录。 此别名在还原和遣返期间将修改为指向活动目录。 同步进程会使目录与恢复区域中所做的任何数据库或池配置更改保持同步。 在遣返期间，这些更改将应用到原始区域中的对应资源。

## <a name="geo-restore-recovery-process-overview"></a>异地还原恢复进程概述

异地还原恢复进程会部署应用程序并通过备份将数据库还原到恢复区域。

恢复进程执行以下操作：

1. 在原始区域中禁用 Web 应用的 Azure 流量管理器终结点。 通过禁用该终结点，当原始区域在恢复期间恢复联机时，可防止用户连接到处于无效状态的应用。

2. 在恢复区域中预配恢复目录服务器，异地还原目录数据库，并将 activecatalog 别名更新为指向还原的目录服务器。 更改目录别名可确保目录同步进程始终同步到活动目录中。

3. 将恢复目录中的所有现有租户标记为脱机，防止在租户数据库还原之前对其进行访问。

4. 在恢复区域中预配应用的实例，将其配置为使用该区域中的还原目录。 为将延迟降到最低，将对示例应用进行设计，确保其始终连接到同一区域中的数据库。

5. 预配将要在其中预配新租户的服务器和弹性池。 创建这些资源可确保预配新租户不会对恢复现有租户造成干扰。

6. 更新新租户别名，使其指向恢复区域中的新租户数据库的服务器。 更改此别名可确保恢复区域中任何新租户的数据库都会经过预配。
        
7. 在恢复区域中预配服务器和弹性池以用于还原租户数据库。 这些服务器和池是原始区域中的配置的镜像。 提前预配池以便为还原所有数据库预留所需容量。

    一个区域出现中断，可能对配对区域中的可用资源造成巨大压力。 如果使用异地还原进行 DR，建议快速预留资源。 如果必须在特定区域恢复应用程序，请考虑使用异地复制。 

8. 为恢复区域中的 Web 应用启用流量管理器终结点。 启用此终结点可让应用程序预配新租户。 在此阶段，现有租户仍处于脱机状态。

9. 按优先顺序批量提交还原数据库的请求。 

    * 合理组织分批，使数据库跨所有池并行还原。  

    * 异步提交还原请求，以便请求可快速提交并在每个池中排队执行。

    * 由于还原请求跨所有池并行处理，因此最好跨多个池分布重要租户。 

10. 监视 SQL 数据库服务，确定数据库还原的时间。 还原后的租户数据库将在目录中标记为联机，并且系统会记录该租户数据库的 rowversion 总和。 

    * 在目录中将租户数据库标记为联机后，应用程序可立即访问这些数据库。

    * 租户数据库中 rowversion 值的总和存储在目录中。 此总和充当一个指纹，可让遣返进程确定恢复区域中是否已更新数据库。       

## <a name="run-the-recovery-script"></a>运行恢复脚本

> [!IMPORTANT]
> 本教程通过异地冗余备份还原数据库。 这些备份通常在 10 分钟内可用，但有时可能需要长达一小时才能使用。 备份可用之前，脚本将暂停。

假设部署应用程序的区域发生中断，开始运行恢复脚本：

1. 在 PowerShell ISE 中，打开 ...\Learning Modules\Business Continuity 和 Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 脚本，设置以下值：

    $DemoScenario = 2：通过从异地冗余备份还原将应用恢复到恢复区域。

2. 选择 F5 运行脚本。  

    * 该脚本将在新的 PowerShell 窗口中打开，然后启动一系列并行运行的 PowerShell 作业。 这些作业会将服务器、池和数据库还原到恢复区域。

    * 恢复区域是与部署应用程序的 Azure 区域相关联的配对区域。 有关详细信息，请参阅 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

3. 在 PowerShell 窗口中监视恢复进程的状态。

    ![恢复进程](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> 要了解恢复作业的代码，请查看 ...\Learning Modules\Business Continuity 和 Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs 文件夹中的 PowerShell 脚本。

## <a name="review-the-application-state-during-recovery"></a>在恢复期间查看应用程序状态
在流量管理器中禁用应用程序终结点时，应用程序不可用。 目录将会还原，所有租户都将标记为脱机。 随后，恢复区域中的应用程序终结点将会启用，应用程序恢复联机。 虽然应用程序可用，但事件中心的租户将显示为脱机，直至其数据库还原。 务必将应用程序设计为能够处理脱机租户数据库。

* 恢复目录数据库后但在租户恢复联机前，请在 Web 浏览器中刷新 Wingtip Tickets 事件中心。

    * 在页脚中，注意目录服务器名称现在带有 -recovery 后缀，并且位于恢复区域。

    * 请注意，尚未还原的租户标记为脱机且不可选择。   
 
    ![恢复进程](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * 如果在租户脱机时直接打开租户的事件页，页面将显示租户脱机通知。 例如，在 Contoso Concert Hall 处于脱机状态时，尝试打开 http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall。

    ![恢复进程](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>在恢复区域中预配新租户
即使未还原租户数据库，也可以在恢复区域中预配新租户。 在恢复区域中预配的新租户数据库稍后将与恢复的数据库一起遣返。   

1. 在 PowerShell ISE 中，打开 ...\Learning Modules\Business Continuity 和 Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 脚本，设置以下属性：

    $DemoScenario = 3：在恢复区域中预配新租户。

2. 选择 F5 运行脚本。

3. 预配完成时，浏览器中会打开 Hawthorn Hall 事件页。 

    请注意，Hawthorn Hall 数据库位于恢复区域。

    ![在恢复区域预配的 Hawthorn Hall](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. 在浏览器中刷新 Wingtip Tickets 事件中心页，可以看到其中包含了 Hawthorn Hall。 

    如果预配了 Hawthorn Hall 但没有等待其他租户还原，其他租户可能仍处于脱机状态。

## <a name="review-the-recovered-state-of-the-application"></a>查看应用程序的恢复状态

恢复进程完成后，应用程序和所有租户将在恢复区域中完全正常运行。 

1. PowerShell 控制台窗口中的显示内容指示所有租户都已恢复后，请刷新事件中心。 

    所有租户（包括新租户 Hawthorn Hall）都显示为联机。

    ![事件中心内的已恢复租户和新租户](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. 单击 Contoso Concert Hall 并打开其事件页。 

    在页脚中，注意数据库位于恢复区域所在的恢复服务器上。

    ![恢复区域中的 Contoso](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. 在 [Azure 门户](https://portal.azure.com)中，打开资源组列表。  

    注意已部署的资源组，以及带有 -recovery 后缀的恢复资源组。 恢复资源组包含恢复进程运行期间创建的所有资源，以及中断期间创建的新资源。 

4. 打开恢复资源组，并注意以下各项：

    * 目录和带有 -recovery 后缀的 tenants1 服务器的恢复版本。 这些服务器上的还原目录和租户数据库全部使用在原始区域中使用的名称。

    * tenants2-dpt-&lt;user&gt;-recovery SQL 服务器。 此服务器用于在中断期间预配新租户。

    * 名为 events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt; 的应用服务，即事件应用的恢复实例。

    ![恢复区域中的 Contoso 资源](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. 打开 tenants2-dpt-&lt;user&gt;-recovery SQL 服务器。 请注意，其中包含数据库 hawthornhall 和弹性池 Pool1。 hawthornhall 数据库配置为 Pool1 弹性池中的弹性数据库。

## <a name="change-the-tenant-data"></a>更改租户数据 
此任务将更新还原的租户数据库之一。 遣返进程将已更改的还原数据库复制到原始区域。 

1. 在浏览器中，找到 Contoso Concert Hall 的事件列表，滚动浏览事件，注意最后一个事件 Seriously Strauss。

2. 在 PowerShell ISE 中，打开 ...\Learning Modules\Business Continuity 和 Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 脚本，设置以下值：

    $DemoScenario = 4：在恢复区域中删除某个租户的事件。

3. 选择 F5 执行脚本。

4. 刷新 Contoso Concert Hall 事件页 (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall)，注意，事件 Seriously Strauss 不见了。

到本教程的此阶段，你已经恢复了应用程序（现在正在恢复区域中运行）。 还在恢复区域中预配了新租户并且修改了其中一个还原租户的数据。  

> [!NOTE]
> 此示例中的其他教程不使用处于恢复状态的应用。 若要尝试其他教程，请务必首先遣返应用程序。

## <a name="repatriation-process-overview"></a>遣返进程概述

中断解决后，遣返进程会将应用程序及其数据库还原到原始区域。

![异地还原遣返](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

该进程将：

1. 停止正在进行的任何还原活动，并取消所有未完成的或正在进行的数据库还原请求。

2. 在原始区域中重新激活中断后未发生更改的租户数据库。 这些数据库包括尚未恢复的数据库和已恢复但随后未进行更改的数据库。 重新激活的数据库将与租户最后访问的数据库完全相同。

3. 在原始区域中预配新租户服务器和弹性池的镜像。 完成此操作后，将新租户别名更新为指向此服务器。 更新别名会导致在原始区域（而非恢复区域）中载入新租户。

3. 使用异地复制，将目录从恢复区域移动到原始区域。

4. 更新原始区域中的池配置，使其与中断期间在恢复区域中的所做更改保持一致。

5. 创建托管中断期间创建的所有新数据库所需的服务器和池。

6. 使用异地复制，将还原后更新的还原租户数据库和中断期间预配的所有新租户数据库进行遣返。 

7. 清理还原进程在恢复区域中创建的资源。

若要限制需要遣返的租户数据库的数量，请立即执行步骤 1-3。  

仅需在此情况执行步骤 4：在中断期间修改了恢复区域中的目录。 如果在恢复区域中创建了新租户或更改了任何数据库或池配置，则目录将会更新。

务必注意，执行步骤 7 可将对租户的中断降到最低，并且不会丢失任何数据。 要实现此目标，进程需使用异地复制。

对每个数据库执行异地复制前，需删除原始区域中的对应数据库。 然后，恢复区域中的数据库将进行异地复制，原始区域中会创建一个次要副本。 复制完成后，租户将在目录中标记为脱机，这会中断与恢复区域中的数据库的任何连接。 接下来，数据库将进行故障转移，任何挂起的事务都会在辅助数据库中进行处理，因此不会丢失任何数据。 

故障转移时，数据库角色将会反转。 原始区域中的辅助数据库将变成读写主数据库，恢复区域中的数据库将变成只读辅助数据库。 目录中的租户条目将更新为引用原始区域中的数据库，租户将标记为联机。 此时，数据库遣返完成。 

应使用重试逻辑编写应用程序，以确保其可在连接中断时自动重新连接。 当应用程序使用目录代理重新连接时，它们将连接到原始区域中的遣返数据库。 可以选择在下班后遣返数据库，不过这种短暂的连接中断通常不容易察觉。

遣返数据库后，可删除恢复区域中的辅助数据库。 原始区域中的数据库将再次使用异地还原进行 DR 保护。

步骤 8 会删除恢复区域中的资源（包括恢复服务器和池）。

## <a name="run-the-repatriation-script"></a>运行遣返脚本
假设中断已解决，开始运行遣返脚本。

如果一直按照本教程操作，脚本会立即重新激活原始区域中的 Fabrikam Jazz Club 和 Dogwood Dojo，因为它们未发生更改。 然后，脚本将遣返新租户 Hawthorn Hall 和 Contoso Concert Hall，因为它们已被修改。 该脚本还会遣返预配 Hawthorn Hall 时更新的目录。
  
1. 在 PowerShell ISE 中，打开 ...\Learning Modules\Business Continuity 和 Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 脚本，请验证是否仍在其 PowerShell 实例中运行目录同步进程。 必要时，可通过进行如下设置将其重启：

    $DemoScenario = 1：开始将租户服务器、池和数据库配置信息同步到目录中.

    选择 F5 运行脚本。

2.  然后，进行如下设置以启动遣返进程：

    $DemoScenario = 5：将应用遣返回原始区域。

    选择 F5，在新的 PowerShell 窗口中运行恢复脚本。 遣返需要几分钟，可在 PowerShell 窗口进行监视。

3. 在脚本运行期间，刷新事件中心页 (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall)。

    请注意，所有租户都为联机状态，并且可通过此进程访问。

4. 选择 Fabrikam Jazz Club 将其打开。 如果未修改此租户，请注意，页脚中的服务器已还原为原始服务器。

5. 打开或刷新 Contoso Concert Hall 事件页。 请注意页脚中，数据库仍在恢复服务器上。 

6. 遣返进程完成后，刷新 Contoso Concert Hall 事件页，注意，数据库现在位于原始区域。

7. 再次刷新事件中心并打开 Hawthorn Hall。 请注意，其数据库也位于原始区域中。 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>在遣返后清理恢复区域的资源
为安全起见，请在遣返完成后删除恢复区域中的资源。 

> [!IMPORTANT]
> 立即删除这些资源以避免其产生任何费用。

还原进程在恢复资源组中创建所有恢复资源。 清理进程删除此资源组并删除目录中对这些资源的所有引用。 

1. 在 PowerShell ISE 中，打开 ...\Learning Modules\Business Continuity 和 Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 脚本，设置：
    
    $DemoScenario = 6：删除恢复区域中的过时资源。

2. 选择 F5 运行脚本。

清理脚本后，应用程序将恢复原样。 现在可再次运行脚本或尝试其他教程。

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>设计应用程序，确保应用和数据库位于相同位置 
设计应用程序，使其始终从租户数据库所在区域中的实例进行连接。 此设计可降低应用程序与数据库之间的延迟。 此优化有望使“应用到数据库”交互比“用户到应用”交互更加密切。  

在遣返期间的某些时段，租户数据库可能遍布在恢复区域和原始区域。 对于每个数据库，应用会通过对租户服务器名称执行 DNS 查找来查找数据库所在的区域。 在 SQL 数据库中，服务器名称是别名。 使用别名的服务器名称包含区域名称。 如果应用程序不与数据库位于相同区域，它将重定向到数据库服务器所在区域中的实例。 重定向到数据库所在区域中的实例可将应用与数据库之间的延迟降到最低。  

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：
> [!div class="checklist"]

>* 使用租户目录保存定期更新的配置信息，以允许在其他区域中创建镜像恢复环境。
>* 使用异地还原将 Azure SQL 数据库恢复到恢复区域。
>* 更新租户目录以反映已还原的租户数据库位置。 
>* 使用 DNS 别名可使应用程序全程连接到租户目录，并且无需进行重新配置。
>* 中断解决后，使用异地复制将恢复后的数据库遣返回原始区域。

请尝试学习[使用数据库异地复制对多租户 SaaS 应用程序进行灾难恢复](saas-dbpertenant-dr-geo-replication.md)教程，了解如何使用异地复制动态缩短恢复大规模多租户应用程序所需的时间。

## <a name="additional-resources"></a>其他资源

[其他基于 Wingtip SaaS 应用程序编写的教程](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
