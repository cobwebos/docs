---
title: "排查 Azure 虚拟机上的更改错误 | Microsoft Docs"
description: "使用更改跟踪排查 Azure 虚拟机上的更改错误。"
services: automation
keywords: "更改, 跟踪, 自动化"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: hero-article
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: ae9ac6baaaeca418fcd3478145c50d1fa7917d7e
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2017
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

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 Runbook 和观察程序任务。
* 要载入的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和清单

就本教程来说，首先需为 VM 启用更改跟踪和清单。 如果以前已为 VM 启用其他自动化解决方案，则此步骤不是必需的。

1. 在左侧菜单上选择“虚拟机”，然后从列表中选择一个 VM。
1. 在左侧菜单的“操作”部分单击“清单”。 此时会打开“启用更改跟踪和清单”页。

执行验证是为了确定是否为该 VM 启用了更改跟踪和清单。
验证包括检查 Log Analytics 工作区和链接的自动化帐户，以及解决方案是否在工作区中。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区用于收集由功能和服务（如清单）生成的数据。
工作区提供了一个位置来查看和分析来自多个数据源的数据。

验证过程还会检查 VM 是否预配了 Microsoft Monitoring Agent (MMA) 和混合辅助角色。
此代理用于与 VM 通信并获取有关已安装软件的信息。
验证过程还会检查 VM 是否预配了 Microsoft Monitoring Agent (MMA) 和自动化混合 Runbook 辅助角色。

如果不满足这些先决条件，则会显示一个横幅，其中提供了启用该解决方案的选项。

