---
title: Azure Migrate 设备
description: 提供服务器评估和迁移中使用的 Azure 迁移设备的概述。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 1c21f06e674871aefde1ae952a459db16feeb717
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676339"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 设备

本文总结了 Azure 迁移设备的先决条件和支持要求。 

## <a name="deployment-scenarios"></a>部署方案

Azure 迁移设备用于以下方案。

**方案** | **工具** | **用于** 
--- | --- | ---
**VMware VM 评估** | Azure 迁移：服务器评估 | 发现 VMware VM<br/><br/> 发现计算机应用和依赖项<br/><br/> 收集机器元数据和性能元数据进行评估。
**VMware VM 无代理迁移** | Azure 迁移：服务器迁移 | 发现 VMware VM <br/><br/> 使用无代理迁移复制 VMware VM。
**超 V VM 评估** | Azure 迁移：服务器评估 | 发现超虚拟机<br/><br/> 收集机器元数据和性能元数据进行评估。
**物理机器评估** |  Azure 迁移：服务器评估 |  发现物理服务器（或您视为物理服务器的 VM）。<br/><br/> 收集机器元数据和性能元数据进行评估。

## <a name="deployment-methods"></a>部署方法

可以使用以下几种方法部署设备：

- 可以使用 VMware VM 和超 VM 的模板（VMware 的 OVA 模板或用于 Hyper-V 的 VHD）部署设备。
- 如果不想使用模板，可以使用 PowerShell 脚本为 VMware 或 Hyper-V 部署设备。
- 在 Azure 政府中，应使用脚本部署设备。
- 对于物理服务器，您始终使用脚本部署设备。
- 下载链接可在下表中提供。


## <a name="appliance---vmware"></a>设备 - VMware 

下表总结了 VMware 的 Azure 迁移设备要求。

