---
title: "如何从 Azure 故障回复到 VMware | Microsoft Docs"
description: "将虚拟机故障转移到 Azure 之后，可以启动故障回复将虚拟机恢复到本地。 了解故障回复的步骤。"
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
ms.openlocfilehash: 6629666eaa913321db3855438bb66d349d5c52bf
ms.lasthandoff: 04/25/2017


---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>从 Azure 故障回复到本地站点
本文介绍如何将虚拟机从 Azure 虚拟机故障回复到本地站点。 根据[使用 Azure Site Recovery 将 VMware 虚拟机和物理服务器复制到 Azure](site-recovery-vmware-to-azure-classic.md) 教程将 VMware 虚拟机或 Windows/Linux 物理服务器从本地站点故障转移到 Azure 后，请遵循本文中的说明进行故障回复。

## <a name="overview-of-failback"></a>故障回复概述
下面是故障回复的工作原理。 故障转移到 Azure 以后，可通过以下几个阶段故障回复到本地站点：

1. [重新保护](site-recovery-how-to-reprotect.md)虚拟机，使其开始复制到本地站点中运行的 VMware 虚拟机。 在此过程中，还需要：
    1. 设置本地主目标：Windows 虚拟机的 Windows 主目标和 Linux 虚拟机的 [Linux 主目标](site-recovery-how-to-install-linux-master-target.md)。
    2. 设置[进程服务器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。
    3. 启动[重新保护](site-recovery-how-to-reprotect.md)。
5. 将 Azure 中的虚拟机复制到本地站点后，启动从 Azure 到本地站点的故障转移。

故障回复数据后，重新保护已故障回复到的本地虚拟机，使其开始复制到 Azure。

有关快速概述，请观看以下有关如何从 Azure 故障转移到本地站点的视频。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>故障回复到原始或备用位置

故障转移 VMware 虚拟机后，可以故障回复到同一个本地源虚拟机（如果仍然存在）。 在这种情况下，只会复制更改。 这种情况称为原始位置恢复。 如果本地虚拟机不存在，则此方案是一种备用位置恢复。

#### <a name="original-location-recovery"></a>原始位置恢复

如果故障回复到原始虚拟机，需要满足以下条件：
* 如果虚拟机由 vCenter 服务器管理，主目标的 ESX 主机应该可以访问虚拟机数据存储。
* 如果虚拟机位于 ESX 主机上，但不由 vCenter 管理，则虚拟机的硬盘必须位于主目标的主机可访问的数据存储中。
* 如果虚拟机位于 ESX 主机上但不使用 vCenter，则应完成针对主目标的 ESX 主机的发现操作，然后才能进行重新保护。 若要对物理服务器进行故障回复，此规则也同样适用。
* 可以故障回复到虚拟存储区网络 (vSAN) 或基于原始设备映射 (RDM) 的磁盘（如果磁盘已存在并且已连接到本地虚拟机）。

#### <a name="alternate-location-recovery"></a>备用位置恢复
如果在重新保护本地虚拟机之前该虚拟机不存在，则该方案称为备用位置恢复。 重新保护工作流将重新创建本地虚拟机。 这还会导致完整数据下载。

* 故障回复到备用位置时，会将虚拟机恢复到主目标服务器所部署到的同一 ESX 主机。 用于创建磁盘的数据存储将与重新保护虚拟机时选择的数据存储相同。
* 只能故障回复到虚拟机文件系统 (VMFS) 数据存储。 如果你有 vSAN 或 RDM，重新保护和故障回复将不起作用。
* 重新保护涉及一次较大的初始数据传输，然后会进行更改。 之所以要执行此过程，是因为本地没有虚拟机。 需要复制回完整的数据。 此重新保护所需的时间还比原始位置恢复长。
* 无法故障回复到基于 vSAN 或 RDM 的磁盘。 只能在 VMFS 数据存储中创建新的虚拟机磁盘 (VMDK)。

故障转移到 Azure 时，物理计算机只能故障回复为 VMware 虚拟机（也称为 P2A2V）。 此流属于备用位置恢复。

* 受保护且已故障转移到 Azure 的 Windows Server 2008 R2 SP1 服务器无法故障回复。
* 确保除了需要故障回复到的必要 ESX/ESXi 主机之外，还发现至少一台主目标服务器。

## <a name="have-you-completed-reprotection"></a>是否已完成重新保护？
在继续下一步之前，请完成重新保护步骤，以便虚拟机处于复制状态并且可以启动故障转移回本地站点的故障转移。 有关详细信息，请参阅[如何在本地重新保护 Azure 中的虚拟机](site-recovery-how-to-reprotect.md)。

## <a name="prerequisites"></a>先决条件

* 执行故障回复时，本地需有配置服务器。 故障回复期间，虚拟机必须位于配置服务器数据库中，否则故障回复不会成功。 因此，请确保定期计划服务器备份。 如果发生灾难，你需要使用相同的 IP 地址还原服务器，让故障回复正常工作。
* 在触发故障回复之前，主目标服务器不应当具有任何快照。

## <a name="steps-to-fail-back"></a>故障回复的步骤

> [!IMPORTANT]
启动故障回复之前，请确保已完成重新保护虚拟机。 虚拟机在其运行状况为“正常”时应处于受保护状态。 若要重新保护虚拟机，请参阅[如何重新保护](site-recovery-how-to-reprotect.md)。

1. 在“复制的项”页中选择该虚拟机，然后右键单击并选择“非计划的故障转移”。
2. 在“确认故障转移”中，验证故障转移方向（从 Azure 故障转移），然后选择要用于故障转移的恢复点（最新恢复点，或最新的应用一致性恢复点）。 应用一致性点将在最新的时间点之后，并将导致丢失部分数据。
3. 故障转移期间，Site Recovery 会关闭 Azure 中的虚拟机。 检查故障回复是否按预期完成后，可以检查 Azure 中的虚拟机是否已关闭。

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>可以将虚拟机故障回复到哪个恢复点？

在故障回复期间，可以使用两个选项来故障回复虚拟机/恢复计划。

如果你选择最新处理的时间点，则所有虚拟机都将故障转移到其最新的可用时间点。 如果恢复计划中有一个复制组，则复制组的每个虚拟机都将故障转移到其独立的最新时间点。

除非虚拟机至少有一个恢复点，否则无法对其进行故障回复。 除非恢复计划的所有虚拟机至少有一个恢复点，否则无法对恢复计划进行故障回复。

> [!NOTE]
> 最新恢复点是一个在崩溃时保持一致的恢复点。

如果选择应用程序一致恢复点，则单个虚拟机故障回复将恢复到其最新的可用应用程序一致恢复点。 如果恢复计划具有复制组，则每个复制组都将恢复到其公共的可用恢复点。
请注意，应用程序一致恢复点在时间上可能会落后，并且可能会有数据丢失。

### <a name="what-happens-to-vmware-tools-post-failback"></a>故障回复后，VMware 工具会发生什么情况？

在故障转移到 Azure 的过程中，VMware 工具无法在 Azure 虚拟机上运行。 对于 Windows 虚拟机，ASR 在故障转移过程中禁用 VMware 工具。 对于 Linux 虚拟机，ASR 在故障转移过程中卸载 VMware 工具。 

在 Windows 虚拟机故障回复期间，VMware 工具将在故障回复时重新启用。 同样，对于 linux 虚拟机，VMware 工具会在故障回复期间重新安装在计算机上。

## <a name="next-steps"></a>后续步骤

故障回复完成后，需要提交虚拟机，确保删除 Azure 中已恢复的虚拟机。

### <a name="commit"></a>提交
要从 Azure 删除故障转移的虚拟机，必须进行提交。
右键单击受保护的项，然后单击“提交”。 某个作业将删除 Azure 中已故障转移的虚拟机。

### <a name="reprotect-from-on-premises-to-azure"></a>从本地到 Azure 进行重新保护

完成提交后，虚拟机将回到本地站点，但不受保护。 若要再次开始复制到 Azure，请执行以下操作：

1. 在“保管库” > “设置” > “已复制的项”中，选择已故障回复的虚拟机，然后单击“重新保护”。
2. 提供要用于将数据发送回 Azure 的进程服务器值。
3. 单击“确定”开始重新保护作业。

> [!NOTE]
> 本地虚拟机启动后，会留出一段时间让代理重新注册到配置服务器（最多 15 分钟）。 在此期间，重新保护将会失败并返回一条错误消息，指出未安装代理。 请等待几分钟，然后重试重新保护。

重新保护作业完成后，虚拟机将复制回 Azure，然后你可以执行故障转移。

## <a name="common-issues"></a>常见问题
在执行故障回复之前，请确保 vCenter 处于连接状态。 否则，断开磁盘连接并将其附加回到虚拟机的操作将会失败。

