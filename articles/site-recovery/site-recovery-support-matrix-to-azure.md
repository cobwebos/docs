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
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 3b9912ac45627f799ef3ca20eccf8d11f9ae52f0


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>用于复制到 Azure 的 Azure Site Recovery 支持矩阵

> [!div class="op_single_selector"]
> * [复制到 Azure](site-recovery-support-matrix-to-azure.md)
> * [复制到客户拥有的辅助站点](site-recovery-support-matrix-to-sec-site.md)


[此处](site-recovery-best-practices.md)及下文介绍了 Azure Site Recovery 的先决条件列表，此列表概括了复制和恢复到 Azure 时 Azure Site Recovery 支持的配置和组件。


## <a name="support-for-deployment-options"></a>部署选项支持

**部署** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
**Azure 门户** | 本地 VMware VM 到 Azure 存储，使用 Resource Manager 或经典存储和网络。<br/><br/> 故障转移到基于 Resource Manager 或经典模型的 VM。 | 本地 Hyper-V VM（不在 VMM 云中）到 Azure 存储，使用 Resource Manager 或经典存储和网络。<br/><br/> 故障转移到基于 Resource Manager 或经典模型的 VM。 | 本地 Hyper-V VM（在 VMM 云中）到 Azure 存储，使用 Resource Manager 或经典存储和网络。<br/><br/> 故障转移到基于 Resource Manager 或经典模型的 VM。
**经典门户** | 只能使用维护模式。 无法创建新的保管库。 | 仅限维护模式。 | 只能使用维护模式。
**PowerShell** | 目前不支持。 | 支持 | 支持


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
**Hyper-V（不包含 VMM）** | Windows Server 2016、带有最新更新的 Windows Server 2012 R2
**包含 VMM 的 Hyper-V** | Windows Server 2016、带有最新更新的 Windows Server 2012 R2<br/><br/> Windows Server 2016 主机应由 SCVMM 2016 托管

  >[!Note]
  >当前不支持混合使用主机（运行 Windows Server 2016 和 2012 R2）的 Hyper-V 站点。

## <a name="support-for-replicated-machine-os-versions"></a>已复制的计算机操作系统版本支持

