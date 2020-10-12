---
title: Azure 自动化 - 更改跟踪和库存概述
description: 本文介绍了更改跟踪和清单功能，可帮助你在环境中识别软件和 Microsoft 服务的更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2fe92942e263cf53b9827ccbcb13a2d7bafc367c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88511042"
---
# <a name="change-tracking-and-inventory-overview"></a>更改跟踪和库存概述

本文介绍了 Azure 自动化中的“更改跟踪和库存”功能。 此功能跟踪虚拟机和服务器基础结构中的更改，帮助查明由分发包管理器管理的软件的操作和环境问题。 更改跟踪和库存功能跟踪的项包括： 

- Windows 软件
- Linux 软件（包）
- Windows 和 Linux 文件
- Windows 注册表项
- Microsoft 服务
- Linux 守护程序

> [!NOTE]
> 若要跟踪 Azure 资源管理器的属性更改，请参阅 Azure Resource Graph [更改历史记录](../governance/resource-graph/how-to/get-resource-changes.md)。

更改跟踪和库存从 Azure Monitor 中获取其数据。 连接到 Log Analytics 工作区的虚拟机使用 Log Analytics 代理收集有关受监视服务器上的已安装软件、Microsoft 服务、Windows 注册表和文件和 Linux 守护程序的更改的数据。 数据可用时，代理会将其发送到 Azure Monitor 进行处理。 Azure Monitor 将逻辑应用于接收的数据，记录这些数据并使其可用。

> [!NOTE]
> 更改跟踪和清单要求将 Log Analytics 工作区链接到自动化帐户。 有关受支持区域的明确列表，请参阅 [Azure 工作区映射](./how-to/region-mappings.md)。 区域映射不会影响在单独的区域中管理自动化帐户内 VM 的功能。

更改跟踪和库存当前不支持以下项：

- Windows 注册表跟踪递归
- 网络文件系统
- 不同的安装方法
- *适用于 Windows 的 .exe 文件

其他限制：

- 未在当前实现中使用“最大文件大小”列和值。
- 如果在30分钟的收集周期内收集超过2500个文件，更改跟踪和清单性能可能会下降。
- 网络流量较高时，更改记录可能需要最多六个小时才能显示。
- 如果在计算机关闭的情况下修改配置，计算机可能会发布属于以前配置的更改。

更改跟踪和库存当前遇到以下问题：

- 不会在 Windows Server 2016 Core RS3 计算机上收集修补程序更新。

- 即使未发生任何更改，Linux 守护程序也可能会显示已更改的状态。 发生此问题的原因 `SvcRunLevels` 是捕获 Azure Monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) 日志中的数据的方式。

## <a name="supported-operating-systems"></a>支持的操作系统

满足 Log Analytics 代理要求的所有操作系统都支持更改跟踪和库存。 官方操作系统版本为 Windows Server 2008 SP1 或更高版本以及 Windows 7 SP1 或更高版本。 许多 Linux 操作系统也支持此功能。 有关支持的操作系统的列表，请参阅 [Log Analytics 代理概述](../azure-monitor/platform/log-analytics-agent.md)。

