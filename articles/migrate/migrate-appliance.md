---
title: Azure Migrate 设备
description: 概述了服务器评估和迁移期间使用的 Azure Migrate 设备。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a313d7b964e6ea849acb5b034ed55975b8c5b524
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927489"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 设备

本文总结了使用 Azure Migrate 设备的先决条件和支持要求。 

## <a name="deployment-scenarios"></a>部署方案

Azure Migrate 设备用于以下应用场景。

**方案** | **工具** | **用于** 
--- | --- | ---
**VMware VM 评估** | Azure Migrate：服务器评估 | 发现 VMware VM<br/><br/> 发现计算机应用和依赖项<br/><br/> 收集计算机元数据和性能元数据以供评估。
**VMware VM 无代理迁移** | Azure Migrate：服务器迁移 | 发现 VMware VM <br/><br/> 使用无代理迁移来复制 VMware VM。
**Hyper-V VM 评估** | Azure Migrate：服务器评估 | 发现 Hyper-V VM<br/><br/> 收集计算机元数据和性能元数据以供评估。
**物理计算机评估** |  Azure Migrate：服务器评估 |  发现物理服务器（或视为物理服务器的 VM）。<br/><br/> 收集计算机元数据和性能元数据以供评估。

## <a name="deployment-methods"></a>部署方法

可以使用几种方法来部署设备：

- 使用适用于 VMware VM 和 Hyper-V VM 的模板（适用于 VMware 的 OVA 模板或适用于 Hyper-V 的 VHD 模板）来部署设备。
- 如果不想使用模板，可以使用 PowerShell 脚本部署 VMware 或 Hyper-V 设备。
- 在 Azure 政府版中，应使用脚本部署设备。
- 对于物理服务器，应始终使用脚本部署设备。
- 下表提供了下载链接。


## <a name="appliance---vmware"></a>设备 - VMware 

下表总结了 VMware 的 Azure Migrate 设备要求。