![更改跟踪和清单载入配置横幅](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

若要启用该解决方案，请单击横幅。
如果在验证后发现缺少下列任何先决条件，则会自动添加这些条件：

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区
* [自动化](./automation-offering-get-started.md)
* VM 上已启用[混合 runbook 辅助角色](./automation-hybrid-runbook-worker.md)

此时会打开“更改跟踪和清单”屏幕。 配置要使用的位置、Log Analytics 工作区和自动化帐户，然后单击“启用”。 如果这些字段灰显，则意味着已为 VM 启用其他自动化解决方案，因此必须使用同一工作区和自动化帐户。

![“启用更改跟踪解决方案”窗口](./media/automation-tutorial-troubleshoot-changes/installed-software-enable.png)

启用解决方案最多可能需要 15 分钟。 在此期间，不应关闭浏览器窗口。
启用该解决方案后，VM 中有关已安装软件和更改的信息会流向 Log Analytics。
这些数据需花费 30 分钟到 6 小时的时间才能用于分析。


## <a name="using-change-tracking-in-log-analytics"></a>在 Log Analytics 中使用更改跟踪

更改跟踪生成发送到 Log Analytics 的日志数据。 若要通过运行查询来搜索日志，请选择“更改跟踪”窗口顶部的“Log Analytics”。
更改跟踪数据存储在 **ConfigurationChange** 类型下。 以下示例 Log Analytics 查询返回所有已停止的 Windows 服务。

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

若要详细了解如何在 Log Analytics 中运行和搜索日志文件，请参阅 [Azure Log Analytics](https://docs.loganalytics.io/index)。

## <a name="configure-change-tracking"></a>配置更改跟踪

可以使用更改跟踪来跟踪 VM 上的配置更改。 以下步骤演示了如何配置注册表项和文件的跟踪。
 
若要选择要收集和跟踪的文件和注册表项，请选择“更改跟踪”页顶部的“编辑设置”。

> [!NOTE]
> 清单和更改跟踪使用相同的集合设置，而设置在工作区级别配置。

如接下来的三部分所述，在“工作区配置”窗口中，添加要跟踪的 Windows 注册表项、Windows 文件或 Linux 文件。

### <a name="add-a-windows-registry-key"></a>添加 Windows 注册表项

1. 在“Windows 注册表”选项卡上，选择“添加”。
    “添加 Windows 注册表以跟踪更改”窗口随即打开。

   ![更改跟踪添加注册表](./media/automation-vm-change-tracking/change-add-registry.png)

2. 在“已启用”下，选择“True”。
3. 在“项名称”框中，输入一个友好名称。
4. （可选）在“组”框中，输入一个组名称。
5. 在“Windows 注册表项”框中，输入要跟踪的注册表项的名称。
6. 选择“保存”。

### <a name="add-a-windows-file"></a>添加 Windows 文件

1. 在“Windows 文件”选项卡上，选择“添加”。 “添加 Windows 文件以跟踪更改”窗口随即打开。

   ![更改跟踪添加 Windows 文件](./media/automation-vm-change-tracking/change-add-win-file.png)

2. 在“已启用”下，选择“True”。
3. 在“项名称”框中，输入一个友好名称。
4. （可选）在“组”框中，输入一个组名称。
5. 在“输入路径”框中，输入要跟踪的文件的完整路径和文件名。
6. 选择“保存”。

### <a name="add-a-linux-file"></a>添加 Linux 文件

1. 在“Linux 文件”选项卡上，选择“添加”。 “添加 Linux 文件以跟踪更改”窗口随即打开。

   ![更改跟踪添加 Linux 文件](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. 在“已启用”下，选择“True”。
3. 在“项名称”框中，输入一个友好名称。
4. （可选）在“组”框中，输入一个组名称。
5. 在“输入路径”框中，输入要跟踪的文件的完整路径和文件名。
6. 在“路径类型”框中，选择“文件”或“目录”。
7. 在“递归”下，若要跟踪指定路径以及其下所有文件和路径的更改，请选择“打开”。 若要仅跟踪指定路径或文件，选择“关闭”。
8. 在“使用 Sudo”下，若要跟踪需要 `sudo` 命令进行访问的文件，请选择“打开”。 否则，选择“关闭。
9. 选择“保存”。

## <a name="enable-activity-log-connection"></a>启用活动日志连接

在 VM 的“更改跟踪”页中，选择“管理活动日志连接”。 此任务打开“Azure 活动日志”页。 选择“连接”，将更改跟踪连接到 VM 的 Azure 活动日志。

启用此设置后，导航到 VM 的“概览”页，然后选择“停止”以停止 VM。 出现提示时，选择“是”即可停止 VM。 将 VM 解除分配以后，请选择“启动”以重启 VM。

停止和启动 VM 时，会在活动日志中记录一个事件。 导航回到“更改跟踪”页。 选择页面底部的“事件”选项卡。 一段时间后，事件会显示在图表和表中。 与前面的步骤一样，可以选择每个事件来查看其详细信息。

![在门户中查看更改详细信息](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>查看更改

启用更改跟踪和清单解决方案以后，即可在“更改跟踪”页查看结果。

在 VM 中的“操作”下选择“更改跟踪”。

![在 OMS 经典门户中创建警报](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

此图表显示了一段时间内发生的更改。
添加活动日志连接以后，顶部的线形图会显示 Azure 活动日志事件。
条形图的每一行代表不同类型的可跟踪更改。
这些类型是 Linux 守护程序、文件、Windows 注册表项、软件、Windows 服务。
“更改”选项卡显示在可视化效果中显示的更改的详细信息，按更改发生时间以降序方式排列（最近发生的排在最前面）。
选择“事件”选项卡时，此表会显示连接的活动日志事件及其相应的详细信息，最近发生的排在最前面。

可以在结果中看到对系统进行了多项更改，包括对服务和软件的更改。 可以使用页面顶部的筛选器，按“更改类型”或时间范围筛选结果。

选择 **WindowsServices** 更改会打开“更改详细信息”窗口。 “更改详细信息”窗口显示更改详细信息以及更改前后的值。 在此实例中，软件保护服务已停止。

![在门户中查看更改详细信息](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 载入适用于更改跟踪和清单的 VM
> * 在更改日志中搜索停止的服务
> * 配置更改跟踪
> * 启用活动日志连接
> * 触发事件
> * 查看更改

继续阅读更改跟踪和清单解决方案的概述可以了解其详细信息。

> [!div class="nextstepaction"]
> [变更管理和清单解决方案](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)
