---
title: 使用 Azure 自动化跟踪更改
description: 借助更改跟踪解决方案可以确定在环境中发生的软件和 Windows 服务更改。
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0716466eab69ef2a2439a533cd51e92307bd056
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>使用更改跟踪解决方案跟踪环境中的更改

本文可帮助你使用更改跟踪解决方案轻松识别环境中的更改。 该解决方案会跟踪对 Windows 和 Linux 软件、Windows 文件和注册表项、Windows 服务和 Linux 守护程序的更改。 标识配置更改可帮助你查明操作问题。

系统会读取对受监视服务器上的已安装软件、Windows 服务、Windows 注册表与文件以及 Linux 守护程序的更改，然后将数据发送到云中的 Log Analytics 服务进行处理。 逻辑应用于接收的数据，云服务记录数据。 通过使用“更改跟踪”仪表板上的信息，可以轻松看到服务器基础结构中所做的更改。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和清单

若要开始跟踪更改，需要为自动化帐户中启用更改跟踪和清单解决方案。

1. 在 Azure 门户中，导航到自动化帐户。
1. 选择**更改跟踪**下的**配置**。
1. 选择现有的 Log Analytics 工作区，或创建一个工作区。

这会启用自动化帐户的解决方案。 启用解决方案最多可能需要 15 分钟。 启用解决方案后，蓝色横幅会通知你。 导航回到“更改跟踪”页以管理解决方案。

## <a name="configuring-change-tracking-and-inventory"></a>配置更改跟踪和清单

若要了解如何到板载计算机添加到解决方案中访问：[载入自动化解决方案](automation-onboard-solutions-from-automation-account.md)。 有了向更改跟踪和清单解决方案登记的计算机后，便可以配置要跟踪的项目了。启用的新文件或注册表项以跟踪时，它会启用更改跟踪和清单。

若要跟踪 Windows 和 Linux 上文件的更改，请使用文件的 MD5 哈希。 然后使用这些哈希检测自上一个清单以来是否进行了更改。

### <a name="configure-linux-files-to-track"></a>配置要跟踪的 Linux 文件

使用以下步骤，配置要在 Linux 计算机上跟踪的文件。

1. 在自动化帐户中，选择**更改跟踪**下的**配置管理**。 单击**编辑设置**（齿轮符号）。
2. 上**更改跟踪**页上，选择 **Linux 文件**，然后单击 **+ 添加**以添加要跟踪的新文件。
3. 在“添加用于更改跟踪的 Linux 文件”中，输入要求该文件或目录进行跟踪的信息，然后单击“保存”。

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置。        |
|项目名称     | 要跟踪的文件的友好名称。        |
|组     | 一个组名，用于对文件进行逻辑分组。        |
|输入路径     | 要检查的文件路径。 例如："/etc/* .conf"       |
|路径类型     | 要跟踪的项的类型，可能值为“文件”和“目录”。        |
|递归     | 确定在查找要跟踪的项时是否使用递归。        |
|使用 Sudo     | 此设置决定了在查找该项时是否使用 Sudo。         |
|链接     | 此设置决定了在遍历目录时如何处理符号链接。<br> **忽略** - 忽略符号链接，不包括引用的文件/目录。<br>**追随** - 在递归期间追随符号链接，并且包括引用的文件/目录。<br>**管理** - 追随符号链接并允许修改返回内容的处置方式。     |

> [!NOTE]
> 不建议使用“管理”链接选项。 不支持文件内容检索。

### <a name="configure-windows-files-to-track"></a>配置要跟踪的 Windows 文件

使用以下步骤，配置要在 Windows 计算机上跟踪的文件。

1. 在自动化帐户中，选择**更改跟踪**下的**配置管理**。 单击**编辑设置**（齿轮符号）。
2. 在**更改跟踪**页上，选择 **Windows 文件**，然后单击 **+ 添加**以添加要跟踪的新文件。
3. 在“添加用于更改跟踪的 Windows 文件”中，输入要求该文件进行跟踪的信息，然后单击“保存”。

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置。        |
|项目名称     | 要跟踪的文件的友好名称。        |
|组     | 一个组名，用于对文件进行逻辑分组。        |
|输入路径     | 用于查看文件的路径，例如“c:\temp\myfile.txt”       |

### <a name="configure-windows-registry-keys-to-track"></a>配置要跟踪的 Windows 注册表项

使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项。

1. 在自动化帐户中，选择**更改跟踪**下的**配置管理**。 单击**编辑设置**（齿轮符号）。
2. 在**更改跟踪**页上，选择 **Windows 注册表**，然后单击 **+ 添加**以添加要跟踪的新注册表项。
3. 在“添加用于更改跟踪的 Windows 注册表”中，输入要求该项进行跟踪的信息，然后单击“保存”。

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置。        |
|项目名称     | 要跟踪的文件的友好名称。        |
|组     | 一个组名，用于对文件进行逻辑分组。        |
|Windows 注册表项   | 要检查的文件路径。 例如：“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

