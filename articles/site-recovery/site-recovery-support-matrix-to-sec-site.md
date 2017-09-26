---
title: "用于使用 Azure Site Recovery 复制到辅助站点的支持矩阵 | Microsoft Docs"
description: "汇总了 Azure Site Recovery 支持的操作系统和组件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/24/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: db7ee5251f2e2016081e55ca4b295e284c8b08cf
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017

---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>用于使用 Azure Site Recovery 复制到辅助站点的支持矩阵

本文汇总了使用 Azure Site Recovery 复制到辅助本地站点时所支持的内容。

## <a name="deployment-options"></a>部署选项

**部署** | **VMware/物理服务器** | **Hyper-V（使用/不使用 SCVMM）**
--- | --- | --- | ---
**Azure 门户** | 本地 VMware VM 到辅助 VMware 站点。<br/><br/> 下载 [InMage Scout 用户指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)（Azure 门户中未提供）。 | 将 VMM 云中的本地 Hyper-V VM 复制到辅助 VMM 云。<br></br> 如果没有 VMM，则不支持  <br/><br/> 仅限标准 Hyper-V 副本。 不支持 SAN。
**经典门户** | 只能使用维护模式。 无法创建新的保管库。 | 仅限维护模式<br></br> 支持 SCVMM
**PowerShell** | 不支持 | 支持<br></br> 支持 SCVMM

## <a name="on-premises-servers"></a>本地服务器

### <a name="virtualization-servers"></a>虚拟化服务器

**部署** | **支持**
--- | ---
**VMware VM/物理服务器** | vSphere 6.0、5.5 或 5.1（具有最新更新）
**Hyper-V（包含 VMM）** | VMM 2016 和 VMM 2012 R2

  >[!Note]
  > 目前不支持混合使用 Windows Server 2016 和 2012 R2 主机的 VMM 2016 云。

### <a name="host-servers"></a>主机服务器

**部署** | **支持**
--- | ---
**VMware VM/物理服务器** | vCenter 5.5 或 6.0（仅支持 5.5 功能）
**Hyper-V（不包含 VMM）** | 该配置不支持复制到辅助站点
**Hyper-V（包含 VMM）** | Windows Server 2016 和带最新更新的 Windows Server 2012 R2。<br/><br/> Windows Server 2016 主机应由 VMM 2016 托管。

## <a name="support-for-replicated-machine-os-versions"></a>已复制的计算机操作系统版本支持
下表汇总了使用 Azure Site Recovery 时涉及的多种部署方案中的操作系统支持。 该支持适用于在所提及的 OS 上运行的所有工作负荷。

**VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
64 位 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 及其更高版本<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4/6.5（运行 Red Hat 兼容内核），或 Unbreakable Enterprise Kernel Release 3 (UEK3)） <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Hyper-V 支持](https://technet.microsoft.com/library/mt126277.aspx)的所有来宾操作系统

>[!Note]
>只能复制具有以下存储的 Linux 计算机：文件系统（EXT3、ETX4、ReiserFS、XFS）；多路径软件设备映射器；卷管理器 (LVM2)。
>不支持使用 HP CCISS 控制器存储的物理服务器。
>ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。

## <a name="network-configuration"></a>网络配置

### <a name="hosts"></a>主机

**配置** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NIC 组合 | 是 | 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

### <a name="guest-vms"></a>来宾 VM

**配置** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NIC 组合 | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
静态 IP (Windows) | 是 | 是
静态 IP (Linux) | 是 | 是
多 NIC | 是 | 是


## <a name="storage"></a>存储

### <a name="host-storage"></a>主机存储

**存储（主机）** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NFS | 是 | 不适用
SMB 3.0 | 不适用 | 是
SAN (ISCSI) | 是 | 是
多路径 (MPIO) | 是 | 是

### <a name="guest-or-physical-server-storage"></a>来宾或物理服务器存储

**配置** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
VMDK | 是 | 不适用
VHD/VHDX | 不适用 | 是（最多 16 个磁盘）
第 2 代 VM | 不适用 | 是
共享群集磁盘 | 是  | 否
加密磁盘 | 否 | 否
UEFI| 否 | 不适用
NFS | 否 | 否
SMB 3.0 | 否 | 否
RDM | 是 | 不适用
磁盘 > 1 TB | 否 | 是
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM | 是 | 是
存储空间 | 否 | 是
热添加/移除磁盘 | 否 | 否
排除磁盘 | 否 | 是
多路径 (MPIO) | 不适用 | 是

## <a name="vaults"></a>保管库

**操作** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
跨资源组移动保管库（订阅内或跨订阅移动） | 否 | 否
跨资源组移动存储、网络和 Azure VM（订阅内或跨订阅移动） | 否 | 否

## <a name="provider-and-agent"></a>提供程序和代理

**Name** | **说明** | **最新版本** | **详细信息**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供程序** | 协调本地服务器与 Azure 之间的通信 <br/><br/> 在本地 VMM 服务器上安装，如果没有 VMM 服务器，则在 Hyper-V 服务器上安装 | 5.1.19（[可从门户获取](http://aka.ms/downloaddra)） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**移动服务** | 协调本地 VMware 服务器/物理服务器和辅助站点之间的复制<br/><br/> 在要复制的 VMware VM 或物理服务器上安装  | N/A（可从门户获取） | 不适用


## <a name="next-steps"></a>后续步骤

- [将 VMM 云中的 Hyper-V VM 复制到辅助站点](site-recovery-vmm-to-vmm.md)
- [将 VMware VM 和物理服务器复制到辅助站点](site-recovery-vmware-to-vmware.md)

