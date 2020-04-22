---
title: 使用 Azure 自动化跟踪更改
description: 借助更改跟踪解决方案可以确定环境中发生的软件和 Windows 服务更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682971"
---
# <a name="track-environment-changes-with-change-tracking"></a>通过更改跟踪跟踪环境更改

本文可帮助你使用更改跟踪解决方案轻松识别环境中的更改。 该解决方案跟踪以下配置更改，以帮助您确定操作问题：

- Windows 软件
- Linux 软件（软件包）
    >[!NOTE]
    >更改跟踪仅跟踪使用分发包管理器管理的软件。

- Windows 和 Linux 文件
- 窗口注册表项
- Windows 服务
- Linux 守护程序

启用解决方案后，您可以通过在自动化帐户中选择 **"配置管理**下的**更改跟踪**"来查看受监视计算机的更改摘要。

> [!NOTE]
> Azure 自动化更改跟踪跟踪虚拟机中的更改。 要跟踪 Azure 资源管理器属性更改，请参阅 Azure 资源图的[更改历史记录](../governance/resource-graph/how-to/get-resource-changes.md)。

可以查看对计算机所做的更改，然后深入了解每个事件的详细信息。 可在图表顶部找到下拉列表，根据更改类型和时间范围限制图表和详细信息。 还可以单击并拖动图表，选择自定义时间范围。 **更改类型**将是**以下值事件**之一，**守护神**，**文件**，**注册表**，**软件**， **Windows 服务**. 类别显示更改的类型，可以**添加**、**修改****或删除**。

![“更改跟踪”仪表板的图像](./media/change-tracking/change-tracking-dash01.png)

单击更改或事件将显示有关该更改的详细信息。 如示例中所示，该服务的启动类型已从“手动”更改为“自动”。

![更改跟踪详细信息的图像](./media/change-tracking/change-tracking-details.png)

对已安装的软件、Windows 服务、Windows 注册表和文件以及受监视服务器上的 Linux 守护进程所做的更改将发送到云中的 Azure 监视器服务进行处理。 逻辑应用于接收的数据，云服务则记录数据。 通过使用“更改跟踪”仪表板上的信息，可以轻松查看服务器基础结构中所做的更改。

## <a name="supported-operating-systems"></a>支持的操作系统

### <a name="windows-operating-systems"></a>Windows 操作系统

Windows 代理官方支持以下版本的 Windows 操作系统：

* Windows Server 2008 R2 或更高版本

### <a name="linux-operating-systems"></a>Linux 操作系统

以下 Linux 分发版受官方支持。 不过，Linux 代理也可在未列出的其他分发版上运行。 除非另行说明，列出每个主版本支持所有的次版本。

#### <a name="64-bit"></a>64 位

* CentOS 6 和 7
* Amazon Linux 2017.09
* Oracle Linux 6 和 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32 位

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

## <a name="limitations"></a>限制

更改跟踪解决方案当前不支持以下项：

* Windows 注册表跟踪递归
* 网络文件系统
* 不同的安装方法
* *Windows 的 **.exe**文件

其他限制：

* 未在当前实现中使用“最大文件大小”列和值****。
* 如果在 30 分钟的收集周期中收集超过 2500 个文件，解决方案性能可能会降低。
* 当网络流量较高时，更改记录最多可能需要六个小时才能显示。
* 如果在关闭计算机时修改配置，计算机可能会过帐属于前一个配置的更改。

## <a name="known-issues"></a>已知问题

更改跟踪解决方案当前遇到以下问题：

* 没有在 Windows Server 2016 Core RS3 计算机上收集修补程序更新。
* Linux 守护进程可能会显示更改的状态，即使没有变化。 这是由于`SvcRunLevels`如何捕获字段。

## <a name="network-requirements"></a>网络要求

更改跟踪特别需要以下地址。 与这些地址的通信使用端口 443。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|* .azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>通配符、递归和环境设置

借助递归可指定通配符以简化目录上的跟踪，借助环境变量可在具有多个或动态驱动器名称的环境中跟踪文件。 以下列表显示配置递归时应了解的常用信息：

* 跟踪多个文件需要使用通配符。
* 通配符仅在路径的最后一段中使用，例如，c：[文件夹\\文件]或/etc/_.conf。
* 如果环境变量的路径无效，验证将成功，但当"清单"运行时，该路径将失败。
* 设置路径时避免常规路径，因为这种类型的设置可能会导致遍历的文件夹过多。

## <a name="change-tracking-data-collection-details"></a>更改跟踪数据收集详细信息

