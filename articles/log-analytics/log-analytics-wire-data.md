---
title: "Log Analytics 中的 Wire Data 解决方案| Microsoft Docs"
description: "线路数据是通过 OMS 代理（包括 Operations Manager 和 Windows 连接的代理）从计算机收集的网络和性能整合数据。 网络数据与日志数据结合在一起，可帮助你将数据相关联。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: banders
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: a552bbce2c6a1b60ab73e3e945985ff3dccbc93a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---

# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Log Analytics 中的 Wire Data 2.0（预览版）解决方案

![Wire Data 符号](./media/log-analytics-wire-data/wire-data2-symbol.png)

线路数据是通过 OMS 代理（包括 Operations Manager 和 Windows 连接的代理以及 Linux 代理）从计算机收集的网络和性能整合数据。 网络数据与其他日志数据结合在一起，可帮助你将数据进行关联。

除了 OMS 代理之外，Wire Data 解决方案使用在 IT 基础结构中的计算机上安装的 Microsoft 依赖关系代理。 依赖关系代理将监视 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)中处于网络层 2-3 层中的计算机接收和发送的网络数据，包括使用的各种协议和端口。 然后，这些代理将数据发送到 Log Analytics。

> [!NOTE]
> 不能将以前版本的 Wire Data 解决方案添加到新工作区。 如果已启用了原始的 Wire Data 解决方案，则可以继续使用该解决方案。 不过，若要使用 Wire Data 2.0，必须首先删除原始版本。

默认情况下，Log Analytics 从 Windows 中内置的计数器收集针对 CPU、内存和磁盘记录的数据以及网络性能数据。 网络以及其他数据的收集针对每个代理实时执行，包括正在由计算机使用的子网和应用程序级协议。 可以在“日志”选项卡的“设置”页面添加其他性能计数器。

