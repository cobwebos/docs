---
title: "如何从 Azure 重新保护到本地 | Microsoft 文档"
description: "将 VM 故障转移到 Azure 之后，可以启动故障回复将 VM 恢复到本地。 了解在故障回复前如何执行重新保护的步骤。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 12/15/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: e650439b4bfd468c4bd7b236b80ce7361de1c6ac
ms.openlocfilehash: 972a3e88d15be656fd8cd3d51b7e507c7cbb49d5


---
# <a name="reprotect-from-azure-to-on-premises"></a>从 Azure 重新保护到本地

## <a name="overview"></a>概述
本文介绍如何将 Azure 虚拟机从 Azure 重新保护到本地站点。 根据[此教程](site-recovery-vmware-to-azure-classic.md)将 VMware 虚拟机或 Windows/Linux 物理服务器从本地站点故障转移到 Azure 以后，请按本文中的说明进行故障回复。

重新保护完成并且受保护的虚拟机正在复制后，可以在 VM 上启动故障回复将其恢复到本地。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="pre-requisites"></a>先决条件
下面是在准备进行重新保护时需要执行或考虑的几个先决条件步骤。

### <a name="reprotect-needs-a-s2s-vpn-or-an-express-route-to-replicate-data-back-to-on-premises"></a>重新保护需要使用 S2S VPN 或快速路由将数据复制回本地
其中，从本地到 Azure 的复制可以通过 Internet 或使用公共对等互连的快速路由进行，重新保护和故障回复需要设置 S2S VPN 以复制数据。 应提供网络，以便 Azure 中已故障转移的 VM 可以访问本地配置服务器。 你还可能要在已故障转移的 VM 所在的 Azure 网络中部署进程服务器 - 此进程服务器还应能够与本地配置服务器通信。

### <a name="process-server-is-needed-to-replicate-the-data-from-source-vms-to-on-premises"></a>需要进程服务器，以便将数据从源 VM 复制到本地
<!-- Read more about a process server here.!todo -->

要重新保护的 Azure VM 将复制数据发送到进程服务器。 应设置你的网络，以便可从 Azure VM 访问进程服务器。

可以在 Azure 中部署进程服务器，也可以使用故障转移期间使用的现有进程服务器。 要考虑的要点是将数据从 Azure VM 发送到进程服务器时的延迟。 

