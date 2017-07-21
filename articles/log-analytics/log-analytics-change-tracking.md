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
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 7e0fa9a83c3c83145a4813422bf73a0e711d0ecc
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# 使用更改跟踪解决方案跟踪环境中的软件更改
<a id="track-software-changes-in-your-environment-with-the-change-tracking-solution" class="xliff"></a>

![“更改跟踪”符号](./media/log-analytics-change-tracking/change-tracking-symbol.png)

本文可帮助你使用 Log Analytics 中的更改跟踪解决方案，以便轻松标识环境中的更改。 该解决方案会跟踪对 Windows 和 Linux 软件、Windows 文件和注册表项、Windows 服务和 Linux 守护程序的更改。 标识配置更改可帮助你查明操作问题。

安装该解决方案，以更新已安装代理的类型。 系统会读取对受监视服务器上的已安装软件、Windows 服务和 Linux 守护程序的更改，然后将数据发送到云中的 Log Analytics 服务进行处理。 逻辑应用于接收的数据，云服务记录数据。 通过使用“更改跟踪”仪表板上的信息，你可以轻松看到服务器基础结构中所做的更改。

## 安装和配置解决方案
<a id="installing-and-configuring-the-solution" class="xliff"></a>
使用以下信息安装和配置解决方案。

* 在想要监视更改的每台计算机上，都必须装有 [Windows](log-analytics-windows-agents.md)、[Operations Manager](log-analytics-om-agents.md) 或 [Linux](log-analytics-linux-agents.md) 代理。
* 从 [Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview)或使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将更改跟踪解决方案添加到 OMS 工作区。  无需进一步的配置。

### 配置要跟踪的 Linux 文件
<a id="configure-linux-files-to-track" class="xliff"></a>
使用以下步骤，配置要在 Linux 计算机上跟踪的文件。

1. 在 OMS 门户中，单击“设置”（齿轮符号）。
2. 在“设置”页上，依次单击“数据”和“Linux 文件跟踪”。
3. 在“Linux 文件更改跟踪”下，键入整个路径（包括要跟踪的文件的文件名），然后单击“添加”符号。 例如："/etc/*.conf"
4. 单击“保存” 。  
  
> [!NOTE]
> Linux 文件跟踪具有其他功能，包括目录跟踪、递归通过目录和通配符跟踪。

### 配置要跟踪的 Windows 文件
<a id="configure-windows-files-to-track" class="xliff"></a>
使用以下步骤，配置要在 Windows 计算机上跟踪的文件。