如果你使用过 [sFlow](http://www.sflow.org/) 或其他包含 [Cisco 的 NetFlow 协议](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)的软件，则不会对来自线路数据的统计信息和数据感到陌生。

一些内置的日志搜索查询类型包括：

- 提供线路数据的代理
- 提供线路数据的代理 IP 地址
- 通过 IP 地址的出站通信
- 通过应用程序协议发送的字节数
- 通过应用程序服务发送的字节数
- 通过不同协议接收的字节数
- 通过 IP 版本发送和接收的总字节数
- 经可靠测量的平均连接延迟
- 启动或接收网络流量的计算机进程
- 某个进程的网络流量数

在使用线路数据搜索时，可以筛选数据并将其分组，以查看有关最突出的代理和协议的信息。 还可以查看某些计算机（IP 地址/MAC 地址）彼此之间何时进行了通信、通信持续了多长时间，以及发送了多少数据。基本上，可以基于搜索查看有关网络流量的元数据。

不过，因为查看的是元数据，因此这不一定适用于深入的故障排除。 Log Analytics 中的线路数据并非完整捕获的网络数据。 因此，它不能用于深入的数据包级故障排除。 与其他收集方法相比，使用代理的优点是不需要安装设备、重新配置网络交换机或执行复杂的配置。 线路数据完全基于代理，将代理安装在一台计算机上，它就会监视自己的网络流量。 另一个优点体现在想要监视在云提供商、主机托管服务提供商或 Microsoft Azure 中运行的工作负荷时。在这种情况下，用户本身不必拥有结构层。

## <a name="connected-sources"></a>连接的源

Wire Data 从 Microsoft 依赖关系代理获取其数据。 依赖关系代理依赖 OMS 代理连接到 Log Analytics。 这意味着服务器必须首先安装和配置 OMS 代理，然后再安装依赖关系代理。 下表介绍了 Wire Data 解决方案支持的连接的源。

| **连接的源** | **支持** | **说明** |
| --- | --- | --- |
| Windows 代理 | 是 | Wire Data 从 Windows 代理计算机分析和收集数据。 <br><br> 除 [OMS 代理](log-analytics-windows-agents.md)以外，Windows 代理还需要 Microsoft 依赖关系代理。 有关完整的操作系统版本列表，请参阅[支持的操作系统](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)。 |
| Linux 代理 | 是 | Wire Data 从 Linux 代理计算机分析和收集数据。<br><br> 除 [OMS 代理](log-analytics-linux-agents.md)以外，Linux 代理还需要 Microsoft 依赖关系代理。 有关完整的操作系统版本列表，请参阅[支持的操作系统](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 是 | Wire Data 在所连接的 [System Center Operations Manager 管理组](log-analytics-om-agents.md)中从 Windows 和 Linux 代理分析和收集数据。 <br><br> 需要从 System Center Operations Manager 代理计算机直接连接到 Log Analytics。 数据从管理组转发到 Log Analytics。 |
| Azure 存储帐户 | 否 | Wire Data 从代理计算机中收集数据，因此Wire Data 中没有从 Azure 存储收集的数据。 |

在 Windows 中，System Center Operations Manager 和 Log Analytics 都可使用 Microsoft Monitoring Agent (MMA) 收集和发送数据。 根据上下文，可将此代理称为 System Center Operations Manager 代理、OMS 代理、Log Analytics 代理、MMA 或直接代理。 System Center Operations Manager 和 Log Analytics 提供略有不同的 MMA 版本。 这些版本每个都可向 System Center Operations Manager 报告，或向 Log Analytics 报告，也可同时向两者报告。

在 Linux 上，适用于 Linux 的 OMS 代理收集数据并将其发送到 Log Analytics。 可对具有 OMS 直接代理的服务器或通过 System Center Operations Manager 管理组附加到 Log Analytics 的服务器使用 Wire Data。

本文中将所有代理都称为“OMS 代理”（不论是在 Linux 还是在 Windows 上，也不论是连接到 System Center Operations Manager 管理组还是直接连接到 Log Analytics）。 仅在上下文需要时才使用代理的具体部署名称。

依赖关系代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 Wire Data 中的数据始终由 OMS 代理直接或使用 OMS 网关传输到 Log Analytics。

![代理示意图](./media/log-analytics-wire-data/agents.png)

如果你是一位 System Center Operations Manager 用户且具有连接到 Log Analytics 的管理组：

- 若 System Center Operations Manager 代理可以访问 Internet 来连接到 Log Analytics，则无需进行额外配置。
- 若 System Center Operations Manager 代理无法通过 Internet 访问 Log Analytics，则需配置 OMS 网关来与 System Center Operations Manager 配合使用。

如果使用的是直接代理，则需要配置 OMS 代理本身才能连接到 Log Analytics 或 OMS 网关。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=52666)下载 OMS 网关。

## <a name="prerequisites"></a>先决条件

- 需要 [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) 解决方案产品/服务。
- 如果正在使用以前版本的 Wire Data 解决方案，必须首先将其删除。 不过，通过原始 Wire Data 解决方案捕获的所有数据在 Wire Data 2.0 和日志搜索中仍然可用。
- 需要管理员特权才能安装或卸载依赖关系代理。
- 依赖关系代理必须安装在具有 64 位操作系统的计算机上。

### <a name="operating-systems"></a>操作系统

以下部分列出了依赖关系代理支持的操作系统。 Wire Data 不支持任何操作系统的 32 位体系结构。

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows 桌面

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux、CentOS Linux 和 Oracle Linux（具有 RHEL 内核）

- 仅默认版本和 SMP Linux 内核版本受支持。
- 任何 Linux 发行版都不支持非标准内核版本（例如 PAE 和 Xen）。 例如，不支持版本字符串为 _2.6.16.21-0.8-xen_ 的系统。
- 不支持自定义内核（包括标准内核的重新编译）。
- 不支持 CentOSPlus 内核。
- 本文后面部分会介绍 Oracle Unbreakable Enterprise Kernel (UEK)。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **OS 版本** | **内核版本** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **OS 版本** | **内核版本** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **OS 版本** | **内核版本** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>具有 Unbreakable Enterprise Kernel (UEK) 的 Oracle Enterprise Linux

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **OS 版本** | **内核版本** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **OS 版本** | **内核版本** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **OS 版本** | **内核版本** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **OS 版本** | **内核版本** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>依赖关系代理下载

| **文件** | **OS** | **版本** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>配置

执行以下步骤，为工作区配置 Wire Data 解决方案。

1. 从 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) 中或者使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Activity Log Analytics 解决方案。
2. 在希望从中获取数据的每台计算机上安装依赖关系代理。 依赖关系代理可以监视与直接邻居之间的连接，因此不需要在每台计算机上都具有代理。

### <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安装依赖关系代理

需要管理员特权才能安装或卸载代理。

在运行 Windows 的计算机上，依赖关系代理是通过 InstallDependencyAgent-Windows.exe 安装的。 如果在没有任何选项的情况下运行此可执行文件，它将启动一个向导，以交互方式指导用户安装。

使用以下步骤在运行 Windows 的每台计算机上安装依赖关系代理：

1. 请按照[将 Windows 计算机连接到 Azure 中的 Log Analytics 服务](log-analytics-windows-agents.md)中的说明安装 OMS 代理。
2. 使用上一部分中的链接下载 Windows 代理，然后使用以下命令运行该代理：InstallDependencyAgent-Windows.exe
3. 按照向导安装代理。
4. 如果依赖关系代理无法启动，请检查日志以获取详细的错误信息。 在 Windows 代理上，日志目录是 %Programfiles%\Microsoft Dependency Agent\logs。