下表显示了各种更改类型的数据收集频率。 对于每种类型，当前状态的数据快照也至少每 24 小时刷新一次：

| **更改类型** | **频率** |
| --- | --- |
| Windows 注册表 | 50 分钟 |
| Windows 文件 | 30 分钟 |
| Linux 文件 | 15 分钟 |
| Windows 服务 | 10 秒到 30 分钟</br> 默认值：30 分钟 |
| Linux 守护程序 | 5 分钟 |
| Windows 软件 | 30 分钟 |
| Linux 软件 | 5 分钟 |

下表显示了用于更改跟踪的每台计算机的跟踪项限制。

| **资源** | **限制**| **说明** |
|---|---|---|
|文件|500||
|注册表|250||
|Windows 软件|250|不包括软件修补程序|
|Linux 包|1250||
|Services|250||
|守护程序|250||

使用更改跟踪的计算机的平均日志分析数据使用量约为每月 40MB。 此值仅为近似值，且随时可能基于环境而更改。 建议监视环境以查看具体使用情况。

### <a name="windows-service-tracking"></a>Windows 服务跟踪

Windows 服务的默认收集频率为 30 分钟。 要配置频率，请转到 **"更改跟踪**"。 在“Windows 服务”选项卡上的“编辑设置”下，有一个滑块，可用于将 Windows 服务的收集频率从短短 10 秒更改为长达 30 分钟********。 请将滑块移至所需的频率，它会自动进行保存。

![Windows 服务滑块](./media/change-tracking/windowservices.png)

代理仅跟踪更改，这可以优化代理的性能。 如果服务还原到其原始状态，则设置高的阈值可能错过更改。 将频率设置为较小的值可以捕获可能会错过的更改。

> [!NOTE]
> 虽然代理可以按 10 秒的间隔跟踪更改，但数据仍要在几分钟后才能显示在门户中。 代理仍会跟踪和记录该事件内在门户中显示的更改。

### <a name="registry-key-change-tracking"></a>注册表项更改跟踪

监视注册表项更改的目的是确定第三方代码和恶意软件可以激活的扩展点。 以下列表显示预配置的注册表项的列表。 配置了这些密钥，但未启用。 若要跟踪这些注册表项，必须启用每个项。

> [!div class="mx-tdBreakAll"]
> |注册表项 | 目的 |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 监视启动时运行的脚本。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 监视关机时运行的脚本。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 监视用户登录 Windows 帐户之前加载的注册表项。 该注册表项用于在 64 位计算机上运行的 32 位程序。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 监视应用程序设置的更改。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 监视常见的自动启动条目，这些条目直接与 Windows 资源管理器挂钩，并且通常使用 Explorer.exe 在进程内运行。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 监视图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 对于在 64 位计算机上运行的 32 位程序，监视图标覆盖处理程序注册。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 用于访问当前页的文档对象模型 (DOM) 并控制导航。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | 监视 Internet Explorer 的新浏览器帮助程序对象插件。 对于在 64 位计算机上运行的 32 位程序，用于访问当前页的文档对象模型 (DOM) 并控制导航。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 对于在 64 位计算机上运行的 32 位程序，监视新的 Internet Explorer 扩展，如自定义工具菜单和自定义工具栏按钮。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 SYSTEM.INI 文件中的 [drivers] 部分。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 对于在 64 位计算机上运行的 32 位程序，监视与 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 关联的 32 位驱动程序。 类似于 SYSTEM.INI 文件中的 [drivers] 部分。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 监视已知或常用的系统 DLL 列表；该系统可以通过加入系统 DLL 的特洛伊木马程序版本来防止利用弱应用程序目录权限。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 监视可从 Winlogon（适用于 Windows 操作系统的交互式登录支持模型）接收事件通知的程序包列表。

## <a name="enable-change-tracking"></a><a name="onboard"></a>启用更改跟踪

要开始跟踪更改，您需要启用更改跟踪解决方案。 有许多方法可以登上计算机以更改跟踪。 以下是推荐和支持的方法，可用于加入此解决方案。

