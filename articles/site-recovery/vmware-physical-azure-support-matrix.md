---
title: 使用 Azure Site Recovery 将 VMware VM 和物理服务器复制到 Azure 时的支持矩阵 | Microsoft Docs
description: 汇总了使用 Azure Site Recovery 将 VMware VM 和物理服务器复制到 Azure 时支持的操作系统和组件。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: raynew
ms.openlocfilehash: 16a19a862d4fc6b424e8441ec5377ea3c819fadb
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317436"
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>VMware 和物理服务器到 Azure 的复制支持矩阵

本文汇总了使用 [Azure Site Recovery](site-recovery-overview.md) 服务执行从 VMware VM 到 Azure 的灾难恢复时支持的组件和设置。

## <a name="replication-scenario"></a>复制方案

**方案** | **详细信息**
--- | ---
VMware VM | 将本地 VMware VM 复制到 Azure。 可以在 Azure 门户中部署此方案，也可使用 [PowerShell](vmware-azure-disaster-recovery-powershell.md) 进行部署。
物理服务器 | 将本地 Windows/Linux 物理服务器复制到 Azure。 可以在 Azure 门户中部署此方案。

## <a name="on-premises-virtualization-servers"></a>本地虚拟化服务器

**服务器** | **要求** | **详细信息**
--- | --- | ---
VMware | vCenter Server 6.5、6.0、5.5 或 vSphere 6.5、6.0、5.5 | 建议使用 vCenter 服务器。<br/><br/> 建议 vSphere 主机和 vCenter 服务器与进程服务器位于同一网络。 默认情况下，进程服务器组件在配置服务器上运行，因此应在此网络中设置配置服务器，除非已设置专用进程服务器。
物理 | 不适用

## <a name="site-recovery-configuration-server"></a>Site Recovery 配置服务器

配置服务器是运行 Site Recovery 组件的本地计算机，这些组件包括配置服务器、进程服务器和主目标服务器。 对于 VMware 复制，请按所有要求设置配置服务器，使用 OVF 模板来创建 VMware VM。 对于物理服务器复制，请手动设置配置服务器计算机。

