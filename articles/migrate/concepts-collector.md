---
title: Azure Migrate 中的收集器设备 | Microsoft Docs
description: 概述了收集器设备及其配置方法。
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: d0dd310a1f6dff389a4d3dd41dc389b7117272fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203662"
---
# <a name="collector-appliance"></a>收集器设备

[Azure Migrate](migrate-overview.md) 会评估要迁移到 Azure 的本地工作负载。 本文介绍如何使用收集器设备。



## <a name="overview"></a>概述

Azure Migrate 收集器是一种轻量设备，可以用来发现本地 vCenter 环境。 此设备可发现本地 VMware 计算机，并将其相关元数据发送到 Azure Migrate 服务。

收集器设备是一种 OVF，可以从 Azure Migrate 项目中下载。 它可以实例化带有 4 核心、8 GB RAM 和一个 80 GB 磁盘的 VMware 虚拟机。 此设备的操作系统是 Windows Server 2012 R2（64 位）。

可以按照此处的步骤创建收集器：[如何创建收集器 VM](tutorial-assessment-vmware.md#create-the-collector-vm)。

## <a name="collector-communication-diagram"></a>收集器通信关系图

![收集器通信关系图](./media/tutorial-assessment-vmware/portdiagram.PNG)


| 组件      | 通信对象   | 所需端口                            | 原因                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| 收集器      | Azure Migrate 服务 | TCP 443                                  | 收集器应该能够通过 SSL 端口 443 与服务通信 |
| 收集器      | vCenter Server        | 默认值 443                             | 收集器应能够与 vCenter 服务器进行通信。 它默认情况下通过 443 连接到 vCenter。 如果 vCenter 在另一端口上侦听，则该端口应作为收集器上的传出端口提供 |
| 收集器      | RDP|   | TCP 3389 | 使你能够通过 RDP 登录到收集器计算机 |





## <a name="collector-pre-requisites"></a>收集器先决条件

收集器需通过一些先决条件检查，目的是确保它能够连接到 Azure Migrate 服务并上传发现的数据。 本文探讨每个先决条件并说明其是必需条件的原因。

### <a name="internet-connectivity"></a>Internet 连接

收集器设备需要连接到 Internet 才能发送所发现的计算机信息。 可以采用下列两种方式之一将计算机连接到 Internet。

1. 可以将收集器配置为进行直接的 Internet 连接。
2. 可以将收集器配置为通过代理服务器进行连接。
    * 如果代理服务器需要身份验证，则可在连接设置中指定用户名和密码。
    * 代理服务器的 IP 地址/FQDN 应该是 http://IPaddress 或 http://FQDN 格式。 仅支持 http 代理。

> [!NOTE]
> 收集器不支持基于 HTTPS 的代理服务器。

#### <a name="whitelisting-urls-for-internet-connection"></a>Internet 连接的允许列表 URL

如果收集器可以通过提供的设置连接到 Internet，则表明先决条件检查成功。 验证连接检查的方法是连接到一系列在下表中给出的 URL。 如果使用任何基于 URL 的防火墙代理控制出站连接，请确保将下列必需的 URL 列入允许列表：

**URL** | **用途**  
--- | ---
*.portal.azure.com | 需检查与 Azure 服务的连接并验证时间同步问题。

另外，该检查还会尝试验证到以下 URL 的连接，但即使不能进行访问，也不表明无法通过检查。 为以下 URL 配置允许列表是可选的，但需执行手动步骤来解决先决条件检查的问题。

**URL** | **用途**  | **如果不启用允许列表，会发生什么情况**
--- | --- | ---
*.oneget.org:443 | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。 | PowerCLI 安装失败。 手动安装该模块。
*.windows.net:443 | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。 | PowerCLI 安装失败。 手动安装该模块。
*.windowsazure.com:443 | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。 | PowerCLI 安装失败。 手动安装该模块。
*.powershellgallery.com:443 | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。 | PowerCLI 安装失败。 手动安装该模块。
*.msecnd.net:443 | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。 | PowerCLI 安装失败。 手动安装该模块。
*.visualstudio.com:443 | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。 | PowerCLI 安装失败。 手动安装该模块。

### <a name="time-is-in-sync-with-the-internet-server"></a>时间与 Internet 服务器同步

收集器应与 Internet 时间服务器同步，确保向服务发出的请求经过身份验证。 portal.azure.com URL 应该能够从收集器访问，以便验证时间。 如果计算机不同步，则需更改收集器 VM 上的时钟时间，使之与当前时间匹配，如下所示：

1. 在 VM 上打开管理员命令提示符。
1. 若要检查时区，请运行 w32tm /tz。
1. 若要同步时间，请运行 w32tm /resync。

### <a name="collector-service-should-be-running"></a>收集器服务应该正在运行

Azure Migrate 收集器服务应该正在计算机上运行。 此服务在计算机启动时自动启动。 如果服务未运行，可以通过控制面板启动 Azure Migrate 收集器服务。 收集器服务负责连接到 vCenter Server，收集计算机元数据和性能数据，然后将其发送到服务。

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

需安装 VMware PowerCLI powershell 模块，这样收集器才能与 vCenter Server 通信，并查询计算机详细信息及其性能数据。 powershell 模块在进行先决条件检查的过程中自动下载并安装。 自动下载要求将一些 URL 加入允许列表中。如果下载失败，则需通过加入允许列表来提供访问权限，或者手动安装模块。

通过以下步骤手动安装模块：

1. 若要在没有 Internet 连接的情况下在收集器上安装 PowerCLI，请执行[此链接](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)中提供的步骤：
2. 在另一可以访问 Internet 的计算机上安装 PowerShell 模块以后，请将 VMware.* 文件从该计算机复制到收集器计算机。
3. 重启先决条件检查，确认 PowerCLI 已安装。

## <a name="connecting-to-vcenter-server"></a>连接到 vCenter Server

收集器应该连接到 vCenter Server 并能够查询虚拟机及其元数据和性能计数器。 此数据可供项目用来对评估进行计算。

1. 若要连接到 vCenter Server，可以使用一个只读帐户来运行发现，该帐户的权限已在下表中给出。

    |任务  |所需角色/帐户  |权限  |
    |---------|---------|---------|
    |基于收集器设备的发现    | 至少需要一个只读用户        |数据中心对象 –> 传播到子对象、角色=只读         |

2. 只能访问那些可供指定的 vCenter 帐户访问的数据中心来获取发现。
3. 若要连接到 vCenter Server，需指定 vCenter FQDN/IP 地址。 默认情况下，将通过端口 443 进行连接。 如果已将 vCenter 配置为在另一端口号上进行侦听，则可将它指定为服务器地址的一部分，采用 IPAddress:Port_Number 或 FQDN:Port_Number 格式。
4. 开始部署之前，应将 vCenter Server 的统计信息设置设定为级别 3。 如果低于级别 3，可以完成发现，但不会收集存储和网络的性能数据。 这种情况的评估大小建议基于 CPU 和内存的性能数据，以及磁盘和网络适配器的配置数据。 [详细了解](./concepts-collector.md)收集的具体数据及其对评估的具体影响。
5. 收集器应该具有网络视线，可以看到 vCenter Server。

> [!NOTE]
> 仅正式支持 vCenter Server 5.5、6.0 和 6.5 版。

> [!IMPORTANT]
> 建议将统计信息级别设置为最高常用级别 (3)，以便正确收集所有计数器。 如果将 vCenter 设置的级别较低，则可能只完整收集几个计数器，而其他的计数器的收集数为 0。 这样，评估可能会显示不完整的数据。

### <a name="selecting-the-scope-for-discovery"></a>选择发现的范围

连接到 vCenter 以后，可以选择一个可供发现的范围。 选择一个范围后，即可通过指定的 vCenter 清单路径发现所有虚拟机。

1. 范围可以是数据中心、文件夹，也可以是 ESXi 主机。
2. 一次只能选择一个范围。 若要选择更多虚拟机，可以先完成一个发现，然后使用新的范围重启发现过程。
3. 只能选择虚拟机数不到 1500 的范围。

## <a name="specify-migration-project"></a>指定迁移项目

连接本地 vCenter 并指定范围以后，即可指定迁移项目详细信息，以便将其用于发现和评估。 指定项目 ID 和密钥，然后进行连接。

## <a name="start-discovery-and-view-collection-progress"></a>启动发现并查看收集进度

启动发现以后，就会发现 vCenter 虚拟机，并会将其元数据和性能数据发送到服务器。 进度状态还会告知以下 ID：

1. 收集器 ID：提供给收集器计算机的唯一 ID。 进行不同的发现时，给定计算机的此 ID 不会更改。 在故障情况下需要向 Microsoft 支持部门报告问题时，可以使用此 ID。
2. 会话 ID：用于正在运行的收集作业的唯一 ID。 当发现作业完成后，可以参阅门户中的同一会话 ID。 每个收集作业的此 ID 都会更改。 发生故障时，可以将此 ID 报告给 Microsoft 支持部门。

### <a name="what-data-is-collected"></a>收集什么数据？

收集作业发现与所选虚拟机相关的以下静态元数据。

1. VM 显示名称（在 vCenter 上）
2. VM 的清单路径（vCenter 中的主机/文件夹）
3. IP 地址
4. MAC 地址
5. 操作系统
5. 核心数、磁盘数、NIC 数
6. 内存大小、磁盘大小
7. VM、磁盘和网络的性能计数器，如下表中所列出的那样。

下表列出了所收集的性能计数器，并且还列出了在未收集特定计数器的情况下受影响的评估结果。

|计数器                                  |级别    |设备级别  |评估影响                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |不可用                |建议的 VM 大小和成本                    |
|mem.usage.average                        | 1       |不可用                |建议的 VM 大小和成本                    |
|virtualDisk.read.average                 | 2       |2                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.write.average                | 2       |2                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|net.received.average                     | 2       |3                 |VM 大小和网络成本                        |
|net.transmitted.average                  | 2       |3                 |VM 大小和网络成本                        |

> [!WARNING]
> 如果刚设置了更高的统计信息级别，将需要最多一天的时间来生成性能计数器。 因此，建议在一天后运行发现。

### <a name="time-required-to-complete-the-collection"></a>完成收集所需的时间

收集器仅发现计算机数据，然后将其发送到项目。 项目可能还需要花一些时间才会将发现的数据显示在门户中，然后你就可以开始创建评估。

根据所选范围中虚拟机数目的不同，可能需要长达 15 分钟的时间才能将静态元数据发送到项目。 静态元数据在门户中可用以后，就会在门户中看到计算机的列表，此时就可以开始创建组。 必须等到收集作业完成且项目已处理数据后，才能创建评估。 收集器上的收集作业完成后，可能需要长达一小时的时间才能在门户中提供性能数据，具体取决于所选范围中虚拟机的数目。

## <a name="locking-down-the-collector-appliance"></a>锁定收集器设备
我们建议在收集器设备上运行持续的 Windows 更新。 如果收集器已有 45 天未更新，收集器将开始自动关闭计算机。 如果正在运行发现，即使已超过 45 天，也不会关闭计算机。 发现作业完成后，将关闭计算机。 如果使用收集器超过 45 天，我们建议通过运行 Windows 更新来始终保持计算机处于已更新状态。

我们还建议执行以下步骤来保护设备
1. 请勿与未授权方共享管理员密码或将其错放。
2. 未使用时请关闭设备。
3. 将设备置于安全网络中。
4. 迁移工作完成后，删除设备实例。 请务必同时删除磁盘备份文件 (VMDK)，因为磁盘上可能缓存了 vCenter 凭据。

## <a name="how-to-upgrade-collector"></a>如何升级收集器

无需再次下载 OVA，即可将收集器升级到最新版本。

1. 下载最新[升级包](https://aka.ms/migrate/col/latestupgrade)。
2. 若要确保下载的修补程序安全，请打开管理员命令窗口并运行以下命令生成 ZIP 文件的哈希。 生成的哈希应与针对特定版本提到的哈希匹配：

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    （用法示例 C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.5.zip SHA256）
3. 将 zip 文件复制到 Azure Migrate 收集器虚拟机（收集器设备）。
4. 右键单击 zip 文件并选择“全部提取”。
5. 右键单击 Setup.ps1 并选择“使用 PowerShell 运行”，然后按照屏幕上的说明来安装更新。

### <a name="list-of-updates"></a>更新列表

#### <a name="upgrade-to-version-1097"></a>升级到版本 1.0.9.7

若要升级到版本 1.0.9.7，请下载[包](https://aka.ms/migrate/col/upgrade_9_7)

**算法** | **哈希值**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

#### <a name="upgrade-to-version-1095"></a>升级到版本 1.0.9.5

若要升级到版本 1.0.9.5，请下载[包](https://aka.ms/migrate/col/upgrade_9_5)

**算法** | **哈希值**
--- | ---
MD5 | d969ebf3bdacc3952df0310d8891ffdf
SHA1 | f96cc428eaa49d597eb77e51721dec600af19d53
SHA256 | 07c03abaac686faca1e82aef8b80e8ad8eca39067f1f80b4038967be1dc86fa1

## <a name="next-steps"></a>后续步骤

[为本地 VMware VM 设置评估](tutorial-assessment-vmware.md)
