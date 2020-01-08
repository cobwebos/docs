---
title: Azure Monitor 中的线路数据解决方案 |Microsoft Docs
description: 线路数据是具有 Log Analytics 代理的计算机提供的整合网络和性能数据。 网络数据与日志数据结合在一起，可帮助将数据相关联。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/03/2018
ms.openlocfilehash: 031a09203ab2ab2bcfcdf4352e975c1374446c25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365795"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Azure Monitor 中的 Wire Data 2.0 （预览版）解决方案

![Wire Data 符号](media/wire-data/wire-data2-symbol.png)

线路数据是通过 Log Analytics 代理（包括由环境中的 Operations Manager 监视的代理）从与 Windows 和 Linux 相连的计算机中收集的网络与性能整合数据。 网络数据与其他日志数据结合在一起，可帮助你将数据进行关联。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

除了 Log Analytics 代理之外，Wire Data 解决方案使用在 IT 基础结构中的计算机上安装的 Microsoft 依赖关系代理。 依赖关系代理将监视 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)中处于网络层 2-3 层中的计算机接收和发送的网络数据，包括使用的各种协议和端口。 然后，将数据发送到使用代理 Azure Monitor。  

>[!NOTE]
>如果已部署服务映射或正在考虑服务映射或[用于 VM 的 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md)，则会在为传输数据提供类似信息的 Azure Monitor 中收集并存储新的连接度量值数据集。

默认情况下，Azure Monitor 会记录 Windows 和 Linux 内置的计数器中的 CPU、内存、磁盘和网络性能数据的数据，以及可以指定的其他性能计数器。 网络以及其他数据的收集针对每个代理实时执行，包括正在由计算机使用的子网和应用程序级协议。  Wire Data 查看应用程序层（而不是 TCP 传输层）的数据。  该解决方案不会查看单个 Ack 和 Syn。  握手完成后，会被视为实时连接并标记为已连接。 只要双方同意开启套接字，并且数据可以在彼此之间来回传递，该实时连接就将保持不变。  一旦任一端关闭连接，就会将其标记为断开连接。  因此，它只对已成功完成数据包的带宽计数，而不会对重新发送或失败的数据包进行报告。

