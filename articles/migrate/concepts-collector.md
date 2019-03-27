---
title: 关于 Azure Migrate 中的收集器设备 | Microsoft Docs
description: 介绍 Azure Migrate 中的收集器设备。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 224511b9748c540f2cd48a3d8393a9c74f76ce32
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498411"
---
# <a name="about-the-collector-appliance"></a>关于收集器设备

 本文提供了有关 Azure Migrate 收集器的信息。

Azure Migrate 收集器是一种轻量级设备，可用于在迁移到 Azure 之前发现本地 vCenter 环境，以便使用 [Azure Migrate](migrate-overview.md) 服务进行评估。  

## <a name="discovery-method"></a>发现方法

在以前，收集器设备有两个选项，一次性发现和连续发现。 一次性发现模型现在已被弃用，因为它依赖于用于性能数据收集的 vCenter Server 统计信息设置（需要统计信息设置设置为级别 3），并且收集的平均计数器（而不是峰值）会导致大小不足。 连续发现模型可确保精细数据收集，并可收集峰值计数器，从而实现准确的大小调整。 其工作原理如下所示：

收集器设备将持续连接到 Azure Migrate 项目，并不断收集 VM 的性能数据。

- 收集器持续分析本地环境，每 20 秒收集一次实时利用率数据。
- 设备汇总 20 秒示例，每隔 15 分钟创建单个数据点。
- 若要创建数据点，设备从 20 秒示例中选择峰值并将其发送到 Azure。
- 此模型不依赖于 vCenter Server 的统计信息设置来收集性能数据。
- 你可以随时从收集器中停止连续分析。

**快速评估：** 使用持续发现设备，一旦发现完成（花费几个小时，具体取决于 VM 数量），就可以立即创建评估。 由于性能数据收集在启动发现时开始，如果希望进行快速评估，则应当将评估中的大小调整条件选择为“按本地”。 对于基于性能的评估，建议在启动发现后等待至少一天，以便获得可靠的大小建议。

设备仅连续收集性能数据，它不会检测本地环境中的任何配置更改（即 VM 添加、删除、磁盘添加等）。 如果本地环境中存在配置更改，可以执行以下操作以在门户中反映更改：

- 添加项（VM、磁盘、核心等）：若要在 Azure 门户中反映这些更改，可以从设备停止发现，然后重启发现。 这可确保在 Azure Migrate 项目中更新更改。

- 删除 VM：由于设备的设计方式，即使停止并启动发现，也不会反映出 VM 已删除这一更改。 这是因为后续发现的数据会追加到较旧的发现后，而不是进行覆盖。 在这种情况下，可以通过从组中删除 VM 并重新计算评估来直接忽略门户中的 VM。

> [!NOTE]
> 一次性发现设备现在已弃用，因为此方法依赖于 vCenter Server 针对性能数据点可用性的统计信息设置并且收集平均性能计数器，这导致用于迁移到 Azure 的 VM 大小不足。

## <a name="deploying-the-collector"></a>部署收集器

使用 OVF 模板部署收集器设备：

- 从 Azure 门户中的 Azure Migrate 项目下载 OVF 模板。 将下载的文件导入到 vCenter Server，以便设置收集器设备 VM。
- 从 OVF 中，VMware 设置一个具有 8 个核心、16 GB RAM 和 80 GB 磁盘的 VM。 操作系统是 Windows Server 2016（64 位）。
- 运行收集器时，将运行一些先决条件检查以确保收集器可以连接到 Azure Migrate。

