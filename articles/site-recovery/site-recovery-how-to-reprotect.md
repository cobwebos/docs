---
title: "如何在本地站点中重新保护 Azure 上的虚拟机 | Microsoft Docs"
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
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: a655c7bf1ea5ca1439d4353df5067c0e07f2d49f
ms.lasthandoff: 04/25/2017


---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>在本地站点中重新保护 Azure 上的虚拟机

## <a name="overview"></a>概述
本文介绍如何将 Azure 虚拟机从 Azure 重新保护到本地站点。 准备好根据[使用 Azure Site Recovery 将 VMware 虚拟机和物理服务器复制到 Azure](site-recovery-failover.md) 将 VMware 虚拟机或 Windows/Linux 物理服务器从本地站点故障转移到 Azure 后，请遵循本文中的说明进行故障回复。

重新保护完成并且受保护的虚拟机正在复制后，可以在虚拟机上启动故障回复将其恢复到本地站点。

请在本文末尾或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中发表任何评论或问题。

有关快速概述，请观看以下有关如何从 Azure 故障转移到本地站点的视频。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>先决条件
下面是在准备进行重新保护时需要执行或考虑的几个先决条件步骤。

* 若要故障回复到的虚拟机受 vCenter 服务器管理，需确保拥有在 vCenter 服务器上发现虚拟机所必需的权限。 [了解详细信息](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
* 如果本地虚拟机上存在快照，则重新保护会失败。 可以先删除快照，然后再继续重新保护。
* 故障回复之前，你需要创建两个其他组件：
  * **创建进程服务器**。 进程服务器从 Azure 中受保护的虚拟机接收数据，将数据发送到本地站点。 在进程服务器与受保护虚拟机之间需要配置低延迟网络。 因此，如果使用 Azure ExpressRoute 连接，可以部署本地进程服务器；如果使用 VPN，可以部署 Azure 进程服务器。
  * **创建主目标服务器**：主目标服务器接收故障回复数据。 创建的本地管理服务器默认情况下已安装主目标服务器。 但是，可能需要创建单独的故障回复用主目标服务器，具体取决于故障回复流量。
        * [Linux 虚拟机需要 Linux 主目标服务器](site-recovery-how-to-install-linux-master-target.md)。
        * Windows 虚拟机需要 Windows 主目标服务器。 可以重复使用本地进程服务器和主目标计算机。
* 执行故障回复时，本地需有配置服务器。 故障回复期间，虚拟机必须位于配置服务器数据库中。 否则，故障回复不会成功。 请确保定期计划服务器备份。 如果发生灾难，需要使用相同的 IP 地址还原服务器，让故障回复正常工作。
* 请确保在 VMware 的主目标虚拟机的配置参数中设置 disk.EnableUUID=true。 如果此行不存在，请添加此行。 若要为虚拟机磁盘 (VMDK) 提供一致的 UUID，以便能够正确进行装载，则必须指定此设置。
* *不能对主目标服务器使用存储 vMotion*。 这会导致故障回复失败。 虚拟机不会启动，因为无法向其提供磁盘。 若要防止出现此情况，请从 vMotion 列表中排除主目标服务器。
* 需要将一个新驱动器添加到主目标服务器。 此驱动器称为保留驱动器。 添加新磁盘并格式化驱动器。
* 主目标具有[在重新保护前要在主目标上检查的公共事项](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)中列出的其他先决条件。


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>将数据复制回本地站点时为何需要 S2S VPN 或 ExpressRoute 连接？
从本地到 Azure 的复制可通过 Internet 或具有公共对等设置的 ExpressRoute 连接完成，而重新保护和故障回复需要设置站点到站点 (S2S) VPN 才能复制数据。 应提供网络，以便 Azure 中已故障转移的虚拟机可以访问 (ping) 本地配置服务器。 还可能需要在已故障转移的虚拟机所在的 Azure 网络中部署进程服务器。 此进程服务器还应能够与本地配置服务器通信。

### <a name="when-should-i-install-a-process-server-in-azure"></a>何时应在 Azure 中安装进程服务器？


要重新保护的 Azure虚拟机会将复制数据发送到进程服务器。 应设置网络，以便可从 Azure虚拟机访问进程服务器。

可以在 Azure 中部署进程服务器，也可以使用故障转移期间使用的现有进程服务器。 要考虑的要点是将数据从虚拟机发送到进程服务器时的延迟。

如果已设置 ExpressRoute 连接，则可以使用本地进程服务器发送数据，因为虚拟机与进程服务器之间的延迟较低。

 ![ExpressRoute 的体系结构关系图](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



但是，如果你只有 S2S VPN，则我们建议在 Azure 中部署进程服务器。

 ![VPN 的体系结构关系图](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


请记住，复制将只能通过 S2S VPN 或 ExpressRoute 网络的专用对等互连进行。 请确保通过该网络通道提供足够的带宽。

详细了解如何安装 [Azure 进程服务器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。

> [!TIP]
> 我们始终建议在故障回复期间使用基于 Azure 的进程服务器。 如果进程服务器离复制虚拟机（Azure 中进行故障转移的计算机）较近，则复制性能较高。 但是，在验证概念或演示期间，可以将本地进程服务器与专用对等互连的 ExpressRoute 一起使用以更快地完成 POC。



### <a name="what-are-the-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>要使重新保护正常工作，需要在不同的组件上打开哪些端口？

![用于故障转移-故障回复的所有端口](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-should-be-used-for-reprotect"></a>应该将哪个主目标服务器用于重新保护？
需要本地主目标服务器，以便从进程服务器接收数据，然后将数据写入到本地虚拟机的 VMDK。 如果要保护 Windows 虚拟机，需要 Windows 主目标服务器。 可以重复使用本地进程服务器和主目标 <!-- !todo component -->。 对于 Linux虚拟机，需要设置附加的本地 Linux 主目标。


单击以下链接，了解如何安装主目标服务器：

* [如何安装 Windows 主目标服务器](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [如何安装 Linux 主目标服务器](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>在完成主目标服务器的安装后要检查的常见事项

* 如果虚拟机在 vCenter 服务器本地，主目标服务器需要访问本地虚拟机的 VMDK。 需要分配向虚拟机磁盘写入复制数据的访问权限。 确保在具有读写访问权限的主目标主机上装载本地虚拟机的数据存储。

* 如果虚拟机不在 vCenter 服务器本地，则 Site Recovery 服务需要在重新保护过程中创建新的虚拟机。 将在要在其上创建主目标的 ESX 主机上创建此虚拟机。 请谨慎选择 ESX 主机，以便将故障回复虚拟机创建在所需的主机上。

* *不能对主目标服务器使用存储 vMotion*。 这会导致故障回复失败。 虚拟机不会启动，因为无法向其提供磁盘。

> [!WARNING]
> 如果主目标受到存储 vMotion 的后期重新保护，则附加到主目标的受保护虚拟机磁盘将迁移到 vMotion 的目标。 如果尝试在此之后进行故障回复，分离磁盘将失败，指出“未找到磁盘”。 此后，将很难查找存储帐户中的磁盘。 你将需要手动找到磁盘，并将它们附加到虚拟机。 在此之后，可以启动本地虚拟机。

* 需要将一个新驱动器添加到现有的 Windows 主目标服务器。 此驱动器称为保留驱动器。 添加新磁盘并格式化驱动器。 保留驱动器用于停止虚拟机复制回本地站点的时间点。 下面是保留驱动器的部分条件，如果不符合这些条件，将不会为主目标服务器列出该驱动器。

   * 卷不应用于任何其他目的，例如复制目标等等。

   * 卷不应处于锁定模式。

   * 卷不应为缓存卷。 该卷上不应存在主目标安装。 进程服务器和主目标自定义安装卷不能用作保留卷。 当进程服务器和主目标安装在某个卷上时，该卷是主目标的缓存卷。

   * 卷的文件系统类型不应为 FAT 或 FAT32。

   * 卷容量应为非零值。

   * Windows 的默认保留卷是 R 卷。

   * Linux 的默认保留卷是 /mnt/retention。
   
   > [!IMPORTANT]
   > 如果使用的是 CS+PS 计算机或规模计算机或 PS+MT 计算机，则需要添加一个新驱动器。 新驱动器应满足上述要求。 如果保留驱动器不存在，则将不会在门户上的选择下拉列表中列出任何内容。 将驱动器添加到本地主目标后，驱动器将需要最多十五分钟才能在门户上的选择项中反映出来。 如果 15 分钟后未显示该驱动器，你还可以刷新配置服务器。



* Linux 故障转移虚拟机需要 Linux 主目标服务器。 Windows 故障转移虚拟机需要 Windows 主目标服务器。

* 在主目标服务器上安装 VMware 工具。 没有 VMware 工具，将无法检测到主目标的 ESXi 主机上的数据存储。

* 使用 vCenter 属性在主目标虚拟机上启用 disk.EnableUUID = True 参数。 <!-- !todo Needs link. -->

* 主目标上应至少附加了一个虚拟机文件系统 (VMFS) 数据存储。 如果未附加任何数据存储，则重新保护页上的“数据存储”输入将为空，你将无法继续进行。

* 主目标服务器在磁盘上不能具有任何快照。 如果有快照，则重新保护和故障回复将会失败。

* 主目标不能具有半虚拟化 SCSI 控制器。 控制器只能是 LSI 逻辑控制器。 如果没有 LSI 逻辑控制器，重新保护将会失败。

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>重新保护的步骤

在重新保护之前，请确保已在 Azure 和本地 Windows 或 [Linux 主目标](site-recovery-how-to-install-linux-master-target.md)中安装[进程服务器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。

> [!NOTE]
> 在 Azure 中启动虚拟机后，会留出一段时间让代理重新注册到配置服务器（最多 15 分钟）。 在此期间，重新保护将会失败并返回一条错误消息，指出未安装代理。 请等待几分钟，然后重试重新保护。



1. 在“保管库” > “已复制的项”中，右键单击已故障转移的虚拟机，然后选择“重新保护”。 也可以单击该计算机，然后从命令按钮中选择“重新保护”。
2. 在边栏选项卡中，可以看到已选中“Azure 到本地”的保护方向。
3. 在“主目标服务器”和“进程服务器”中，选择本地主目标服务器和进程服务器。
4. 选择要将本地磁盘恢复到的“数据存储”。 删除本地虚拟机后，如果需要创建新磁盘，可使用此选项。 如果磁盘已存在，则会忽略此选项，但你仍然需要指定一个值。
5. 选择保留驱动器。
6. 将自动选择故障回复策略。
7. 单击“确定”开始重新保护后，作业会开始将虚拟机从 Azure 复制到本地站点。 可以在“作业”选项卡上跟踪进度。

如果要恢复到备用位置（删除了本地虚拟机时），请选择针对主目标服务器配置的保留驱动器和数据存储。 故障回复到本地站点时，故障回复保护计划中的 VMware 虚拟机将使用与主目标服务器相同的数据存储。 将在 vCenter 中创建新虚拟机。
如果要将 Azure 中的虚拟机恢复到现有本地虚拟机，则应使用读/写访问权限将本地虚拟机的数据存储装载在主目标服务器的 ESXi 主机上。
    ![重新保护对话框](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

也可以在恢复计划级别重新保护。 只能使用恢复计划重新保护复制组。 使用恢复计划重新保护时，需要为每台受保护的计算机提供值。

> [!NOTE]
> 应使用相同的主目标服务器重新保护复制组。 如果使用不同的主目标服务器重新保护复制组，服务器无法提供共同的时间点。


重新保护成功后，虚拟机将进入受保护状态。

## <a name="next-steps"></a>后续步骤

虚拟机进入受保护状态后，可以启动故障回复。 故障回复将关闭 Azure 中的虚拟机，并启动本地虚拟机。 因此，应用程序存在较短的停机时间。 因此，请选择应用程序可以容许停机的时间作为故障回复时间。

[启动虚拟机故障回复的步骤](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back)

## <a name="common-issues"></a>常见问题

* 如果虚拟机是使用模板创建的，请确保每个虚拟机的磁盘使用唯一的 UUID。 如果本地虚拟机的 UUID 与主目标的 UUID 冲突（因为两者都是基于同一模板创建的），重新保护将会失败。 需要部署不是基于同一模板创建的另一个主目标。
* 如果执行只读的用户 vCenter 发现并保护虚拟机，保护将会成功且故障转移可正常工作。 进行重新保护期间，操作会失败，因为无法发现数据存储。 征兆是在重新保护期间不会列出数据存储。 若要解决此问题，可以使用具有适当权限的帐户更新 vCenter 凭据并重试该作业。 有关详细信息，请参阅[通过 Azure Site Recovery 将 VMware 虚拟机和物理服务器复制到 Azure](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
* 在故障回复 Linux 虚拟机并在本地运行它时，会看到网络管理器程序包已从该计算机卸载。 发生此卸载的原因是虚拟机在 Azure 中恢复时，网络管理器程序包遭到删除。
* 当 Linux 虚拟机配置有静态 IP 地址且故障转移到 Azure 时，将通过 DHCP 获取 IP 地址。 当故障转移回复到本地时，该虚拟机会继续使用 DHCP 获取 IP 地址。 如有需要，请手动登录到该计算机并将 IP 地址设置回静态地址。 Windows 虚拟机可以重新获取其静态 IP。
* 如果使用 ESXi 5.5 免费版或 vSphere 6 虚拟机监控程序免费版，则故障转移会成功，但故障回复不会成功。 要启用故障回复，请升级为程序的评估许可证。
* 如果无法从进程服务器访问配置服务器，请使用 Telnet 在端口 443 上检查与配置服务器的连接。 也可以尝试从进程服务器 ping 配置服务器。 连接到配置服务器后，进程服务器也应会发出检测信号。
* 如果尝试故障回复到备用 vCenter，请确保已发现新 vCenter，同时已发现主目标服务器。 一种典型的症状是，“重新保护”对话框中显示数据存储不可访问/不可见。
* 作为物理本地服务器保护的 Windows Server 2008 R2 SP1 服务器无法从 Azure 故障回复到本地站点。

