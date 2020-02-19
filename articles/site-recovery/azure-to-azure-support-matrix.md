---
title: Azure VM 灾难恢复的支持矩阵与 Azure Site Recovery
description: 汇总了对 Azure Vm 使用 Azure Site Recovery 对辅助区域进行灾难恢复的支持。
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: d278f96acf8d8efc57a9ae7fb57f9a758339162a
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444071"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure 区域之间 Azure VM 灾难恢复的支持矩阵

本文汇总了使用[Azure Site Recovery](site-recovery-overview.md)服务将 azure vm 从一个 azure 区域到另一个 azure 区域的灾难恢复的支持和先决条件。


## <a name="deployment-method-support"></a>部署方法支持

**部署** |  **支持**
--- | ---
**Azure 门户** | 支持。
**PowerShell** | 支持。 [了解详细信息](azure-to-azure-powershell.md)
**REST API** | 支持。
**CLI** | 目前不支持


## <a name="resource-support"></a>资源支持

**资源操作** | **详细信息**
--- | --- 
**跨资源组移动保管库** | 不支持
**跨资源组移动计算/存储/网络资源** | 不支持。<br/><br/> 如果在 VM 复制后移动 VM 或相关组件（如存储/网络），则需要禁用并重新启用 VM 的复制。
**将 Azure VM 从一个订阅复制到另一个订阅以进行灾难恢复** | 在同一 Azure Active Directory 租户中受支持。
**在受支持的地理群集内跨区域迁移虚拟机（订阅内和跨订阅）** | 在同一 Azure Active Directory 租户中受支持。
**在同一区域内迁移 VM** | 不支持。

## <a name="region-support"></a>区域支持

可以在同一地理群集中的任意两个区域之间复制和恢复 VM。 在定义地理群集时请注意数据延迟和主权。


**地理群集** | **Azure 区域**
-- | --
美洲 | 加拿大东部、加拿大中部、美国中南部、美国中西部、美国东部、美国东部 2、美国西部、美国西部 2、美国中部、美国中北部
欧洲 | 英国西部、英国南部、北欧、西欧、法国中部、法国南部、南非西部、南非北部、挪威东部、挪威西部
亚洲 | 印度南部、印度中部、印度西部、东南亚、东亚、日本东部、日本西部、韩国中部、韩国南部、阿拉伯联合酋长国中部、阿拉伯联合酋长国北部
澳大利亚   | 澳大利亚东部、澳大利亚东南部、澳大利亚中部、澳大利亚中部 2
Azure Government    | US Gov 弗吉尼亚州、US Gov 爱荷华州、US Gov 亚利桑那州、US Gov 德克萨斯州、US DOD 东部、US DOD 中部 
德国 | 德国中部、德国东北部
中国 | 中国东部、中国北部、中国北部 2、中国东部 2
为国内灾难恢复保留的受限制区域 |德国北部为德国中西部保留，瑞士西部瑞士北部为法国南部 

>[!NOTE]
>
> - 对于**巴西南部**，可以复制和故障转移到以下区域：美国中南部、美国西部、美国东部、美国东部2、美国西部、美国西部2和美国中北部。
> - 巴西南部只能用作可使用 Site Recovery 复制 Vm 的源区域。 它不能充当目标区域。 这是因为地理距离导致延迟问题。 请注意，如果将巴西南部作为源区域故障转移到目标，则支持从目标区域到巴西南部的故障回复。
> - 可以在具有相应访问权限的区域内工作。
> - 如果要在其中创建保管库的区域未显示，请确保订阅有权在该区域中创建资源。
> - 如果在启用复制时看不到地理群集中的某个区域，请确保订阅有权在该区域中创建 Vm。



## <a name="cache-storage"></a>缓存存储

此表汇总了对复制期间 Site Recovery 使用的缓存存储帐户的支持。

**设置** | **支持** | **详细信息**
--- | --- | ---
常规用途 V2 存储帐户（热存储层和冷存储层） | 支持 | 不建议使用 GPv2，因为 V2 的事务成本明显高于 V1 存储帐户。
高级存储 | 不支持 | 标准存储帐户用于缓存存储，有助于优化成本。
虚拟网络的 Azure 存储防火墙  | 支持 | 如果你使用的是启用了防火墙的缓存存储帐户或目标存储帐户，请确保“[允许受信任的 Microsoft 服务](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)”。<br></br>同时，确保允许访问至少一个源 Vnet 子网。


