---
title: "用于复制到 Azure 的 Azure Site Recovery 支持矩阵 | Microsoft Docs"
description: "汇总了 Azure Site Recovery 支持的操作系统和组件"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 103643ace17961cd4e2e37313f75c775e3dcd8cd
ms.contentlocale: zh-cn
ms.lasthandoff: 09/26/2017

---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>用于从本地复制到 Azure 的 Azure Site Recovery 支持矩阵


本文总结了复制和恢复到 Azure 时 Azure Site Recovery 支持的配置和组件。 有关 Azure Site Recovery 要求的详细信息，请参阅[先决条件](site-recovery-prereq.md)。


## <a name="support-for-deployment-options"></a>部署选项支持

**部署** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）** |
--- | --- | ---
**Azure 门户** | 本地 VMware VM 到 Azure 存储，使用 Azure 资源管理器或经典存储和网络。<br/><br/> 故障转移到基于资源管理器的 VM 或经典 VM。 | 本地 Hyper-V VM 到 Azure 存储，使用资源管理器或经典存储和网络。<br/><br/> 故障转移到基于资源管理器的 VM 或经典 VM。
**经典门户** | 只能使用维护模式。 无法创建新的保管库。 | 仅限维护模式。
**PowerShell** | 目前不支持。 | 支持


## <a name="support-for-datacenter-management-servers"></a>数据中心管理服务器支持

### <a name="virtualization-management-entities"></a>虚拟化管理实体

**部署** | **支持**
--- | ---
**VMware VM/物理服务器** | vCenter 6.5、6.0 或 5.5
**Hyper-V（具有 Virtual Machine Manager）** | System Center Virtual Machine Manager 2016 和 System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > 目前不支持混合使用 Windows Server 2016 和 2012 R2 主机的 System Center Virtual Machine Manager 2016 云。
  > 当前不支持包括现有 SCVMM 2012 R2 至 2016 升级的配置。
### <a name="host-servers"></a>主机服务器

**部署** | **支持**
--- | ---
**VMware VM/物理服务器** | vSphere 6.5、6.0、5.5
**Hyper-V（具有/不具有 Virtual Machine Manager）** | Windows Server 2016、带有最新更新的 Windows Server 2012 R2。<br></br>如果使用了 SCVMM，Windows Server 2016 主机应由 SCVMM 2016 托管。


  >[!Note]
  >当前不支持混合使用主机（运行 Windows Server 2016 和 2012 R2）的 Hyper-V 站点。 当前不支持恢复到 Windows Server 2016 主机上 VM 的备用位置。

## <a name="support-for-replicated-machine-os-versions"></a>已复制的计算机操作系统版本支持

