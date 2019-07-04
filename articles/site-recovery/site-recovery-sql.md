---
title: 使用 SQL Server 和 Azure Site Recovery 为 SQL Server 设置灾难恢复 | Microsoft Docs
description: 本文介绍如何使用 SQL Server 和 Azure Site Recovery 为 SQL Server 设置灾难恢复。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491779"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>为 SQL Server 设置灾难恢复

本文介绍如何结合使用 SQL Server 业务连续性和灾难恢复 (BCDR) 技术与 [Azure Site Recovery](site-recovery-overview.md) 来保护应用程序的 SQL Server 后端。

在开始之前，请确保您了解 SQL Server 灾难恢复功能，包括故障转移群集、 Always On 可用性组、 数据库镜像、 日志传送、 活动地域复制和自动故障转移组。

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>使用 Site Recovery 的 SQL Server BCDR 技术集成的灾难恢复建议

所选的到恢复 SQL server BCDR 技术应根据 RTO 和 RPO 需要根据下表。 建立该选择后，Site Recovery 可与故障转移操作的这项技术来安排恢复整个应用程序集成。

**部署类型** | **BCDR 技术** | **SQL 的预期的 RTO** | **SQL 的预期的 RPO** |
--- | --- | --- | ---
Azure IaaS VM 上或在本地 SQL Server| **[Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | 等效于使主数据库的辅助副本所花费的时间 | 复制是异步到辅助副本，因此不会丢失一些数据。
Azure IaaS VM 上或在本地 SQL Server| **[故障转移群集 (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | 等效于在节点之间故障转移所花费的时间 | 它使用共享的存储，因此同一视图的存储实例位于故障转移。
Azure IaaS VM 上或在本地 SQL Server| **[数据库镜像 （高性能模式）](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | 等效于强制服务，镜像服务器用作温备用服务器所花费的时间。 | 复制是异步的。 镜像数据库可能会滞后稍微滞后于主体数据库。 该间隔是通常很小，howvever，可以变得很明显，如果主体或镜像服务器的系统负载过大。<br></br>日志传送可以是数据库镜像的补充，并且是异步数据库镜像的良好替代方法
作为在 Azure 上的 PaaS SQL<br></br>（弹性池，SQL 数据库服务器） | **活动异地复制** | 30 秒一次触发<br></br>当将故障转移激活到辅助数据库之一时，所有其他辅助数据库会自动链接到新的主数据库。 | 5 秒的 RPO<br></br>活动异地复制利用 SQL Server 以异步方式将主数据库上提交的事务复制到使用快照隔离的辅助数据库的 Alwayson 技术。 <br></br>保证辅助数据永不包含部分事务。
SQL 作为 PaaS 配置了 Azure 上的活动异地复制<br></br>（SQL 数据库托管实例，弹性池时，SQL 数据库服务器） | **自动故障转移组** | RTO 为 1 小时 | 5 秒的 RPO<br></br>自动故障转移组提供基于活动异地复制的组语义，但使用相同的异步复制机制。
Azure IaaS VM 上或在本地 SQL Server| **使用 Azure Site Recovery 复制** | 通常不到 15 分钟。 [阅读更多](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)若要了解有关 Azure Site Recovery 提供 RTO SLA 的信息。 | 应用程序一致性和崩溃一致性的 5 分钟时间的 1 小时。 

> [!NOTE]
> 几个重要注意事项： 保护 SQL 工作负荷使用 Azure Site Recovery 时：
> * Azure Site Recovery 不区分应用程序，因此，由 Azure Site Recovery 保护任何版本的受支持的操作系统部署的 SQL server。 [了解详细信息](vmware-physical-azure-support-matrix.md#replicated-machines)。
> * 您可以选择要用于在 Azure、 HYPER-V、 VMware 或物理基础结构的任何部署 Site Recovery。 请按照[指导](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2)如何保护 SQL Server 群集使用 Azure Site Recovery 文档结尾处。
> * 确保数据更改率 （写入字节数 / 秒） 在计算机上观察到位于[Site Recovery 限制](vmware-physical-azure-support-matrix.md#churn-limits)。 对于 windows 计算机，你可以查看此性能选项卡上任务管理器中。 观察写入每个磁盘的速度。
> * Azure Site Recovery 支持存储空间直通上复制的故障转移群集实例。 [了解详细信息](azure-to-azure-how-to-enable-replication-s2d-vms.md)。
 

## <a name="disaster-recovery-of-application"></a>应用程序的灾难恢复

**Azure Site Recovery 会安排的测试故障转移和故障转移的整个应用程序的恢复计划的帮助。** 

有一些先决条件以确保恢复计划完全自定义根据您的需求。 任何 SQL Server 部署通常需要 Active Directory。 它还需要在应用程序层的连接。

### <a name="step-1-set-up-active-directory"></a>步骤 1：设置 Active Directory

在辅助恢复站点上安装 Active Directory，使 SQL Server 能够正常运行。

* **小型企业** - 如果使用少量的应用程序和适用于本地站点的单个域控制器，并且想要故障转移整个站点，我们建议使用 Site Recovery 复制将域控制器复制到辅助数据中心或 Azure。
* **中大型企业** - 如果使用大量的应用程序和 Active Directory 林，并且想要按应用程序或工作负荷进行故障转移，我们建议在辅助数据中心或 Azure 中设置附加的域控制器。 如果使用 Always On 可用性组恢复到远程站点，我们建议在辅助站点或 Azure 上配置另一个域控制器，供已恢复 SQL Server 实例使用。

本文中的说明假设辅助位置提供了域控制器。 [详细了解](site-recovery-active-directory.md)如何使用 Site Recovery 保护 Active Directory。

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>步骤 2：确保与其他应用程序层和 web 层的连接

确保数据库层是目标 Azure 区域中启动并运行，一旦有与该应用程序和 web 层的连接。 应预先采取必要的步骤来验证与测试故障转移的连接。

了解如何设计应用程序的几个示例使用的连接性注意事项：
* [为云灾难恢复设计应用程序](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [弹性池灾难恢复策略](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>步骤 3：与 Always On 集成，主动异地复制或应用程序故障转移的自动故障转移组

Always On 的 BCDR 技术，主动异地复制和自动故障转移组具有目标 Azure 区域中运行的 SQL server 的次要副本。 因此，应用程序故障转移的第一步是使此副本成为主数据库 （假设已在辅助数据库中有域控制器）。 此步骤可能不需要你选择进行自动故障转移。 仅数据库故障转移完成后，您应故障转移 web 或应用程序层。

> [!NOTE] 
> 如果保护了使用 Azure Site Recovery 的 SQL 机，只需创建这些计算机的恢复组并将其故障转移添加恢复计划中。

[创建恢复计划](site-recovery-create-recovery-plans.md)与应用程序和 web 层虚拟机。 请按照以下步骤来添加数据库层的故障转移：

1. 将脚本导入到 Azure 自动化帐户中。 这包括用于在 [Resource Manager 虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)和[经典虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中对 SQL 可用性组进行故障转移的脚本。

    [![部署到 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. 将 ASR-SQL-FailoverAG 添加为恢复计划的第一个组的准备操作。

1. 按照脚本中提供的说明创建一个自动化变量来提供可用性组的名称。

### <a name="step-4-conduct-a-test-failover"></a>步骤 4：执行测试故障转移

某些 BCDR 技术，如 SQL Always On 本身不支持测试故障转移。 因此，我们建议使用以下方法**仅当与此类技术集成时**:

1. 在虚拟机上设置 [Azure 备份](../backup/backup-azure-arm-vms.md)，该虚拟机在 Azure 中托管可用性组副本。

1. 触发对恢复计划进行测试性故障转移之前，请从上一步骤中进行的备份恢复虚拟机。

    ![从 Azure 备份进行还原](./media/site-recovery-sql/restore-from-backup.png)

1. 在从备份还原的虚拟机中[强制实施仲裁](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)。

1. 将侦听程序的 IP 更新为测试故障转移网络中可用的某个 IP。

    ![更新侦听器 IP](./media/site-recovery-sql/update-listener-ip.png)

1. 使侦听器联机。

    ![使侦听器联机](./media/site-recovery-sql/bring-listener-online.png)

1. 使用在前端 IP 池下创建的与每个可用性组侦听器对应的一个 IP 与在后端池中添加的 SQL 虚拟机创建一个负载均衡器。

     ![创建负载均衡器 - 前端 IP 池](./media/site-recovery-sql/create-load-balancer1.png)

    ![创建负载均衡器 - 后端池](./media/site-recovery-sql/create-load-balancer2.png)

1. 添加应用程序层，在此恢复计划在后续恢复组中后跟 web 层的故障转移。 
1. 执行测试故障转移的恢复计划，以测试应用程序的端到端故障转移。

## <a name="steps-to-do-a-failover"></a>用于执行故障转移的步骤

一旦你已在步骤 3 中的恢复计划中添加脚本，并验证它通过执行测试故障转移与在步骤 4 中的专用方法，你可以执行步骤 3 中创建的恢复计划的故障的转移。

请注意，应用程序和 web 层的故障转移步骤应在测试故障转移和故障转移恢复计划中相同。

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>如何保护 SQL Server 群集 (standard edition/SQL Server 2008 R2)

对于运行 SQL Server Standard 版本或 SQL Server 2008 R2 的群集，建议使用 Site Recovery 复制来保护 SQL Server。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 到 Azure 和本地到 Azure

Site Recovery 不提供来宾群集支持，将复制到 Azure 区域时。 SQL Server 也不会为 Standard 版本提供低成本灾难恢复解决方案。 在此方案中，我们建议保护独立的主位置中的 SQL Server 到 SQL Server 群集，并在辅助副本中恢复它。

1. 配置其他独立 SQL Server 实例上的主要 Azure 区域或在本地站点。
1. 将此实例配置为想要保护的数据库的镜像。 在高安全模式下配置镜像。
1. 在主站点上配置 Site Recovery ([Azure](azure-to-azure-tutorial-enable-replication.md)， [HYPER-V](site-recovery-hyper-v-site-to-azure.md)或[VMware Vm/物理服务器)](site-recovery-vmware-to-azure-classic.md)。
1. 使用 Site Recovery 复制来将新的 SQL Server 实例复制到辅助站点。 由于它是高安全性镜像副本，它将与主群集同步，但使用 Site Recovery 复制复制。


![标准群集](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>故障回复注意事项

对于 SQL Server Standard 群集，计划外故障转移后的故障回复需要从镜像实例 SQL Server 备份并还原到原始群集，并重新建立镜像。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>如何 does SQL 获取授权时使用 Azure Site Recovery 保护？
适用于 SQL Server 的 Azure Site Recovery 复制已在“软件保障”下进行过介绍，即适用于所有 Azure Site Recovery 方案（本地到 Azure 的灾难恢复或跨区域的 Azure IaaS 灾难恢复）的灾难恢复权益。 [了解详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>将 Azure Site Recovery 支持我的 SQL 版本？
Azure Site Recovery 不区分应用程序。 因此，可以通过 Azure Site Recovery 保护任何版本的受支持的操作系统部署的 SQL server。 [了解详细信息](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>后续步骤
* [详细了解](site-recovery-components.md) Site Recovery 体系结构。
* 对于在 Azure 中的 SQL 服务器，详细了解如何[高可用性解决方案](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)辅助 Azure 区域中的恢复。
* 对于在 Azure 中的 SQL 数据库，详细了解如何[业务连续性](../sql-database/sql-database-business-continuity.md)并[高可用性](../sql-database/sql-database-high-availability.md)辅助 Azure 区域中的恢复选项。
* 适用于在本地、 在 SQL server 计算机[了解详细信息](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions)有关恢复 Azure 虚拟机中的高可用性选项。
