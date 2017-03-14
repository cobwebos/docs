---
title: "Azure Site Recovery 支持矩阵 | Microsoft 文档"
description: "汇总了 Azure Site Recovery 支持的操作系统和组件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b9d269a780e9a4c61263208f26f440b1121c682
ms.openlocfilehash: f437598b612a145c5dd8b46a1ba340d298a76981
ms.lasthandoff: 03/01/2017


---
# <a name="azure-site-recovery-support-matrix"></a>Azure Site Recovery 支持矩阵

本文汇总了 Azure Site Recovery 支持的操作系统和组件。 每篇相应部署文章中均提供有每种可用部署方案支持的组件和先决条件的列表，本文档是对这些内容的汇总。

## <a name="support-for-azure-replication-scenarios"></a>支持 Azure 复制方案

**部署** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
**Azure 门户** | 本地 VMware VM 到 Azure 存储，使用 Resource Manager 或经典存储和网络。<br/><br/> 故障转移到基于 Resource Manager 或经典模型的 VM。 | 本地 Hyper-V VM（不在 VMM 云中）到 Azure 存储，使用 Resource Manager 或经典存储和网络。<br/><br/> 故障转移到基于 Resource Manager 或经典模型的 VM。 | 本地 Hyper-V VM（在 VMM 云中）到 Azure 存储，使用 Resource Manager 或经典存储和网络。<br/><br/> 故障转移到基于 Resource Manager 或经典模型的 VM。
**经典门户** | 只能使用维护模式。 无法创建新的保管库。 | 仅限维护模式。 | 只能使用维护模式。
**PowerShell** | 目前不支持。 | 支持 | 支持


## <a name="support-for-secondary-site-replication-scenarios"></a>支持辅助站点复制方案

**部署** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
**Azure 门户** | 本地 VMware VM 到辅助 VMware 站点。<br/><br/> 下载帮助指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) InMage Scout 用户指南。 Azure 门户中未提供 | 不支持 | VMM 云中的本地 Hyper-V VM 到辅助 VMM 云<br/><br/> 仅限标准 Hyper-V 复制，不支持 SAN
**经典门户** | 只能使用维护模式。 无法创建新的保管库。 | 仅限维护模式。 | 只能使用维护模式。
**PowerShell** | 不支持。 | 不可用 | 支持



## <a name="support-for-virtualization-server-operating-systems"></a>虚拟化服务器操作系统支持

### <a name="host-servers-replicate-to-azure"></a>主机服务器（复制到 Azure）

**VMware VM/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | ---
vCenter 5.5 或 6.0（仅支持 5.5 功能） <br/><br/> vSphere 6.0、5.5 或 5.1（装有最新更新） | Windows Server 2016、带有最新更新的 Windows Server 2012 R2<br/><br/> 当前不支持混合使用主机（运行 Windows Server 2016 和 2012 R2）的 Hyper-V 站点。 | Windows Server 2016、带有最新更新的 Windows Server 2012 R2<br/><br/> Windows Server 2016 主机应由运行 System Center 2016 的 VMM 托管。<br/><br/> 目前不支持混合使用 Windows Server 2016 和 2012 R2 主机的 VMM 2016 云。

### <a name="host-servers-replicate-to-secondary-site"></a>主机服务器（复制到辅助站点）

**VMware VM/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
vCenter 5.5 或 6.0（仅支持 5.5 功能） <br/><br/> vSphere 6.0、5.5 或 5.1（装有最新更新） | Windows Server 2016、Windows Server 2012 R2 或 Windows Server 2012（含最新更新）。<br/><br/> Windows Server 2016 主机应由运行 System Center 2016 的 VMM 托管。<br/><br/> 目前不支持混合使用 Windows Server 2016 和更低版主机的 VMM 2016 云。


## <a name="support-for-replicated-machines"></a>复制的计算机支持

### <a name="machines-replicate-to-azure"></a>计算机（复制到 Azure）

虚拟机必须满足 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

