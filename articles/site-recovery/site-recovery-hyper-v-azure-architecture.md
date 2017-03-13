---
title: "如何在 Site Recovery 中完成从 Hyper-V 到 Azure 的复制？ | Microsoft 文档"
description: "本文概述了如何在 Azure Site Recovery 中进行 Hyper-V 复制"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.lasthandoff: 03/06/2017


---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>如何从 Hyper-V 复制到 Azure？

阅读本文，了解使用 [Azure Site Recovery](site-recovery-overview.md) 服务完成从 Hyper-V 到 Azure 的复制时涉及的体系结构和工作流。

请将任何评论发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

可以将以下内容复制到 Azure：
- **使用 VMM 的 Hyper-V**：位于本地 Hyper-V 主机中的 VM，该主机在 System Center Virtual MAchine Manager (VMM) 云中托管。 主机可以运行任何[支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。 可以复制运行任何[受 Hyper-V 和 Azure 支持的](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)来宾操作系统的 Hyper-V VM。
- **不使用 VMM 的 Hyper-V**：位于 Hyper-V 主机（不在 VMM 云中托管）上的本地 VM。 主机可以运行任何[支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)。 可以复制运行任何[受 Hyper-V 和 Azure 支持的](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)来宾操作系统的 Hyper-V VM。


## <a name="architectural-components"></a>体系结构组件

**区域** | **组件** | **详细信息**
--- | --- | ---
**Azure** | 在 Azure 中，需要创建 Microsoft Azure 帐户、Azure 存储帐户和 Azure 网络。 | 存储和网络帐户可以是基于 Resource Manager 的帐户或经典帐户。<br/><br/> 复制的数据存储在存储帐户中；从本地站点故障转移时，将使用复制的数据创建 Azure VM。<br/><br/> 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**VMM 服务器** | 位于 VMM 云中的 Hyper-V 主机 | 如果 Hyper-V 主机在 VMM 云中托管，则可在恢复服务保管库中注册 VMM 服务器。<br/><br/> 在 VMM 服务器上，可以安装 Site Recovery 提供程序来协调与 Azure 的复制。<br/><br/> 若要配置网络映射，需设置逻辑网络和 VM 网络。 VM 网络应链接到与云关联的逻辑网络。
**Hyper-V 主机** | Hyper-V 服务器在部署时，可以使用（也可以不使用）VMM 服务器。 | 如果没有 VMM 服务器，则会将 Site Recovery 提供程序安装在主机上，以便协调通过 Internet 进行的与 Site Recovery 的复制。 如果有 VMM 服务器，则会将提供程序安装在其上，而不是安装在主机上。<br/><br/> 恢复服务代理安装在主机上，用于处理数据复制。<br/><br/> 来自提供程序和代理的通信都是安全且经过加密的。 Azure 存储空间中的复制数据也已加密。
**Hyper-V VM** | Hyper-V 主机服务器上需要一个或多个 VM。 | 不需在 VM 上显式安装任何内容

## <a name="deployment-steps"></a>部署步骤

1. **Azure**：设置 Azure 组件。 建议在开始部署 Site Recovery 之前设置存储和网络帐户。
2. **保管库**：用户为 Site Recovery 创建恢复服务保管库，并配置保管库设置，包括：配置源设置和目标设置、设置复制策略，以及启用复制。
3. **源和目标**：
    - **VMM 云中的 Hyper-V 主机**：在指定源设置的过程中，用户需下载 Azure Site Recovery 提供程序并将其安装在 VMM 服务器上，以及下载 Azure 恢复服务代理并将其安装在每个 Hyper-V 主机上。 源将是 VMM 服务器。 目标是 Azure。
    - 无 VMM 的 Hyper-V 主机：当用户指定源设置时，会下载提供程序和代理并将其安装在每个 Hyper-V 主机上。 在部署过程中，用户会将主机集合到一个 Hyper-V 站点中，并将该站点指定为源。 目标是 Azure。

    ![从 Hyper-V/VMM 到 Azure 的复制](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![从 Hyper-V 站点到 Azure 的复制](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **复制策略**：为 Hyper-V 站点或 VMM 云创建复制策略。 策略将应用到站点或云中主机上的所有 VM。
5. **启用复制**：为 Hyper-V VM 启用复制。 初始复制根据复制策略设置进行。 数据更改将受到跟踪，完成初始复制后，开始将增量更改复制到 Azure。 项的受跟踪更改保存在 .hrl 文件中。
6. **测试性故障转移**：运行测试性故障转移，确保一切按预期运行。

详细了解部署：
- [从 Hyper-V VM 到 Azure 的复制入门 - 使用 VMM](site-recovery-vmm-to-azure.md)
- [从 Hyper-V VM 到 Azure 的复制入门 - 不使用 VMM](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Hyper-V 复制工作流

### <a name="enable-protection"></a>启用保护

1. 为 Hyper-V VM 启用保护以后，就会在 Azure 门户中或本地启动“启用保护”。
2. 该作业将检查计算机是否符合先决条件，然后调用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)，以使用配置的设置来设置复制。
3. 该作业通过调用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法启动初始复制，以便初始化完整的 VM 复制，然后将 VM 的虚拟磁盘发送到 Azure。
4. 可以在“作业”选项卡中监视作业。
        ![作业列表](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![启用保护向下钻取](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>初始复制

1. 当触发初始复制时，系统会拍摄一个 [Hyper-V VM 快照](https://technet.microsoft.com/library/dd560637.aspx)。
2. 虚拟硬盘是逐一复制的，直至全部复制到 Azure 为止。 可能需要一段时间才能完成，具体取决于 VM 大小和网络带宽。 若要优化你的网络使用，请参阅[如何管理本地到 Azure 保护网络带宽使用](https://support.microsoft.com/kb/3056159)。
3. 如果在初始复制期间发生磁盘更改，Hyper-V 副本复制跟踪器将跟踪这些更改，并将其记录在 Hyper-V 复制日志 (.hrl) 中。 这些文件位于与磁盘相同的文件夹中。 每个磁盘都有一个关联的 .hrl 文件，该文件将发送到辅助存储。
4. 当初始复制正在进行时，快照和日志将占用磁盘资源。
5. 当初始复制完成时，将删除 VM 快照。 日志中的增量磁盘更改会进行同步，并合并到父磁盘中。


### <a name="finalize-protection"></a>完成保护

1. 初始复制完成后，“在虚拟机上完成保护”作业将配置网络和其他复制后设置，使虚拟机受到保护。
    ![完成保护作业](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. 如果要复制到 Azure，可能需要调整虚拟机的设置，使其随时可进行故障转移。 此时，你可以运行测试故障转移以检查一切是否按预期工作。

### <a name="delta-replication"></a>增量复制

1. 在完成初始复制后，根据复制设置开始增量同步。
2. Hyper-V 副本复制跟踪器跟踪对虚拟硬盘所做的更改，并将其另存为 .hrl 文件。 为复制配置的每个磁盘都有一个关联的 .hrl 文件。 在初始复制完成后，此日志会被发送到客户的存储帐户中。 当日志正处于传输到 Azure 的过程中时，主磁盘中的变更将被记录到同一目录的另一日志文件中。
3. 在初始复制和增量复制过程中，可以在“VM”视图中监视 VM。 [了解详细信息](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)。  

### <a name="replication-synchronization"></a>复制同步

1. 如果增量复制失败且完整复制因为带宽或时间限制而需要大量开销，则会将 VM 标记为需要重新同步。 例如，如果 .hrl 文件达到磁盘大小的 50%，系统会将 VM 标记为重新同步。
2.  重新同步通过计算源虚拟机磁盘和目标虚拟机的校验和并只发送增量数据来最大程度地减小发送的数据量。 重新同步使用固定块区块算法，其中源文件和目标文件被分到固定区块。 系统会针对每个区块生成校验和，然后进行比较，以确定源中的哪些区块需要应用到目标。
3. 重新同步完成后，应会恢复正常增量复制。 默认情况下，重新同步安排为在非工作时间自动运行，但你可以手动重新同步虚拟机。 例如，如果发生网络中断或其他中断，可以继续重新同步。 为此，请在“门户”>“重新同步”中选择 VM。

    ![手动重新同步](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>重试

如果发生复制错误，将会进行内置重试。 此逻辑可以分为以下两类：

**类别** | **详细信息**
--- | ---
**不可恢复的错误** | 不尝试执行任何重试操作。 VM 状态为“严重”，并且需要管理员干预。 这些错误的示例包括：VHD 链断裂；副本 VM 的状态无效；网络身份验证错误：授权错误；“找不到 VM”错误（适用于独立的 Hyper-V 服务器）
**可恢复的错误** | 使用从第一次尝试开始增大重试间隔时间（1、2、4、8、10 分钟）的指数退避算法，在到达复制间隔时间后重试。 如果错误仍然存在，则每隔 30 分钟重试一次。 示例包括：网络错误；磁盘空间不足错误；内存不足的情况 |

## <a name="protection-and-recovery-lifecycle"></a>保护和恢复生命周期

![工作流](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>后续步骤

- [查看部署先决条件](site-recovery-prereq.md)
- 通过以下方式进行故障排除：
    - [监视和故障排除保护](site-recovery-monitoring-and-troubleshooting.md)
    - [Microsoft 支持部门提供的帮助](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [常见错误和解决方法](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

