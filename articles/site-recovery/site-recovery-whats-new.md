---
title: Azure Site Recovery 中的新增功能
description: 提供 Azure Site Recovery 服务中的新功能和最新更新。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 4a9feff3b0bc98b5d9853268d033e60de55ea5ea
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024827"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 中的新增功能

[Azure Site Recovery](site-recovery-overview.md) 服务会持续进行更新和改进。 本文介绍最新版本、新功能和新内容，让你始终了解最新动态。 此页会定期更新。

你可以在[Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)通道中跟踪并订阅 Site Recovery 更新通知。



## <a name="supported-updates"></a>支持的更新

对于 Site Recovery 组件，我们支持 N-4 版本，其中 N 是最新的已发布版本。 下表概述了这些报表。

**Update** |  **统一安装** | **配置服务器 .ova** | **移动服务代理** | **Site Recovery 提供程序** | **恢复服务代理**
--- | --- | --- | --- | --- | ---
[汇总43](https://support.microsoft.com/help/4531426/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[汇总42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[汇总41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[汇总40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0
[汇总39](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0

        

[了解](service-updates-how-to.md)有关更新安装和支持的详细信息。

## <a name="updates-january-2020"></a>更新（2020年1月）

### <a name="update-rollup-43"></a>更新汇总43

[更新汇总 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序（在汇总中详细说明）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）


## <a name="updates-november-2019"></a>更新（2019年11月）

### <a name="update-rollup-42"></a>更新汇总42

[更新汇总 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序（在汇总中详细说明）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）


## <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**UEFI** | Site Recovery 现在支持具有基于 UEFI 的启动体系结构的 Azure Vm 的灾难恢复。
**Linux** | Site Recovery 现在支持运行 Linux 的 Azure Vm 和 Azure 磁盘加密（ADE）。
**第2代** | 所有第2代 Azure Vm 现在都支持灾难恢复。
**区域** | 你现在可以在挪威地区启用 Azure Vm 的灾难恢复。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 到 Azure 的灾难恢复

用于 VMware 到 Azure 灾难恢复的新功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**UEFI** | Site Recovery 现在支持具有基于 UEFI 的启动体系结构的 VMware Vm 的灾难恢复。<br/><br/> 支持的操作系统包括 Windows Server 2019，Windows Server 2016，Windows Server 2012 R2，Windows Server 2012，SLES 12 SP4，RHEL 8。

## <a name="update-to-servicing-stack-updatesha-2"></a>更新到服务堆栈更新/SHA-1

若要将 Azure Vm 的灾难恢复到次要区域，或者将本地 VMware Vm 或物理服务器灾难恢复到 Azure，请注意以下事项：

- 在移动服务扩展（适用于 Azure Vm）和移动服务代理（对于 VMware/物理计算机）的版本9.30.5407.1 中，一些计算机操作系统必须运行服务堆栈更新和 SHA-1。 下表显示了详细信息。
- 根据链接的 KB，安装更新和 SHA-1。 2019年9月不支持 SHA-1，如果未启用 SHA-1 代码签名，则代理扩展不会按预期安装/升级。
- 详细了解[sha-1 升级和要求](https://aka.ms/SHA-2KB)。

**操作系统** | **Azure VM** | **VMware VM/物理计算机**
--- | --- | ---
**Windows 2008 R2 SP1** | [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [SHA-1](https://support.microsoft.com/help/4474419)| [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [SHA-1](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [服务堆栈更新](https://support.microsoft.com/help/4493730)<br/> [SHA-1](https://support.microsoft.com/help/4474419)| [服务堆栈更新](https://support.microsoft.com/help/4493730)<br/> [SHA-1](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [SHA-1](https://support.microsoft.com/help/4474419)| [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [Sha-1](https://support.microsoft.com/help/4474419)。



## <a name="updates-october-2019"></a>更新（2019年10月）

### <a name="update-rollup-41"></a>更新汇总41

[更新汇总 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序（在汇总中详细说明）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）



### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**测试故障转移设置** | 设置测试故障转移时，你现在可以为测试故障转移 VM 和网络配置设置，包括 IP 地址、NSG、内部负载均衡，以及每个计算机 NIC 的公共 IP 地址。 这些设置是可选的，不更改当前行为。 如果未配置这些设置，则可在测试故障转移时选择 Azure VNet。 [了解详细信息](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/)。
**恢复计划** | 恢复计划现在限制为100个 Vm，以确保故障转移的可靠性。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 到 Azure 的灾难恢复

用于 VMware 到 Azure 灾难恢复的新功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**恢复计划** | 恢复计划现在限制为100个 Vm，以确保故障转移的可靠性。


## <a name="updates-september-2019"></a>更新（2019年9月）

### <a name="update-rollup-40"></a>更新汇总40

[更新汇总 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序（在汇总中详细说明）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）




### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**故障回复后清理** | 故障转移到辅助 Azure，然后故障回复到主要区域后，Site Recovery 会自动清除次要区域中的计算机。 无需手动删除 Vm 和 Nic。
**测试故障转移保留 IP 地址** | 你现在可以在灾难恢复演练期间保留源 VM 的 IP 地址，并为测试故障转移选择静态 IP 地址。

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
新进程服务器警报 | 我们添加了新的进程服务器警报。 [了解详细信息](vmware-physical-azure-monitor-process-server.md)。 

### <a name="hyper-v-disaster-recovery"></a>Hyper-v 灾难恢复

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
存储帐户 | Site Recovery 现在支持将启用了防火墙的存储帐户用于 Hyper-v 到 Azure 的灾难恢复。  你可以选择 "支持防火墙的存储帐户" 作为目标帐户，或选择 "缓存存储"。 如果使用启用防火墙的帐户，请确保启用允许受信任的 Microsoft 服务的选项。<br/><br/> 对于带有或不带 System Center VMM 的 Hyper-v Vm，支持此项。


## <a name="updates-august-2019"></a>更新（2019年8月）

### <a name="update-rollup-39"></a>更新汇总39

[更新汇总 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序（在汇总中详细说明）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）


### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**无 Azure AD 加密** | 对于运行 Windows 的托管磁盘，Azure VM 复制现在支持没有 Azure AD 应用的加密。
**用于故障转移的网络资源** | 当故障转移到另一个区域时，你现在可以将网络资源设置（Nsg、负载平衡、公共 IP 地址）附加到 VM。 

## <a name="updates-july-2019"></a>更新（2019年7月）

### <a name="update-rollup-38"></a>更新汇总 38

[更新汇总 38](https://support.microsoft.com/help/4513507/)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序（在汇总中详细说明）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）


### <a name="general"></a>常规

Site Recovery 现在支持将常规用途 v2 存储帐户用于缓存存储或目标存储。 以前仅支持 v1。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 到 Azure 的灾难恢复

复制到包含托管磁盘的 Azure VM 时，你现在可以复制最多 8 TB 的磁盘。


## <a name="updates-june-2019"></a>更新（2019年6月）

### <a name="update-rollup-37"></a>更新汇总37

[更新汇总 37](https://support.microsoft.com/help/4508614/)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | 更新 Site Recovery 代理和提供程序（在汇总中详细说明）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**GPT 分区** | 从更新汇总37开始（移动服务版本9.25.5241.1），UEFI 最多支持五个 GPT 分区。 在此更新之前，支持四个。



## <a name="updates-may-2019"></a>更新（可能为2019）

### <a name="update-rollup-36"></a>更新汇总 36

[更新汇总 36](https://support.microsoft.com/help/4503156)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**复制添加的磁盘** | 为已启用灾难恢复的 Azure VM 添加的数据磁盘进行复制。 [了解详细信息](azure-to-azure-enable-replication-added-disk.md)。
**自动更新** | 为在启用了灾难恢复的 Azure Vm 上运行的移动服务扩展配置自动更新时，现在可以选择要使用的现有自动化帐户，而不是使用 Site Recovery 创建的默认帐户。 [了解详细信息](azure-to-azure-autoupdate.md)。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**进程服务器监视** | 对于本地 VMware Vm 和物理服务器的灾难恢复，请监视服务器运行状况报告和警报的进程服务器问题并对其进行故障排除。 [了解详细信息](vmware-physical-azure-monitor-process-server.md)。 





## <a name="updates-march-2019"></a>更新（2019年3月）

### <a name="update-rollup-35"></a>更新汇总35

[更新汇总 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**托管磁盘** | 现在，将本地 VMware Vm 和物理服务器复制到 Azure 中的托管磁盘。 本地数据将发送到 Azure 中的缓存存储帐户，并在目标位置中的托管磁盘上创建恢复点。 这可确保无需管理多个目标存储帐户。
**配置服务器** | Site Recovery 现在支持具有多个 Nic 的配置服务器。 在保管库中注册配置服务器之前，请将其他适配器添加到配置服务器 VM。 如果以后添加，则需要在保管库中重新注册服务器。


## <a name="updates-february-2019"></a>更新（2019年2月）

### <a name="update-rollup-34"></a>更新汇总34 

[更新汇总 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。


### <a name="update-rollup-33"></a>更新汇总33 

[更新汇总 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。


### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复 
本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**网络映射** | 对于 Azure VM 灾难恢复，你现在可以在启用复制时使用任何可用的目标网络。 
**标准 SSD** | 你现在可以使用[标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)为 Azure vm 设置灾难恢复。
**存储空间直通** | 可以通过使用[存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)实现高可用性，为在 Azure VM 应用上运行的应用设置灾难恢复。  结合使用存储空间直通（S2D）和 Site Recovery 提供对 Azure VM 工作负荷的全面保护。 S2D 使你可以在 Azure 中托管来宾群集。 当 VM 承载关键应用程序（例如 SAP ASCS 层、SQL Server 或横向扩展文件服务器）时，此方法特别有用。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复
本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**Linux BRTFS 文件系统** | Site Recovery 现在支持将 VMware Vm 复制到 BRTFS 文件系统。 如果是以下情况，则不支持复制：<br/><br/>-启用复制后，将更改 BTRFS 文件系统子卷。<br/><br/>-文件系统分散在多个磁盘上。<br/><br/>-BTRFS 文件系统支持 RAID。
**Windows Server 2019** | 为运行 Windows Server 2019 的计算机添加了支持。


## <a name="updates-january-2019"></a>更新（2019年1月）


### <a name="accelerated-networking-azure-vms"></a>加速网络（Azure Vm）

加速网络为 VM 启用单一根 i/o 虚拟化（SR-IOV），从而提高网络性能。 为 Azure VM 启用复制时，Site Recovery 会检测是否启用了加速网络。 如果启用了加速网络，Site Recovery 会在故障转移后自动在目标副本 Azure VM 上针对 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 配置加速网络。

[了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="update-rollup-32"></a>更新汇总32 

[更新汇总 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 为 RedHat 工作站6/7 添加了支持，并为 Ubuntu、Debian 和 SUSE 添加了新的内核版本。
**存储空间直通** | Site Recovery 支持使用存储空间直通（S2D）的 Azure Vm。

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware Vm/物理服务器灾难恢复

本月增加的功能在表中进行了总结。
 
**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Redhat Enterprise Linux 7.6、RedHat Workstation 6/7、Oracle Linux 6.10 和 Oracle Linux 7.6 的支持以及新的 Ubuntu、Debian 和 SUSE 内核版本。


### <a name="update-rollup-31"></a>更新汇总31 

[更新汇总 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。

### <a name="vmware-vmsphysical-servers-replication"></a>VMware Vm/物理服务器复制 
本月增加的功能在表中进行了总结。
**功能** | **详细信息**
--- | ---
**Linux 支持** |  添加了针对 Oracle Linux 6.8 的支持、Oracle Linux 6.9 和 Oracle Linux 7.0 与 Red Hat 兼容内核，以及 Unbreakable Enterprise 内核（UEK）版本5。
**LVM** | 为 LVM 和 LVM2 卷添加了支持。<br/><br/> 现在支持磁盘分区和 LVM 卷上的/boot 目录。
**Directories** | 为这些目录添加的支持被设置为单独的分区或不在同一系统磁盘上的文件系统：<br/><br/> /（root）、/boot、/usr、/usr/local、/var、/etc。
**Windows Server 2008** | 添加了对动态磁盘的支持。
**故障转移** | 改善了 VMware Vm 的故障转移时间，其中 storvsc 和 vsbus 不是启动驱动程序。
**UEFI 支持** | Azure Vm 不支持启动类型 UEFI。 你现在可以通过 Site Recovery 将本地物理服务器与 UEFI 迁移到 Azure。 Site Recovery 迁移之前，通过将启动类型转换为 BIOS 来迁移服务器。 Site Recovery 以前仅支持对 Vm 执行此转换。 支持适用于运行 Windows Server 2012 或更高版本的物理服务器。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复
本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了针对 Oracle Linux 6.8 的支持、Oracle Linux 6.9 和 Oracle Linux 7.0 与 Red Hat 兼容内核，以及 Unbreakable Enterprise 内核（UEK）版本5。
**Linux BRTFS 文件系统** | 支持 Azure Vm。
**可用性区域中的 Azure Vm** | 可以为在可用性区域中部署的 Azure Vm 启用到另一个区域的复制。 现在可以为 Azure VM 启用复制功能，并设置一个目标，以便故障转移到单个 VM 实例、可用性集中的 VM 或可用性区域中的 VM。 此设置不影响复制。 [阅读](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
**支持防火墙的存储（门户/PowerShell）** | 为[启用防火墙的存储帐户添加了](https://docs.microsoft.com/azure/storage/common/storage-network-security)支持。<br/><br/> 可以将支持防火墙的存储帐户上的非托管磁盘的 Azure Vm 复制到其他 Azure 区域，以便进行灾难恢复。<br/><br/> 可以使用启用防火墙的存储帐户作为非托管磁盘的目标存储帐户。<br/><br/> 在门户中支持并使用 PowerShell。

## <a name="updates-december-2018"></a>更新（2018年12月）

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>移动服务（Azure Vm）的自动更新

Site Recovery 增加了一个选项，可以针对移动服务扩展进行自动更新。 移动服务扩展安装在每个通过 Site Recovery 复制的 Azure VM 上。 启用复制时，请选择是否允许 Site Recovery 管理对扩展的更新。

更新不需 VM 重启，且不影响复制。 [了解详细信息](azure-to-azure-autoupdate.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>针对 Azure VM 灾难恢复的价格计算器

Azure Vm 的灾难恢复产生了 VM 许可成本，并产生了网络和存储成本。 Azure 提供[定价计算器](https://aka.ms/a2a-cost-estimator)，用于计算这些费用。 Site Recovery 现在提供[定价估算](https://aka.ms/a2a-cost-estimator)功能，可以根据三层应用的情况对示例部署定价。该应用使用 6 个 VM，其中包含 12 个标准 HDD 磁盘和 6 个高级 SSD 磁盘。

- 该示例假设数据更改为标准的一天 10 GB，高级版的数据更改为 20 GB。
- 对于特定的部署，可以更改变量来估算费用。
- 可以指定 VM 的数目、托管磁盘的数目和类型，以及预期的跨 VM 总数据更改率。
- 另外，可以通过应用一个压缩因素来估算带宽费用。

[阅读](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。


## <a name="updates-october-2018"></a>更新（2018年10月）

### <a name="update-rollup-30"></a>更新汇总30 

[更新汇总 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复
本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**区域支持** | 为澳大利亚中部1和澳大利亚中部2添加了 Site Recovery 支持。
**磁盘加密支持** | 添加了对通过 Azure AD 应用进行 Azure 磁盘加密（ADE）加密的 Azure Vm 的灾难恢复的支持。 [了解详细信息](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**磁盘排除** | 在 Azure VM 复制过程中，未初始化的磁盘现在会自动排除。
**支持防火墙的存储（PowerShell）** | 为[启用防火墙的存储帐户添加了](https://docs.microsoft.com/azure/storage/common/storage-network-security)支持。<br/><br/> 可以将支持防火墙的存储帐户上的非托管磁盘的 Azure Vm 复制到其他 Azure 区域，以便进行灾难恢复。<br/><br/> 可以使用启用防火墙的存储帐户作为非托管磁盘的目标存储帐户。<br/><br/> 仅支持使用 PowerShell。


### <a name="update-rollup-29"></a>更新汇总29 

[更新汇总 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。


## <a name="updates-august-2018"></a>更新（2018年8月）

### <a name="update-rollup-28"></a>更新汇总 28 

[更新汇总 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复 
本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 RedHat Enterprise Linux 6.10 的支持;CentOS 6.10。<br/><br/>
**云支持** | 德国云中 Azure Vm 支持的灾难恢复。
**跨订阅灾难恢复** | 支持将一个区域中的 Azure Vm 复制到不同订阅中的另一个区域，该区域位于同一个 Azure Active Directory 租户中。 [了解详细信息](https://aka.ms/cross-sub-blog)。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理服务器灾难恢复 
本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 为 RedHat Enterprise Linux 6.10、CentOS 6.10 添加的支持。<br/><br/> 现在支持在传统的 BIOS 兼容模式下使用 GUID 分区表（GPT）分区形式的基于 Linux 的 Vm。 有关详细信息，请查看[AZURE VM 常见问题解答](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks)。 
**迁移后 Vm 的灾难恢复** | 支持为迁移到 Azure 的本地 VMware VM 启用到次要区域的灾难恢复，而无需在启用复制前卸载 VM 上的移动服务。
**Windows Server 2008** | 支持迁移运行 Windows Server 2008 R2/2008 64 位和32位的计算机。<br/><br/> 仅迁移（复制和故障转移）。 故障回复不受支持。

## <a name="updates-july-2018"></a>更新（2018年7月）

### <a name="update-rollup-27-july-2018"></a>更新汇总27（2018年7月）

[更新汇总 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)提供了以下更新。

**Update** | **详细信息**
--- | ---
**提供程序和代理** | Site Recovery 代理和提供程序的更新（如汇总中所述）。
**问题修复/改进** | 许多修补程序和改进功能（如汇总中所述）。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复 

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 为 Red Hat Enterprise Linux 7.5 添加了支持。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理服务器灾难恢复 

本月增加的功能在表中进行了总结。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 为 Red Hat Enterprise Linux 7.5 添加了支持，SUSE Linux Enterprise Server 12。



## <a name="next-steps"></a>后续步骤

在 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)页上时刻了解更新。
