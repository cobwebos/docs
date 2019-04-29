---
title: Azure Site Recovery 新增功能 | Microsoft Docs
description: 汇总 Azure Site Recovery 中引入的新功能
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: 5ee1328dddb6ae1e1c878384097b0e10aa32feeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776282"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 中的新增功能

[Azure Site Recovery](site-recovery-overview.md) 服务会持续进行更新和改进。 本文介绍最新版本、新功能和新内容，让你始终了解最新动态。 此页会定期更新。

如果有关于 Site Recovery 功能的建议，请[提供反馈](https://feedback.azure.com/forums/256299-site-recovery)。

## <a name="q1-2019"></a>2019 年第 1 季度 

### <a name="update-rollup-34-february-2019"></a>更新汇总 34 (2019 年 2 月)

[更新汇总 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**问题的修补程序** | 多个修复和改进 （如所述汇总）



### <a name="update-rollup-33-february-2019"></a>更新汇总 33 (2019 年 2 月)

[更新汇总 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**问题的修补程序** | 多个修复和改进 （如所述汇总）
**网络映射** | 对于 Azure VM 灾难恢复，你现在可以使用任何可用的目标网络时启用复制。 
**标准 SSD** | 你现在可以设置的 Azure Vm 使用灾难恢复[标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)。
**存储空间直通** | 您可以设置使用的 Azure VM 应用上运行的应用的灾难恢复[存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)以实现高可用性。
**BRTFS 文件系统** | VMware Vm，除了 Azure Vm 支持。<br/><br/> 如果不支持：启用复制后更改 BTRFS 文件系统子卷、 文件系统分散在多个磁盘，或如果 BTRFS 文件系统支持 RAID。



### <a name="update-rollup-32-january-2019"></a>更新汇总 32 (2019 年 1 月)

[更新汇总 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**问题的修补程序** | 多个修复和改进 （如所述汇总）
**适用于 Linux 的灾难恢复** | **Azure VM**：RedHat 工作站 7 月 6 日;支持针对 Ubuntu、 Debian 和 SUSE 新内核版本。<br/><br/> **VMware Vm/物理服务器**:Redhat Enterprise Linux 7.6;RedHat 工作站 7 月 6 日;Oracle Linux 6.10/7.6;根据有关 Ubuntu、 Debian 和 SUSE 的新内核版本。


### <a name="update-rollup-31-january-2019"></a>更新汇总 31 (2019 年 1 月)

[更新汇总 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**问题的修补程序** | 多个修复和改进 （如所述汇总）
**适用于 Linux 的灾难恢复** | **Azure VM**：Oracle Linux 6.8 和 6.9/7.0;UEK5 内核支持。<br/><br/> **VMware Vm/物理服务器**:Oracle Linux 6.8 和 6.9/7.0;UEK5 内核支持。
**BRTFS 文件系统** | Azure Vm 支持。
**LVM** | LVM 和 LVM2 卷添加的支持。<br/><br/> 支持 /boot 目录和 LVM 卷上的磁盘分区。
**Directories** | 添加对这些目录 seet 作为单独的分区或不在同一系统磁盘的文件系统的支持: / (root)、 /boot、 /usr、 /usr/local、 /var 和 /etc。
**Windows Server 2008** | 添加了对动态磁盘的支持。
**VMware VM 故障转移** | Storvsc 和 vsbus 在不启动驱动程序的 VMware Vm 的改进了故障转移时间。
**UEFI 支持** | Azure Vm 不支持引导类型 UEFI。 现在可以使用 Site Recovery Azure 到迁移的本地物理服务器使用 UEFI。 站点恢复通过在迁移之前将引导类型转换为 BIOS 迁移服务器。 站点恢复以前此转换 Vm 仅支持。 支持是可用于物理服务器运行 Windows Server 2012 或更高版本。
**可用性区域中的 azure Vm** | 可以启用部署在可用性区域中的 Azure Vm 的复制到另一个区域。 ou 可为 Azure VM 启用复制，并将故障转移的目标设置为单个 VM 实例，VM 在可用性集中或在可用性区域中的 VM。 此设置不影响复制。 [阅读](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。


## <a name="q4-2018"></a>2018 年第 4 季度

### <a name="update-rollup-30-october-2018"></a>更新汇总 30 (2018 年 10 月)

[更新汇总 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**问题的修补程序** | 多个修复和改进 （如所述汇总）
**区域支持** | Site Recovery 支持为澳大利亚中部 1 和澳大利亚中部 2 添加了。
**对磁盘加密的支持** | 添加了对 Azure 磁盘加密 (ADE) 加密与 Azure AD 应用的 Azure Vm 的灾难恢复的支持。 [了解详细信息](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**磁盘排除** | 未初始化的磁盘现在会在 Azure VM 复制期间自动排除。
**启用了防火墙的存储** | 添加了对支持[启用了防火墙的存储帐户](https://docs.microsoft.com/azure/storage/common/storage-network-security)。<br/><br/> 可以使用为另一个 Azure 区域的灾难恢复的启用防火墙的存储帐户上的非托管磁盘复制 Azure Vm。<br/><br/> 可以对非托管磁盘作为目标存储帐户中使用启用了防火墙的存储帐户。<br/><br/> 支持仅使用 PowerShell。


### <a name="update-rollup-29-october-2018"></a>更新汇总 29 (2018 年 10 月)

[更新汇总 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**问题的修补程序** | 多个修复和改进 （如所述汇总）

### <a name="automatic-updates-for-the-mobility-service-extension"></a>针对移动服务扩展进行的自动更新

Site Recovery 增加了一个选项，可以针对移动服务扩展进行自动更新。 移动服务扩展安装在每个通过 Site Recovery 复制的 Azure VM 上。 启用复制时，请选择是否允许 Site Recovery 管理对扩展的更新。 更新不需 VM 重启，且不影响复制。 [了解详细信息](azure-to-azure-autoupdate.md)。

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>使用加速网络针对 VM 进行的灾难恢复

使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，提升网络性能。 为 Azure VM 启用复制时，Site Recovery 会检测是否启用了加速网络。 如果启用了加速网络，Site Recovery 会在故障转移后自动在目标副本 Azure VM 上针对 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 配置加速网络。 [了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>针对 Azure VM 灾难恢复的价格计算器

进行 Azure VM 的灾难恢复需支付 VM 许可费用以及网络和存储费用。 Azure 提供[定价计算器](https://aka.ms/a2a-cost-estimator)，用于计算这些费用。 Site Recovery 现在提供[定价估算](https://aka.ms/a2a-cost-estimator)功能，可以根据三层应用的情况对示例部署定价。该应用使用 6 个 VM，其中包含 12 个标准 HDD 磁盘和 6 个高级 SSD 磁盘。 此示例假设标准磁盘的数据更改率为每天 10 GB，高级磁盘的数据更改率为每天 20 GB。 对于特定的部署，可以更改变量来估算费用。 可以指定 VM 的数目、托管磁盘的数目和类型，以及预期的跨 VM 总数据更改率。 另外，可以通过应用一个压缩因素来估算带宽费用。 [阅读](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。



## <a name="q3-2018"></a>2018 年第 3 季度 


### <a name="update-rollup-28-august-2018"></a>更新汇总 28 (2018 年 8 月)

[更新汇总 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**适用于 Linux 的灾难恢复** | **Azure VM**：添加受支持的 RedHat Enterprise Linux 6.10;CentOS 6.10。<br/><br/> **VMware Vm**:RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> 基于 Linux 的虚拟机现在支持使用在传统的 BIOS 兼容性模式中的 GUID 分区表 (GPT) 分区样式。 请参阅[有关 Azure IaaS VM 磁盘常见问题解答](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/faq-for-disks)有关详细信息。 
**云支持** | 支持在德国云中的 Azure Vm 灾难恢复。
**跨订阅灾难恢复** | 将一个区域中的 Azure Vm 复制到在不同订阅中，同一 Azure Active Directory 租户中的另一个区域的支持。 [了解详细信息](https://aka.ms/cross-sub-blog)。
**Windows Server 2008** | 对迁移的支持计算机正在运行 Windows Server 2008 R2/2008年 64 位和 32 位。<br/><br/> 仅适用于迁移 （复制和故障转移）。 不支持故障回复。

### <a name="update-rollup-27-july-2018"></a>更新汇总 27 (2018 年 7 月)

[更新汇总 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序 （如所述汇总）
**适用于 Linux 的灾难恢复** | **Azure VM**：Red Hat Enterprise Linux 7.5<br/><br/> **VMware Vm/物理服务器**:Red Hat Enterprise Linux 7.5，SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>后续步骤

在 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)页上时刻了解更新。




 









