---
title: 为重新保护准备 VMware Vm，并 Azure Site Recovery
description: 在故障转移后，准备好将 VMware Vm 故障回复到 Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257181"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>准备 VMware Vm 的重新保护和故障回复

将本地 VMware Vm 或物理服务器[故障转移](site-recovery-failover.md)到 Azure 后，你可以重新保护在故障转移后创建的 azure vm，以便将其复制回本地站点。 通过从 Azure 到本地的复制，可以在准备就绪后，通过运行从 Azure 到本地的故障转移来故障回复。

继续之前，请先了解此视频，了解如何从 Azure 故障回复到本地站点。<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>重新保护/故障回复组件

你需要准备多个组件和设置，然后才能从 Azure 重新保护和故障回复。

组件| **세부 정보**
--- | ---
**本地配置服务器** | 本地配置服务器必须运行并连接到 Azure。<br/><br/> 要故障回复到的 VM 必须位于配置服务器数据库中。 如果灾难影响配置服务器，请使用相同的 IP 地址进行还原，以确保故障回复正常工作。<br/><br/>  如果在故障转移时保留复制的计算机的 IP 地址，则应在 Azure Vm 计算机与配置服务器的故障回复 NIC 之间建立站点到站点连接（或 ExpressRoute 连接）。 对于保留 IP 地址，配置服务器需要两个 Nic，一个用于源计算机连接，另一个用于 Azure 故障回复连接。 这避免了源和故障转移 Vm 的子网地址范围重叠。
**Azure 中的进程服务器** | 在故障回复到本地站点之前，你需要 Azure 中的进程服务器。<br/><br/> 进程服务器接收受保护的 Azure VM 的数据，并将其发送到本地站点。<br/><br/> 在进程服务器与受保护的 VM 之间需要低延迟网络，因此我们建议在 Azure 中部署进程服务器以提高复制性能。<br/><br/> 对于概念证明，你可以使用本地进程服务器和 ExpressRoute 与专用对等互连。<br/><br/> 进程服务器应该位于故障转移 VM 所在的 Azure 网络中。 进程服务器还必须能够与本地配置服务器和主目标服务器进行通信。
**单独的主目标服务器** | 主目标服务器接收故障回复数据，默认情况下，Windows 主目标服务器在本地配置服务器上运行。<br/><br/> 主目标服务器最多可以有60个附加到它的磁盘。 故障回复的 Vm 的总体总数为60，或者如果要故障回复大量流量，请创建单独的主目标服务器用于故障回复。<br/><br/> 如果将计算机收集到了多 VM 一致性的复制组中，则 Vm 必须全部为 Windows，或者必须全部为 Linux。 为什么？ 由于复制组中的所有 Vm 都必须使用相同的主目标服务器，因此，主目标服务器必须具有与复制的计算机相同的操作系统（具有相同或更高的版本）。<br/><br/> 主目标服务器的磁盘上不应有任何快照，否则，重新保护和故障回复将不起作用。<br/><br/> 主目标不能有半虚拟化 SCSI 控制器。 控制器只能是 LSI 逻辑控制器。 如果没有 LSI 逻辑控制器，重新保护会失败。
**故障回复复制策略** | 若要复制回到本地站点，需要故障回复策略。 此策略是在创建到 Azure 的复制策略时自动创建的。<br/><br/> 此策略自动与配置服务器关联。 它设置为 RPO 阈值15分钟，恢复点保留期为24小时，应用一致性快照频率为60分钟。 无法编辑此策略。 
**站点到站点 VPN/ExpressRoute 专用对等互连** | 重新保护和故障回复需要站点到站点 VPN 连接或 ExpressRoute 专用对等互连来复制数据。 


## <a name="ports-for-reprotectionfailback"></a>用于重新保护/故障回复的端口

必须打开多个端口才能重新保护/故障回复。 下图说明了端口和重新保护/故障回复流。

