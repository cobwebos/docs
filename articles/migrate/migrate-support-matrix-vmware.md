---
title: VMware 评估和迁移 Azure Migrate 支持矩阵
description: 汇总了使用 Azure Migrate 服务评估和迁移 VMware Vm 到 Azure 的支持设置和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 00ca474a6cb32c7ad3e47aef750126e958e43501
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372447"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>用于 VMware 评估和迁移的支持矩阵

你可以使用[Azure Migrate 服务](migrate-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文总结了用于评估和迁移本地 VMware Vm 的支持设置和限制。


## <a name="vmware-scenarios"></a>VMware 方案

下表汇总了 VMware Vm 支持的方案。

**部署** | **详细信息**
--- | ---
**评估本地 VMware Vm** | [设置](tutorial-prepare-vmware.md)第一次评估。<br/><br/> [运行](scale-vmware-assessment.md)大规模评估。
**迁移 VMware VM** | 你可以使用无代理迁移进行迁移, 但有一些限制, 或者使用基于代理的迁移。 [了解详细信息](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
Azure 权限 | 你需要订阅中的 "参与者" 或 "所有者" 权限才能创建 Azure Migrate 项目。
VMware 限制  | 在单个项目中最多评估35000个 VMware Vm。

项目可以包括 VMware Vm 和 Hyper-v Vm, 最高可达评估限制。

**Geography**可以在多个地理区域中创建 Azure Migrate 项目。 即使你只能在这些地理位置创建项目, 你仍可以评估或迁移其他目标位置的计算机。 项目地域仅用于存储已发现的元数据。


 **地域** | **元数据存储位置**
 --- | ---
 Azure 政府 | US Gov 弗吉尼亚州
 亚太区 | 东南亚或东亚
 欧洲 | 欧洲南部或西欧
 英国 | 英国南部或英国西部
 美国 | 美国中部或美国西部2


 > [!NOTE]
 > Azure 政府版支持目前仅适用于[较旧版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)的 Azure Migrate。


## <a name="assessment-vmware-server-requirements"></a>评估-VMware 服务器要求

此表总结了 VMware 虚拟化服务器的评估支持和限制。

**支持** | **详细信息**
--- | ---
**vCenter 服务器** | 要评估的 VMware Vm 必须由运行5.5、6.0、6.5 或6.7 的一个或多个 vCenter 服务器进行管理。

## <a name="assessment-vcenter-server-permissions"></a>评估-vCenter Server 权限

若只进行评估，需要一个适用于 vCenter Server 的只读帐户。

## <a name="assessment-appliance-requirements"></a>评估-设备要求

**支持** | **详细信息**
--- | ---
**ESXi** | 设备 VM 必须部署在运行版本5.5 或更高版本的 ESXi 主机上。
**Azure Migrate 项目** | 设备可以与单个项目关联。
**vCenter Server** | 设备最多可在 vCenter Server 上发现 10000 VMware Vm。<br/> 设备可以连接到一个 vCenter Server。


## <a name="assessment-url-access-requirements"></a>评估-URL 访问要求

Azure Migrate 设备需要 internet 连接到 internet。

- 部署设备时, Azure Migrate 会对下表中汇总的 Url 进行连接性检查。
- 如果你使用的是基于 URL 的防火墙, 则允许访问这些 Url, 确保代理解析查找 Url 时收到的任何 CNAME 记录。

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到 Azure 门户中的 Azure Migrate。
*.windows.net | 登录到 Azure 订阅。
*.microsoftonline.com | 为设备创建 Active Directory 应用程序, 以便与 Azure Migrate 服务通信。
management.azure.com | 为设备创建 Active Directory 应用程序, 以便与 Azure Migrate 服务通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.azure.net | 管理 Azure Key Vault 中的机密。
*.servicebus.windows.net | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。


## <a name="assessment-port-requirements"></a>评估-端口要求

**设备** | **Connection**
--- | ---
设备 | TCP 端口3389上的入站连接, 允许到设备的远程桌面连接。<br/> 端口44368上的入站连接, 使用以下 URL 远程访问设备管理应用: https://< 设备-ip 或名称 >: 44368 <br/>端口443上的出站连接, 将发现和性能元数据发送到 Azure Migrate。
vCenter Server | TCP 端口443上的入站连接, 使设备能够收集配置和性能元数据以进行评估。 <br/> 默认情况下, 设备会在端口443上连接到 vCenter。 如果 vCenter 服务器侦听其他端口, 则可以在设置发现时修改端口。


## <a name="agentless-migration-vmware-server-requirements"></a>无代理迁移-VMware 服务器要求

此表总结了 VMware 虚拟化服务器的评估支持和限制。

**支持** | **详细信息**
--- | ---
**vCenter 服务器** | 使用无代理迁移迁移的 VMware Vm 必须由运行5.5、6.0、6.5 或6.7 的一个或多个 vCenter 服务器进行管理。

## <a name="agentless-migration-vcenter-server-permissions"></a>无代理迁移-vCenter Server 权限

**权限** | **详细信息**
--- | ---
Datastore.Browse | 允许浏览 VM 日志文件来排除快照创建和删除故障。
Datastore.LowLevelFileOperations | 允许 "数据存储浏览器" 中的读/写/删除/重命名操作, 用于排查快照创建和删除的问题。
VirtualMachine.Configuration.DiskChangeTracking | 允许启用或禁用 VM 磁盘的更改跟踪, 以便在快照之间请求更改的数据块
VirtualMachine.Configuration.DiskLease | 允许 VM 使用磁盘租约操作, 使用 VMware vSphere 虚拟磁盘开发工具包 (VDDK) 读取磁盘。
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | 允许使用 VDDK 打开虚拟机上的磁盘。
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | 允许对与 VM 关联的文件执行读取操作, 下载日志, 并在发生故障时进行故障排除。
VirtualMachine.SnapshotManagement.* | 允许创建和管理用于复制的 VM 快照。
虚拟机。 | 允许 VM 在迁移到 Azure 期间关闭。


## <a name="agentless-migration-vmware-vm-requirements"></a>无代理迁移-VMware VM 要求

**支持** | **详细信息**
--- | ---
**受支持的操作系统** | Azure 支持的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统可以使用无代理迁移进行迁移。
**Azure 所需的更改** | 某些 Vm 可能需要更改才能在 Azure 中运行。 Azure Migrate 会自动对以下操作系统进行这些更改:<br/> -Red Hat Enterprise Linux 6.5 +、7.0 +<br/> -CentOS 6.5 +、7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8<br/><br/> 对于其他操作系统, 需要在迁移之前手动进行调整。 相关文章包含有关如何执行此操作的说明。
**Linux 启动** | 如果/boot 位于专用分区上, 则它应驻留在 OS 磁盘上, 而不会分布在多个磁盘上。<br/> 如果/boot 是根 (/) 分区的一部分, 则 "/" 分区应在 OS 磁盘上, 而不是在其他磁盘上。
**UEFI 启动** | 迁移不支持具有 UEFI 引导的 Vm。
**加密磁盘/卷** | 不支持对具有加密磁盘/卷的 Vm 进行迁移。
**RDM/传递磁盘** | 如果 Vm 具有 RDM 或传递磁盘, 则这些磁盘不会复制到 Azure。
**NFS** | 不会复制装载为 Vm 上的卷的 NFS 卷。
**目标磁盘** | Vm 只能迁移到 Azure 中的托管磁盘 (标准 HHD、高级 SSD)。


## <a name="agentless-migration-appliance-requirements"></a>无代理迁移-设备要求


**支持** | **详细信息**
--- | ---
**ESXi** | 设备 VM 必须部署在运行版本5.5 或更高版本的 ESXi 主机上。
**Azure Migrate 项目** | 设备可以与单个项目关联。
**vCenter Server** | 设备最多可在 vCenter Server 上发现 10000 VMware Vm。<br/> 设备可以连接到一个 vCenter Server。
**VDDK** | 如果在 Azure Migrate 服务器迁移的情况下运行无代理迁移, 则必须在设备 VM 上安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。

## <a name="agentless-migration-url-access-requirements"></a>无代理迁移-URL 访问要求

Azure Migrate 设备需要 internet 连接到 internet。

- 部署设备时, Azure Migrate 会对下表中汇总的 Url 进行连接性检查。
- 如果你使用的是基于 URL 的防火墙, 则允许访问这些 Url, 确保代理解析查找 Url 时收到的任何 CNAME 记录。

**URL** | **详细信息**  
--- | ---
*.portal.azure.com | 导航到 Azure 门户中的 Azure Migrate。
*.windows.net | 登录到 Azure 订阅。
*.microsoftonline.com | 为设备创建 Active Directory 应用程序, 以便与 Azure Migrate 服务通信。
management.azure.com | 为设备创建 Active Directory 应用程序, 以便与 Azure Migrate 服务通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.azure.net | 管理 Azure Key Vault 中的机密。
*.servicebus.windows.net | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。


## <a name="agentless-migration-port-requirements"></a>无代理迁移-端口要求

**设备** | **Connection**
--- | ---
设备 | 用于将复制的数据上传到 Azure 的出站 TCP 端口 3389, 以及用于复制和迁移的 Azure Migrate。
vCenter Server | TCP 端口443上的入站连接, 使设备能够协调复制-创建快照、复制数据、发布快照
vSphere/EXSI 主机 | TCP 端口902上的入站, 用于从快照复制数据。


## <a name="agent-based-migration-vmware-server-requirements"></a>基于代理的迁移-VMware 服务器要求

此表总结了 VMware 虚拟化服务器的评估支持和限制。

**支持** | **详细信息**
--- | ---
**vCenter 服务器/ESXI** | 迁移的 VMware Vm 必须由运行5.5、6.0、6.5 或6.7 的一个或多个 vCenter 服务器管理, 或在 vSphere 版本为5.5、6.0、6.5 或6.7 的 ESXI 主机上运行。

### <a name="agent-based-migration-vcenter-server-permissions"></a>基于代理的迁移-vCenter Server 权限

**权限** | **详细信息**
--- | ---
Datastore.AllocateSpace | 允许 VM、快照、克隆或虚拟磁盘的数据存储空间分配。
Datastore.Browse | 允许浏览 VM 日志文件来排除快照创建和删除故障。
Datastore.LowLevelFileOperations | 允许数据存储浏览器中的读取、写入、删除和重命名操作, 用于排查快照创建/删除问题。
Datastore.UpdateVirtualMachineFiles | Resignatured 数据存储后, 允许在数据存储中更新 VM 文件的路径。
Network.AssignNetwork | 允许将网络分配给 VM 资源。
AssignVirtualMachineToResourcePool | 允许将 VM 分配到资源池。
Resource.MigratePoweredOffVirtualMachine | 允许将关闭的 VM 迁移到另一个资源池或主机。
Resource.MigratePoweredOnVirtualMachine | 允许使用启用了虚拟机的虚拟机迁移到不同的资源池或主机。
Tasks.CreateTask | 允许扩展创建用户定义任务。
Tasks.UpdateTask | 允许扩展更新用户定义的任务。
VirtualMachine。 | 允许配置 VM 选项和设备。
虚拟机。 AnswerQuestion | 允许解决 VM 状态转换或运行时错误的问题。
虚拟机。 DeviceConnection | 允许更改 VM disconnectable 虚拟设备的已连接状态。
虚拟机。 ConfigureCDMedia | 允许配置虚拟 DVD 或 cd-rom 设备。
虚拟机。 ConfigureFloppyMedia | 允许配置虚拟软盘设备。
虚拟机。关机 | 允许 VM 在迁移到 Azure 期间关闭。
虚拟机。 PowerOn | 允许打开已关闭的 VM, 并恢复挂起的 VM。
虚拟机。 VMwareToolsInstall | 允许将 VMware 工具 CD 安装程序装载和卸载为来宾操作系统的 cd-rom。
VirtualMachine.Inventory.CreateNew | 允许创建 VM 和分配所需的资源。
VirtualMachine.Inventory.Register | 允许将现有 VM 添加到 vCenter Server 或主机清单中。
VirtualMachine.Inventory.Unregister | 允许从 vCenter Server 或主机清单中注销 Vm。
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | 允许对与 VM 关联的文件进行写入操作, 包括 .vmx、磁盘、日志和 nvram。
VirtualMachine.Provisioning.AllowVirtualMachineDownload | 允许对与 VM 关联的文件执行读取操作, 以便下载日志以进行故障排除。
VirtualMachine.SnapshotManagement.RemoveSnapshot | 允许从快照历史记录中删除快照。

## <a name="agent-based-migration-replication-appliance-requirements"></a>基于代理的迁移-复制设备要求

表中总结了用于 VMware Vm 和物理服务器的基于代理的迁移所需的[复制装置](migrate-replication-appliance.md)的要求 Azure Migrate 服务器迁移。

> [!NOTE]
> 当使用 Azure Migrate 中心提供的 .OVA 模板设置复制设备时, 设备将运行 Windows Server 2016 并符合支持要求。 如果在物理服务器上手动设置复制设备, 请确保它符合要求。



组件 | 要求
--- | ---
 | **VMware 设置**(VMware VM 设备)
**PowerCLI** | 如果复制设备在 VMware VM 上运行, 则应安装[PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) 。
**NIC 类型** | VMXNET3 (如果设备是 VMware VM)
 | **硬件设置**
CPU 核心数 | 8
RAM | 16 GB
磁盘数量 | 这操作系统磁盘、进程服务器缓存磁盘和保留驱动器。
可用磁盘空间 (缓存) | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
**软件设置** |
操作系统 | Windows Server 2016 或 Windows Server 2012 R2
操作系统区域设置 | 美国英语
TLS | 应启用 TLS 1.2。
.NET Framework | 应在计算机上安装 .NET Framework 4.6 或更高版本 (启用了强密码。
MySQL | MySQL 应安装在设备上。<br/> 应安装 MySQL。 可以手动安装, 也可以在设备部署期间安装 Site Recovery。
其他应用 | 不应在复制设备上运行其他应用。
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置
**网络设置** |
IP 地址类型 | Static
端口 | 443（控制通道协调）<br>9443（数据传输）
NIC 类型 | VMXNET3

### <a name="replication-appliance-url-access"></a>复制设备 URL 访问

复制设备需要对这些 Url 的访问权限。

**URL** | **详细信息**
--- | ---
\*.backup.windowsazure.com | 用于复制的数据传输和协调
\*.store.core.windows.net | 用于复制的数据传输和协调
\*.blob.core.windows.net | 用于访问存储所复制数据的存储帐户
\*.hypervrecoverymanager.windowsazure.com | 用于复制管理操作和协调
https:\//management.azure.com | 用于复制管理操作和协调
*.services.visualstudio.com | 用于遥测数据（可选）
time.nist.gov | 用于检查系统时间与全球时间之间的时间同步。
time.windows.com | 用于检查系统时间与全球时间之间的时间同步。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF 安装程序需要对这些 Url 的访问权限。 它们由 Azure Active Directory 用于访问控制和标识管理
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下载


#### <a name="mysql-installation-options"></a>MySQL 安装选项

可以使用以下方法之一在复制设备上安装 MySQL。

**安装** | **详细信息**
--- | ---
手动下载和安装 | 下载 MySQL 应用程序 & 将其放在 C:\Temp\ASRSetup 文件夹中, 然后手动安装。<br/> 设置设备 MySQL 时, 将显示 "已安装"。
不联机下载 | 将 MySQL 安装程序应用程序放在 C:\Temp\ASRSetup. 文件夹中。 安装设备并单击下载并安装 MySQL 时, 安装程序将使用所添加的安装程序。
从 Azure Migrate 下载 | 当你安装设备并提示你安装 MySQL 时, 请选择 "**下载并安装**"。



## <a name="agent-based-migration-vmware-vm-requirements"></a>基于代理的迁移-VMware VM 要求

**支持** | **详细信息**
--- | ---
**计算机工作负荷** | Azure Migrate 支持迁移受支持计算机上运行的任何工作负荷 (假设 Active Directory、SQL server 等)。
**操作系统** | 有关最新信息, 请查看 Site Recovery 的[操作系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure Migrate 提供完全相同的 VM 操作系统支持。
**Linux 文件系统/来宾存储** | 有关最新信息, 请查看[Linux 文件系统](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)对 Site Recovery 的支持。 Azure Migrate 具有相同的 Linux 文件系统支持。
**网络/存储** | 有关最新信息, 请查看 Site Recovery 的[网络](../site-recovery/vmware-physical-azure-support-matrix.md#network)和[存储](../site-recovery/vmware-physical-azure-support-matrix.md#storage)必备组件。 Azure Migrate 提供完全相同的网络/存储要求。
**Azure 要求** | 有关最新信息, 请查看 Site Recovery 的[Azure 网络](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存储](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[计算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 对于 VMware 迁移, Azure Migrate 具有相同的要求。
**移动服务** | 必须在要迁移的每个 VM 上安装移动服务代理。
**目标磁盘** | Vm 只能迁移到 Azure 中的托管磁盘 (标准 HHD、高级 SSD)。



## <a name="agent-based-migration-url-access-requirements"></a>基于代理的迁移-URL 访问要求

在 VMware Vm 上运行的移动服务需要 internet 连接到 internet。

- 部署移动服务时, 它会对下表中汇总的 Url 执行连接性检查。
- 如果你使用的是基于 URL 的防火墙, 则允许访问这些 Url, 确保代理解析查找 Url 时收到的任何 CNAME 记录。

**URL** | **详细信息**  
--- | ---
*.portal.azure.com | 导航到 Azure 门户中的 Azure Migrate。
*.windows.net | 登录到 Azure 订阅。
*.microsoftonline.com | 为设备创建 Active Directory 应用程序, 以便与 Azure Migrate 服务通信。
management.azure.com | 为设备创建 Active Directory 应用程序, 以便与 Azure Migrate 服务通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.azure.net | 管理 Azure Key Vault 中的机密。
*.servicebus.windows.net | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。

## <a name="agent-based-migration-port-requirements"></a>基于代理的迁移-端口要求

**设备** | **Connection**
--- | ---
VM | Vm 上运行的移动服务与本地配置443服务器进行通信, 以进行复制管理。<br/><br/> VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
复制设备 | 复制设备通过端口 HTTPS 443 出站来协调与 Azure 的复制。
进程服务器 | 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。<br/> 默认情况下, 进程服务器在复制设备上运行。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 Vm 必须满足此表中汇总的 Azure VM 要求。 当 Site Recovery 为复制运行必备项检查时, 如果无法满足某些要求, 则检查将失败。

组件 | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 使用[无代理复制](#agentless-migration-vmware-vm-requirements)为 vmware vm 和[使用基于代理的复制的 vmware vm](#agent-based-migration-vmware-vm-requirements)验证支持的操作系统。<br/> 你可以迁移在受支持的操作系统上运行的任何工作负荷。 | 如果不支持，检查会失败。
来宾操作系统体系结构 | 64 位。 | 如果不支持，检查会失败。
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 64 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不受支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不受支持。 | 如果不支持，检查会失败。
BitLocker | 不受支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
迁移后连接-Windows | 若要在迁移后连接到运行 Windows 的 Azure Vm:<br/> -迁移之前, 在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。<br/> 对于站点到站点 VPN 访问, 请启用 rdp, 并允许**Windows 防火墙** -> 中的 rdp 允许用于**域和专用**网络的**应用和功能**。 此外, 请检查操作系统的 SAN 策略是否设置为**OnlineAll**。 [了解详细信息](https://support.microsoft.com/kb/3031135)。 |
迁移后连接-Linux | 使用 SSH 迁移后连接到 Azure Vm:<br/> 在迁移之前, 请在本地计算机上检查安全外壳服务是否设置为 "启动", 以及防火墙规则是否允许 SSH 连接。<br/> 故障转移后, 在 Azure VM 上, 允许已故障转移的 VM 上的网络安全组和连接到的 Azure 子网的 SSH 端口建立传入连接。 此外, 为 VM 添加公共 IP 地址。 |  


## <a name="next-steps"></a>后续步骤

[准备 VMware](tutorial-prepare-vmware.md)评估和迁移。
