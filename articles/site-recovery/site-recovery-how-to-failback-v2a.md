---
title: "如何从 Azure 故障回复到本地 | Microsoft 文档"
description: "将 VM 故障转移到 Azure 之后，可以启动故障回复将 VM 恢复到本地。 了解故障回复的步骤。"
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
ms.sourcegitcommit: 1a6dfb60f5db8b842af27b4c88bc3e5927331622
ms.openlocfilehash: 20446aadbb7bc3d13c5a38de5c4040f5898d4f6b


---
# <a name="failback-from-azure-to-on-premises"></a>从 Azure 故障回复到本地
本文介绍如何将 Azure 虚拟机从 Azure 故障回复到本地站点。 根据[此教程](site-recovery-vmware-to-azure-classic.md)将 VMware 虚拟机或 Windows/Linux 物理服务器从本地站点故障转移到 Azure 以后，请按本文中的说明进行故障回复。

## <a name="overview"></a>概述
下面是故障回复的工作原理：

* 故障转移到 Azure 以后，可通过以下几个阶段故障回复到本地站点：
  * **阶段 1**：重新保护 Azure VM，使之开始复制回到本地站点中运行的 VMware VM。 为此，还需要 
        1. 设置本地主目标服务器
        2. 设置进程服务器
        3. 然后，启动重新保护
  * **阶段 2**：将 Azure VM 复制到本地站点以后，通过运行故障转移从 Azure 进行故障回复。
  * **阶段 3**：对数据进行故障回复以后，对故障回复到的本地 VM 进行重新保护，使之开始复制到 Azure。

### <a name="failback-to-the-original-or-alternate-location"></a>故障回复到原始或备用位置

    
如果你对某个 VMware VM 进行了故障转移，则可故障回复到同一源 VM，前提是该 VM 仍存在于本地。 在这种情况下，仅复制回增量更改。 这种情况称为原始位置恢复。

* 如果故障回复到原始 VM，则需满足以下条件：
  * 如果 VM 由 vCenter 服务器管理，主目标的 ESX 主机应该可以访问 VM 数据存储。
  * 如果 VM 位于 ESX 主机上，但不由 vCenter 管理，则 VM 的硬盘必须位于 MT 的主机可访问的数据存储中。
  * 如果 VM 位于 ESX 主机上但不使用 vCenter，则应完成针对 MT 的 ESX 主机的发现操作，然后才能进行重新保护。 如果你要对物理服务器进行故障回复，此规则也同样适用。

  
如果在重新保护本地 VM 之前该 VM 不存在，则这称为备用位置恢复。 在此处重新保护工作流将重新创建本地 VM。

* 故障回复到备用位置时，会将 VM 恢复到主目标服务器所部署到的同一 ESX 主机。 用于创建磁盘的数据存储将与重新保护 VM 时选择的数据存储相同。
* 只能故障回复到 VMFS 数据存储。 如果你有 vSAN 或 RDM，重新保护和故障回复将不起作用。
* 重新保护涉及一次较大的初始数据传输，然后会进行增量更改。 这是因为 VM 不存在于本地，需要复制回完整的数据。 此重新保护所需的时间还比原始位置恢复长。

故障转移到 Azure 时，物理计算机只能故障回复为 VMware 虚拟机（也称为 P2A2V）。 此流属于备用位置恢复。

* 受保护且已故障转移到 Azure 的 Windows Server 2008 R2 SP1 计算机无法故障回复。
* 确保除了需要故障回复到的必要 ESX/ESXi 主机之外，还发现至少一台主目标服务器。


## <a name="pre-requisites"></a>先决条件

* 如果你要故障回复到的 VM 受 vCenter 服务器管理，则需确保你拥有在 vCenter 服务器上发现 VM 所必需的权限。 [了解详细信息](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
* 如果本地 VM 上存在快照，则重新保护会失败。 可以先删除快照，然后再继续重新保护。
* 故障回复之前，你需要创建两个其他组件：
  * **创建进程服务器**。 进程服务器用于从 Azure 中受保护的 VM 接收数据和发送本地数据。 这就要求它位于进程服务器和受保护的 VM 之间的低延迟网络上。 因此进程服务器可以在本地（如果你使用的是快速路由连接）或在 Azure 上（如果你使用的是 VPN）。
  * **创建主目标服务器**：主目标服务器接收故障回复数据。 在本地创建的管理服务器默认情况下已安装主目标服务器。 但是，你可能需要创建单独的故障回复用主目标服务器，具体取决于故障回复流量。 
        * Linux VM 需要 Linux 主目标服务器。 
        * Windows VM 需要 Windows 主目标。
* 执行故障回复时，配置服务器必须为本地服务器。 故障回复期间，虚拟机必须位于配置服务器数据库中，而失败的故障回复将不会成功。 因此，请确保定期计划服务器备份。 如果发生灾难，你需要使用相同的 IP 地址进行还原，让故障回复正常工作。
* 请确保在 VMware 的主目标 VM 的“配置参数”中设置了 disk.enableUUID=true 设置。 如果此行不存在，请添加此行。 若要为 VMDK 提供一致的 UUID，以便能够正确进行装载，则这是必需的。
* **主目标服务器不能进行存储 vMotion**。 这会导致故障回复失败。 将不显示 VM，因为无法向其提供磁盘。
* 需要将一个新驱动器添加到主目标服务器。 此驱动器称为保留驱动器。 添加新磁盘并格式化驱动器。


## <a name="steps-to-failback"></a>故障回复的步骤

启动故障回复之前，**请确保已完成重新保护虚拟机**。 虚拟机在其运行状况为“正常”时应处于受保护状态。 若要重新保护虚拟机，请详细了解[如何重新保护](site-recovery-how-to-reprotect.md)。


1. 在“复制的项”页中选择该虚拟机，然后右键单击并选择“非计划的故障转移”。
2. 在“确认故障转移”中，验证故障转移方向（从 Azure 故障转移），然后选择要用于故障转移的恢复点（最新恢复点，或最新的应用一致性恢复点）。 应用一致性点将在最新的时间点之后，并将导致丢失部分数据。
3. 故障转移期间，Site Recovery 会关闭 Azure VM。 检查故障回复是否按预期完成以后，即可检查 Azure VM 是否已关闭。

## <a name="next-steps"></a>后续步骤

故障回复完成后，你需要提交虚拟机，以确保删除在 Azure 中恢复的 VM。

1. 右键单击受保护的项，然后单击“提交”。 此时将触发一个作业，该作业将删除 Azure 中已故障转移的虚拟机。

完成提交以后，VM 会回到本地站点，但不受保护。 若要再次开始复制到 Azure，请执行以下操作：

1. 在“保管库”>“设置”>“复制的项”中，选择已故障回复的 VM，然后单击“重新保护”。
2. 提供要用于将数据发送回 Azure 的进程服务器值。
3. 单击“确定”开始重新保护作业。

重新保护作业完成后，VM 将复制回 Azure，这时你可以执行故障转移。

## <a name="common-issues"></a>常见问题





<!--HONumber=Feb17_HO2-->


