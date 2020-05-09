---
title: Azure Migrate 设备
description: 提供服务器评估和迁移中所使用的 Azure Migrate 设备的概述。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 439f6d9c80a0b93f071d30d580facc4604cabbac
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780328"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 设备

本文总结了 Azure Migrate 设备的先决条件和支持要求。 

## <a name="deployment-scenarios"></a>部署方案

在以下方案中使用 Azure Migrate 设备。

**方案** | **工具** | **用于** 
--- | --- | ---
**VMware VM 评估** | Azure Migrate：服务器评估 | 发现 VMware Vm<br/><br/> 发现计算机应用和依赖项<br/><br/> 收集计算机元数据和性能元数据以进行评估。
**VMware VM 无代理迁移** | Azure Migrate：服务器迁移 | 发现 VMware Vm <br/><br/> 复制 VMware Vm 和无代理迁移。
**Hyper-v VM 评估** | Azure Migrate：服务器评估 | 发现 Hyper-v Vm<br/><br/> 收集计算机元数据和性能元数据以进行评估。
**物理计算机评估** |  Azure Migrate：服务器评估 |  发现物理服务器（或视为物理服务器的 Vm）。<br/><br/> 收集计算机元数据和性能元数据以进行评估。

## <a name="deployment-methods"></a>部署方法

可以使用几种方法来部署设备：

- 可以使用适用于 VMware Vm 和 Hyper-v Vm （适用于 VMware 的 .OVA 模板或适用于 Hyper-v 的 VHD）的模板部署该设备。
- 如果不想使用模板，可以使用 PowerShell 脚本部署适用于 VMware 或 Hyper-v 的设备。
- 在 Azure 政府版中，应使用脚本部署设备。
- 对于物理服务器，你始终使用脚本来部署该设备。
- 下表提供下载链接。


## <a name="appliance---vmware"></a>设备-VMware 

下表总结了 VMware 的 Azure Migrate 设备要求。

