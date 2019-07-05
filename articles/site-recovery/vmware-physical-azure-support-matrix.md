---
title: 使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 时的支持矩阵 | Microsoft Docs
description: 总结了对 VMware Vm 和物理服务器到 Azure 中使用 Azure Site Recovery 的灾难恢复的支持。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: raynew
ms.openlocfilehash: 5dc98048099264942552862498b5137b4954c200
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491645"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>将 VMware VM 和物理服务器灾难恢复到 Azure 时的支持矩阵

本文汇总了支持的组件和设置的 VMware Vm 灾难恢复和物理服务器到 Azure 中使用[Azure Site Recovery](site-recovery-overview.md)。

- [了解详细信息](vmware-azure-architecture.md)有关 VMware VM/物理服务器灾难恢复体系结构。
- 请按照我们[教程](tutorial-prepare-azure.md)尝试灾难恢复。

## <a name="deployment-scenarios"></a>部署方案

**方案** | **详细信息**
--- | ---
VMware VM 的灾难恢复 | 将本地 VMware VM 复制到 Azure。 可以在 Azure 门户中部署此方案，也可使用 [PowerShell](vmware-azure-disaster-recovery-powershell.md) 进行部署。
物理服务器的灾难恢复 | 将本地 Windows/Linux 物理服务器复制到 Azure。 可以在 Azure 门户中部署此方案。

## <a name="on-premises-virtualization-servers"></a>本地虚拟化服务器

**服务器** | **要求** | **详细信息**
--- | --- | ---
vCenter Server | 6\.7、 6.5、 6.0 或 5.5 的版本 | 我们建议您在灾难恢复部署中使用 vCenter 服务器。
vSphere 主机 | 6\.7、 6.5、 6.0 或 5.5 的版本 | 建议 vSphere 主机和 vCenter 服务器与进程服务器位于同一网络。 默认情况下，进程服务器在配置服务器上运行。 [了解详细信息](vmware-physical-azure-config-process-server-overview.md)。


## <a name="site-recovery-configuration-server"></a>Site Recovery 配置服务器

配置服务器是运行 Site Recovery 组件的本地计算机，这些组件包括配置服务器、进程服务器和主目标服务器。

- 为 VMware Vm 设置配置服务器下载的 OVF 模板来创建 VMware VM。
- 对于物理服务器，则配置服务器计算机手动设置。

