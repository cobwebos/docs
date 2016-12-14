---
title: "使用 Azure Site Recovery 进行 Hyper-V 复制 | Microsoft 文档"
description: "通过了解本文介绍的技术概念，可帮助你成功安装、配置和管理 Azure Site Recovery。"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: mkjain
editor: 
ms.assetid: 97916915-1379-47df-8369-12ddf022c4da
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: b49771ff1e29aeb6ec582c21061085504705991b


---
# <a name="hyper-v-replication-with-azure-site-recovery"></a>使用 Azure Site Recovery 进行 Hyper-V 复制
本文介绍的技术概念，可帮助你使用 Azure Site Recovery 成功地向 Azure 保护配置和管理 Hyper-V 站点或 System Center Virtual Machine Manager (VMM) 站点。

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>设置用于在本地和 Azure 之间复制的源环境
作为在本地和 Azure 之间的灾难恢复设置的一部分，请确保在 VMM 服务器上下载并安装 Azure Site Recovery 提供程序。 在每个 Hyper-V 主机上安装 Azure 恢复服务代理。

![用于在本地和 Azure 之间复制的 VMM 站点部署](media/site-recovery-understanding-site-to-azure-protection/image00.png)

在 Hyper-V 托管的基础结构中设置源环境与在 VMM 托管的基础结构中设置源环境类似。 唯一的区别是，提供程序和代理都安装在 Hyper-V 主机上。 在 VMM 环境中，提供程序安装在 VMM 服务器上，而代理安装在 Hyper-V 主机上（在复制到 Azure 的情况下）。

## <a name="workflows"></a>工作流
### <a name="enable-protection"></a>启用保护
从 Azure 门户或本地保护虚拟机后，将启动一个名为“**启用保护**”的 Site Recovery 作业。 可以在“**作业**”选项卡下对作业进行监视。

![作业列表中的“启用保护”作业](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

“**启用保护**”作业将在调用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 方法前检查先决条件。 此方法会使用在保护期间配置的输入创建到 Azure 的复制。

“**启用保护**”作业通过调用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法从本地开始初始复制。 此方法可以向 Azure 发送虚拟机的虚拟磁盘。

![有关“启用保护”作业的详细信息](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>完成虚拟机上的保护
当触发初始复制时，系统会拍摄一个 [Hyper-V VM 快照](https://technet.microsoft.com/library/dd560637.aspx)。 系统会逐个处理虚拟硬盘，直到所有硬盘都被上载到 Azure。 根据磁盘大小和带宽，该过程通常要花一段时间才能完成。 若要优化你的网络使用，请参阅[如何管理本地到 Azure 保护网络带宽使用](https://support.microsoft.com/kb/3056159)。

在初始复制完成时，**在虚拟机上完成保护**作业将配置网络和复制后的各项设置。 在进行初始复制时：

* 将跟踪对磁盘所做的所有更改。
* 将耗用额外磁盘存储用于快照和 Hyper-V 副本日志 (HRL) 文件。

初始复制完成后，将删除 Hyper-V VM 快照。 此删除操作会将初始复制后的数据更改合并到父磁盘。

![作业列表中的“完成虚拟机上的保护”作业](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>增量复制
Hyper-V 副本复制跟踪器是 Hyper-V 副本复制引擎的一部分，以 Hyper-V 副本日志 (*.hrl) 文件的形式记录对虚拟磁盘做出的变更。 HRL 文件位于关联磁盘的相同目录中。

为复制配置的每个磁盘都有一个关联的 HRL 文件。 在初始复制完成后，此日志会被发送到客户的存储帐户中。 当日志正处于传输到 Azure 的过程中时，主站点中的变更将被记录到同一目录的另一日志文件中。

在初始复制或增量复制期间，可以在 VM 视图中监视 VM 复制的运行状况，如[监视虚拟机的复制运行状况](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)中所述。  

### <a name="resynchronization"></a>重新同步
当增量复制失败且完整初始复制占用大量网络带宽或时间时，虚拟机会标记为重新同步。 例如，当 HRL 文件的大小累积到总磁盘大小的 50% 时，虚拟机会标记为重新同步。 重新同步通过计算源虚拟机磁盘和目标虚拟机磁盘的校验和并只发送差异来最大程度地减小通过网络发送的数据量。

重新同步完成后，应会恢复正常增量复制。 如果发生网络中断或其他中断，可以继续重新同步。

默认情况下，自动计划的重新同步将配置为工作时间以外的时间。 如果虚拟机需要手动重新同步，可从门户选择虚拟机，然后单击“**重新同步**”。

![手动重新同步](media/site-recovery-understanding-site-to-azure-protection/image04.png)

重新同步使用固定块区块算法，其中源文件和目标文件被分到固定区块。 系统会针对每个区块生成校验和，然后进行比较，以确定源中的哪些区块需要应用到目标。

### <a name="retry-logic"></a>重试逻辑
有针对复制错误的内置重试逻辑。 此逻辑可以分为以下两类：

| 类别 | 方案 |
| --- | --- |
| 不可恢复的错误 |不尝试执行任何重试操作。 虚拟机复制状态为“**严重**”，并且需要管理员干预。 示例包括： <ul><li>VHD 链断开</li><li>副本虚拟机状态无效</li><li>网络身份验证错误</li><li>授权错误</li><li>在独立 Hyper-V 服务器的情况下未找到虚拟机</li></ul> |
| 可恢复的错误 |使用从第一次尝试开始增大重试间隔时间（1、2、4、8、10 分钟）的指数退避算法，在到达复制间隔时间后重试。 如果错误仍然存在，则每隔 30 分钟重试一次。 示例包括： <ul><li>网络错误</li><li>磁盘空间不足</li><li>出现内存不足</li></ul> |

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Hyper-V 虚拟机保护和恢复生命周期
![Hyper-V 虚拟机保护和恢复生命周期](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>其他参考资料
* [监视对 VMware、VMM、Hyper-V 和物理站点的保护以及对其进行故障排除](site-recovery-monitoring-and-troubleshooting.md)
* [联系 Microsoft 支持](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
* [常见 Azure Site Recovery 错误及解决方法](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)



<!--HONumber=Nov16_HO3-->


