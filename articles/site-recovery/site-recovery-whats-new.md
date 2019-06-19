---
title: Azure Site Recovery 新增功能 | Microsoft Docs
description: 汇总 Azure Site Recovery 中引入的新功能
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 50e1cb95249f0108430e978ae3ffe23b6edc778d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418383"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 中的新增功能

[Azure Site Recovery](site-recovery-overview.md) 服务会持续进行更新和改进。 本文介绍最新版本、新功能和新内容，让你始终了解最新动态。 此页会定期更新。

如果有关于 Site Recovery 功能的建议，请[提供反馈](https://feedback.azure.com/forums/256299-site-recovery)。


## <a name="updates-march-2019"></a>更新（2019 年 3 月）

### <a name="update-rollup-35"></a>更新汇总 35

[更新汇总 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）

#### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**托管磁盘** | 现在可将本地 VMware VM 和物理服务器直接复制到 Azure 中的托管磁盘。 本地数据将发送到 Azure 中的缓存存储帐户，恢复点将在目标位置的托管磁盘中创建。 这可确保无需管理多个目标存储帐户。
**配置服务器** | Site Recovery 现在支持具有多个 NIC 的配置服务器。 必须先将附加的适配器添加到配置服务器 VM，然后在保管库中注册配置服务器。 如果在注册之后再添加，则需要在保管库中重新注册服务器。


## <a name="updates-february-2019"></a>更新（2019 年 2 月）

### <a name="update-rollup-34"></a>更新汇总 34 

[更新汇总 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。


### <a name="update-rollup-33"></a>更新汇总 33 

[更新汇总 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。


#### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复 
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**网络映射** | 对于 Azure VM 灾难恢复，现在可以在启用复制时使用任何可用的目标网络。 
**标准 SSD** | 现在可以使用[标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)对 Azure VM 设置灾难恢复。
**存储空间直通** | 可以使用[存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)对 Azure VM 应用中运行的应用设置灾难恢复，以实现高可用性。  将存储空间直通 (S2D) 与 Site Recovery 结合使用可为 Azure VM 工作负荷提供全面的保护。 使用 S2D 可在 Azure 中托管来宾群集。 当 VM 托管了关键应用程序（例如 SAP ASCS 层、SQL Server 或横向扩展文件服务器）时，此功能特别有用。


#### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux BRTFS 文件系统** | Site Recovery 现在支持复制采用使用 BRTFS 文件系统的 VMware VM。 如果存在以下情况，则不支持复制：<br/><br/>- 启用复制后 BTRFS 文件系统子卷发生更改。<br/><br/>- 文件系统分散在多个磁盘中。<br/><br/>- BTRFS 文件系统支持 RAID。
**Windows Server 2019** | 添加了对运行 Windows Server 2019 的计算机的支持。


## <a name="updates-january-2019"></a>更新（2019 年 1 月）

### <a name="accelerated-networking-azure-vms"></a>加速网络 (Azure VM)

使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，提升网络性能。 为 Azure VM 启用复制时，Site Recovery 会检测是否启用了加速网络。 如果启用了加速网络，Site Recovery 会在故障转移后自动在目标副本 Azure VM 上针对 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 配置加速网络。

[了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="update-rollup-32"></a>更新汇总 32 

[更新汇总 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

#### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 有关 Ubuntu、 Debian 和 SUSE RedHat 工作站 7 月 6 日，以及新的内核版本添加了支持。
**存储空间直通** | Site Recovery 支持使用存储空间直通 (S2D) 的 Azure VM。

#### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM/物理服务器复制 
**功能** | **详细信息**
--- | ---
**Linux 支持** | 有关 Ubuntu、 Debian 和 SUSE 的 Redhat Enterprise Linux 7.6、 RedHat 工作站 7 月 6 日，Oracle Linux 6.10/7.6 和新的内核版本添加了支持。


### <a name="update-rollup-31"></a>更新汇总 31 

[更新汇总 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

#### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM/物理服务器复制 
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Oracle Linux 6.8 和 6.9/7.0 以及 UEK5 内核的支持。
**LVM** | 添加了对 LVM 和 LVM2 卷的支持。<br/><br/> 现在支持磁盘分区和 LVM 卷上的 /boot 目录。
**Directories** | 添加了对这些设置为独立分区的目录，或不在同一系统磁盘中的文件系统的支持：<br/><br/> /(root)、/boot、/usr、/usr/local、/var、/etc。
**Windows Server 2008** | 添加了对动态磁盘的支持。
**故障转移** | 改进了 storvsc 和 vsbus 不是启动驱动程序的 VMware VM 的故障转移时间。
**UEFI 支持** | Azure VM 不支持 UEFI 启动类型。 现在可以通过 Site Recovery 将使用 UEFI 的本地物理服务器迁移到 Azure。 Site Recovery 在迁移服务器之前会将启动类型转换为 BIOS。 Site Recovery 以前仅支持对 VM 执行此转换。 该项支持适用于运行 Windows Server 2012 或更高版本的物理服务器。

#### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Oracle Linux 6.8 和 6.9/7.0 以及 UEK5 内核的支持。
**Linux BRTFS 文件系统** | 支持对 Azure VM 使用。
**可用性区域中的 azure Vm** | 可以启用部署在可用性区域中的 Azure Vm 的复制到另一个区域。 现在可以为 Azure VM 启用复制功能，并设置一个目标，以便故障转移到单个 VM 实例、可用性集中的 VM 或可用性区域中的 VM。 此设置不影响复制。 [阅读](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
**已启用防火墙的存储（门户/PowerShell）** | 添加了对[已启用防火墙的存储帐户](https://docs.microsoft.com/azure/storage/common/storage-network-security)的支持。<br/><br/> 可将已启用防火墙的存储帐户中使用非托管磁盘的 Azure VM 复制到另一个 Azure 区域，以实现灾难恢复。<br/><br/> 可将已启用防火墙的存储帐户用作非托管磁盘的目标存储帐户。<br/><br/> 支持在门户和 PowerShell 中使用。

## <a name="updates-december-2018"></a>更新（2018 年 12 月）

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>移动服务的自动更新 (Azure VM)

Site Recovery 增加了一个选项，可以针对移动服务扩展进行自动更新。 移动服务扩展安装在每个通过 Site Recovery 复制的 Azure VM 上。 启用复制时，请选择是否允许 Site Recovery 管理对扩展的更新。

更新不需 VM 重启，且不影响复制。 [了解详细信息](azure-to-azure-autoupdate.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>针对 Azure VM 灾难恢复的价格计算器

进行 Azure VM 的灾难恢复需支付 VM 许可费用以及网络和存储费用。 Azure 提供[定价计算器](https://aka.ms/a2a-cost-estimator)，用于计算这些费用。 Site Recovery 现在提供[定价估算](https://aka.ms/a2a-cost-estimator)功能，可以根据三层应用的情况对示例部署定价。该应用使用 6 个 VM，其中包含 12 个标准 HDD 磁盘和 6 个高级 SSD 磁盘。

- 此示例假设标准磁盘的数据更改率为每天 10 GB，高级磁盘的数据更改率为每天 20 GB。
- 对于特定的部署，可以更改变量来估算费用。
- 可以指定 VM 的数目、托管磁盘的数目和类型，以及预期的跨 VM 总数据更改率。
- 另外，可以通过应用一个压缩因素来估算带宽费用。

[阅读](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。


## <a name="updates-october-2018"></a>更新（2018 年 10 月）

### <a name="update-rollup-30"></a>更新汇总 30 

[更新汇总 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

#### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**区域支持** | Site Recovery 支持为澳大利亚中部 1 和澳大利亚中部 2 添加了。
**磁盘加密支持** | 添加了对在 Azure AD 应用中使用 Azure 磁盘加密 (ADE) 进行加密的 Azure VM 进行灾难恢复的支持。 [了解详细信息](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**磁盘排除** | 现在，在 Azure VM 复制期间会自动排除未初始化的磁盘。
**已启用防火墙的存储 (PowerShell)** | 添加了对[已启用防火墙的存储帐户](https://docs.microsoft.com/azure/storage/common/storage-network-security)的支持。<br/><br/> 可将已启用防火墙的存储帐户中使用非托管磁盘的 Azure VM 复制到另一个 Azure 区域，以实现灾难恢复。<br/><br/> 可将已启用防火墙的存储帐户用作非托管磁盘的目标存储帐户。<br/><br/> 仅支持在 PowerShell 中使用。


### <a name="update-rollup-29"></a>更新汇总 29 

[更新汇总 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。


## <a name="updates-august-2018"></a>更新（2018 年 8 月）

### <a name="update-rollup-28"></a>更新汇总 28 

[更新汇总 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

#### <a name="azure-vms-disaster-recovery"></a>Azure VM 灾难恢复 
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加受支持的 RedHat Enterprise Linux 6.10;CentOS 6.10。<br/><br/>
**云支持** | 添加了对德国云中 Azure VM 灾难恢复的支持。
**跨订阅灾难恢复** | 支持将一个区域中的 Azure VM 复制到同一 Azure Active Directory 租户中不同订阅内的另一个区域。 [了解详细信息](https://aka.ms/cross-sub-blog)。

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理服务器灾难恢复 
更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加对 RedHat Enterprise Linux 6.10，CentOS 6.10 支持。<br/><br/> 现在支持基于 Linux 且在旧式 BIOS 兼容模式下使用 GUID 分区表 (GPT) 分区样式的 VM。 有关详细信息，请查看 [Azure VM 常见问题解答](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks)。 
**迁移后的 VM 灾难恢复** | 支持将已迁移到 Azure 的本地 VMware VM 灾难恢复到次要区域，启用复制之前无需在 VM 上卸载移动服务。
**Windows Server 2008** | 支持迁移运行 Windows Server 2008 R2/2008 64 位和 32 位的计算机。<br/><br/> 仅限迁移（复制和故障转移）。 不支持故障回复。

## <a name="updates-july-2018"></a>更新（2018 年 7 月）

### <a name="update-rollup-27-july-2018"></a>更新汇总 27（2018 年 7 月）

[更新汇总 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

#### <a name="azure-vms-disaster-recovery"></a>Azure VM 灾难恢复 

更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Red Hat Enterprise Linux 7.5 的支持。

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理服务器灾难恢复 

更新中添加了新的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Red Hat Enterprise Linux 7.5、SUSE Linux Enterprise Server 12 的支持。



## <a name="next-steps"></a>后续步骤

在 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)页上时刻了解更新。