若要了解 TLS 1.2 的客户端要求，请参阅[强制 Azure 自动化执行 TLS 1.2](automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

## <a name="network-requirements"></a>网络要求

更改跟踪和库存特别要求使用下表中列出的网络地址。 与以下地址的通信要使用端口 443。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|\* .azure-automation.net | *.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>更改跟踪和库存用户界面

在 Azure 门户中使用更改跟踪和库存功能来查看受监视计算机的更改摘要。 通过在自动化帐户的“配置管理”下为“更改跟踪”或“库存” 选择其中一个添加 VM 选项，可以使用该功能。  

![“更改跟踪”仪表板](./media/change-tracking/change-tracking-dash01.png)

可在仪表板顶部找到下拉列表，根据更改类型和时间范围限制更改跟踪图表和详细信息。 还可以单击并拖动图表，选择自定义时间范围。 

可以单击更改或事件以显示其详细信息。 可用的更改类型包括：

- 事件
- 守护程序
- 文件
- 注册表
- 软件
- Microsoft 服务

可以添加、修改或删除每个更改。 下面的示例演示了服务的启动类型在手动和自动之间的更改。

![更改跟踪和清单详细信息](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>Azure 安全中心中的 FIM 支持

更改跟踪和库存功能可利用 [Azure 安全中心文件完整性监视 (FIM)](../security-center/security-center-file-integrity-monitoring.md)。 虽然 FIM 仅监视文件和注册表，但完整的更改跟踪和库存功能还包括以下内容的跟踪：

- 软件更改
- Microsoft 服务
- Linux 守护程序

> [!NOTE]
> 启用完整的更改跟踪和库存功能可能会产生额外的费用。 请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。 可以从 Azure 门户中提供 [的已安装监视解决方案列表](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) 中删除 FIM。 请参阅 [删除监视解决方案](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)。

## <a name="tracking-of-file-changes"></a>跟踪文件更改

若要跟踪 Windows 和 Linux 上文件中的更改，更改跟踪和库存功能会使用文件的 MD5 哈希。 此功能使用哈希算法来检测自上次清点后是否进行了更改。

## <a name="tracking-of-file-content-changes"></a>跟踪文件内容更改

更改跟踪和清单允许您查看 Windows 或 Linux 文件的内容。 对于文件的每个更改，更改跟踪和库存功能将文件内容存储在 [Azure 存储帐户](../storage/common/storage-account-create.md)中。 跟踪文件时，可以在更改前后查看其内容。 文件内容可以是内联的，也可以并排查看。 

![查看文件中的更改](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>跟踪注册表项

更改跟踪和清单允许监视 Windows 注册表项的更改。 此监视操作可以确定第三方代码和恶意软件可以激活的扩展点。 下表列出了预配置（但未启用）的注册表项。 若要跟踪这些注册表项，必须将它们都启用。

> [!div class="mx-tdBreakAll"]
> |注册表项 | 目的 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 监视启动时运行的脚本。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 监视关机时运行的脚本。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 监视用户登录 Windows 帐户之前加载的注册表项。 该注册表项用于在 64 位计算机上运行的 32 位应用程序。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 监视应用程序设置的更改。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 监视直接挂钩到 Windows 资源管理器中的上下文菜单处理程序，并且通常与 **explorer.exe**在进程内运行。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 监视直接挂钩到 Windows 资源管理器并在 **explorer.exe**中运行的复制挂钩处理程序。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 监视图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 监视在 64 位计算机上运行的 32 位应用程序的图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 用于访问当前页的文档对象模型 (DOM) 并控制导航。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 对于在 64 位计算机上运行的 32 位应用程序，用于访问当前页的文档对象模型 (DOM) 并控制导航。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 对于在 64 位计算机上运行的 32 位应用程序，监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 system.ini 文件中的 [drivers] 部分。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 对于在 64 位计算机上运行的 32 位应用程序，监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 system.ini 文件中的 [drivers] 部分。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 监视已知或常用的系统 DLL 列表。 监视可通过删除系统 Dll 的特洛伊木马版本来防止人们利用弱应用程序目录权限。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 监视可从 winlogon.exe（适用于 Windows 的交互式登录支持模型）接收事件通知的包列表。

## <a name="recursion-support"></a>递归支持

更改跟踪和库存功能支持递归，这样你就可以指定通配符来简化跨目录的跟踪。 递归还提供了环境变量，使你能够跨具有多个或动态驱动器名称的环境跟踪文件。 下面的列表包含在配置递归时应了解的常见信息：

- 跟踪多个文件需要使用通配符。

- 只能在文件路径的最后一段中使用通配符，例如， **c:\folder \\ file*** 或 **/etc/*.**

- 如果环境变量具有无效路径，验证会成功，但在执行过程中路径会失败。

- 设置路径时应避免常规路径名，因为此类型的设置可能会导致遍历太多文件夹。

## <a name="change-tracking-and-inventory-data-collection"></a>更改跟踪和库存数据收集

下表显示更改跟踪和库存功能支持的更改类型的数据收集频率。 对于每种类型，当前状态的数据快照也至少每 24 小时刷新一次。

| **更改类型** | **频率** |
| --- | --- |
| Windows 注册表 | 50 分钟 |
| Windows 文件 | 30 分钟 |
| Linux 文件 | 15 分钟 |
| Microsoft 服务 | 10 秒到 30 分钟</br> 默认值：30 分钟 |
| Linux 守护程序 | 5 分钟 |
| Windows 软件 | 30 分钟 |
| Linux 软件 | 5 分钟 |

下表显示了用于更改跟踪和库存功能的每台计算机的跟踪项限制。

| **资源** | **限制** |
|---|---|---|
|文件|500|
|注册表|250|
|Windows 软件（不包括修补程序） |250|
|Linux 包|1250|
|服务|250|
|守护程序|250|

使用更改跟踪和库存的计算机的平均 Log Analytics 数据用量为每月大约 40 MB，具体取决于环境。 使用 "Log Analytics" 工作区的 "使用情况和估计成本" 功能，可以在使用情况图表中查看更改跟踪和库存的数据引入。 使用此数据视图可以评估你的数据使用情况，并确定它如何影响你的帐单。 请参阅[了解自己的使用情况和预估成本](../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs)

### <a name="microsoft-service-data"></a>Microsoft 服务数据

Windows 服务的默认收集频率为 30 分钟。 可以使用“编辑设置”下“Microsoft 服务”选项卡上的滑块配置频率。

![Microsoft 服务滑块](./media/change-tracking/windowservices.png)

为了优化性能，Log Analytics 代理只跟踪更改。 如果服务还原到其原始状态，则设置高的阈值可能错过更改。 将频率设置为较小的值可以捕获可能会错过的更改。

> [!NOTE]
> 虽然代理可以按 10 秒的间隔跟踪更改，但数据仍要在几分钟后才能显示在 Azure 门户中。 代理仍会跟踪和记录在门户中显示期间发生的更改。

## <a name="support-for-alerts-on-configuration-state"></a>支持有关配置状态的警报

更改跟踪和库存的主要功能是就混合环境的配置状态更改发出警报。 可以触发许多有用的操作来响应警报，例如对 Azure 函数、自动化 runbook、webhook 等触发的操作。 对计算机的 **c:\windows\system32\drivers\etc\hosts** 文件进行更改时发出警报是更改跟踪和清单数据的一个良好的警报。 还有更多的警报方案，包括下表中定义的查询方案。

|查询  |说明  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|用于跟踪对系统关键文件的更改。|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|用于跟踪对关键配置文件的修改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|用于跟踪对系统关键服务的更改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|用于跟踪对系统关键服务的更改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|用于需锁定软件配置的环境。|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|用于查看哪些计算机安装了过时的或不符合标准的软件版本。 该查询会报告最新报告的配置状态，而不报告更改。|
|ConfigurationChange <br>&#124; where RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 用于跟踪对重要的防病毒密钥的更改。|
|ConfigurationChange <br>&#124; where RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| 用于跟踪对防火墙设置的更改。|

## <a name="next-steps"></a>后续步骤

- 若要从自动化帐户启用此功能，请参阅[从自动化帐户启用更改跟踪和库存](automation-enable-changes-from-auto-acct.md)。

- 若要通过浏览 Azure 门户来启用此功能，请参阅 [从 Azure 门户启用更改跟踪和清单](automation-enable-changes-from-browse.md)。

- 若要从 runbook 启用此功能，请参阅[从 runbook 启用更改跟踪和库存](automation-enable-changes-from-runbook.md)。

- 若要从 Azure VM 启用此功能，请参阅[从 Azure VM 启用更改跟踪和库存](automation-enable-changes-from-vm.md)。
