---
title: 在 Azure 自动化中排查 Azure VM 上的更改问题 | Microsoft Docs
description: 本文介绍如何排查 Azure VM 上的更改问题。
services: automation
ms.subservice: change-inventory-management
keywords: 更改, 跟踪, 更改跟踪, 清单, 自动化
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 211b34b4424fa5bc9b82dc1cc2a2da574ffc5d96
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743690"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>排查 Azure VM 上的更改问题

本教程介绍如何排查 Azure 虚拟机上的更改错误。 启用更改跟踪和清单后，可以在计算机上跟踪对软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的更改。
确定这些配置更改有助于查明环境中的操作问题。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为 VM 启用更改跟踪和清单
> * 在更改日志中搜索停止的服务
> * 配置更改跟踪
> * 启用活动日志连接
> * 触发事件
> * 查看更改
> * 配置警报

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 runbook 和观察程序任务。
* 要启用该功能的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

就本教程来说，首先需要启用更改跟踪和清单。 如果以前启用了该功能，则无需执行此步骤。

>[!NOTE]
>如果字段灰显，则意味着已为 VM 启用其他自动化功能，因此必须使用同一工作区和自动化帐户。

1. 选择“虚拟机”，然后从列表中选择一个 VM。
2. 在左侧菜单上的“操作”下选择“清单”。  “清单”页随即打开。

    ![启用更改](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. 选择 [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区。 此工作区将收集由更改跟踪和清单等功能生成的数据。 工作区提供了一个位置来查看和分析来自多个数据源的数据。

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. 选择要使用的自动化帐户。

5. 配置部署位置。

5. 单击“启用”为 VM 部署该功能。 

在设置期间，将使用适用于 Windows 的 Log Analytics 代理和[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)来预配 VM。 启用更改跟踪和清单最多可能需要 15 分钟。 在此期间，不应关闭浏览器窗口。

启用该功能后，VM 中有关已安装软件和更改的信息会流向 Azure Monitor 日志。
这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>在 Azure Monitor 日志中使用更改跟踪和清单

更改跟踪和清单会生成发送到 Azure Monitor 日志的日志数据。 若要通过运行查询来搜索日志，请在“更改跟踪”页面顶部选择“Log Analytics”。 更改跟踪数据存储在 `ConfigurationChange` 类型下。

以下示例 Log Analytics 查询将返回所有已停止的 Windows 服务。

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

若要详细了解如何在 Azure Monitor 日志中运行和搜索日志文件，请参阅 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)。

## <a name="configure-change-tracking"></a>配置更改跟踪

使用更改跟踪时，可以通过使用 VM 上“更改跟踪”页面顶部的“编辑设置”，来选择要收集和跟踪的文件和注册表项。 可以在“工作区配置”页上添加要跟踪的 Windows 注册表项、Windows 文件或 Linux 文件。

> [!NOTE]
> 更改跟踪和清单使用相同的集合设置，而设置在工作区级别配置。

### <a name="add-a-windows-registry-key"></a>添加 Windows 注册表项

1. 在“Windows 注册表”选项卡上，选择“添加”。 

1. 在“添加要进行更改跟踪的 Windows 注册表”页面上，输入要跟踪的项的信息，然后单击“保存”。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 确定是否应用了设置        |
    |项名称     | 要跟踪的文件的友好名称        |
    |组     | 一个组名，用于对文件进行逻辑分组        |
    |Windows 注册表项   | 用于查看文件的路径，例如：“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="add-a-windows-file"></a>添加 Windows 文件

1. 在“Windows 文件”选项卡上，选择“添加”。 

1. 在“添加要进行更改跟踪的 Windows 文件”页面上，输入要跟踪的文件或目录的信息，然后单击“保存”。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 确定是否应用了设置        |
    |项名称     | 要跟踪的文件的友好名称        |
    |组     | 一个组名，用于对文件进行逻辑分组        |
    |输入路径     | 用于查看文件的路径，例如：“c:\temp\\\*.txt”<br>还可以使用环境变量，例如“%winDir%\System32\\\*.*”         |
    |递归     | 在查找要跟踪的项时，确定是否使用递归。        |
    |上传所有设置的文件内容| 针对已跟踪的更改启用或关闭文件内容上传功能。 可用选项：**True** 或 **False**。|

### <a name="add-a-linux-file"></a>添加 Linux 文件

1. 在“Linux 文件”选项卡上，选择“添加”。 

1. 在“添加要进行更改跟踪的 Linux 文件”页面上，输入要跟踪的文件或目录的信息，然后单击“保存”。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 确定是否应用了设置        |
    |项名称     | 要跟踪的文件的友好名称        |
    |组     | 一个组名，用于对文件进行逻辑分组        |
    |输入路径     | 用于查看文件的路径，例如“/etc/*.conf”       |
    |路径类型     | 要跟踪的项的类型，可能值为“文件”和“目录”        |
    |递归     | 在查找要跟踪的项时，确定是否使用递归。        |
    |使用 Sudo     | 此设置确定在检查该项时是否使用 Sudo。         |
    |链接     | 此设置确定在遍历目录时如何处理符号链接。<br> **忽略** - 忽略符号链接，不包括引用的文件/目录<br>**追随** - 在递归期间追随符号链接，并且包括引用的文件/目录<br>**管理** - 追随符号链接并允许修改返回内容的处置方式      |
    |上传所有设置的文件内容| 针对已跟踪的更改启用或关闭文件内容上传功能。 可用选项：True 或 False。|

   > [!NOTE]
   > 不建议将“链接”属性的值设置为“管理” 。 不支持文件内容检索。

## <a name="enable-activity-log-connection"></a>启用活动日志连接

1. 在 VM 的“更改跟踪”页中，选择“管理活动日志连接”。 

2. 在“Azure 活动日志”页上，单击“连接”，将更改跟踪和清单连接到 VM 的 Azure 活动日志。

3. 导航到 VM 的“概述”页，然后选择“停止”以停止 VM。 

4. 出现提示时，选择“是”即可停止 VM。 

5. 将 VM 解除分配后，选择“启动”以重启 VM。 停止和启动 VM 时，会在活动日志中记录一个事件。 

## <a name="view-changes"></a>查看更改

1. 导航回到“更改跟踪”页面并在页面底部选择“事件”选项卡。 

2. 一段时间后，更改跟踪事件会显示在图表和表格中。 此图表显示了一段时间内发生的更改。 顶部的折线图显示 Azure 活动日志事件。 条形图的每一行代表不同类型的可跟踪更改。 这些类型是 Linux 守护程序、文件、Windows 注册表项、软件和 Windows 服务。 更改选项卡展示已显示的更改的详细信息，最先显示最近的更改。

    ![在门户中查看事件](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. 请注意，系统进行了多次更改，其中包括服务和软件的更改。 可以使用页面顶部的筛选器，按“更改类型”或时间范围筛选结果。

    ![VM 的更改列表](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. 选择一个“WindowsServices”更改。 此选择将打开“更改详细信息”页，其中会显示更改详细信息以及更改前后的值。 在此实例中，软件保护服务已停止。

    ![在门户中查看更改详细信息](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>配置警报

查看 Azure 门户中的更改可能会很有帮助，但能够在发生更改（例如服务停止）时收到警报会更有益。 让我们为已停止的服务添加警报。 

1. 在 Azure 门户中，转到“监视”。 

2. 在“共享服务”下选择“警报”，并单击“+ 新建警报规则”。

3. 单击“选择”以选择资源。 

4. 在“选择资源”页上，从“按资源类型筛选”下拉菜单中选择“Log Analytics” 。 

5. 选择 Log Analytics 工作区，然后单击“完成”。

    ![选择资源](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. 单击“添加条件”。

7. 在“配置信号逻辑”页上的表中选择“自定义日志搜索”。 

8. 在搜索查询文本框中输入以下查询：

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    此查询返回在指定时间范围内已停止 W3SVC 服务的计算机。

9. 在“警报逻辑”下，  输入 0 作为“阈值”。 完成后，单击“完成”。

    ![配置信号逻辑](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. 在“操作组”下，选择“新建” 。 操作组是可以在多个警报中使用的一组操作。 这些操作可能包括但不限于电子邮件通知、Runbook、Webhook 以及其他操作。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

11. 在“警报详细信息”下，输入警报的名称和说明。 

12. 将“严重性”设置为“信息(严重性 2)”、“警告(严重性 1)”或“关键(严重性 0)”。

13. 在“操作组名称”框中输入警报的名称和一个短名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

14. 对于“操作”，输入操作的名称，例如“电子邮件管理员” 。 

15. 对于“操作类型”，选择“电子邮件/短信/推送/语音” 。 

16. 对于“详细信息”，选择“编辑详细信息” 。

    ![添加操作组](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. 在“电子邮件/短信/推送/语音”窗格中，输入名称，选中“电子邮件”复选框，然后输入有效的电子邮件地址。 完成后，在窗格中单击“确定”，然后在“添加操作组”页上单击“确定” 。

18. 若要自定义警报电子邮件的主题，请选择“自定义操作”。 

19. 对于“创建规则”，选择“电子邮件主题”，然后选择“创建警报规则”。 此警报会指出更新部署成功的时间以及哪些计算机是该更新部署运行的一部分。 下图是 W3SVC 服务停止时收到的示例电子邮件。

    ![电子邮件](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 为 VM 启用更改跟踪和清单
> * 在更改日志中搜索停止的服务
> * 配置更改跟踪
> * 启用活动日志连接
> * 触发事件
> * 查看更改
> * 配置警报

继续阅读更改跟踪和清单功能的概述可以了解其详细信息。

> [!div class="nextstepaction"]
> [更改跟踪和清单概述](automation-change-tracking.md)