组件  | **要求**
--- |---
CPU 核心数 | 8
RAM | 16 GB
磁盘数目 | 3 磁盘<br/><br/> 磁盘包括 OS 磁盘、进程服务器缓存磁盘和用于故障回复的保留驱动器。
磁盘可用空间 | 600 GB 的进程服务器缓存的空间。
磁盘可用空间 | 600 GB 的保留驱动器的空间。
操作系统  | Windows Server 2012 R2 或带有桌面体验的 Windows Server 2016 |
操作系统区域设置 | 美国英语
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | 不需要为配置服务器版本[9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)或更高版本。 
Windows Server 角色 | 未启用 Active Directory 域服务;Internet 信息服务 (IIS) 或者的 HYPER-V。 
组策略| - 阻止访问命令提示符。 <br/> - 阻止访问注册表编辑工具。 <br/> - 信任文件附件的逻辑。 <br/> - 打开脚本执行。 <br/> - [了解更多信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 确保：<br/><br/> - 无预先存在的默认网站 <br/> - 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置  <br/> - 端口 443 上没有预先存在的网站/应用侦听<br/>
NIC 类型 | VMXNET3（部署为 VMware VM 时）
IP 地址类型 | Static
端口 | 443，用于控制通道协调<br/>9443 数据传输

## <a name="replicated-machines"></a>复制的计算机

Site Recovery 支持复制在支持的计算机上运行的任何工作负荷。

组件  | **详细信息**
--- | ---
计算机设置 | 复制到 Azure 的计算机必须满足 [Azure 要求](#azure-vm-requirements)。
计算机工作负载 | Site Recovery 支持复制在支持的计算机上运行的任何工作负荷。 [了解详细信息](https://aka.ms/asr_workload)。
Windows | Windows Server 2019 (支持从[更新汇总 34](https://support.microsoft.com/help/4490016) （版本 9.22 的移动服务） 及更高版本。<br/> Windows Server 2016 （64 位服务器核心、 带桌面体验的服务器）<br/> - Windows Server 2012 R2, Windows Server 2012<br/> Windows Server 2008 R2 在最低 SP1。<br/> -Windows Server 2008 中，64 和 32 位与的最低版本为 SP2]。 仅适用于迁移的支持。 [了解详细信息](migrate-tutorial-windows-server-2008.md)。<br/> -Windows 10 中，Windows 8.1，Windows 8 中，Windows 7 64 位 (支持从[更新汇总 36](https://support.microsoft.com/help/4503156) （及更高版本的移动服务版本 9.22）。 不支持 Windows 7 RTM。 
Linux | 仅支持 64 位系统。 不支持 32 位系统。<br/><br/>每个 Linux 服务器都应有[Linux Integration Services (LIS) 组件](https://www.microsoft.com/download/details.aspx?id=55106)安装。 它是需要在 Azure 中测试故障转移/故障转移后启动服务器。 如果丢失了 LIS 组件，请确保安装[组件](https://www.microsoft.com/download/details.aspx?id=55106)之前启用复制以在 Azure 中启动的计算机。 <br/><br/> Site Recovery 会协调故障转移，以在 Azure 中运行 Linux 服务器。 但是，Linux 供应商可能会限制仅支持尚未达到使用寿命的分发版本。<br/><br/> 在 Linux 发行版中，仅支持属于分发次要版本/更新的原版内核。<br/><br/> 不支持跨主要 Linux 发行版升级受保护的计算机。 若要升级，请禁用复制，升级操作系统，然后再重新启用复制。<br/><br/> [了解详细信息](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)有关适用于 Linux 和开放源代码技术在 Azure 中的支持。
Linux Red Hat Enterprise | 5.2 到 5.11</b><br/> 6.1 到 6.10</b><br/> 7.0 到 7.6<br/> <br/> 运行 Red Hat Enterprise Linux 5.2-5.11 和 6.1 6.10 服务器而没有[Linux Integration Services (LIS) 组件](https://www.microsoft.com/download/details.aspx?id=55106)预安装。 请确保安装[组件](https://www.microsoft.com/download/details.aspx?id=55106)之前启用复制以在 Azure 中启动的计算机。
Linux：CentOS | 5.2 到 5.11</b><br/> 6.1 到 6.10</b><br/> 7.0 到 7.6<br/> <br/> 不具有服务器运行 CentOS 5.2-5.11 和 6.1 6.10 [Linux Integration Services (LIS) 组件](https://www.microsoft.com/download/details.aspx?id=55106)预安装。 请确保安装[组件](https://www.microsoft.com/download/details.aspx?id=55106)之前启用复制以在 Azure 中启动的计算机。
Ubuntu | Ubuntu 14.04 LTS 服务器[（查看支持内核版本）](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 服务器[（查看支持内核版本）](#ubuntu-kernel-versions)
Debian | Debian 7/debian 8 [（查看支持内核版本）](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3、 SP4 [（查看支持内核版本）](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3、SUSE Linux Enterprise Server 11 SP4<br/> 不支持复制的计算机从 SUSE Linux Enterprise Server 11 SP3 升级到 SP4。 若要升级，请禁用复制，并在升级后重新启用。
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> 运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3，4 和 5 （UEK3，UEK4，UEK5） 


### <a name="ubuntu-kernel-versions"></a>Ubuntu 内核版本


**支持的版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | [9.24][9.25 UR]  | 3.13.0-24-generic 到 3.13.0-169-generic，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-146-generic，<br/>到 4.15.0-1042-azure 4.15.0-1023-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-generic 到 3.13.0-167-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-143-generic、<br/>4.15.0-1023-azure 到 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-generic 到 3.13.0-165-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-142-generic、<br/>4.15.0-1023-azure 到 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-generic 到 3.13.0-164-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-140-generic、<br/>4.15.0-1023-azure 到 4.15.0-1036-azure |
|||
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generic 到 4.4.0-146-generic，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>到 4.15.0-48-generic 4.15.0-13-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>到 4.15.0-1042-azure 4.15.0-1012-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generic 到 4.4.0-143-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-46-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-generic 到 4.4.0-142-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-45-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-generic 到 4.4.0-140-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-43-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1036-azure|

### <a name="debian-kernel-versions"></a>Debian 内核版本


**支持的版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR]，[9.23][9.23 UR]， [9.24][9.24 UR]| 3.2.0-4-amd64 到 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR]， [9.24][9.24 UR] | 3.16.0-4-amd64 到 3.16.0-7-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 支持的内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4） | [9.25][9.25 UR] | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 到 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>到 4.4.121-92.104-default SP2(LTSS) 4.4.121-92.73-default</br></br>到 4.4.176-94.88-default SP3 4.4.73-5-default</br></br>到 4.4.176-4.25-azure SP3 4.4.138-4.7-azure</br></br>到 4.12.14-95.13-default SP4 4.12.14-94.41-default</br>到 4.12.14-6.9-azure SP4 4.12.14-6.3-azure |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4） | [9.24][9.24 UR] | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 到 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 到 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default 到 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default 到 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4） | [9.23][9.23 UR] | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 到 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 到 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default 到 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default 到 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3） | [9.22][9.22 UR] | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 到 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 到 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 到 4.4.162-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Linux 文件系统/来宾存储

组件  | **支持**
--- | ---
文件系统 | ext3、ext4、XFS
卷管理器 | -支持 LVM。<br/> -从支持上 LVM 的 boot[更新汇总 31](https://support.microsoft.com/help/4478871/) （版本 9.20 的移动服务） 及更高版本。 它不支持在较早的移动服务版本。<br/> 的不支持多个 OS 磁盘。
半虚拟化存储设备 | 不支持半虚拟化驱动程序导出的设备。
多队列块 IO 设备 | 不支持。
具有 HP CCISS 存储控制器的物理服务器 | 不支持。
设备/装入点命名约定 | 设备名称或装入点名称应是唯一的。<br/> 请确保没有两个设备/装入点的具有区分大小写的名称。 例如命名为与相同的 VM 的设备*device1*并*Device1*不受支持。
目录 | 正在运行的版本早于版本 9.20 的移动服务 (在中发布[更新汇总 31](https://support.microsoft.com/help/4478871/))，然后遵循以下限制：<br/><br/> -（如果设置为单独的分区/文件系统） 这些目录必须位于源服务器上的同一 OS 磁盘: / (root)、 /boot、 /usr、 /usr/local、 /var 和 /etc。</br> -/Boot 目录应位于磁盘分区上并不能是 LVM 卷。<br/><br/> 从版本 9.20 及更高版本，不会应用这些限制。 
启动目录 | 的在 VM 上支持多个启动磁盘 <br/> -不支持跨多个磁盘 LVM 卷上的启动。<br/> 不能复制没有启动磁盘的计算机。
可用空间要求| /root 分区上的 2 GB <br/><br/> 安装文件夹中的 250 MB
XFSv5 | 例如元数据校验和的 XFS 文件系统上的 XFSv5 功能是受支持 （移动服务版本 9.10 及更高版本）。<br/> 可使用 xfs_info 实用工具来检查分区的 XFS 超级块。 如果 `ftype` 设置为 1，则表示正在使用 XFSv5 功能。
BTRFS | 从支持 BTRFS[更新汇总 34](https://support.microsoft.com/help/4490016) （版本 9.22 的移动服务） 及更高版本。 如果不支持 BTRFS:<br/><br/> -启用保护后更改了 BTRFS 文件系统子宗卷。</br> -BTRFS 文件系统分散在多个磁盘。</br> - BTRFS 文件系统支持 RAID。

## <a name="vmdisk-management"></a>VM/磁盘管理

**Action** | **详细信息**
--- | ---
调整复制的 VM 上的磁盘大小 | 。
在复制的 VM 上添加磁盘 | 不支持。<br/> 禁用 VM 的复制，添加磁盘，，然后重新启用复制。

## <a name="network"></a>网络

组件  | **支持**
--- | ---
主机网络 NIC 组合 | 对于 VMware VM，受支持。 <br/><br/>对于物理计算机复制，不支持。
主机网络 VLAN | 是的。
主机网络 IPv4 | 是的。
主机网络 IPv6 | 不。
来宾/服务器网络 NIC 组合 | 不。
来宾/服务器网络 IPv4 | 是的。
来宾/服务器网络 IPv6 | 不。
来宾/服务器网络静态 IP (Windows) | 是的。
来宾/服务器网络静态 IP (Linux) | 是的。 <br/><br/>VM 配置为在故障回复时使用 DHCP。
来宾/服务器网络多个 NIC | 是的。


## <a name="azure-vm-network-after-failover"></a>Azure VM 网络（故障转移后）

组件  | **支持**
--- | ---
Azure ExpressRoute | 是
ILB | 是
ELB | 是
Azure 流量管理器 | 是
多 NIC | 是
保留 IP 地址 | 是
IPv4 | 是
保留源 IP 地址 | 是
Azure 虚拟网络服务终结点<br/> | 是
加速网络 | 否

## <a name="storage"></a>存储
组件  | **支持**
--- | ---
动态磁盘 | OS 磁盘必须是基本磁盘。 <br/><br/>数据磁盘可以是动态磁盘
Docker 磁盘配置 | 否
主机 NFS | VMware 支持<br/><br/> 物理服务器不支持
主机 SAN (iSCSI/FC) | 是
主机 vSAN | VMware 支持<br/><br/> 不适用于物理服务器
主机多路径 (MPIO) | 是，针对以下项进行了测试：Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON
主机虚拟卷 (VVols) | VMware 支持<br/><br/> 不适用于物理服务器
来宾/服务器 VMDK | 是
来宾/服务器共享群集磁盘 | 否
来宾/服务器加密磁盘 | 否
来宾/服务器 NFS | 否
来宾/服务器 iSCSI | 否
来宾/服务器 SMB 3.0 | 否
来宾/服务器 RDM | 是<br/><br/> 不适用于物理服务器
> 1 TB 的来宾/服务器磁盘 | 是<br/><br/>最大 4,095 GB<br/><br/> 磁盘必须大于 1024 MB。
逻辑和物理扇区大小均为 4K 的来宾/服务器磁盘 | 否
来宾/服务器磁盘使用 4k 逻辑和 512 字节物理扇区大小 | 否
包含 > 4 TB 的条带化磁盘的来宾/服务器卷 <br/><br/>逻辑卷管理 (LVM)| 是
来宾/服务器 - 存储空间 | 否
来宾/服务器热添加/删除磁盘 | 否
来宾/服务器 - 排除磁盘 | 是
来宾/服务器多路径 (MPIO) | 否
来宾/服务器 GPT 分区 | 从支持五个分区[更新汇总 37](https://support.microsoft.com/help/4508614/) （版本 9.25 的移动服务） 及更高版本。 以前四个受支持。
来宾/服务器 EFI/UEFI 启动 | -支持 9.13 或更高版本运行移动服务版本时。<br/> -支持时迁移 VMware Vm 或物理服务器运行 Windows Server 2012 或更高版本到 Azure。<br/> -你仅可以将 Vm 复制进行迁移。 不支持故障回复到本地。<br/> -仅支持 NTFS 和不支持安全 UEFI 启动类型。 <br/> 的磁盘扇区大小应为每个物理扇区的 512 字节。

## <a name="replication-channels"></a>复制通道

|**复制类型**   |**支持**  |
|---------|---------|
|卸载的数据传输 (ODX)    |       否  |
|脱机设定种子        |   否      |
| Azure Data Box | 否

## <a name="azure-storage"></a>Azure 存储

组件  | **支持**
--- | ---
本地冗余存储 | 是
异地冗余存储 | 是
读取访问异地冗余存储 | 是
冷存储 | 否
热存储| 否
块 Blob | 否
静态加密 (SSE)| 是
高级存储 | 是
导入/导出服务 | 否
适用于 Vnet 的 azure 存储防火墙 | 是的。<br/> 目标存储/缓存存储帐户 （用来存储复制数据） 上配置。
常规用途 v2 存储帐户 （热和冷层） | 是 （事务成本是 v2 与 V1 相比要高得多）

## <a name="azure-compute"></a>Azure 计算

**功能** | **支持**
--- | ---
可用性集 | 是
可用性区域 | 否
HUB | 是
托管磁盘 | 是

## <a name="azure-vm-requirements"></a>Azure VM 要求

在本地复制到 Azure Vm 必须满足下表中总结的 Azure VM 要求。 Site Recovery 运行时复制的先决条件检查，如果未满足某些要求，检查会失败。

组件  | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证复制的计算机[支持的操作系统](#replicated-machines)。 | 如果不支持，检查会失败。
来宾操作系统体系结构 | 64 位。 | 如果不支持，检查会失败。
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 第 | 如果不支持，检查会失败。
数据磁盘计数 | 64 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。 |
VM 名称 | 1 到 63 个字符。<br/><br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。

## <a name="churn-limits"></a>变动量限制

下表提供了 Azure Site Recovery 限制。 
- 这些限制基于我们的测试，但不涵盖所有可能的应用程序 I/O 组合。
- 实际结果可能因应用程序 I/O 组合而异。
- 为获得最佳结果，我们强烈建议你运行[部署规划器工具](site-recovery-deployment-planner.md)，和执行广泛的应用程序测试使用测试故障转移，获取应用的真实的性能视图。

**复制目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动量** | **每天的总源磁盘数据变动量**
---|---|---|---
标准存储 | 8 KB | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB  | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |  每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |20 MB/秒 | 每个磁盘 1684 GB


**源数据变动量** | **最大限制**
---|---
每个 VM 的平均数据变动量| 25 MB/秒
VM 上所有磁盘的峰值数据变动量 | 54 MB/秒
进程服务器支持的每日最大数据变动量 | 2 TB

- 这是在假设存在 30% 的 I/O 重叠的情况下给出的平均数。
- Site Recovery 能够根据重叠率、较大的写入大小和实际工作负荷 I/O 行为处理更高的吞吐量。
- 这些数字假设典型积压大约五分钟时间。 也就是说，数据在上传后会在 5 分钟内进行处理并创建恢复点。

## <a name="vault-tasks"></a>保管库任务

**Action** | **支持**
--- | ---
跨资源组移动保管库 | 否
移动保管库内和跨订阅 | 否
跨资源组移动存储、网络和 Azure VM | 否
移动存储、 网络和 Azure Vm 内部和跨订阅。 | 否


## <a name="obtain-latest-components"></a>获取最新的组件

**Name** | **说明** | **详细信息**
--- | --- | ---
配置服务器 | 已安装在本地。<br/> 协调本地 VMware 服务器 / 物理机和 Azure 之间的通信。 | - [了解有关](vmware-physical-azure-config-process-server-overview.md)配置服务器。<br/> - [了解有关](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)升级到最新版本。<br/> - [了解有关](vmware-azure-deploy-configuration-server.md)设置配置服务器。 
进程服务器 | 默认安装在配置服务器上。<br/> 接收复制数据，通过缓存、 压缩和加密，对它进行优化，并将其发送到 Azure。<br/> 随着部署扩大，您可以添加额外的进程服务器以处理更大的复制流量。 | - [了解有关](vmware-physical-azure-config-process-server-overview.md)进程服务器。<br/> - [了解有关](vmware-azure-manage-process-server.md#upgrade-a-process-server)升级到最新版本。<br/> - [了解有关](vmware-physical-large-deployment.md#set-up-a-process-server)设置横向扩展进程服务器。
移动服务 | VMware VM 或想要复制物理服务器上安装。<br/> 协调本地 VMware 服务器/物理服务器与 Azure 之间复制。| - [了解有关](vmware-physical-mobility-service-overview.md)移动服务。<br/> - [了解有关](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)升级到最新版本。<br/> 



## <a name="next-steps"></a>后续步骤
[了解如何](tutorial-prepare-azure.md)为 VMware VM 的灾难恢复准备 Azure。

[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
