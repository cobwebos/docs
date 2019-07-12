---
title: Azure Migrate 设备体系结构 |Microsoft Docs
description: 概述 Azure Migrate 设备
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811448"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 设备

本文介绍 Azure Migrate 设备。 使用 Azure Migrate 评估和迁移工具用于发现、 评估以及将应用程序、 基础结构和工作负荷迁移到 Microsoft Azure 时，将设备的部署。 

[Azure Migrate](migrate-services-overview.md)提供中心，以跟踪发现、 评估和迁移的本地应用和工作负荷，及私钥/公钥云 Vm 到 Azure。 中心提供了评估和迁移，以及第三方独立软件供应商 (ISV) 产品/服务的 Azure 迁移工具。



## <a name="appliance-overview"></a>设备概述

Azure Migrate 设备类型和使用情况如下所示。

**部署为** | 用途  | **详细信息**
--- | --- |  ---
VMware VM | 使用 Azure Migrate 评估工具的 VMware VM 评估。<br/><br/> 使用 Azure 迁移 Server 迁移工具的 VMware VM 无代理迁移 | 下载 OVA 模板并导入 vCenter 服务器来创建设备 VM。
Hyper-V VM | 使用 Azure Migrate 评估工具的 HYPER-V VM 评估。 | 下载 zip 格式的 VHD 并导入到 HYPER-V 来创建设备 VM。

## <a name="appliance-access"></a>设备访问

配置设备后，可以通过 TCP 端口 3389 远程访问设备的 VM。 可以还远程访问的设备，使用 URL 44368 端口上的 web 管理应用： ``` https://<appliance-ip-or-name>:44368 ```。

## <a name="appliance-license"></a>设备许可证
该设备附带了 Windows Server 2016 评估版许可证，有效期为 180 天。 如果在评估期即将到期，我们建议你下载并部署新的设备，或激活设备 VM 的操作系统许可证。

## <a name="appliance-agents"></a>设备代理
设备已安装这些代理。

**代理** | **详细信息**
--- | ---
发现代理 | 从本地 Vm 收集配置数据。
评估代理 | 配置文件的本地环境以收集 VM 的性能数据。
迁移适配器 | 将安排 VM 复制并协调虚拟机和 Azure 之间的通信。
迁移网关 | 将 VM 数据复制到 Azure。


## <a name="appliance-deployment-requirements"></a>设备部署要求

