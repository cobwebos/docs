---
title: Azure Site Recovery 新增功能 | Microsoft Docs
description: 提供 Azure Site Recovery 中引入的新功能的摘要
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: raynew
ms.openlocfilehash: 5cd4b86c9c70f713a207f7feea9fa8efc06b6247
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146898"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 中的新增功能

[Azure Site Recovery](site-recovery-overview.md) 服务会持续进行更新和改进。 本文介绍最新版本、新功能和新内容，让你始终了解最新动态。 此页会定期更新。

你可以在[Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)通道中跟踪并订阅 Site Recovery 更新通知。

## <a name="supported-updates"></a>支持的更新

对于 Site Recovery 组件, 我们支持 N-4 版本, 其中 N 是最新的已发布版本。 下表总结了这些情况。

**更新** |  **统一安装** | **配置服务器 .ova** | **移动服务代理** | **Site Recovery 提供程序** | **恢复服务代理** 
--- | --- | --- | --- | ---
[汇总39](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0
[汇总38](https://support.microsoft.com/help/4513507/) | 9.26.5269.1 | 5.1.4500.0 | 9.26.5269.1 | 5.1.4500.0 | 2.0.9165.0
[汇总37](https://support.microsoft.com/help/4508614/) | 9.25.5241.1 | 5.1.4300.0 | 9.25.5241.1 | 5.1.4300.0 | 2.0.9163.0
[汇总36](https://support.microsoft.com/help/4503156/) | 9.24.5211.1 | 5.1.4150.0 | 9.24.5211.1 | 5.1.4150.0 | 2.0.9160.0 
[汇总35](https://support.microsoft.com/help/4494485/) | 9.23.5163.1 | 5.1.4000.0 | 9.23.5163.1 | 5.1.4000.0 | 2.0.9156.0 
        

[了解](service-updates-how-to.md)有关更新安装和支持的详细信息。


## <a name="updates-august-2019"></a>更新 (2019 年8月)

### <a name="update-rollup-39"></a>更新汇总39

[更新汇总 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）


### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**无 Azure AD 加密** | 对于运行 Windows 的托管磁盘, Azure VM 复制现在支持没有 Azure AD 应用的加密。
**用于故障转移的网络资源** | 当故障转移到另一个区域时, 你现在可以将网络资源设置 (Nsg、负载平衡、公共 IP 地址) 附加到 VM。 

## <a name="updates-july-2019"></a>更新 (2019 年7月)

### <a name="update-rollup-38"></a>更新汇总 38

[更新汇总 38](https://support.microsoft.com/help/4513507/)提供了以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）


### <a name="general"></a>常规

Site Recovery 现在支持将常规用途 v2 存储帐户用于缓存存储或目标存储。 以前仅支持 v1。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 到 Azure 的灾难恢复

复制到包含托管磁盘的 Azure VM 时, 你现在可以复制最多 8 TB 的磁盘。


## <a name="updates-june-2019"></a>更新（2019 年 6 月）

### <a name="update-rollup-37"></a>更新汇总 37

[更新汇总 37](https://support.microsoft.com/help/4508614/) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**GPT 分区** | 从更新汇总 37 开始（移动服务版本 9.25.5241.1），UEFI 最多支持五个 GPT 分区。 在此更新之前，支持了四个。



## <a name="updates-may-2019"></a>更新（2019 年 5 月）

### <a name="update-rollup-36"></a>更新汇总 36

[更新汇总 36](https://support.microsoft.com/help/4503156) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**复制添加的磁盘** | 为添加到已启用灾难恢复的 Azure VM 的数据磁盘启用复制。 [了解详细信息](azure-to-azure-enable-replication-added-disk.md)。
**自动更新** | 为在启用了灾难恢复的 Azure Vm 上运行的移动服务扩展配置自动更新时, 现在可以选择要使用的现有自动化帐户, 而不是使用 Site Recovery 创建的默认帐户。 [了解详细信息](azure-to-azure-autoupdate.md)。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**进程服务器监视** | 对于本地 VMware VM 和物理服务器的灾难恢复，通过改进的服务器运行状况报告和警报来监视和排查进程服务器问题。 [了解详细信息](vmware-physical-azure-monitor-process-server.md)。 





## <a name="updates-march-2019"></a>更新（2019 年 3 月）

### <a name="update-rollup-35"></a>更新汇总 35

[更新汇总 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**托管磁盘** | 现在可将本地 VMware VM 和物理服务器直接复制到 Azure 中的托管磁盘。 本地数据将发送到 Azure 中的缓存存储帐户，恢复点将在目标位置的托管磁盘中创建。 这可确保无需管理多个目标存储帐户。
**配置服务器** | Site Recovery 现在支持具有多个 Nic 的配置服务器。 在保管库中注册配置服务器之前, 请将其他适配器添加到配置服务器 VM。 如果在注册之后再添加，则需要在保管库中重新注册服务器。


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


### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复 
下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**网络映射** | 对于 Azure VM 灾难恢复，现在可以在启用复制时使用任何可用的目标网络。 
**标准 SSD** | 现在可以使用[标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)对 Azure VM 设置灾难恢复。
**存储空间直通** | 可以使用[存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)对 Azure VM 应用中运行的应用设置灾难恢复，以实现高可用性。  将存储空间直通 (S2D) 与 Site Recovery 结合使用可为 Azure VM 工作负荷提供全面的保护。 使用 S2D 可在 Azure 中托管来宾群集。 当 VM 托管了关键应用程序（例如 SAP ASCS 层、SQL Server 或横向扩展文件服务器）时，此功能特别有用。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复
下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux BRTFS 文件系统** | Site Recovery 现在支持复制采用使用 BRTFS 文件系统的 VMware VM。 如果存在以下情况，则不支持复制：<br/><br/>- 启用复制后 BTRFS 文件系统子卷发生更改。<br/><br/>- 文件系统分散在多个磁盘中。<br/><br/>- BTRFS 文件系统支持 RAID。
**Windows Server 2019** | 添加了对运行 Windows Server 2019 的计算机的支持。


## <a name="updates-january-2019"></a>更新（2019 年 1 月）


### <a name="accelerated-networking-azure-vms"></a>加速网络 (Azure VM)

加速网络为 VM 启用单一根 i/o 虚拟化 (SR-IOV), 从而提高网络性能。 为 Azure VM 启用复制时，Site Recovery 会检测是否启用了加速网络。 如果启用了加速网络，Site Recovery 会在故障转移后自动在目标副本 Azure VM 上针对 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 配置加速网络。

[了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="update-rollup-32"></a>更新汇总 32 

[更新汇总 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 为 RedHat 工作站6/7 添加了支持, 并为 Ubuntu、Debian 和 SUSE 添加了新的内核版本。
**存储空间直通** | Site Recovery 支持使用存储空间直通 (S2D) 的 Azure VM。

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM/物理服务器灾难恢复

下表中总结了本月添加的功能。
 
**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Redhat Enterprise Linux 7.6、RedHat Workstation 6/7、Oracle Linux 6.10/7.6 和新的 Ubuntu、Debian 和 SUSE 内核版本的支持。


### <a name="update-rollup-31"></a>更新汇总 31 

[更新汇总 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM/物理服务器复制 
下表中总结了本月添加的功能。
**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Oracle Linux 6.8 和 6.9/7.0 以及 UEK5 内核的支持。
**LVM** | 添加了对 LVM 和 LVM2 卷的支持。<br/><br/> 现在支持磁盘分区和 LVM 卷上的 /boot 目录。
**Directories** | 添加了对这些设置为独立分区的目录，或不在同一系统磁盘中的文件系统的支持：<br/><br/> /(root)、/boot、/usr、/usr/local、/var、/etc。
**Windows Server 2008** | 添加了对动态磁盘的支持。
**故障转移** | 改进了 storvsc 和 vsbus 不是启动驱动程序的 VMware VM 的故障转移时间。
**UEFI 支持** | Azure VM 不支持 UEFI 启动类型。 现在可以通过 Site Recovery 将使用 UEFI 的本地物理服务器迁移到 Azure。 Site Recovery 在迁移服务器之前会将启动类型转换为 BIOS。 Site Recovery 以前仅支持对 VM 执行此转换。 该项支持适用于运行 Windows Server 2012 或更高版本的物理服务器。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复
下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Oracle Linux 6.8 和 6.9/7.0 以及 UEK5 内核的支持。
**Linux BRTFS 文件系统** | 支持对 Azure VM 使用。
**可用性区域中的 Azure Vm** | 可以为在可用性区域中部署的 Azure Vm 启用到另一个区域的复制。 现在可以为 Azure VM 启用复制功能，并设置一个目标，以便故障转移到单个 VM 实例、可用性集中的 VM 或可用性区域中的 VM。 此设置不影响复制。 [阅读](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
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

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复
下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**区域支持** | 为澳大利亚中部1和澳大利亚中部2添加了 Site Recovery 支持。
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

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复 
下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 RedHat Enterprise Linux 6.10 的支持;CentOS 6.10。<br/><br/>
**云支持** | 添加了对德国云中 Azure VM 灾难恢复的支持。
**跨订阅灾难恢复** | 支持将一个区域中的 Azure VM 复制到同一 Azure Active Directory 租户中不同订阅内的另一个区域。 [了解详细信息](https://aka.ms/cross-sub-blog)。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理服务器灾难恢复 
下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 为 RedHat Enterprise Linux 6.10、CentOS 6.10 添加的支持。<br/><br/> 现在支持基于 Linux 且在旧式 BIOS 兼容模式下使用 GUID 分区表 (GPT) 分区样式的 VM。 有关详细信息，请查看 [Azure VM 常见问题解答](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks)。 
**迁移后的 VM 灾难恢复** | 支持将已迁移到 Azure 的本地 VMware VM 灾难恢复到次要区域，启用复制之前无需在 VM 上卸载移动服务。
**Windows Server 2008** | 支持迁移运行 Windows Server 2008 R2/2008 64 位和 32 位的计算机。<br/><br/> 仅限迁移（复制和故障转移）。 不支持故障回复。

## <a name="updates-july-2018"></a>更新（2018 年 7 月）

### <a name="update-rollup-27-july-2018"></a>更新汇总 27（2018 年 7 月）

[更新汇总 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（参阅汇总中的详述）。
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复 

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Red Hat Enterprise Linux 7.5 的支持。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理服务器灾难恢复 

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Red Hat Enterprise Linux 7.5、SUSE Linux Enterprise Server 12 的支持。



## <a name="next-steps"></a>后续步骤

在 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)页上时刻了解更新。
