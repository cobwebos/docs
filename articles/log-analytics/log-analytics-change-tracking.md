---
title: "使用 Azure Log Analytics 跟踪更改 | Microsoft 文档"
description: "Log Analytics 中的更改跟踪解决方案可帮助用户确定在环境中发生的软件和 Windows 服务更改。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 528b569ff9ffb3659e9210ea70e3aa06921cfe0d
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2017
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>使用更改跟踪解决方案跟踪环境中的软件更改

![“更改跟踪”符号](./media/log-analytics-change-tracking/change-tracking-symbol.png)

本文可帮助你使用 Log Analytics 中的更改跟踪解决方案，以便轻松标识环境中的更改。 该解决方案会跟踪对 Windows 和 Linux 软件、Windows 文件和注册表项、Windows 服务和 Linux 守护程序的更改。 标识配置更改可帮助你查明操作问题。

安装该解决方案，以更新已安装代理的类型。 在监视服务器上读取对已安装软件、Windows 服务和 Linux 守护程序的更改。 然后，将数据发送到云中的 Log Analytics 服务进行处理。 逻辑应用于接收的数据，云服务记录数据。 通过使用“更改跟踪”仪表板上的信息，可以轻松看到服务器基础结构中所做的更改。

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案
使用以下信息安装和配置解决方案。

* 在想要监视更改的每台计算机上，都必须装有 [Windows](log-analytics-windows-agents.md)、[Operations Manager](log-analytics-om-agents.md) 或 [Linux](log-analytics-linux-agents.md) 代理。
* 将更改跟踪解决方案从 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview) 添加到 OMS 工作区。 或者，你可以使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中的信息来添加解决方案。 无需进一步配置。

### <a name="configure-linux-files-to-track"></a>配置要跟踪的 Linux 文件
使用以下步骤，配置要在 Linux 计算机上跟踪的文件。

1. 在 OMS 门户中，单击“设置”（齿轮符号）。
2. 在“设置”页上，依次单击“数据”和“Linux 文件跟踪”。
3. 在“Linux 文件更改跟踪”下，键入整个路径（包括要跟踪的文件的文件名），然后单击“添加”符号。 例如："/etc/*.conf"
4. 单击“保存”。  

> [!NOTE]
> Linux 文件跟踪具有一些附加功能，包括目录跟踪、通过目录递归和通配符跟踪。

### <a name="configure-windows-files-to-track"></a>配置要跟踪的 Windows 文件
使用以下步骤，配置要在 Windows 计算机上跟踪的文件。

