---
title: 用于通过 Azure 资源移动器将 Azure Vm 移到另一个区域的支持矩阵
description: 查看支持通过 Azure 资源移动器在区域之间移动 Azure Vm。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 10/11/2020
ms.author: raynew
ms.openlocfilehash: d71181c5f45ab63febae7288f07189dc52ea12fd
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945907"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>支持在 Azure 区域之间移动 Azure Vm

本文汇总了使用资源移动器在 Azure 区域之间移动虚拟机和相关网络资源时的支持和先决条件。

> [!IMPORTANT]
> Azure 资源转移器目前提供预览版。


## <a name="windows-vm-support"></a>Windows VM 支持

资源移动器支持运行这些 Windows 操作系统的 Azure Vm。

**操作系统** | **详细信息**
--- | ---
Windows Server 2019 | 支持 Server Core、带桌面体验的服务器。
Windows Server 2016  | 支持 Server Core、带桌面体验的服务器。
Windows Server 2012 R2 | 支持。
Windows Server 2012 | 支持。
Windows Server 2008 R2 SP1/SP2 | 支持。<br/><br/> 对于运行 Windows Server 2008 R2 SP1/SP2 的计算机，需要安装 (SSU) 和[sha-1 更新](https://support.microsoft.com/help/4474419)的 Windows[服务堆栈更新](https://support.microsoft.com/help/4490628)。  从 2019 年 9 月开始不再支持 SHA-1。如果未启用 SHA-2 代码签名，则无法按预期方式安装/升级代理扩展。 详细了解 [SHA-2 升级和要求](https://aka.ms/SHA-2KB)。
Windows 10 (x64) | 支持。
Windows 8.1 (x64) | 支持。
Windows 8 (x64) | 支持。
Windows 7 (x64) SP1 和更高版本 | 在运行 Windows 7 SP1 的计算机上安装 Windows [服务堆栈更新 (SSU) ](https://support.microsoft.com/help/4490628) 和 [sha-1 更新](https://support.microsoft.com/help/4474419) 。  2019年9月不支持 SHA-1，如果未启用 SHA-1 代码签名，则 "准备" 步骤将不会成功。 详细了解 [SHA-2 升级和要求](https://aka.ms/SHA-2KB)。


## <a name="linux-vm-support"></a>Linux VM 支持

资源移动支持运行这些 Linux 操作系统的 Azure Vm。

**操作系统** | **详细信息**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、8.0、8.1
Ubuntu 14.04 LTS Server | [受支持的内核版本](#supported-ubuntu-kernel-versions)
Ubuntu 16.04 LTS Server | [受支持的内核版本](#supported-ubuntu-kernel-versions)<br/><br/> 使用基于密码的身份验证和登录以及用于配置云 Vm 的云 init 包的 Ubuntu 服务器，可能会在故障转移时禁用基于密码的登录 (具体取决于云初始化配置) 。 可以在虚拟机上重新启用基于密码的登录，方法是在 Azure 门户中已故障转移的 VM 的支持 > 疑难解答 > 设置 "菜单 (重置密码。
Ubuntu 18.04 LTS Server | [支持的内核版本](#supported-ubuntu-kernel-versions)。
Debian 7 | [支持的内核版本](#supported-debian-kernel-versions)。
Debian 8 | [支持的内核版本](#supported-debian-kernel-versions)。
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4。 [受支持的内核版本](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 和 15 SP1。 [（受支持的内核版本）](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> 运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3、4 和 5（UEK3、UEK4、UEK5）


### <a name="supported-ubuntu-kernel-versions"></a>支持的 Ubuntu 内核版本

**版本** | **内核版本** 
--- | --- 
14.04 LTS |  3.13.0-24-generic 到 3.13.0-170-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-148-generic、<br/>4.15.0-1023-azure 到 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-generic 到 4.4.0-171-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-74-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-generic 到 4.15.0-74-generic </br> 4.18.0-13-generic 到 4.18.0-25-generic </br> 5.0.0-15-generic 到 5.0.0-37-generic </br> 5.3.0-19-generic 到 5.3.0-24-generic </br> 4.15.0-1009-azure 到 4.15.0-1037-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1028-azure </br> 5.3.0-1007-azure 到 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>支持的 Debian 内核版本 

**版本** |  **内核版本** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 到 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64 到 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64 到 3.16.0-10-amd64，4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>支持的 SUSE Linux Enterprise Server 12 内核版本 

**版本** | **内核版本** 
--- |  --- 
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4） |  支持所有[库存 SUSE 12 SP1、SP2、SP3、SP4 内核](https://www.suse.com/support/kb/doc/?id=000019587)。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>支持 SUSE Linux Enterprise Server 15 内核版本

**版本** | **内核版本** |
--- |  --- |
SUSE Linux Enterprise Server 15 和 15 SP1 |  支持所有库存 SUSE 15 和 15 SP1 内核。</br></br> 4.12.14-5.5-azure 到 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>支持的 Linux 文件系统/来宾存储

* 文件系统：ext3、ext4、XFS、BTRFS
* 卷管理器：LVM2
* 多路径软件：设备映射器


## <a name="supported-vm-compute-settings"></a>支持的 VM 计算设置

**设置** | **支持** | **详细信息**
--- | --- | ---
大小 | 至少具有两个 CPU 内核和 1 GB RAM 的任何 Azure VM 大小 | 验证 [Azure 虚拟机大小](../virtual-machines/sizes-general.md)。
可用性集 | 目前不支持 | 如果使用默认选项将具有可用性集的 Azure VM 添加到移动集合，则准备过程将失败。 可以选择将 VM 移到可用性区域，或将其作为单个实例 VM 来移动。 你可以在 "编辑目标属性" 页中修改这些设置。
可用性区域 | 支持 | 支持，具体取决于目标区域支持。
Microsoft (发布 Azure 库映像)  | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
 (第三方发布的 Azure 库映像)   | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
 (第三方发布的自定义映像) | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
使用 Site Recovery 的 Vm | 不支持 | 使用后端 Site Recovery 在虚拟机区域之间移动资源。 如果已在使用 Site Recovery，请禁用复制，然后启动准备进程。
RBAC 策略 | 不支持 | 基于角色的访问控制 (在虚拟机上的 RBAC) 策略不会复制到目标区域中的 VM。
扩展 | 不支持 | 扩展不会复制到目标区域中的 VM。 移动完成后，手动安装它们。


## <a name="supported-vm-storage-settings"></a>支持的 VM 存储设置

此表汇总了对 Azure VM OS 磁盘、数据磁盘和临时磁盘的支持。 请务必遵循适用于 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 和 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM 的 VM 磁盘限制以及目标，以避免任何性能问题。

> [!NOTE]
> 目标 VM 大小应等于或大于源 VM 大小。 用于验证的参数包括：数据磁盘计数、Nic 计数、可用 Cpu、内存（GB）。 如果不是，则会发出错误。


**组件** | **支持** | **详细信息**
--- | --- | ---
OS 磁盘的最大大小 | 2048 GB | [深入了解 ](../virtual-machines/windows/managed-disks-overview.md)VM 磁盘相关信息。
临时磁盘 | 不支持 | 临时磁盘始终从准备进程中排除。<br/><br/> 请勿在临时磁盘上存储任何持久性数据。 [了解详细信息](../virtual-machines/windows/managed-disks-overview.md#temporary-disk)。
数据磁盘的最大大小 | 托管磁盘为 8192 GB
数据磁盘最小大小 |  托管磁盘为 2 GB |
数据磁盘的最大数量 | 最多为 64，根据对特定的 Azure VM 大小的支持而定 | [深入了解 ](../virtual-machines/windows/sizes.md)VM 大小相关信息。
数据磁盘更改率 | 每个高级存储的磁盘最大为 10 MBps。 每个标准存储的磁盘最大为 2 MBps。 | 如果磁盘上的平均数据更改率连续高于最大值，则准备工作不会赶上。<br/><br/>  但是，如果最大值不确定，准备可能会赶上，但你可能会看到稍微延迟的恢复点。
数据磁盘 (标准存储帐户)  | 不支持。 | 将存储类型更改为托管磁盘，然后尝试移动 VM。
数据磁盘 (高级存储帐户)  | 不支持 | 将存储类型更改为托管磁盘，然后尝试移动 VM。
托管磁盘 (标准)  | 支持  |
托管磁盘 (高级)  | 支持 |
标准 SSD | 支持 |
第 2 代（UEFI 启动） | 支持
启动诊断存储帐户 | 不支持 | 将 VM 移至目标区域后重新启用它。

### <a name="limits-and-data-change-rates"></a>限制和数据更改率

下表汇总了基于测试的限制。 这并不涵盖所有可能的应用程序 i/o 组合。 实际结果因应用程序 i/o 组合而异。 需要考虑两个限制：每个磁盘的数据变动量，以及每个 VM 的数据变动量。

**存储目标** | **平均源磁盘 I/O** |**平均源磁盘数据变动量** | **每天的总源磁盘数据变动量**
---|---|---|---
标准存储 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |    每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |20 MB/秒 | 每个磁盘 1684 GB

## <a name="supported-vm-networking-settings"></a>支持的 VM 网络设置

**设置** | **支持** | **详细信息**
--- | --- | ---
NIC | 支持 | 指定目标区域中的现有资源，或在准备过程中创建新的资源。 
内部负载均衡器 | 支持 | 指定目标区域中的现有资源，或在准备过程中创建新的资源。  
公共负载均衡器 | 目前不支持 | 指定目标区域中的现有资源，或在准备过程中创建新的资源。  
公共 IP 地址 | 支持 | 指定目标区域中的现有资源，或在准备过程中创建新的资源。<br/><br/> 公共 IP 地址是特定于区域的，在移动后不会保留在目标区域中。 修改网络设置 (包括目标位置) 的负载均衡规则时，请记住这一点。
网络安全组 | 支持 | 指定目标区域中的现有资源，或在准备过程中创建新的资源。  
保留（静态）IP 地址 | 支持 | 当前无法配置此设置。 该值默认为源值。 <br/><br/> 如果源 VM 上的 NIC 具有静态 IP 地址，并且目标子网具有相同的可用 IP 地址，则会将其分配给目标 VM。<br/><br/> 如果目标子网没有相同的 IP 地址，则 VM 的启动移动会失败。
动态 IP 地址 | 支持 | 当前无法配置此设置。 该值默认为源值。<br/><br/> 如果源上的 NIC 具有动态 IP 寻址，则默认情况下，目标 VM 上的 NIC 也是动态的。
IP 配置 | 支持 | 当前无法配置此设置。 该值默认为源值。

## <a name="outbound-access-requirements"></a>出站访问要求

要移动的 Azure Vm 需要出站访问权限。


### <a name="url-access"></a>URL 访问

 如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 URL：

**名称** | **Azure 公有云** | **详细信息** 
--- | --- | --- 
存储 | `*.blob.core.windows.net`  | 允许将数据从 VM 写入源区域中的缓存存储帐户。 
Azure Active Directory | `login.microsoftonline.com`  | 向 Site Recovery 服务 URL 提供授权和身份验证。 
复制 | `*.hypervrecoverymanager.windowsazure.com` | 允许 VM 与 Site Recovery 服务进行通信。 
服务总线 | `*.servicebus.windows.net` | 允许 VM 写入 Site Recovery 监视和诊断数据。 

## <a name="nsg-rules"></a>NSG 规则
如果使用的是网络安全组 (NSG) 规则来控制出站连接，请创建这些 [服务标记](../virtual-network/service-tags-overview.md) 规则。 每个规则都应允许 HTTPS (443) 上的出站访问。
- 为源区域创建存储标记规则。
- 创建 *AzureSiteRecovery* 标记规则，以允许访问任何区域中的 Site Recovery 服务。 此标记与这些其他标记具有依赖关系，因此，你需要为这些标记创建规则：
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- 建议在非生产环境中测试规则。 [查看一些示例](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。 


## <a name="next-steps"></a>后续步骤

尝试使用资源移动器将 [AZURE VM 移](tutorial-move-region-virtual-machines.md) 到另一个区域。