复制到 Azure 时，受保护的虚拟机必须符合 [Azure 要求](#failed-over-azure-vm-requirements)。
下表汇总了使用 Azure Site Recovery 时多种部署方案中的已复制操作系统支持。 该支持适用于在所提及的 OS 上运行的所有工作负荷。

 **VMware/物理服务器** | **Hyper-V（使用/不使用 VMM）** |
--- | --- |
64 位 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 及其更高版本<br/>*Windows Server 2016* - 当前在 VMware 虚拟机和物理服务器上不受支持。 <br/><br/> Red Hat Enterprise Linux：5.2 到 5.11、6.1 到 6.9、7.0 到 7.3 <br/><br/>CentOS：5.2 到 5.11、6.1 到 6.9、7.0 到 7.3 <br/><br/>Ubuntu 14.04 LTS 服务器[（受支持的内核版本）](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS 服务器[（受支持的内核版本）](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)） <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>（不支持复制计算机从 SLES 11 SP3 升级到 SLES 11 SP4。 如果已将复制计算机从 SLES 11SP3 升级到 SLES 11 SP4，则需要禁用复制，并在升级后再次对计算机启用保护。） | [Azure 支持的](https://technet.microsoft.com/library/cc794868.aspx)任何来宾 OS


>[!IMPORTANT]
>（适用于复制到 Azure 的 VMware/物理服务器）
>
> 在 Red Hat Enterprise Linux Server 7+ 和 CentOS 7+ 服务器上，从 Azure Site Recovery 移动服务版本 9.8 开始支持内核版本 3.10.0-514。<br/><br/>
> 使用内核版本为 3.10.0-514，且移动服务版本低于版本 9.8 的客户需要先禁用复制，将移动服务的版本更新至版本 9.8，然后再重新启用复制。


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>VMware/物理服务器支持的 Ubuntu 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic 到 3.13.0-117-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic 到 3.13.0-121-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic 到 3.13.0-128-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-91-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic 到 4.4.0-81-generic、<br/>4.8.0-34-generic 到 4.8.0-56-generic、<br/>4.10.0-14-generic 到 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic 到 4.4.0-91-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-32-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Linux 上支持的文件系统和来宾存储配置（VMware/物理服务器）

运行 VMware 或物理服务器的 Linux 服务器支持以下文件系统和存储配置软件：
* 文件系统：ext3、ext4、ReiserFS（仅限 Suse Linux Enterprise Server）和 XFS
* 卷管理器：LVM2
* 多路径软件：设备映射程序

不支持半虚拟化存储设备（由半虚拟化驱动程序导出的设备）。<br/>
不支持多队列块 IO 设备。<br/>
不支持使用 HP CCISS 存储控制器的物理服务器。<br/>

>[!Note]
> 在 Linux 服务器中，下列目录（如果设置为单独的分区/文件系统）必须位于源服务器的同一磁盘（OS 磁盘）：   / (root)、/boot、/usr、/usr/local、/var 和 /etc<br/><br/>
> 移动服务从版本 9.10 开始支持 XFS 文件系统上的 XFSv5 功能，如元数据校验和等。 如果使用 XFSv5 功能，请确保运行的是移动服务版本 9.10 或更高版本。 可使用 xfs_info 实用工具来检查分区的 XFS 超级块。 如果将 ftyp 设置为 1，则使用 XFSv5 功能。
>


## <a name="support-for-network-configuration"></a>网络配置支持
下表汇总了使用 Azure Site Recovery 复制到 Azure 时多种部署方案中的网络配置支持。

### <a name="host-network-configuration"></a>主机网络配置

**配置** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）**
--- | --- | ---
NIC 组合 | 是<br/><br/>复制物理计算机时不支持| 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

### <a name="guest-vm-network-configuration"></a>来宾 VM 网络配置

**配置** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）**
--- | --- | ---
NIC 组合 | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
静态 IP (Windows) | 是 | 是
静态 IP (Linux) | 是 <br/><br/>虚拟机配置为在故障回复时使用 DHCP  | 否
多 NIC | 是 | 是

### <a name="failed-over-azure-vm-network-configuration"></a>故障转移 Azure VM 网络配置

**Azure 网络** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）**
--- | --- | ---
Express Route | 是 | 是
ILB | 是 | 是
ELB | 是 | 是
流量管理器 | 是 | 是
多 NIC | 是 | 是
保留 IP | 是 | 是
IPv4 | 是 | 是
保留源 IP | 是 | 是


## <a name="support-for-storage"></a>存储支持
下表汇总了使用 Azure Site Recovery 复制到 Azure 的多种部署方案中的存储配置支持。

### <a name="host-storage-configuration"></a>主机存储支持

**配置** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）**
--- | --- | --- | ---
NFS | VMware 支持<br/><br/> 物理服务器不支持 | 不适用
SMB 3.0 | 不适用 | 是
SAN (ISCSI) | 是 | 是
多路径 (MPIO)<br></br>测试使用对象：Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON | 是 | 是

### <a name="guest-or-physical-server-storage-configuration"></a>来宾或物理服务器存储配置

**配置** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）**
--- | --- | ---
VMDK | 是 | 不适用
VHD/VHDX | 不适用 | 是
第 2 代 VM | 不适用 | 是
EFI/UEFI| 否 | 是
共享群集磁盘 | 否 | 否
加密磁盘 | 否 | 否
NFS | 否 | 不适用
SMB 3.0 | 否 | 否
RDM | 是<br/><br/> 不适用于物理服务器 | 不适用
磁盘 > 1 TB | 是<br/><br/>最大 4095 GB | 是<br/><br/>最大 4095 GB
具有 4K 扇区大小的磁盘 | 是 | 是，第 1 代 VM 支持<br/><br/>第 2 代 VM 不支持。
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM 逻辑卷管理 | 是 | 是
存储空间 | 否 | 是
热添加/移除磁盘 | 否 | 否
排除磁盘 | 是 | 是
多路径 (MPIO) | 不适用 | 是

**Azure 存储** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）**
--- | --- | ---
LRS | 是 | 是
GRS | 是 | 是
RA-GRS | 是 | 是
冷存储 | 否 | 否
热存储| 否 | 否
静态加密 (SSE)| 是 | 是
高级存储 | 是 | 是
导入/导出服务 | 否 | 否


## <a name="support-for-azure-compute-configuration"></a>Azure 计算配置支持

**计算功能** | **VMware/物理服务器** | **Hyper-V（具有/不具有 Virtual Machine Manager）**
--- | --- | ---
可用性集 | 是 | 是
HUB | 是 | 是  
托管磁盘 | 是 | 是<br/><br/>当前不支持从使用托管磁盘的 Azure VM 故障回复到本地。

## <a name="failed-over-azure-vm-requirements"></a>故障转移 Azure VM 要求

可以部署站点恢复以复制运行受 Azure 支持的任何操作系统的虚拟机和物理服务器。 这包括大多数的 Windows 和 Linux 版本。 复制到 Azure 时，要复制的本地 VM 必须符合以下 Azure 要求。

**实体** | **要求** | **详细信息**
--- | --- | ---
**来宾操作系统** | 对于从 Hyper-V 到 Azure 的复制，Site Recovery 支持 [Azure 支持](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有操作系统。 <br/><br/> 对于 VMware 和物理服务器复制，请检查 Windows 和 Linux [先决条件](site-recovery-vmware-to-azure-classic.md) | 如果不支持，先决条件检查会失败。
**来宾操作系统体系结构** | 64 位 | 如果不支持，先决条件检查会失败
**操作系统磁盘大小** | 如果要**将 VMware VM 或物理服务器复制到 Azure**，则最大为 2048 GB。<br/><br/>对于 **Hyper-V 第 1 代** VM，最大为 2048 GB。<br/><br/>对于 **Hyper-V 第 2 代** VM，最大为 300 GB。  | 如果不支持，先决条件检查会失败
**操作系统磁盘计数** | 1 | 如果不支持，先决条件检查会失败。
**数据磁盘计数** | 如果将 **VMware VM 复制到 Azure**，则为 64 个或更少；如果将 **Hyper-V VM 复制到 Azure**，则为 16 个或更少 | 如果不支持，先决条件检查会失败
**数据磁盘 VHD 大小** | 最大 4095 GB | 如果不支持，先决条件检查会失败
**网络适配器** | 支持多个适配器 |
**共享 VHD** | 不支持 | 如果不支持，先决条件检查会失败
**FC 磁盘** | 不支持 | 如果不支持，先决条件检查会失败
**硬盘格式** | VHD <br/><br/> VHDX | 尽管 Azure 当前不支持 VHDX，但故障转移到 Azure 时，站点恢复会自动将 VHDX 转换为 VHD。 故障回复到本地时，虚拟机将继续使用 VHDX 格式。
**Bitlocker** | 不支持 | 保护虚拟机之前，必须先禁用 Bitlocker。
**VM 名称** | 介于 1 和 63 个字符之间。 限制为字母、数字和连字符。 VM 名称必须以字母或数字开始或结尾。 | 在 Site Recovery 中更新虚拟机属性中的值。
**VM 类型** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁盘类型为“基本”的第 2 代 VM（其中包括一个或两个格式化为 VHDX 的数据卷），并且支持的磁盘空间大小小于 300 GB。<br></br>不支持 Linux 第 2 代 VM。 [了解详细信息](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>恢复服务保管库操作支持

**操作** | **VMware/物理服务器** | **Hyper-V（无 Virtual Machine Manager）** | **Hyper-V（具有 Virtual Machine Manager）**
--- | --- | --- | ---
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否 | 否 | 否
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否 | 否 | 否


## <a name="support-for-provider-and-agent"></a>提供程序和代理支持

**Name** | **说明** | **最新版本** | **详细信息**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供程序** | 协调本地服务器与 Azure 之间的通信 <br/><br/> 如果没有 Virtual Machine Manager 服务器，则安装在本地 Virtual Machine Manager 服务器或 Hyper-V 服务器上 | 5.1.19（[可从门户获取](http://aka.ms/downloaddra)） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 统一安装（VMware 到 Azure）** | 协调本地 VMware 服务器与 Azure 之间的通信 <br/><br/> 在本地 VMware 服务器上安装 | 9.3.4246.1（可从门户获取） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**移动服务** | 协调本地 VMware 服务器/物理服务器和 Azure/辅助站点之间的复制<br/><br/> 在想要复制的 VMware VM 或物理服务器上安装  | N/A（可从门户获取） | 不适用
**Microsoft Azure 恢复服务 (MARS) 代理** | 协调 Hyper-V VM 与 Azure 之间的复制<br/><br/> 在本地 Hyper-V 服务器（包含或不包含 Virtual Machine Manager 服务器）上安装 | 最新代理（[可从门户获取](http://aka.ms/latestmarsagent)） |






## <a name="next-steps"></a>后续步骤
[检查先决条件](site-recovery-prereq.md)