#### <a name="windows-command-line"></a>Windows 命令行

使用下表中的选项从命令行进行安装。 若要查看安装标志列表，请运行安装程序并使用 /? 标志，如下所示。

InstallDependencyAgent-Windows.exe /?

| **标志** | **说明** |
| --- | --- |
| <code>/?</code> | 获取命令行选项列表。 |
| <code>/S</code> | 执行无提示安装，无用户提示。 |

默认情况下，Windows 依赖关系代理的文件放置在 C:\Program Files\Microsoft Dependency Agent 中。

### <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安装依赖关系代理

需要根目录访问才能安装或配置代理。

使用 InstallDependencyAgent-Linux64.bin（具有自解压二进制文件的 Shell 脚本）在 Linux 计算机上安装依赖关系代理。 可使用 _sh_ 来运行文件或将执行权限添加到文件本身。

使用以下步骤在每台 Linux 计算机上安装依赖关系代理：

1. 按照[从 Linux 计算机收集和管理数据](log-analytics-agent-linux.md)中的说明安装 OMS 代理。
2. 使用上一部分中的链接下载 Linux 依赖关系代理，然后以 root 身份使用以下命令安装该代理：sh InstallDependencyAgent-Linux64.bin
3. 如果依赖关系代理无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是：/var/opt/microsoft/dependency-agent/log。

若要查看安装标志列表，请在使用 `-help` 标志的情况下运行安装程序，如下所示。

```
InstallDependencyAgent-Linux64.bin -help
```

| **标志** | **说明** |
| --- | --- |
| <code>-help</code> | 获取命令行选项列表。 |
| <code>-s</code> | 执行无提示安装，无用户提示。 |
| <code>--check</code> | 检查权限和操作系统，但不安装代理。 |

依赖关系代理的文件放置在以下目录中：

| **文件** | **位置** |
| --- | --- |
| 核心文件 | /opt/microsoft/dependency-agent |
| 日志文件 | /var/opt/microsoft/dependency-agent/log |
| 配置文件 | /etc/opt/microsoft/dependency-agent/config |
| 服务可执行文件 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二进制存储文件 | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>安装脚本示例

若要轻松地在多台服务器上同时部署依赖关系代理，请使用脚本。 可使用以下脚本示例下载依赖关系代理，并在 Windows 或 Linux 上进行安装。

#### <a name="powershell-script-for-windows"></a>适用于 Windows 的 PowerShell 脚本

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>适用于 Linux 的 Shell 脚本

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

若通过 Desired State Configuration 部署依赖关系代理，可使用 xPSDesiredStateConfiguration 模块和少量代码进行操作，如下所示：

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>卸载依赖关系代理

可使用以下部分来帮助删除依赖关系代理。

#### <a name="uninstall-the-dependency-agent-on-windows"></a>卸载 Windows 上的依赖关系代理

管理员可通过“控制面板”卸载适用于 Windows 的依赖关系代理。

管理员还可以运行 %Programfiles%\Microsoft Dependency Agent\Uninstall.exe 卸载依赖关系代理。

#### <a name="uninstall-the-dependency-agent-on-linux"></a>卸载 Linux 上的依赖关系代理

若要从 Linux 中彻底卸载依赖关系代理，必须删除代理本身以及随该代理自动安装的连接器。 可使用以下单个命令同时卸载这两项：

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>管理包

在 Log Analytics 工作区中激活 Wire Data 时，将向该工作区中的所有 Windows 服务器发送 300KB 的管理包。 若在[连接的管理组](log-analytics-om-agents.md)中使用 System Center Operations Manager 代理，则会从 System Center Operations Manager 部署依赖关系监视器管理包。 如果代理是直接连接的，则 Log Analytics 会传送管理包。

管理包名为 Microsoft.IntelligencePacks.ApplicationDependencyMonitor。 它将写入到 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs。 管理包所使用的数据源是 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll。

## <a name="using-the-solution"></a>使用解决方案

**安装和配置解决方案**

使用以下信息安装和配置解决方案。

- Wire Data 解决方案从运行 Windows Server 2012 R2、Windows 8.1 和更高版本操作系统的计算机获取数据。
- 你想要获取线路数据的计算机上需要安装 Microsoft.NET Framework 4.0 或更高版本。
- 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的流程，将 Wire Data 解决方案添加到 Log Analytics 工作区。 无需进一步的配置。
- 如果想要查看特定解决方案的线路数据，需要先将该解决方案添加到工作区。

在安装代理并安装解决方案后，Wire Data 2.0 磁贴将出现在工作区中。

> [!NOTE]
> 当前，必须使用 OMS 门户查看线路数据。 无法使用 Azure 门户查看线路数据。