如果使用过 [sFlow](http://www.sflow.org/) 或其他包含 [Cisco 的 NetFlow 协议](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)的软件，则不会对来自线路数据的统计信息和数据感到陌生。

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

不过，因为查看的是元数据，因此这不一定适用于深入的故障排除。 Azure Monitor 中的传输数据不是网络数据的完整捕获。  因此，它不能用来进行数据包级别的深度故障排除。 与其他收集方法相比，使用代理的优点是无需安装设备、重新配置网络交换机或执行复杂的配置。 线路数据完全基于代理，将代理安装在一台计算机上，它就会监视自己的网络流量。 另一个优点体现在想要监视在云提供商、主机托管服务提供商或 Microsoft Azure 中运行的工作负荷时。在这种情况下，用户本身不必拥有结构层。

## <a name="connected-sources"></a>连接的源

Wire Data 从 Microsoft 依赖关系代理获取其数据。 Dependency Agent 依赖于 Log Analytics 代理连接到 Azure Monitor。 这意味着服务器必须首先安装和配置 Log Analytics 代理，然后再安装 Dependency Agent。 下表介绍了 Wire Data 解决方案支持的连接的源。

| **连接的源** | **支持** | **说明** |
| --- | --- | --- |
| Windows 代理 | 是 | Wire Data 从 Windows 代理计算机分析和收集数据。 <br><br> 除[适用于 Windows 的 Log Analytics 代理](../platform/agent-windows.md)外，Windows 代理还需要 Microsoft Dependency Agent。 有关完整的操作系统版本列表，请参阅[支持的操作系统](vminsights-enable-overview.md#supported-operating-systems)。 |
| Linux 代理 | 是 | Wire Data 从 Linux 代理计算机分析和收集数据。<br><br> 除[适用于 Linux 的 Log Analytics 代理](../learn/quick-collect-linux-computer.md)外，Linux 代理还需要 Microsoft Dependency Agent。 有关完整的操作系统版本列表，请参阅[支持的操作系统](vminsights-enable-overview.md#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 是 | Wire Data 在所连接的 [System Center Operations Manager 管理组](../platform/om-agents.md)中从 Windows 和 Linux 代理分析和收集数据。 <br><br> 需要从 System Center Operations Manager 代理计算机到 Azure Monitor 的直接连接。 |
| Azure 存储帐户 | 否 | Wire Data 从代理计算机中收集数据，因此Wire Data 中没有从 Azure 存储收集的数据。 |

在 Windows 上，System Center Operations Manager 和 Azure Monitor 使用 Microsoft Monitoring Agent （MMA）来收集和发送数据。 根据上下文，可将此代理称为 System Center Operations Manager 代理、Log Analytics 代理、MMA 或直接代理。 System Center Operations Manager 和 Azure Monitor 提供略微不同的 MMA 版本。 每个版本都可以 System Center Operations Manager、Azure Monitor 或两者。

在 Linux 上，适用于 Linux 的 Log Analytics 代理收集数据并将其发送到 Azure Monitor。 你可以在代理直接连接到 Azure Monitor 的服务器上或通过 System Center Operations Manager 管理组连接到 Azure Monitor 的服务器上使用线路数据。

依赖关系代理不会传输任何数据本身，也不需要对防火墙或端口进行任何更改。 传输数据中的数据始终由 Log Analytics 代理传输到 Azure Monitor （直接或通过 Log Analytics 网关）。

![代理示意图](./media/wire-data/agents.png)

如果你是具有连接到 Azure Monitor 的管理组的 System Center Operations Manager 用户：

- 如果 System Center Operations Manager 代理可以访问 internet 连接到 Azure Monitor，则不需要其他配置。
- 你需要配置 Log Analytics 网关，以便在 System Center Operations Manager 代理无法通过 internet 访问 Azure Monitor 时使用 System Center Operations Manager。

如果你的 Windows 或 Linux 计算机无法直接连接到服务，则需要使用 Log Analytics 网关配置 Log Analytics 代理以连接到 Azure Monitor。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=52666)下载 Log Analytics 网关。

## <a name="prerequisites"></a>必备组件

- 需要 [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) 解决方案产品/服务。
- 如果正在使用以前版本的 Wire Data 解决方案，必须首先将其删除。 不过，通过原始 Wire Data 解决方案捕获的所有数据在 Wire Data 2.0 和日志搜索中仍然可用。
- 需要管理员权限才能安装或卸载依赖关系代理。
- 依赖关系代理必须安装在具有64位操作系统的计算机上。

### <a name="operating-systems"></a>操作系统

以下部分列出了依赖关系代理支持的操作系统。 Wire Data 不支持任何操作系统的 32 位体系结构。

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows 桌面

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>受支持的 Linux 操作系统
以下部分列出了适用于 Linux 上的依赖关系代理的受支持操作系统。  

- 仅默认版本和 SMP Linux 内核版本受支持。
- 任何 Linux 发行版都不支持非标准内核版本（例如 PAE 和 Xen）。 例如，不支持版本字符串为“2.6.16.21-0.8-xen”的系统。
- 不支持自定义内核（包括标准内核的重新编译）。

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 版本 | 内核版本 |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 版本 | 内核版本 |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| OS 版本 | 内核版本 |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| OS 版本 | 内核版本 |
|:--|:--|
| Ubuntu 18.04 | 内核4.15。\*<br>4.18* |
| Ubuntu 16.04.3 | 内核 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| OS 版本 | 内核版本
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 内核版本
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Dependency Agent 下载

| 文件 | OS | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>配置

执行以下步骤，为工作区配置 Wire Data 解决方案。

1. 从[Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview)或使用[从解决方案库添加监视解决方案](../../azure-monitor/insights/solutions.md)中所述的过程，启用 Activity Log Analytics 解决方案。
2. 在要获取数据的每台计算机上安装依赖关系代理。 依赖关系代理可以监视到直属邻居的连接，因此你可能不需要在每台计算机上使用代理。

> [!NOTE]
> 不能将以前版本的 Wire Data 解决方案添加到新工作区。 如果已启用了原始的 Wire Data 解决方案，则可以继续使用该解决方案。 不过，若要使用 Wire Data 2.0，必须首先删除原始版本。
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安装依赖项代理

需要管理员特权才能安装或卸载代理。

依赖关系代理安装在运行 Windows 的计算机上，通过 Installdependencyagent-windows.exe。 如果在没有任何选项的情况下运行此可执行文件，它将启动一个向导，以交互方式指导用户安装。

使用以下步骤在每台运行 Windows 的计算机上安装依赖关系代理：

1. 遵循[从托管在环境中的 Windows 计算机收集数据](../../azure-monitor/platform/agent-windows.md)所述步骤安装 Log Analytics 代理。
2. 使用上一部分中的链接下载 Windows 依赖项代理，并使用以下命令运行该代理： `InstallDependencyAgent-Windows.exe`
3. 按照向导安装代理。
4. 如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 对于 Windows 代理，日志目录是 %Programfiles%\Microsoft Dependency Agent\logs。

#### <a name="windows-command-line"></a>Windows 命令行

使用下表中的选项从命令行进行安装。 若要查看安装标志列表，请运行安装程序并使用 /? 标志，如下所示。

InstallDependencyAgent-Windows.exe /?

| **标志** | **说明** |
| --- | --- |
| <code>/?</code> | 获取命令行选项列表。 |
| <code>/S</code> | 执行无提示安装，无用户提示。 |

默认情况下，Windows 依赖关系代理的文件放置在 C:\Program Files\Microsoft 依赖项代理中。

### <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安装 Dependency Agent

需要根目录访问才能安装或配置代理。

依赖关系代理通过 Installdependencyagent-linux64.bin （带有自解压缩二进制文件的 shell 脚本）在 Linux 计算机上安装。 可使用 _sh_ 来运行文件或将执行权限添加到文件本身。

使用以下步骤在每台 Linux 计算机上安装 Dependency Agent：

1. 遵循[从托管在环境中的 Linux 计算机收集数据](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)所述步骤安装 Log Analytics 代理。
2. 使用上一部分中的链接下载 Linux 依赖关系代理，然后以 root 身份使用以下命令安装该代理：sh InstallDependencyAgent-Linux64.bin
3. 如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是：/var/opt/microsoft/dependency-agent/log。

若要查看安装标志列表，请在使用 `-help` 标志的情况下运行安装程序，如下所示。

```
InstallDependencyAgent-Linux64.bin -help
```

| **标志** | **说明** |
| --- | --- |
| <code>-help</code> | 获取命令行选项列表。 |
| <code>-s</code> | 执行无提示安装，无用户提示。 |
| <code>--check</code> | 检查权限和操作系统，但不安装代理。 |

Dependency Agent 的文件放置在以下目录中：

| **文件** | **位置** |
| --- | --- |
| 核心文件 | /opt/microsoft/dependency-agent |
| 日志文件 | /var/opt/microsoft/dependency-agent/log |
| 配置文件 | /etc/opt/microsoft/dependency-agent/config |
| 服务可执行文件 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二进制存储文件 | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>安装脚本示例

若要同时在多台服务器上轻松部署依赖关系代理，可以使用脚本。 您可以使用以下脚本示例，在 Windows 或 Linux 上下载和安装依赖关系代理。

#### <a name="powershell-script-for-windows"></a>适用于 Windows 的 PowerShell 脚本

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>适用于 Linux 的 Shell 脚本

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>期望状态配置

若要通过所需状态配置部署依赖关系代理，可以使用 xPSDesiredStateConfiguration 模块和类似于下面的代码：

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>卸载依赖关系代理

使用下列部分来帮助你删除依赖项代理。

#### <a name="uninstall-the-dependency-agent-on-windows"></a>卸载 Windows 上的依赖关系代理

管理员可通过“控制面板”卸载适用于 Windows 的 Dependency Agent。

管理员还可以运行 %Programfiles%\Microsoft Dependency Agent\Uninstall.exe 卸载 Dependency Agent。

#### <a name="uninstall-the-dependency-agent-on-linux"></a>卸载 Linux 上的依赖关系代理

若要完全从 Linux 中卸载依赖关系代理，必须删除代理本身和连接器（随代理一起自动安装）。 可使用以下单个命令同时卸载这两项：

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>管理包

在 Log Analytics 工作区中激活 Wire Data 时，将向该工作区中的所有 Windows 服务器发送 300KB 的管理包。 若在[连接的管理组](../platform/om-agents.md)中使用 System Center Operations Manager 代理，则会从 System Center Operations Manager 部署依赖关系监视器管理包。 如果代理是直接连接的，Azure Monitor 会传递管理包。

管理包名为 Microsoft.IntelligencePacks.ApplicationDependencyMonitor。 它将写入到 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs。 管理包所使用的数据源是 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll。

## <a name="using-the-solution"></a>使用解决方案

使用以下信息安装和配置解决方案。

- Wire Data 解决方案从运行 Windows Server 2012 R2、Windows 8.1 和更高版本操作系统的计算机获取数据。
- 想要获取线路数据的计算机上需要安装 Microsoft.NET Framework 4.0 或更高版本。
- 使用[从解决方案库添加监视解决方案](solutions.md)中所述的过程，将网络数据解决方案添加到 Log Analytics 工作区。 无需进一步的配置。
- 如果想要查看特定解决方案的线路数据，需要先将该解决方案添加到工作区。

在安装代理并安装解决方案后，Wire Data 2.0 磁贴将出现在工作区中。

![Wire Data 磁贴](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>使用 Wire Data 2.0 解决方案

在 Azure 门户的 Log Analytics 工作区的“概览”页中，单击“Wire Data 2.0”磁贴打开 Wire Data 仪表板。 该仪表板包含下表中的边栏选项卡。 每个边栏选项卡按照指定范围和时间范围列出了匹配该边栏选项卡条件的最多 10 个项。 可通过单击边栏选项卡底部的“查看全部”或单击边栏选项卡标题，运行返回所有记录的日志搜索。

| **边栏选项卡** | **说明** |
| --- | --- |
| 正在捕获网络流量的代理 | 显示正在捕获网络流量的代理数，并列出正在捕获流量的排名前 10 的计算机。 单击数字可以针对 <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> 运行日志搜索。 单击列表中的某台计算机可运行日志搜索，将返回已捕获的总字节数。 |
| 本地子网 | 显示代理已发现的本地子网数。  单击数字可以针对 <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> 运行日志搜索，这将列出所有子网以及通过每个子网发送的字节数。 单击列表中的某个子网可运行日志搜索，将返回通过该子网发送的总字节数。 |
| 应用程序级协议 | 显示代理发现的使用中的应用程序级协议的数目。 单击数字可以针对 <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> 运行日志搜索。 单击某个协议可运行日志搜索，将返回使用该协议发送的总字节数。 |

![Wire Data 仪表板](./media/wire-data/wire-data-dash.png)

可以使用“正在捕获网络流量的代理”边栏选项卡来确定计算机正在消耗多少网络带宽。 可以通过此边栏选项卡轻松找到你的环境中“最健谈的”计算机。 此类计算机可能负载过重，行为异常，或者使用比平时更多的网络资源。

![日志搜索示例](./media/wire-data/log-search-example01.png)

类似地，可以使用“本地子网”边栏选项卡确定有多少网络流量正在通过各个子网移动。 用户通常围绕其应用程序的关键领域定义子网。 可以通过此边栏选项卡查看这些领域。

![日志搜索示例](./media/wire-data/log-search-example02.png)

“应用程序级协议”边栏选项卡很有用，因为它可以帮助你了解正在使用什么协议。 例如，你可能预料网络环境中没有使用 SSH。 查看此边栏选项卡中提供的信息可以快速确认或否定你的预期。

![日志搜索示例](./media/wire-data/log-search-example03.png)

了解协议流量是否在随时间推移而增加或减少也非常有用。 例如，如果某个应用程序传输的数据量在增加，则可能有某些事情需要注意或需要特别注意。

## <a name="input-data"></a>输入数据

线路数据使用已经启用的代理来收集网络流量的元数据。 每个代理大约每 15 秒发送一次数据。

## <a name="output-data"></a>输出数据

将为每种输入数据创建 _WireData_ 类型的记录。 WireData 记录具有下表中所示的属性：

| 属性 | Description |
|---|---|
| Computer | 从中收集了数据的计算机名称 |
| TimeGenerated | 记录的时间 |
| LocalIP | 本地计算机的 IP 地址 |
| SessionState | 已连接或已断开连接 |
| ReceivedBytes | 已接收的字节数 |
| ProtocolName | 使用的网络协议的名称 |
| IPVersion | IP 版本 |
| 方向 | 入站或出站 |
| MaliciousIP | 某个已知恶意源的 IP 地址 |
| 严重性 | 可疑恶意软件的严重性 |
| RemoteIPCountry | 远程 IP 地址的国家/地区 |
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

- [搜索日志](../../azure-monitor/log-query/log-query-overview.md)以查看详细的线路数据搜索记录。
