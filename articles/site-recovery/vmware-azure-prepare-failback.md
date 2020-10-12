---
title: 准备 VMware VM 以使用 Azure Site Recovery 进行重新保护和故障回复
description: 准备好在故障转移后使用 Azure Site Recovery 故障回复 VMware VM
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85847731"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>为 VMware VM 的重新保护和故障回复做好准备

将本地 VMware VM 或物理服务器[故障转移](site-recovery-failover.md)到 Azure 后，可以重新保护故障转移后创建的 Azure VM，以便将其复制回到本地站点。 启用从 Azure 到本地的复制后，可以在准备就绪时通过运行从 Azure 到本地的故障转移来进行故障回复。

继续之前，请先了解此视频，了解如何从 Azure 故障回复到本地站点。<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>重新保护/故障回复组件

在重新保护并从 Azure 故障回复之前，需要准备好多个组件和设置。

组件****| **详细信息**
--- | ---
**本地配置服务器** | 本地配置服务器必须正在运行且已连接到 Azure。<br/><br/> 要故障回复到的 VM 必须在配置服务器数据库中存在。 如果灾难影响了配置服务器，请使用相同的 IP 地址还原该服务器，以确保故障回复正常工作。<br/><br/>  如果故障转移时保留了复制的计算机的 IP 地址，则应在 Azure VM 计算机与配置服务器的故障回复 NIC 之间建立站点到站点连接（或 ExpressRoute 连接）。 对于保留的 IP 地址，配置服务器需要两个 NIC - 一个用于源计算机连接，另一个用于 Azure 故障回复连接。 这可以避免源 VM 与故障转移的 VM 的子网地址范围重叠。
**Azure 中的进程服务器** | 在故障回复到本地站点之前，需在 Azure 中部署一个进程服务器。<br/><br/> 进程服务器从受保护的 Azure VM 接收数据，并将其发送到本地站点。<br/><br/> 需在进程服务器与受保护 VM 之间使用低延迟网络，因此，我们建议在 Azure 中部署进程服务器以提高复制性能。<br/><br/> 如需概念证明，可将本地进程服务器与 ExpressRoute 一起用于专用对等互连。<br/><br/> 进程服务器应位于故障转移的 VM 所在的 Azure 网络中。 进程服务器还必须能够与本地配置服务器和主目标服务器通信。
**单独的主目标服务器** | 主目标服务器接收故障回复数据。默认情况下，Windows 主目标服务器将在本地配置服务器上运行。<br/><br/> 最多可将 60 个磁盘附加到一个主目标服务器。 如果要故障回复的 VM 包含的磁盘总共超过 60 个，或者你要故障回复大量的流量，请创建独立的主目标服务器用于故障回复。<br/><br/> 如果将计算机收集到复制组以实现多 VM 一致性，这些 VM 必须全部运行 Windows，或者全部运行 Linux。 为什么？ 因为复制组中的所有 VM 必须使用同一个主目标服务器，而主目标服务器必须采用与复制的计算机相同的操作系统（且版本相同或更高）。<br/><br/> 主目标服务器的磁盘中不应包含任何快照，否则重新保护和故障回复无法正常进行。<br/><br/> 主目标不能有半虚拟化 SCSI 控制器。 控制器只能是 LSI 逻辑控制器。 如果没有 LSI 逻辑控制器，重新保护会失败。
**故障回复复制策略** | 若要复制回到本地站点，需要创建故障回复策略。 此策略是在创建目标为 Azure 的复制策略时自动创建的。<br/><br/> 此策略自动与配置服务器关联。 它将 RPO 阈值设置为 15 分钟，将恢复点保留期设置为 24 小时，将应用一致性快照频率设置为 60 分钟。 无法编辑该策略。 
**站点到站点 VPN/ExpressRoute 专用对等互连** | 重新保护和故障回复需要通过站点到站点 VPN 连接或 ExpressRoute 专用对等互连来复制数据。 


## <a name="ports-for-reprotectionfailback"></a>用于重新保护/故障回复的端口

必须为重新保护/故障回复打开多个端口。 下图演示了端口和重新保护/故障回复流。