## <a name="limitations"></a>限制

更改跟踪解决方案当前不支持以下项：

* 用于 Windows 文件跟踪的文件夹（目录）
* Windows 文件跟踪递归
* Windows 文件跟踪通配符
* 路径变量
* 网络文件系统
* 文件内容

其他限制：

* “最大文件大小”列和值在当前实现中未使用。
* 如果在 30 分钟收集周期内收集 2500 多个文件，则解决方案性能可能会下降。
* 当网络流量较高时，更改记录可能需要最多六个小时才能显示。
* 如果在计算机关闭的情况下修改配置，计算机可能会发布属于以前配置的文件更改。

## <a name="known-issues"></a>已知问题

更改跟踪解决方案当前遇到以下问题：

* 没有为 Windows 10 创意者更新和 Windows Server 2016 Core RS3 计算机收集修补程序更新。

## <a name="change-tracking-data-collection-details"></a>“更改跟踪”数据收集详细信息

下表显示了各种更改类型的数据收集频率。 对于每个类型的当前状态的数据快照也会刷新至少每隔 24 小时：

| **更改类型** | **频率** |
| --- | --- |
| Windows 注册表 | 50 分钟 |
| Windows 文件 | 30 分钟 |
| Linux 文件 | 15 分钟 |
| Windows 服务 | 30 分钟 |
| Linux 守护程序 | 5 分钟 |
| Windows 软件 | 30 分钟 |
| Linux 软件 | 5 分钟 |

### <a name="registry-key-change-tracking"></a>注册表项更改跟踪

监视注册表项更改的目的是确定第三方代码和恶意软件可以激活的可扩展性点。 以下列表显示预配置的注册表项的列表。 配置了这些密钥，但未启用。 若要跟踪这些注册表项，必须启用每个项。

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;监视启动时运行的脚本。     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;监视关机时运行的脚本。     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;监视用户登录 Windows 帐户之前加载的注册表项。 该注册表项用于在 64 位计算机上运行的 32 位程序。    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;监视应用程序设置的更改。     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视图标覆盖处理程序注册。|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;对于在 64 位计算机上运行的 32 位程序，监视图标覆盖处理程序注册。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视 Internet Explorer 的新浏览器帮助程序对象插件。 用于访问当前页的文档对象模型 (DOM) 并控制导航。|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视 Internet Explorer 的新浏览器帮助程序对象插件。 对于在 64 位计算机上运行的 32 位程序，该插件可用于访问当前页的文档对象模型 (DOM) 并控制导航。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;对于在 64 位计算机上运行的 32 位程序，监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 SYSTEM.INI 文件中的 [drivers] 部分。|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;对于在 64 位计算机上运行的 32 位程序，监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 SYSTEM.INI 文件中的 [drivers] 部分。|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视已知或常用的系统 DLL 列表；该系统可以通过加入系统 DLL 的特洛伊木马程序版本来防止利用弱应用程序目录权限。|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;监视可从 Winlogon（适用于 Windows 操作系统的交互式登录支持模型）接收事件通知的程序包列表。|

## <a name="use-change-tracking"></a>使用“更改跟踪”

启用解决方案后，可以查看的更改摘要受监视的计算机的通过选择**更改跟踪**下**配置管理**自动化帐户中。

可以查看对更改你的计算机，然后为每个事件的深入详细信息。 在顶部的图表以限制的图表和基于更改类型和时间范围的详细的信息，下拉列表确定均可用。 还可以单击并拖动图，以选择的自定义时间范围。

![“更改跟踪”仪表板的图像](./media/automation-change-tracking/change-tracking-dash01.png)

单击在更改或事件将显示有关所做的更改的详细信息。 如示例中所示，该服务的启动类型已从手动更改为自动。

![更改跟踪详细信息的图像](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>搜索日志

除了门户中提供的详细信息以外，还可以针对日志执行搜索。 在打开“更改跟踪”页的情况下，单击“Log Analytics”打开“日志搜索”页。

### <a name="sample-queries"></a>示例查询

下表提供了此解决方案收集的更改记录的示例日志搜索。

|查询  |说明  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 显示已设置为自动，但被报告为正在停止 Windows 服务的最新的清单记录<br>结果仅限于该 SoftwareName 和计算机的最新记录      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|显示已删除软件的更改记录|

## <a name="next-steps"></a>后续步骤

访问有关更改跟踪的教程，详细了解解决方案的用法：

> [!div class="nextstepaction"]
> [排查环境中的更改错误](automation-tutorial-troubleshoot-changes.md)

* 使用 [Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-searches.md)可查看详细的更改跟踪数据。
