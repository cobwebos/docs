---
title: 排查 Azure 虚拟机上的更改错误 | Microsoft Docs
description: 使用更改跟踪排查 Azure 虚拟机上的更改错误。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: 更改, 跟踪, 自动化
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 77dda5f113a10f0bfb59457b1059563c58db0dde
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816925"
---
# <a name="troubleshoot-changes-in-your-environment"></a>排查环境中的更改错误

本教程介绍如何排查 Azure 虚拟机上的更改错误。 启用更改跟踪即可在计算机上跟踪对软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的更改。
确定这些配置更改有助于查明环境中的操作问题。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 载入适用于更改跟踪和清单的 VM
> * 在更改日志中搜索停止的服务
> * 配置更改跟踪
> * 启用活动日志连接
> * 触发事件
> * 查看更改
> * 配置警报

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 Runbook 和观察程序任务。
* 要载入的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和清单

就本教程来说，首先需为 VM 启用更改跟踪和清单。 如果以前已为 VM 启用其他自动化解决方案，则此步骤不是必需的。

1. 在左侧菜单上选择“虚拟机”，然后从列表中选择一个 VM。
1. 在左侧菜单的“操作”部分单击“清单”。 此时会打开“更改跟踪”页。

![启用更改](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) 此时会打开“更改跟踪”屏幕。 配置要使用的位置、Log Analytics 工作区和自动化帐户，然后单击“启用”。 如果这些字段灰显，则意味着已为 VM 启用其他自动化解决方案，因此必须使用同一工作区和自动化帐户。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区用于收集由功能和服务（如清单）生成的数据。
工作区提供了一个位置来查看和分析来自多个数据源的数据。

在载入期间，VM 预配了 Microsoft Monitoring Agent (MMA) 和混合辅助角色。
此代理用于与 VM 通信并获取有关已安装软件的信息。

启用解决方案最多可能需要 15 分钟。 在此期间，不应关闭浏览器窗口。
启用该解决方案后，VM 中有关已安装软件和更改的信息会流向 Azure Monitor 日志。
这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>使用 Azure Monitor 日志中的更改跟踪

更改跟踪生成发送到 Azure Monitor 日志的日志数据。
若要通过运行查询来搜索日志，请选择“更改跟踪”窗口顶部的“Log Analytics”。
更改跟踪数据存储在 **ConfigurationChange** 类型下。
以下示例 Log Analytics 查询返回所有已停止的 Windows 服务。

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

若要详细了解如何在 Azure Monitor 日志中运行和搜索日志文件，请参阅 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)。

## <a name="configure-change-tracking"></a>配置更改跟踪

可以使用更改跟踪来跟踪 VM 上的配置更改。 以下步骤演示了如何配置注册表项和文件的跟踪。

若要选择要收集和跟踪的文件和注册表项，请选择“更改跟踪”页顶部的“编辑设置”。

> [!NOTE]
> 清单和更改跟踪使用相同的集合设置，而设置在工作区级别配置。

如接下来的三部分所述，在“工作区配置”窗口中，添加要跟踪的 Windows 注册表项、Windows 文件或 Linux 文件。

### <a name="add-a-windows-registry-key"></a>添加 Windows 注册表项

1. 在“Windows 注册表”选项卡上，选择“添加”。
    “添加 Windows 注册表以跟踪更改”窗口随即打开。

1. 在“添加用于更改跟踪的 Windows 注册表”中，输入要求该项进行跟踪的信息，然后单击“保存”

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置        |
|项名称     | 要跟踪的文件的友好名称        |
|组     | 一个组名，用于对文件进行逻辑分组        |
|Windows 注册表项   | 用于查看文件的路径，例如：“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="add-a-windows-file"></a>添加 Windows 文件

1. 在“Windows 文件”选项卡上，选择“添加”。 “添加 Windows 文件以跟踪更改”窗口随即打开。

1. 在“添加用于更改跟踪的 Windows 文件”中，输入要求该文件或目录进行跟踪的信息，然后单击“保存”

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置        |
|项名称     | 要跟踪的文件的友好名称        |
|组     | 一个组名，用于对文件进行逻辑分组        |
|输入路径     | 用于查看文件的路径，例如：“c:\temp\\\*.txt”<br>还可以使用环境变量，例如“%winDir%\System32\\\*.*”         |
|递归     | 在查找要跟踪的项时，确定是否使用递归。        |
|上传所有设置的文件内容| 针对已跟踪的更改启用或关闭文件内容上传功能。 可用选项：**True** 或 **False**。|

### <a name="add-a-linux-file"></a>添加 Linux 文件

1. 在“Linux 文件”选项卡上，选择“添加”。 “添加 Linux 文件以跟踪更改”窗口随即打开。