1. 在 OMS 门户中，单击“设置”（齿轮符号）。
2. 在“设置”页上，依次单击“数据”和“Windows 文件跟踪”。
3. 在“Windows 文件更改跟踪”下，键入整个路径（包括要跟踪的文件的文件名），并单击“添加”符号。 例如：C:\Program Files (x86)\Internet Explorer\iexplore.exe 或 C:\Windows\System32\drivers\etc\hosts。
4. 单击“保存” 。  
   ![Windows 文件更改跟踪](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>配置要跟踪的 Windows 注册表项
使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项。

1. 在 OMS 门户中，单击“设置”（齿轮符号）。
2. 在“设置”页上，依次单击“数据”和“Windows 注册表跟踪”。
3. 在“Windows 注册表更改跟踪”下，键入要跟踪的完整注册表项，并单击“添加”符号。
4. 单击“保存” 。  
   ![Windows 注册表更改跟踪](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Linux 文件集合属性的说明
1. **类型**
   * **文件**（报表文件元数据 - 大小、修改日期、哈希，等等）
   * **目录**（报表目录元数据 - 大小、修改日期，等等）
2. **链接**（处理对其他文件或目录的 Linux 符号链接引用）
   * 忽略（在递归期间忽略符号链接以便不包括引用的文件/目录）
   * **追随**（在递归期间追随符号链接以便同时包括引用的文件/目录）
   * **管理**（追随符号链接并修改返回内容的处置）

   > [!NOTE]   
   > 不建议使用“管理”链接选项。 不支持文件内容检索。

3. **递归**（递归通过文件夹级别并跟踪满足路径声明的所有文件）
4. **Sudo**（实现对需要 sudo 权限的文件或目录的访问）

### <a name="limitations"></a>限制
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

### <a name="known-issues"></a>已知问题
更改跟踪解决方案当前遇到以下问题：
* 没有为 Windows 10 创意者更新和 Windows Server 2016 Core RS3 计算机收集修补程序更新。

## <a name="change-tracking-data-collection-details"></a>“更改跟踪”数据收集详细信息
“更改跟踪”使用已启用的代理来收集软件清单和 Windows 服务元数据。

下表显示“更改跟踪”的数据收集方法和其他数据收集方式的详细信息。

| 平台 | 直接代理 | Operations Manager 代理 | Linux 代理 | Azure 存储空间 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows 和 Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | 耗时 5 分钟到 50 分钟，具体取决于更改类型。 有关详细信息，请查阅下表。 |


下表显示了各种更改类型的数据收集频率。

| **更改类型** | **频率** | **代理在****发现****差异时是否发送差异？** |
| --- | --- | --- |
| Windows 注册表 | 50 分钟 | 否 |
| Windows 文件 | 30 分钟 | 是的。 如果 24 小时内没有更改，则会发送快照。 |
| Linux 文件 | 15 分钟 | 是的。 如果 24 小时内没有更改，则会发送快照。 |
| Windows 服务 | 30 分钟 | 是的，当发现更改时，每 30 分钟发送一次。 每 24 小时发送一次快照，无论是否有更改。 因此，即使没有更改，也会发送快照。 |
| Linux 守护程序 | 5 分钟 | 是的。 如果 24 小时内没有更改，则会发送快照。 |
| Windows 软件 | 30 分钟 | 是的，当发现更改时，每 30 分钟发送一次。 每 24 小时发送一次快照，无论是否有更改。 因此，即使没有更改，也会发送快照。 |
| Linux 软件 | 5 分钟 | 是的。 如果 24 小时内没有更改，则会发送快照。 |

### <a name="registry-key-change-tracking"></a>注册表项更改跟踪

Log Analytics 通过“更改跟踪”解决方案执行 Windows 注册表监视和跟踪。 监视注册表项更改的目的是确定第三方代码和恶意软件可以激活的可扩展性点。 以下列表显示了解决方案默认跟踪的注册表项及其跟踪原因。

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - 监视启动时运行的脚本。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - 监视关机时运行的脚本。
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - 监视用户登录 Windows 帐户之前加载的注册表项。 该注册表项用于在 64 位计算机上运行的 32 位程序。
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components
    - 监视应用程序设置的更改。
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - 监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - 监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - 监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - 监视图标覆盖处理程序注册。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - 对于在 64 位计算机上运行的 32 位程序，监视图标覆盖处理程序注册。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - 监视 Internet Explorer 的新浏览器帮助程序对象插件。 用于访问当前页的文档对象模型 (DOM) 并控制导航。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - 监视 Internet Explorer 的新浏览器帮助程序对象插件。 对于在 64 位计算机上运行的 32 位程序，该插件可用于访问当前页的文档对象模型 (DOM) 并控制导航。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - 监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - 对于在 64 位计算机上运行的 32 位程序，监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - 监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 SYSTEM.INI 文件中的 [drivers] 部分。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - 对于在 64 位计算机上运行的 32 位程序，监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 SYSTEM.INI 文件中的 [drivers] 部分。
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - 监视已知或常用的系统 DLL 列表；该系统可以通过加入系统 DLL 的特洛伊木马程序版本来防止利用弱应用程序目录权限。
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - 监视可从 Winlogon（适用于 Windows 操作系统的交互式登录支持模型）接收事件通知的程序包列表。


## <a name="use-change-tracking"></a>使用“更改跟踪”
安装解决方案后，可以通过使用 OMS 中“**概述**”页上的“**更改跟踪**”磁贴，查看受监视服务器的更改摘要。

![“更改跟踪”磁贴的图像](./media/log-analytics-change-tracking/change-tracking-tile.png)

可以查看基础结构的更改，并向下钻取以下类别的详细信息：

* 软件和 Windows 服务的配置类型所做的更改
* 应用程序的软件更改和单个服务器的更新
* 每个应用程序的软件更改总数
* Linux 包
* 单个服务器的 Windows 服务更改
* Linux 守护程序更改

![“更改跟踪”仪表板的图像](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![“更改跟踪”仪表板的图像](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>查看任何更改类型的更改
1. 在“**概述**”页上单击“**更改跟踪**”磁贴。
2. 在“**更改跟踪**”仪表板上，查看其中一个更改类型边栏选项卡中的摘要信息，并在“**日志搜索**”页中单击一条信息，以查看其详细信息。
3. 在任何日志搜索页上，都可以按时间、详细结果和日志搜索历史记录查看结果。 还可以按方面进行筛选以缩减搜索结果。

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)可查看详细的更改跟踪数据。
