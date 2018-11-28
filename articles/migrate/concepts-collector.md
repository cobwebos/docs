---
title: 关于 Azure 迁移中的收集器设备 | Microsoft Docs
description: 介绍 Azure 迁移中的收集器设备。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 81e6731068db84f02073f02c49bea9a8fb7c7c70
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241185"
---
# <a name="about-the-collector-appliance"></a>关于收集器设备

 本文提供了有关 Azure 迁移收集器的信息。

Azure 迁移收集器是一种轻量级设备，可用于在迁移到 Azure 之前发现本地 vCenter 环境，以便使用 [Azure 迁移](migrate-overview.md) 服务进行评估。  

## <a name="discovery-methods"></a>发现方法

收集器设备有两个选项：一次性发现或持续发现。

### <a name="one-time-discovery"></a>一次性发现

收集器设备与 vCenter Server 进行一次性通信，以收集有关虚拟机的元数据。 使用此方法时：

- 设备未持续连接到 Azure 迁移项目。
- 发现完成后，本地环境的更改不会反映在 Azure迁移中。 要反映任何更改，您需要重新发现同一项目中的相同环境。
- 当收集虚拟机的性能数据时，设备依赖于在 vCenter Server 中存储的历史性能数据。 它收集过去一个月内性能历史记录。
- 若要收集历史性能数据，需将 vCenter Server 中的统计信息设置设置为第三级。 在级别设置为三级后，需等待至少一天 vCenter 才能收集性能计数器。 因此，建议至少在一天后运行发现。 如果要根据 1 周或 1 个月的性能数据来评估环境，则需等待相应的时间。
- 在此发现方法中，Azure 迁移针对每个可能会导致大小不足的指标（而不是峰值计数器）收集平均计数器。 建议使用持续发现选项以获取更准确地大小调整结果。

### <a name="continuous-discovery"></a>持续发现

收集器设备将持续连接到 Azure 迁移项目，并不断收集虚拟机的性能数据。

- 收集器持续分析本地环境，每 20 秒收集一次实时利用率数据。
- 设备汇总 20 秒示例，每隔 15 分钟创建单个数据点。
- 若要创建数据点，设备从 20 秒示例中选择峰值并将其发送到 Azure。
- 此模型不依赖于 vCenter Server 的统计信息设置来收集性能数据。
- 你可以随时从收集器中停止连续分析。

请注意，设备仅连续收集性能数据，它不会检测本地环境中的任何配置更改（即虚拟机添加、删除、磁盘添加等）。 如果本地环境中存在配置更改，可以执行以下操作以在门户中反映更改：

- 添加项（虚拟机、磁盘、内核等）：若要在 Azure 门户中反映这些更改，可以从设备停止发现，然后重新启动设备。 这可确保在 Azure 迁移项目中更新更改。

- 删除虚拟机：由于设备的设计方式，即使停止并启动发现，也不会反映虚拟机的删除。 这是因为后续发现的数据会追加到较旧的发现后，而不是进行覆盖。 在这种情况下，可以通过从组中删除虚拟机并重新计算评估来直接忽略门户中的虚拟机。

> [!NOTE]
> 连续性发现功能为预览版。 建议使用此方法，因为此方法收集细粒度的性能数据并实现精确的大小调整。

## <a name="deploying-the-collector"></a>部署收集器

使用 OVF 模板部署收集器设备：

- 从 Azure 门户中的 Azure 迁移项目下载 OVF 模板。 将下载的文件导入到 vCenter Server，以便设置收集器设备虚拟机。
- 从 OVF 中，VMware 设置一个具有 4 个内核、8 GB RAM 和 80 GB 磁盘的虚拟机。 操作系统是 Windows Server 2012 R2（64 位）。
- 运行收集器时，将运行一些先决条件检查以确保收集器可以连接到 Azure 迁移服务。