* 如果已设置快速路由，则可以使用本地 PS 发送数据。 这是因为 VM 和 PS 之间的延迟会很低。
    
    ![Expressroute 的体系结构关系图](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* 但是，如果你只有 S2S VPN，则我们建议在 Azure 中部署进程服务器。

    ![VPN 的体系结构关系图](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


请记住，复制将只能通过 S2S VPN 或快速路由网络的专用对等互连进行。 请确保通过该网络通道提供足够的带宽。

### <a name="ports-to-be-opened-on-different-machines-so-that-all-the-components-can-communicate"></a>要打开不同计算机上的端口，以便所有组件可以进行通信

![故障转移-故障回复所有端口](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="master-target-needs-to-be-available-on-premises-to-receive-data-from-process-server"></a>本地需要有主目标服务器，以便从进程服务器接收数据
本地需要主目标服务器，以便从进程服务器接收数据，然后将数据写入到本地 VM 的 VMDK。 如果要保护 Windows VM，需要 Windows 主目标服务器，并可以在此处重复使用本地 PS+MT <!-- !todo component -->。 对于 Linux VM，需要在本地设置其他 Linux 主目标服务器。

单击以下链接可阅读有关如何安装主目标服务器的步骤。

* [如何安装 Windows 主目标服务器](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [如何安装 Linux 主目标服务器](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>在完成主目标服务器的安装后要检查的常见事项

* 如果 VM 存在于本地的 vCenter 服务器上，主目标服务器将需要访问本地 VM 的 VMDK。 这是为了将复制的数据写入到 VM 的磁盘。 为此，需要确保**应使用读写访问权限将本地 VM 的数据存储装载在 MT 主机上**。

* 如果 VM 不存在于本地的 vCenter 服务器上，则需要在重新保护过程中创建新的 VM。 将在要在其上创建 MT 的 ESX 主机上创建此 VM。 因此，请仔细选择 ESX 主机，以便将故障回复 VM 创建在所需的主机上。
    
* **主目标服务器不能进行存储 vMotion**。 这会导致故障回复失败。 将不显示 VM，因为无法向其提供磁盘。

* 需要将一个新驱动器添加到现有的主目标服务器。 此驱动器称为保留驱动器。 添加新磁盘并格式化驱动器。 保留驱动器用于停止 VM 复制回本地的时间点。 保留驱动器的部分条件如下所示，如果不符合这些条件，将不会为主目标服务器列出该驱动器。
   
   a. 卷不应用于任何其他目的（复制的目标，等等）

   b.保留“数据库类型”设置，即设置为“共享”。 卷不应处于锁定模式。

   c. 卷不应为缓存卷。 （该卷上不应存在 MT 安装。 PS+MT 自定义安装卷不能用作保留卷。 此处安装的 PS+MT 卷是 MT 的缓存卷。）

   d.单击“下一步”。 卷文件系统类型不应为 FAT 和 FAT32。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 卷容量应为非零值。

   e. Windows 的默认保留卷是 R 卷。

   f. Linux 的默认保留卷是 /mnt/retention。

* Linux 故障转移 VM 需要 Linux 主目标服务器。 Windows 故障转移 VM 需要 Windows 主目标服务器。

* 在主目标服务器上安装 VMware 工具。 没有 VMware 工具，将无法检测到 MT 的 ESXi 主机上的数据存储。

* 通过 vCenter 属性在 MT VM 上启用 disk.EnableUUID = True 参数。 <!-- !todo Needs link. -->



### <a name="failback-policy"></a>故障回复策略
若要复制回本地，你需要一个故障回复策略。 在创建正向策略时，将自动创建此策略。 请注意：

1. 此策略在创建过程中会与配置服务器自动关联。
2. 此策略不可编辑。
3. 此策略的设置值为（RPO 阈值 = 15 分钟，恢复点保留期 = 24 小时，应用一致性快照频率 = 60 分钟）![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)



## <a name="steps-to-reprotect"></a>重新保护的步骤

1. 在“保管库”>“复制的项”中，选择已故障转移的 VM，然后右键单击“重新保护”。 也可以单击该计算机，然后从命令按钮中选择重新保护。
2. 在边栏选项卡中，可以看到已选中“Azure 到本地”的保护方向。
3. 在“主目标服务器”和“进程服务器”中，选择本地主目标服务器，以及进程服务器。
4. 选择要将本地磁盘恢复到的“数据存储”。 删除本地 VM 并且需要创建新磁盘时，可使用此选项。 如果磁盘已存在，但你仍然需要指定一个值，则忽略此选项。
5. 选择保留驱动器。 
6. 将自动选择故障回复策略。
7. 单击“确定”开始重新保护以后，作业就会开始将 VM 从 Azure 复制到本地站点。 可以在“作业”选项卡上跟踪进度。

如果要恢复到备用位置（删除了本地 VM 时），请选择针对主目标服务器配置的保留驱动器和数据存储。 故障回复到本地站点时，故障回复保护计划中的 VMware VM 将使用与主目标服务器相同的数据存储，并将在 vCenter 中创建新 VM。 如果要将 Azure VM 恢复到现有本地 VM，则应使用读/写访问权限将本地 VM 的数据存储装载在主目标服务器的 ESXi 主机上。
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

你也可以在恢复计划级别重新保护。 如果有复制组，只能使用恢复计划对其进行重新保护。 通过恢复计划重新保护时，需要为每台受保护的计算机提供上述值。

> [!NOTE]
> 应使用相同的主目标服务器重新保护复制组。 如果使用不同的主目标服务器重新保护它们，则无法为其获取一个共同的时间点。
> 
> 

重新保护成功后，VM 将进入受保护状态。

## <a name="next-steps"></a>后续步骤

VM 进入受保护状态后，可以启动故障回复。 故障回复将关闭 Azure 中的 VM，启动本地 VM。 因此，应用程序存在较短的停机时间。 因此，请将应用程序可以面对停机的时间选择为故障回复时间。

[启动 VM 故障回复的步骤](site-recovery-how-to-failback-v2a.md#steps-to-failback)

## <a name="common-issues"></a>常见问题 



<!--HONumber=Feb17_HO2-->