**要求** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
复制内容 | Windows 或 Linux VM 上的任何工作负荷 | 任何工作负荷 | 任何工作负荷
主机 OS | 64 位 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 及其更高版本<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)） <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 所需的存储：文件系统（EXT3、ETX4、ReiserFS、XFS）；多路径软件设备映射器（多路径）；卷管理器：LVM2。 不支持使用 HP CCISS 控制器存储的物理服务器。 ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。 | [Azure 支持的](https://technet.microsoft.com/library/cc794868.aspx)任何来宾 OS | [Azure 支持的](https://technet.microsoft.com/library/cc794868.aspx)任何来宾 OS


### <a name="machines-replicate-to-secondary-site"></a>计算机（复制到辅助站点）

**要求** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
复制内容 | Windows 或 Linux VM 上的任何工作负荷 | 任何工作负荷 | 任何工作负荷
主机 OS | 64 位 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 及其更高版本<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)） <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 所需的存储：文件系统（EXT3、ETX4、ReiserFS、XFS）；多路径软件设备映射器（多路径）；卷管理器：LVM2。<br/><br/> 不支持使用 HP CCISS 控制器存储的物理服务器。 ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。 | Hyper-V 支持的任何来宾 OS](https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>提供程序和代理支持

**Name** | **说明** | **最新版本** | **详细信息**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供程序** | 协调本地服务器与 Azure/辅助站点之间的通信 <br/><br/> 在本地 VMM 服务器上安装，如果没有 VMM 服务器，则在 Hyper-V 服务器上安装 | 5.1.1700（可从门户获取） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 统一安装（VMware 到 Azure）** | 协调本地 VMware 服务器与 Azure 之间的通信 <br/><br/> 在本地 VMware 服务器上安装 | 9.3.4246.1（可从门户获取） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**移动服务** | 协调本地 VMware 服务器/物理服务器和 Azure/辅助站点之间的复制<br/><br/> 在想要复制的 VMware VM 或物理服务器上安装  | NA（可从门户获取） | 。
**Microsoft Azure 恢复服务 (MARS) 代理** | 协调 Hyper-V VM 与 Azure 之间的复制<br/><br/> 在本地 Hyper-V 服务器（包含或不包含 VMM 服务器）上安装 | |

## <a name="support-for-networking"></a>网络支持

### <a name="networking-replicate-to-azure"></a>网络（复制到 Azure）

**主机网络** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
NIC 组合 | 是<br/><br/>在物理计算机中不受支持| 是 | 是
VLAN | 是 | 是 | 是
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否

**来宾 VM 网络** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
NIC 组合 | 否 | 否 | 否
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否
静态 IP (Windows) | 是 | 是 | 是
静态 IP (Linux) | 否 | 否 | 否
多 NIC | 是 | 是 | 是

**Azure 网络** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
Express Route | 是 | 是 | 是
ILB | 是 | 是 | 是
ELB | 是 |  |
流量管理器 | 是 | 是 | 是
多 NIC | 是 | 是 | 是
保留 IP | 是 | 是 | 是
IPv4 | 是 | 是 | 是
保留源 IP | 是 | 是 | 是

### <a name="networking-replicate-to-secondary-site"></a>网络（复制到辅助站点）

**主机网络** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NIC 组合 | 是 | 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

**来宾 VM 网络** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NIC 组合 | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
静态 IP (Windows) | 是 | 是
静态 IP (Linux) | 是 | 是
多 NIC | 是 | 是


## <a name="support-for-storage"></a>存储支持

### <a name="storage-replicate-to-azure"></a>存储（复制到 Azure）

**存储（主机）** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
NFS | VMware 支持<br/><br/> 物理服务器不支持 | 不可用 | 不可用
SMB 3.0 | 不可用 | 是 | 是
SAN (ISCSI) | 是 | 是 | 是
多路径 (MPIO) | VMware 支持<br/><br/> 物理服务器不支持 | 是 | 是

**存储（来宾 VM/物理服务器）** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
VMDK | 是 | 不可用 | 不可用
VHD/VHDX | 不可用 | 是 | 是
第 2 代 VM | 不可用 | 是 | 是
共享群集磁盘 | VMware 支持<br/><br/> 物理服务器不支持 | 否 | 否
加密磁盘 | 否 | 否 | 否
EFI/UEFI| 否 | 是 | 是
NFS | 否 | 否 | 否
SMB 3.0 | 否 | 否 | 否
RDM | 是<br/><br/> 物理服务器不支持 | 不可用 | 不可用
磁盘 > 1 TB | 否 | 否 | 否
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM | 是 | 是 | 是
存储空间 | 否 | 是 | 是
热添加/移除磁盘 | 否 | 否 | 否
排除磁盘 | 是 | 是 | 是
多路径 (MPIO) | 不可用 | 是 | 是

**Azure 存储空间** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
LRS | 是 | 是 | 是
GRS（仅适用于标准存储） | 是 | 是 | 是
冷存储 | 否 | 否 | 否
热存储| 否 | 否 | 否
静态加密 | 是 | 是 | 是
高级存储 | 是 | 是 | 是
导入/导出服务 | 否 | 否 | 否


### <a name="storage-replicate-to-secondary-site"></a>存储（复制到辅助站点）

**存储（主机）** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NFS | 是 | 不可用
SMB 3.0 | 不可用 | 是
SAN (ISCSI) | 是 | 是
多路径 (MPIO) | 是 | 是

**存储（来宾 VM/物理服务器）** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
VMDK | 是 | 不可用
VHD/VHDX | 不可用 | 是（最多 16 个磁盘）
第 2 代 VM | 不可用 | 是
共享群集磁盘 | 是  | 否
加密磁盘 | 否 | 否
UEFI| 否 | 不可用
NFS | 否 | 否
SMB 3.0 | 否 | 否
RDM | 是 | 不可用
磁盘 > 1 TB | 否 | 是
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM | 是 | 是
存储空间 | 否 | 是
热添加/移除磁盘 | 否 | 否
排除磁盘 | 否 | 否
多路径 (MPIO) | 不可用 | 是

## <a name="support-for-recovery-services-vault-actions"></a>恢复服务保管库操作支持

### <a name="vaults-replicate-to-azure"></a>保管库（复制到 Azure）

**操作** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否 | 否 | 否
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否 | 否 | 否

### <a name="vaults-replicate-to-secondary-site"></a>保管库（复制到辅助站点）

**操作** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否 | 否
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否 | 否


## <a name="support-for-azure-compute-replicate-to-azure"></a>Azure 计算支持（复制到 Azure）

**计算功能** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
共享磁盘来宾群集 | 否 | 否 | 否
可用性集 | 否 | 否 | 否
HUB | 是 | 是 | 是

## <a name="support-for-azure-vms"></a>支持 Azure VM

可以部署 Site Recovery 以复制运行受 Azure 支持的任何操作系统的虚拟机和物理服务器。 这包括大多数的 Windows 和 Linux 版本。 要复制的本地 VM 必须符合 Azure 要求。

**功能** | **要求** | **详细信息**
--- | --- | ---
**Hyper-V 主机** | 应运行 Windows Server 2012 R2 或更高版本 | 如果操作系统不受支持，先决条件检查将会失败
**VMware 虚拟机监控程序** | 支持的操作系统 | [检查要求](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**来宾操作系统** | 对于从 Hyper-V 到 Azure 的复制，Site Recovery 支持 [Azure 支持](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有操作系统。 <br/><br/> 对于 VMware 和物理服务器复制，请检查 Windows 和 Linux [先决条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 如果不支持，先决条件检查将会失败。
**来宾操作系统体系结构** | 64 位 | 如果不支持，先决条件检查将会失败
**操作系统磁盘大小** | 最大 1023 GB | 如果不支持，先决条件检查将会失败
**操作系统磁盘计数** | 1 | 如果不支持，先决条件检查将会失败。
**数据磁盘计数** | 如果将 **VMware VM 复制到 Azure**，则为 64 个或更少；如果将 **Hyper-V VM 复制到 Azure**，则为 16 个或更少 | 如果不支持，先决条件检查将会失败
**数据磁盘 VHD 大小** | 最大 1023 GB | 如果不支持，先决条件检查将会失败
**网络适配器** | 支持多个适配器 |
**静态 IP 地址** | 支持 | 如果主虚拟机使用的是静态 IP 地址，则你可为要在 Azure 中创建的虚拟机指定静态 IP 地址。<br/><br/> **在 Hyper-v 上运行的 Linux VM** 不支持静态 IP 地址。
**iSCSI 磁盘** | 不支持 | 如果不支持，先决条件检查将会失败
**共享 VHD** | 不支持 | 如果不支持，先决条件检查将会失败
**FC 磁盘** | 不支持 | 如果不支持，先决条件检查将会失败
**硬盘格式** | VHD <br/><br/> VHDX | 尽管 Azure 当前不支持 VHDX，但当你故障转移到 Azure 时，站点恢复会自动将 VHDX 转换为 VHD。 当你故障回复到本地时，虚拟机将继续使用 VHDX 格式。
**Bitlocker** | 不支持 | 保护虚拟机之前，必须先禁用 Bitlocker。
**VM 名称** | 介于 1 和 63 个字符之间。 限制为字母、数字和连字符。 应以字母或数字开头和结尾 | 在 Site Recovery 中更新虚拟机属性中的值
**VM 类型** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁盘类型为“基本”的第 2 代 VM，其中包括一个或两个格式化为 VHDX 的数据卷，并且支持的大小小于 300 GB。<br/><br/>。 不支持 Linux 第 2 代 VM。 [了解详细信息](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |








## <a name="next-steps"></a>后续步骤
检查[先决条件](site-recovery-prereq.md)

