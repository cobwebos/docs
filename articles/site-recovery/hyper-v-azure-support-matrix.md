---
title: "用于 Hyper-V 到 Azure 的复制的支持矩阵 | Microsoft Docs"
description: "汇总了使用 Azure Site Recovery 执行 Hyper-V 到 Azure 的复制时支持的组件和相关要求"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.openlocfilehash: 58d54c1e0e6aa88878b45400b9211396f5d1b9d5
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>用于 Hyper-V 到 Azure 的复制的支持矩阵


本文汇总了使用 [Azure Site Recovery](site-recovery-overview.md) 服务执行本地 Hyper-V VM 到 Azure 的灾难恢复时支持的组件和设置。


## <a name="supported-scenarios"></a>支持的方案

**方案** | **详细信息**
--- | --- 
**Hyper-V（包含 VMM）** | 对于托管在 System Center Virtual Machine Manager (VMM) 结构中的 Hyper-V 主机，可针对在其上运行的 VM 执行到 Azure 的灾难恢复<br/><br/> 可以在 Azure 门户中部署此方案，也可使用 PowerShell 进行部署。<br/><br/> 由 VMM 托管 Hyper-V 主机时，也可以执行到辅助本地站点的灾难恢复。 阅读[本文](tutorial-vmm-to-vmm.md)，了解此方案的详细信息。
**不包含 VMM 的 Hyper-V** | 对于在并非由 VMM 托管的 Hyper-V 主机，可针对在其上运行的 VM 执行到 Azure 的灾难恢复。<br/><br/> 可以在 Azure 门户中部署此方案，也可使用 PowerShell 进行部署。 


## <a name="on-premises-servers"></a>本地服务器

**服务器** | **要求** | **详细信息**
--- | --- | ---
**Hyper-V（在不包含 VMM 的情况下运行）** | Windows Server 2016、带有最新更新的 Windows Server 2012 R2。 | 在 Site Recovery 中配置 Hyper-V 站点时，不支持混合使用运行 Windows Server 2016 和 2012 R2 的主机。<br/><br/> 对于位于运行 Windows Server 2016 的主机上的 VM，不支持恢复到备用位置。
**Hyper-V（在包含 VMM 的情况下运行）** | VMM 2016、VMM 2012 R2 | 如果使用了 VMM，Windows Server 2016 主机应由 VMM 2016 托管。<br/><br/> 当前不支持混合使用 Hyper-V 主机（运行 Windows Server 2016 和 2012 R2）的 VMM 云。<br/><br/> 不支持包括现有 VMM 2012 R2 服务器到 2016 的升级的环境。


## <a name="replicated-vms"></a>复制的 VM


下表汇总了 VM 支持。 Site Recovery 支持在受支持的操作系统上运行的任何工作负荷。 

 **组件** | **详细信息**
