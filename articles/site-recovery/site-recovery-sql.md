---
title: 使用 SQL Server 和 Azure Site Recovery 为 SQL Server 设置灾难恢复 | Microsoft Docs
description: 本文介绍如何使用 SQL Server 和 Azure Site Recovery 为 SQL Server 设置灾难恢复。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 14fbca6dea735ed1ee13fca20f19379cc2c4d0a9
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742325"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>为 SQL Server 设置灾难恢复

本文介绍如何帮助保护应用程序的 SQL Server 后端。 为此, 请结合使用 SQL Server 业务连续性和灾难恢复 (BCDR) 技术和[Azure Site Recovery](site-recovery-overview.md)。

在开始之前, 请确保了解 SQL Server 的灾难恢复功能。 这些功能包括：

* 故障转移群集
* Always On 可用性组
* 数据库镜像
* 日志传送
* 活动异地复制
* 自动故障转移组

## <a name="combining-bcdr-technologies-with-site-recovery"></a>结合 BCDR 技术与 Site Recovery

根据下表中所述的恢复时间目标 (RTO) 和恢复点目标 (RPO) 需求, 选择 BCDR 技术来恢复 SQL Server 实例。 将 Site Recovery 与所选技术的故障转移操作结合起来, 协调整个应用程序的恢复。