![用于故障转移和故障回复的端口](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>在 Azure 中部署进程服务器

1. 在 Azure 中[设置进程服务器](vmware-azure-set-up-process-server-azure.md)进行故障回复。
2. 确保 Azure Vm 可以访问进程服务器。 
3. 请确保站点到站点 VPN 连接或 ExpressRoute 专用对等网络具有足够的带宽，可将数据从进程服务器发送到本地站点。

## <a name="deploy-a-separate-master-target-server"></a>部署单独的主目标服务器

1. 请注意主目标服务器的[要求和限制](#reprotectionfailback-components)。
2. 创建[Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers)或[Linux](vmware-azure-install-linux-master-target.md)主目标服务器，以匹配要重新保护的 vm 的操作系统并对其进行故障回复。
3. 请确保不为主目标服务器使用存储 vMotion，否则故障回复可能会失败。 VM 计算机无法启动，因为磁盘不可供其使用。
    - 若要防止出现这种情况，请从 vMotion 列表中排除主目标服务器。
    - 如果主目标在重新保护后经历了存储 vMotion 任务，则附加到主目标服务器的受保护 VM 磁盘将迁移到 vMotion 任务的目标。 如果尝试在此之后进行故障回复，则磁盘分离会因为找不到磁盘而失败。 然后，很难在存储帐户中找到磁盘。 如果发生这种情况，请手动查找并将其附加到 VM。 之后，可以启动本地 VM。

4. 将保留驱动器添加到现有的 Windows 主目标服务器。 添加新磁盘并格式化驱动器。 保留驱动器用于停止 VM 复制回本地站点时的时间点。 请注意以下条件。 如果未满足这些要求，则不会为主目标服务器列出该驱动器：
    - 卷没有用于任何其他目的，例如复制目标，并且不处于锁定模式。
    - 卷不是缓存卷。 用于进程服务器和主目标的自定义安装卷不能用作保留卷。 当进程服务器和主目标安装在某个卷上时，该卷是主目标的缓存卷。
    - 卷的文件系统类型不是 FAT 或 FAT32。
    - 卷容量为非零值。
    - Windows 的默认保留卷是 R 卷。
    - Linux 的默认保留卷是 /mnt/retention。

5. 如果使用的是现有进程服务器，请添加驱动器。 新驱动器必须满足上一步中的要求。 如果保留驱动器不存在，则它不会显示在门户上的选择下拉列表中。 将驱动器添加到本地主目标后，该驱动器最多将需要 15 分钟才会显示在门户上的选择项中。 如果15分钟后未显示该驱动器，则可以刷新配置服务器。
6. 在主目标服务器上安装 VMware 工具或 open-vm-tools。 没有这些工具，将无法检测主目标的 ESXi 主机上的数据存储。
7. 设置磁盘。在 VMware 中的主目标 VM 的配置参数中设置 Disk.enableuuid = true。 如果此行不存在，请添加此行。 若要为 VMDK 提供一致的 UUID，以便能够正确进行装载，则此设置是必需的。
8. 检查 vCenter Server 访问要求：
    - 如果要故障回复到的 VM 位于 VMware vCenter 服务器托管的 ESXi 主机上，则主目标服务器需要访问本地 VM 虚拟机磁盘（VMDK）文件，以便将复制的数据写入虚拟机的磁盘。 确保本地 VM 数据存储在主目标主机上装载时具有读/写访问权限。
    - 如果 VM 不在 VMware vCenter 服务器管理的 ESXi 主机上，则 Site Recovery 会在重新保护期间创建新的 VM。 此 VM 是在创建主目标服务器 VM 的 ESXi 主机上创建的。 仔细选择 "ESXi 主机"，以在所需的主机上创建 VM。 此 VM 的硬盘必须位于可由运行主目标服务器的主机访问的数据存储中。
    - 另一选择（如果故障回复的本地 VM 已存在）是在进行故障回复之前将其删除。 然后，故障回复就会在与主目标 ESXi 主机相同的主机上创建新的 VM。 故障回复到备用位置时，会将数据恢复到与本地主目标服务器使用的数据存储和 ESXi 主机相同的数据存储和主机。
9. 对于故障回复到 VMware Vm 的物理计算机，你应该完成运行主目标服务器的主机的发现，然后才能重新保护计算机。
10. 检查主目标 VM 上是否至少附加了一个虚拟机文件系统（VMFS）数据存储的 ESXi 主机。 如果未附加 VMFS 数据存储，则 "重新保护" 设置中的 "数据存储" 输入将为空，并且你无法继续。


## <a name="next-steps"></a>后续步骤

[重新保护](vmware-azure-reprotect.md) VM。
