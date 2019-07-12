---
title: 对于 VMware 评估和迁移的 azure 迁移支持矩阵
description: 汇总了支持设置和限制的评估和迁移 VMware Vm 到 Azure 中使用 Azure Migrate 服务。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811578"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>对于 VMware 评估和迁移的支持矩阵

可以使用[Azure Migrate 服务](migrate-overview.md)来评估和将计算机迁移到 Microsoft Azure 云。 本文汇总了支持设置和用于评估和迁移的本地 VMware Vm 的限制。


## <a name="vmware-scenarios"></a>VMware 方案

表概述了 VMware Vm 支持的方案。

**部署** | **详细信息** 
--- | --- 
**评估本地 VMware Vm** | [设置](tutorial-prepare-vmware.md)第一个评估。<br/><br/> [运行](scale-vmware-assessment.md)大规模的评估。
**将 VMware Vm 迁移** | 可以有一些限制，使用无代理迁移，迁移，也可以使用基于代理的迁移。 [了解详细信息](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
Azure 权限 | 需要参与者或所有者的权限的订阅中创建一个 Azure Migrate 项目。
VMware 限制  | 评估最多 35,000 单个项目中的 VMware Vm。

项目可以包括 VMware Vm 和 HYPER-V 虚拟机，直到达到评估限制为止。

## <a name="assessment-vmware-server-requirements"></a>评估 VMware 服务器要求

此表总结了评估支持和对 VMware 虚拟化服务器的限制。

**支持** | **详细信息**
--- | ---
**vCenter 服务器** | 你想要评估的 VMware Vm 必须通过运行 5.5、 6.0、 6.5、 或 6.7 的一个或多个 vCenter 服务器管理。

## <a name="assessment-vcenter-server-permissions"></a>评估 vCenter 服务器权限

仅评估时，需要在 vCenter 服务器的只读帐户。

## <a name="assessment-appliance-requirements"></a>评估设备要求

**支持** | **详细信息**
--- | ---
**ESXi** | 设备 VM 必须部署在运行版本 5.5 或更高版本的 ESXi 主机上。
**Azure Migrate 项目** | 一台设备可以与单个项目相关联。
**vCenter Server** | 一台设备可发现 vCenter 服务器上的最多 10,000 个 VMware Vm。<br/> 一台设备可以连接到一个 vCenter 服务器。


## <a name="assessment-url-access-requirements"></a>评估 URL 访问要求

Azure Migrate 设备需要 internet 连接到 internet。

- 在部署设备时，Azure Migrate 执行到下表中汇总的 Url 的连接性检查。
- 如果您使用基于 URL 的 firewall.proxy，允许访问这些 Url，并确保代理解析查找 Url 时接收到任何 CNAME 记录。

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到在 Azure 门户中 Azure Migrate。
*.windows.net | 登录到 Azure 订阅。
*.microsoftonline.com | 创建设备与 Azure Migrate 服务进行通信的 Active Directory 应用程序。
management.azure.com | 创建设备与 Azure Migrate 服务进行通信的 Active Directory 应用程序。
dc.services.visualstudio.com | 上传用于内部监视的应用程序日志。
*.vault.azure.net | 管理 Azure 密钥保管库中的机密。
*.servicebus.windows.net | 设备和 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。


## <a name="assessment-port-requirements"></a>评估端口要求

**设备** | **Connection**
--- | --- 
设备 | TCP 端口 3389，以允许远程桌面连接到设备上的入站的连接。<br/> 入站端口 44368 远程访问设备管理应用程序使用的 URL 上的连接： https://<appliance-ip-or-name>:44368 <br/>要将发现和性能元数据发送到 Azure Migrate 的端口 443 的出站连接。
vCenter 服务器 | 入站 TCP 端口 443 以允许收集配置和性能评估的元数据的设备上的连接。 <br/> 设备连接到默认端口 443 上的 vCenter。 如果 vCenter 服务器在不同端口上侦听，则可以修改端口时设置发现。


## <a name="agentless-migration-vmware-server-requirements"></a>无代理迁移 VMware 服务器要求

此表总结了评估支持和对 VMware 虚拟化服务器的限制。

**支持** | **详细信息**
--- | ---
**vCenter 服务器** | 使用无代理迁移迁移 VMware Vm 必须由运行 5.5、 6.0、 6.5、 或 6.7 的一个或多个 vCenter 服务器管理。

## <a name="agentless-migration-vcenter-server-permissions"></a>无代理迁移 vCenter 服务器权限

**权限** | **详细信息**
--- | --- 
Datastore.Browse | 允许浏览 VM 的日志文件快照创建和删除操作进行故障排除。
Datastore.LowLevelFileOperations | 在数据存储浏览器，快照创建和删除操作进行故障排除允许读取/写入/删除/重命名操作。
VirtualMachine.Configuration.DiskChangeTracking | 允许启用或禁用更改跟踪的 VM 磁盘提取快照之间的数据已更改的块
VirtualMachine.Configuration.DiskLease | 允许 VM，以读取使用 VMware vSphere 虚拟磁盘开发工具包 (VDDK) 磁盘的磁盘的租约的操作。
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | 允许打开 VM，以读取使用 VDDK 磁盘上的磁盘。
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | 允许的 VM，若要下载日志并发生故障时进行故障排除与关联的文件的读取的操作。 
VirtualMachine.SnapshotManagement.* | 允许创建和管理复制的 VM 快照。 
关闭虚拟 Machine.Interaction.Power | 允许将 VM 迁移到 Azure 期间关闭。


## <a name="agentless-migration-vmware-vm-requirements"></a>无代理迁移 VMware VM 要求

**支持** | **详细信息**
--- | ---
**受支持的操作系统** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)并[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)可以使用无代理迁移迁移 Azure 支持的操作系统。
**适用于 Azure 所需的更改** | 某些 Vm 可能需要更改，以便它们可以在 Azure 中运行。 Azure Migrate，可自动对以下操作系统的这些更改：<br/> -Red Hat Enterprise Linux 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7、 8<br/><br/> 对于其他操作系统，你需要进行迁移之前手动调整。 相关文章包含有关如何执行此操作的说明。
**Linux 启动** | 如果 /boot 专用分区上，它应驻留在 OS 磁盘，并且不能跨越多个磁盘。<br/> 如果 /boot 是根 （/） 分区的一部分，/ 分区应在 OS 磁盘上并不跨其他磁盘。
**UEFI 启动** | 具有 UEFI boot 的虚拟机不支持进行迁移。
**加密的磁盘/卷** | 使用加密的磁盘卷的虚拟机不支持进行迁移。
**RDM/传递磁盘** | 如果 Vm 有 RDM 或传递磁盘，则这些磁盘不会复制到 Azure。
**NFS** | 不会复制在 Vm 上装载为卷的 NFS 卷。
**目标磁盘** | 虚拟机只能迁移到 Azure 中的托管磁盘 (标准 HHD，高级 SSD)。


