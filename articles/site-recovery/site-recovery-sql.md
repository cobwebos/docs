---
title: 为具有 Azure Site Recovery 的 SQL Server 设置灾难恢复
description: 本文介绍如何使用 SQL Server 和 Azure Site Recovery 为 SQL Server 设置灾难恢复。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084737"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>为 SQL Server 设置灾难恢复

本文介绍如何帮助保护应用程序的 SQL Server 后端。 为此，可以结合使用 SQL Server 业务连续性和灾难恢复 (BCDR) 技术与 [Azure Site Recovery](site-recovery-overview.md)。

在开始之前，请确保了解 SQL Server 灾难恢复功能。 这些功能包括：

* 故障转移群集
* Always On 可用性组
* 数据库镜像
* 日志传送
* 活动异地复制
* 自动故障转移组

## <a name="combining-bcdr-technologies-with-site-recovery"></a>将 BCDR 技术与 Site Recovery 相结合

应该根据下表中所述的恢复时间目标 (RTO) 和恢复点目标 (RPO) 需求，选择适当的 BCDR 技术来恢复 SQL Server 实例。 将 Site Recovery 与所选技术的故障转移操作相结合，以协调整个应用程序的恢复。

部署类型 | BCDR 技术 | SQL Server 的预期 RTO | SQL Server 的预期 RPO |
--- | --- | --- | ---
Azure 基础结构即服务 (IaaS) 虚拟机 (VM) 上的或本地的 SQL Server。| [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | 将次要副本设为主要副本所花费的时间。 | 由于复制到次要副本是一种异步操作，因此会有一定的数据丢失。
Azure IaaS VM 上的或本地的 SQL Server。| [故障转移群集 (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | 在节点之间进行故障转移所花费的时间。 | 由于 Always On FCI 使用共享存储，因此故障转移时会提供相同的存储实例视图。
Azure IaaS VM 上的或本地的 SQL Server。| [数据库镜像（高性能模式）](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | 强制服务所花费的时间，使用镜像服务器作为温备用服务器。 | 复制是异步的。 镜像数据库可能稍微滞后于主体数据库。 滞后时间通常很小。 但是，如果主体或镜像服务器的系统负载过大，则滞后时间可能很大。<br/><br/>日志传送可用作数据库镜像的补充。 它是异步数据库镜像的理想替代方案。
Azure 上的 SQL 平台即服务 (PaaS)。<br/><br/>此部署类型包括弹性池和 Azure SQL 数据库服务器。 | 活动异地复制 | 触发故障转移后持续 30 秒。<br/><br/>对一个辅助数据库激活故障转移后，所有其他辅助数据库将自动链接到新的主数据库。 | 5 秒 RPO。<br/><br/>活动异地复制使用 SQL Server 的 Always On 技术。 它使用快照隔离以异步方式将主数据库上已提交的事务复制到辅助数据库。<br/><br/>保证辅助数据永不包含部分事务。
Azure 上配置了活动异地复制的 SQL as PaaS。<br/><br/>此部署类型包括 SQL 数据库托管实例、弹性池和 SQL 数据库服务器。 | 自动故障转移组 | 1 小时 RTO。 | 5 秒 RPO。<br/><br/>自动故障转移组在活动异地复制的顶层提供组语义。 但使用相同的异步复制机制。
Azure IaaS VM 上的或本地的 SQL Server。| 使用 Azure Site Recovery 进行复制 | RTO 通常小于 15 分钟。 有关详细信息，请阅读 [Site Recovery 提供的 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 | 为应用程序一致性提供 1 小时保证，为崩溃一致性提供 5 分钟保证。 若要寻求降低 RPO，请使用其他 BCDR 技术。

> [!NOTE]
> 使用 Site Recovery 帮助保护 SQL 工作负荷时需要考虑到几个重要因素：
> * Site Recovery 是应用程序不可知的。 Azure Site Recovery 可帮助保护部署在受支持操作系统上的任何 SQL Server 版本。 有关详细信息，请参阅复制的计算机的[恢复支持矩阵](vmware-physical-azure-support-matrix.md#replicated-machines)。
> * 对于 Azure、Hyper-V、VMware 或物理基础结构中的任何部署，都可以选择使用 Site Recovery。 请遵照本文档末尾的指导来了解[如何使用 Site Recovery 帮助保护 SQL Server 群集](#how-to-help-protect-a-sql-server-cluster)。
> * 确保在计算机上观测到的数据更改率在 [Site Recovery 限制](vmware-physical-azure-support-matrix.md#churn-limits)范围内。 更改率以每秒写入字节数度量。 对于运行 Windows 的计算机，可以选择任务管理器中的“性能”选项卡来查看此更改率。 观测每个磁盘的写入速度。
> * Site Recovery 支持复制存储空间直通上的故障转移群集实例。 有关详细信息，请参阅[如何启用存储空间直通复制](azure-to-azure-how-to-enable-replication-s2d-vms.md)。

## <a name="disaster-recovery-of-an-application"></a>应用程序的灾难恢复

Site Recovery 借助恢复计划来协调整个应用程序的测试故障转移和故障转移。

需要满足一些先决条件才能确保根据需要完全自定义恢复计划。 任何 SQL Server 部署通常都需要一个 Active Directory 部署。 它还需要应用层的连接。

### <a name="step-1-set-up-active-directory"></a>步骤1：设置 Active Directory

在辅助恢复站点上安装 Active Directory，使 SQL Server 能够正常运行。

* **小型企业**：你有少量的应用程序和用于本地站点的单个域控制器。 若要故障转移整个站点，请使用 Site Recovery 复制。 此服务会将域控制器复制到辅助数据中心或 Azure。
* **大中型企业**：可能需要设置其他域控制器。
  - 如果你有大量的应用程序、使用 Active Directory 林，并且想要按应用程序或工作负荷进行故障转移，请在辅助数据中心或 Azure 中设置另一个域控制器。
  -  如果你使用 Always On 可用性组恢复到远程站点，请在辅助站点或 Azure 中设置另一个域控制器。 此域控制器供已恢复的 SQL Server 实例使用。

本文中的说明假设辅助位置中提供了域控制器。 有关详细信息，请参阅[使用 Site Recovery 帮助保护 Active Directory](site-recovery-active-directory.md) 的过程。

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>步骤2：确保与其他层的连接

在目标 Azure 区域中运行数据库层后，确保与应用层和 Web 层建立连接。 提前采取必要的步骤来验证与测试故障转移建立的连接。

通过以下示例了解如何根据连接注意事项设计应用程序：

* [根据云灾难恢复设计应用程序](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [弹性池灾难恢复策略](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>步骤3：与 Always On、活动异地复制和自动故障转移组进行互操作

BCDR 技术 Always On、活动异地复制和自动故障转移组为目标 Azure 区域中运行的 SQL Server 提供辅助副本。 应用程序故障转移的第一步是将此副本指定为主副本。 此步骤假设次要区域中已有一个域控制器。 如果你选择执行自动故障转移，则可能不需要执行该步骤。 只有在完成数据库故障转移后，才故障转移 Web 层和应用层。

> [!NOTE]
> 如果已使用 Site Recovery 来帮助保护 SQL 计算机，则只需创建这些计算机的恢复组，并在恢复计划中添加其故障转移。

使用应用层和 Web 层虚拟机[创建恢复计划](site-recovery-create-recovery-plans.md)。 以下步骤说明如何添加数据库层的故障转移：

1. 导入相应的脚本，用于在[资源管理器虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)和[经典虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中对 SQL 可用性组进行故障转移。 将脚本导入到 Azure 自动化帐户中。

    [![“部署到 Azure”徽标图像](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 将 ASR-SQL-FailoverAG 脚本添加为恢复计划的第一个组的准备操作。

1. 遵照脚本中的说明创建自动化变量。 此变量提供可用性组的名称。

### <a name="step-4-conduct-a-test-failover"></a>步骤4：执行测试故障转移

某些 BCDR 技术（例如 SQL Always On）本身不支持测试故障转移。 我们建议仅在使用此类技术时才运用以下方法。

1. 在 Azure 中托管可用性组副本的 VM 上设置 [Azure 备份](../backup/backup-azure-arm-vms.md)。

1. 触发对恢复计划进行测试故障转移之前，请从上一步骤中进行的备份恢复 VM。

    ![显示用于从 Azure 备份还原配置的窗口屏幕截图](./media/site-recovery-sql/restore-from-backup.png)

1. 在从备份还原的 VM 中[强制仲裁](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)。

1. 将侦听器的 IP 地址更新为测试故障转移网络中的可用地址。

    ![规则窗口和 IP 地址属性对话框的屏幕截图](./media/site-recovery-sql/update-listener-ip.png)

1. 使侦听器联机。

    ![标有 Content_AG 的窗口屏幕截图，其中显示了服务器名称和状态](./media/site-recovery-sql/bring-listener-online.png)

1. 确保故障转移网络中的负载均衡器有一个 IP 地址，它来自与每个可用性组侦听器对应的前端 IP 地址池，以及后端池中的 SQL Server VM。

     ![标题为“SQL-AlwaysOn-LB - 前端 IP 池”的窗口屏幕截图](./media/site-recovery-sql/create-load-balancer1.png)

    ![标题为“SQL-AlwaysOn-LB - 后端 IP 池”的窗口屏幕截图](./media/site-recovery-sql/create-load-balancer2.png)

1. 在后续恢复组中，依次为此恢复计划中的应用层和 Web 层添加故障转移。

1. 执行恢复计划的测试性故障转移，以测试应用程序的端到端故障转移。

## <a name="steps-to-do-a-failover"></a>用于执行故障转移的步骤

在步骤 3 中添加脚本并在步骤 4 中验证该脚本后，可以执行步骤 3 中创建的恢复计划的故障转移。

在测试故障转移和故障转移恢复计划中，应用层和 Web 层的故障转移步骤应该相同。

## <a name="how-to-help-protect-a-sql-server-cluster"></a>如何帮助保护 SQL Server 群集

对于运行 SQL Server Standard Edition 或 SQL Server 2008 R2 的群集，建议使用 Site Recovery 复制来帮助保护 SQL Server。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 到 Azure，以及本地到 Azure

复制到 Azure 区域时，Site Recovery 不支持来宾群集。 SQL Server Standard Edition 也不提供低成本灾难恢复解决方案。 在此方案中，我们建议在主要位置的独立 SQL Server 实例中保护 SQL Server 群集，并在次要位置恢复它。

1. 在主要 Azure 区域或本地站点中配置其他独立 SQL Server 实例。

1. 将此实例配置为需要帮助保护的数据库的镜像。 在高安全模式下配置镜像。

1. 在主要站点上为 [Azure](azure-to-azure-tutorial-enable-replication.md)、[Hyper-V](site-recovery-hyper-v-site-to-azure.md) 或 [VMware VM 和物理服务器](site-recovery-vmware-to-azure-classic.md)配置 Site Recovery。

1. 使用 Site Recovery 复制将新的 SQL Server 实例复制到次要站点。 由于该实例是高安全性镜像副本，因此会将它与主群集同步，但会使用 Site Recovery 复制来复制它。

   ![标准群集插图，其中显示了主要站点、Site Recovery 和 Azure 之间的关系与流](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>故障回复注意事项

对于 SQL Server Standard 群集，完成计划外故障转移后进行故障回复需要执行 SQL Server 备份和还原。 从镜像实例对原始群集执行此操作，并重建镜像。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>与 Site Recovery 配合使用时如何获得 SQL Server 的授权？

SQL Server 的 Site Recovery 复制已涵盖在“软件保障 - 灾难恢复”权益中。 这项权益适用于所有 Site Recovery 方案：本地到 Azure 的灾难恢复，以及跨区域的 Azure IaaS 灾难恢复。 有关详细信息，请参阅 [Azure Site Recovery 定价](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery 是否支持我的 SQL Server 版本？

Site Recovery 是应用程序不可知的。 Azure Site Recovery 可帮助保护部署在受支持操作系统上的任何 SQL Server 版本。 有关详细信息，请参阅复制的计算机的[恢复支持矩阵](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Site Recovery 体系结构](site-recovery-components.md)。
* 对于 Azure 中的 SQL 服务器，请详细了解适用于次要 Azure 区域中的恢复的[高可用性解决方案](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)。
* 对于 SQL 数据库，请详细了解适用于次要 Azure 区域中的恢复的[业务连续性](../sql-database/sql-database-business-continuity.md)和[高可用性](../sql-database/sql-database-high-availability.md)选项。
* 对于本地的 SQL Server 计算机，请详细了解适用于 Azure 虚拟机中的恢复的[高可用性选项](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions)。