- [详细了解](tutorial-assessment-vmware.md#create-the-collector-vm)如何创建收集器。


## <a name="collector-prerequisites"></a>收集器先决条件

收集器必须通过一些先决条件检查，目的是确保它能够通过 Internet 连接到 Azure Migrate 服务并上传发现的数据。

- **验证 Azure 云**：收集器需要知道计划迁移到的 Azure 云。
    - 如果打算迁移到 Azure 政府云，请选择 Azure 政府。
    - 如果打算迁移到 Azure 商业版云，请选择 Azure 全局。
    - 根据此处指定的云，设备将向各自的终结点发送已发现的元数据。
- **检查 Internet 连接**：收集器可直接或通过代理连接到 Internet。
    - 先决条件检查验证是否能连接到[必需和可选 URL](#urls-for-connectivity)。
    - 如果可以直接连接到 Internet，则无需特定操作，否则要确保收集器可以访问所需的 URL。
    - 如果要通过代理进行连接，请注意下面的要求。
- **验证时间同步**：收集器应与 Internet 时间服务器同步，以确保向服务发出的请求经过身份验证。
    - portal.azure.com URL 应该能够从收集器访问，以便验证时间。
    - 如果计算机不同步，则需更改收集器 VM 上的时钟时间，使之与当前时间匹配。 为此，打开 VM 上的管理员提示符，运行 w32tm /tz 检查时区。 运行 w32tm /resync 来同步时间。
- **检查收集器服务是否正在运行**：Azure Migrate 收集器服务应该正在收集器 VM 上运行。
    - 此服务在计算机启动时自动启动。
    - 如果服务未运行，则从控制面板中启动它。
    - 收集器服务连接到 vCenter Server，收集 VM 元数据和性能数据，并将其发送到 Azure Migrate 服务。
- **检查 VMware PowerCLI 6.5 是否已安装**：VMware PowerCLI 6.5 PowerShell 模块必须安装在收集器 VM 上，以便它可以与 vCenter Server 进行通信。
    - 如果收集器可访问模块安装所需的 URL，则会在收集器部署过程中自动安装模块。
    - 如果收集器不能在部署过程中安装模块，则必须手动安装。
- **检查 vCenter Server 连接**：收集器必须能够连接到 vCenter Server 和查询 VM、其元数据和性能计数器。 [验证先决条件](#connect-to-vcenter-server)以进行连接。


### <a name="connect-to-the-internet-via-a-proxy"></a>通过代理连接到 Internet

- 如果代理服务器需要身份验证，则可在连接控制器时指定用户名和密码。
- 代理服务器的 IP 地址/FQDN 应指定为*http:\//IPaddress*或*http:\//FQDN*。
- 仅支持 HTTP 代理。 收集器不支持基于 HTTPS 的代理服务器。
- 如果代理服务器是截取代理，则必须将代理证书导入到收集器 VM。
  1. 在收集器 VM 中，转到“开始菜单” > “管理计算机证书”。
  2. 在“证书”工具中，在“证书 - 本地计算机”下，找到“受信任的发布者”“证书” > 。

      ![“证书”工具](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. 将代理证书复制到收集器 VM。 你可能需要联系网络管理员获取代理证书。
  4. 双击证书打开证书，并单击“安装证书”。
  5. 在“证书导入向导”>“存储位置”中，选择“本地计算机”。

     ![证书存储位置](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. 选择“将所有证书放入下列存储” > “浏览” > “受信任的发布者”。 单击“完成”导入证书。

     ![证书存储](./media/concepts-intercepting-proxy/certificate-store.png)

  7. 检查证书是否按预期导入，并检查 Internet 连接性先决条件检查是否按预期工作。


### <a name="urls-for-connectivity"></a>用于连接的 URL

验证连接检查的方法是连接到一系列 URL。

**URL** | **详细信息**  | **先决条件检查**
--- | --- | ---
*.portal.azure.com | 适用于 Azure 全局。 检查与 Azure 服务和时间同步的连接。 | 必须能够访问 URL。<br/><br/> 如果没有连接，先决条件检查会失败。
*.portal.azure.us | 仅适用于 Azure 政府。 检查与 Azure 服务和时间同步的连接。 | 必须能够访问 URL。<br/><br/> 如果没有连接，先决条件检查会失败。
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| 用于下载 PowerShell vCenter PowerCLI 模块。 | Url 的访问权限是必需的。<br/><br/> 先决条件检查不会失败。<br/><br/> 收集器 VM 上的自动模块安装将失败。 你将需要在已建立 internet 连接的计算机手动安装该模块，然后将这些模块复制到设备。 [了解详细信息，请转到步骤 #4 在此故障排除指南](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception)。


### <a name="install-vmware-powercli-module-manually"></a>手动安装 VMware PowerCLI 模块

1. 使用[这些步骤](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)安装模块。 这些步骤介绍了联机和脱机安装。
2. 如果收集器 VM 处于脱机状态，并且模块安装在具有 Internet 访问的不同计算机上，则需要将该计算机中的 VMware.* 文件复制到收集器 VM。
3. 安装完成后，可以重新启动先决条件检查，以确认 PowerCLI 已安装。

### <a name="connect-to-vcenter-server"></a>连接到 vCenter Server

收集器连接到 vCenter Server，并查询 VM 元数据和性能计数器。 以下是执行此连接所需的组件。

- 仅支持 vCenter Server 5.5、6.0、6.5 和 6.7 版。
- 需要使用具有下面概述的权限的只读帐户进行发现。 只能访问该帐户可访问的数据中心以进行发现。
- 默认情况下，连接到带有 FQDN 或 IP 地址的 vCenter Server。 如果 vCenter Server 在不同端口上侦听，则使用 IPAddress:Port_Number 或 FQDN:Port_Number 的形式连接到该端口。
- 收集器应该具有网络视线，可以看到 vCenter Server。

#### <a name="account-permissions"></a>帐户权限

**帐户** | **权限**
--- | ---
至少一个只读用户帐户 | 数据中心对象 –> 传播到子对象、角色=只读   


## <a name="collector-communications"></a>收集器通信

下面的图表汇总了收集器的通信方式。

![收集器通信关系图](./media/tutorial-assessment-vmware/portdiagram.PNG)


**收集器通信对象** | 端口 | **详细信息**
--- | --- | ---
Azure Migrate 服务 | TCP 443 | 收集器通过 SSL 443 与 Azure Migrate 服务进行通信。
vCenter Server | TCP 443 | 收集器必须能够与 vCenter Server 进行通信。<br/><br/> 默认情况下，它通过 443 连接到 vCenter。<br/><br/> 如果 vCenter Server 在另一端口上侦听，则该端口应作为收集器上的传出端口提供。
RDP | TCP 3389 |

## <a name="collected-metadata"></a>收集的元数据

> [!NOTE]
> 元数据收集器设备用于帮助您正确调整其大小在应用程序迁移到 Azure，Azure Migrate 发现执行 Azure 适用性分析、 应用程序依赖项分析和规划成本。 Microsoft 不使用此数据相对于任何许可证符合性审核。

收集器设备发现每个 VM 的以下配置元数据。 VM 的配置数据在发现启动后一小时即可使用。

- VM 显示名称（在 vCenter Server 上）
- VM 的清单路径（vCenter Server 上的主机/文件夹）
- IP 地址
- MAC 地址
- 操作系统
- 核心数、磁盘数、NIC 数
- 内存大小、磁盘大小
- VM、磁盘和网络的性能计数器。

### <a name="performance-counters"></a>性能计数器

 收集器设备每隔 20 秒从 ESXi 主机为每个 VM 收集以下性能计数器。 这些计数器是 vCenter 计数器，虽然术语表示其为平均值，但 20 秒示例是实时计数器。 启动发现两个小时后，门户开始提供 VM 的性能数据。 在创建基于性能的评估之前，强烈建议等待至少一天，以获得精确的大小调整建议。 如果需要“即时满足条件”，可选择以“作为本地”为大小调整条件创建评估，如此则不会将性能数据纳入调整范围。

**计数器** |  **对评估的影响**
--- | ---
cpu.usage.average | 建议的 VM 大小和成本  
mem.usage.average | 建议的 VM 大小和成本  
virtualDisk.read.average | 计算磁盘大小、存储成本和 VM 大小
virtualDisk.write.average | 计算磁盘大小、存储成本和 VM 大小
virtualDisk.numberReadAveraged.average | 计算磁盘大小、存储成本和 VM 大小
virtualDisk.numberWriteAveraged.average | 计算磁盘大小、存储成本和 VM 大小
net.received.average | 计算 VM 大小                          
net.transmitted.average | 计算 VM 大小     

下文提供 Azure Migrate 收集的 VMware 计数器的完整列表：

**类别** |  **元数据** | **vCenter 数据点**
--- | --- | ---
虚拟机详细信息 | VM ID | vm.Config.InstanceUuid
虚拟机详细信息 | VM 名称 | vm.Config.Name
虚拟机详细信息 | vCenter Server ID | VMwareClient.InstanceUuid
虚拟机详细信息 |  VM 说明 |  vm.Summary.Config.Annotation
虚拟机详细信息 | 许可证产品名称 | vm.Client.ServiceContent.About.LicenseProductName
虚拟机详细信息 | 操作系统类型 | vm.Summary.Config.GuestFullName
虚拟机详细信息 | 操作系统版本 | vm.Summary.Config.GuestFullName
虚拟机详细信息 | 启动类型 | vm.Config.Firmware
虚拟机详细信息 | 内核数 | vm.Config.Hardware.NumCPU
虚拟机详细信息 | 内存 (MB) | vm.Config.Hardware.MemoryMB
虚拟机详细信息 | 磁盘数目 | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk).count
虚拟机详细信息 | 磁盘大小列表 | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk)
虚拟机详细信息 | 网络适配器列表 | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualEthernetCard)
虚拟机详细信息 | CPU 使用率 | cpu.usage.average
虚拟机详细信息 | 内存利用率 | mem.usage.average
磁盘详细信息（每个磁盘） | 磁盘密钥值 | disk.Key
磁盘详细信息（每个磁盘） | 磁盘单元数 | disk.UnitNumber
磁盘详细信息（每个磁盘） | 磁盘控制器密钥值 | disk.ControllerKey.Value
磁盘详细信息（每个磁盘） | 预配量 (GB) | virtualDisk.DeviceInfo.Summary
磁盘详细信息（每个磁盘） | 磁盘名称 | 使用 disk.UnitNumber, disk.Key 和 disk.ControllerKey.Value 生成此值
磁盘详细信息（每个磁盘） | 每秒读取操作数 | virtualDisk.numberReadAveraged.average
磁盘详细信息（每个磁盘） | 每秒写入操作数 | virtualDisk.numberWriteAveraged.average
磁盘详细信息（每个磁盘） | 读取吞吐量（兆字节/秒） | virtualDisk.read.average
磁盘详细信息（每个磁盘） | 写入吞吐量（兆字节/秒） | virtualDisk.write.average
网络适配器详细信息（每个 NIC） | 网络适配器名称 | nic.Key
网络适配器详细信息（每个 NIC） | MAC 地址 | ((VirtualEthernetCard)nic).MacAddress
网络适配器详细信息（每个 NIC） | IPv4 地址 | vm.Guest.Net
网络适配器详细信息（每个 NIC） | IPv6 地址 | vm.Guest.Net
网络适配器详细信息（每个 NIC） | 读取吞吐量（兆字节/秒） | net.received.average
网络适配器详细信息（每个 NIC） | 写入吞吐量（兆字节/秒） | net.transmitted.average
库存路径详细信息 | 名称 | container.GetType().Name
库存路径详细信息 | 子对象类型 | container.ChildType
库存路径详细信息 | 引用详细信息 | container.MoRef
库存路径详细信息 | 完整库存路径 | container.Name + 完整路径
库存路径详细信息 | 父级详细信息 | Container.Parent
库存路径详细信息 | 每个 VM 的文件夹详细信息 | ((Folder)container).ChildEntity.Type
库存路径详细信息 | 每个 VM 文件夹的数据中心详细信息 | ((Datacenter)container).VmFolder
库存路径详细信息 | 每个主机文件夹的数据中心详细信息 | ((Datacenter)container).HostFolder
库存路径详细信息 | 每个主机的群集详细信息 | ((ClusterComputeResource)container).Host)
库存路径详细信息 | 每个 VM 的主机详细信息 | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>保护收集器设备

建议执行以下步骤来保护收集器设备：

- 请勿与未授权方共享管理员密码或将其错放。
- 未使用时请关闭设备。
- 将设备置于安全网络中。
- 在完成迁移后，删除设备实例。
- 此外，完成迁移后，还要同时删除磁盘备份文件 (VMDK)，因为磁盘上可能缓存了 vCenter 凭据。

## <a name="os-license-in-the-collector-vm"></a>收集器 VM 中的 OS 许可证

收集器附带有效期为 180 天的 Windows Server 2012 R2 评估许可证。 如果收集器 VM 的评估期到期，则建议下载新 OVA 并创建一个新的设备。

## <a name="updating-the-os-of-the-collector-vm"></a>更新收集器 VM 的 OS

尽管收集器设备具有 180 天的评估许可证，但你需要不断更新设备上的 OS 以避免设备的自动关闭。

- 如果收集器在 60 天内未更新，则启动自动关闭计算机功能。
- 如果正在运行发现，即使已超过 60 天未进行更新，也不会关闭计算机。 发现作业完成后，将关闭计算机。
- 如果使用收集器超过 60 天，则建议通过运行 Windows 更新来始终保持计算机处于最新状态。

## <a name="upgrading-the-collector-appliance-version"></a>收集器设备版本升级

无需再次下载 OVA，即可将收集器升级到最新版本。

1. 下载[最新列出的升级包](concepts-collector-upgrade.md)
2. 若要确保下载的修补程序安全，请打开管理员命令窗口并运行以下命令生成 ZIP 文件的哈希。 生成的哈希应与针对特定版本提到的哈希相匹配：

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    （用法示例 C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256）
3. 将 zip 文件复制到 Azure Migrate 收集器虚拟机（收集器设备）。
4. 右键单击 zip 文件并选择“全部提取”。
5. 右键单击 Setup.ps1 并选择“使用 PowerShell 运行”，然后按照屏幕上的说明来安装更新。

## <a name="discovery-process"></a>发现过程

设备设置完成后，可以运行发现。 工作方式如下：

- 按范围运行发现。 将发现指定的 vCenter 清单路径中的所有 VM。
    - 一次设置一个范围。
    - 范围可以包含 1500 个 VM 或更少。
    - 范围可以是数据中心、文件夹，或 ESXi 主机。
- 连接到 vCenter Server 后，通过指定集合的迁移项目来进行连接。
- 发现 VM，并且其元数据和性能数据发送到 Azure。 这些操作是集合作业的一部分。
    - 收集器设备被赋予一个特定的收集器 ID，该 ID 对于发现中的给定计算机是持续存在的。
    - 正在运行的集合作业会被指定一个特定的会话 ID。 每个集合作业的会话 ID 都不同，并可用于故障排除。

## <a name="next-steps"></a>后续步骤

[为本地 VMware VM 设置评估](tutorial-assessment-vmware.md)