## <a name="agentless-migration-appliance-requirements"></a>无代理迁移设备要求


**支持** | **详细信息**
--- | ---
**ESXi** | 设备 VM 必须部署在运行版本 5.5 或更高版本的 ESXi 主机上。
**Azure Migrate 项目** | 一台设备可以与单个项目相关联。
**vCenter Server** | 一台设备可发现 vCenter 服务器上的最多 10,000 个 VMware Vm。<br/> 一台设备可以连接到一个 vCenter 服务器。
**VDDK** | 如果你正运行带有 Azure 迁移服务器迁移的无代理迁移，必须在设备 VM 上安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。

## <a name="agentless-migration-url-access-requirements"></a>无代理的迁移 URL 访问要求

Azure Migrate 设备需要 internet 连接到 internet。

- 在部署设备时，Azure Migrate 执行到下表中汇总的 Url 的连接性检查。
- 如果您使用基于 URL 的 firewall.proxy，允许访问这些 Url，并确保代理解析查找 Url 时接收到任何 CNAME 记录。

**URL** | **详细信息**  
--- | --- 
*.portal.azure.com | 导航到在 Azure 门户中 Azure Migrate。
*.windows.net | 登录到 Azure 订阅。
*.microsoftonline.com | 创建设备与 Azure Migrate 服务进行通信的 Active Directory 应用程序。
management.azure.com | 创建设备与 Azure Migrate 服务进行通信的 Active Directory 应用程序。
dc.services.visualstudio.com | 上传用于内部监视的应用程序日志。
*.vault.azure.net | 管理 Azure 密钥保管库中的机密。
*.servicebus.windows.net | 设备和 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。


