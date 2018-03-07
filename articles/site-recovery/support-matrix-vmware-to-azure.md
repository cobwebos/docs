---
title: "将 VMware VM 和物理主机复制到 Azure 时的 Azure Site Recovery 支持矩阵| Microsoft Docs"
description: "汇总了使用 Azure Site Recovery 将 VMware VM 复制到 Azure 时支持的操作系统和组件。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: raynew
ms.openlocfilehash: 31754cd765c90b9e36d16dc766b0a3546e6fd93e
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>VMware 和物理服务器到 Azure 的复制支持矩阵


本文汇总了使用 [Azure Site Recovery](site-recovery-overview.md) 服务执行从 VMware VM 到 Azure 的灾难恢复时支持的组件和设置。


## <a name="supported-scenarios"></a>支持的方案

**方案** | **详细信息**
--- | ---
**VMware VM** | 可以针对本地 VMware VM 执行到 Azure 的灾难恢复。 可以在 Azure 门户中部署此方案，也可使用 PowerShell 进行部署。
**物理服务器** | 可以针对本地 Windows/Linux 物理服务器执行到 Azure 的灾难恢复。 可以在 Azure 门户中部署此方案。

## <a name="on-premises-virtualization-servers"></a>本地虚拟化服务器

**服务器** | **要求** | **详细信息**
--- | --- | ---
**VMware** | vCenter Server 6.5、6.0、5.5 或 vSphere 6.5、6.0、5.5 | 建议使用 vCenter 服务器。
**物理服务器** | 不适用


## <a name="replicated-machines"></a>复制的计算机

下表汇总了复制支持的计算机。 Site Recovery 支持复制在具有受支持操作系统上运行的任何工作负荷。