--- | ---
VM 配置 | 复制到 Azure 的 VM 必须满足[Azure 要求](#failed-over-azure-vm-requirements)。
来宾操作系统 | [Azure 支持的](https://technet.microsoft.com/library/cc794868.aspx)任何来宾操作系统。<br/><br/> 不支持 Windows Server 2016 Nano Server。




## <a name="hyper-v-network-configuration"></a>Hyper-V 网络配置

**组件** | **Hyper-V（包含 VMM）** | **不包含 VMM 的 Hyper-V**
--- | --- | ---
主机网络：NIC 组合 | 是
主机网络：VLAN | 是
主机网络：IPv4 | 是
主机网络：IPv6 | 否
来宾 VM 网络：NIC 组合 | 否
来宾 VM 网络：IPv4 | 是
来宾 VM 网络：IPv6 | 否
来宾 VM 网络：静态 IP (Windows) | 是
来宾 VM 网络：静态 IP (Linux) | 否
来宾 VM 网络：多 NIC | 是



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM 网络配置（故障转移后）

**组件** | **Hyper-V（包含 VMM）** | **不包含 VMM 的 Hyper-V**
--- | --- | ---
ExpressRoute | 是 | 是
ILB | 是 | 是
ELB | 是 | 是
流量管理器 | 是 | 是
多 NIC | 是 | 是
保留 IP | 是 | 是
IPv4 | 是 | 是
保留源 IP 地址 | 是 | 是
VNET 服务终结点<br/><br/> （Azure 存储防火墙和虚拟网络） | 否 | 否


## <a name="hyper-v-host-storage"></a>Hyper-V 主机存储

**存储** | **Hyper-V（包含 VMM）** | 不包含 VMM 的 Hyper-V
--- | --- | --- | ---
NFS | 不可用 | 不可用
SMB 3.0 | 是 | 是
SAN (ISCSI) | 是 | 是
多路径 (MPIO)。 测试使用对象：<br></br> Microsoft DSM、EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM for CLARiiON | 是 | 是

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM 来宾存储

**存储** | **Hyper-V（包含 VMM）** | 不包含 VMM 的 Hyper-V
--- | --- | ---
VMDK | 不可用 | 不可用
VHD/VHDX | 是 | 是
第 2 代 VM | 是 | 是
EFI/UEFI| 是 | 是
共享群集磁盘 | 否 | 否
加密磁盘 | 否 | 否
NFS | 不可用 | 不可用
SMB 3.0 | 否 | 否
RDM | 不可用 | 不可用
磁盘 > 1 TB | 是，最大 4095 GB | 是，最大 4095 GB
磁盘：4K 逻辑和物理扇区 | 不支持：第 1 代/第 2 代 | 不支持：第 1 代/第 2 代
磁盘：4K 逻辑扇区和 512 字节物理扇区 | 是 |  是
包含条带化磁盘的卷 > 1 TB<br/><br/> LVM 逻辑卷管理 | 是 | 是
存储空间 | 是 | 是
热添加/移除磁盘 | 否 | 否
排除磁盘 | 是 | 是
多路径 (MPIO) | 是 | 是

## <a name="azure-storage"></a>Azure 存储

**组件** | **Hyper-V（包含 VMM）** | **不包含 VMM 的 Hyper-V**
--- | --- | ---
LRS | 是 | 是
GRS | 是 | 是
RA-GRS | 是 | 是
冷存储 | 否 | 否
热存储| 否 | 否
块 Blob | 否 | 否
静态加密 (SSE)| 是 | 是
高级存储 | 是 | 是
导入/导出服务 | 否 | 否
VNET 服务终结点（Azure 存储防火墙和 VNET），目的是缓存用于复制数据的存储帐户 | 否 | 否


## <a name="azure-compute-features"></a>Azure 计算功能

**功能** | **Hyper-V（包含 VMM）** | **不包含 VMM 的 Hyper-V**
--- | --- | ---
可用性集 | 是 | 是
HUB | 是 | 是  
托管磁盘 | 是，用于故障转移<br/><br/> 不支持托管磁盘的故障回复 | 是，用于故障转移<br/><br/> 不支持托管磁盘的故障回复

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的本地 VM 必须满足此表中汇总的 Azure VM 要求。

**组件** | **要求** | **详细信息**
--- | --- | ---
**来宾操作系统** | Site Recovery 支持 [Azure 支持的](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)所有操作系统。  | 如果不支持，先决条件检查会失败。
**来宾操作系统体系结构** | 64 位 | 如果不支持，先决条件检查会失败。
**操作系统磁盘大小** | 第 1 代 VM 最大 2048 GB。<br/><br/> 第 2 代 VM 最大 300 GB。  | 如果不支持，先决条件检查会失败。
**操作系统磁盘计数** | 1 | 如果不支持，先决条件检查会失败。
**数据磁盘计数** | 16 个或更少  | 如果不支持，先决条件检查会失败。
**数据磁盘 VHD 大小** | 最大 4095 GB | 如果不支持，先决条件检查会失败。
**网络适配器** | 支持多个适配器 |
**共享 VHD** | 不支持 | 如果不支持，先决条件检查会失败。
**FC 磁盘** | 不支持 | 如果不支持，先决条件检查会失败。
**硬盘格式** | VHD <br/><br/> VHDX | 故障转移到 Azure 时，Site Recovery 自动将 VHDX 转换为 VHD。 故障回复到本地时，虚拟机将继续使用 VHDX 格式。
**Bitlocker** | 不支持 | 为 VM 启用复制之前，必须先禁用 Bitlocker。
**VM 名称** | 介于 1 和 63 个字符之间。 限制为字母、数字和连字符。 VM 名称必须以字母或数字开始或结尾。 | 在 Site Recovery 中更新 VM 属性中的值。
**VM 类型** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁盘类型为“基本”的第 2 代 VM（其中包括一个或两个格式化为 VHDX 的数据卷），并且支持的磁盘空间大小小于 300 GB。<br></br>不支持 Linux 第 2 代 VM。 [了解详细信息](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>恢复服务保管库操作

**Action** |  **Hyper-V（包含 VMM）** | **不包含 VMM 的 Hyper-V**
--- | --- | --- 
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否 | 否 
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否 | 否 


## <a name="provider-and-agent"></a>提供程序和代理

要确保部署与本文的设置兼容，请确保正在运行最新的提供程序和代理版本。

**Name** | **说明** | **详细信息**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供程序** | 协调本地服务器与 Azure 之间的通信 <br/><br/> 包含 VMM 的 Hyper-V：安装在 VMM 服务器上<br/><br/> 不包含 VMM 的 Hyper-V：安装在 Hyper-V 主机上| 最新版本：5.1.2700.1（可从门户获取）<br/><br/> [最新功能和修复](https://aka.ms/latest_asr_updates)
**Microsoft Azure 恢复服务 (MARS) 代理** | 协调 Hyper-V VM 与 Azure 之间的复制<br/><br/> 在本地 Hyper-V 服务器（包含或不包含 VMM ）上安装 | 可从门户获取最新代理






## <a name="next-steps"></a>后续步骤
了解如何为本地 Hyper-V VM 的灾难恢复[准备 Azure](tutorial-prepare-azure.md)。 
