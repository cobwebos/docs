---
title: "使用 SQL Server 和 Azure Site Recovery 复制应用程序 | Microsoft Docs"
description: "本文介绍了如何使用针对 SQL Server 的 Azure Site Recovery 灾难功能来复制 SQL Server。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: e53f60979e01a0eabe118d3ae6457a61bd4b0ded
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>使用 SQL Server 灾难恢复和 Azure Site Recovery 来保护 SQL Server

本文介绍如何结合使用 SQL Server 业务连续性和灾难恢复 (BCDR) 技术与 [Azure Site Recovery](site-recovery-overview.md) 来保护应用程序的 SQL Server 后端。

在开始之前，请确保了解 SQL Server 灾难恢复功能，包括故障转移群集、Always On 可用性组、数据库镜像和日志传送。


## <a name="sql-server-deployments"></a>SQL Server 部署

许多工作负载使用 SQL Server 作为基础。可将 SQL Server 与 SharePoint、Dynamics 和 SAP 等应用集成来实现数据服务。  可通过多种方式部署 SQL Server：

* **独立 SQL Server**：SQL Server 和所有数据库都托管在一台计算机（物理或虚拟）上。 当虚拟化时，主机群集用于本地高可用性。 不会实现来宾级别的高可用性。
* **SQL Server 故障转移群集实例 (Always On FCI)**：在一个 Windows 故障转移群集中配置两个或更多个运行带共享磁盘的 SQL Server 实例的节点。 如果某个节点关闭，群集可将 SQL Server 故障转移到其他实例。 此设置通常用于在主站点上实现高可用性。 此部署不能防止共享存储层中出现故障或中断。 共享磁盘可以使用 iSCSI、光纤通道或共享 vhdx 来实现。
* **SQL Always On 可用性组**：使用同步复制与自动故障转移在可用性组中配置 SQL Server 数据库时，在不共享任何内容的群集中设置两个或更多个节点。

 本文利用以下本机 SQL 灾难恢复技术将数据库恢复到远程站点：

* SQL Always On 可用性组，针对 SQL Server 2012 或 2014 Enterprise Edition 提供灾难恢复。
* SQL Server Standard Edition（任何版本）或 SQL Server 2008 R2 高安全性模式下的 SQL 数据库镜像。

## <a name="site-recovery-support"></a>Site Recovery 支持

### <a name="supported-scenarios"></a>支持的方案
Site Recovery 可以保护下表中汇总的 SQL Server。

**方案** | **到辅助站点** | **到 Azure**
--- | --- | ---
**Hyper-V** | 是 | 是
**VMware** | 是 | 是
**物理服务器** | 是 | 是

### <a name="supported-sql-server-versions"></a>支持的 SQL Server 版本
支持的方案支持以下 SQL Server 版本：

* SQL Server 2016 Enterprise 和 Standard
* SQL Server 2014 Enterprise 和 Standard
* SQL Server 2012 Enterprise 和 Standard
* SQL Server 2008 R2 Enterprise 和 Standard

### <a name="supported-sql-server-integration"></a>支持的 SQL Server 集成

Site Recovery 可与表中汇总的本机 SQL Server BCDR 技术集成，以提供灾难恢复解决方案。

**功能** | **详细信息** | **SQL Server** |
--- | --- | ---
**Always On 可用性组** | SQL Server 的多个独立实例，每个实例在包含多个节点的故障转移群集中运行。<br/><br/>数据库可以分组到可在 SQL Server 实例上复制（镜像）的故障转移组，因此不需要任何共享存储。<br/><br/>在主站点与一个或多个辅助站点之间提供灾难恢复。 使用同步复制与自动故障转移在可用性组中配置 SQL Server 数据库时，可以在不共享任何内容的群集中设置两个节点。 | SQL Server 2014 和 2012 Enterprise 版本
**故障转移群集 (Always On FCI)** | SQL Server 利用 Windows 故障转移群集实现本地 SQL Server 工作负载的高可用性。<br/><br/>使用共享磁盘运行 SQL Server 实例的节点是在故障转移群集中配置的。 如果实例关闭，群集将故障转移到另一个节点。<br/><br/>群集无法防止共享存储的故障或中断。 共享磁盘可以使用 iSCSI、光纤通道或共享 VHDX 来实现。 | SQL Server Enterprise 版本<br/><br/>SQL Server Standard 版本（仅限两个节点）
**数据库镜像（高安全性模式）** | 在单个辅助副本中保护单个数据库。 提供高安全性（同步）和高性能（异步）复制模式。 不需要故障转移群集。 | SQL Server 2008 R2<br/><br/>SQL Server Enterprise 的所有版本
**独立 SQL Server** | SQL Server 和数据库托管在单个服务器（物理或虚拟）上。 如果是虚拟服务器，则主机群集用于高可用性。 没有来宾级别的高可用性。 | Enterprise 或 Standard 版本