## <a name="agentless-migration-port-requirements"></a>无代理迁移端口要求

**设备** | **Connection**
--- | --- 
设备 | 出站 TCP 端口 3389 将复制的数据上传到 Azure，并使用 Azure Migrate 通信进行复制和迁移。
vCenter 服务器 | TCP 端口 443 以允许设备以协调复制-创建快照，复制数据上的入站的连接发布快照
vSphere/EXSI 主机 | 入站 TCP 端口的设备，可将数据从快照复制的 902。 


## <a name="agent-based-migration-vmware-server-requirements"></a>基于代理的迁移 VMware 服务器要求

此表总结了评估支持和对 VMware 虚拟化服务器的限制。

**支持** | **详细信息**
--- | ---
**vCenter server/ESXI** | 必须由一个或多个 vCenter 服务器 5.5、 6.0、 6.5、 或 6.7、 运行或与 vSphere 版本 5.5、 6.0、 6.5 或 6.7 ESXI 主机上运行管理 VMware Vm 迁移。

### <a name="agent-based-migration-vcenter-server-permissions"></a>基于代理的迁移的 vCenter 服务器权限

**权限** | **详细信息**
--- | --- 
Datastore.AllocateSpace | 对于 VM，对数据存储空间分配快照、 克隆或虚拟磁盘。 
Datastore.Browse | 允许浏览 VM 的日志文件快照创建和删除操作进行故障排除。
Datastore.LowLevelFileOperations | 允许读取、 写入、 删除和重命名来解决快照创建/删除数据存储浏览器中的操作。
Datastore.UpdateVirtualMachineFiles | 允许更新到上一种数据存储的虚拟机文件的路径，数据存储已 resignatured 之后。
Network.AssignNetwork | 允许将网络分配给 VM 资源。
AssignVirtualMachineToResourcePool | 允许分配给资源池的 VM。
Resource.MigratePoweredOffVirtualMachine | 可迁移关闭的 VM 到其他资源池或主机。
Resource.MigratePoweredOnVirtualMachine | 允许使用 vMotion，到其他资源池或主机上 VM 的迁移。
Tasks.CreateTask | 允许创建用户定义的任务的扩展。
Tasks.UpdateTask | 允许扩展来更新用户定义的任务。
VirtualMachine.Configuration。 | 允许配置的 VM 选项和设备。
虚拟 Machine.Interaction.AnswerQuestion | 允许 VM 状态转换或运行时错误的问题的解决的方法。
Virtual Machine.Interaction.DeviceConnection | 允许更改虚拟机的 disconnectable 虚拟设备的连接的状态。
Virtual Machine.Interaction.ConfigureCDMedia | 允许虚拟 DVD 或 CD-ROM 设备的配置。
Virtual Machine.Interaction.ConfigureFloppyMedia | 允许虚拟软盘设备的配置。
Virtual Machine.Interaction.PowerOff | 允许将 VM 迁移到 Azure 期间关闭。
虚拟 Machine.Interaction.PowerOn | 允许开机电源已关闭 VM 和恢复挂起的 VM。
Virtual Machine.Interaction.VMwareToolsInstall | 允许装载和卸载 VMware 工具 CD 安装程序作为来宾操作系统 CD-ROM。
VirtualMachine.Inventory.CreateNew | 允许的 VM 创建和分配的所需的资源。
VirtualMachine.Inventory.Register | 允许添加的现有 VM 的 vCenter 服务器或主机清单。
VirtualMachine.Inventory.Unregister | 允许 VMe 从 vCenter 服务器或主机清单中注销。
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | 允许使用 VM，包括 vmx、 磁盘、 日志和 nvram 关联的文件的写入操作。
VirtualMachine.Provisioning.AllowVirtualMachineDownload | 允许使用 VM，若要下载用于故障排除日志关联的文件的读取的操作。
VirtualMachine.SnapshotManagement.RemoveSnapshot | 允许的快照的快照历史记录中删除。