- [查看](migrate-support-matrix-vmware.md#assessment-appliance-requirements)VMware 设备和设备需要访问的 Url 的部署要求。
- [查看](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)HYPER-V 设备和设备需要访问的 Url 的部署要求。


## <a name="collected-performance-data-vmware"></a>收集的性能数据-VMware

下面是设备收集并发送到 Azure 的 VMware VM 性能数据。

**数据** | **计数器** | **评估影响**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建议的 VM 大小/成本
内存利用率 | mem.usage.average | 建议的 VM 大小/成本
磁盘读取吞吐量 (MB / 秒) | virtualDisk.read.average | 磁盘大小、 存储成本、 VM 大小的计算
磁盘写入吞吐量 (MB / 秒) | virtualDisk.write.average | 磁盘大小、 存储成本、 VM 大小的计算
磁盘每秒读取操作数 | virtualDisk.numberReadAveraged.average | 磁盘大小、 存储成本、 VM 大小的计算
磁盘每秒写入操作数 | virtualDisk.numberWriteAveraged.average  | 磁盘大小、 存储成本、 VM 大小的计算
NIC 的读取吞吐量 (MB / 秒) | net.received.average | 计算 VM 大小
NIC 写入吞吐量 (MB / 秒) | net.transmitted.average  |计算 VM 大小


## <a name="collected-metadata-vmware"></a>收集的元数据-VMware

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
**每个磁盘详细信息** | 
磁盘密钥值 | disk.Key
Dikunit 数 | disk.UnitNumber
磁盘控制器密钥值 | disk.ControllerKey.Value
预配量 (GB) | virtualDisk.DeviceInfo.Summary
磁盘名称 | 使用磁盘生成的值。UnitNumber、 磁盘。键，磁盘。ControllerKey.VAlue
每秒读取操作数 | virtualDisk.numberReadAveraged.average
每秒写入操作数 | virtualDisk.numberWriteAveraged.average
读取吞吐量 (MB / 秒) | virtualDisk.read.average
写入吞吐量 (MB / 秒) | virtualDisk.write.average
**每个 NIC 的详细信息** | 
网络适配器名称 | nic.Key
MAC 地址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 地址 | vm.Guest.Net
IPv6 地址 | vm.Guest.Net
读取吞吐量 (MB / 秒) | net.received.average
写入吞吐量 (MB / 秒) | net.transmitted.average
**清单路径详细信息** | 
名称 | container.GetType().Name
子对象类型 | container.ChildType
引用详细信息 | container.MoRef
父级详细信息 | Container.Parent
每个 VM 的文件夹详细信息 | ((Folder)container).ChildEntity.Type
每个 VM 的数据中心详细信息 | ((Datacenter)container).VmFolder
数据中心的每个主机文件夹的详细信息 | ((Datacenter)container).HostFolder
每个主机群集详细信息 | ((ClusterComputeResource)container).Host
每个 VM 的主机详细信息 | （(HostSystem) 容器）。VM



## <a name="collected-performance-data-hyper-v"></a>收集的性能数据的 Hyper-v

下面是设备收集并发送到 Azure 的 VMware VM 性能数据。

**性能计数器类** | **计数器** | **评估影响**
--- | --- | ---
HYPER-V 虚拟机监控程序虚拟处理器 | 来宾运行的时间百分比 | 建议的 VM 大小/成本
HYPER-V 动态内存 VM | 当前压力 （%）<br/> Guest Visible Physical Memory (MB) | 建议的 VM 大小/成本
Hyper V 虚拟存储设备 | 读取字节数/秒 | 磁盘大小、 存储成本、 VM 大小的计算
Hyper V 虚拟存储设备 | 写入字节数/秒 | 磁盘大小、 存储成本、 VM 大小的计算
HYPER-V 虚拟网络适配器 | 每秒接收字节 | 计算 VM 大小
HYPER-V 虚拟网络适配器 | 每秒发送的字节 | 计算 VM 大小

- CPU 使用率是附加到 VM 的所有虚拟处理器的所有使用情况的总和。
- 内存利用率 (当前压力 * Guest Visible Physical Memory) / 100。
- 从列出的 HYPER-V 性能计数器收集磁盘和网络使用率值。

## <a name="collected-metadata-hyper-v"></a>收集元数据-的 Hyper-v

下面是设备收集并发送到 Azure 的 HYPER-V VM 元数据的完整列表。

**数据** | **WMI 类** | **WMI 类属性**
--- | --- | ---
**计算机详细信息** | 
BIOS _ Msvm_BIOSElement 的序列号 | BIOSSerialNumber
VM 类型 (Gen 1 或 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 显示名称 | Msvm_VirtualSystemSettingData | ElementName
虚拟机版本 | Msvm_ProcessorSettingData | VirtualQuantity
内存 （字节） | Msvm_MemorySettingData | VirtualQuantity
可供 VM 的最大内存 | Msvm_MemorySettingData | 限制
启用动态内存 | Msvm_MemorySettingData | DynamicMemoryEnabled
操作系统名称/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsciExchangeItems 名称数据
VM 电源状态 | Msvm_ComputerSystem | EnabledState
**每个磁盘详细信息** | 
磁盘标识符 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虚拟硬盘类型 | Msvm_VirtualHardDiskSettingData | 类型
虚拟硬盘大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
父虚拟硬盘 | Msvm_VirtualHardDiskSettingData | ParentPath
**每个 NIC 的详细信息** | 
IP 地址 (合成 Nic) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
已启用 DHCP (合成 Nic) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (合成 Nic) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 地址 (合成 Nic) | Msvm_SyntheticEthernetPortSettingData | 地址
NIC ID (旧 Nic) | MsvmEmulatedEthernetPortSetting 数据 | InstanceID
NIC MAC ID (旧 Nic) | MsvmEmulatedEthernetPortSetting 数据 | 地址




## <a name="discovery-and-collection-process"></a>发现和收集进程

与 vCenter 服务器和 HYPER-V 主机/群集使用以下过程，该设备进行通信。


1. **启动发现**:
    - 在上的 HYPER-V 设备启动发现，它与 WinRM 端口 5985 (HTTP) 和 5986(https (HTTPS) 上的 HYPER-V 主机。
    - 在 VMware 设备上启动发现，它与默认情况下的 TCP 端口 443 上的 vCenter 服务器通信。 如果 vCenter 服务器在不同端口上侦听，可以在设备的 web 应用中配置它。
2. **收集元数据和性能数据**:
    - 设备使用的通用信息模型 (CIM) 会话端口 5985 和 5986 上的 HYPER-V 主机收集的 HYPER-V VM 数据。
    - 在设备与默认值，以从 vCenter Server 收集的 VMware VM 数据通过端口 443 进行通信。
3. **将数据发送**:设备通过 SSL 端口 443 将收集的数据发送到 Azure Migrate Server 评估和 Azure 迁移服务器迁移。
    - 性能数据的设备可收集实时利用率数据。
        - 对于 VMware，每隔 20 秒和 hyper-v，对于每个性能指标每隔 30 秒收集性能数据。
        - 收集的数据汇总为十分钟创建单个数据点。
        - 峰值使用率值选择从所有 20 个/30 第二个数据点，以及评估计算发送到 Azure。
        - 根据指定在评估属性 (50/第 90/第 95/第 99) 中的百分位值，10 だ 牧点按升序排序，并相应的百分位值用于计算评估
    - 服务器迁移设备开始收集 VM 数据，并将其复制到 Azure。
4. **评估和迁移**:现在可以从使用 Azure Migrate Server 评估设备收集的元数据创建评估。 此外，还可以启动将使用 Azure 迁移服务器迁移来协调无代理 VM 复制的 VMware Vm 迁移。


![体系结构](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>设备升级

设备是进行升级，因为会更新在设备上运行 Azure Migrate 代理。

- 此操作自动，因为默认情况下在设备上启用自动更新。
- 可以更改此默认设置来手动更新代理。
- 若要禁用自动更新，HKLM\SOFTWAREMicrosoft\Azure 中设置注册表项设备自动更新。

### <a name="set-agent-updates-to-manual"></a>将代理更新设置为手动

对于手动更新，请确保你在同一时间更新在设备上的所有代理使用**都更新**为每个过时代理在设备上的按钮。 您可以切换回在任何时间自动更新的更新设置。

## <a name="next-steps"></a>后续步骤

[了解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)若要设置 VMware 设备。
[了解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)以设置 HYPER-V 设备。