**要求** | **VMware** 
--- | ---
**设备组件** | 产品具有以下组件：<br/><br/> - **管理应用**：这是一个 Web 应用程序，用于在设备部署期间用户输入。 用于评估计算机以迁移到 Azure 时使用。<br/> - **发现代理**：代理收集计算机配置数据。 用于评估计算机以迁移到 Azure 时使用。<br/>- **评估代理**：代理收集性能数据。 用于评估计算机以迁移到 Azure 时使用。<br/>- **自动更新服务**：更新设备组件（每 24 小时运行一次）。<br/>- **DRA 代理**：协调 VM 复制，并协调复制的计算机和 Azure 之间的通信。 仅在使用无代理迁移将 VMware VM 复制到 Azure 时使用。<br/>- **网关**：向 Azure 发送复制的数据。 仅在使用无代理迁移将 VMware VM 复制到 Azure 时使用。
**支持部署** | 使用 OVA 模板部署为 VMware VM。<br/><br/> 使用 PowerShell 安装脚本部署为 VMware VM 或物理计算机。
**项目支持** |  设备可以与单个项目关联。 <br/> 任意数量的设备都可以与单个项目关联。<br/> 
**发现限制** | 设备可以在 vCenter 服务器上发现多达 10，000 个 VMware VM。<br/> 设备可以连接到单个 vCenter 服务器。
**OVA 模板** | 从门户或 从https://aka.ms/migrate/appliance/vmware下载。<br/><br/> 下载大小为 11.2 GB。<br/><br/> 下载的设备模板附带 Windows Server 2016 评估许可证，有效期为 180 天。 如果评估期接近到期，我们建议您下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**电源外壳脚本** | 脚本[下载](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**软件/硬件** |  设备应在具有 Windows Server 2016、32 GB RAM、8 个 vCPU、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 本产品需要直接或通过代理进行互联网接入。<br/><br/> 如果在 VMware VM 上运行设备，则在 vCenter 服务器上需要足够的资源来分配满足要求的 VM。<br/><br/> 如果在物理计算机上运行设备，请确保它运行 Windows Server 2016，并满足硬件要求。 
**VMware 要求** | 如果将设备部署为 VMware VM，则必须将其部署在运行版本 5.5 或更高版本的 ESXi 主机上。<br/><br/> vCenter 服务器运行 5.5、6.0、6.5 或 6.7。
**VDDK（无代理迁移）** | 如果将设备部署为 VMware VM，并且正在运行无代理迁移，则必须在设备 VM 上安装 VMware vSphere VDDK。
**哈希值-OVA** | [验证](tutorial-assess-vmware.md#verify-security)OVA 模板哈希值。
**哈希值-电源外壳脚本** | [验证](deploy-appliance-script.md#verify-file-security)PowerShell 脚本哈希值。




## <a name="appliance---hyper-v"></a>设备 - 超 V

**要求** | **Hyper-V** 
--- | ---
**设备组件** | 产品具有以下组件：<br/><br/>- **管理应用**：这是一个 Web 应用程序，用于在设备部署期间用户输入。 用于评估计算机以迁移到 Azure 时使用。<br/> - **发现代理**：代理收集计算机配置数据。 用于评估计算机以迁移到 Azure 时使用。<br/>- **评估代理**：代理收集性能数据。 用于评估计算机以迁移到 Azure 时使用。<br/>- **自动更新服务**：更新设备组件（每 24 小时运行一次）。
**支持部署** | 使用 VHD 模板部署为 Hyper-V VM。<br/><br/> 使用 PowerShell 安装脚本部署为 Hyper-V VM 或物理计算机。
**项目支持** |  设备可以与单个项目关联。 <br/> 任意数量的设备都可以与单个项目关联。<br/> 
**发现限制** | 设备可以发现多达 5000 个超 V VM。<br/> 设备可连接到多达 300 台 Hyper-V 主机。
**VHD 模板** | 压缩文件夹，包括 VHD。 从门户或 从https://aka.ms/migrate/appliance/hyperv下载。<br/><br/> 下载大小为 10 GB。<br/><br/> 下载的设备模板附带 Windows Server 2016 评估许可证，有效期为 180 天。 如果评估期接近到期，我们建议您下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**电源外壳脚本** | 脚本[下载](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**软件/硬件***   |  设备应在具有 Windows Server 2016、32 GB RAM、8 个 vCPU、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接或通过代理进行 Internet 访问。<br/><br/> 如果将设备作为 Hyper-V VM 运行，则 Hyper-V 主机上需要足够的资源来分配 16 GB RAM、8 个 vCPU、大约 80 GB 的存储空间以及设备 VM 的外部交换机。<br/><br/> 如果在物理计算机上运行设备，请确保它运行 Windows Server 2016，并满足硬件要求。 
**超 V 要求** | 如果使用 VHD 模板部署设备，Azure 迁移提供的设备 VM 是 Hyper-V VM 版本 5.0。<br/><br/> Hyper-V 主机必须运行 Windows Server 2012 R2 或更高版本。 
**哈希值-VHD** | [验证](tutorial-assess-hyper-v.md#verify-security)VHD 模板哈希值。
**哈希值-电源外壳脚本** | [验证](deploy-appliance-script.md#verify-file-security)PowerShell 脚本哈希值。


## <a name="appliance---physical"></a>设备 - 物理

**要求** | **物理** 
--- | ---
**设备组件** | 产品具有以下组件： <br/><br/> - **管理应用**：这是一个 Web 应用程序，用于在设备部署期间用户输入。 用于评估计算机以迁移到 Azure 时使用。<br/> - **发现代理**：代理收集计算机配置数据。 用于评估计算机以迁移到 Azure 时使用。<br/>- **评估代理**：代理收集性能数据。 用于评估计算机以迁移到 Azure 时使用。<br/>- **自动更新服务**：更新设备组件（每 24 小时运行一次）。
**支持部署** | 使用 PowerShell 安装脚本部署为专用物理计算机或 VM。 该脚本可从门户下载。
**项目支持** |  设备可以与单个项目关联。 <br/> 任意数量的设备都可以与单个项目关联。<br/> 
**发现限制** | 设备可以发现多达 250 台物理服务器。
**电源外壳脚本** | 从门户将脚本 （AzureMigrate 安装程序.ps1） 下载到压缩文件夹中。 [了解详细信息](tutorial-assess-physical.md#set-up-the-appliance)。 或者，[直接下载](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 下载大小为 59.7 MB。
**软件/硬件** |  设备应在具有 Windows Server 2016、32 GB RAM、8 个 vCPU、大约 80 GB 磁盘存储和外部虚拟交换机的计算机上运行。<br/> 设备需要静态或动态 IP 地址，并且需要直接或通过代理进行 Internet 访问。<br/><br/> 如果在物理计算机上运行设备，请确保它运行 Windows Server 2016，并满足硬件要求。 
**哈希值** | [验证](deploy-appliance-script.md#verify-file-security)PowerShell 脚本哈希值。

## <a name="url-access"></a>URL 访问

Azure 迁移设备需要连接到 Internet。

- 部署设备时，Azure 迁移会对所需的 URL 执行连接检查。
- 如果您使用的是基于 URL 的代理连接到互联网，则需要允许访问这些 URL，确保代理解析在查找 URL 时收到的任何 CNAME 记录。

### <a name="public-cloud-urls"></a>公共云 URL

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到 Azure 门户。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登录到 Azure 订阅。
*.microsoftonline.com <br/> *.microsoftonline-p.com | 为设备创建 Azure 活动目录 （AD） 应用，以便与 Azure 迁移进行通信。
management.azure.com | 为设备创建 Azure AD 应用，以便与 Azure 迁移服务进行通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.azure.net | 在 Azure 密钥保管库中管理机密。
aka.ms/* | 允许访问也称为链接。 用于 Azure 迁移设备更新。
download.microsoft.com/download | 允许从微软下载下载。
*.servicebus.windows.net | 设备和 Azure 迁移服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 连接到 Azure 迁移服务 URL。
*.hypervrecoverymanager.windowsazure.com | **用于无 VMware 无代理迁移**<br/><br/> 连接到 Azure 迁移服务 URL。
\* .blob.core.windows.net |  **用于无 VMware 无代理迁移**<br/><br/>将数据上载到存储以进行迁移。

### <a name="government-cloud-urls"></a>政府云 URL

**URL** | **详细信息**  
--- | --- |
*.portal.azure.us  | 导航到 Azure 门户。
graph.windows.net | 登录到 Azure 订阅。
login.microsoftonline.us  | 为设备创建 Azure 活动目录 （AD） 应用，以便与 Azure 迁移进行通信。
management.usgovcloudapi.net | 为设备创建 Azure AD 应用，以便与 Azure 迁移服务进行通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.usgovcloudapi.net | 在 Azure 密钥保管库中管理机密。
aka.ms/* | 允许访问也称为链接。 用于 Azure 迁移设备更新。
download.microsoft.com/download | 允许从微软下载下载。
*.servicebus.usgovcloudapi.net  | 设备和 Azure 迁移服务之间的通信。
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | 连接到 Azure 迁移服务 URL。
*.hypervrecoverymanager.windowsazure.us | **用于无 VMware 无代理迁移**<br/><br/> 连接到 Azure 迁移服务 URL。
*.blob.core.usgovcloudapi.net  |  **用于无 VMware 无代理迁移**<br/><br/>将数据上载到存储以进行迁移。
*.applicationinsights.us | 上传用于内部监视的应用日志。





## <a name="collected-data---vmware"></a>收集的数据 - VMware

设备收集元数据、性能数据和依赖关系分析数据（如果使用无代理[依赖关系分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>元数据

Azure 迁移应用发现元数据可帮助您确定计算机和应用是否已准备好迁移到 Azure、大小合适的计算机和应用、计划成本并分析应用程序依赖项。 Microsoft 不会在任何许可证合规性审核中使用此数据。

下面是设备收集和发送到 Azure 的 VMware VM 元数据的完整列表。

**数据** | **计数器**
--- | --- 
**机器详细信息** | 
VM ID | vm.Config.InstanceUuid 
VM 名称 | vm.Config.Name
vCenter Server ID | VMwareClient.实例.Uuid
VM 说明 | vm.Summary.Config.Annotation
许可证产品名称 | vm.Client.ServiceContent.About.LicenseProductName
操作系统类型 | Vm。摘要配置.来宾全名
启动类型 | vm.Config.Firmware
内核数 | vm.Config.Hardware.NumCPU
内存(MB) | vm.Config.Hardware.MemoryMB
磁盘数目 | Vm。配置.硬件.设备.列表。）查找全部（x =>是虚拟磁盘）。
磁盘大小列表 | Vm。配置.硬件.设备.列表。）查找全部（x =>是虚拟磁盘）
网络适配器列表 | Vm。配置.硬件.设备.列表。）查找全部（x =>是虚拟以太网）。
CPU 使用率 | cpu.usage.average
内存利用率 |mem.usage.average
**每个磁盘详细信息** | 
磁盘密钥值 | disk.Key
迪库奈特编号 | disk.UnitNumber
磁盘控制器密钥值 | disk.ControllerKey.Value
预配量 (GB) | virtualDisk.DeviceInfo.Summary
磁盘名称 | 使用磁盘生成的值。单位编号，磁盘。钥匙，磁盘控制器键.VAlue
每秒读取操作数 | virtualDisk.numberReadAveraged.average
每秒写入操作数 | virtualDisk.numberWriteAveraged.average
读取吞吐量（每秒 MB） | virtualDisk.read.average
写入吞吐量（每秒 MB） | virtualDisk.write.average
**每个 NIC 详细信息** | 
网络适配器名称 | nic.Key
MAC 地址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 地址 | vm.Guest.Net
IPv6 地址 | vm.Guest.Net
读取吞吐量（每秒 MB） | net.received.average
写入吞吐量（每秒 MB） | net.transmitted.average
**库存路径详细信息** | 
名称 | container.GetType().Name
子对象类型 | container.ChildType
引用详细信息 | container.MoRef
父级详细信息 | Container.Parent
每个 VM 的文件夹详细信息 | ((Folder)container).ChildEntity.Type
每个 VM 的数据中心详细信息 | ((Datacenter)container).VmFolder
每个主机文件夹的数据中心详细信息 | ((Datacenter)container).HostFolder
每个主机的群集详细信息 | （（群集计算资源）容器）。主机
每个 VM 的主机详细信息 | （（主机系统）容器）。Vm

### <a name="performance-data"></a>性能数据


以下是设备收集和发送到 Azure 的 VMware VM 性能数据。

**数据** | **计数器** | **评估影响**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 推荐的 VM 大小/成本
内存利用率 | mem.usage.average | 推荐的 VM 大小/成本
磁盘读取吞吐量（MB/秒） | virtualDisk.read.average | 计算磁盘大小、存储成本、VM 大小
磁盘写入吞吐量（MB/秒） | virtualDisk.write.average | 计算磁盘大小、存储成本、VM 大小
每秒磁盘读取操作 | virtualDisk.numberReadAveraged.average | 计算磁盘大小、存储成本、VM 大小
每秒磁盘写入操作 | virtualDisk.numberWriteAveraged.average  | 计算磁盘大小、存储成本、VM 大小
NIC 读取吞吐量（MB/秒） | net.received.average | 计算 VM 大小
NIC 写入吞吐量（MB/秒） | net.transmitted.average  |计算 VM 大小

### <a name="app-dependencies-metadata"></a>应用依赖项元数据

无代理依赖项分析收集连接和处理数据。

#### <a name="connection-data"></a>连接数据

以下是设备从启用用于无代理依赖项分析的每个 VM 中收集的连接数据。 此数据将发送到 Azure。

**数据** | **使用的命令** 
--- | --- 
本地端口 | netstat
本地 IP 地址 | netstat
远程端口 | netstat
远程 IP 地址 | netstat
TCP 连接状态 | netstat
进程 ID | netstat
不是。 活动连接 | netstat

#### <a name="process-data"></a>处理数据
以下是设备从启用的无代理依赖项分析的每个 VM 收集的进程数据。 此数据将发送到 Azure。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
进程名称 | Win32_Process | 可执行路径
进程参数 | Win32_Process | CommandLine
应用程序名称 | Win32_Process | 可执行路径属性的版本信息.产品名称参数

#### <a name="linux-vm-data"></a>Linux VM 数据

以下是设备从启用的无代理依赖项分析的每个 Linux VM 中收集的连接和处理数据。 此数据将发送到 Azure。

**数据** | **使用的命令** 
--- | ---
本地端口 | netstat 
本地 IP 地址 | netstat 
远程端口 | netstat 
远程 IP 地址 | netstat 
TCP 连接状态 | netstat 
不是。 活动连接 | netstat
进程 ID  | netstat 
进程名称 | ps
进程参数 | ps
应用程序名称 | dpkg 或 rpm



## <a name="collected-data---hyper-v"></a>收集的数据 - 超 V

设备收集元数据、性能数据和依赖关系分析数据（如果使用无代理[依赖关系分析](concepts-dependency-visualization.md)）。

### <a name="metadata"></a>元数据
Azure 迁移应用发现元数据可帮助您确定计算机和应用是否已准备好迁移到 Azure、大小合适的计算机和应用、计划成本并分析应用程序依赖项。 Microsoft 不会在任何许可证合规性审核中使用此数据。

下面是设备收集和发送到 Azure 的 Hyper-V VM 元数据的完整列表。

**数据* | **WMI CLASS** | **WMI CLASS 属性**
--- | --- | ---
**机器详细信息** | 
BIOS 的序列号 = Msvm_BIOSElement | BIOS序列编号
VM 类型（第 1 代或第 2 代） | Msvm_VirtualSystemSettingData | 虚拟系统子类型
VM 显示名称 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | 虚拟数量
内存（字节） | Msvm_MemorySettingData | 虚拟数量
VM 可使用的最大内存 | Msvm_MemorySettingData | 限制
启用动态内存 | Msvm_MemorySettingData | DynamicMemoryEnabled
操作系统名称/版本/FQDN | Msvm_KvpExchangeComponent | 来宾内部交换项目名称数据
VM 电源状态 | Msvm_ComputerSystem | EnabledState
**每个磁盘详细信息** | 
磁盘标识符 | Msvm_VirtualHardDiskSettingData | 虚拟磁盘Id
虚拟硬盘类型 | Msvm_VirtualHardDiskSettingData | 类型
虚拟硬盘大小 | Msvm_VirtualHardDiskSettingData | 最大内部大小
虚拟硬盘父级 | Msvm_VirtualHardDiskSettingData | 父路径
**每个 NIC 详细信息** | 
IP 地址（合成 NIC） | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
启用 DHCP（合成 NIC） | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID（合成 NIC） | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 地址（合成 NIC） | Msvm_SyntheticEthernetPortSettingData | 地址
NIC ID（旧 NIC） | Ms模拟以太网端口设置数据 | InstanceID
NIC MAC ID（旧 NIC） | Ms模拟以太网端口设置数据 | 地址

### <a name="performance-data"></a>性能数据

以下是设备收集和发送到 Azure 的 Hyper VM 性能数据。

**性能计数器类** | **计数器** | **评估影响**
--- | --- | ---
超V虚拟机管理程序虚拟处理器 | 百分比来宾运行时间 | 推荐的 VM 大小/成本
超 V 动态内存 VM | 电流压力 （%）<br/> 访客可见物理内存 （MB） | 推荐的 VM 大小/成本
超V虚拟存储设备 | 读取字节/秒 | 计算磁盘大小、存储成本、VM 大小
超V虚拟存储设备 | 写入字节/秒 | 计算磁盘大小、存储成本、VM 大小
Hyper-V 虚拟网络适配器 | 接收字节/秒 | 计算 VM 大小
Hyper-V 虚拟网络适配器 | 发送字节/秒 | 计算 VM 大小

- CPU 利用率是连接到 VM 的所有虚拟处理器的所有使用情况的总和。
- 内存利用率为（当前压力 = 来宾可见物理内存）/ 100。
- 磁盘和网络利用率值从列出的 Hyper-V 性能计数器收集。

## <a name="appliance-upgrades"></a>设备升级

随着在设备上运行的 Azure 迁移代理更新，设备将升级。 这是自动发生的，因为默认情况下在设备上启用了自动更新。 您可以更改此默认设置以手动更新代理。

- **关闭自动更新**：通过将HKEY_LOCAL_MACHINE_SOFTWARE_Microsoft_AzureAppliance"自动更新"键设置为 0 （DWORD）来关闭注册表中的自动更新。 如果您决定使用手动更新，请务必同时更新设备上的所有代理，使用设备上每个过期代理的 **"更新**"按钮。
- **手动更新**：对于手动更新，请确保使用设备上每个过期代理的 **"更新**"按钮更新设备上的所有代理。 您可以随时将更新设置切换回自动更新。

![自动更新设备](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>后续步骤

- [了解如何](how-to-set-up-appliance-vmware.md)为 VMware 设置设备。
- [了解如何](how-to-set-up-appliance-hyper-v.md)为 Hyper-V 设置产品。
- [了解如何](how-to-set-up-appliance-physical.md)为物理服务器设置设备。