## <a name="agent-based-migration-replication-appliance-requirements"></a>基于代理的迁移复制设备要求

要求[复制设备](migrate-replication-appliance.md)用于基于代理的 VMware Vm 和物理迁移表中总结了使用 Azure 迁移服务器迁移的服务器。

> [!NOTE]
> 如果设置了使用 Azure 迁移中心中提供的 OVA 模板复制设备时，设备将在运行 Windows Server 2016 并符合支持要求。 如果设置的物理服务器上手动复制设备，请确保它符合要求。



组件  | 要求  
--- | ---
 | **VMware 设置**（VMware VM 设备）
**PowerCLI** | [PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)如果复制设备正在运行的 VMware VM 上应安装。
**NIC 类型** | VMXNET3 （如果该设备是 VMware VM）
 | **硬件设置** 
CPU 核心数 | 8 
RAM | 16 GB
磁盘数目 | 三个：操作系统磁盘、 进程服务器缓存磁盘和保留驱动器。
可用磁盘空间 （缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
**软件设置** | 
操作系统 | Windows Server 2016 或 Windows Server 2012 R2
操作系统区域设置 | 美国英语
TLS | 应启用 TLS 1.2。
.NET framework | 应 （使用已启用强加密在计算机上安装.NET framework 4.6 或更高版本。
MySQL | 应在设备上安装 MySQL。<br/> 应安装 MySQL。 您可以手动安装，或 Site Recovery 可以在设备部署期间安装。 
其他应用 | 您不应复制设备上运行其他应用。
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V 
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置 
**网络设置** | 
IP 地址类型 | Static 
端口 | 443（控制通道协调）<br>9443（数据传输） 
NIC 类型 | VMXNET3 

### <a name="replication-appliance-url-access"></a>复制设备 URL 访问

复制设备需要访问这些 Url。

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
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF 安装程序需要访问这些 Url。 它们由 Azure Active Directory 用于访问控制和标识管理
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下载


#### <a name="mysql-installation-options"></a>MySQL 安装选项

可以使用下列方法之一在复制设备上安装 MySQL。

**安装** | **详细信息**
--- | ---
手动下载并安装 | 下载 MySQL 应用程序并将其放在文件夹 C:\Temp\ASRSetup，然后手动安装。<br/> 如果设置了 MySQL 将显示为已安装的设备。 
不会联机下载 | 将 MySQL 安装程序应用程序放置 C:\Temp\ASRSetup 的文件夹中。 如果您安装该设备，单击以下载并安装 MySQL 安装程序将使用你添加安装程序。 
从 Azure 下载迁移 | 如果您安装该设备，系统会提示输入 MySQL 选择**下载并安装**。



## <a name="agent-based-migration-vmware-vm-requirements"></a>基于代理的迁移 VMware VM 要求

