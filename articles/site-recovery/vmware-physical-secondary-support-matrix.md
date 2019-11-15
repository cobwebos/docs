---
title: 支持使用 Azure Site Recovery 的 VMware/物理灾难恢复到辅助站点
description: 总结了对使用 Azure Site Recovery 将 VMware VM 或物理服务器灾难恢复到辅助站点的支持。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082191"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>将 VMware VM 和物理服务器灾难恢复到辅助站点时的支持矩阵

本文汇总了使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 VMware VM 或 Windows/Linux 物理服务器灾难恢复到辅助 VMware 站点时所支持的内容。

- 如果要将 VMware VM 或物理服务器复制到 Azure，请查看[此支持矩阵](vmware-physical-azure-support-matrix.md)。
- 如果要将 Hyper-V VM 复制到辅助站点，请查看[此支持矩阵](hyper-v-azure-support-matrix.md)。

> [!NOTE]
> 复制本地 VMware VM 和物理服务器的功能由 InMage Scout 提供。 InMage Scout 随附在 Azure Site Recovery 服务订阅中。

## <a name="end-of-support-announcement"></a>结束支持公告
在本地 VMware 或物理数据中心之间进行复制的 Site Recovery 方案将达到支持终止。

- 从8月2018开始，无法在恢复服务保管库中配置该方案，并且无法从保管库下载 InMage Scout 软件。 现有部署仍受支持。
- - 从 2020 年 12 月 31 日起，该方案将不受支持。
现有合作伙伴可以将新客户加入到该方案中，直到支持结束。
- 在 2018 年和 2019 年期间，将发布两个更新：

    - 更新 7：修复了网络配置和符合性问题，并提供 TLS 1.2 支持。
    - 更新 8：添加了对 Linux 操作系统 RHEL/CentOS 7.3/7.4/7.5 和 SUSE 12 的支持
    - 更新 8 之后，不会再发布进一步更新。 针对更新 8 中添加的操作系统，修补程序支持有限，会尽最大努力修复错误。

## <a name="host-servers"></a>主机服务器

**操作系统** | **详细信息**
--- | ---
vCenter 服务器 | vCenter 5.5、6.0 和 6.5<br/><br/> 如果运行 6.0 或 6.5，请注意，仅支持 5.5 功能。


## <a name="replicated-vm-support"></a>复制的 VM 支持

下表总结了通过 Site Recovery 完成复制的计算机的操作系统支持。 支持的操作系统上可运行任何工作负荷。

**操作系统** | **详细信息**
--- | ---
Windows Server | 64 位 Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、带 SP1（或更高版本）的 Windows Server 2008 R2。
Linux | Red Hat Enterprise Linux 6.7、6.8、6.9、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、6.8、6.9、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5、6.8（运行 Red Hat 兼容内核），或 Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3、11 SP4 


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
来宾 VM - Windows/Linux - 静态 IP 地址 | 是
来宾 VM - 多 NIC | 是


## <a name="storage"></a>存储

### <a name="host-storage"></a>主机存储

存储（主机） | **支持** 
--- | --- 
NFS | 是 
SMB 3.0 | 不适用 
SAN (ISCSI) | 是 
多路径 (MPIO) | 是 

### <a name="guest-or-physical-server-storage"></a>来宾或物理服务器存储

**配置** | **支持** 
--- | --- 
VMDK | 是 
VHD/VHDX | 不适用 
第 2 代 VM | 不适用 
共享群集磁盘 | 是 
加密磁盘 | 否 
UEFI| 是 
NFS | 否 
SMB 3.0 | 否 
RDM | 是 
磁盘 > 1 TB | 是 
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM | 是 
存储空间 | 否 
热添加/移除磁盘 | 是 
排除磁盘 | 是 
多路径 (MPIO) | 不适用 

## <a name="vaults"></a>保管库

**操作** | **支持** 
--- | --- 
跨资源组移动保管库（订阅内或跨订阅移动） | 否 
跨资源组移动存储、网络和 Azure VM（订阅内或跨订阅移动） | 否 

## <a name="mobility-service-and-updates"></a>移动服务和更新

移动服务可协调本地 VMware 服务器/物理服务器和辅助站点之间的复制。 如果设置了复制，应确保安装了最新版本的移动服务和其他组件。

| **更新** | **详细信息** |
| --- | --- |
|Scout 更新 | Scout 更新是累积更新。 <br/><br/> [了解和下载](vmware-physical-secondary-disaster-recovery.md#updates)最新的 Scout 更新 |
|组件更新 | Scout 更新包括所有组件的更新，包括 RX 服务器、配置服务器、进程和主目标服务器，vContinuum 服务器和要保护的源服务器。<br/><br/> [了解详细信息](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates)。|


## <a name="next-steps"></a>后续步骤

下载 [InMage Scout 用户指南](https://aka.ms/asr-scout-user-guide)

- [将 VMM 云中的 Hyper-V VM 复制到辅助站点](tutorial-vmm-to-vmm.md)
- [将 VMware VM 和物理服务器复制到辅助站点](tutorial-vmware-to-vmware.md)
