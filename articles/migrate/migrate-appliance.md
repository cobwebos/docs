---
title: Azure Migrate 设备体系结构 |Microsoft Docs
description: 提供 Azure Migrate 设备的概述
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c31a9fde50de8190cdd7bc19600344a8e58cf60b
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827318"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 设备

本文介绍 Azure Migrate 设备。 当你使用 Azure Migrate 评估和迁移工具来发现、评估和迁移应用、基础结构和工作负载以 Microsoft Azure 时, 你可以部署设备。 

[Azure Migrate](migrate-services-overview.md)提供了一个中心, 用于跟踪对 Azure 的本地应用程序、工作负荷以及专用/公有云 vm 的发现、评估和迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。



## <a name="appliance-overview"></a>设备概述

Azure Migrate 设备类型和用法如下所示。

**部署为** | 用途 | **详细信息**
--- | --- |  ---
VMware VM | Azure Migrate 评估工具的 VMware VM 评估。<br/><br/> 采用 Azure Migrate Server 迁移工具的 VMware VM 无代理迁移 | 下载 .OVA 模板并导入到 vCenter Server, 以创建设备 VM。
Hyper-V VM | 通过 Azure Migrate 评估工具进行 hyper-v VM 评估。 | 下载压缩的 VHD 并将其导入到 Hyper-v 以创建设备 VM。

## <a name="appliance-access"></a>设备访问

配置设备后, 可通过 TCP 端口3389远程访问设备 VM。 你还可以在端口44368上为设备远程访问 web 管理应用, URL 为: `https://<appliance-ip-or-name>:44368`。

## <a name="appliance-license"></a>设备许可证
该设备附带了 Windows Server 2016 评估版许可证, 该许可证在180天内有效。 如果评估期接近过期, 我们建议你下载并部署新设备, 或者激活设备 VM 的操作系统许可证。

## <a name="appliance-agents"></a>设备代理
设备安装了这些代理。

**代理** | **详细信息**
--- | ---
发现代理 | 收集本地虚拟机的配置数据
评估代理 | 分析本地环境以收集 VM 性能数据。
迁移适配器 | 协调 VM 复制, 并协调 Vm 与 Azure 之间的通信。
迁移网关 | 将复制的 VM 数据发送到 Azure。


## <a name="appliance-deployment-requirements"></a>设备部署要求