* [通过虚拟机](automation-onboard-solutions-from-vm.md)
* [通过浏览多个计算机](automation-onboard-solutions-from-browse.md)
* [通过自动化帐户](automation-onboard-solutions-from-automation-account.md)
* [使用 Azure 自动化 runbook](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>配置更改跟踪

要了解如何将计算机载入解决方案，请参阅[载入自动化解决方案](automation-onboard-solutions-from-automation-account.md)。 使用"更改跟踪"解决方案载入计算机后，可以配置要跟踪的项目。启用要跟踪的新文件或注册表项时，将同时启用该密钥进行更改跟踪。

若要跟踪 Windows 和 Linux 上文件中的更改，请使用文件的 MD5 哈希。 然后使用这些哈希检测自上一个库存以来是否进行了更改。

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>在 Azure 安全中心启用文件完整性监视

Azure 安全中心添加了基于 Azure 更改跟踪构建的文件完整性监视 （FIM）。 虽然 FIM 仅监视文件和注册表，但完整的更改跟踪解决方案还包括：

- 软件更改
- Windows 服务
- Linux 守护神

如果您已经启用了 FIM，并且希望尝试完整的更改跟踪解决方案，则需要执行以下步骤。 此过程不会删除设置。

> [!NOTE]
> 有关详细信息，启用完整的更改跟踪解决方案可能会导致额外费用，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

1. 通过导航到工作区并将其定位在[已安装的监视解决方案列表中](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)，删除监视解决方案。
2. 单击解决方案的名称以打开其摘要页，然后单击"删除"，详见[删除监视解决方案](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)。
3. 通过导航到自动化帐户并选择 **"配置管理**下的**更改跟踪**"来重新启用解决方案。
4. 确认工作区设置详细信息，然后单击"**启用**"。

## <a name="configure-file-content-change-tracking"></a>配置文件内容更改跟踪

您可以使用文件内容更改跟踪查看文件更改前后的内容。 此功能可用于 Windows 和 Linux 文件。 对于对文件的每次更改，文件的内容都存储在存储帐户中。 文件在更改之前和之后、内联或并排显示。 若要了解详细信息，请参阅[查看所跟踪文件的内容](change-tracking-file-contents.md)。

![查看文件中的更改](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>配置要跟踪的 Windows 注册表项

使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项：

1. 在自动化帐户中，选择 **"配置管理**下的**更改跟踪**"。 单击“编辑设置”（齿轮符号）****。
2. 在“更改跟踪”页上，选择“Windows 注册表”，然后单击“+ 添加”以添加要跟踪的新注册表项********。
3. 在“添加用于更改跟踪的 Windows 注册表”中，输入要求该项进行跟踪的信息，然后单击“保存”********。

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置。        |
|项名称     | 要跟踪的注册表项的友好名称。        |
|组     | 用于对注册表项进行逻辑分组的组名。        |
|Windows 注册表项   | 要检查的注册表项的路径。 例如：“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

## <a name="configure-file-tracking-on-windows"></a>在 Windows 上配置文件跟踪

使用以下步骤在 Windows 计算机上配置文件跟踪：

1. 在自动化帐户中，选择 **"配置管理**下的**更改跟踪**"。 单击“编辑设置”（齿轮符号）****。
2. 在“更改跟踪”页上，选择“Windows 文件”，然后单击“+ 添加”以添加要跟踪的新文件********。
3. 在“添加用于更改跟踪的 Windows 文件”中，输入要求该文件进行跟踪的信息，然后单击“保存”********。

|属性  |说明  |
|---------|---------|
|已启用     | 如果应用了该设置，则为 True，否则为 False。        |
|项名称     | 要跟踪的文件的友好名称。        |
|组     | 用于对文件进行逻辑分组的组名。        |
|输入路径     | 要检查文件的路径，例如 **，c：\temp\\\*.txt**<br>您还可以使用环境变量，如`%winDir%\System32\\\*.*`。       |
|递归     | 如果查找要跟踪的项目时使用递归，则为 True，否则为 False。        |
|上传所有设置的文件内容| 如果为 True，则在跟踪的更改上上载文件内容，否则为 False。|

## <a name="configure-file-tracking-on-linux"></a>在 Linux 上配置文件跟踪

使用以下步骤，在 Linux 计算机上配置要跟踪的文件：

1. 在自动化帐户中，选择 **"配置管理**下的**更改跟踪**"。 单击“编辑设置”（齿轮符号）****。
2. 在“更改跟踪”页上，选择“Linux 文件”，然后单击“+ 添加”以添加要跟踪的新文件********。
3. 在“添加 Linux 文件以更改跟踪”中，输入要跟踪的文件或目录的信息，然后单击“保存”********。

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置。        |
|项名称     | 要跟踪的文件的友好名称。        |
|组     | 用于对文件进行逻辑分组的组名。        |
|输入路径     | 要检查的文件路径。 例如："/etc/* .conf"       |
|路径类型     | 要跟踪的项类型，可能值为“文件”和“目录”。        |
|递归     | 在查找要跟踪的项时，确定是否使用递归。        |
|使用 Sudo     | 此设置确定在检查该项时是否使用 Sudo。         |
|链接     | 此设置确定在遍历目录时如何处理符号链接。<br> **忽略** - 忽略符号链接，不包括引用的文件/目录。<br>**追随** - 在递归期间追随符号链接，并且包含引用的文件/目录。<br>**管理** - 追随符号链接并允许更改返回的内容。     |
|上传所有设置的文件内容| 针对已跟踪的更改启用或关闭文件内容上传功能。 可用选项：“True”或“False”********。|

> [!NOTE]
> 不建议使用“管理”链接选项。 不支持文件内容检索。

## <a name="search-logs"></a>搜索日志

您可以针对日志执行各种更改记录的搜索。 打开"更改跟踪"页后，单击 **"日志分析**"，这将打开"日志"页。 下表提供了此解决方案收集的更改记录的示例日志搜索：

|查询  |说明  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 显示已设置为“自动”，但报告为“已停止”的 Windows 服务的最新库存记录<br>结果仅限于该 SoftwareName 和 Computer 的最新记录      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|显示已删除软件的更改记录|

## <a name="alert-on-changes"></a>进行更改时发出警报

更改跟踪的关键功能是警报配置状态和对混合环境配置状态的任何更改。 下面的示例显示文件**C：\windows_system32_drivers_etc_hosts**已在计算机上进行了修改。 此文件很重要，因为 Windows 使用它将主机名解析为 IP 地址。 此操作优先于 DNS，并可能导致连接问题或流量重定向到恶意或其他危险网站。

![一个图表，显示 hosts 文件的更改情况](./media/change-tracking/changes.png)

若要进一步分析此更改，请单击“Log Analytics”，转到“日志搜索”。**** 进入“日志搜索”以后，使用查询 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 搜索对 Hosts 文件所做的内容更改。 此查询查找的更改包括对特定文件的文件内容的更改，该文件的完全限定路径包含“hosts”一词。 也可将路径部分更改为完全限定的形式（例如 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`），以便请求特定的文件。

查询返回所需结果后，单击日志搜索中的 **"新建警报规则**"以打开警报创建页。 也可在 Azure 门户中通过 **Azure Monitor** 导航到该体验。 

再次检查查询并修改警报逻辑。 在本示例中，你希望在检测到更改的情况下触发警报，即使只在环境中的所有计算机上检测到一个更改。

![一个显示更改查询（用于跟踪对 hosts 文件的更改）的图像](./media/change-tracking/change-query.png)

在设置条件逻辑以后，请分配操作组，以便执行操作来响应触发的警报。 在此示例中，我设置了要发送的电子邮件以及要创建的 ITSM 票证。  也可执行许多其他的有用操作，例如触发 Azure Function、自动化 runbook、wbhook 或逻辑应用。

![一个图像，显示如何配置一个可以针对更改发出警报的操作组](./media/change-tracking/action-groups.png)

在设置所有参数和逻辑以后，可以将警报应用到环境。

### <a name="alert-suggestions"></a>警报建议

虽然对主机文件的更改发出警报是更改跟踪或清单数据警报的一个很好的应用，但还有更多用于警报的方案，包括下面部分中定义的案例及其示例查询。

|查询  |说明  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|用于跟踪对系统关键文件的更改|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|用于跟踪对关键配置文件的修改|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|用于跟踪对系统关键服务的更改|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|用于跟踪对系统关键服务的更改|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|用于需锁定软件配置的环境|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|用于查看哪些计算机安装了过时的或不符合标准的软件版本。 它报告最新报告的配置状态，而不报告更改。|
|ConfigurationChange <br>&#124;注册表项 =\\\"HKEY_LOCAL_MACHINE软件\\微软\\Windows\\当前\\版本质量兼容性"| 用于跟踪对重要的防病毒键的更改|
|ConfigurationChange <br>注册表\\项包含 #"HKEY_LOCAL_MACHINE SYSTEM\\当前控制集\\服务\\共享访问\\参数\\防火墙策略&#124;"| 用于跟踪对防火墙设置的更改|

## <a name="next-steps"></a>后续步骤

访问有关更改跟踪的教程，详细了解解决方案的用法：

> [!div class="nextstepaction"]
> [故障排除环境中的更改](automation-tutorial-troubleshoot-changes.md)

* 使用[Azure 监视器日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)查看详细的更改跟踪数据。