要求 | **VMware** 
--- | ---
**权限** | 若要在本地或远程访问设备 Web 应用，需要成为设备计算机上的域管理员或本地管理员。
**设备组件** | 设备包括以下组件：<br/><br/> - 管理应用：在设备部署期间用于用户输入的 Web 应用。 在评估要迁移到 Azure 的计算机时会使用它。<br/> - 发现代理：该代理收集计算机配置数据。 在评估要迁移到 Azure 的计算机时会使用它。<br/>- **收集器代理**：代理收集性能数据。 在评估要迁移到 Azure 的计算机时会使用它。<br/>- 自动更新服务：更新设备组件（每 24 小时运行一次）。<br/>- DRA 代理：协调 VM 复制，并协调复制计算机与 Azure 之间的通信。 只有在使用无代理迁移将 VMware VM 复制到 Azure 时，才会使用它。<br/>- 网关：将复制的数据发送到 Azure。 只有在使用无代理迁移将 VMware VM 复制到 Azure 时，才会使用它。
**支持的部署** | 使用 OVA 模板部署为 VMware VM。<br/><br/> 使用 PowerShell 安装脚本部署为 VMware VM 或物理计算机。
**项目支持** |  设备可与单个项目关联。 <br/> 可以将任意数量的设备与单个项目关联。<br/> 
**发现限制** | 一个设备最多可在 vCenter Server 上发现 10,000 个 VMware VM。<br/> 设备可以连接到单个 vCenter Server。
**OVA 模板** | 从门户或从[此处](https://go.microsoft.com/fwlink/?linkid=2140333)下载<br/><br/> 下载大小为 11.6 GB。<br/><br/> 下载的设备模板附带有效期为 180 天的 Windows Server 2016 评估许可证。 如果评估期临近过期，建议下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**PowerShell 脚本** | 请参阅此 [文](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)。<br/><br/> 
**软件/硬件** |  设备应在具有 Windows Server 2016、32-GB RAM、8 个 vCPU、约 80 GB 磁盘存储和一个外部虚拟交换机的计算机上运行。<br/> 设备需要直接访问或通过代理访问 Internet。<br/><br/> 如果在 VMware VM 上运行设备，vCenter Server 上需要有足够的资源来分配给满足要求的 VM。<br/><br/> 如果在物理计算机上运行设备，请确保它运行的是 Windows Server 2016，并满足硬件要求。
**VMware 要求** | 如果将设备部署为 VMware VM，必须将其部署在运行版本 5.5 或更高版本的 ESXi 主机上。<br/><br/> 运行 5.5、6.0、6.5 或 6.7 的 vCenter Server。
**VDDK（无代理迁移）** | 如果将设备部署为 VMware VM，并且运行的是无代理迁移，则必须在设备 VM 上安装 VMware vSphere VDDK。
**哈希值 - OVA** | [验证](tutorial-assess-vmware.md#verify-security) OVA 模板哈希值。
**哈希值 - PowerShell 脚本** | [验证](deploy-appliance-script.md#verify-file-security) PowerShell 脚本哈希值。




## <a name="appliance---hyper-v"></a>设备 - Hyper-V

要求 | **Hyper-V** 
--- | ---
**权限** | 若要在本地或远程访问设备 Web 应用，需要成为设备计算机上的域管理员或本地管理员。
**设备组件** | 设备包括以下组件：<br/><br/>- 管理应用：在设备部署期间用于用户输入的 Web 应用。 在评估要迁移到 Azure 的计算机时会使用它。<br/> - 发现代理：该代理收集计算机配置数据。 在评估要迁移到 Azure 的计算机时会使用它。<br/>- **收集器代理**：代理收集性能数据。 在评估要迁移到 Azure 的计算机时会使用它。<br/>- 自动更新服务：更新设备组件（每 24 小时运行一次）。
**支持的部署** | 使用 VHD 模板部署为 Hyper-V VM。<br/><br/> 使用 PowerShell 安装脚本部署为 Hyper-V VM 或物理计算机。
**项目支持** |  设备可与单个项目关联。 <br/> 可以将任意数量的设备与单个项目关联。<br/> 
**发现限制** | 一个设备最多可发现 5000 个 Hyper-V VM。<br/> 一个设备最多可连接到 300 个 Hyper-V 主机。
**VHD 模板** | 包含 VHD 的压缩文件夹。 从门户或从 [此处](https://go.microsoft.com/fwlink/?linkid=2140422)下载。<br/><br/> 下载大小为 10.4 GB。<br/><br/> 下载的设备模板附带有效期为 180 天的 Windows Server 2016 评估许可证。 如果评估期临近过期，建议下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**PowerShell 脚本** | 请参阅此 [文](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)。<br/><br/> 
**软件/硬件***   |  设备应在 Windows Server 2016、16 GB RAM、8个 vcpu、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接访问或通过代理访问 Internet。<br/><br/> 如果将设备作为 Hyper-V VM 运行，Hyper-V 主机上需要有足够的资源来分配 16-GB RAM、8 个 vCPU、约 80 GB 的存储空间，以及设备 VM 的一个外部交换机。<br/><br/> 如果在物理计算机上运行设备，请确保它运行的是 Windows Server 2016，并满足硬件要求。 
**Hyper-V 要求** | 如果使用 VHD 模板部署设备，那么 Azure Migrate 提供的设备 VM 是 Hyper-V VM 版本 5.0。<br/><br/> Hyper-V 主机必须运行 Windows Server 2012 R2 或更高版本。 
**哈希值 - VHD** | [验证](tutorial-assess-hyper-v.md#verify-security) VHD 模板哈希值。
**哈希值 - PowerShell 脚本** | [验证](deploy-appliance-script.md#verify-file-security) PowerShell 脚本哈希值。


## <a name="appliance---physical"></a>设备 - 物理服务器

要求 | **物理服务器** 
--- | ---
**权限** | 若要在本地或远程访问设备 Web 应用，需要成为设备计算机上的域管理员或本地管理员。
**设备组件** | 设备包括以下组件： <br/><br/> - 管理应用：在设备部署期间用于用户输入的 Web 应用。 在评估要迁移到 Azure 的计算机时会使用它。<br/> - 发现代理：该代理收集计算机配置数据。 在评估要迁移到 Azure 的计算机时会使用它。<br/>- **收集器代理**：代理收集性能数据。 在评估要迁移到 Azure 的计算机时会使用它。<br/>- 自动更新服务：更新设备组件（每 24 小时运行一次）。
**支持的部署** | 使用 PowerShell 安装脚本部署为专用物理计算机或 VM。 可从门户下载该脚本。
**项目支持** |  设备可与单个项目关联。 <br/> 可以将任意数量的设备与单个项目关联。<br/> 
**发现限制** | 一个设备最多可以发现1000个物理服务器。
**PowerShell 脚本** | 从门户或 [此处](https://go.microsoft.com/fwlink/?linkid=2140334) ( # A0) 下载压缩文件夹中的脚本。 [了解详细信息](tutorial-assess-physical.md#set-up-the-azure-migrate-appliance)。<br/><br/> 下载大小为 85 MB。
**软件/硬件** |  设备应在 Windows Server 2016、16 GB RAM、8个 vcpu、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接访问或通过代理访问 Internet。<br/><br/> 如果在物理计算机上运行设备，请确保它运行的是 Windows Server 2016，并满足硬件要求。<br/> 不支持在具有 Windows Server 2019 的计算机上运行设备。
**哈希值** | [验证](deploy-appliance-script.md#verify-file-security) PowerShell 脚本哈希值。

## <a name="url-access"></a>URL 访问

Azure Migrate 设备需要连接到 Internet。

- 部署设备时，Azure Migrate 会对所需的 URL 执行连接性检查。
- 你需要允许访问列表中的所有 URL。 如果只是执行评估，可以跳过仅对 VMware 无代理迁移标记为“必需”的 URL。
-  如果使用基于 URL 的代理连接到 Internet，请确保代理在查找 URL 时会解析接收到的任何 CNAME 记录。

### <a name="public-cloud-urls"></a>公有云 URL

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到 Azure 门户。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登录到 Azure 订阅。
*.microsoftonline.com <br/> *.microsoftonline-p.com | 为设备创建 Azure Active Directory (AD) 应用，以便与 Azure Migrate 通信。
management.azure.com | 为设备创建 Azure AD 应用，以便与 Azure Migrate 服务通信。
*.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.azure.net | 在 Azure Key Vault 中管理机密。 注意：请确保要复制的计算机有权访问它。
aka.ms/* | 允许访问 aka 链接。 用于 Azure Migrate 设备更新。
download.microsoft.com/download | 允许从 Microsoft 下载中进行下载。
*.servicebus.windows.net | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 连接到 Azure Migrate 服务 URL。
*.hypervrecoverymanager.windowsazure.com | 用于 VMware 无代理迁移<br/><br/> 连接到 Azure Migrate 服务 URL。
\* .blob.core.windows.net |  用于 VMware 无代理迁移<br/><br/>将数据上传到存储以供迁移。

### <a name="government-cloud-urls"></a>政府云 URL

**URL** | **详细信息**  
--- | --- |
*.portal.azure.us  | 导航到 Azure 门户。
graph.windows.net | 登录到 Azure 订阅。
login.microsoftonline.us  | 为设备创建 Azure Active Directory (AD) 应用，以便与 Azure Migrate 通信。
management.usgovcloudapi.net | 为设备创建 Azure AD 应用，以便与 Azure Migrate 服务通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.usgovcloudapi.net | 在 Azure Key Vault 中管理机密。
aka.ms/* | 允许访问 aka 链接。 用于 Azure Migrate 设备更新。
download.microsoft.com/download | 允许从 Microsoft 下载中进行下载。
*.servicebus.usgovcloudapi.net  | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | 连接到 Azure Migrate 服务 URL。
*.hypervrecoverymanager.windowsazure.us | 用于 VMware 无代理迁移<br/><br/> 连接到 Azure Migrate 服务 URL。
*.blob.core.usgovcloudapi.net  |  用于 VMware 无代理迁移<br/><br/>将数据上传到存储以供迁移。
*.applicationinsights.us | 上传用于内部监视的应用日志。





## <a name="collected-data---vmware"></a>收集的数据 - VMware

设备收集元数据、性能数据和依赖项分析数据（如果使用无代理[依赖项分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>元数据

Azure Migrate 设备发现的元数据有助于确定计算机和应用是否已准备好迁移到 Azure、为计算机和应用设定合适大小、计划成本，并分析应用程序依赖项。 Microsoft 不会在任何许可证合规性审核中使用此数据。

下面是设备收集并发送到 Azure 的 VMware VM 元数据的完整列表。

**数据** | **计数器**
--- | --- 
**计算机详细信息** | 
VM ID | vm.Config.InstanceUuid 
VM 名称 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
VM 说明 | vm.Summary.Config.Annotation
许可证产品名称 | vm.Client.ServiceContent.About.LicenseProductName
操作系统类型 | vm.SummaryConfig.GuestFullName
启动类型 | vm.Config.Firmware
内核数 | vm.Config.Hardware.NumCPU
内存 (MB) | vm.Config.Hardware.MemoryMB
磁盘数目 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁盘大小列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
网络适配器列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
内存利用率 |mem.usage.average
**每磁盘详细信息** | 
磁盘密钥值 | disk.Key
Dikunit 数量 | disk.UnitNumber
磁盘控制器密钥值 | disk.ControllerKey.Value
预配量 (GB) | virtualDisk.DeviceInfo.Summary
磁盘名称 | 使用 disk.UnitNumber, disk.Key 和 disk.ControllerKey.VAlue 生成此值
每秒读取操作数 | virtualDisk.numberReadAveraged.average
每秒写入操作数 | virtualDisk.numberWriteAveraged.average
读取吞吐量（MB/秒） | virtualDisk.read.average
写入吞吐量（MB/秒） | virtualDisk.write.average
**每 NIC 详细信息** | 
网络适配器名称 | nic.Key
MAC 地址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 地址 | vm.Guest.Net
IPv6 地址 | vm.Guest.Net
读取吞吐量（MB/秒） | net.received.average
写入吞吐量（MB/秒） | net.transmitted.average
**库存路径详细信息** | 
名称 | container.GetType().Name
子对象类型 | container.ChildType
引用详细信息 | container.MoRef
父级详细信息 | Container.Parent
每个 VM 的文件夹详细信息 | ((Folder)container).ChildEntity.Type
每个 VM 的数据中心详细信息 | ((Datacenter)container).VmFolder
每个主机文件夹的数据中心详细信息 | ((Datacenter)container).HostFolder
每个主机的群集详细信息 | ((ClusterComputeResource)container).Host
每个 VM 的主机详细信息 | ((HostSystem)container).VM

### <a name="performance-data"></a>性能数据


下面是设备收集并发送到 Azure 的 VMware VM 性能数据。

**数据** | **计数器** | **评估影响**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建议的 VM 大小/成本
内存利用率 | mem.usage.average | 建议的 VM 大小/成本
磁盘读取吞吐量（MB/秒） | virtualDisk.read.average | 计算磁盘大小、存储成本和 VM 大小
磁盘写入吞吐量（MB/秒） | virtualDisk.write.average | 计算磁盘大小、存储成本和 VM 大小
每秒的磁盘读取操作次数 | virtualDisk.numberReadAveraged.average | 计算磁盘大小、存储成本和 VM 大小
每秒的磁盘写入操作次数 | virtualDisk.numberWriteAveraged.average  | 计算磁盘大小、存储成本和 VM 大小
NIC 读取吞吐量（MB/秒） | net.received.average | 计算 VM 大小
NIC 写入吞吐量（MB/秒） | net.transmitted.average  |计算 VM 大小


### <a name="installed-apps-metadata"></a>安装的应用元数据

应用程序发现收集已安装的应用程序和操作系统数据。

#### <a name="windows-vm-apps-data"></a>Windows VM 应用数据

此设备从每个启用了应用程序发现的 VM 收集的已安装应用程序数据。 这些数据将发送到 Azure。

**数据** | **注册表位置** | **Key**
--- | --- | ---
应用程序名称  | HKLM： \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM： \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM： \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM： \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
提供程序  | HKLM： \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM： \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Windows VM 功能数据

以下是设备从每个启用了应用程序发现的 VM 收集的功能数据。 这些数据将发送到 Azure。

**数据**  | **PowerShell cmdlet** | **属性**
--- | --- | ---
名称  | Get-help  | 名称
特征类型 | Get-help  | FeatureType
Parent  | Get-help  | Parent

#### <a name="windows-vm-sql-server-metadata"></a>Windows VM SQL Server 元数据

下面是此设备从运行 Microsoft SQL server 的 Vm 收集的用于应用程序发现的 SQL server 元数据。 这些数据将发送到 Azure。

**数据**  | **注册表位置**  | **Key**
--- | --- | ---
名称  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL 服务器 \ 实例 Names\SQL  | installedInstance
版本  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | 版本 
Service Pack  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | SP
Version  | HKLM： \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Version 

#### <a name="windows-vm-operating-system-data"></a>Windows VM 操作系统数据

以下是设备为每个启用了应用程序发现的 VM 收集的操作系统数据。 这些数据将发送到 Azure。

数据  | WMI 类  | WMI 类属性
--- | --- | ---
名称  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
体系结构  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Linux VM 应用数据

此设备从每个启用了应用程序发现的 VM 收集的已安装应用程序数据。 根据 VM 的操作系统，运行一个或多个命令。 这些数据将发送到 Azure。

数据  | 命令
--- | --- 
名称 | rpm，dpkg，对齐
Version | rpm，dpkg，对齐
提供程序 | rpm，dpkg，对齐

#### <a name="linux-vm-operating-system-data"></a>Linux VM 操作系统数据

以下是设备为每个启用了应用程序发现的 VM 收集的操作系统数据。 这些数据将发送到 Azure。

**数据**  | **命令** 
--- | --- | ---
名称 <br/> 版本 | 从以下一个或多个文件收集：<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
体系结构 | uname


### <a name="app-dependencies-metadata"></a>应用依赖项元数据

无代理依赖项分析收集连接和进程数据。

#### <a name="windows-vm-app-dependencies-data"></a>Windows VM 应用程序依赖关系数据

下面是设备从已为无代理依赖项分析启用的每个 VM 收集的连接数据。 这些数据将发送到 Azure。

**数据** | **使用的命令** 
--- | --- 
本地端口 | netstat
本地 IP 地址 | netstat
远程端口 | netstat
远程 IP 地址 | netstat
TCP 连接状态 | netstat
进程 ID | netstat
活动连接数 | netstat


下面是设备从已为无代理依赖项分析启用的每个 VM 收集的进程数据。 这些数据将发送到 Azure。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
进程名称 | Win32_Process | ExecutablePath
进程参数 | Win32_Process | CommandLine
应用程序名称 | Win32_Process | ExecutablePath 属性的 VersionInfo.ProductName 参数

#### <a name="linux-vm-app-dependencies-data"></a>Linux VM 应用程序依赖关系数据

下面是设备从已为无代理依赖项分析启用的每个 Linux VM 收集的连接和进程数据。 这些数据将发送到 Azure。

**数据** | **使用的命令** 
--- | ---
本地端口 | netstat 
本地 IP 地址 | netstat 
远程端口 | netstat 
远程 IP 地址 | netstat 
TCP 连接状态 | netstat 
活动连接数 | netstat
进程 ID  | netstat 
进程名称 | ps
进程参数 | ps
应用程序名称 | dpkg 或 rpm



## <a name="collected-data---hyper-v"></a>收集的数据 - Hyper-V

设备收集元数据、性能数据和依赖项分析数据（如果使用无代理[依赖项分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>元数据
Azure Migrate 设备发现的元数据有助于确定计算机和应用是否已准备好迁移到 Azure、为计算机和应用设定合适大小、计划成本，并分析应用程序依赖项。 Microsoft 不会在任何许可证合规性审核中使用此数据。

下面是设备收集并发送到 Azure 的 Hyper-V VM 元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
**计算机详细信息** | 
BIOS _ Msvm_BIOSElement 的序列号 | BIOSSerialNumber
VM 类型（第 1 代或第 2 代） | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 显示名称 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | VirtualQuantity
内存（字节） | Msvm_MemorySettingData | VirtualQuantity
VM 可使用的最大内存 | Msvm_MemorySettingData | 限制
已启用动态内存 | Msvm_MemorySettingData | DynamicMemoryEnabled
操作系统名称/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名称数据
VM 电源状态 | Msvm_ComputerSystem | EnabledState
**每磁盘详细信息** | 
磁盘标识符 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虚拟硬盘类型 | Msvm_VirtualHardDiskSettingData | 类型
虚拟硬盘大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
虚拟硬盘父级 | Msvm_VirtualHardDiskSettingData | ParentPath
**每 NIC 详细信息** | 
IP 地址（综合 NIC） | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
已启用 DHCP（综合 NIC） | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID（综合 NIC） | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 地址（综合 NIC） | Msvm_SyntheticEthernetPortSettingData | 地址
NIC ID（旧 NIC） | MsvmEmulatedEthernetPortSetting 数据 | InstanceID
NIC MAC ID（旧 NIC） | MsvmEmulatedEthernetPortSetting 数据 | 地址

### <a name="performance-data"></a>性能数据

下面是设备收集并发送到 Azure 的 Hyper VM 性能数据。

**性能计数器类** | **计数器** | **评估影响**
--- | --- | ---
Hyper-V 虚拟机监控程序虚拟处理器 | % 来宾运行时间 | 建议的 VM 大小/成本
Hyper-V 动态内存 VM | 当前压力 (%)<br/> 来宾可见物理内存 (MB) | 建议的 VM 大小/成本
Hyper-V 虚拟存储设备 | 读取的字节数/秒 | 计算磁盘大小、存储成本和 VM 大小
Hyper-V 虚拟存储设备 | 写入的字节数/秒 | 计算磁盘大小、存储成本和 VM 大小
Hyper-V 虚拟网络适配器 | 接收的字节数/秒 | 计算 VM 大小
Hyper-V 虚拟网络适配器 | 发送的字节数/秒 | 计算 VM 大小

- CPU 使用率是所有附加到 VM 的虚拟处理器的所有使用量之和。
- 内存使用率为（当前压力 * 来宾可见物理内存）/100。
- 从列出的 Hyper-V 性能计数器中可收集磁盘和网络利用率值。


## <a name="collected-data---physical"></a>收集的数据 - 物理服务器

设备收集元数据、性能数据和依赖项分析数据（如果使用无代理[依赖项分析](concepts-dependency-visualization.md)）。

### <a name="windows-metadata"></a>Windows 元数据

Azure Migrate 设备发现的元数据有助于确定计算机和应用是否已准备好迁移到 Azure、为计算机和应用设定合适大小、计划成本，并分析应用程序依赖项。 Microsoft 不会在任何许可证合规性审核中使用此数据。

下面是设备收集并发送到 Azure 的 Windows 服务器元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain、Name、PartOfDomain
处理器核心计数 | Win32_PRocessor | NumberOfCores
分配的内存 | Win32_ComputerSystem | TotalPhysicalMemory
BIOS 序列号 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
启动类型 | Win32_DiskPartition | 检查 EFI/BIOS 类型 = GPT:System 的分区
OS 名称 | Win32_OperatingSystem | Caption
OS 版本 |Win32_OperatingSystem | 版本
OS 体系结构 | Win32_OperatingSystem | OSArchitecture
磁盘计数 | Win32_DiskDrive | Model、Size、DeviceID、MediaType、Name
磁盘大小 | Win32_DiskDrive | 大小
NIC 列表 | Win32_NetworkAdapterConfiguration | Description、Index
NIC IP 地址 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 地址 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux 元数据

下面是设备收集并发送到 Azure 的 Linux 服务器元数据的完整列表。

**数据** | **LINUX** 
--- | --- 
FQDN | cat /proc/sys/kernel/hostname, hostname -f
处理器核心计数 |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
分配的内存 | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS 序列号 | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
启动类型 | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
OS 名称/版本 | 可以访问以下文件获取 OS 版本和名称：<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
OS 体系结构 | Uname -m
磁盘计数 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
启动磁盘 | df /boot \| sed -n 2p \| awk '{print $1}'
磁盘大小 | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC 列表 | ip -o -4 addr show \| awk '{print $2}'
NIC IP 地址 | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC MAC 地址 | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows 性能数据

下面是设备收集并发送到 Azure 的 Windows 服务器性能数据。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
CPU 使用率 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
内存使用率 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 计数 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 获取网络设备计数。
每 NIC 接收的数据 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
每 NIC 传输的数据 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
磁盘计数 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 磁盘计数
磁盘详细信息 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec、DiskWriteBytesPerSec、DiskReadsPerSec、DiskReadBytesPerSec。

### <a name="linux-performance-data"></a>Linux 性能数据

下面是设备收集并发送到 Azure 的 Linux 服务器性能数据。

**数据** | **Linux** 
--- | --- 
CPU 使用率 | cat /proc/stat/| grep 'cpu' /proc/stat
内存使用率 | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
NIC 计数 | lshw -class network \| grep eth[0-60] \| wc -l
每 NIC 接收的数据 | cat /sys/class/net/eth$nic/statistics/rx_bytes
每 NIC 传输的数据 | cat /sys/class/net/eth$nic/statistics/tx_bytes
磁盘计数 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
磁盘详细信息 | cat /proc/diskstats


## <a name="appliance-upgrades"></a>设备升级

当设备上运行的 Azure Migrate 代理更新时，设备也会随之升级。 升级会自动执行，因为设备上默认启用了自动更新。 可以更改此默认设置，变为手动更新设备服务。

### <a name="turn-off-auto-update"></a>禁用自动更新

1. 在运行设备的计算机上，打开注册表编辑器。
2. 导航到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance。
3. 若要禁用自动更新，请创建一个注册表项 AutoUpdate，将 DWORD 值设为 0。

    ![设置注册表项](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>启用自动更新

可以使用以下任一方法启用自动更新：

- 从 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance 删除 AutoUpdate 注册表项。
- 单击 "**设置先决条件**" 面板中的 "**查看设备服务**" 以打开自动更新。

删除注册表项的步骤：

1. 在运行设备的计算机上，打开注册表编辑器。
2. 导航到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance。
3. 删除先前创建的用于禁用自动更新的注册表项 AutoUpdate。

若要从设备配置管理器中启用，在发现完成后，执行以下操作：

1. 在设备配置管理器上，请参阅 **设置先决条件** 面板
2. 在最新更新检查中，单击 " **查看设备服务** " 并单击链接以打开自动更新。

    ![启用自动更新](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>查看设备服务版本

可以使用以下方法之一查看设备服务版本：

- 在 "设备配置管理器" 中，请参阅 **设置先决条件** 面板。
- 在设备计算机的“控制面板” > “程序和功能”中查看。

若要签入设备配置管理器：

1. 在设备配置管理器上，请参阅 **设置先决条件** 面板
2. 在最新更新检查中，单击 " **查看设备服务**"。

    ![检查版本](./media/migrate-appliance/versions.png)

若要在控制面板中查看，请执行以下操作：

1. 在设备上，单击“启动” > “控制面板” > “程序和功能”
2. 查看列表中的设备服务版本。

    ![在控制面板中查看版本](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>手动更新旧版本

如果你运行的是任何组件的旧版本，则必须卸载该服务，并手动更新到最新版本。

1. 若要查看最新的设备服务版本，请[下载](https://aka.ms/latestapplianceservices) LatestComponents.json 文件。
2.    下载后，在记事本中打开 LatestComponents.json 文件。
3. 在文件中找到最新服务版本，并找到相应的下载链接。 例如：

    "Name":"ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version":"6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    使用文件中的下载链接下载已过时服务的最新版本。
5. 下载后，在管理员命令窗口中运行以下命令，验证已下载的 MSI 的完整性。

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` 例如：C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. 检查命令输出是否与文件中服务的哈希值条目匹配（例如，上述 MD5 哈希值）。
6. 现在，运行 MSI 以安装服务。 此安装是无提示安装，完成后安装窗口将关闭。
7. 安装完成后，可以在“控制面板” > “程序和功能”中查看服务的版本。 现在，服务版本应已升级到 JSON 文件中显示的最新版本。



## <a name="next-steps"></a>后续步骤

- [了解如何](how-to-set-up-appliance-vmware.md)为 VMware 设置设备。
- [了解如何](how-to-set-up-appliance-hyper-v.md)为 Hyper-V 设置设备。
- [了解如何](how-to-set-up-appliance-physical.md)为物理服务器设置设备。

