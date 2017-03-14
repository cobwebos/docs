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
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 39e91dea775cf03f726db29f27f30142b69f9dfe
ms.lasthandoff: 02/22/2017


---
# <a name="reprotect-from-azure-to-on-premises"></a>从 Azure 到本地进行重新保护

## <a name="overview"></a>概述
本文介绍如何将 Azure 虚拟机从 Azure 重新保护到本地站点。 根据[此教程](site-recovery-vmware-to-azure-classic.md)将 VMware 虚拟机或 Windows/Linux 物理服务器从本地站点故障转移到 Azure 以后，请按本文中的说明进行故障回复。

重新保护完成并且受保护的虚拟机正在复制后，可以在 VM 上启动故障回复将其恢复到本地。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

有关快速视频概述，还可以浏览此处的视频。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

可以在此处阅读有关完整故障回复流程的信息。

## <a name="pre-requisites"></a>先决条件
下面是在准备进行重新保护时需要执行或考虑的几个先决条件步骤。

* 如果你要故障回复到的 VM 受 vCenter 服务器管理，则需确保你拥有在 vCenter 服务器上发现 VM 所必需的权限。 [了解详细信息](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
* 如果本地 VM 上存在快照，则重新保护会失败。 可以先删除快照，然后再继续重新保护。
* 故障回复之前，你需要创建两个其他组件：
  * **创建进程服务器**。 进程服务器用于从 Azure 中受保护的 VM 接收数据和发送本地数据。 这就要求它位于进程服务器和受保护的 VM 之间的低延迟网络上。 因此进程服务器可以在本地（如果你使用的是快速路由连接）或在 Azure 上（如果你使用的是 VPN）。
  * **创建主目标服务器**：主目标服务器接收故障回复数据。 在本地创建的管理服务器默认情况下已安装主目标服务器。 但是，你可能需要创建单独的故障回复用主目标服务器，具体取决于故障回复流量。 
        * [Linux VM 需要 Linux 主目标服务器](site-recovery-how-to-install-linux-master-target.md)。 
        * Windows VM 需要 Windows 主目标。 你可以重复使用本地 PS+MT 计算机。
* 执行故障回复时，配置服务器必须为本地服务器。 故障回复期间，虚拟机必须位于配置服务器数据库中，而失败的故障回复将不会成功。 因此，请确保定期计划服务器备份。 如果发生灾难，你需要使用相同的 IP 地址进行还原，让故障回复正常工作。
* 请确保在 VMware 的主目标 VM 的“配置参数”中设置了 disk.enableUUID=true 设置。 如果此行不存在，请添加此行。 若要为 VMDK 提供一致的 UUID，以便能够正确进行装载，则这是必需的。
* **主目标服务器不能进行存储 vMotion**。 这会导致故障回复失败。 将不显示 VM，因为无法向其提供磁盘。
* 需要将一个新驱动器添加到主目标服务器。 此驱动器称为保留驱动器。 添加新磁盘并格式化驱动器。
* 主目标具有[在重新保护前要在主目标上检查的公共事项](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-master-target)中列出的其他先决条件。


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-to-replicate-data-back-to-on-premises"></a>将数据复制回本地时为何需要 S2S VPN 或 ExpressRoute？
从本地到 Azure 的复制可通过 Internet 或具有公共对等设置的 ExpressRoute 完成，而重新保护和故障回复需要设置 S2S VPN 才能复制数据。 **应提供网络，以便 Azure 中已故障转移的 VM 可以访问 (ping) 本地配置服务器**。 你还可能要在已故障转移的 VM 所在的 Azure 网络中部署进程服务器 - 此进程服务器还应能够与本地配置服务器通信。

### <a name="when-should-i-install-a-process-server-in-azure"></a>何时应当在 Azure 中安装进程服务器？


要重新保护的 Azure VM 会将复制数据发送到进程服务器。 应设置你的网络，以便可从 Azure VM 访问进程服务器。

可以在 Azure 中部署进程服务器，也可以使用故障转移期间使用的现有进程服务器。 要考虑的要点是将数据从 Azure VM 发送到进程服务器时的延迟。 

* 如果已设置快速路由，则可以使用本地 PS 发送数据。 这是因为 VM 和 PS 之间的延迟会很低。
    
    ![ExpressRoute 的体系结构关系图](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* 但是，如果你只有 S2S VPN，则我们建议在 Azure 中部署进程服务器。

    ![VPN 的体系结构关系图](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


请记住，复制将只能通过 S2S VPN 或快速路由网络的专用对等互连进行。 请确保通过该网络通道提供足够的带宽。

可以[在此处](site-recovery-vmware-setup-azure-ps-resource-manager.md)阅读有关如何安装 Azure 进程服务器的详细信息。

### <a name="what-are-the-different-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>要使重新保护可以工作，需要在不同的组件上打开哪些不同的端口？

![故障转移-故障回复所有端口](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-to-use-for-reprotect"></a>哪个主目标服务器要用于重新保护？
本地需要主目标服务器，以便从进程服务器接收数据，然后将数据写入到本地 VM 的 VMDK。 如果要保护 Windows VM，需要 Windows 主目标服务器，并可以在此处重复使用本地 PS+MT <!-- !todo component -->。 对于 Linux VM，需要在本地设置其他 Linux 主目标服务器。


单击以下链接可阅读有关如何安装主目标服务器的步骤。

* [如何安装 Windows 主目标服务器](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [如何安装 Linux 主目标服务器](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>在完成主目标服务器的安装后要检查的常见事项

* 如果 VM 存在于本地的 vCenter 服务器上，主目标服务器将需要访问本地 VM 的 VMDK。 这是为了将复制的数据写入到 VM 的磁盘。 为此，需要确保**应使用读写访问权限将本地 VM 的数据存储装载在 MT 主机上**。

* 如果 VM 不存在于本地的 vCenter 服务器上，则需要在重新保护过程中创建新的 VM。 将在要在其上创建 MT 的 ESX 主机上创建此 VM。 因此，请仔细选择 ESX 主机，以便将故障回复 VM 创建在所需的主机上。
    
* **主目标服务器不能进行存储 vMotion**。 这会导致故障回复失败。 将不显示 VM，因为无法向其提供磁盘。

* 需要将一个新驱动器添加到现有的 Windows 主目标服务器。 此驱动器称为保留驱动器。 添加新磁盘并格式化驱动器。 保留驱动器用于停止 VM 复制回本地的时间点。 保留驱动器的部分条件如下所示，如果不符合这些条件，将不会为主目标服务器列出该驱动器。
   
   * 卷不应用于任何其他目的（复制的目标，等等）

   * 卷不应处于锁定模式。

   * 卷不应为缓存卷。 （该卷上不应存在 MT 安装。 PS+MT 自定义安装卷不能用作保留卷。 此处安装的 PS+MT 卷是 MT 的缓存卷。）

   * 卷文件系统类型不应为 FAT 和 FAT32。

   * 卷容量应为非零值。

   * Windows 的默认保留卷是 R 卷。

   * Linux 的默认保留卷是 /mnt/retention。

* Linux 故障转移 VM 需要 Linux 主目标服务器。 Windows 故障转移 VM 需要 Windows 主目标服务器。

* 在主目标服务器上安装 VMware 工具。 没有 VMware 工具，将无法检测到 MT 的 ESXi 主机上的数据存储。

* 通过 vCenter 属性在 MT VM 上启用 disk.EnableUUID = True 参数。 <!-- !todo Needs link. -->

* 主目标应当附加了至少一个 VMFS 数据存储。 如果未附加任何数据存储，则重新保护页上的“数据存储”输入将为空，你将无法继续进行。

* 主目标服务器在磁盘上不能具有任何快照。 如果有快照，则重新保护/故障回复将会失败。

* MT 不能具有半虚拟化 SCSI 控制器。 它只能是 LSI 逻辑控制器。 如果没有 LSI 逻辑控制器，则重新保护将失败。

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>重新保护的步骤

在重新保护之前，请确保你已在 Azure 和本地 Windows 或 [Linux 主目标](site-recovery-how-to-install-linux-master-target.md)中安装了[进程服务器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。

> [!NOTE]
> 当 VM 在 Azure 中引导后，它会留出一些时间让代理重新注册回配置服务器（最多 15 分钟）。 在此期间，你会发现重新保护失败，并且错误消息指出没有安装代理。 请等待几分钟，然后重试重新保护。
 
 

1. 在“保管库”>“复制的项”中，选择已故障转移的 VM，然后右键单击“重新保护”。 也可以单击该计算机，然后从命令按钮中选择重新保护。
2. 在边栏选项卡中，可以看到已选中“Azure 到本地”的保护方向。
3. 在“主目标服务器”和“进程服务器”中，选择本地主目标服务器，以及进程服务器。
4. 选择要将本地磁盘恢复到的“数据存储”。 删除本地 VM 并且需要创建新磁盘时，可使用此选项。 如果磁盘已存在，则会忽略此选项，但你仍然需要指定一个值。
5. 选择保留驱动器。 
6. 将自动选择故障回复策略。
7. 单击“确定”开始重新保护以后，作业就会开始将 VM 从 Azure 复制到本地站点。 可以在“作业”选项卡上跟踪进度。

如果要恢复到备用位置（删除了本地 VM 时），请选择针对主目标服务器配置的保留驱动器和数据存储。 故障回复到本地站点时，故障回复保护计划中的 VMware VM 将使用与主目标服务器相同的数据存储，并将在 vCenter 中创建新 VM。 如果要将 Azure VM 恢复到现有本地 VM，则应使用读/写访问权限将本地 VM 的数据存储装载在主目标服务器的 ESXi 主机上。
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

你也可以在恢复计划级别重新保护。 如果有复制组，只能使用恢复计划对其进行重新保护。 通过恢复计划重新保护时，需要为每台受保护的计算机提供上述值。

> [!NOTE]
> 应使用相同的主目标服务器重新保护复制组。 如果使用不同的主目标服务器重新保护它们，则无法为其获取一个共同的时间点。
 

重新保护成功后，VM 将进入受保护状态。

## <a name="next-steps"></a>后续步骤

VM 进入受保护状态后，可以启动故障回复。 故障回复将关闭 Azure 中的 VM，启动本地 VM。 因此，应用程序存在较短的停机时间。 因此，请将应用程序可以面对停机的时间选择为故障回复时间。

[启动 VM 故障回复的步骤](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-failback)

## <a name="common-issues"></a>常见问题 

* 如果你的虚拟机是使用模板创建的，在进行保护之前，应当确保每个 VM 对于磁盘都具有唯一的 UUID。 如果本地 VM 的 UUID 与主目标的 UUID 冲突（因为两者都是基于同一模板创建的），则重新保护将失败。 你将需要部署不是基于同一模板创建的另一个主目标。
* 如果执行只读的用户 vCenter 发现并保护虚拟机，操作将成功且故障转移可正常工作。 进行重新保护期间，操作会失败，因为无法发现数据存储。 征兆是在重新保护期间不会列出数据存储。 若要解决此问题，可以使用具有适当权限的帐户更新 vCenter 凭据并重试该作业。 有关详细信息，请参阅[通过 Azure Site Recovery 将 VMware 虚拟机和物理服务器复制到 Azure](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
* 在故障回复 Linux VM 并在本地运行它时，会看到网络管理器程序包已从该计算机卸载。 发生此卸载的原因是 VM 在 Azure 中恢复时，网络管理器程序包遭到删除。
* 当 Linux VM 配置有静态 IP 地址且故障转移到 Azure 时，将通过 DHCP 获取 IP 地址。 当故障转移回复到本地时，该 VM 会继续使用 DHCP 获取 IP 地址。 如有需要，请手动登录到该计算机并将 IP 地址设置回静态地址。 Windows VM 可以重新获取其静态 IP。
* 如果使用 ESXi 5.5 免费版或 vSphere 6 虚拟机监控程序免费版，则故障转移会成功，但故障回复不会成功。 要启用故障回复，请升级为程序的评估许可证。
* 如果无法从进程服务器访问配置服务器，请通过 Telnet 检查到配置服务器的连接，以及端口 443 上到配置服务器的连接。 还可以尝试从进程服务器计算机固定配置服务器。 连接到配置服务器后，进程服务器也应会发出检测信号。
* 如果尝试故障回复到备用 vCenter，请确保已发现新 vCenter，同时已发现主目标服务器。 一种典型的症状是，“重新保护”对话框中显示数据存储不可访问/不可见。
* 作为物理本地计算机保护的 WS2008R2SP1 计算机无法从 Azure 故障回复到本地。