部署类型 | BCDR 技术 | 预期 SQL Server 的 RTO | SQL Server 的预期 RPO |
--- | --- | --- | ---
在 Azure 基础结构即服务 (IaaS) 虚拟机 (VM) 或本地 SQL Server。| [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | 使辅助副本成为主副本所花的时间。 | 由于对辅助副本的复制是异步的, 因此会出现一些数据丢失。
SQL Server Azure IaaS VM 或本地。| [故障转移群集 (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | 在节点之间进行故障转移所花费的时间。 | 由于 Always On FCI 使用共享存储, 因此在故障转移时可以使用相同的存储实例视图。
SQL Server Azure IaaS VM 或本地。| [数据库镜像 (高性能模式)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | 强制执行服务所需的时间, 该服务使用镜像服务器作为热备用服务器。 | 复制是异步的。 镜像数据库可能稍微滞后于主体数据库。 Lag 通常很小。 但是, 如果主体服务器或镜像服务器的系统负载过大, 则可能会变得很大。<br/><br/>日志传送可以作为数据库镜像的补充。 这是异步数据库镜像的理想替代方法。
Azure 上的 SQL as 平台即服务 (PaaS)。<br/><br/>此部署类型包括弹性池和 Azure SQL 数据库服务器。 | 活动异地复制 | 触发故障转移后30秒。<br/><br/>为某个辅助数据库激活故障转移时, 所有其他辅助数据库会自动链接到新的主数据库。 | 5秒的 RPO。<br/><br/>活动异地复制使用 SQL Server 的 Always On 技术。 它通过使用快照隔离以异步方式将主数据库上已提交的事务复制到辅助数据库。<br/><br/>保证辅助数据永远不会有部分事务。
在 Azure 上配置了活动异地复制的 PaaS 作为 PaaS 的 SQL。<br/><br/>此部署类型包含 SQL 数据库托管实例、弹性池和 SQL 数据库服务器。 | 自动故障转移组 | 一小时的 RTO。 | 5秒的 RPO。<br/><br/>自动故障转移组在活动异地复制的顶层提供组语义。 但使用相同的异步复制机制。
SQL Server Azure IaaS VM 或本地。| 与 Azure Site Recovery 复制 | RTO 通常小于15分钟。 若要了解详细信息, 请阅读[Site Recovery 提供的 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 | 一小时的时间, 用于应用程序一致性和5分钟, 用于实现故障一致性。 如果要寻找更低的 RPO, 请使用其他 BCDR 技术。

> [!NOTE]
> 使用 Site Recovery 保护 SQL 工作负荷时, 有几个重要的注意事项:
> * Site Recovery 应用程序不可知。 Site Recovery 有助于保护在受支持的操作系统上部署的任何 SQL Server 版本。 若要了解详细信息, 请参阅用于恢复复制的计算机的[支持矩阵](vmware-physical-azure-support-matrix.md#replicated-machines)。
> * 对于 Azure、Hyper-v、VMware 或物理基础结构中的任何部署, 你都可以选择使用 Site Recovery。 请按照本文末尾的指导操作,[了解如何使用 Site Recovery 帮助保护 SQL Server 群集](#how-to-help-protect-a-sql-server-cluster)。
> * 确保计算机上观察到的数据更改率在[Site Recovery 限制](vmware-physical-azure-support-matrix.md#churn-limits)范围内。 更改率以写入字节数/秒为单位。 对于运行 Windows 的计算机, 可以通过在任务管理器中选择 "**性能**" 选项卡来查看此更改率。 观察每个磁盘的写入速度。
> * Site Recovery 支持复制存储空间直通上的故障转移群集实例。 若要了解详细信息, 请参阅[如何启用存储空间直通复制](azure-to-azure-how-to-enable-replication-s2d-vms.md)。

## <a name="disaster-recovery-of-an-application"></a>应用程序的灾难恢复

Site Recovery 利用恢复计划来协调整个应用程序的测试故障转移和故障转移。

需要确保恢复计划完全根据你的需求进行自定义的先决条件。 任何 SQL Server 部署通常都需要 Active Directory 部署。 它还需要应用层的连接。

### <a name="step-1-set-up-active-directory"></a>步骤 1：设置 Active Directory

在辅助恢复站点中设置 Active Directory, 以便 SQL Server 正常运行。

* **小型企业**:你有少量的应用程序和本地站点的单个域控制器。 如果要对整个站点进行故障转移, 请使用 Site Recovery 复制。 此服务将域控制器复制到辅助数据中心或 Azure。
* **大中型企业**:你可能需要设置其他域控制器。
  - 如果有大量应用程序, 有一个 Active Directory 的林, 并且想要按应用程序或工作负荷进行故障转移, 请在辅助数据中心或 Azure 中设置另一个域控制器。
  -  如果使用 Always On 可用性组恢复到远程站点, 请在辅助站点或 Azure 中设置另一个域控制器。 此域控制器用于已恢复的 SQL Server 实例。

本文中的说明假定域控制器在辅助位置中可用。 若要了解详细信息, 请参阅[帮助保护 Active Directory 与 Site Recovery](site-recovery-active-directory.md)的过程。

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>步骤 2：确保与其他层的连接

在目标 Azure 区域中运行数据库层后, 确保你已连接到应用程序层和 web 层。 提前执行必要的步骤以使用测试故障转移来验证连接性。

若要了解如何设计应用程序以了解连接性注意事项, 请参阅以下示例:

* [为云灾难恢复设计应用程序](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [弹性池灾难恢复策略](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>步骤 3：与 Always On、活动异地复制和自动故障转移组互操作

BCDR 技术 Always On、活动异地复制和自动故障转移组具有在目标 Azure 区域中运行 SQL Server 的辅助副本。 应用程序故障转移的第一步是将此副本指定为主副本。 此步骤假设辅助数据库中已有域控制器。 如果选择执行自动故障转移, 则可能不需要执行此步骤。 只有在数据库故障转移完成后, 才会对 web 和应用程序层进行故障转移。

> [!NOTE]
> 如果已帮助使用 Site Recovery 保护 SQL 计算机, 只需创建这些计算机的恢复组, 并在恢复计划中添加其故障转移。

使用应用程序和 web 层虚拟机[创建恢复计划](site-recovery-create-recovery-plans.md)。 以下步骤说明如何添加数据库层的故障转移:

1. 导入脚本以在[资源管理器虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)和[经典虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中对 SQL 可用性组进行故障转移。 将脚本导入到 Azure 自动化帐户。

    [!["部署到 Azure" 徽标的图像](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 添加 ASR-Asr-sql-failoverag 脚本, 作为恢复计划第一组的预操作。

1. 按照脚本中提供的说明创建自动化变量。 此变量提供可用性组的名称。

### <a name="step-4-conduct-a-test-failover"></a>步骤 4：执行测试故障转移

某些 BCDR 技术 (例如 SQL Always On) 本身不支持测试故障转移。 建议*仅在使用此类技术时使用*以下方法。

1. 在 Azure 中托管可用性组副本的 VM 上设置[Azure 备份](../backup/backup-azure-arm-vms.md)。

1. 在触发恢复计划的测试故障转移之前, 请从上一步中创建的备份恢复 VM。

    ![显示用于从 Azure 备份还原配置的窗口的屏幕截图](./media/site-recovery-sql/restore-from-backup.png)

1. 在从备份还原的虚拟机中[强制实施仲裁](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)。

1. 更新侦听器的 IP 地址, 使其成为测试故障转移网络中可用的地址。

    !["规则窗口和 IP 地址属性" 对话框的屏幕截图](./media/site-recovery-sql/update-listener-ip.png)

1. 使侦听器联机。

    ![标记为 "Content_AG" 的窗口的屏幕截图, 显示服务器名称和状态](./media/site-recovery-sql/bring-listener-online.png)

1. 创建负载均衡器。 对于每个可用性组侦听器, 请从前端 IP 池创建一个 IP 地址。 同时将 SQL Server VM 添加到后端池。

     ![标题为 "SQL-AlwaysOn-前端 IP 池" 的窗口的屏幕截图](./media/site-recovery-sql/create-load-balancer1.png)

    ![标题为 "SQL-AlwaysOn-后端 IP 池" 的窗口的屏幕截图](./media/site-recovery-sql/create-load-balancer2.png)

1. 在以后的恢复组中, 添加应用层的故障转移, 并在此恢复计划中添加你的 web 层。

1. 对恢复计划进行测试性故障转移, 以测试应用程序的端到端故障转移。

## <a name="steps-to-do-a-failover"></a>用于执行故障转移的步骤

在步骤3中添加脚本并在步骤4中验证后, 可以执行步骤3中创建的恢复计划的故障转移。

应用程序层和 web 层的故障转移步骤在测试故障转移和故障转移恢复计划中应该是相同的。

## <a name="how-to-help-protect-a-sql-server-cluster"></a>如何帮助保护 SQL Server 群集

对于运行 SQL Server Standard edition 或 SQL Server 2008 R2 的群集, 建议使用 Site Recovery 复制来帮助保护 SQL Server。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 到 Azure 和本地到 Azure

在复制到 Azure 区域时, Site Recovery 不提供来宾群集支持。 SQL Server Standard 版本还不提供低成本灾难恢复解决方案。 在这种情况下, 我们建议在主位置中保护 SQL Server 群集的独立 SQL Server 实例, 并在辅助副本中恢复它。

1. 在主 Azure 区域或本地站点上配置其他独立 SQL Server 实例。

1. 将实例配置为要帮助保护的数据库的镜像。 在高安全性模式下配置镜像。

1. 在[Azure](azure-to-azure-tutorial-enable-replication.md)、 [Hyper-v](site-recovery-hyper-v-site-to-azure.md)或[VMware vm 和物理服务器](site-recovery-vmware-to-azure-classic.md)的主站点上配置 Site Recovery。

1. 使用 Site Recovery 复制将新的 SQL Server 实例复制到辅助站点。 由于它是高安全性镜像副本, 因此会将它与主群集同步, 但会使用 Site Recovery 复制进行复制。

   ![显示主站点、Site Recovery 和 Azure 之间的关系和流的标准群集的图像](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>故障回复注意事项

对于 SQL Server Standard 群集, 非计划的故障转移后的故障回复需要 SQL Server 备份和还原。 此操作可从镜像实例完成, 并重新建立镜像。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>使用 Site Recovery 时, SQL Server 如何获得许可？

SQL Server 的 Site Recovery 复制在软件保障灾难恢复权益的范围内。 此覆盖率适用于所有 Site Recovery 方案: 本地到 Azure 的灾难恢复和跨区域的 Azure IaaS 灾难恢复。 有关详细信息, 请参阅[Azure Site Recovery 定价](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery 是否支持我的 SQL Server 版本？

Site Recovery 应用程序不可知。 Site Recovery 有助于保护在受支持的操作系统上部署的任何 SQL Server 版本。 有关详细信息, 请参阅用于恢复复制的计算机的[支持矩阵](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="next-steps"></a>后续步骤

* 详细了解[Site Recovery 的体系结构](site-recovery-components.md)。
* 有关 Azure 中的 SQL Server, 请在辅助 Azure 区域中详细了解用于恢复的[高可用性解决方案](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)。
* 有关 SQL 数据库的详细信息, 请参阅 Azure 辅助 Azure 区域中的恢复[业务连续性](../sql-database/sql-database-business-continuity.md)和[高可用性](../sql-database/sql-database-high-availability.md)选项。
* 有关本地 SQL Server 计算机的详细信息, 请参阅 Azure 虚拟机中的恢复的[高可用性选项](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions)。
