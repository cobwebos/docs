---
title: "用于复制到辅助站点的 Azure Site Recovery 支持矩阵 | Microsoft Docs"
description: "汇总了 Azure Site Recovery 支持的操作系统和组件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 0a2c3bec5fc6fb44b4baddc393d6f9387bbb5b94


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-customer-owned-secondary-site"></a>用于复制到客户拥有的辅助站点的 Azure Site Recovery 支持矩阵

> [!div class="op_single_selector"]
> * [复制到 Azure](site-recovery-support-matrix-to-azure.md)
> * [复制到客户拥有的辅助站点](site-recovery-support-matrix-to-sec-site.md)

[此处](site-recovery-best-practices.md)及下文介绍了 Azure Site Recovery 的先决条件列表，此列表概括了复制和恢复到客户拥有的辅助站点时，Azure Site Recovery 支持的配置和组件。

## <a name="support-for-deployment-options"></a>部署选项支持

**部署** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
**Azure 门户** | 本地 VMware VM 到辅助 VMware 站点。<br/><br/> 下载帮助指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) InMage Scout 用户指南。 Azure 门户中未提供 | 不支持 | VMM 云中的本地 Hyper-V VM 到辅助 VMM 云<br/><br/> 仅限标准 Hyper-V 复制，不支持 SAN
**经典门户** | 只能使用维护模式。 无法创建新保管库。 | 不支持 | 只能使用维护模式。
**PowerShell** | 不支持。 | 不支持 | 支持



## <a name="support-for-datacenter-management-servers"></a>数据中心管理服务器支持

### <a name="virtualization-management-entities"></a>虚拟化管理实体

**部署** | **支持**
--- | ---
**VMware VM/物理服务器** | vSphere 6.0、5.5 或 5.1（具有最新更新）
**Hyper-V（包含 VMM）** | SCVMM 2016 和 SCVMM 2012 R2

  >[!Note]
  > 目前不支持混合使用 Windows Server 2016 和 2012 R2 主机的 SCVMM 2016 云。

### <a name="host-servers"></a>主机服务器

**部署** | **支持**
--- | ---
**VMware VM/物理服务器** | vCenter 5.5 或 6.0（仅支持 5.5 功能）
**Hyper-V（不包含 VMM）** | 非复制到辅助站点支持的配置
**包含 VMM 的 Hyper-V** | Windows Server 2016、带有最新更新的 Windows Server 2012 R2<br/><br/> Windows Server 2016 主机应由 SCVMM 2016 托管

## <a name="support-for-replicated-machine-machine-os-versions"></a>已复制的计算机操作系统版本支持
下表汇总了使用 Azure Site Recovery 时多种部署方案中的操作系统支持。 该支持适用于在所提及的操作系统上运行的**任何工作负荷**。

**VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
64 位 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 及其更高版本<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)） <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Hyper-V 支持](https://technet.microsoft.com/library/mt126277.aspx)的任何来宾 OS

>[!Note]
>**Linux 版本的存储支持** 文件系统（EXT3、ETX4、ReiserFS、XFS）多路径软件设备映射器（多路径）卷管理器：具有 HP CCISS 控制器存储的 (LVM2) 物理服务器**不**受支持。
>ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。

## <a name="support-for-network"></a>网络支持
下表汇总了使用 Azure Site Recovery 复制到 Azure 时多种部署方案中的网络配置支持。

### <a name="host-network-configuration"></a>主机网络配置

**配置** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NIC 组合 | 是 | 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

### <a name="guest-vm-network-configuration"></a>来宾 VM 网络配置

**配置** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NIC 组合 | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
静态 IP (Windows) | 是 | 是
静态 IP (Linux) | 是 | 是
多 NIC | 是 | 是


## <a name="support-for-storage"></a>存储支持
下表汇总了使用 Azure Site Recovery 复制到 Azure 时多种部署方案中的存储配置支持。

### <a name="host-storage-configuration"></a>主机存储支持

**存储（主机）** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
NFS | 是 | 不可用
SMB 3.0 | 不可用 | 是
SAN (ISCSI) | 是 | 是
多路径 (MPIO) | 是 | 是

### <a name="guest-physical-server-storage-configuration"></a>来宾/物理服务器存储配置

**配置** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
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

**操作** | **VMware/物理服务器** | **Hyper-V（包含 VMM）**
--- | --- | ---
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否 | 否
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否 | 否

## <a name="support-for-provider-and-agent"></a>提供程序和代理支持

**Name** | **说明** | **最新版本** | **详细信息**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供程序** | 协调本地服务器与 Azure 之间的通信 <br/><br/> 在本地 VMM 服务器上安装，如果没有 VMM 服务器，则在 Hyper-V 服务器上安装 | 5.1.19（[可从门户获取](http://aka.ms/downloaddra)） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**移动服务** | 协调本地 VMware 服务器/物理服务器和辅助站点之间的复制<br/><br/> 在想要复制的 VMware VM 或物理服务器上安装  | NA（可从门户获取） | 不可用


## <a name="next-steps"></a>后续步骤
[准备部署](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


