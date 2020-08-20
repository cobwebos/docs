---
title: Azure Site Recovery 中的新增功能
description: 提供 Azure Site Recovery 服务中的新功能和最新更新的摘要。
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 09afd4416efc8e6904b7c487d5ed2f3be22db081
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654814"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 中的新增功能

[Azure Site Recovery](site-recovery-overview.md) 服务会持续进行更新和改进。 本文介绍最新版本、新功能和新内容，让你始终了解最新动态。 此页会定期更新。

可以在 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)频道中关注并订阅 Site Recovery 更新通知。

## <a name="supported-updates"></a>支持的更新

对于 Site Recovery 组件，我们支持 N-4 版本，其中 N 是最新发布的版本。 下表汇总了这些更新。

**更新** |  **统一安装程序** | **配置服务器 ova** | **移动服务代理** | **Site Recovery 提供程序** | **恢复服务代理**
--- | --- | --- | --- | --- | ---
[汇总49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0
[汇总48](https://support.microsoft.com/help/4573888/) | 9.35.5659.1 | 5.1.6200.0 | 9.35.5659.1 | 5.1.6200.0 | 2.0.9186.0
[汇总47](https://support.microsoft.com/help/4570609/) | 9.34.5634.1 | 5.1.6100.0 | 9.34.5634.1 | 5.1.6100.0 | HyperV 计算机-2.0.9183。0 <br> VMware 计算机-2.0.9177。0
[汇总 46](https://support.microsoft.com/help/4564347/) | 9.33.5598.1 | 5.1.5900.0 | 9.33.5598.1 | 5.1.5900.0 | 2.0.9175.0
[汇总 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0

[详细了解](service-updates-how-to.md)更新安装和支持。


## <a name="updates-august-2020"></a>2020年8月 (更新) 

### <a name="update-rollup-49"></a>更新汇总49

[更新汇总 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) 提供以下更新：

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）。
**问题修复/改进** | 汇总中详细说明的一些修复和改进。

## <a name="updates-july-2020"></a>2020年7月 (更新) 

### <a name="update-rollup-48"></a>更新汇总48

[更新汇总 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) 提供以下更新：

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）。
**问题修复/改进** | 汇总中详细说明的一些修复和改进。

> [!NOTE]
> 更新汇总48存在一个已知问题，用于启用使用 ADE 加密的 Linux 计算机的复制。 [了解详细信息](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137)。

### <a name="update-rollup-47"></a>更新汇总47

[更新汇总 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) 提供以下更新：

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）。
**问题修复/改进** | 汇总中详细说明的一些修复和改进。

## <a name="updates-june-2020"></a>更新（2020 年 6 月）

### <a name="update-rollup-46"></a>更新汇总 46

[更新汇总 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) 提供以下更新：

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）。
**问题修复/改进** | 汇总中详细说明的一些修复和改进。

## <a name="updates-march-2020"></a>更新（2020 年 3 月）

### <a name="update-rollup-45"></a>更新汇总 45

[更新汇总 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) 提供以下更新：

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）。
**问题修复/改进** | 汇总中详细说明的一些修复和改进。

## <a name="updates-january-2020"></a>更新（2020 年 1 月）

### <a name="update-rollup-44"></a>更新汇总 44

[更新汇总 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 没有针对 Site Recovery 提供程序和代理的更新。
**问题修复/改进** | 汇总中详细说明的一些修复和改进。

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware 灾难恢复

Azure 虚拟机现在支持使用客户管理的密钥进行静态加密。 [了解详细信息](azure-to-azure-how-to-enable-replication-cmk-disks.md)。


### <a name="update-rollup-43"></a>更新汇总 43

[更新汇总 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）


## <a name="updates-november-2019"></a>更新（2019 年 11 月）

### <a name="update-rollup-42"></a>更新汇总 42

[更新汇总 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）


### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**UEFI** | Site Recovery 现在支持对采用基于 UEFI 的启动体系结构的 Azure VM 进行灾难恢复。
**Linux** | Site Recovery 现在支持运行 Linux 的已启用 Azure 磁盘加密 (ADE) 的 Azure VM。
**第 2 代** | 所有第 2 代 Azure VM 现在都支持灾难恢复。
**区域** | 现在，可以在挪威地区为 Azure VM 启用灾难恢复。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 到 Azure 的灾难恢复

表中汇总了 VMware 到 Azure 的灾难恢复新功能。

**功能** | **详细信息**
--- | ---
**UEFI** | Site Recovery 现在支持对采用基于 UEFI 的启动体系结构的 VMware VM 进行灾难恢复。<br/><br/> 支持的操作系统包括 Windows Server 2019、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、SLES 12 SP4 和 RHEL 8。

## <a name="update-to-servicing-stack-updatesha-2"></a>更新到服务堆栈更新/SHA-2

若要将 Azure VM 灾难恢复到次要区域，或者将本地 VMware VM 或物理服务器灾难恢复到 Azure，请注意以下事项：

- 从移动服务扩展（适用于 Azure VM）和移动服务代理（适用于 VMware/物理计算机）版本 9.30.5407.1 开始，某些计算机操作系统必须运行服务堆栈更新和 SHA-2。 下表显示了详细信息。
- 请根据链接的知识库安装该更新和 SHA-2。 从 2019 年 9 月开始不再支持 SHA-1，如果未启用 SHA-2 代码签名，则无法按预期方式安装/升级代理扩展。
- 详细了解 [SHA-2 升级和要求](https://aka.ms/SHA-2KB)。

**操作系统** | Azure VM  | **VMware VM/物理计算机**
--- | --- | ---
**Windows 2008 R2 SP1** | [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [服务堆栈更新](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [服务堆栈更新](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [服务堆栈更新](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)



## <a name="updates-october-2019"></a>更新（2019 年 10 月）

### <a name="update-rollup-41"></a>更新汇总 41

[更新汇总 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）



### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**测试故障转移设置** | 设置测试故障转移时，现在可以配置测试故障转移 VM 和网络的设置，包括 IP 地址、NSG、内部负载均衡，以及每个计算机 NIC 的公共 IP 地址。 这些设置是可选的，不会更改当前行为。 如果未配置这些设置，可以在测试故障转移时选择 Azure VNet。 [了解详细信息](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/)。
**恢复计划** | 恢复计划现在限制为 100 个 VM，以确保故障转移的可靠性。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 到 Azure 的灾难恢复

表中汇总了 VMware 到 Azure 的灾难恢复新功能。

**功能** | **详细信息**
--- | ---
**恢复计划** | 恢复计划现在限制为 100 个 VM，以确保故障转移的可靠性。


## <a name="updates-september-2019"></a>更新（2019 年 9 月）

### <a name="update-rollup-40"></a>更新汇总 40

[更新汇总 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）




### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**故障回复后清理** | 故障转移到辅助 Azure 之后，再故障回复到主要区域，Site Recovery 会自动清理辅助区域中的计算机。 无需手动删除 VM 和 NIC。
**测试故障转移保留 IP 地址** | 现在，可以在灾难恢复演练期间保留源 VM 的 IP 地址，并为测试故障转移选取一个静态 IP 地址。

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
新进程服务器警报 | 我们添加了新的进程服务器警报。 [了解详细信息](vmware-physical-azure-monitor-process-server.md)。

### <a name="hyper-v-disaster-recovery"></a>Hyper-V 灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
存储帐户 | Site Recovery 现在支持使用为 Hyper-V 到 Azure 灾难恢复启用了防火墙的存储帐户。  可以选择已启用防火墙的存储帐户作为目标帐户，或用于缓存存储。 如果使用启用防火墙的帐户，请确保启用允许受信任的 Microsoft 服务的选项。<br/><br/> 包含或不包含 System Center VMM 的 Hyper-V VM 支持此选项。


## <a name="updates-august-2019"></a>更新（2019 年 8 月）

### <a name="update-rollup-39"></a>更新汇总 39

[更新汇总 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）


### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

表中汇总了 Azure VM 灾难恢复的新功能。

**功能** | **详细信息**
--- | ---
**不使用 Azure AD 的加密** | 现在，将 Azure VM 复制到运行 Windows 的托管磁盘时，支持不使用 Azure AD 应用的加密。
**用于故障转移的网络资源** | 故障转移到另一个区域时，现在可以将网络资源设置（NSG、负载均衡、公共 IP 地址）附加到 VM。

## <a name="updates-july-2019"></a>更新（2019 年 7 月）

### <a name="update-rollup-38"></a>更新汇总 38

[更新汇总 38](https://support.microsoft.com/help/4513507/) 提供以下更新。

**更新** | **详细信息**
--- | ---
**提供程序和代理** | 对 Site Recovery 代理和提供程序的更新（请参阅汇总中的详述）
**问题修复/改进** | 已做出多项修复和改进（参阅汇总中的详述）


### <a name="general"></a>常规

Site Recovery 现在支持将常规用途 v2 存储帐户用于缓存存储或目标存储。 以前仅支持 v1。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware 到 Azure 的灾难恢复

复制到包含托管磁盘的 Azure VM 时，现在可以复制最多 8 TB 的磁盘。


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
**自动更新** | 为在启用了灾难恢复的 Azure Vm 上运行的移动服务扩展配置自动更新时，现在可以选择要使用的现有自动化帐户，而不是使用 Site Recovery 创建的默认帐户。 [了解详细信息](azure-to-azure-autoupdate.md)。


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
**配置服务器** | Site Recovery 现在支持具有多个 NIC 的配置服务器。 先将附加的适配器添加到配置服务器 VM，然后再在保管库中注册配置服务器。 如果在注册之后再添加，则需要在保管库中重新注册服务器。


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
**标准 SSD** | 现在可以使用[标准 SSD 磁盘](../virtual-machines/disks-types.md#standard-ssd)对 Azure VM 设置灾难恢复。
**存储空间直通** | 可以使用[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)对 Azure VM 应用中运行的应用设置灾难恢复，以实现高可用性。  将存储空间直通 (S2D) 与 Site Recovery 结合使用可为 Azure VM 工作负荷提供全面的保护。 使用 S2D 可在 Azure 中托管来宾群集。 当 VM 托管了关键应用程序（例如 SAP ASCS 层、SQL Server 或横向扩展文件服务器）时，此功能特别有用。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理服务器灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux BRTFS 文件系统** | Site Recovery 现在支持复制采用使用 BRTFS 文件系统的 VMware VM。 如果存在以下情况，则不支持复制：<br/><br/>- 启用复制后 BTRFS 文件系统子卷发生更改。<br/><br/>- 文件系统分散在多个磁盘中。<br/><br/>- BTRFS 文件系统支持 RAID。
**Windows Server 2019** | 添加了对运行 Windows Server 2019 的计算机的支持。


## <a name="updates-january-2019"></a>更新（2019 年 1 月）


### <a name="accelerated-networking-azure-vms"></a>加速网络 (Azure VM)

使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，提升网络性能。 为 Azure VM 启用复制时，Site Recovery 会检测是否启用了加速网络。 如果启用了加速网络，Site Recovery 会在故障转移后自动在目标副本 Azure VM 上针对 [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 配置加速网络。

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
**Linux 支持** | 为 RedHat 工作站6/7 添加了支持，并为 Ubuntu、Debian 和 SUSE 添加了新的内核版本。
**存储空间直通** | Site Recovery 支持使用存储空间直通 (S2D) 的 Azure VM。

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM/物理服务器灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对 Redhat Enterprise Linux 7.6、RedHat Workstation 6/7、Oracle Linux 6.10 和 Oracle Linux 7.6 的支持以及新的 Ubuntu、Debian 和 SUSE 内核版本。


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
**Linux 支持** |  添加了对具有 Red Hat 兼容内核的 Oracle Linux 6.8、Oracle Linux 6.9 和 Oracle Linux 7.0 的支持以及对 Unbreakable Enterprise Kernel (UEK) 第 5 版的支持。
**LVM** | 添加了对 LVM 和 LVM2 卷的支持。<br/><br/> 现在支持磁盘分区和 LVM 卷上的 /boot 目录。
**Directories** | 添加了对这些设置为独立分区的目录，或不在同一系统磁盘中的文件系统的支持：<br/><br/> /(root)、/boot、/usr、/usr/local、/var、/etc。
**Windows Server 2008** | 添加了对动态磁盘的支持。
**故障转移** | 改进了 storvsc 和 vsbus 不是启动驱动程序的 VMware VM 的故障转移时间。
**UEFI 支持** | Azure VM 不支持 UEFI 启动类型。 现在可以通过 Site Recovery 将使用 UEFI 的本地物理服务器迁移到 Azure。 Site Recovery 在迁移服务器之前会将启动类型转换为 BIOS。 Site Recovery 以前仅支持对 VM 执行此转换。 该项支持适用于运行 Windows Server 2012 或更高版本的物理服务器。

### <a name="azure-vm-disaster-recovery"></a>Azure VM 灾难恢复

下表中总结了本月添加的功能。

**功能** | **详细信息**
--- | ---
**Linux 支持** | 添加了对具有 Red Hat 兼容内核的 Oracle Linux 6.8、Oracle Linux 6.9 和 Oracle Linux 7.0 的支持以及对 Unbreakable Enterprise Kernel (UEK) 第 5 版的支持。
**Linux BRTFS 文件系统** | 支持对 Azure VM 使用。
**可用性区域中的 Azure Vm** | 可以为在可用性区域中部署的 Azure Vm 启用到另一个区域的复制。 现在可以为 Azure VM 启用复制功能，并设置一个目标，以便故障转移到单个 VM 实例、可用性集中的 VM 或可用性区域中的 VM。 此设置不影响复制。 [阅读](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
**已启用防火墙的存储（门户/PowerShell）** | 添加了对[已启用防火墙的存储帐户](../storage/common/storage-network-security.md)的支持。<br/><br/> 可将已启用防火墙的存储帐户中使用非托管磁盘的 Azure VM 复制到另一个 Azure 区域，以实现灾难恢复。<br/><br/> 可将已启用防火墙的存储帐户用作非托管磁盘的目标存储帐户。<br/><br/> 支持在门户和 PowerShell 中使用。

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
**已启用防火墙的存储 (PowerShell)** | 添加了对[已启用防火墙的存储帐户](../storage/common/storage-network-security.md)的支持。<br/><br/> 可将已启用防火墙的存储帐户中使用非托管磁盘的 Azure VM 复制到另一个 Azure 区域，以实现灾难恢复。<br/><br/> 可将已启用防火墙的存储帐户用作非托管磁盘的目标存储帐户。<br/><br/> 仅支持在 PowerShell 中使用。


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
**Linux 支持** | 为 RedHat Enterprise Linux 6.10、CentOS 6.10 添加的支持。<br/><br/> 现在支持基于 Linux 且在旧式 BIOS 兼容模式下使用 GUID 分区表 (GPT) 分区样式的 VM。 有关详细信息，请查看 [Azure VM 常见问题解答](../virtual-machines/linux/faq-for-disks.md)。
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