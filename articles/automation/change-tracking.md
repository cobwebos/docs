---
title: Azure Automation 中的更改跟踪和清单概述
description: 更改跟踪和清单有助于识别你的环境中发生的软件和 Microsoft 服务更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 6a21effc3e567e75a8851fec35ff80dffc60a761
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787169"
---
# <a name="overview-of-change-tracking-and-inventory"></a>更改跟踪和清单概述

本文介绍如何在 Azure 自动化中更改跟踪和清点。 此功能跟踪虚拟机和服务器基础结构中的更改，以帮助你查明分发包管理器管理的软件的操作和环境问题。 更改跟踪和清单跟踪的项包括： 

- Windows 软件
- Linux 软件（包）
- Windows 和 Linux 文件
- Windows 注册表项
- Microsoft 服务
- Linux 守护程序

> [!NOTE]
> 若要跟踪 Azure 资源管理器属性更改，请参阅 Azure 资源图[更改历史记录](../governance/resource-graph/how-to/get-resource-changes.md)。

更改跟踪和清单从 Azure Monitor 中获取其数据。 连接到 Log Analytics 工作区的虚拟机使用 Log Analytics 代理收集有关已安装的软件、Microsoft 服务、Windows 注册表和文件以及受监视服务器上的任何 Linux 守护程序的更改的数据。 数据可用时，代理会将其发送到 Azure Monitor 进行处理。 Azure Monitor 将逻辑应用到接收的数据，记录该数据，并使其可用。 

更改跟踪和清单功能在 Azure 自动化中同时启用了更改跟踪和清单功能区域。 由于这两个区域都使用相同的 Log Analytics 代理，因此，在任何一个功能区域中添加 VM 的过程都是相同的。 

> [!NOTE]
> 若要使用更改跟踪和清单功能，必须在自动化帐户的同一订阅和区域中查找所有 Vm。

更改跟踪和清单当前不支持以下项：

* Windows 注册表跟踪递归
* 网络文件系统
* 不同的安装方法
* *适用于 Windows 的 **.exe**文件

其他限制：

* 未在当前实现中使用“最大文件大小”列和值****。
* 如果在30分钟的收集周期内收集超过2500个文件，则更改跟踪和清单性能可能会下降。
* 当网络流量较高时，更改记录可能需要最多六个小时才能显示。
* 如果在计算机关闭的情况下修改配置，计算机可能会发布属于以前配置的更改。

更改跟踪和清单当前遇到以下问题：

* 没有在 Windows Server 2016 Core RS3 计算机上收集修补程序更新。
* 即使未发生任何更改，Linux 守护程序也可能会显示已更改的状态。 发生此问题的原因是捕获 Azure Monitor `SvcRunLevels` [ConfigurationChange](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange)日志中的数据的方式。

## <a name="supported-operating-systems"></a>支持的操作系统