组件 | **要求**
--- |---
CPU 核心数 | 8
RAM | 16 GB
磁盘数目 | 3 磁盘<br/><br/> 磁盘包括 OS 磁盘、进程服务器缓存磁盘和用于故障回复的保留驱动器。
磁盘可用空间 | 对于进程服务器缓存，600 GB 的空间是必需的。
磁盘可用空间 | 对于保留驱动器，600 GB 的空间是必需的。
操作系统  | Windows Server 2012 R2 或 Windows Server 2016 |
操作系统区域设置 | 美国英语
PowerCLI | 应安装 [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")。
Windows Server 角色 | 请勿启用： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V |
组策略| 请勿启用： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 确保：<br/><br/> - 无预先存在的默认网站 <br> - 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置  <br> - 端口 443 上没有预先存在的网站/应用侦听<br>
NIC 类型 | VMXNET3（部署为 VMware VM 时）
IP 地址类型 | 静态
端口 | 443，用于控制通道协调<br>9443，用于数据传输

## <a name="replicated-machines"></a>复制的计算机

Site Recovery 支持复制在支持的计算机上运行的任何工作负荷。

组件 | **详细信息**
--- | ---
计算机设置 | 复制到 Azure 的计算机必须满足 [Azure 要求](#azure-vm-requirements)。
Windows 操作系统 | 64 位 Windows Server 2016（服务器核心，带桌面体验的服务器）、Windows Server 2012 R2、Windows Server 2012、带 SP1（或更高版本）的 Windows Server 2008 R2。 不支持 Windows 2016 Nano Server。
Linux 操作系统 | Red Hat Enterprise Linux：5.2 到 5.11、6.1 到 6.9、7.0 到 7.4 <br/><br/>CentOS：5.2 到 5.11、6.1 到 6.9、7.0 到 7.4 <br/><br/>Ubuntu 14.04 LTS 服务器[（受支持的内核版本）](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 服务器[（受支持的内核版本）](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[（受支持的内核版本）](#debian-kernel-versions)<br/><br/>Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核），或 Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3、SUSE Linux Enterprise Server 11 SP4 <br/><br/>不支持将复制计算机从 SP3 升级到 SP4。 若要升级，请禁用复制并在升级后重新启用它。

>[!NOTE]
>
> - 在 Linux 发行版中，仅支持属于分发次要版本/更新的原版内核。
>
> - 不支持跨主要 Linux 发行版升级受保护的计算机。 若要升级，请禁用复制，升级操作系统，然后再重新启用复制。
>

### <a name="ubuntu-kernel-versions"></a>Ubuntu 内核版本


**支持的版本** | **Azure Site Recovery 移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | 9.17 | 3.13.0-24-generic to 3.13.0-149-generic,<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-127-generic |
14.04 LTS | 9.16 | 3.13.0-24-generic 到 3.13.0-144-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-119-generic |
14.04 LTS | 9.15 | 3.13.0-24-generic 到 3.13.0-144-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-119-generic |
14.04 LTS | 9.14 | 3.13.0-24-generic 到 3.13.0-142-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-116-generic |
|||
16.04 LTS | 9.17 | 4.4.0-21-generic 到 4.4.0-127-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-43-generic |
16.04 LTS | 9.16 | 4.4.0-21-generic 到 4.4.0-119-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-38-generic |
16.04 LTS | 9.15 | 4.4.0-21-generic 到 4.4.0-119-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-38-generic |
16.04 LTS | 9.14 | 4.4.0-21-generic 到 4.4.0-116-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-36-generic |


### <a name="debian-kernel-versions"></a>Debian 内核版本


**支持的版本** | **Azure Site Recovery 移动服务版本** | **内核版本** |
--- | --- | --- |
Debian 7 | 9.17 | 3.2.0-4-amd64 到 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.14、9.15、9.16 | 3.2.0-4-amd64 到 3.2.0-5-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.17 | 3.16.0-4-amd64 到 3.16.0-6-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 到 3.16.0-5-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.14、9.15 | 3.16.0-4-amd64 到 3.16.0-5-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.5-amd64 |


## <a name="linux-file-systemsguest-storage"></a>Linux 文件系统/来宾存储

组件 | **支持**
--- | ---
文件系统 | ext3、ext4、XFS。
卷管理器 | LVM2。
半虚拟化存储设备 | 不支持半虚拟化驱动程序导出的设备。
多队列块 IO 设备 | 不支持。
具有 HP CCISS 存储控制器的物理服务器 | 不支持。
目录 | 下列目录（如果设置为单独的分区/文件系统）都必须位于源服务器上的同一 OS 磁盘：/(root)、/boot、/usr、/usr/local、/var 和 /etc。</br></br> /boot 应位于磁盘分区上，而不是位于 LVM 卷上。<br/><br/>
可用空间要求| /root 分区上的 2 GB <br/><br/> 安装文件夹中的 250 MB
XFSv5 | 从版本 9.10 开始，移动服务支持 XFS 文件系统上的 XFSv5 功能，例如元数据校验和。 可使用 xfs_info 实用工具来检查分区的 XFS 超级块。 如果 ftyp 设置为 1，则表示正在使用 XFSv5 功能。



## <a name="network"></a>网络

组件 | **支持**
--- | ---
主机网络 NIC 组合 | 对于 VMware VM，受支持。 <br/><br/>对于物理计算机复制，不支持。
主机网络 VLAN | 是的。
主机网络 IPv4 | 是的。
主机网络 IPv6 | 不是。
来宾/服务器网络 NIC 组合 | 不是。
来宾/服务器网络 IPv4 | 是的。
来宾/服务器网络 IPv6 | 不是。
来宾/服务器网络静态 IP (Windows) | 是的。
来宾/服务器网络静态 IP (Linux) | 是的。 <br/><br/>VM 配置为在故障回复时使用 DHCP。
来宾/服务器网络多个 NIC | 是的。


## <a name="azure-vm-network-after-failover"></a>Azure VM 网络（故障转移后）

组件 | **支持**
--- | ---
Azure ExpressRoute | 是
ILB | 是
ELB | 是
Azure 流量管理器 | 是
多 NIC | 是
保留 IP 地址 | 是
IPv4 | 是
保留源 IP 地址 | 是
Azure 虚拟网络服务终结点<br/> （不带 Azure 存储防火墙） | 是
加速网络 | 否

## <a name="storage"></a>存储
组件 | **支持**
--- | ---
主机 NFS | VMware 支持<br/><br/> 物理服务器不支持
主机 SAN (iSCSI/FC) | 是
主机 vSAN | VMware 支持<br/><br/> 不适用于物理服务器
主机多路径 (MPIO) | 是，针对以下项进行了测试：Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON
主机虚拟卷 (VVols) | VMware 支持<br/><br/> 不适用于物理服务器
来宾/服务器 VMDK | 是
来宾/服务器 EFI/UEFI| 部分（只有 Windows Server 2012 及更高版本的 VMware 虚拟机可迁移到 Azure） </br></br> 请参阅表末尾的说明
来宾/服务器共享群集磁盘 | 否
来宾/服务器加密磁盘 | 否
来宾/服务器 NFS | 否
来宾/服务器 SMB 3.0 | 否
来宾/服务器 RDM | 是<br/><br/> 不适用于物理服务器
> 1 TB 的来宾/服务器磁盘 | 是<br/><br/>最大 4,095 GB
逻辑和物理扇区大小均为 4K 的来宾/服务器磁盘 | 是
逻辑扇区大小为 4K 且物理扇区大小为 512 字节 的来宾/服务器磁盘 | 是
包含 > 4 TB 的条带化磁盘的来宾/服务器卷 <br><br/>逻辑卷管理 (LVM)| 是
来宾/服务器 - 存储空间 | 否
来宾/服务器热添加/删除磁盘 | 否
来宾/服务器 - 排除磁盘 | 是
来宾/服务器多路径 (MPIO) | 否

> [!NOTE]
> 运行 Windows Server 2012 或更高版本的 UEFI 引导 VMware 虚拟机可迁移到 Azure。 存在以下限制：

> - 仅支持到 Azure 的迁移。 不支持故障回复到本地 VMware 站点。
> - 服务器在 OS 磁盘上不应具有四个以上分区。
> - 需要移动服务 9.13 版或更高版本。
> - 不适用于物理服务器。

## <a name="azure-storage"></a>Azure 存储

组件 | **支持**
--- | ---
本地冗余存储 | 是
异地冗余存储 | 是
读取访问异地冗余存储 | 是
冷存储 | 否
热存储| 否
块 Blob | 否
静态加密（存储服务加密）| 是
高级存储 | 是
导入/导出服务 | 否
在目标存储/缓存存储帐户（用来存储复制数据）上配置的用于虚拟网络的 Azure 存储防火墙 | 否
常规用途 V2 存储帐户（包括热存储层和冷存储层） | 否

## <a name="azure-compute"></a>Azure 计算

**功能** | **支持**
--- | ---
可用性集 | 是
HUB | 是
托管磁盘 | 是

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的本地 VM 必须满足此表中汇总的 Azure VM 要求。 Site Recovery 运行先决条件检查时，如果不符合某些要求，该检查将失败。

组件 | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证复制的计算机[支持的操作系统](#replicated-machines)。 | 如果不支持，检查会失败。
来宾操作系统体系结构 | 64 位。 | 如果不支持，检查会失败。
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。  
数据磁盘计数 | 64 或更少。 | 如果不支持，检查会失败。  
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。 |
VM 名称 | 1 到 63 个字符。<br/><br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。


## <a name="vault-tasks"></a>保管库任务

**Action** | **支持**
--- | ---
跨资源组移动保管库<br/><br/> 订阅内和跨订阅移动 | 否
跨资源组移动存储、网络和 Azure VM<br/><br/> 订阅内和跨订阅移动 | 否


## <a name="mobility-service"></a>移动服务

名称 | **说明** | **最新版本** | **详细信息**
--- | --- | --- | --- | ---
Azure Site Recovery 统一安装程序 | 协调本地 VMware 服务器与 Azure 之间的通信 <br/><br/> 在本地 VMware 服务器上安装 | 9.12.4653.1（可从门户获取） | [最新功能和修复](https://aka.ms/latest_asr_updates)
移动服务 | 协调本地 VMware 服务器/物理服务器和 Azure/辅助站点之间的复制<br/><br/> 在想要复制的 VMware VM 或物理服务器上安装 | 9.12.4653.1（可从门户获取） | [最新功能和修复](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>后续步骤
[了解如何](tutorial-prepare-azure.md)为 VMware VM 的灾难恢复准备 Azure。