**支持** | **详细信息**
--- | ---
**计算机工作负荷** | Azure Migrate 支持的任何工作负荷 (例如 Active Directory，SQL server 等) 的迁移支持的计算机上运行。
**操作系统** | 最新信息，请查看[操作系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)Site recovery。 Azure Migrate 提供的相同 VM 操作系统支持。
**Linux 文件系统/来宾存储** | 最新信息，请查看[Linux 文件系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)Site recovery。 Azure 迁移具有相同的 Linux 文件系统支持。
**网络/存储** | 最新信息，请查看[网络](../site-recovery/vmware-physical-azure-support-matrix.md#network)并[存储](../site-recovery/vmware-physical-azure-support-matrix.md#storage)Site Recovery 的先决条件。 Azure Migrate 提供相同的网络存储要求。
**Azure 要求** | 最新信息，请查看[Azure 网络](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)，[存储](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)，并[计算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)站点恢复的要求。 Azure Migrate 有相同要求 VMware 迁移。
**移动服务** | 必须在你想要迁移的每个 VM 上安装移动服务代理。
**目标磁盘** | 虚拟机只能迁移到 Azure 中的托管磁盘 (标准 HHD，高级 SSD)。

   

## <a name="agent-based-migration-url-access-requirements"></a>基于代理的迁移 URL 访问要求

VMware Vm 上运行的移动服务需要 internet 连接到 internet。

- 部署时移动服务，它将执行连接性检查下表中汇总的 url。
- 如果您使用基于 URL 的 firewall.proxy，允许访问这些 Url，并确保代理解析查找 Url 时接收到任何 CNAME 记录。

**URL** | **详细信息**  
--- | --- 
*.portal.azure.com | 导航到在 Azure 门户中 Azure Migrate。
*.windows.net | 登录到 Azure 订阅。
*.microsoftonline.com | 创建设备与 Azure Migrate 服务进行通信的 Active Directory 应用程序。 
management.azure.com | 创建设备与 Azure Migrate 服务进行通信的 Active Directory 应用程序。
dc.services.visualstudio.com | 上传用于内部监视的应用程序日志。
*.vault.azure.net | 管理 Azure 密钥保管库中的机密。
*.servicebus.windows.net | 设备和 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net | 将数据上传到存储帐户。

## <a name="agent-based-migration-port-requirements"></a>基于代理的迁移端口要求

**设备** | **Connection**
--- | --- 
VM | 在 Vm 上运行的移动服务与通信端口 HTTPS 443 上的本地配置服务器的复制管理入站。<br/><br/> VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
复制设备 | 复制设备来协调通过 HTTPS 443 出站端口与 Azure 的复制。
进程服务器 | 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。<br/> 默认情况下在复制设备上运行的进程服务器。

## <a name="azure-vm-requirements"></a>Azure VM 要求

所有在本地复制到 Azure Vm 必须满足下表中总结的 Azure VM 要求。 Site Recovery 运行时复制的先决条件检查，如果未满足某些要求，检查会失败。

组件  | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证支持的操作系统[使用无代理复制的 VMware 虚拟机](#agentless-migration-vmware-vm-requirements)，并为[使用基于代理的复制的 VMware Vm](#agent-based-migration-vmware-vm-requirements)。<br/> 你可以迁移任何受支持的操作系统上运行的工作负荷。 | 如果不支持，检查会失败。
来宾操作系统体系结构 | 64 位。 | 如果不支持，检查会失败。
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 64 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 | 
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。 
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
在迁移 Windows 后，连接 | 若要连接到在迁移后运行 Windows 的 Azure Vm:<br/> -在迁移之前启用的本地 VM 上的 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP    。<br/> 为站点到站点 VPN 访问启用 RDP，并允许在 RDP **Windows 防火墙** -> **允许的应用程序和功能**有关**域和专用**网络。 此外，检查操作系统的 SAN 策略设置为**OnlineAll**。 [了解详细信息](https://support.microsoft.com/kb/3031135)。 | 
在迁移 Linux 后，连接 | 若要使用 SSH 在迁移后连接到 Azure Vm:<br/> 迁移之前，在本地计算机上检查，安全外壳服务设置为启动，以及防火墙规则允许 SSH 连接。<br/> 故障转移后，在 Azure VM 上允许与故障转移的 VM 上的网络安全组规则和连接到 Azure 子网的 SSH 端口建立传入连接。 此外，为 VM 添加公共 IP 地址。 |  


## <a name="next-steps"></a>后续步骤

[为 VMware 准备](tutorial-prepare-vmware.md)评估和迁移。