**要求** | **VMware** 
--- | ---
**权限** | 若要在本地或远程访问设备 web 应用，你需要是域管理员，或者是设备计算机上的本地管理员。
**设备组件** | 设备包含以下组件：<br/><br/> - **管理应用**：这是在设备部署期间用于用户输入的 web 应用。 评估要迁移到 Azure 的计算机时使用。<br/> - **发现代理**：代理收集计算机配置数据。 评估要迁移到 Azure 的计算机时使用。<br/>- **评估代理**：代理收集性能数据。 评估要迁移到 Azure 的计算机时使用。<br/>- **自动更新服务**：更新设备组件（每24小时运行一次）。<br/>- **DRA 代理**：协调 VM 复制，并协调复制的计算机与 Azure 之间的通信。 仅当使用无代理迁移将 VMware Vm 复制到 Azure 时使用。<br/>- **网关**：将复制的数据发送到 Azure。 仅当使用无代理迁移将 VMware Vm 复制到 Azure 时使用。
**支持的部署** | 使用 .OVA 模板部署为 VMware VM。<br/><br/> 使用 PowerShell 安装脚本部署为 VMware VM 或物理计算机。
**项目支持** |  设备可以与单个项目关联。 <br/> 可以将任意数量的设备与单个项目相关联。<br/> 
**发现限制** | 设备最多可在 vCenter Server 上发现 10000 VMware Vm。<br/> 设备可以连接到单个 vCenter Server。
**.OVA 模板** | 从门户或从https://aka.ms/migrate/appliance/vmware下载。<br/><br/> 下载大小为 11.2 GB。<br/><br/> 下载的设备模板附带了 Windows Server 2016 评估版许可证，该许可证在180天内有效。 如果评估期接近过期，我们建议你下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**PowerShell 脚本** | 脚本[下载](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**软件/硬件** |  设备应在具有 Windows Server 2016、32-GB RAM、8个 vcpu、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 设备需要直接或通过代理访问 internet。<br/><br/> 如果在 VMware VM 上运行设备，vCenter Server 上需要足够的资源来分配满足要求的 VM。<br/><br/> 如果在物理计算机上运行设备，请确保它正在运行 Windows Server 2016，并满足硬件要求。 
**VMware 要求** | 如果将设备部署为 VMware VM，则必须将其部署在运行版本5.5 或更高版本的 ESXi 主机上。<br/><br/> 运行5.5、6.0、6.5 或 6.7 vCenter Server。
**VDDK （无代理迁移）** | 如果将设备部署为 VMware VM，并且正在运行无代理迁移，则必须在设备 VM 上安装 VMware vSphere VDDK。
**哈希值-.OVA** | [验证](tutorial-assess-vmware.md#verify-security).ova 模板哈希值。
**哈希值-PowerShell 脚本** | [验证](deploy-appliance-script.md#verify-file-security)PowerShell 脚本哈希值。




## <a name="appliance---hyper-v"></a>设备-Hyper-v

**要求** | **Hyper-V** 
--- | ---
**权限** | 若要在本地或远程访问设备 web 应用，你需要是域管理员，或者是设备计算机上的本地管理员。
**设备组件** | 设备包含以下组件：<br/><br/>- **管理应用**：这是在设备部署期间用于用户输入的 web 应用。 评估要迁移到 Azure 的计算机时使用。<br/> - **发现代理**：代理收集计算机配置数据。 评估要迁移到 Azure 的计算机时使用。<br/>- **评估代理**：代理收集性能数据。 评估要迁移到 Azure 的计算机时使用。<br/>- **自动更新服务**：更新设备组件（每24小时运行一次）。
**支持的部署** | 使用 VHD 模板部署为 Hyper-v VM。<br/><br/> 使用 PowerShell 安装脚本部署为 Hyper-v VM 或物理计算机。
**项目支持** |  设备可以与单个项目关联。 <br/> 可以将任意数量的设备与单个项目相关联。<br/> 
**发现限制** | 一个设备最多可以发现5000个 Hyper-v Vm。<br/> 一个设备最多可以连接到300个 Hyper-v 主机。
**VHD 模板** | 包含 VHD 的压缩文件夹。 从门户或从https://aka.ms/migrate/appliance/hyperv下载。<br/><br/> 下载大小为 10 GB。<br/><br/> 下载的设备模板附带了 Windows Server 2016 评估版许可证，该许可证在180天内有效。 如果评估期接近过期，我们建议你下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**PowerShell 脚本** | 脚本[下载](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**软件/硬件***   |  设备应在具有 Windows Server 2016、32-GB RAM、8个 vcpu、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接或通过代理访问 internet。<br/><br/> 如果将设备作为 Hyper-v VM 运行，Hyper-v 主机上需要有足够的资源来分配 16 GB RAM、8个 vcpu、大约 80 GB 的存储空间，以及设备 VM 的外部交换机。<br/><br/> 如果在物理计算机上运行设备，请确保它正在运行 Windows Server 2016，并满足硬件要求。 
**Hyper-v 要求** | 如果使用 VHD 模板部署设备，Azure Migrate 提供的设备 VM 为 Hyper-v VM 版本5.0。<br/><br/> Hyper-v 主机必须运行 Windows Server 2012 R2 或更高版本。 
**哈希值-VHD** | [验证](tutorial-assess-hyper-v.md#verify-security)VHD 模板哈希值。
**哈希值-PowerShell 脚本** | [验证](deploy-appliance-script.md#verify-file-security)PowerShell 脚本哈希值。


## <a name="appliance---physical"></a>设备-物理

**要求** | **物理** 
--- | ---
**权限** | 若要在本地或远程访问设备 web 应用，你需要是域管理员，或者是设备计算机上的本地管理员。
**设备组件** | 设备包含以下组件： <br/><br/> - **管理应用**：这是在设备部署期间用于用户输入的 web 应用。 评估要迁移到 Azure 的计算机时使用。<br/> - **发现代理**：代理收集计算机配置数据。 评估要迁移到 Azure 的计算机时使用。<br/>- **评估代理**：代理收集性能数据。 评估要迁移到 Azure 的计算机时使用。<br/>- **自动更新服务**：更新设备组件（每24小时运行一次）。
**支持的部署** | 使用 PowerShell 安装脚本部署为专用物理计算机或 VM。 可以从门户下载该脚本。
**项目支持** |  设备可以与单个项目关联。 <br/> 可以将任意数量的设备与单个项目相关联。<br/> 
**发现限制** | 一个设备最多可以发现250个物理服务器。
**PowerShell 脚本** | 从门户下载压缩文件夹中的脚本（AzureMigrateInstaller）。 [了解详细信息](tutorial-assess-physical.md#set-up-the-appliance)。 或者，[直接下载](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 下载大小为 59.7 MB。
**软件/硬件** |  设备应在具有 Windows Server 2016、32-GB RAM、8个 vcpu、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接或通过代理访问 internet。<br/><br/> 如果在物理计算机上运行设备，请确保它正在运行 Windows Server 2016，并满足硬件要求。<br/> 不支持在具有 Windows Server 2019 的计算机上运行设备。
**哈希值** | [验证](deploy-appliance-script.md#verify-file-security)PowerShell 脚本哈希值。

## <a name="url-access"></a>URL 访问

Azure Migrate 设备需要连接到 internet。

- 部署设备时，Azure Migrate 会对所需的 Url 进行连接性检查。
- 你需要允许访问列表中的所有 Url。 如果只是执行评估，则可以跳过标记为 "仅 VMware 无代理迁移所需的 Url"。
-  如果使用基于 URL 的代理连接到 internet，请确保代理在查找 Url 时解析接收到的任何 CNAME 记录。

### <a name="public-cloud-urls"></a>公有云 Url

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到 Azure 门户。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登录到 Azure 订阅。
*.microsoftonline.com <br/> *.microsoftonline-p.com | 为设备创建 Azure Active Directory （AD）应用程序，以便与 Azure Migrate 通信。
management.azure.com | 为设备创建 Azure AD 应用程序，以便与 Azure Migrate 服务通信。
*.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.azure.net | 管理 Azure Key Vault 中的机密。
aka.ms/* | 允许访问称为 "链接"。 用于 Azure Migrate 设备更新。
download.microsoft.com/download | 允许从 Microsoft 下载下载。
*.servicebus.windows.net | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 连接到 Azure Migrate 服务 Url。
*.hypervrecoverymanager.windowsazure.com | **用于 VMware 无代理迁移**<br/><br/> 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net |  **用于 VMware 无代理迁移**<br/><br/>将数据上传到存储进行迁移。

### <a name="government-cloud-urls"></a>政府云 Url

**URL** | **详细信息**  
--- | --- |
*.portal.azure.us  | 导航到 Azure 门户。
graph.windows.net | 登录到 Azure 订阅。
login.microsoftonline.us  | 为设备创建 Azure Active Directory （AD）应用程序，以便与 Azure Migrate 通信。
management.usgovcloudapi.net | 为设备创建 Azure AD 应用程序，以便与 Azure Migrate 服务通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*. vault.usgovcloudapi.net | 管理 Azure Key Vault 中的机密。
aka.ms/* | 允许访问称为 "链接"。 用于 Azure Migrate 设备更新。
download.microsoft.com/download | 允许从 Microsoft 下载下载。
*. servicebus.usgovcloudapi.net  | 设备与 Azure Migrate 服务之间的通信。
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | 连接到 Azure Migrate 服务 Url。
*.hypervrecoverymanager.windowsazure.us | **用于 VMware 无代理迁移**<br/><br/> 连接到 Azure Migrate 服务 Url。
*.blob.core.usgovcloudapi.net  |  **用于 VMware 无代理迁移**<br/><br/>将数据上传到存储进行迁移。
*. applicationinsights.us | 上传用于内部监视的应用日志。





## <a name="collected-data---vmware"></a>收集的数据-VMware

设备收集元数据、性能数据和依赖关系分析数据（如果使用无代理[依赖项分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>元数据

Azure Migrate 设备发现的元数据可帮助你确定计算机和应用是否已准备好迁移到 Azure、适合计算机和应用的规模、计划成本，以及分析应用程序依赖关系。 Microsoft 不会在任何许可证相容性审核中使用此数据。

下面是设备收集并发送到 Azure 的 VMware VM 元数据的完整列表。

**数据** | **对抗**
--- | --- 
**计算机详细信息** | 
VM ID | vm.Config.InstanceUuid 
VM 名称 | vm.Config.Name
vCenter Server ID | VMwareClient
VM 说明 | vm.Summary.Config.Annotation
许可证产品名称 | vm.Client.ServiceContent.About.LicenseProductName
操作系统类型 | vmnetwork.SummaryConfig.GuestFullName
启动类型 | vm.Config.Firmware
内核数 | vm.Config.Hardware.NumCPU
内存(MB) | vm.Config.Hardware.MemoryMB
磁盘数目 | vmnetwork.System.linq.enumerable.tolist （）。FindAll （x => 为 VirtualDisk）。计数
磁盘大小列表 | vmnetwork.System.linq.enumerable.tolist （）。FindAll （x => 为 VirtualDisk）
网络适配器列表 | vmnetwork.System.linq.enumerable.tolist （）。FindAll （x => 为 VirtualEthernet）。计数
CPU 使用率 | cpu.usage.average
内存利用率 |mem.usage.average
**每磁盘详细信息** | 
磁盘密钥值 | disk.Key
Dikunit 号 | disk.UnitNumber
磁盘控制器密钥值 | disk.ControllerKey.Value
预配量 (GB) | virtualDisk.DeviceInfo.Summary
磁盘名称 | 使用磁盘生成的值。UnitNumber，磁盘。密钥和磁盘。ControllerKey
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
**清单路径详细信息** | 
名称 | container.GetType().Name
子对象类型 | container.ChildType
引用详细信息 | container.MoRef
父级详细信息 | Container.Parent
每个 VM 的文件夹详细信息 | ((Folder)container).ChildEntity.Type
每个 VM 的数据中心详细信息 | ((Datacenter)container).VmFolder
每个主机文件夹的数据中心详细信息 | ((Datacenter)container).HostFolder
每个主机的群集详细信息 | （（ClusterComputeResource）容器。主持人
每个 VM 的主机详细信息 | （（HostSystem）容器。VMNETWORK

### <a name="performance-data"></a>性能数据


下面是设备收集并发送到 Azure 的 VMware VM 性能数据。

**数据** | **计数器** | **评估影响**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建议的 VM 大小/成本
内存利用率 | mem.usage.average | 建议的 VM 大小/成本
磁盘读取吞吐量（MB/秒） | virtualDisk.read.average | 磁盘大小、存储成本、VM 大小的计算
磁盘写入吞吐量（MB/秒） | virtualDisk.write.average | 磁盘大小、存储成本、VM 大小的计算
每秒磁盘读取操作数 | virtualDisk.numberReadAveraged.average | 磁盘大小、存储成本、VM 大小的计算
磁盘写入操作数/秒 | virtualDisk.numberWriteAveraged.average  | 磁盘大小、存储成本、VM 大小的计算
NIC 读取吞吐量（MB/秒） | net.received.average | VM 大小的计算
NIC 写入吞吐量（MB/秒） | net.transmitted.average  |VM 大小的计算

### <a name="app-dependencies-metadata"></a>应用程序依赖关系元数据

无代理依赖项分析收集连接和处理数据。

#### <a name="connection-data"></a>连接数据

此设备从每个已启用无代理依赖关系分析的 VM 收集的连接数据。 此数据会发送到 Azure。

**数据** | **使用的命令** 
--- | --- 
本地端口 | netstat
本地 IP 地址 | netstat
远程端口 | netstat
远程 IP 地址 | netstat
TCP 连接状态 | netstat
进程 ID | netstat
否。 活动连接 | netstat

#### <a name="process-data"></a>处理数据
以下是设备从每个已启用无代理依赖关系分析的 VM 收集的处理数据。 此数据会发送到 Azure。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
进程名称 | Win32_Process | ExecutablePath
处理参数 | Win32_Process | CommandLine
应用程序名称 | Win32_Process | ExecutablePath 属性的 VersionInfo 参数

#### <a name="linux-vm-data"></a>Linux VM 数据

以下是设备从每个支持无代理依赖关系分析的 Linux VM 收集的连接和处理数据。 此数据会发送到 Azure。

**数据** | **使用的命令** 
--- | ---
本地端口 | netstat 
本地 IP 地址 | netstat 
远程端口 | netstat 
远程 IP 地址 | netstat 
TCP 连接状态 | netstat 
否。 活动连接 | netstat
进程 ID  | netstat 
进程名称 | ps
处理参数 | ps
应用程序名称 | dpkg 或 rpm



## <a name="collected-data---hyper-v"></a>收集的数据-Hyper-v

设备收集元数据、性能数据和依赖关系分析数据（如果使用无代理[依赖项分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>元数据
Azure Migrate 设备发现的元数据可帮助你确定计算机和应用是否已准备好迁移到 Azure、适合计算机和应用的规模、计划成本，以及分析应用程序依赖关系。 Microsoft 不会在任何许可证相容性审核中使用此数据。

下面是设备收集并发送到 Azure 的 Hyper-v VM 元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
**计算机详细信息** | 
BIOS Msvm_BIOSElement 的序列号 | BIOSSerialNumber
VM 类型（第1代或第2代） | Msvm_VirtualSystemSettingData | VirtualSystemSubType
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
虚拟硬盘父项 | Msvm_VirtualHardDiskSettingData | ParentPath
**每 NIC 详细信息** | 
IP 地址（合成 Nic） | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 已启用（合成 Nic） | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID （合成 Nic） | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 地址（合成 Nic） | Msvm_SyntheticEthernetPortSettingData | 地址
NIC ID （旧 Nic） | MsvmEmulatedEthernetPortSetting 数据 | InstanceID
NIC MAC ID （旧 Nic） | MsvmEmulatedEthernetPortSetting 数据 | 地址

### <a name="performance-data"></a>性能数据

下面是设备收集并发送到 Azure 的超级 VM 性能数据。

**性能计数器类** | **计数器** | **评估影响**
--- | --- | ---
Hyper-v 虚拟机监控程序虚拟处理器 | % Guest 运行时间 | 建议的 VM 大小/成本
Hyper-v 动态内存 VM | 当前压力（%）<br/> 来宾可见物理内存（MB） | 建议的 VM 大小/成本
Hyper-v 虚拟存储设备 | 每秒读取的字节数 | 磁盘大小、存储成本、VM 大小的计算
Hyper-v 虚拟存储设备 | 每秒写入的字节数 | 磁盘大小、存储成本、VM 大小的计算
Hyper-V 虚拟网络适配器 | 每秒接收的字节数 | VM 大小的计算
Hyper-V 虚拟网络适配器 | 发送的字节数/秒 | VM 大小的计算

- "CPU 使用率" 是所有附加到 VM 的虚拟处理器的所有使用量之和。
- 内存使用率为（当前压力 * 来宾可见物理内存）/100。
- 磁盘和网络利用率值从列出的 Hyper-v 性能计数器中收集。


## <a name="collected-data---physical"></a>收集的数据-物理

设备收集元数据、性能数据和依赖关系分析数据（如果使用无代理[依赖项分析](concepts-dependency-visualization.md)）。

### <a name="windows-metadata"></a>Windows 元数据

Azure Migrate 设备发现的元数据可帮助你确定计算机和应用是否已准备好迁移到 Azure、适合计算机和应用的规模、计划成本，以及分析应用程序依赖关系。 Microsoft 不会在任何许可证相容性审核中使用此数据。

下面是设备收集并发送到 Azure 的 Windows server 元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain、Name、PartOfDomain
处理器核心计数 | Win32_PRocessor | NumberOfCores
分配的内存 | Win32_ComputerSystem | TotalPhysicalMemory
BIOS 序列号 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
启动类型 | Win32_DiskPartition | 检查类型为 GPT 的分区 **：** EFI/BIOS 系统
OS 名称 | Win32_OperatingSystem | Caption
OS 版本 |Win32_OperatingSystem | 版本
操作系统体系结构 | Win32_OperatingSystem | OSArchitecture
磁盘计数 | Win32_DiskDrive | 型号，大小，DeviceID，媒体名称，名称
磁盘大小 | Win32_DiskDrive | 大小
NIC 列表 | Win32_NetworkAdapterConfiguration | 说明，索引
NIC IP 地址 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 地址 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux 元数据

下面是设备收集并发送到 Azure 的 Linux 服务器元数据的完整列表。

**数据** | **LINUX** 
--- | --- 
FQDN | cat/proc/sys/kernel/hostname，主机名-f
处理器核心计数 |  /proc/cpuinfo \| awk "/^ processor/{print $3}" \| wc.exe-l
分配的内存 | cat/proc/meminfo \| grep MemTotal \| awk "{printf"%. 0f "，$ 2/1024}"
BIOS 序列号 | lshw \| grep "串行：" \| head-n1 \| awk "{print $2}" <br/> /usr/sbin/dmidecode-t 1 \| Grep "串行" \| awk "{$1 =" "; $2 =" "; print}"
BIOS GUID | cat/sys/class/dmi/id/product_uuid
启动类型 | [-d/sys/firmware/efi]  && echo efi \| \| echo BIOS
操作系统名称/版本 | 我们将为 OS 版本和名称访问以下文件：<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
操作系统体系结构 | Uname-m
磁盘计数 | fdisk-l \| Egrep ' Disk. * bytes ' \| awk ' {print $2} ' \| cut-d '： '
启动磁盘 | df/boot \| sed-n 2 p \| awk "{print $1}"
磁盘大小 | fdisk-l \| Egrep "Disk. * bytes" \| egrep $disk： \| awk "{print $5}"
NIC 列表 | ip-o-4 地址显示\| awk "{print $2}"
NIC IP 地址 | ip 地址显示 $nic \| grep inet \| awk "{print $2}" \| cut-d "/" 
NIC MAC 地址 | ip 地址显示 $nic \| grep 网\| awk "{print $2}"

### <a name="windows-performance-data"></a>Windows 性能数据

下面是设备收集并发送到 Azure 的 Windows server 性能数据。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
CPU 使用率 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
内存使用率 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 计数 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 获取网络设备计数。
每 NIC 接收的数据 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
按 NIC 传输的数据 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
磁盘计数 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 磁盘计数
磁盘详细信息 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux 性能数据

下面是设备收集并发送到 Azure 的 Linux 服务器性能数据。

**数据** | **Linux** 
--- | --- 
CPU 使用率 | cat/proc/stat/| grep ' cpu '/proc/stat
内存使用率 | 免费\| grep Mem \| awk "{print $ 3/$ 2 * 100.0}"
NIC 计数 | lshw 类网络\| grep eth [0-60] \| wc.exe-l
每 NIC 接收的数据 | cat/sys/class/net/eth $ nic/statistics/rx_bytes
按 NIC 传输的数据 | cat/sys/class/net/eth $ nic/statistics/tx_bytes
磁盘计数 | fdisk-l \| Egrep ' Disk. * bytes ' \| awk ' {print $2} ' \| cut-d '： '
磁盘详细信息 | cat/proc/diskstats


## <a name="appliance-upgrades"></a>设备升级

在设备上运行的 Azure Migrate 代理更新时，会升级设备。 这会自动发生，因为默认情况下，在设备上启用了自动更新。 可以更改此默认设置，手动更新设备服务。

### <a name="turn-off-auto-update"></a>关闭自动更新

1. 在运行设备的计算机上，打开注册表编辑器。
2. 导航到**HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**"。
3. 若要禁用自动更新，请创建 DWORD 值为0的注册表项**自动**更新密钥。

    ![设置注册表项](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>启用自动更新

可以使用以下任一方法启用自动更新：

- 通过从 HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance. 删除自动更新注册表项
- 发现完成后，在设备 Configuration Manager。

删除注册表项：

1. 在运行设备的计算机上，打开注册表编辑器。
2. 导航到**HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**"。
3. 删除之前创建的注册表项**自动**更新，以关闭自动更新。

若要在设备 Configuration Manager 中打开，请在发现完成后执行以下操作：

1. 在设备计算机上，打开 "设备 Configuration Manager"。
2. 在 "**设备服务** > "**的 "自动更新" Azure Migrate 组件已关闭**，单击打开自动更新。

    ![启用自动更新](./media/migrate-appliance/turn-on.png)

### <a name="check-the-appliance-services-version"></a>检查设备服务版本

你可以使用以下方法之一检查设备服务版本：

- 在设备 Configuration Manager 中，发现完成后。
- 在设备计算机上，在 "**控制面板** > " 的 "**程序和功能**"。

若要签入设备 Configuration Manager：

1. 发现完成后，打开设备 Configuration Manager （在设备 web 应用中）。
2. 在 "**设备服务**" 中，验证设备服务版本。

    ![检查版本](./media/migrate-appliance/version.png)

若要签入控制面板，请执行以下操作：

1. 在设备上，单击 "**开始** > "**"控制面板" "** > **程序和功能**"
2. 查看列表中的 "设备服务版本"。

    ![检查控制面板中的版本](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>手动更新较早版本

如果你运行的是任何组件的较旧版本，则必须卸载该服务，并手动更新到最新版本。

1. 若要检查最新的设备服务版本，请[下载](https://aka.ms/latestapplianceservices)LatestComponents 文件。
2.  下载后，在记事本中打开 LatestComponents 文件。
3. 在文件中找到最新的服务版本，并找到该文件的下载链接。 例如：

    "Name"： "ASRMigrationWebApp"，"DownloadLink"： "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi"，"Version"： "6.0.211.2"，"Md5Hash"： "e00a742acc35e78a64a6a81e75469b84"

4.  使用文件中的下载链接下载过时服务的最新版本。
5. 下载后，请在管理员命令窗口中运行以下命令，以验证下载的 MSI 的完整性。

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```例如： C：\>CertUtil-HASHFILE C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. 检查命令输出是否与文件中服务的哈希值条目匹配（例如，上面的 MD5 哈希值）。
6. 现在，运行 MSI 以安装服务。 这是一个无提示安装，安装窗口完成后关闭。
7. 安装完成后，请在 "**控制面板** > " 的 "**程序和功能**" 中检查服务的版本。 现在，服务版本应升级到 json 文件中显示的最新版本。



## <a name="next-steps"></a>后续步骤

- [了解如何](how-to-set-up-appliance-vmware.md)为 VMware 设置设备。
- [了解如何](how-to-set-up-appliance-hyper-v.md)为 hyper-v 设置设备。
- [了解如何](how-to-set-up-appliance-physical.md)为物理服务器设置设备。