![Wire Data 磁贴](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>使用 Wire Data 2.0 解决方案

在 OMS 门户中，单击“Wire Data 2.0”磁贴以打开“Wire Data”仪表板。 该仪表板包含下表中的边栏选项卡。 每个边栏选项卡按照指定范围和时间范围列出了匹配该边栏选项卡条件的最多 10 个项。 可通过单击边栏选项卡底部的“查看全部”或单击边栏选项卡标题，运行返回所有记录的日志搜索。

| **边栏选项卡** | **说明** |
| --- | --- |
| 正在捕获网络流量的代理 | 显示正在捕获网络流量的代理数，并列出正在捕获流量的排名前 10 的计算机。 单击数字可以针对 <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code> 运行日志搜索。 单击列表中的某台计算机可运行日志搜索，将返回已捕获的总字节数。 |
| 本地子网 | 显示代理已发现的本地子网数。  单击数字可以针对 <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> 运行日志搜索，这将列出所有子网以及通过每个子网发送的字节数。 单击列表中的某个子网可运行日志搜索，将返回通过该子网发送的总字节数。 |
| 应用程序级协议 | 显示代理发现的使用中的应用程序级协议的数目。 单击数字可以针对 <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code> 运行日志搜索。 单击某个协议可运行日志搜索，将返回使用该协议发送的总字节数。 |

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Wire Data 仪表板](./media/log-analytics-wire-data/wire-data-dash.png)

可以使用“正在捕获网络流量的代理”边栏选项卡来确定计算机正在消耗多少网络带宽。 可以通过此边栏选项卡轻松找到你的环境中“最健谈的”计算机。 此类计算机可能负载过重，行为异常，或者使用比平时更多的网络资源。

![日志搜索示例](./media/log-analytics-wire-data/log-search-example01.png)

类似地，可以使用“本地子网”边栏选项卡确定有多少网络流量正在通过各个子网移动。 用户通常围绕其应用的关键领域定义子网。 可以通过此边栏选项卡查看这些领域。

![日志搜索示例](./media/log-analytics-wire-data/log-search-example02.png)

“应用程序级协议”边栏选项卡很有用，因为它可以帮助你了解正在使用什么协议。 例如，你可能预料网络环境中没有使用 SSH。 查看此边栏选项卡中提供的信息可以快速确认或否定你的预期。

![日志搜索示例](./media/log-analytics-wire-data/log-search-example03.png)

在此示例中，可以钻取到 SSH 详细信息来查看哪些计算机在使用 SSH 并查看许多其他通信详细信息。

![sh 搜索结果](./media/log-analytics-wire-data/ssh-details.png)

了解协议流量是否在随时间推移而增加或减少也非常有用。 例如，如果某个应用程序传输的数据量在增加，则可能有某些事情需要注意或需要特别注意。

## <a name="input-data"></a>输入数据

线路数据使用已经启用的代理来收集网络流量的元数据。 每个代理大约每 15 秒发送一次数据。

## <a name="output-data"></a>输出数据

将为每种输入数据创建 _WireData_ 类型的记录。 WireData 记录具有下表中所示的属性：

| 属性 | 说明 |
|---|---|
| Computer | 从中收集了数据的计算机名称 |
| TimeGenerated | 记录的时间 |
| LocalIP | 本地计算机的 IP 地址 |
| SessionState | 已连接或已断开连接 |
| ReceivedBytes | 已接收的字节数 |
| ProtocolName | 使用的网络协议的名称 |
| IPVersion | IP 版本 |
| Direction | 入站或出站 |
| MaliciousIP | 某个已知恶意源的 IP 地址 |
| Severity | 可疑恶意软件的严重性 |
| RemoteIPCountry | 远程 IP 地址所在的国家/地区 |
| ManagementGroupName | Operations Manager 管理组的名称 |
| SourceSystem | 从中收集了数据的源 |
| SessionStartTime | 会话开始时间 |
| SessionEndTime | 会话结束时间 |
| LocalSubnet | 从中收集了数据的子网 |
| LocalPortNumber | 本地端口号 |
| RemoteIP | 远程计算机使用的远程 IP 地址 |
| RemotePortNumber | 远程 IP 地址使用的端口号 |
| SessionID | 标识两个 IP 地址之间的通信会话的唯一值 |
| SentBytes | 已发送的字节数 |
| TotalBytes | 会话期间发送的总字节数 |
| ApplicationProtocol | 使用的网络协议的类型   |
| ProcessID | Windows 进程 ID |
| ProcessName | 进程的路径和文件名 |
| RemoteIPLongitude | IP 经度值 |
| RemoteIPLatitude | IP 纬度值 |


## <a name="next-steps"></a>后续步骤

- [搜索日志](log-analytics-log-searches.md)以查看详细的线路数据搜索记录。

