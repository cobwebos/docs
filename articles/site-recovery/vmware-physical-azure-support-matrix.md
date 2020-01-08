---
title: Azure Site Recovery 中的 VMware/物理灾难恢复的支持矩阵
description: 汇总了使用 Azure Site Recovery 对 VMware Vm 和物理服务器到 Azure 的灾难恢复的支持。
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 752f2276f23aa22feac870493e1ea5c1fdc4ca98
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645933"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>将 VMware VM 和物理服务器灾难恢复到 Azure 时的支持矩阵

本文汇总了使用[Azure Site Recovery](site-recovery-overview.md)在 Azure 中将 VMware vm 和物理服务器灾难恢复到 Azure 时支持的组件和设置。

- [详细了解](vmware-azure-architecture.md)VMware VM/物理服务器灾难恢复体系结构。
- 遵循我们的[教程](tutorial-prepare-azure.md)来尝试灾难恢复。

## <a name="deployment-scenarios"></a>部署方案

**方案** | **详细信息**
--- | ---
VMware Vm 的灾难恢复 | 将本地 VMware VM 复制到 Azure。 可以在 Azure 门户中部署此方案，也可使用 [PowerShell](vmware-azure-disaster-recovery-powershell.md) 进行部署。
物理服务器的灾难恢复 | 将本地 Windows/Linux 物理服务器复制到 Azure。 可以在 Azure 门户中部署此方案。

## <a name="on-premises-virtualization-servers"></a>本地虚拟化服务器

**Server** | **要求** | **详细信息**
--- | --- | ---
vCenter Server | 版本6.7、6.5、6.0 或5。5 | 建议你在灾难恢复部署中使用 vCenter 服务器。
vSphere 主机 | 版本6.7、6.5、6.0 或5。5 | 建议 vSphere 主机和 vCenter 服务器与进程服务器位于同一网络。 默认情况下，进程服务器在配置服务器上运行。 [了解详细信息](vmware-physical-azure-config-process-server-overview.md)。


## <a name="site-recovery-configuration-server"></a>Site Recovery 配置服务器

配置服务器是运行 Site Recovery 组件的本地计算机，这些组件包括配置服务器、进程服务器和主目标服务器。

- 对于 VMware Vm，你可以通过下载 OVF 模板来创建 VMware VM 来设置配置服务器。
- 对于物理服务器，请手动设置配置服务器计算机。