1. 在 OMS 门户中，单击“设置”（齿轮符号）。
2. 在“设置”页上，依次单击“数据”和“Windows 文件跟踪”。
3. 在“Windows 文件更改跟踪”下，键入整个路径（包括要跟踪的文件的文件名），然后单击“添加”符号。 例如：C:\Program Files (x86)\Internet Explorer\iexplore.exe 或 C:\Windows\System32\drivers\etc\hosts。
4. 单击“保存” 。  
   ![Windows 文件更改跟踪](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### 配置要跟踪的 Windows 注册表项
<a id="configure-windows-registry-keys-to-track" class="xliff"></a>
使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项。

1. 在 OMS 门户中，单击“设置”（齿轮符号）。
2. 在“设置”页上，依次单击“数据”和“Windows 注册表跟踪”。
3. 在“Windows 注册表更改跟踪”下，键入要跟踪的完整注册表项，然后单击“添加”符号。
4. 单击“保存” 。  
   ![Windows 注册表更改跟踪](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### Linux 文件集合属性的说明
<a id="explanation-of-linux-file-collection-properties" class="xliff"></a>
1. **类型**
   * **文件**（报表文件元数据 - 大小、修改日期、哈希，等等）
   * **目录**（报表目录元数据 - 大小、修改日期，等等）
2. **链接**（处理对其他文件或目录的 Linux 符号链接引用）
   * **忽略**（在递归期间忽略符号链接以便不包括引用的文件/目录）
   * **追随**（在递归期间追随符号链接以便同时包括引用的文件/目录）
   * **管理**（追随符号链接并修改返回内容的处置） 
   
   > [!NOTE]   
   > 建议不要使用“管理”链接选项，因为当前不支持文件内容检索。
   
3. **递归**（递归通过文件夹级别并跟踪满足路径声明的所有文件）
4. **Sudo**（实现对需要 sudo 权限的文件或目录的访问）

### 限制
<a id="limitations" class="xliff"></a>
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

## “更改跟踪”数据收集详细信息
<a id="change-tracking-data-collection-details" class="xliff"></a>
“更改跟踪”使用已启用的代理来收集软件清单和 Windows 服务元数据。

下表显示“更改跟踪”的数据收集方法和其他数据收集方式的详细信息。

| 平台 | 直接代理 | SCOM 代理 | Linux 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows 和 Linux |![是](./media/log-analytics-change-tracking/oms-bullet-green.png) |![是](./media/log-analytics-change-tracking/oms-bullet-green.png) |![是](./media/log-analytics-change-tracking/oms-bullet-green.png) |![否](./media/log-analytics-change-tracking/oms-bullet-red.png) |![否](./media/log-analytics-change-tracking/oms-bullet-red.png) |![是](./media/log-analytics-change-tracking/oms-bullet-green.png) | 耗时 5 分钟到 50 分钟，具体取决于更改类型。 有关详细信息，请参阅下文。 |


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

### 注册表项更改跟踪
<a id="registry-key-change-tracking" class="xliff"></a>

Log Analytics 通过“更改跟踪”解决方案执行 Windows 注册表监视和跟踪。 监视注册表项更改的目的是确定第三方代码和恶意软件可以激活的可扩展性点。 以下列表显示了解决方案默认跟踪的注册表项及其跟踪原因。

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - 监视启动时运行的脚本。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - 监视关机时运行的脚本。
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - 对于在 64 位计算机上运行的 32 位程序，监视用户登录 Windows 帐户之前加载的注册表项。
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
    - 监视适用于 Internet Explorer 的新浏览器帮助程序对象插件，该插件可用于访问当前页面的文档对象模型 (DOM) 并控制导航。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - 对于在 64 位计算机上运行的 32 位程序，监视适用于 Internet Explorer 的新浏览器帮助程序对象插件，该插件可用于访问当前页面的文档对象模型 (DOM) 并控制导航。
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


## 使用“更改跟踪”
<a id="use-change-tracking" class="xliff"></a>
安装解决方案后，你可以通过使用 OMS 中“**概述**”页上的“**更改跟踪**”磁贴，查看受监视服务器的更改摘要。

![“更改跟踪”磁贴的图像](./media/log-analytics-change-tracking/change-tracking-tile.png)

你可以查看基础结构的更改，然后向下钻取以下类别的详细信息：

* 软件和 Windows 服务的配置类型所做的更改
* 应用程序的软件更改和单个服务器的更新
* 每个应用程序的软件更改总数
* Linux 包
* 单个服务器的 Windows 服务更改
* Linux 守护程序更改

![“更改跟踪”仪表板的图像](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![“更改跟踪”仪表板的图像](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### 查看任何更改类型的更改
<a id="to-view-changes-for-any-change-type" class="xliff"></a>
1. 在“**概述**”页上单击“**更改跟踪**”磁贴。
2. 在“**更改跟踪**”仪表板上，查看其中一个更改类型边栏选项卡中的摘要信息，然后在“**日志搜索**”页中单击一条信息，以查看其详细信息。
3. 在任何日志搜索页上，都可以按时间、详细结果和日志搜索历史记录查看结果。 还可以按方面进行筛选以缩减搜索结果。

## 后续步骤
<a id="next-steps" class="xliff"></a>
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)可查看详细的更改跟踪数据。