满足 Log Analytics 代理要求的所有操作系统都支持更改跟踪和清单。 正式支持的 Windows 操作系统版本为 Windows Server 2008 SP1 或更高版本以及 Windows 7 SP1 或更高版本。 还支持多个 Linux 操作系统。 请参阅[Log Analytics 代理概述](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。 

## <a name="network-requirements"></a>网络要求

更改跟踪和清点具体要求下表中列出的网络地址。 与这些地址的通信使用端口443。

|Azure Public  |Azure Government   |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|* .azure-automation.net | *.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>更改跟踪和清单用户界面

使用 Azure 门户中更改跟踪和清单查看所监视的计算机的更改摘要。 此功能可通过在自动化帐户中选择 "**配置管理**" 下的 "**更改跟踪**" 和 "**清单**" 的 "添加 vm" 选项之一。  

![更改跟踪仪表板](./media/change-tracking/change-tracking-dash01.png)

下拉面板顶部提供了下拉列表，可根据更改类型和时间范围限制更改跟踪图表和详细信息。 还可以单击并拖动图表，选择自定义时间范围。 

您可以单击更改或事件以显示其详细信息。 可用的更改类型为：

* 事件
* 守护程序
* 文件
* 注册表
* 软件
* Microsoft 服务

可以添加、修改或删除每项更改。 在下面的示例中，你可以看到服务的启动类型更改从 "手动" 更改为 "自动"。

![更改跟踪详细信息](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>文件更改跟踪

为了跟踪 Windows 和 Linux 上文件的更改，更改跟踪和清单使用文件的 MD5 哈希。 此功能使用哈希来检测自上次清点后是否进行了更改。

## <a name="tracking-of-file-content-changes"></a>文件内容更改的跟踪

更改跟踪和清单允许在文件更改前后查看 Windows 或 Linux 文件的内容。 对于文件的每个更改，更改跟踪和清单将文件内容存储在[Azure 存储帐户](../storage/common/storage-create-storage-account.md)中。 跟踪文件时，可以在更改前后查看其内容。 您可以直接查看内容或并排查看内容。 

![查看文件中的更改](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>跟踪注册表项

更改跟踪和清单允许监视注册表项的更改。 通过监视，可以确定第三方代码和恶意软件可以激活的扩展点。 下表列出了预配置（但未启用）的注册表项。 若要跟踪这些密钥，必须启用每个密钥。

> [!div class="mx-tdBreakAll"]
> |注册表项 | 目标 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 监视直接挂接到 Windows 资源管理器并在进程中与**资源管理器**运行的常见自动启动条目。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 监视启动时运行的脚本。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 监视关机时运行的脚本。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 监视用户登录 Windows 帐户之前加载的密钥。 密钥用于在64位计算机上运行的32位应用程序。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 监视应用程序设置的更改。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 监视直接挂接到 Windows 资源管理器并在进程中与**资源管理器**运行的常见自动启动条目。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 监视直接挂接到 Windows 资源管理器并在进程中与**资源管理器**运行的常见自动启动条目。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 监视图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 监视在64位计算机上运行的32位应用程序的图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 用于访问当前页的文档对象模型 (DOM) 并控制导航。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 用于访问当前页的文档对象模型（DOM），并控制在64位计算机上运行的32位应用程序的导航。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 监视新的 Internet Explorer 扩展，如自定义工具菜单和在64位计算机上运行的32位应用程序的自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、、.msadpcm、msadpcm、msgsm610 和 vidc 关联的32位驱动程序。 类似于**system.web**文件中的 [驱动程序] 部分。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、、.msadpcm、msadpcm、msgsm610、vidc、、、以及64位计算机上运行的32位应用程序相关联的32位驱动程序。 类似于**system.web**文件中的 [驱动程序] 部分。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 监视已知或常用系统 Dll 的列表。 此系统可通过删除系统 Dll 的特洛伊木马版本来防止人们利用弱应用程序目录权限。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 监视可从**winlogon**（Windows 的交互式登录支持模型）接收事件通知的程序包列表。

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>支持 Azure 安全中心中的文件完整性监视

更改跟踪和库存利用了[Azure 安全中心文件完整性监视（FIM）](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)。 虽然 FIM 仅监视文件和注册表，但完整的更改跟踪和清单功能还包括以下内容的跟踪：

- 软件更改
- Microsoft 服务
- Linux 守护程序

## <a name="recursion-support"></a>递归支持

更改跟踪和清单支持递归，这允许你指定通配符来简化跨目录的跟踪。 递归还提供了环境变量，使你能够跨具有多个或动态驱动器名称的环境跟踪文件。 下面的列表包含在配置递归时应了解的常见信息：

* 跟踪多个文件需要使用通配符。
* 只能在路径的最后一段中使用通配符，例如， **c:\folder\\file*** 或 **/etc/*.**。
* 如果环境变量具有无效路径，则验证成功，但在执行过程中路径会失败。
* 设置路径时应避免通用路径名，因为此类型的设置可能会导致遍历太多文件夹。

## <a name="change-tracking-and-inventory-data-collection"></a>更改跟踪和清单数据收集

下表显示更改跟踪和清单支持的更改类型的数据收集频率。 对于每种类型，当前状态的数据快照也至少每24小时刷新一次。

| **更改类型** | **频率** |
| --- | --- |
| Windows 注册表 | 50 分钟 |
| Windows 文件 | 30 分钟 |
| Linux 文件 | 15 分钟 |
| Microsoft 服务 | 10 秒到 30 分钟</br> 默认值：30 分钟 |
| Linux 守护程序 | 5 分钟 |
| Windows 软件 | 30 分钟 |
| Linux 软件 | 5 分钟 |

下表显示了每台计算机的跟踪项限制更改跟踪和清单。

| **资源** | **限制** |
|---|---|---|
|文件|500|
|注册表|250|
|Windows 软件（不包括修补程序） |250|
|Linux 包|1250|
|服务|250|
|守护程序|250|

使用更改跟踪和清单的计算机的平均 Log Analytics 数据使用率大约为每月 40 MB，具体取决于你的环境。 使用 "Log Analytics" 工作区的 "使用情况和估计成本" 功能，可以在使用情况图表中查看更改跟踪和库存的数据引入。 你可以使用此数据视图来评估你的数据使用情况，并确定它如何影响你的帐单。 请参阅[了解你的使用情况和估计成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)。  

### <a name="microsoft-service-data"></a>Microsoft 服务数据

Microsoft 服务的默认收集频率为30分钟。 你可以使用 "**编辑设置**" 下 " **Microsoft 服务**" 选项卡上的滑块来配置频率。 

![Microsoft 服务滑块](./media/change-tracking/windowservices.png)

为优化性能，Log Analytics 代理只跟踪更改。 如果服务恢复到其原始状态，则设置较高的阈值可能会丢失更改。 将频率设置为较小的值，可以捕获可能丢失的更改。

> [!NOTE]
> 尽管代理可以将更改跟踪到10秒的间隔，但仍需要几分钟时间才能在 Azure 门户中显示数据。 仍会跟踪和记录在门户中显示的时间内发生的更改。

## <a name="support-for-alerts-on-configuration-state"></a>支持有关配置状态的警报

更改跟踪和清单的一项重要功能是对混合环境的配置状态发生更改时发出警报。 许多有用的操作可触发，以响应警报，例如对 Azure 函数、自动化 runbook、webhook 等执行的操作。 对计算机的**C:\windows\system32\drivers\etc\hosts**文件进行更改时发出警报是更改跟踪和清单数据的一个良好的警报。 还有更多用于报警的方案，包括下表中定义的查询方案。 

|查询  |说明  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|用于跟踪对系统关键文件的更改。|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|用于跟踪对密钥配置文件所做的修改。|
|ConfigurationChange <br>&#124;，其中 ConfigChangeType = = "Microsoft services" 和 SvcName 包含 "w3svc" 且 SvcState = = "已停止"|用于跟踪对系统关键服务所做的更改。|
|ConfigurationChange <br>&#124;，其中 ConfigChangeType = = "守护程序" 和 SvcName 包含 "ssh"，SvcState！ = "运行"|用于跟踪对系统关键服务所做的更改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|对于需要锁定的软件配置的环境很有用。|
|ConfigurationData <br>&#124;，其中 SoftwareName 包含 "Monitoring Agent" 和 CurrentVersion！ = "8.0.11081.0"|用于查看哪些计算机安装了过时或不相容的软件版本。 此查询报告上次报告的配置状态，但不报告更改。|
|ConfigurationChange <br>&#124;，其中 RegistryKey = = @ "\\HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion QualityCompat"| 用于跟踪对关键防病毒密钥的更改。|
|ConfigurationChange <br>&#124; 其中，RegistryKey 包含 @ "\\HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\win2k3\\Parameters FirewallPolicy"| 用于跟踪对防火墙设置所做的更改。|

## <a name="next-steps"></a>后续步骤

* 若要在 runbook 中使用更改跟踪和清单，请参阅[管理更改跟踪和清单](change-tracking-file-contents.md)。
* 若要解决与更改跟踪和清单相关的错误，请参阅[更改跟踪和清单故障排除](automation-tutorial-troubleshoot-changes.md)。
* 使用[Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)查看详细的更改跟踪数据。