复制到 Azure 时，受保护的虚拟机必须符合 [Azure 要求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。
下表汇总了使用 Azure Site Recovery 时多种部署方案中的已复制操作系统支持。 该支持适用于在所提及的操作系统上运行的**任何工作负荷**。

 **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | ---
64 位 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 及其更高版本<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)） <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Azure 支持的](https://technet.microsoft.com/library/cc794868.aspx)任何来宾 OS | [Azure 支持的](https://technet.microsoft.com/library/cc794868.aspx)任何来宾 OS


>[!Note]
>**Linux 版本的存储支持** 文件系统（EXT3、ETX4、ReiserFS、XFS）多路径软件设备映射器（多路径）卷管理器：具有 HP CCISS 控制器存储的 (LVM2) 物理服务器**不**受支持。
>ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。

## <a name="support-for-network"></a>网络支持
下表汇总了使用 Azure Site Recovery 复制到 Azure 时多种部署方案中的网络配置支持。

### <a name="host-network-configuration"></a>主机网络配置

**配置** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
NIC 组合 | 是<br/><br/>在物理计算机中不受支持| 是 | 是
VLAN | 是 | 是 | 是
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否

### <a name="guest-vm-network-configuration"></a>来宾 VM 网络配置

**配置** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
NIC 组合 | 否 | 否 | 否
IPv4 | 是 | 是 | 是
IPv6 | 否 | 否 | 否
静态 IP (Windows) | 是 | 是 | 是
静态 IP (Linux) | 否 | 否 | 否
多 NIC | 是 | 是 | 是

### <a name="failed-over-azure-vm-network-configuration"></a>故障转移 Azure VM 网络配置

**Azure 网络** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
Express Route | 是 | 是 | 是
ILB | 是 | 是 | 是
ELB | 是 | 是 | 是
流量管理器 | 是 | 是 | 是
多 NIC | 是 | 是 | 是
保留 IP | 是 | 是 | 是
IPv4 | 是 | 是 | 是
保留源 IP | 是 | 是 | 是


## <a name="support-for-storage"></a>存储支持
下表汇总了使用 Azure Site Recovery 复制到 Azure 时多种部署方案中的存储配置支持。

### <a name="host-storage-configuration"></a>主机存储支持

**配置** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
NFS | VMware 支持<br/><br/> 物理服务器不支持 | 不可用 | 不可用
SMB 3.0 | 不可用 | 是 | 是
SAN (ISCSI) | 是 | 是 | 是
多路径 (MPIO)<br></br>测试使用对象：Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON | VMware 支持<br/><br/> | 是 | 是

### <a name="guest-physical-server-storage-configuration"></a>来宾/物理服务器存储配置

**配置** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
VMDK | 是 | 不可用 | 不可用
VHD/VHDX | 不可用 | 是 | 是
第 2 代 VM | 不可用 | 是 | 是
EFI/UEFI| 否 | 是 | 是
共享群集磁盘 | VMware 支持<br/><br/> 物理服务器不支持 | 否 | 否
加密磁盘 | 否 | 否 | 否
NFS | 否 | 不可用 | 不可用
SMB 3.0 | 否 | 否 | 否
RDM | 是<br/><br/> 物理服务器不支持 | 不可用 | 不可用
磁盘 > 1 TB | 否 | 否 | 否
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM 逻辑卷管理 | 是 | 是 | 是
存储空间 | 否 | 是 | 是
热添加/移除磁盘 | 否 | 否 | 否
排除磁盘 | 是 | 是 | 是
多路径 (MPIO) | 不可用 | 是 | 是

**Azure 存储空间** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
LRS | 是 | 是 | 是
GRS | 是 | 是 | 是
冷存储 | 否 | 否 | 否
热存储| 否 | 否 | 否
静态加密 (SSE)| 是 | 是 | 是
高级存储 | 是 | 否 | 否
导入/导出服务 | 否 | 否 | 否


## <a name="support-for-azure-compute-configuration"></a>Azure 计算配置支持

**计算功能** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
可用性集 | 否 | 否 | 否
HUB | 是 | 是 | 是

## <a name="failed-over-azure-vm-requirements"></a>故障转移 Azure VM 要求

可以部署 Site Recovery 以复制运行受 Azure 支持的任何操作系统的虚拟机和物理服务器。 这包括大多数的 Windows 和 Linux 版本。 复制到 Azure 时，要复制的本地 VM 必须符合以下 Azure 要求。

**实体** | **要求** | **详细信息**
--- | --- | ---
**来宾操作系统** | 对于从 Hyper-V 到 Azure 的复制，Site Recovery 支持 [Azure 支持](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有操作系统。 <br/><br/> 对于 VMware 和物理服务器复制，请检查 Windows 和 Linux [先决条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 如果不支持，先决条件检查将会失败。
**来宾操作系统体系结构** | 64 位 | 如果不支持，先决条件检查将会失败
**操作系统磁盘大小** | 最大 1023 GB | 如果不支持，先决条件检查将会失败
**操作系统磁盘计数** | 1 | 如果不支持，先决条件检查将会失败。
**数据磁盘计数** | 16 或更少（最大值取决于所创建的虚拟机大小。 16 = XL） | 如果不支持，先决条件检查将会失败
**数据磁盘 VHD 大小** | 最大 1023 GB | 如果不支持，先决条件检查将会失败
**网络适配器** | 支持多个适配器 |
**共享 VHD** | 不支持 | 如果不支持，先决条件检查将会失败
**FC 磁盘** | 不支持 | 如果不支持，先决条件检查将会失败
**硬盘格式** | VHD <br/><br/> VHDX | 尽管 Azure 当前不支持 VHDX，但当你故障转移到 Azure 时，站点恢复会自动将 VHDX 转换为 VHD。 当你故障回复到本地时，虚拟机将继续使用 VHDX 格式。
**Bitlocker** | 不支持 | 保护虚拟机之前，必须先禁用 Bitlocker。
**VM 名称** | 介于 1 和 63 个字符之间。 限制为字母、数字和连字符。 应以字母或数字开头和结尾 | 在 Site Recovery 中更新虚拟机属性中的值
**VM 类型** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁盘类型为“基本”的第 2 代 VM，其中包括一个或两个格式化为 VHDX 的数据卷，并且支持的大小小于 300 GB。<br></br>不支持 Linux 第 2 代 VM。 [了解详细信息](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>恢复服务保管库操作支持

**操作** | **VMware/物理服务器** | **Hyper-V（不包含 VMM）** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否 | 否 | 否
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否 | 否 | 否


## <a name="support-for-provider-and-agent"></a>提供程序和代理支持

**Name** | **说明** | **最新版本** | **详细信息**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供程序** | 协调本地服务器与 Azure 之间的通信 <br/><br/> 在本地 VMM 服务器上安装，如果没有 VMM 服务器，则在 Hyper-V 服务器上安装 | 5.1.19（[可从门户获取](http://aka.ms/downloaddra)） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 统一安装（VMware 到 Azure）** | 协调本地 VMware 服务器与 Azure 之间的通信 <br/><br/> 在本地 VMware 服务器上安装 | 9.3.4246.1（可从门户获取） | [最新功能和修复](https://support.microsoft.com/kb/3155002)
**移动服务** | 协调本地 VMware 服务器/物理服务器和 Azure/辅助站点之间的复制<br/><br/> 在想要复制的 VMware VM 或物理服务器上安装  | NA（可从门户获取） | 不可用
**Microsoft Azure 恢复服务 (MARS) 代理** | 协调 Hyper-V VM 与 Azure 之间的复制<br/><br/> 在本地 Hyper-V 服务器（包含或不包含 VMM 服务器）上安装 | 最新代理（[可从门户获取](http://aka.ms/latestmarsagent)） |






## <a name="next-steps"></a>后续步骤
[准备部署](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