- [详细了解](tutorial-assessment-vmware.md#create-the-collector-vm)如何创建收集器。


## <a name="collector-prerequisites"></a>收集器先决条件

收集器必须通过一些先决条件检查，目的是确保它能够通过互联网连接到 Azure 迁移服务并上传发现的数据。

- 检查 Internet 连接：收集器可直接或通过代理连接到 Internet。
    - 先决条件检查验证是否能连接到[必需和可选 URL](#connect-to-urls)。
    - 如果可以直接连接到 Internet，则无需特定操作，否则要确保收集器可以访问所需的 URL。
    - 如果要通过代理进行连接，请注意[下面的要求](#connect-via-a-proxy)。
- 验证时间同步：收集器应与 Internet 时间服务器同步，以确保向服务发出的请求经过身份验证。
    - portal.azure.com URL 应该能够从收集器访问，以便验证时间。
    - 如果计算机不同步，则需更改收集器虚拟机上的时钟时间，使之与当前时间匹配。 为此，打开虚拟机上的管理员提示符，运行 w32tm /tz 检查时区。 运行 w32tm /resync 来同步时间。
- 检查收集器服务运行情况：Azure 迁移收集器服务应该正在收集器虚拟机上运行。
    - 此服务在计算机启动时自动启动。
    - 如果服务未运行，则从控制面板中启动它。
    - 收集器服务连接到 vCenter Server，收集虚拟机元数据和性能数据，并将其发送到 Azure 迁移服务。
- 检查安装的 VMware PowerCLI 6.5：VMware PowerCLI 6.5 PowerShell 模块必须安装在收集器虚拟机上，以便它可以与 vCenter Server 进行通信。
    - 如果收集器可访问模块安装所需的 URL，则会在收集器部署过程中自动安装模块。
    - 如果收集器不能在部署过程中安装模块，则必须[手动安装](#install-vwware-powercli-module-manually)。
- 检查到 vCenter Server 的连接：收集器必须能够连接到 vCenter Server 和查询虚拟机、其元数据和性能计数器。 [验证先决条件](#connect-to-vcenter-server)以进行连接。


### <a name="connect-to-the-internet-via-a-proxy"></a>通过代理连接到 Internet

- 如果代理服务器需要身份验证，则可在连接控制器时指定用户名和密码。
- 代理服务器的 IP 地址/FQDN 应该指定为 *http://IPaddress* 或 *http://FQDN*。
- 仅支持 HTTP 代理。 收集器不支持基于 HTTPS 的代理服务器。
- 如果代理服务器是拦截代理，则必须将代理证书导入到收集器虚拟机。
    1. 在收集器虚拟机中，转到“开始菜单” > “管理计算机证书”。
    2. 在“证书”工具中，在“证书 - 本地计算机”下，找到“受信任的发布者”“证书” > 。

        ![“证书”工具](./media/concepts-intercepting-proxy/certificates-tool.png)

    3. 将代理证书复制到收集器虚拟机。 你可能需要联系网络管理员获取代理证书。
    4. 双击证书打开证书，并单击“安装证书”。
    5. 在“证书导入向导”>“存储位置”中，选择“本地计算机”。

    ![证书存储位置](./media/concepts-intercepting-proxy/certificate-store-location.png)

    6. 选择“将所有证书放入下列存储” > “浏览” > “受信任的发布者”。 单击“完成”导入证书。

    ![证书存储](./media/concepts-intercepting-proxy/certificate-store.png)

    7. 检查证书是否按预期导入，并检查 Internet 连接性先决条件检查是否按预期工作。




### <a name="connect-to-urls"></a>连接到 URL

验证连接检查的方法是连接到一系列 URL。

**URL** | **详细信息**  | **先决条件检查**
--- | --- | ---
*.portal.azure.com | 检查与 Azure 服务和时间同步的连接。 | 必须能够访问 URL。<br/><br/> 如果没有连接，先决条件检查会失败。
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| 用于下载 PowerShell vCenter PowerCLI 模块。 | 可选的 URL 访问。<br/><br/> 先决条件检查不会失败。<br/><br/> 收集器 VM 上的自动模块安装将失败。 需要手动安装该模块。


### <a name="install-vmware-powercli-module-manually"></a>手动安装 VMware PowerCLI 模块

1. 使用[这些步骤](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)安装模块。 这些步骤介绍了联机和脱机安装。
2. 如果收集器虚拟机处于脱机状态，并且模块安装在具有 Internet 访问的不同计算机上，则需要将该计算机中的 VMware.* 文件复制到收集器虚拟机。
3. 安装完成后，可以重新启动先决条件检查，以确认 PowerCLI 已安装。

### <a name="connect-to-vcenter-server"></a>连接到 vCenter Server

收集器连接到 vCenter Server，并查询虚拟机元数据和性能计数器。 以下是执行此连接所需的组件。

- 仅支持 vCenter Server 5.5、6.0 和 6.5 版。
- 需要使用具有下面概述的权限的只读帐户进行发现。 只能访问该帐户可访问的数据中心以进行发现。
- 默认情况下，连接到带有 FQDN 或 IP 地址的 vCenter Server。 如果 vCenter Server 在不同端口上侦听，则使用 IPAddress:Port_Number 或 FQDN:Port_Number 的形式连接到该端口。
- 若要收集存储和网络的性能数据，vCenter Server 的统计信息设置必须设置为三级。
- 如果级别低于三级，则可进行发现，但无法收集性能数据。 可能收集一些计数器，但其他计数器将设为零。
- 如果无法收集存储和网络的性能数据，则评估大小建议基于 CPU 和内存的性能数据，以及磁盘和网络适配器的配置数据。
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
Azure 迁移服务 | TCP 443 | 收集器通过 SSL 443 与 Azure 迁移服务进行通信。
vCenter Server | TCP 443 | 收集器必须能够与 vCenter Server 进行通信。<br/><br/> 默认情况下，它通过 443 连接到 vCenter。<br/><br/> 如果 vCenter Server 在另一端口上侦听，则该端口应作为收集器上的传出端口提供。
RDP | TCP 3389 |


## <a name="securing-the-collector-appliance"></a>保护收集器设备

建议执行以下步骤来保护收集器设备：

- 请勿与未授权方共享管理员密码或将其错放。
- 未使用时请关闭设备。
- 将设备置于安全网络中。
- 在完成迁移后，删除设备实例。
- 此外，完成迁移后，还要同时删除磁盘备份文件 (VMDK)，因为磁盘上可能缓存了 vCenter 凭据。

## <a name="os-license-in-the-collector-vm"></a>收集器 VM 中的 OS 许可证

收集器附带有效期为 180 天的 Windows Server 2012 R2 评估许可证。 如果收集器虚拟机的评估期到期，则建议下载新 OVA 并创建一个新的设备。

## <a name="updating-the-os-of-the-collector-vm"></a>更新收集器 VM 的操作系统

尽管收集器设备具有 180 天的评估许可证，但你需要不断更新设备上的操作系统以避免设备的自动关闭。

- 如果收集器在 60 天内未更新，则启动自动关闭计算机功能。
- 如果正在运行发现，即使已超过 60 天未进行更新，也不会关闭计算机。 发现作业完成后，将关闭计算机。
- 如果使用收集器超过 60 天，则建议通过运行 Windows 更新来始终保持计算机处于最新状态。

## <a name="upgrading-the-collector-appliance-version"></a>收集器设备版本升级

无需再次下载 OVA，即可将收集器升级到最新版本。

1. 下载[最新列出的升级包](concepts-collector-upgrade.md)
2. 若要确保下载的修补程序安全，请打开管理员命令窗口并运行以下命令生成 ZIP 文件的哈希。 生成的哈希应与针对特定版本提到的哈希匹配：

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    （用法示例 C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256）
3. 将 zip 文件复制到 Azure 迁移收集器虚拟机（收集器设备）。
4. 右键单击 zip 文件并选择“全部提取”。
5. 右键单击 Setup.ps1 并选择“使用 PowerShell 运行”，然后按照屏幕上的说明来安装更新。

## <a name="discovery-process"></a>发现过程

设备设置完成后，可以运行发现。 工作方式如下：

- 按范围运行发现。 将发现指定的 vCenter 清单路径中的所有虚拟机。
    - 一次设置一个范围。
    - 范围可以包含 1500 个虚拟机或更少。
    - 范围可以是数据中心、文件夹，或 ESXi 主机。
- 连接到 vCenter Server 后，通过指定集合的迁移项目来进行连接。
- 发现虚拟机，并且其元数据和性能数据发送到 Azure。 这些操作是集合作业的一部分。
    - 收集器设备被赋予一个特定的收集器 ID，该 ID 对于发现中的给定计算机是持续存在的。
    - 正在运行的集合作业会被指定一个特定的会话 ID。 每个集合作业的会话 ID 都不同，并可用于故障排除。

### <a name="collected-metadata"></a>收集的元数据

收集器设备发现虚拟机的以下静态元数据：

- 虚拟机显示名称（在 vCenter Server 上）
- 虚拟机的清单路径（vCenter Server 上的主机/文件夹）
- IP 地址
- MAC 地址
- 操作系统
- 核心数、磁盘数、NIC 数
- 内存大小、磁盘大小
- 虚拟机、磁盘和网络的性能计数器。

#### <a name="performance-counters"></a>性能计数器

- “一次性发现”：收集一次性发现的计数器时，请注意以下事项：

    - 它可能需要最多 15 分钟的时间来收集配置元数据并将其发送到项目。
    - 收集完配置数据后，可能需要一小时的时间才能在门户中使用性能数据。
    - 在门户中提供元数据后，将显示虚拟机列表，便可以开始创建用于评估的组。
- 连续性发现：对于连续性发现，请注意以下事项：
    - 虚拟机的配置数据在发现启动后一小时即可使用
    - 性能数据在 2 小时后开始变为可用。
    - 在开始发现之后，设备至少需要一天的时间来分析环境，然后创建评估。

**计数器** | **级别** | **每个设备级别** | **对评估的影响**
--- | --- | --- | ---
cpu.usage.average | 1 | NA | 建议的虚拟机大小和成本  
mem.usage.average | 1 | NA | 建议的虚拟机大小和成本  
virtualDisk.read.average | 2 | 2 | 计算磁盘大小、存储成本和虚拟机大小
virtualDisk.write.average | 2 | 2  | 计算磁盘大小、存储成本和虚拟机大小
virtualDisk.numberReadAveraged.average | 1 | 3 |  计算磁盘大小、存储成本和虚拟机大小
virtualDisk.numberWriteAveraged.average | 1 | 3 |   计算磁盘大小、存储成本和虚拟机大小
net.received.average | 2 | 3 |  计算虚拟机大小                          |
net.transmitted.average | 2 | 3 | 计算虚拟机大小     

## <a name="next-steps"></a>后续步骤

[为本地 VMware 虚拟机设置评估](tutorial-assessment-vmware.md)