组件 | **要求**
--- |---
CPU 内核数 | 8
RAM | 16 GB
磁盘数 | 3 磁盘<br/><br/> 磁盘包括 OS 磁盘、进程服务器缓存磁盘和用于故障回复的保留驱动器。
磁盘可用空间 | 用于进程服务器缓存的 600 GB 空间。
磁盘可用空间 | 保留驱动器的 600 GB 空间。
操作系统  | Windows Server 2012 R2 或具有桌面体验的 Windows Server 2016 <br/><br> 如果你计划使用此设备的内置主目标来进行故障回复，请确保操作系统版本与复制的项相同或更高。|
操作系统区域设置 | 美国英语
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | 对于配置服务器版本[9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)或更高版本不是必需的。 
Windows Server 角色 | 不要启用 Active Directory 域服务;Internet Information Services （IIS）或 Hyper-v。 
组策略| - 阻止访问命令提示符。 <br/> - 阻止访问注册表编辑工具。 <br/> - 信任文件附件的逻辑。 <br/> - 打开脚本执行。 <br/> - [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 确保：<br/><br/> - 无预先存在的默认网站 <br/> - 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置  <br/> - 端口 443 上没有预先存在的网站/应用侦听<br/>
NIC 类型 | VMXNET3（部署为 VMware VM 时）
IP 地址类型 | 静态
端口 | 443用于控制通道业务流程<br/>9443用于数据传输

## <a name="replicated-machines"></a>复制的计算机

Site Recovery 支持复制在支持的计算机上运行的任何工作负荷。

> [!Note]
> 下表列出了对包含 BIOS 启动的计算机的支持。 有关基于 UEFI 的计算机的支持，请参阅[存储](#storage)部分。

组件 | **详细信息**
--- | ---
计算机设置 | 复制到 Azure 的计算机必须满足 [Azure 要求](#azure-vm-requirements)。
计算机工作负载 | Site Recovery 支持复制在支持的计算机上运行的任何工作负荷。 [了解详细信息](https://aka.ms/asr_workload)。
Windows Server 2019 | 从[更新汇总 34](https://support.microsoft.com/help/4490016) （移动服务版本9.22）开始支持。
Windows Server 2016 64 位 | 支持服务器核心、带桌面体验的服务器。
Windows Server 2012 R2/Windows Server 2012 | 支持。
Windows Server 2008 R2 （带 SP1）。 | 支持。<br/><br/> 在移动服务代理的版本[9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)中，需要在运行 Windows 2008 R2 SP1 或更高版本的计算机上安装[服务堆栈更新（SSU）](https://support.microsoft.com/help/4490628)和[sha-1 更新](https://support.microsoft.com/help/4474419)。 2019年9月不支持 SHA-1，如果未启用 SHA-1 代码签名，则代理扩展不会按预期安装/升级。 详细了解[sha-1 升级和要求](https://aka.ms/SHA-2KB)。
Windows Server 2008 SP2 或更高版本（64位/32 位） |  仅支持迁移。 [了解详细信息](migrate-tutorial-windows-server-2008.md)。<br/><br/> 在移动服务代理的版本[9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)中，需要在 WINDOWS 2008 SP2 计算机上安装[服务堆栈更新（SSU）](https://support.microsoft.com/help/4493730)和[sha-1 更新](h https://support.microsoft.com/help/4474419)。 2019年9月不支持 ISHA-1; 如果未启用 SHA-1 代码签名，则代理扩展不会按预期安装/升级。 详细了解[sha-1 升级和要求](https://aka.ms/SHA-2KB)。
Windows 10、Windows 8.1、Windows 8 | 支持。
带 SP1 的 Windows 7 64 位 | 从[更新汇总 36](https://support.microsoft.com/help/4503156) （移动服务版本9.22）开始支持。 </br></br> 在移动服务代理的[9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)中，你需要在 WINDOWS 7 SP1 计算机上安装[服务堆栈更新（SSU）](https://support.microsoft.com/help/4490628)和[sha-1 更新](https://support.microsoft.com/help/4474419)。  2019年9月不支持 SHA-1，如果未启用 SHA-1 代码签名，则代理扩展不会按预期安装/升级。 详细了解[sha-1 升级和要求](https://aka.ms/SHA-2KB)。
Linux | 仅支持64位系统。 不支持32位系统。<br/><br/>每个 Linux 服务器都应安装[linux Integration Services （.lis）组件](https://www.microsoft.com/download/details.aspx?id=55106)。 在测试故障转移/故障转移后，需要在 Azure 中启动该服务器。 如果缺少 .LIS 组件，请确保在为要在 Azure 中启动的计算机启用复制之前安装[组件](https://www.microsoft.com/download/details.aspx?id=55106)。 <br/><br/> Site Recovery 会协调故障转移，以在 Azure 中运行 Linux 服务器。 但是，Linux 供应商可能会限制仅支持尚未达到使用寿命的分发版本。<br/><br/> 在 Linux 发行版中，仅支持属于分发次要版本/更新的原版内核。<br/><br/> 不支持跨主要 Linux 发行版升级受保护的计算机。 若要升级，请禁用复制，升级操作系统，然后再重新启用复制。<br/><br/> [详细了解](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)Azure 中对 Linux 和开源技术的支持。
Linux Red Hat Enterprise | 5.2 到 5.11</b><br/> 6.1 到 6.10</b> </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、 [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、 [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/> 运行 Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 的服务器未预装[Linux Integration Services （.lis）组件](https://www.microsoft.com/download/details.aspx?id=55106)。 请确保在启用复制之前安装[组件](https://www.microsoft.com/download/details.aspx?id=55106)，以便在 Azure 中启动计算机。
Linux：CentOS | 5.2 到 5.11</b><br/> 6.1 到 6.10</b><br/> 7.0 至7。6<br/> <br/> 运行 CentOS 5.2-5.11 & 6.1-6.10 的服务器未预装[Linux Integration Services （.lis）组件](https://www.microsoft.com/download/details.aspx?id=55106)。 请确保在启用复制之前安装[组件](https://www.microsoft.com/download/details.aspx?id=55106)，以便在 Azure 中启动计算机。
Ubuntu | Ubuntu 14.04 LTS 服务器[（查看支持的内核版本）](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 服务器[（查看支持的内核版本）](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS 服务器[（查看支持的内核版本）](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [（查看支持的内核版本）](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1、SP2、SP3、SP4 [（查看支持的内核版本）](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3、SUSE Linux Enterprise Server 11 SP4<br/> 不支持从 SUSE Linux Enterprise Server 11 SP3 升级到 SP4 的复制计算机。 若要升级，请禁用复制，并在升级后重新启用。
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、 [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> 运行 Red Hat 兼容内核或 Unbreakable Enterprise 内核 Release 3、4 & 5 （UEK3、UEK4、UEK5） 

> [!Note]
> 对于每个 Windows 版本，Azure Site Recovery 仅支持[长期服务通道（LTSC）](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)生成。  目前不支持[半年频道](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel)发布。

### <a name="ubuntu-kernel-versions"></a>Ubuntu 内核版本

**支持的版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | [9.28][9.28 UR]| 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
|||
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-泛型到 4.4.0-一般，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-72-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-泛型到 4.4.0-166-泛型，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-66-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-泛型到 4.4.0-164-泛型，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-64-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1059-azure|
16.04 LTS | [9.28][9.28 UR] | 4.4.0-21-泛型到 4.4.0-159-泛型，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-58-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1055-azure|
|||
18.04 LTS | [9.31][9.31 UR]| 4.15.0--4.15.0-泛型到-泛型 </br> 4.18.0-13-泛型到 4.18.0-25-泛型 </br> 5.0.0-15-泛型到 5.0.0-37-泛型 </br> 5.3.0-19-泛型到 5.3.0-24-泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 1012-azure 到 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-泛型到 4.15.0-66-泛型 </br> 4.18.0-13-泛型到 4.18.0-25-泛型 </br> 5.0.0-15-泛型到 5.0.0-32-泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 1012-azure 到 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-泛型到 4.15.0-62-泛型 </br> 4.18.0-13-泛型到 4.18.0-25-泛型 </br> 5.0.0-15-泛型到 5.0.0-27-泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 1012-azure 到 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Debian 内核版本


**支持的版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
Debian 7 | [9.28][9.28 UR]、 [9.29][9.29 UR]、 [9.30][9.30 UR]、 [9.31][9.31 UR]| 3.2.0-4-amd64 到 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30][9.30 UR]、 [9.31][9.31 UR] | 3.16.0-4-amd64 到 3.16.0-旁1/-0，4.9.0. bpo 到4.9.0 旁 1/-0-amd64 |
Debian 8 | [9.28][9.28 UR]、 [9.29][9.29 UR] | 3.16.0-4-amd64 到 3.16.0-10-amd64，4.9.0 旁 1/-0. bpo 到4.9.0 旁 1/-0-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 支持的内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | [9.31][9.31 UR] | 所有[股票 SUSE 12 SP1、SP2、SP3、SP4 内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 到 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | [9.30][9.30 UR] | 所有[股票 SUSE 12 SP1、SP2、SP3、SP4 内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 到 4.12.14-6.26-azure  |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | [9.29][9.29 UR] | 所有[股票 SUSE 12 SP1、SP2、SP3、SP4 内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 到 4.12.14-6.23-azure  |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | [9.28][9.28 UR] | SP1 3.12.49-11-default 到 3.12.74-60.64.40-default</br></br> SP1 （LTSS） 3.12.74-60.64.45-默认值为 3.12.74-60.64.118-默认值</br></br> SP2 4.4.21-69-default 到 4.4.120-92.70-default</br></br>SP2 （LTSS） 4.4.121-92.73-默认值为 4.4.121-92.117-默认值</br></br>SP3 4.4.73-默认为 4.4.180-94.100-默认值</br></br>SP3 4.4.138-从 azure 到 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-默认值为 4.12.14-95.29</br>SP4 4.12.14-6.3-azure 到 4.12.14-6.23-azure |


## <a name="linux-file-systemsguest-storage"></a>Linux 文件系统/来宾存储

组件 | **支持**
--- | ---
文件系统 | ext3、ext4、XFS
卷管理器 | -受支持。<br/> -从[更新汇总 31](https://support.microsoft.com/help/4478871/) （移动服务版本9.20）开始支持 LVM 上的/boot。 在早期的移动服务版本中不受支持。<br/> -不支持多个 OS 磁盘。
半虚拟化存储设备 | 不支持半虚拟化驱动程序导出的设备。
多队列块 IO 设备 | 不支持。
具有 HP CCISS 存储控制器的物理服务器 | 不支持。
设备/装入点命名约定 | 设备名称或装入点名称应是唯一的。<br/> 确保没有两个设备/装入点具有区分大小写的名称。 例如，不支持*device1*和*device1*相同 VM 的命名设备。
目录 | 如果运行早于版本9.20 的移动服务版本（在[更新汇总 31](https://support.microsoft.com/help/4478871/)中发布），则这些限制适用：<br/><br/> -这些目录（如果设置为单独的分区/文件系统）必须位于源服务器上的同一 OS 磁盘：/（root）、/boot、/usr、/usr/local、/var 和/etc。</br> -/Boot 目录应位于磁盘分区，而不是 LVM 卷上。<br/><br/> 从版本9.20 开始，这些限制并不适用。 
启动目录 | -启动磁盘不得采用 GPT 分区格式。 这是一种 Azure 体系结构限制。 支持将 GPT 磁盘作为数据磁盘。<br/><br/> 虚拟机上不支持多个启动磁盘<br/><br/> -不支持跨多个磁盘的 LVM 卷上的/boot。<br/> -不能复制没有启动磁盘的计算机。
可用空间要求| /root 分区上的 2 GB <br/><br/> 安装文件夹中的 250 MB
XFSv5 | 9\.10 支持 XFS 文件系统上的 XFSv5 功能，例如元数据校验和。<br/> 可使用 xfs_info 实用工具来检查分区的 XFS 超级块。 如果 `ftype` 设置为1，则使用 XFSv5 功能。
BTRFS | BTRFS 支持从[更新汇总 34](https://support.microsoft.com/help/4490016) （移动服务版本9.22）开始。 如果是以下情况，则不支持 BTRFS：<br/><br/> -启用保护后，BTRFS 文件系统 subvolume 会更改。</br> -BTRFS 文件系统分散在多个磁盘上。</br> -BTRFS 文件系统支持 RAID。

## <a name="vmdisk-management"></a>VM/磁盘管理

**Action** | **详细信息**
--- | ---
调整复制的 VM 上的磁盘大小 | 在故障转移之前，在源 VM 上受支持，直接位于 VM 属性中。 无需禁用/重新启用复制。<br/><br/> 如果在故障转移后更改源 VM，则更改不会捕获。<br/><br/> 如果在故障转移后更改 Azure VM 上的磁盘大小，则故障回复时，Site Recovery 将创建包含更新的新 VM。
在复制的 VM 上添加磁盘 | 不支持。<br/> 为 VM 禁用复制，添加磁盘，然后重新启用复制。

## <a name="network"></a>网络

组件 | **支持**
--- | ---
主机网络 NIC 组合 | 对于 VMware VM，受支持。 <br/><br/>对于物理计算机复制，不支持。
主机网络 VLAN | 可以。
主机网络 IPv4 | 可以。
主机网络 IPv6 | 不。
来宾/服务器网络 NIC 组合 | 不。
来宾/服务器网络 IPv4 | 可以。
来宾/服务器网络 IPv6 | 不。
来宾/服务器网络静态 IP (Windows) | 可以。
来宾/服务器网络静态 IP (Linux) | 可以。 <br/><br/>VM 配置为在故障回复时使用 DHCP。
来宾/服务器网络多个 NIC | 可以。


## <a name="azure-vm-network-after-failover"></a>Azure VM 网络（故障转移后）

组件 | **支持**
--- | ---
Azure ExpressRoute | 是
ILB | 是
ELB | 是
Azure Traffic Manager | 是
多 NIC | 是
保留 IP 地址 | 是
IPv4 | 是
保留源 IP 地址 | 是
Azure 虚拟网络服务终结点<br/> | 是
加速网络 | 否

## <a name="storage"></a>存储空间
组件 | **支持**
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
来宾/服务器 iSCSI | 对于迁移-是<br/>对于灾难恢复-否，iSCSI 将作为附加磁盘故障回复到 VM
来宾/服务器 SMB 3.0 | 否
来宾/服务器 RDM | 是<br/><br/> 不适用于物理服务器
> 1 TB 的来宾/服务器磁盘 | 是，磁盘必须大于 1024 MB<br/><br/>复制到托管磁盘时最大为 8192 GB （向前9.26 版）<br></br> 复制到存储帐户时高达 4095 GB
逻辑和物理扇区大小均为 4K 的来宾/服务器磁盘 | 否
具有4K 逻辑和512字节物理扇区大小的来宾/服务器磁盘 | 否
包含 > 4 TB 的条带化磁盘的来宾/服务器卷 <br/><br/>逻辑卷管理 (LVM)| 是
来宾/服务器 - 存储空间 | 否
来宾/服务器热添加/删除磁盘 | 否
来宾/服务器 - 排除磁盘 | 是
来宾/服务器多路径 (MPIO) | 否
来宾/服务器 GPT 分区 | 从[更新汇总 37](https://support.microsoft.com/help/4508614/) （移动服务版本9.25）开始支持五个分区。 之前仅支持 4 个。
ReFS | 移动服务版本9.23 或更高版本支持复原文件系统
来宾/服务器 EFI/UEFI 引导 | -支持 Windows Server 2012 或更高版本，具有移动代理版本9.30 的 SLES 12 SP4 和 RHEL 8。0<br/> -不支持安全 UEFI 启动类型。 

## <a name="replication-channels"></a>复制通道

|**复制类型**   |**支持**  |
|---------|---------|
|卸载的数据传输（ODX）    |       否  |
|离线种子设定        |   否      |
| Azure Data Box | 否

## <a name="azure-storage"></a>Azure 存储

组件 | **支持**
--- | ---
本地冗余存储 | 是
地域冗余存储 | 是
读取访问异地冗余存储 | 是
冷存储 | 否
热存储| 否
块 Blob | 否
静态加密（SSE）| 是
静态加密（CMK）| 否
高级存储器 | 是
导入/导出服务 | 否
适用于 Vnet 的 Azure 存储防火墙 | 可以。<br/> 在目标存储/缓存存储帐户（用于存储复制数据）上配置。
常规用途 v2 存储帐户（热层和冷层） | 是（与 V1 相比，V2 的事务成本明显较高）

## <a name="azure-compute"></a>Azure 计算

**功能** | **支持**
--- | ---
可用性集 | 是
可用性区域 | 否
HUB | 是
托管磁盘 | 是

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的本地 Vm 必须满足此表中汇总的 Azure VM 要求。 当 Site Recovery 为复制运行必备项检查时，如果无法满足某些要求，则检查将失败。

组件 | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证复制的计算机[支持的操作系统](#replicated-machines)。 | 如果不支持，检查会失败。
来宾操作系统体系结构 | 64 位。 | 如果不支持，检查会失败。
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 第 | 如果不支持，检查会失败。
数据磁盘计数 | 64 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 复制到托管磁盘时最大为 8192 GB （向前9.26 版）<br></br>复制到存储帐户时高达 4095 GB| 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。 |
VM 名称 | 1 到 63 个字符。<br/><br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。

## <a name="resource-group-limits"></a>资源组限制

要了解可在单个资源组下保护的虚拟机的数量，请参阅[订阅限制和配额](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)一文

## <a name="churn-limits"></a>改动限制

下表提供了 Azure Site Recovery 限制。 
- 这些限制基于我们的测试，但并未涵盖所有可能的应用程序 i/o 组合。
- 实际结果可能因应用程序 I/O 组合而异。
- 为了获得最佳效果，我们强烈建议你运行[部署规划器工具](site-recovery-deployment-planner.md)，并使用测试故障转移执行大量应用程序测试，以获得应用的真正性能图片。

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
VM 上所有磁盘的峰值数据变动量 | 54 MB/秒
进程服务器支持的每日最大数据变动量 | 2 TB

- 这是在假设存在 30% 的 I/O 重叠的情况下给出的平均数。
- Site Recovery 能够根据重叠率、较大的写入大小和实际工作负荷 I/O 行为处理更高的吞吐量。
- 这些数字假设一般积压工作（backlog）约为五分钟。 也就是说，数据在上传后会在 5 分钟内进行处理并创建恢复点。

## <a name="vault-tasks"></a>保管库任务

**Action** | **支持**
--- | ---
跨资源组移动保管库 | 否
在订阅内和跨订阅移动保管库 | 否
跨资源组移动存储、网络和 Azure VM | 否
在订阅内和跨订阅移动存储、网络、Azure Vm。 | 否


## <a name="obtain-latest-components"></a>获取最新组件

**名称** | **说明** | **详细信息**
--- | --- | ---
配置服务器 | 已在本地安装。<br/> 协调本地 VMware 服务器或物理计算机与 Azure 之间的通信。 | - [了解](vmware-physical-azure-config-process-server-overview.md)配置服务器。<br/> - [了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)升级到最新版本。<br/> - [了解](vmware-azure-deploy-configuration-server.md)设置配置服务器。 
进程服务器 | 默认安装在配置服务器上。<br/> 接收复制数据，通过缓存、压缩和加密对其进行优化，并将其发送到 Azure。<br/> 随着部署的增长，你可以添加额外的进程服务器来处理更大的复制流量。 | - [了解](vmware-physical-azure-config-process-server-overview.md)进程服务器。<br/> - [了解](vmware-azure-manage-process-server.md#upgrade-a-process-server)升级到最新版本。<br/> - [了解如何](vmware-physical-large-deployment.md#set-up-a-process-server)设置横向扩展进程服务器。
移动服务 | 安装在要复制的 VMware VM 或物理服务器上。<br/> 协调本地 VMware 服务器/物理服务器和 Azure 之间的复制。| - [了解](vmware-physical-mobility-service-overview.md)移动服务。<br/> - [了解](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)升级到最新版本。<br/> 



## <a name="next-steps"></a>后续步骤
[了解如何](tutorial-prepare-azure.md)为 VMware VM 的灾难恢复准备 Azure。


[9.31 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
