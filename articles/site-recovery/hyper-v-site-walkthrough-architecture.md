---
title: "查看使用 Azure Site Recovery 执行 Hyper-V 到 Azure 复制（无 System Center VMM）的体系结构 | Microsoft Docs"
description: "本文概述通过 Azure Site Recovery 服务将本地 Hyper-V VM 复制到 Azure（不使用 VMM）所用的组件和体系结构。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: d57cbc5b205cfb020070d567097f3bb648ce5300
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-azure"></a>步骤 1：查看从 Hyper-V 复制到 Azure 的体系结构


本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Hyper-V 虚拟机（不由 System Center VMM 托管）复制到 Azure 时涉及的组件和进程。

请将任何评论发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。



## <a name="architectural-components"></a>体系结构组件

在没有 VMM 的情况下，将 Hyper-V VM 复制到 Azure 涉及多个组件。

**组件** | **位置** | **详细信息**
--- | --- | ---
**Azure** | 在 Azure 中，需要创建 Microsoft Azure 帐户、Azure 存储帐户和 Azure 网络。 | 复制的数据存储在存储帐户中；从本地站点故障转移时，将使用复制的数据创建 Azure VM。<br/><br/> 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**Hyper-V** | Hyper-V 主机和群集收集到 Hyper-V 站点中。 Azure Site Recovery 提供程序和恢复服务代理安装在每个 Hyper-V 主机上。 | 提供程序通过 Internet 使用 Site Recovery 来安排复制。 恢复服务代理处理数据复制。<br/><br/> 来自提供程序和代理的通信都是安全且经过加密的。 Azure 存储中的复制数据也已加密。
**Hyper-V VM** | 需要一个或多个在 Hyper-V 主机服务器上运行的 VM。 | 不需在 VM 上显式安装任何内容。

在[支持矩阵](site-recovery-support-matrix-to-azure.md)中了解每个组件的部署先决条件和要求。

图 1：Hyper-V 站点到 Azure 的复制

![组件](./media/hyper-v-site-walkthrough-architecture/arch-onprem-azure-hypervsite.png)


## <a name="replication-process"></a>复制过程

图 2：从 Hyper-V 到 Azure 的复制和恢复过程

![工作流](./media/hyper-v-site-walkthrough-architecture/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>启用保护

1. 为 Hyper-V VM 启用保护以后，就会在 Azure 门户中或本地启动“启用保护”。
2. 该作业会检查计算机是否符合先决条件，然后调用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)，以使用配置的设置来设置复制。
3. 该作业通过调用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法启动初始复制，以便初始化完整的 VM 复制，然后将 VM 的虚拟磁盘发送到 Azure。
4. 可以在“作业”选项卡中监视作业。
 
### <a name="replicate-the-initial-data"></a>复制初始数据

1. 当触发初始复制时，系统会拍摄一个 [Hyper-V VM 快照](https://technet.microsoft.com/library/dd560637.aspx)。
2. 虚拟硬盘是逐一复制的，直至全部复制到 Azure 为止。 可能需要一段时间才能完成，具体取决于 VM 大小和网络带宽。 要优化网络使用，请参阅[如何管理本地到 Azure 保护网络带宽使用](https://support.microsoft.com/kb/3056159)。
3. 如果在初始复制期间发生磁盘更改，Hyper-V 副本复制跟踪器将跟踪这些更改，并将其记录在 Hyper-V 复制日志 (.hrl) 中。 这些文件位于与磁盘相同的文件夹中。 每个磁盘都有一个关联的 .hrl 文件，该文件将发送到辅助存储。
4. 当初始复制正在进行时，快照和日志将占用磁盘资源。
5. 当初始复制完成时，将删除 VM 快照。 日志中的增量磁盘更改会进行同步，并合并到父磁盘中。


### <a name="finalize-protection"></a>完成保护

1. 初始复制完成后，“在虚拟机上完成保护”作业将配置网络和其他复制后设置，使虚拟机受到保护。
2. 如果要复制到 Azure，可能需要调整虚拟机的设置，使其随时可进行故障转移。 此时，可以运行测试故障转移以检查一切是否按预期工作。

### <a name="replicate-the-delta"></a>复制增量

1. 在完成初始复制后，根据复制设置开始增量同步。
2. Hyper-V 副本复制跟踪器跟踪对虚拟硬盘所做的更改，并将其另存为 .hrl 文件。 为复制配置的每个磁盘都有一个关联的 .hrl 文件。 在初始复制完成后，此日志会被发送到客户的存储帐户中。 当日志正处于传输到 Azure 的过程中时，主磁盘中的变更会被记录到同一目录的另一日志文件中。
3. 在初始复制和增量复制过程中，可以在“VM”视图中监视 VM。 [了解详细信息](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)。  

### <a name="synchronize-replication"></a>同步复制

1. 如果增量复制失败且完整复制因为带宽或时间限制而需要大量开销，则会将 VM 标记为需要重新同步。 例如，如果 .hrl 文件达到磁盘大小的 50%，系统会将 VM 标记为重新同步。
2.  重新同步通过计算源虚拟机磁盘和目标虚拟机的校验和并只发送增量数据来最大程度地减小发送的数据量。 重新同步使用固定块区块算法，其中源文件和目标文件被分到固定区块。 系统会针对每个区块生成校验和，并进行比较，以确定源中的哪些区块需要应用到目标。
3. 重新同步完成后，应会恢复正常增量复制。 默认情况下，重新同步安排为在非工作时间自动运行，但可以手动重新同步虚拟机。 例如，如果发生网络中断或其他中断，可以继续重新同步。 为此，请在“门户”>“重新同步”中选择 VM。

    ![手动重新同步](./media/hyper-v-site-walkthrough-architecture/image4.png)


### <a name="retry-logic"></a>重试逻辑

如果发生复制错误，会进行内置重试。 此逻辑可以分为以下两类：

**类别** | **详细信息**
--- | ---
**不可恢复的错误** | 不尝试执行任何重试操作。 VM 状态为“严重”，并且需要管理员干预。 这些错误的示例包括：VHD 链断裂；副本 VM 的状态无效；网络身份验证错误：授权错误；“找不到 VM”错误（适用于独立的 Hyper-V 服务器）
**可恢复的错误** | 使用从第一次尝试开始增大重试间隔时间（1、2、4、8、10 分钟）的指数退避算法，在到达复制间隔时间后重试。 如果错误仍然存在，则每隔 30 分钟重试一次。 示例包括：网络错误；磁盘空间不足错误；内存不足的情况 |



## <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 可以运行从本地 Hyper-V VM 到 Azure 的计划内或计划外[故障转移](site-recovery-failover.md)。 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来协调多台计算机的故障转移。
4. 运行故障转移后，应会在 Azure 中看到创建的副本 VM。 如果需要，可向 VM 分配公共 IP 地址。
5. 然后提交故障转移，开始从副本 Azure VM 访问工作负荷。
6. 当本地主站点再次可用时，便可以[故障回复](site-recovery-failback-from-azure-to-hyper-v.md)。 启动从 Azure 到主站点的计划内故障转移。 运行计划内故障转移时，可以选择故障回复到同一 VM 或备用位置，并同步 Azure 与本地之间的更改，确保不会丢失数据。 在本地创建 VM 时，请提交故障转移。




## <a name="next-steps"></a>后续步骤

转到[步骤 2：查看部署先决条件](hyper-v-site-walkthrough-prerequisites.md)
