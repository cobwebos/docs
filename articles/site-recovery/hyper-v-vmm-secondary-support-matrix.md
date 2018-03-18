---
title: "使用 Azure Site Recovery 将 VMM 云中的 Hyper-V VM 复制到辅助站点时的支持矩阵 | Microsoft Docs"
description: "汇总了对使用 Azure Site Recovery 将 VMM 云中的 Hyper-V VM 复制到辅助站点的支持。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 767b0e76b73c082ddb75374f51700b85272f713e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-hyper-v-vms-to-a-secondary-site"></a>将 Hyper-V VM 复制到辅助站点时的支持矩阵

本文汇总了使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 System Center Virtual Machine Manager (VMM) 云中托管的 Hyper-V VM 复制到辅助站点时所支持的内容。 如果要将 Hyper-V VM 复制到 Azure，请查看[此支持矩阵](hyper-v-azure-support-matrix.md)。

> [!NOTE]
> Hyper-V 主机在 VMM 云中托管时，只能复制到辅助站点。

  

## <a name="host-servers"></a>主机服务器

**操作系统** | **详细信息**
--- | ---
Windows Server 2012 R2 | 服务器必须正在运行最新更新。
Windows Server 2016 |  目前不支持混合使用 Windows Server 2016 和 2012 R2 主机的 VMM 2016 云。<br/><br/> 当前不支持从 System Center 2012 R2 VMM 2012 R2 升级到 System Center 2016 的部署。


## <a name="replicated-vm-support"></a>复制的 VM 支持

下表总结了通过 Site Recovery 完成复制的计算机的操作系统支持。 支持的操作系统上可运行任何工作负荷。

**Windows 版本** | **Hyper-V（包含 VMM）**
--- | ---
Windows Server 2016 | Windows Server 2016 上的[Hyper-V 支持](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows)的所有来宾操作系统 
Windows Server 2012 R2 | Windows Server 2012 R2 上的[Hyper-V 支持](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29)的所有来宾操作系统

## <a name="linux-machine-storage"></a>Linux 计算机存储

仅有以下存储的 Linux 计算机可以复制：

- 文件系统（EXT3、ETX4、ReiserFS、XFS）。
- 多路径软件设备映射程序。
- 卷管理器 (LVM2)。
- 不支持使用 HP CCISS 控制器存储的物理服务器。
- ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。

## <a name="network-configuration---hostguest-vm"></a>网络配置 - 主机/来宾 VM

**配置** | **支持**  
--- | --- 
主机 - NIC 组合 | 是 
主机 - VLAN | 是 
主机 - IPv4 | 是 
主机 - IPv6 | 否 
来宾 VM - NIC 组合 | 否
来宾 VM - IPv4 | 是
来宾 VM - IPv6 | 否
来宾 - Windows/Linux - 静态 IP 地址 | 是
来宾 VM - 多 NIC | 是


## <a name="storage"></a>存储

### <a name="host-storage"></a>主机存储

**存储（主机）** | **支持**
--- | --- 
NFS | 不适用
SMB 3.0 |  是
SAN (ISCSI) | 是
多路径 (MPIO) | 是

### <a name="guest-or-physical-server-storage"></a>来宾或物理服务器存储

**配置** | **支持**
--- | --- | 
VMDK |  不适用
VHD/VHDX | 是（最多 16 个磁盘）
第 2 代 VM | 是
共享群集磁盘 | 否
加密磁盘 | 否
UEFI| 不适用
NFS | 否
SMB 3.0 | 否
RDM | 不适用
磁盘 > 1 TB | 是
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM | 是
存储空间 | 是
热添加/移除磁盘 | 否
排除磁盘 | 是
多路径 (MPIO) | 是

## <a name="vaults"></a>保管库

**Action** | **支持**
--- | --- 
跨资源组移动保管库（订阅内或跨订阅移动） |  否
跨资源组移动存储、网络和 Azure VM（订阅内或跨订阅移动） | 否

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery 提供程序

该提供程序协调 VMM 服务器之间的通信。 

**最新** | **更新**
--- | --- | --- | --- | ---
5.1.19（[可从门户获取](http://aka.ms/downloaddra)） | [最新功能和修复](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>后续步骤

[将 VMM 云中的 Hyper-V VM 复制到辅助站点](tutorial-vmm-to-vmm.md)