- [查看](migrate-support-matrix-vmware.md#assessment-appliance-requirements)VMware 设备的部署要求以及设备需要访问的 url。
- [查看](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)hyper-v 设备的部署要求, 以及设备需要访问的 url。


## <a name="collected-performance-data-vmware"></a>收集的性能数据-VMware

下面是设备收集并发送到 Azure 的 VMware VM 性能数据。

**数据** | **计数器** | **评估影响**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建议的 VM 大小/成本
内存使用情况 | mem.usage.average | 建议的 VM 大小/成本
磁盘读取吞吐量 (MB/秒) | virtualDisk.read.average | 磁盘大小、存储成本、VM 大小的计算
磁盘写入吞吐量 (MB/秒) | virtualDisk.write.average | 磁盘大小、存储成本、VM 大小的计算
每秒磁盘读取操作数 | virtualDisk.numberReadAveraged.average | 磁盘大小、存储成本、VM 大小的计算
磁盘写入操作数/秒 | virtualDisk.numberWriteAveraged.average  | 磁盘大小、存储成本、VM 大小的计算
NIC 读取吞吐量 (MB/秒) | net.received.average | VM 大小的计算
NIC 写入吞吐量 (MB/秒) | net.transmitted.average  |VM 大小的计算


## <a name="collected-metadata-vmware"></a>收集的元数据-VMware

下面是设备收集并发送到 Azure 的 VMware VM 元数据的完整列表。

**数据** | **计数器**
--- | --- 
**计算机详细信息** | 
虚拟机 ID | vm.Config.InstanceUuid 
VM 名称 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
VM 说明 | vm.Summary.Config.Annotation
许可证产品名称 | vm.Client.ServiceContent.About.LicenseProductName
操作系统类型 | vm.SummaryConfig.GuestFullName
引导类型 | vm.Config.Firmware
内核数 | vm.Config.Hardware.NumCPU
内存(MB) | vm.Config.Hardware.MemoryMB
磁盘数量 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁盘大小列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
网络适配器列表 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
内存使用情况 |mem.usage.average
**每磁盘详细信息** | 
磁盘密钥值 | disk.Key
Dikunit 号 | disk.UnitNumber
磁盘控制器密钥值 | disk.ControllerKey.Value
预配量 (GB) | virtualDisk.DeviceInfo.Summary
磁盘名称 | 使用磁盘生成的值。UnitNumber, 磁盘。密钥和磁盘。ControllerKey
每秒读取操作数 | virtualDisk.numberReadAveraged.average
每秒写入操作数 | virtualDisk.numberWriteAveraged.average
读取吞吐量 (MB/秒) | virtualDisk.read.average
写入吞吐量 (MB/秒) | virtualDisk.write.average
**每 NIC 详细信息** | 
网络适配器名称 | nic.Key
MAC 地址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 地址 | vm.Guest.Net
IPv6 地址 | vm.Guest.Net
读取吞吐量 (MB/秒) | net.received.average
写入吞吐量 (MB/秒) | net.transmitted.average
**清单路径详细信息** | 
名称 | container.GetType().Name
子对象类型 | container.ChildType
引用详细信息 | container.MoRef
父级详细信息 | Container.Parent
每个 VM 的文件夹详细信息 | ((Folder)container).ChildEntity.Type
每个 VM 的数据中心详细信息 | ((Datacenter)container).VmFolder
每个主机文件夹的数据中心详细信息 | ((Datacenter)container).HostFolder
每个主机的群集详细信息 | ((ClusterComputeResource) 容器。主持人
每个 VM 的主机详细信息 | ((HostSystem) 容器。VMNETWORK



## <a name="collected-performance-data-hyper-v"></a>收集的性能数据-Hyper-v

下面是设备收集并发送到 Azure 的 VMware VM 性能数据。

**性能计数器类** | **计数器** | **评估影响**
--- | --- | ---
Hyper-v 虚拟机监控程序虚拟处理器 | % Guest 运行时间 | 建议的 VM 大小/成本
Hyper-v 动态内存 VM | 当前压力 (%)<br/> 来宾可见物理内存 (MB) | 建议的 VM 大小/成本
Hyper-v 虚拟存储设备 | 每秒读取的字节数 | 磁盘大小、存储成本、VM 大小的计算
Hyper-v 虚拟存储设备 | 每秒写入的字节数 | 磁盘大小、存储成本、VM 大小的计算
Hyper-v 虚拟网络适配器 | 每秒接收的字节数 | VM 大小的计算
Hyper-v 虚拟网络适配器 | 发送的字节数/秒 | VM 大小的计算

- "CPU 使用率" 是所有附加到 VM 的虚拟处理器的所有使用量之和。
- 内存使用率为 (当前压力 * 来宾可见物理内存)/100。
- 磁盘和网络利用率值从列出的 Hyper-v 性能计数器中收集。

## <a name="collected-metadata-hyper-v"></a>收集的元数据-Hyper-v

下面是设备收集并发送到 Azure 的 Hyper-v VM 元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
**计算机详细信息** | 
BIOS 的序列号 _ Msvm_BIOSElement | BIOSSerialNumber
VM 类型 (第1代或第2代) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 显示名称 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | VirtualQuantity
内存 (字节) | Msvm_MemorySettingData | VirtualQuantity
VM 可使用的最大内存 | Msvm_MemorySettingData | 限制
已启用动态内存 | Msvm_MemorySettingData | DynamicMemoryEnabled
操作系统名称/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名称数据
VM 电源状态 | Msvm_ComputerSystem | EnabledState
**每磁盘详细信息** | 
磁盘标识符 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虚拟硬盘类型 | Msvm_VirtualHardDiskSettingData | type
虚拟硬盘大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
虚拟硬盘父项 | Msvm_VirtualHardDiskSettingData | ParentPath
**每 NIC 详细信息** | 
IP 地址 (合成 Nic) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 已启用 (合成 Nic) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (合成 Nic) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 地址 (合成 Nic) | Msvm_SyntheticEthernetPortSettingData | 地址
NIC ID (旧 Nic) | MsvmEmulatedEthernetPortSetting 数据 | InstanceID
NIC MAC ID (旧 Nic) | MsvmEmulatedEthernetPortSetting 数据 | 地址




## <a name="discovery-and-collection-process"></a>发现和收集过程

设备使用以下过程与 vCenter 服务器和 Hyper-v 主机/群集通信。


1. **开始发现**:
    - 当你在 Hyper-v 设备上启动发现时, 它会与 WinRM 端口 5985 (HTTP) 和 5986 (HTTPS) 上的 Hyper-v 主机通信。
    - 在 VMware 设备上启动发现时, 默认情况下, 它会与 TCP 端口443上的 vCenter 服务器通信。 如果 vCenter 服务器侦听其他端口, 则可以在设备 web 应用中进行配置。
2. **收集元数据和性能数据**:
    - 设备使用通用信息模型 (CIM) 会话来收集端口5985和5986上的 Hyper-v 主机上的 Hyper-v VM 数据。
    - 默认情况下, 设备与端口443通信, 以便从 vCenter Server 收集 VMware VM 数据。
3. **发送数据**:设备会将收集的数据发送到 Azure Migrate 服务器评估, 并通过 SSL 端口 443 Azure Migrate 服务器迁移。
    - 对于性能数据, 设备会收集实时利用率数据。
        - 对于 VMware, 每隔20秒收集一次性能数据, 对于每个性能指标, 将每30秒收集一次。
        - 收集的数据将汇总起来, 以创建一个数据点10分钟。
        - 峰值利用率值是从所有20/30 秒数据点中选择的, 并发送到 Azure 进行评估计算。
        - 根据在评估属性 (第 50/90/95/99%) 中指定的百分位值, 按升序对十分钟点进行排序, 并使用适当的百分位值计算评估
    - 对于服务器迁移, 设备会开始收集 VM 数据, 并将其复制到 Azure。
4. **评估和迁移**:你现在可以使用 Azure Migrate Server 评估为设备收集的元数据创建评估。 此外, 还可以使用 Azure Migrate Server 迁移开始迁移 VMware Vm, 以安排无代理 VM 复制。


![体系结构](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>设备升级

在设备上运行的 Azure Migrate 代理更新时, 会升级设备。

- 这会自动发生, 因为默认情况下在设备上启用了自动更新。
- 您可以更改此默认设置以手动更新代理。
- 若要禁用自动更新, 请在注册表编辑器 > HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance, 并将注册表项 "自动更新" 设置为 0 (DWORD)。
 
### <a name="set-agent-updates-to-manual"></a>将代理更新设置为手动

对于手动更新, 请确保在设备上使用每个过时代理的 "**更新**" 按钮同时更新设备上的所有代理。 你可以随时将更新设置切换回自动更新。

## <a name="next-steps"></a>后续步骤

[了解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)为 VMware 设置设备。
[了解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)为 hyper-v 设置设备。