**组件** | **详细信息**
--- | ---
计算机配置 | 复制到 Azure 的计算机必须满足 [Azure 要求](#failed-over-azure-vm-requirements)。
计算机操作系统 (Windows) | 64 位 Windows Server 2016（服务器核心，带桌面体验的服务器）、Windows Server 2012 R2、Windows Server 2012、带 SP1（或更高版本）的 Windows Server 2008 R2
计算机操作系统 (Linux) | Red Hat Enterprise Linux：5.2 到 5.11、6.1 到 6.9、7.0 到 7.4 <br/><br/>CentOS：5.2 到 5.11、6.1 到 6.9、7.0 到 7.4 <br/><br/>Ubuntu 14.04 LTS 服务器[（受支持的内核版本）](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS 服务器[（受支持的内核版本）](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)） <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>（不支持复制计算机从 SLES 11 SP3 升级到 SLES 11 SP4。 如果将复制计算机从 SLES 11SP3 升级到 SLES 11 SP4，则需要禁用复制，并在升级后再次对计算机启用保护。）

>[!NOTE]
>
> - 在 Linux 分发中，仅支持属于分发的次要版本发布/更新的常用内核。
>
> - 不支持在由 Site Recovery 提供保护的 VMware 虚拟机或物理服务器上跨 Linux 分发的主版本进行升级。 跨主版本（例如 CentOS 6.\* 到 CentOS 7.\*）升级操作系统时，将禁用计算机的复制、升级计算机上的操作系统，然后再次启用复制。
>

### <a name="ubuntu-kernel-versions"></a>Ubuntu 内核版本


**支持的版本** | **Azure Site Recovery 移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic 到 3.13.0-121-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic 到 3.13.0-128-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic 到 3.13.0-132-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic 到 3.13.0-137-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic 到 4.4.0-81-generic、<br/>4.8.0-34-generic 到 4.8.0-56-generic、<br/>4.10.0-14-generic 到 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic 到 4.4.0-91-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic 到 4.4.0-96-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic 到 4.4.0-104-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Linux 文件系统/来宾存储配置

**组件** | **支持**
--- | ---
文件系统 | ext3、ext4、ReiserFS（仅限 Suse Linux Enterprise Server）和 XFS
卷管理器 | LVM2
多路径软件 | 设备映射器
半虚拟化存储设备 | 不支持半虚拟化驱动程序导出的设备。
多队列块 IO 设备 | 不支持。
具有 HP CCISS 存储控制器的物理服务器 | 不支持。
目录 | 下列目录（如果设置为单独的分区/文件系统）必须位于源服务器上的同一 OS 磁盘：/ (root)、/boot、/usr、/usr/local、/var 和 /etc。 </br></br> 如果 / (root) 卷是一个 LVM 卷，则 /boot 必须位于同一磁盘上的一个单独分区中且不能是 LVM 卷。<br/><br/>
XFSv5 | 在版本 9.10 和更高版本中，移动服务支持 XFS 文件系统上的 XFSv5 功能，例如元数据校验和。 可使用 xfs_info 实用工具来检查分区的 XFS 超级块。 如果 ftyp 设置为 1，则表示正在使用 XFSv5 功能。



## <a name="network"></a>网络

**组件** | **支持**
--- | ---
主机网络 NIC 组合 | 对于 VMware VM，受支持。 <br/><br/>对于物理计算机复制，不支持。
主机网络 VLAN | 是
主机网络 IPv4 | 是
主机网络 IPv6 | 否
来宾/服务器网络 NIC 组合 | 否
来宾/服务器网络 IPv4 | 是
来宾/服务器网络 IPv6 | 否
来宾/服务器网络静态 IP (Windows) | 是
来宾/服务器网络静态 IP (Linux) | 是 <br/><br/>VM 配置为在故障回复时使用 DHCP。  
来宾/服务器网络多个 NIC | 是


## <a name="azure-vm-network-after-failover"></a>Azure VM 网络（故障转移后）

**组件** | **支持**
--- | ---
Azure ExpressRoute | 是
ILB | 是
ELB | 是
Azure 流量管理器 | 是
多 NIC | 是
保留 IP 地址 | 是
IPv4 | 是
保留源 IP 地址 | 是
Azure 虚拟网络服务终结点<br/><br/> （Azure 存储防火墙和虚拟网络） | 否

## <a name="storage"></a>存储
**组件** | **支持**
--- | ---
主机 NFS | VMware 支持<br/><br/> 物理服务器不支持。
主机 SAN (ISCSI) | 是
主机多路径 (MPIO) | 是，针对以下项进行了测试：Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON
来宾/服务器 VMDK | 是
来宾/服务器 EFI/UEFI| 部分（只有 Windows Server 2012 及更高版本的 VMware 虚拟机可迁移到 Azure） </br></br> 请参阅表末尾的说明。
来宾/服务器共享群集磁盘 | 否
来宾/服务器加密磁盘 | 否
来宾/服务器 NFS | 否
来宾/服务器 SMB 3.0 | 否
来宾/服务器 RDM | 是<br/><br/> 不适用于物理服务器
> 1 TB 的来宾/服务器磁盘 | 是<br/><br/>最大 4,095 GB
逻辑和物理扇区大小均为 4K 的来宾/服务器磁盘 | 是
逻辑扇区大小为 4K 且物理扇区大小为 512 字节 的来宾/服务器磁盘 | 是
具有 > 4 TB 的条带化磁盘的来宾/服务器卷 <br><br/>LVM 逻辑卷管理 | 是
来宾/服务器 - 存储空间 | 否
来宾/服务器热添加/删除磁盘 | 否
来宾/服务器 - 排除磁盘 | 是
来宾/服务器多路径 (MPIO) | 不适用

> [!NOTE]
> 运行 Windows Server 2012 或更高版本的 UEFI 引导 VMware 虚拟机可迁移到 Azure。 存在以下限制：

> - 仅支持到 Azure 的迁移。 不支持故障回复到本地 VMware 站点。
> - 服务器在 OS 磁盘上不应具有四个以上分区。
> - 需要移动服务 9.13 版或更高版本。
> - 不适用于物理服务器。

## <a name="azure-storage"></a>Azure 存储

**组件** | **支持**
--- | ---
LRS | 是
GRS | 是
RA-GRS | 是
冷存储 | 否
热存储| 否
块 Blob | 否
静态加密 (SSE)| 是
高级存储 | 是
导入/导出服务 | 否
虚拟网络服务终结点<br/><br/> 在目标存储/缓存存储帐户（用来存储复制数据）上配置的存储防火墙和虚拟网络 | 否
常规用途 V2 存储帐户（包括热存储层和冷存储层） | 否

## <a name="azure-compute"></a>Azure 计算

**功能** | **支持**
--- | ---
可用性集 | 是
HUB | 是   
托管磁盘 | 是

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的本地 VM 必须满足此表中汇总的 Azure VM 要求。

**组件** | **要求** | **详细信息**
--- | --- | ---
**来宾操作系统** | 验证[支持的操作系统](#replicated machines)。 | 如果不支持，先决条件检查会失败。
**来宾操作系统体系结构** | 64 位 | 如果不支持，先决条件检查会失败。
**操作系统磁盘大小** | 最大 2048 GB | 如果不支持，先决条件检查会失败。
**操作系统磁盘计数** | 1 | 如果不支持，先决条件检查会失败。
**数据磁盘计数** | 如果*将 VMware VM 复制到 Azure*，则计数为 64 或更少。 如果*将 Hyper-V VM 复制到 Azure*，则计数为 16 或更少。 | 如果不支持，先决条件检查会失败。
**数据磁盘 VHD 大小** | 最大 4,095 GB | 如果不支持，先决条件检查会失败。
**网络适配器** | 支持多个适配器。 |
**共享 VHD** | 不支持。 | 如果不支持，先决条件检查会失败。
**FC 磁盘** | 不支持。 | 如果不支持，先决条件检查会失败。
**硬盘格式** | VHD <br/><br/> VHDX | 尽管 Azure 当前不支持 VHDX，但故障转移到 Azure 时，站点恢复会自动将 VHDX 转换为 VHD。 故障回复到本地时，虚拟机将继续使用 VHDX 格式。
**BitLocker** | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
**VM 名称** | 1 到 63 个字符。<br/><br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 | 请在 Site Recovery 中的计算机属性中更新该值。
**VM 类型** | 第 1 代。<br/><br/> 第 2 代 - Windows。 | OS 磁盘类型为“基本”的第 2 代 VM（其中包括一个或两个格式化为 VHDX 的数据卷），并且支持的磁盘空间大小小于 300 GB。<br></br>不支持 Linux 第 2 代 VM。 若要了解详细信息，请参阅[到 Azure 的灾难恢复已增强](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。|

## <a name="vault-tasks"></a>保管库任务

**Action** | **支持**
--- | ---
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否


## <a name="mobility-service"></a>移动服务

**Name** | **说明** | **最新版本** | **详细信息**
--- | --- | --- | --- | ---
**Azure Site Recovery 统一安装程序** | 协调本地 VMware 服务器与 Azure 之间的通信 <br/><br/> 在本地 VMware 服务器上安装 | 9.12.4653.1（可从门户获取） | [最新功能和修复](https://aka.ms/latest_asr_updates)
**移动服务** | 协调本地 VMware 服务器/物理服务器和 Azure/辅助站点之间的复制<br/><br/> 在想要复制的 VMware VM 或物理服务器上安装 | 9.12.4653.1（可从门户获取） | [最新功能和修复](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>后续步骤
[了解如何](tutorial-prepare-azure.md)为 VMware VM 的灾难恢复准备 Azure。