## <a name="deployment-recommendations"></a>部署建议

下表汇总了有关将 SQL Server BCDR 技术与 Site Recovery 集成的建议。

| **版本** | **版本** | **部署** | **本地到本地** | **本地到 Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 或 2012 |Enterprise |故障转移群集实例 |Always On 可用性组 |Always On 可用性组 |
|| Enterprise |用于实现高可用性的 Always On 可用性组 |Always On 可用性组 |Always On 可用性组 | |
|| 标准 |故障转移群集实例 (FCI) |使用本地镜像进行 Site Recovery 复制 |使用本地镜像进行 Site Recovery 复制 | |
|| Enterprise 或 Standard |独立 |站点恢复复制 |站点恢复复制 | |
| SQL Server 2008 R2 或 2008 |Enterprise 或 Standard |故障转移群集实例 (FCI) |使用本地镜像进行 Site Recovery 复制 |使用本地镜像进行 Site Recovery 复制 |
|| Enterprise 或 Standard |独立 |站点恢复复制 |站点恢复复制 | |
| SQL Server（任何版本） |Enterprise 或 Standard |故障转移群集实例 - DTC 应用程序 |站点恢复复制 |不支持 |

## <a name="deployment-prerequisites"></a>部署先决条件

* 运行受支持 SQL Server 版本的本地 SQL Server 部署。 通常还需要为 SQL Server 安装 Active Directory。
* 要部署的方案所要满足的要求。 详细了解有关[复制到 Azure](site-recovery-support-matrix-to-azure.md) 和[本地](site-recovery-support-matrix.md)的支持要求以及[部署先决条件](site-recovery-prereq.md)。
* 若要在 Azure 中设置恢复，请在 SQL Server 虚拟机上运行 [Azure 虚拟机准备情况评估](http://www.microsoft.com/download/details.aspx?id=40898)工具，确保虚拟机与 Azure 和 Site Recovery 兼容。

## <a name="set-up-active-directory"></a>设置 Active Directory

在辅助恢复站点上安装 Active Directory，使 SQL Server 能够正常运行。

* **小型企业** - 如果使用少量的应用程序和适用于本地站点的单个域控制器，并且想要故障转移整个站点，我们建议使用 Site Recovery 复制将域控制器复制到辅助数据中心或 Azure。
* **中大型企业** - 如果使用大量的应用程序和 Active Directory 林，并且想要按应用程序或工作负荷进行故障转移，我们建议在辅助数据中心或 Azure 中设置附加的域控制器。 如果使用 Always On 可用性组恢复到远程站点，我们建议在辅助站点或 Azure 上配置另一个域控制器，供已恢复 SQL Server 实例使用。

本文中的说明假设辅助位置提供了域控制器。 [详细了解](site-recovery-active-directory.md)如何使用 Site Recovery 保护 Active Directory。


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>与 SQL Server Always On 集成以便复制到 Azure

下面是需要执行的操作：

1. 将脚本导入到 Azure 自动化帐户中。 这包括用于在 [Resource Manager 虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)和[经典虚拟机](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中对 SQL 可用性组进行故障转移的脚本。

    [![“部署到 Azure”](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. 将 ASR-SQL-FailoverAG 添加为恢复计划的第一个组的准备操作。

1. 按照脚本中提供的说明创建一个自动化变量来提供可用性组的名称。

### <a name="steps-to-do-a-test-failover"></a>用于执行测试故障转移的步骤

SQL Always On 无法原生支持测试性故障转移。 因此，我们建议：

1. 在虚拟机上设置 [Azure 备份](../backup/backup-azure-vms.md)，该虚拟机在 Azure 中托管可用性组副本。

1. 触发对恢复计划进行测试性故障转移之前，请从上一步骤中进行的备份恢复虚拟机。

    ![从 Azure 备份进行还原 ](./media/site-recovery-sql/restore-from-backup.png)

1. 在从备份还原的虚拟机中[强制实施仲裁](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)。

1. 将侦听器的 IP 更新为测试故障转移网络中可用的某个 IP。

    ![更新侦听器 IP](./media/site-recovery-sql/update-listener-ip.png)

1. 使侦听器联机。

    ![使侦听器联机](./media/site-recovery-sql/bring-listener-online.png)

1. 使用在前端 IP 池下创建的与每个可用性组侦听器对应的一个 IP 与在后端池中添加的 SQL 虚拟机创建一个负载均衡器。

     ![创建负载均衡器 - 前端 IP 池 ](./media/site-recovery-sql/create-load-balancer1.png)

    ![创建负载均衡器 - 后端池 ](./media/site-recovery-sql/create-load-balancer2.png)

1. 对恢复计划进行测试性故障转移。

### <a name="steps-to-do-a-failover"></a>用于执行故障转移的步骤

在恢复计划中添加脚本并通过执行测试故障转移对恢复计划进行验证后，可以执行恢复计划的故障转移。


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>与 SQL Server Always On 集成以便复制到辅助本地站点

如果 SQL Server 使用可用性组（或 FCI）实现高可用性，我们建议也在恢复站点上使用可用性组。 请注意，这适用于不使用分布式事务的应用。

1. 在可用性组中[配置数据库](https://msdn.microsoft.com/library/hh213078.aspx)。
1. 在辅助站点上创建虚拟网络。
1. 在该虚拟网络与主站点之间配置站点到站点 VPN 连接。
1. 在恢复站点上创建虚拟机，并在其上安装 SQL Server。
1. 将现有的 Always On 可用性组扩展到新的 SQL Server VM。 将此 SQL Server 实例配置为异步副本。
1. 创建可用性组侦听器，或更新现有的侦听器，以包含异步副本虚拟机。
1. 确保应用程序场是使用侦听器设置的。 如果它是使用数据库服务器名称设置的，请将其更新为使用侦听器，以便不需要在故障转移后重新配置该场。

对于使用分布式事务的应用程序，我们建议使用 [VMware/物理服务器站点到站点复制](site-recovery-vmware-to-vmware.md)部署 Site Recovery。

### <a name="recovery-plan-considerations"></a>恢复计划注意事项
1. 将此示例脚本添加到主站点和辅助站点上的 VMM 库。

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. 为应用程序创建恢复计划时，请向 Group-1 脚本化步骤中添加一个准备操作，用以调用脚本来对可用性组进行故障转移。

## <a name="protect-a-standalone-sql-server"></a>保护独立 SQL Server

在此方案中，建议使用 Site Recovery 复制保护 SQL Server 计算机。 确切步骤取决于 SQL Server 是 VM 还是物理服务器，以及是要复制到 Azure 还是辅助本地站点。 了解 [Site Recovery 方案](site-recovery-overview.md)。

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>保护 SQL Server 群集 (Standard Edition/Windows Server 2008 R2)

对于运行 SQL Server Standard 版本或 SQL Server 2008 R2 的群集，建议使用 Site Recovery 复制来保护 SQL Server。

### <a name="on-premises-to-on-premises"></a>本地到本地

* 如果应用使用分布式事务，我们建议针对 Hyper-V 环境使用 [SAN 复制](site-recovery-vmm-san.md)，或者针对 VMware 环境使用 [VMware/物理服务器到 VMware 的复制](site-recovery-vmware-to-vmware.md)，来部署 Site Recovery。
* 对于非 DTC 应用程序，可以使用上述方法通过利用本地高安全性数据库镜像将群集恢复为独立服务器。

### <a name="on-premises-to-azure"></a>本地到 Azure

复制到 Azure 时，Site Recovery 未提供来宾群集。 SQL Server 也不会为 Standard 版本提供低成本灾难恢复解决方案。 在此方案中，建议在独立 SQL Server 中保护本地 SQL Server，并在 Azure 中恢复它。

1. 在本地站点中配置其他独立 SQL Server 实例。
1. 将此实例配置为想要保护的数据库的镜像。 在高安全模式下配置镜像。
1. 在本地站点上为 [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 或 [VMware VM/物理服务器](site-recovery-vmware-to-azure-classic.md)配置 Site Recovery。
1. 使用 Site Recovery 复制将新的 SQL Server 实例复制到 Azure。 由于该实例是高安全性镜像副本，因此会将它与主群集同步，但会使用 Site Recovery 复制将它复制到 Azure。


![标准群集](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>故障回复注意事项

对于 SQL Server Standard 群集，计划外故障转移后的故障回复需要从镜像实例 SQL Server 备份并还原到原始群集，并重新建立镜像。

## <a name="next-steps"></a>后续步骤
[详细了解](site-recovery-components.md) Site Recovery 体系结构。