1. 在“添加用于更改跟踪的 Linux 文件”中，输入要求该文件或目录进行跟踪的信息，然后单击“保存”

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置        |
|项名称     | 要跟踪的文件的友好名称        |
|组     | 一个组名，用于对文件进行逻辑分组        |
|输入路径     | 用于查看文件的路径，例如“/etc/*.conf”       |
|路径类型     | 要跟踪的项的类型，可能值为“文件”和“目录”        |
|递归     | 在查找要跟踪的项时，确定是否使用递归。        |
|使用 Sudo     | 此设置确定在检查该项时是否使用 Sudo。         |
|链接     | 此设置确定在遍历目录时如何处理符号链接。<br> **忽略** - 忽略符号链接，不包括引用的文件/目录<br>**追随** - 在递归期间追随符号链接，并且包括引用的文件/目录<br>**管理** - 追随符号链接并允许修改返回内容的处置方式      |
|上传所有设置的文件内容| 针对已跟踪的更改启用或关闭文件内容上传功能。 可用选项：**True** 或 **False**。|

   > [!NOTE]
   > 不建议使用“管理”链接选项。 不支持文件内容检索。

## <a name="enable-activity-log-connection"></a>启用活动日志连接

在 VM 的“更改跟踪”页中，选择“管理活动日志连接”。 此任务打开“Azure 活动日志”页。 选择“连接”，将更改跟踪连接到 VM 的 Azure 活动日志。

启用此设置后，导航到 VM 的“概览”页，然后选择“停止”以停止 VM。 出现提示时，选择“是”即可停止 VM。 将 VM 解除分配以后，请选择“启动”以重启 VM。

停止和启动 VM 时，会在活动日志中记录一个事件。 导航回到“更改跟踪”页。 选择页面底部的“事件”选项卡。 一段时间后，事件会显示在图表和表中。 与前面的步骤一样，可以选择每个事件来查看其详细信息。

![在门户中查看更改详细信息](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>查看更改

启用更改跟踪和清单解决方案以后，即可在“更改跟踪”页查看结果。

在 VM 中的“操作”下选择“更改跟踪”。

![显示对 VM 的更改列表的屏幕截图](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

此图表显示了一段时间内发生的更改。
添加活动日志连接以后，顶部的线形图会显示 Azure 活动日志事件。
条形图的每一行代表不同类型的可跟踪更改。
这些类型是 Linux 守护程序、文件、Windows 注册表项、软件、Windows 服务。
“更改”选项卡显示在可视化效果中显示的更改的详细信息，按更改发生时间以降序方式排列（最近发生的排在最前面）。
选择“事件”选项卡时，此表会显示连接的活动日志事件及其相应的详细信息，最近发生的排在最前面。

可以在结果中看到对系统进行了多项更改，包括对服务和软件的更改。 可以使用页面顶部的筛选器，按“更改类型”或时间范围筛选结果。

选择 **WindowsServices** 更改会打开“更改详细信息”窗口。 “更改详细信息”窗口显示更改详细信息以及更改前后的值。 在此实例中，软件保护服务已停止。

![在门户中查看更改详细信息](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>配置警报

查看 Azure 门户中的更改可能会很有帮助，但能够在发生更改（例如服务停止）时收到警报会更有益。

若要为已停止的服务添加警报，请在 Azure 门户中转至“监视”。 在“共享服务”下，选择“警报”，并单击“+ 新建警报规则”

单击“选择”以选择资源。 在“选择资源”页上，从“按资源类型筛选”下拉列表中选择“Log Analytics”。 选择 Log Analytics 工作区，然后选择“完成”。

![选择资源](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

在“配置信号逻辑”页上单击“添加条件”，在表中选择“自定义日志搜索”。 在“搜索查询”文本框中输入以下查询：

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

此查询返回在指定时间范围内已停止 W3SVC 服务的计算机。

在“警报逻辑”下，输入 **0** 作为“阈值”。 完成后，选择“完成”。

![配置信号逻辑](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

在“操作组”下，选择“新建”。 操作组是可以在多个警报中使用的一组操作。 这些操作可能包括但不限于电子邮件通知、Runbook、Webhook 以及其他操作。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

在“警报详细信息”下，输入警报的名称和说明。 将“严重性”设置为“信息(严重性 2)”、“警告(严重性 1)”或“关键(严重性 0)”。

在“操作组名称”框中输入警报的名称和一个短名称。 使用此组发送通知时，短名称用来代替完整的操作组名称。

在“操作”下输入操作的名称，例如“电子邮件管理员”。 在“操作类型”下，选择“电子邮件/短信/推送/语音”。 在“详细信息”下，选择“编辑详细信息”。

![添加操作组](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

在“电子邮件/短信/推送/语音”窗格中，输入一个名称。 选中“电子邮件”复选框，然后输入有效的电子邮件地址。 单击“电子邮件/短信/推送/语音”页上的“确定”，然后单击“添加操作组”页上的“确定”。

若要自定义警报电子邮件的主题，请在“创建规则”下的“自定义操作”下选择“电子邮件主题”。 完成后，请选择“创建警报规则”。 此警报会指出更新部署成功的时间以及哪些计算机是该更新部署运行的一部分。

下图是 W3SVC 服务停止时收到的示例电子邮件。

![电子邮件](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 载入适用于更改跟踪和清单的 VM
> * 在更改日志中搜索停止的服务
> * 配置更改跟踪
> * 启用活动日志连接
> * 触发事件
> * 查看更改
> * 配置警报

继续阅读更改跟踪和清单解决方案的概述可以了解其详细信息。

> [!div class="nextstepaction"]
> [变更管理和清单解决方案](automation-change-tracking.md)