## <a name="replicated-machine-operating-systems"></a>复制的计算机操作系统

Site Recovery 支持复制那些运行本节中所列操作系统的 Azure VM。

### <a name="windows"></a>Windows


**操作系统** | **详细信息**
--- | ---
Windows Server 2019 | 支持服务器核心、带桌面体验的服务器。
Windows Server 2016  | 支持的服务器核心、带桌面体验的服务器。
Windows Server 2012 R2 | 支持。
Windows Server 2012 | 支持。
Windows Server 2008 R2 SP1/SP2 | 支持。<br/><br/> 在 Azure Vm 的移动服务扩展版本[9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)中，需要在运行 windows Server 2008 R2 SP1/SP2 的计算机上安装 windows[服务堆栈更新（SSU）](https://support.microsoft.com/help/4490628)和[sha-1 更新](https://support.microsoft.com/help/4474419)。  2019年9月不支持 SHA-1，如果未启用 SHA-1 代码签名，则代理扩展不会按预期安装/升级。 详细了解[sha-1 升级和要求](https://aka.ms/SHA-2KB)。
Windows 10 (x64) | 支持。
Windows 8.1 （x64） | 支持。
Windows 8 （x64） | 支持。
带 SP1 的 Windows 7 （x64） | 在 Azure Vm 的移动服务扩展版本[9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)中，需要在运行 WINDOWS 7 SP1 的计算机上安装 windows[服务堆栈更新（SSU）](https://support.microsoft.com/help/4490628)和[sha-1 更新](https://support.microsoft.com/help/4474419)。  2019年9月不支持 SHA-1，如果未启用 SHA-1 代码签名，则代理扩展不会按预期安装/升级。 详细了解[sha-1 升级和要求](https://aka.ms/SHA-2KB)。



#### <a name="linux"></a>Linux

**操作系统** | **详细信息**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、 [8.0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)、8。1
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、8.0、8。1
Ubuntu 14.04 LTS Server | [受支持的内核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [受支持的内核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 使用基于密码的身份验证和登录的 Ubuntu 服务器，以及用于配置云 Vm 的云初始化包，可能会在故障转移时禁用基于密码的登录（具体取决于 cloudinit 配置）。 可以在虚拟机上重新启用基于密码的登录，方法是从 Azure 门户中故障转移的 VM 的支持 > 疑难解答 > 设置 "菜单重置密码。
Ubuntu 18.04 LTS 服务器 | [受支持的内核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [受支持的内核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [受支持的内核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4。 [（受支持的内核版本）](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15和 15 SP1。 [（受支持的内核版本）](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 不支持将复制计算机从 SP3 升级到 SP4。 如果已升级复制的计算机，则需要禁用复制并在升级后重新启用复制。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、 [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> 运行 Red Hat 兼容内核或 Unbreakable Enterprise 内核 Release 3、4 & 5 （UEK3、UEK4、UEK5） 


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 Ubuntu 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-泛型到 3.13.0-一般，<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-泛型到 4.4.0-<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-泛型到 4.4.0-泛型，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-74-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-azure|
16.04 LTS | 9.31 | 4.4.0-21-泛型到 4.4.0-一般，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-72-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-泛型到 4.4.0-166-泛型，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-66-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-泛型到 4.4.0-164-泛型，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-64-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-泛型到 4.15.0-74-泛型 </br> 4.18.0-13-泛型到 4.18.0-25-泛型 </br> 5.0.0-15-泛型到 5.0.0-37-泛型 </br> 5.3.0-19-泛型到 5.3.0-24-泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 1012-azure 到 5.0.0-1028-azure </br> 5.3.0-1007-azure 到 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0--4.15.0-泛型到-泛型 </br> 4.18.0-13-泛型到 4.18.0-25-泛型 </br> 5.0.0-15-泛型到 5.0.0-37-泛型 </br> 5.3.0-19-泛型到 5.3.0-24-泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 1012-azure 到 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-泛型到 4.15.0-66-泛型 </br> 4.18.0-13-泛型到 4.18.0-25-泛型 </br> 5.0.0-15-泛型到 5.0.0-32-泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 1012-azure 到 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0--4.15.0-泛型到-泛型 </br> 4.18.0-13-泛型到 4.18.0-25-泛型 </br> 5.0.0-15-泛型到 5.0.0-29-泛型 </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 1012-azure 到 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 Debian 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
Debian 7 | 9.28、9.29、9:30、9.31 | 3.2.0-4-amd64 到 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29、9:30、9.31 | 3.16.0-4-amd64 到 3.16.0-旁1/-0，4.9.0. bpo 到4.9.0 旁 1/-0-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 到 3.16.0-10-amd64，4.9.0 旁 1/-0. bpo 到4.9.0 旁 1/-0-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 SUSE Linux Enterprise Server 12 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | 9.32 | 所有[股票 SUSE 12 SP1、SP2、SP3、SP4 内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 到 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | 9.31 | 所有[股票 SUSE 12 SP1、SP2、SP3、SP4 内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 到 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | 9.30 | 所有[股票 SUSE 12 SP1、SP2、SP3、SP4 内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 到 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 （SP1、SP2、SP3、SP4） | 9.29 | 所有[股票 SUSE 12 SP1、SP2、SP3、SP4 内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure，</br>4.12.14-6.3-azure 到 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持 SUSE Linux Enterprise Server 15 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 和 15 SP1 | 9.32 | 所有[股票 SUSE 15 和15个内核](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15)都受支持。</br></br> 4.12.14-5.5-azure 到 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>复制的计算机 - Linux 文件系统/来宾存储

* 文件系统：ext3、ext4、ReiserFS（仅限 Suse Linux Enterprise Server）和 XFS、BTRFS
* 卷管理器：LVM2
* 多路径软件：设备映射程序


## <a name="replicated-machines---compute-settings"></a>复制的计算机 - 计算设置

**设置** | **支持** | **详细信息**
--- | --- | ---
大小 | 具有至少 2 个 CPU 内核和 1 GB RAM 的任意 Azure VM 大小 | 验证 [Azure 虚拟机大小](../virtual-machines/windows/sizes.md)。
可用性集 | 支持 | 如果为使用默认选项的 Azure VM 启用复制，则会基于源区域设置自动创建可用性集。 可以修改这些设置。
可用性区域 | 支持 |
混合使用权益 (HUB) | 支持 | 如果源 VM 启用了 HUB 许可证，则测试故障转移或故障转移 VM 也使用 HUB 许可证。
虚拟机规模集 | 不支持 |
Azure 库映像 — 由 Microsoft 发布 | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
Azure 库映像 - 由第三方发布 | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
自定义映像 - 由第三方发布 | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
使用 Site Recovery 迁移的 VM | 支持 | 如果使用 Site Recovery 将 VMware VM 或物理计算机迁移到 Azure，则需要卸载计算机上运行的旧版移动服务，并在重启计算机后将该计算机复制到另一个 Azure 区域。
RBAC 策略 | 不支持 | Vm 上基于角色的访问控制（RBAC）策略不会复制到目标区域中的故障转移 VM。
扩展 | 不支持 | 扩展不会复制到目标区域中的故障转移 VM。 需要在故障转移后手动安装它。

## <a name="replicated-machines---disk-actions"></a>复制的计算机 - 磁盘操作

**Action** | **详细信息**
-- | ---
调整复制的 VM 上的磁盘大小 | 故障转移之前在源 VM 上受支持。 无需禁用/重新启用复制。<br/><br/> 如果在故障转移后更改源 VM，则不会捕获更改。<br/><br/> 如果在故障转移后更改 Azure VM 上的磁盘大小，则 Site Recovery 不会捕获更改，故障回复将是原始 VM 大小。
将磁盘添加到复制的 VM | 支持

## <a name="replicated-machines---storage"></a>复制的计算机 - 存储

此表汇总了对 Azure VM OS 磁盘、数据磁盘和临时磁盘的支持。

- 请务必遵循适用于 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 和 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM 的 VM 磁盘限制以及目标，以避免任何性能问题。
- 如果使用默认设置进行部署，Site Recovery 会根据源设置自动创建磁盘和存储帐户。
- 如果自定义，请确保遵循指南。

组件 | **支持** | **详细信息**
--- | --- | ---
OS 磁盘的最大大小 | 2048 GB | [深入了解 ](../virtual-machines/windows/managed-disks-overview.md)VM 磁盘相关信息。
临时磁盘 | 不支持 | 始终从复制中排除临时磁盘。<br/><br/> 请勿在临时磁盘上存储任何持久性数据。 [了解详细信息](../virtual-machines/windows/managed-disks-overview.md)。
数据磁盘的最大大小 | 托管磁盘为 8192 GB<br></br>4095 GB （适用于非托管磁盘）|
数据磁盘最小大小 | 不限制非托管磁盘。 2 GB，适用于托管磁盘 | 
数据磁盘的最大数量 | 最多为 64，根据对特定的 Azure VM 大小的支持而定 | [深入了解 ](../virtual-machines/windows/sizes.md)VM 大小相关信息。
数据磁盘更改率 | 每个高级存储的磁盘最大为 10 MBps。 每个标准存储的磁盘最大为 2 MBps。 | 如果磁盘上的平均数据更改率持续高于最大值，复制将跟不上。<br/><br/>  但是，如果偶尔超出最大值，则复制可跟上，但可能会看到稍有延迟的恢复点。
数据磁盘 - 标准存储帐户 | 支持 |
数据磁盘 - 高级存储帐户 | 支持 | 如果 VM 将磁盘分散在高级和标准存储帐户上，则可以为每个磁盘选择不同的目标存储帐户，以确保在目标区域中具有相同的存储配置。
托管磁盘 - 标准 | 在支持 Azure Site Recovery 的 Azure 区域中受支持。 |
托管磁盘 - 高级 | 在支持 Azure Site Recovery 的 Azure 区域中受支持。 |
标准 SSD | 支持 |
冗余 | LRS 和 GRS 受支持。<br/><br/> ZRS 不受支持。
冷存储和热存储 | 不支持 | 冷存储和热存储不支持 VM 磁盘
存储空间 | 支持 |
静态加密 (SSE) | 支持 | SSE 是存储帐户的默认设置。   
静态加密（CMK） | 支持 | 托管磁盘支持软件和 HSM 密钥    
适用于 Windows OS 的 Azure 磁盘加密 (ADE) | 支持具有托管磁盘的 Vm。 不支持使用非托管磁盘的 Vm |
适用于 Linux OS 的 Azure 磁盘加密 (ADE) | 支持 |
热添加 | 支持 | 使用托管磁盘的 Vm 支持为添加到复制的 Azure VM 的数据磁盘启用复制。
热删除磁盘 | 不支持 | 如果在 VM 上删除数据磁盘，则需要禁用复制，并再次为 VM 启用复制。
排除磁盘 | 支持。 必须使用[Powershell](azure-to-azure-exclude-disks.md)进行配置。 |  默认情况下会排除临时磁盘。
存储空间直通  | 崩溃一致恢复点支持。 不支持应用程序一致恢复点。 |
横向扩展文件服务器  | 崩溃一致恢复点支持。 不支持应用程序一致恢复点。 |
LRS | 支持 |
GRS | 支持 |
RA-GRS | 支持 |
ZRS | 不支持 |
冷存储和热存储 | 不支持 | 冷存储和热存储不支持虚拟机磁盘
虚拟网络的 Azure 存储防火墙  | 支持 | 如果限制对存储帐户的虚拟网络访问，请启用 "[允许受信任的 Microsoft 服务](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)"。
常规用途 V2 存储帐户（冷热存储层） | 支持 | 与常规用途 V1 存储帐户相比，事务成本显著增加
第2代（UEFI 启动） | 支持

>[!IMPORTANT]
> 若要避免性能问题，请确保遵循适用于[Linux](../virtual-machines/linux/disk-scalability-targets.md)或[WINDOWS](../virtual-machines/windows/disk-scalability-targets.md) vm 的 VM 磁盘可伸缩性和性能目标。 如果使用默认设置，Site Recovery 会根据源配置创建所需的磁盘和存储帐户。 如果自定义和选择自己的设置，请遵循源 Vm 的磁盘可伸缩性和性能目标。

## <a name="limits-and-data-change-rates"></a>限制和数据更改率

下表汇总了 Site Recovery 限制。

- 这些限制基于我们的测试，但显然并未涵盖所有可能的应用程序 i/o 组合。
- 实际结果可能因应用的 i/o 组合而异。
- 每个磁盘数据更改和每个虚拟机数据改动有两个限制。

**存储目标** | **平均源磁盘 i/o** |**平均源磁盘数据变动量** | **每天的总源磁盘数据变动量**
---|---|---|---
标准存储 | 8 KB | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB  | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |  每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |20 MB/秒 | 每个磁盘 1684 GB

## <a name="replicated-machines---networking"></a>复制的计算机 - 网络
**设置** | **支持** | **详细信息**
--- | --- | ---
NIC | 特定 Azure VM 大小支持的最大数量 | 在故障转移期间创建 VM 时会创建 NIC。<br/><br/> 故障转移 VM 上的 NIC 数目取决于启用复制时源 VM 上的 NIC 数目。 如果在启用复制后添加或删除 NIC，不会影响故障转移后复制的 VM 上的 NIC 数目。 另请注意，故障转移后的 Nic 顺序不一定与原始订单相同。
内部负载均衡器 | 支持 | 在恢复计划中使用 Azure 自动化脚本关联预配置的负载均衡器。
内部负载均衡器 | 支持 | 在恢复计划中使用 Azure 自动化脚本关联预配置的负载均衡器。
公共 IP 地址 | 支持 | 将现有的公共 IP 地址与 NIC 关联。 或者，在恢复计划中使用 Azure 自动化脚本创建公共 IP 地址并将其与 NIC 关联。
NIC 上的 NSG | 支持 | 在恢复计划中使用 Azure 自动化脚本将 NSG 与 NIC 关联。
子网上的 NSG | 支持 | 在恢复计划中使用 Azure 自动化脚本将 NSG 与子网关联。
保留（静态）IP 地址 | 支持 | 如果源 VM 上的 NIC 具有静态 IP 地址，并且目标子网具有相同的可用 IP 地址，则会将它分配给故障转移 VM。<br/><br/> 如果目标子网没有相同的可用 IP 地址，则为 VM 保留子网中的某个可用 IP 地址。<br/><br/> 此外可以在“复制的项” **“设置”** “计算和网络” > “网络接口” >  > 中指定固定的 IP 地址和子网。
动态 IP 地址 | 支持 | 如果源上的 NIC 具有动态 IP 地址，故障转移 VM 上的 NIC 也默认为动态。<br/><br/> 如果有需要，可以将其修改为固定的 IP 地址。
多个 IP 地址 | 不支持 | 故障转移具有多个 IP 地址的 NIC 的 VM 时，只保留源区域中 NIC 的主 IP 地址。 若要分配多个 IP 地址，可以将 Vm 添加到[恢复计划](recovery-plan-overview.md)，并附加脚本来向计划分配其他 IP 地址，也可以在故障转移后手动或使用脚本进行更改。 
流量管理器     | 支持 | 可以预配置流量管理器，这样在正常情况下，流量路由到源区域中的终结点；发生故障转移时，流量路由到目标区域中的终结点。
Azure DNS | 支持 |
自定义 DNS  | 支持 |
未经身份验证的代理 | 支持 | [了解详细信息](site-recovery-azure-to-azure-networking-guidance.md)    
经过身份验证的代理 | 不支持 | 如果 VM 对出站连接使用经过身份验证的代理，则不能使用 Azure Site Recovery 复制该 VM。    
到本地的 VPN 站点到站点连接<br/><br/>（有或没有 ExpressRoute）| 支持 | 确保 Udr 和 Nsg 的配置方式，使 Site Recovery 的流量不会路由到本地。 [了解详细信息](site-recovery-azure-to-azure-networking-guidance.md)    
VNET 到 VNET 连接 | 支持 | [了解详细信息](site-recovery-azure-to-azure-networking-guidance.md)  
虚拟网络服务终结点 | 支持 | 若要限制对存储帐户的虚拟网络访问，请确保允许受信任的 Microsoft 服务访问存储帐户。
加速网络 | 支持 | 必须在源 VM 上启用加速网络。 [了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。



## <a name="next-steps"></a>后续步骤
- 有关复制 Azure Vm 的详细阅读[网络指南](site-recovery-azure-to-azure-networking-guidance.md)。
- 通过[复制 Azure VM](site-recovery-azure-to-azure.md) 来部署灾难恢复。
