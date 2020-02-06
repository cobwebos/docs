---
title: Azure Migrate 设备
description: 提供服务器评估和迁移中所使用的 Azure Migrate 设备的概述。
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 652fe9d379d6e2ba50e9e282f384905e154368d8
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031657"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 设备

本文介绍 Azure Migrate 设备。 使用[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具来发现和评估要迁移到 Microsoft Azure 的应用、基础结构和工作负载时，可以部署设备。 将 VMware Vm 迁移到 Azure 时，还可以使用 Azure Migrate：使用[无代理迁移](server-migrate-overview.md)的[服务器评估](migrate-services-overview.md#azure-migrate-server-migration-tool)将 VMware vm 迁移到 Azure。

## <a name="appliance-overview"></a>设备概述

在以下方案中使用 Azure Migrate 设备。

**方案** | **工具** | **用于** 
--- | --- | ---
VMware VM | Azure Migrate：服务器评估<br/><br/> Azure Migrate：服务器迁移 | 发现 VMware Vm<br/><br/> 发现计算机应用和依赖项<br/><br/> 收集计算机元数据和性能元数据以进行评估。<br/><br/> 复制 VMware Vm 和无代理迁移。
Hyper-V VM | Azure Migrate：服务器评估 | 发现 Hyper-v Vm<br/><br/> 收集计算机元数据和性能元数据以进行评估。
物理机 |  Azure Migrate：服务器评估 |  发现物理服务器<br/><br/> 收集计算机元数据和性能元数据以进行评估。

## <a name="appliance---vmware"></a>设备-VMware 

**要求** | **VMware** 
--- | ---
**下载格式** | ..OVA 
**下载链接** | https://aka.ms/migrate/appliance/vmware 
**下载大小** | 11.2 GB
**许可证** | 下载的设备模板附带了 Windows Server 2016 评估版许可证，该许可证在180天内有效。 如果评估期接近过期，我们建议你下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**部署** | 将设备部署为 VMware VM。 VCenter Server 上需要足够的资源来分配具有 32 GB RAM 的 VM、8个 vcpu、大约 80 GB 的磁盘存储和外部虚拟交换机。<br/> 设备需要直接或通过代理访问 internet。<br/> 设备可以连接到单个 vCenter Server。
**硬件** | VCenter 上的资源，用于分配含 32 GB RAM 8 个 vcpu 的 VM、大约 80 GB 的磁盘存储和外部虚拟交换机。 
**哈希值** | MD5： c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256：4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**vCenter 服务器/主机** | 设备 VM 必须部署在运行版本5.5 或更高版本的 ESXi 主机上。<br/><br/> 运行5.5、6.0、6.5 或 6.7 vCenter Server。
**Azure Migrate 项目** | 设备可以与单个项目关联。 <br/> 可以将任意数量的设备与单个项目相关联。<br/> 
**发现** | 设备最多可在 vCenter Server 上发现 10000 VMware Vm。<br/> 设备可以连接到单个 vCenter Server。
**设备组件** | 管理应用：部署期间用于用户输入的设备中的 Web 应用。<br/> 发现代理：收集计算机配置数据。<br/> 评估代理：收集性能数据。<br/> DRA：协调 VM 复制，并协调计算机/Azure 之间的通信。<br/> 网关：将复制的数据发送到 Azure。<br/> 自动更新服务：更新组件（每24小时运行一次）。
**VDDK （无代理迁移）** | 如果在 Azure Migrate 服务器迁移的情况下运行无代理迁移，则必须在设备 VM 上安装 VMware vSphere VDDK。


## <a name="appliance---hyper-v"></a>设备-Hyper-v

**要求** | **Hyper-V** 
--- | ---
**下载格式** | 压缩文件夹（包含 VHD）
**下载链接** | https://aka.ms/migrate/appliance/hyperv 
**下载大小** | 10 GB
**许可证** | 下载的设备模板附带了 Windows Server 2016 评估版许可证，该许可证在180天内有效。 如果评估期接近过期，我们建议你下载并部署新设备，或者激活设备 VM 的操作系统许可证。
**设备部署**   |  将设备部署为 Hyper-v VM。<br/> Azure Migrate 提供的设备 VM 为 Hyper-v VM 版本5.0。<br/> Hyper-v 主机必须运行 Windows Server 2012 R2 或更高版本。<br/> 主机需要足够的空间来分配 16 GB RAM、8个 vcpu、大约 80 GB 的存储空间，以及设备 VM 的外部交换机。<br/> 设备需要静态或动态 IP 地址以及 internet 访问。
**硬件** | Hyper-v 主机上的资源，用于分配 16 GB RAM、8个 vcpu、大约 80 GB 的存储空间，以及设备 VM 的外部交换机。
**哈希值** | MD5：29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256：37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Hyper-V 主机** | 正在运行 Windows Server 2012 R2 或更高版本。
**Azure Migrate 项目** | 设备可以与单个项目关联。 <br/> 可以将任意数量的设备与单个项目相关联。<br/> 
**发现** | 设备最多可在 vCenter Server 上发现 5000 VMware Vm。<br/> 一个设备最多可以连接到300个 Hyper-v 主机。
**设备组件** | 管理应用：部署期间用于用户输入的设备中的 Web 应用。<br/> 发现代理：收集计算机配置数据。<br/> 评估代理：收集性能数据。<br/>  自动更新服务：更新组件（每24小时运行一次）。


## <a name="appliance---physical"></a>设备-物理

**要求** | **物理** 
--- | ---
**下载格式** | 压缩文件夹（包含基于 PowerShell 的安装程序脚本）
**下载链接** | [下载链接](https://go.microsoft.com/fwlink/?linkid=2105112)
**下载大小** | 59.7 MB
**硬件** | 专用物理计算机，或使用虚拟机。 运行设备的计算机需要 16 GB RAM、8个 vcpu、大约 80 GB 的存储空间和一个外部交换机。<br/> 设备需要静态或动态 IP 地址以及 internet 访问。
**哈希值** | MD5：1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256： a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**操作系统** | 设备计算机应运行 Windows Server 2016。 
**设备部署**   |  设备安装程序脚本是从门户下载的（在压缩文件夹中）。 <br/> 解压文件夹，并运行 PowerShell 脚本（AzureMigrateInstaller）。
**发现** | 一个设备最多可以发现250个物理服务器。
**设备组件** | 管理应用：部署期间用于用户输入的设备中的 Web 应用。<br/> 发现代理：收集计算机配置数据。<br/> 评估代理：收集性能数据。<br/>  自动更新服务：更新组件（每24小时运行一次）。


## <a name="url-access"></a>URL 访问

Azure Migrate 设备需要连接到 internet。

- 部署设备时，Azure Migrate 会对下表中汇总的 Url 进行连接性检查。
- 如果使用基于 URL 的代理连接到 internet，则允许访问这些 Url，确保代理解析查找 Url 时收到的任何 CNAME 记录。

**URL** | **详细信息**  
--- | --- |
*.portal.azure.com  | 导航到 Azure 门户。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | 登录到 Azure 订阅。
*.microsoftonline.com <br/> *.microsoftonline-p.com | 为设备创建 Active Directory 应用程序，以便与 Azure Migrate 通信。
management.azure.com | 为设备创建 Active Directory 应用程序，以便与 Azure Migrate 服务通信。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
*.vault.azure.net | 管理 Azure Key Vault 中的机密。
aka.ms/* | 允许访问称为 "链接"。 用于 Azure Migrate 设备更新。
download.microsoft.com/download | 允许从 Microsoft 下载下载。
*.servicebus.windows.net | 设备与 Azure Migrate 服务之间的通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | 连接到 Azure Migrate 服务 Url。
*.hypervrecoverymanager.windowsazure.com | **用于 VMware 无代理迁移**<br/><br/> 连接到 Azure Migrate 服务 Url。
\* .blob.core.windows.net |  **用于 VMware 无代理迁移**<br/><br/>将数据上传到存储进行迁移。




## <a name="collected-data---vmware"></a>收集的数据-VMware

### <a name="collected-performance-data-vmware"></a>收集的性能数据-VMware

下面是设备收集并发送到 Azure 的 VMware VM 性能数据。

**数据** | **计数器** | **评估影响**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建议的 VM 大小/成本
内存使用率 | mem.usage.average | 建议的 VM 大小/成本
磁盘读取吞吐量（MB/秒） | virtualDisk.read.average | 磁盘大小、存储成本、VM 大小的计算
磁盘写入吞吐量（MB/秒） | virtualDisk.write.average | 磁盘大小、存储成本、VM 大小的计算
每秒磁盘读取操作数 | virtualDisk.numberReadAveraged.average | 磁盘大小、存储成本、VM 大小的计算
磁盘写入操作数/秒 | virtualDisk.numberWriteAveraged.average  | 磁盘大小、存储成本、VM 大小的计算
NIC 读取吞吐量（MB/秒） | net.received.average | VM 大小的计算
NIC 写入吞吐量（MB/秒） | net.transmitted.average  |VM 大小的计算


### <a name="collected-metadata-vmware"></a>收集的元数据-VMware

> [!NOTE]
> Azure Migrate 设备发现的元数据用于在将应用程序迁移到 Azure、执行 Azure 适用性分析、应用程序依赖关系分析和成本规划时，帮助你适当调整应用程序的大小。 Microsoft 不会将此数据与任何许可证相容性审核相对使用。

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
内存(MB) | vm.Config.Hardware.MemoryMB
磁盘数目 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁盘大小列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
网络适配器列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
内存使用率 |mem.usage.average
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

## <a name="collected-data---hyper-v"></a>收集的数据-Hyper-v

### <a name="collected-performance-data-hyper-v"></a>收集的性能数据-Hyper-v

> [!NOTE]
> Azure Migrate 设备发现的元数据用于在将应用程序迁移到 Azure、执行 Azure 适用性分析、应用程序依赖关系分析和成本规划时，帮助你适当调整应用程序的大小。 Microsoft 不会将此数据与任何许可证相容性审核相对使用。

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

### <a name="collected-metadata-hyper-v"></a>收集的元数据-Hyper-v

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
NIC MAC 地址（合成 Nic） | Msvm_SyntheticEthernetPortSettingData | Address
NIC ID （旧 Nic） | MsvmEmulatedEthernetPortSetting 数据 | InstanceID
NIC MAC ID （旧 Nic） | MsvmEmulatedEthernetPortSetting 数据 | Address




## <a name="discovery-and-collection-process"></a>发现和收集过程

设备使用以下过程与 vCenter 服务器和 Hyper-v 主机/群集通信。

1. **开始发现**：
    - 当你在 Hyper-v 设备上启动发现时，它会与 WinRM 端口5985（HTTP）和5986（HTTPS）上的 Hyper-v 主机通信。
    - 在 VMware 设备上启动发现时，默认情况下，它会与 TCP 端口443上的 vCenter 服务器通信。 如果 vCenter 服务器侦听其他端口，则可以在设备 web 应用中进行配置。
2. **收集元数据和性能数据**：
    - 设备使用通用信息模型（CIM）会话来收集端口5985和5986上的 Hyper-v 主机上的 Hyper-v VM 数据。
    - 默认情况下，设备与端口443通信，以便从 vCenter Server 收集 VMware VM 数据。
3. **发送数据**：设备将收集的数据发送到 Azure Migrate 服务器评估，并通过 SSL 端口 443 Azure Migrate 服务器迁移。 设备可以通过 internet 连接到 Azure，也可以将 ExpressRoute 用于公共/Microsoft 对等互连。
    - 对于性能数据，设备会收集实时利用率数据。
        - 对于 VMware，每隔20秒收集一次性能数据，对于每个性能指标，将每30秒收集一次。
        - 收集的数据将汇总到10分钟后创建单个数据点。
        - 峰值利用率值是从所有 20/30 秒数据点中选择的，并发送到 Azure 进行评估计算。
        - 根据在评估属性（第 50/90/95/99%）中指定的百分位值，按升序对十分钟点进行排序，并使用适当的百分位值计算评估
    - 对于服务器迁移，设备会开始收集 VM 数据，并将其复制到 Azure。
4. **评估和迁移**：你现在可以使用 Azure Migrate Server 评估从设备收集的元数据中创建评估。 此外，还可以使用 Azure Migrate Server 迁移开始迁移 VMware Vm，以安排无代理 VM 复制。


![体系结构](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>设备升级

在设备上运行的 Azure Migrate 代理更新时，会升级设备。

- 这会自动发生，因为默认情况下在设备上启用了自动更新。
- 您可以更改此默认设置以手动更新代理。
- 若要禁用自动更新，请在 "注册表编辑器" > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance "，并将注册表项" 自动更新 "设置为0（DWORD）。
 
### <a name="set-agent-updates-to-manual"></a>将代理更新设置为手动

对于手动更新，请确保在设备上使用每个过时代理的 "**更新**" 按钮同时更新设备上的所有代理。 你可以随时将更新设置切换回自动更新。

## <a name="next-steps"></a>后续步骤

[了解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)为 VMware 设置设备。
[了解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)为 hyper-v 设置设备。