![用于故障转移和故障回复的端口](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>在 Azure 中部署进程服务器

1. 在 Azure 中[设置进程服务器](vmware-azure-set-up-process-server-azure.md)进行故障回复。
2. 确保 Azure VM 可以访问进程服务器。 
3. 确保站点到站点 VPN 连接或 ExpressRoute 专用对等网络提供足够的带宽，可将数据从进程服务器发送到本地站点。

## <a name="deploy-a-separate-master-target-server"></a>部署单独的主目标服务器

1. 请注意主目标服务器的[要求和限制](#reprotectionfailback-components)。
2. 创建 [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) 或 [Linux](vmware-azure-install-linux-master-target.md) 主目标服务器，以匹配要重新保护和故障回复的 VM 的操作系统。
3. 确保不要为主目标服务器使用存储 vMotion，否则故障回复可能会失败。 VM 无法启动，因为磁盘不可供其使用。
    - 若要防止出现此情况，请从 vMotion 列表中排除主目标服务器。
    - 如果主目标在重新保护后执行存储 vMotion 任务，则附加到主目标服务器的受保护 VM 磁盘将迁移到 vMotion 任务的目标。 如果尝试在此之后进行故障回复，则磁盘分离会因为找不到磁盘而失败。 此后，会难以在存储帐户中找到磁盘。 如果发生这种情况，请手动查找磁盘并将其附加到 VM。 然后，即可启动本地 VM。

4. 向现有的 Windows 主目标服务器添加一个保留驱动器。 添加新磁盘并格式化驱动器。 保留驱动器用于停止 VM 复制回本地站点的时间点。 请注意以下条件。 如果不满足这些条件，则无法为主目标服务器列出驱动器：
    - 卷未用于任何其他目的（例如用作复制目标），并且不处于锁定模式。
    - 卷不是缓存卷。 用于进程服务器和主目标的自定义安装卷不能用作保留卷。 当进程服务器和主目标安装在某个卷上时，该卷是主目标的缓存卷。
    - 卷的文件系统类型不是 FAT 或 FAT32。
    - 卷容量为非零值。
    - Windows 的默认保留卷是 R 卷。
    - Linux 的默认保留卷是 /mnt/retention。

5. 如果使用现有的进程服务器，请添加驱动器。 新驱动器必须满足上一步骤中所述的要求。 如果保留驱动器不存在，则它不会显示在门户上的选择下拉列表中。 将驱动器添加到本地主目标后，该驱动器最多将需要 15 分钟才会显示在门户上的选择项中。 如果 15 分钟后未显示该驱动器，可以刷新配置服务器。
6. 在主目标服务器上安装 VMware 工具或 open-vm-tools。 没有这些工具，将无法检测主目标的 ESXi 主机上的数据存储。
7. 在 VMware 的主目标 VM 的配置参数中设置 disk.EnableUUID=true。 如果此行不存在，请添加此行。 若要为 VMDK 提供一致的 UUID，以便能够正确进行装载，则此设置是必需的。
8. 检查 vCenter Server 访问要求：
    - 如果要故障回复到的 VM 位于 VMware vCenter Server 管理的 ESXi 主机上，则主目标服务器需要能够访问本地 VM 虚拟机磁盘 (VMDK) 文件，这样才能将复制的数据写入虚拟机的磁盘。 确保在具有读写访问权限的主目标主机上装载本地 VM 数据存储。
    - 如果 VM 不是位于 VMware vCenter Server 管理的 ESXi 主机上，Site Recovery 将在重新保护期间创建新的 VM。 此 VM 的创建位置为创建主目标服务器 VM 所在的 ESXi 主机上。 谨慎选择 ESXi 主机，以在所需的主机上创建 VM。 此 VM 的硬盘必须位于可由运行主目标服务器的主机访问的数据存储中。
    - 另一选择（如果故障回复的本地 VM 已存在）是在进行故障回复之前将其删除。 故障回复之后会在与主目标 ESXi 主机相同的主机上创建新的 VM。 故障回复到备用位置时，会将数据恢复到与本地主目标服务器所用的相同数据存储和 ESXi 主机。
9. 对于故障回复到 VMware VM 的物理计算机，在重新保护此计算机之前，应对运行主目标服务器的主机完成发现。
10. 检查主目标 VM 所在的 ESXi 主机是否至少附加了一个虚拟机文件系统 (VMFS) 数据存储。 如果未附加任何 VMFS 数据存储，则重新保护设置中的数据存储输入为空，因此无法继续操作。


## <a name="next-steps"></a>后续步骤

[Reprotect](vmware-azure-reprotect.md)重新保护 VM。
