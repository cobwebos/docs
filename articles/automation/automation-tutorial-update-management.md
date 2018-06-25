---
title: 管理 Azure Windows VM 的更新和修补程序
description: 本文概述了如何使用 Azure 自动化更新管理来管理 Azure Windows VM 的更新和修补程序。
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 92258ce7ea39a06f2af85efd9174b1b200710566
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216960"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>使用 Azure 自动化管理 Windows 更新

可以使用更新管理解决方案来管理虚拟机的更新和修补程序。 本教程介绍了如何快速评估可用更新的状态、计划所需更新的安装、查看部署结果，以及创建警报来验证更新是否已成功应用。

有关定价信息，请参阅[更新管理自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 载入用于更新管理的 VM
> * 查看更新评估
> * 配置警报
> * 计划更新部署
> * 查看部署结果

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 VIsual Studio 订户的每月 Azure 额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或者注册一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Azure 自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 Runbook 和观察程序任务。
* 要载入的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-update-management"></a>启用更新管理

就本教程来说，首先请在 VM 上启用更新管理：

1. 在 Azure 门户的左菜单中，选择“虚拟机”。 从列表中选择 VM。
2. 在 VM 页的“操作”下选择“更新管理”。 “启用更新管理”窗格随即打开。

执行验证，确定是否为该 VM 启用了更新管理。 此验证包括针对 Azure Log Analytics 工作区和链接的自动化帐户进行检查，并检查更新管理解决方案是否在工作区中。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区用于收集由功能和服务（如更新管理）生成的数据。 工作区提供了一个位置来查看和分析来自多个数据源的数据。

验证过程还会检查 VM 是否预配了 Microsoft Monitoring Agent (MMA) 和自动化混合 Runbook 辅助角色。 此代理用于与 Azure 自动化通信并获取关于更新状态的信息。 代理要求打开端口 443 以便与 Azure 自动化服务进行通信以及下载更新。

如果在载入过程中发现缺少下列任何先决条件，则会自动添加这些条件：

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区
* [自动化帐户](./automation-offering-get-started.md)
* [混合 Runbook 辅助角色](./automation-hybrid-runbook-worker.md)（在 VM 上启用）

在“更新管理”下，设置要使用的位置、Log Analytics 工作区和自动化帐户。 然后选择“启用”。 如果这些选项不可用，则意味着已经为 VM 启用了其他自动化解决方案。 在这种情况下，必须使用同一工作区和自动化帐户。

![“启用更新管理解决方案”窗口](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

启用解决方案最多可能需要几分钟时间。 在此期间，请勿关闭浏览器窗口。 启用该解决方案后，VM 中缺少的更新信息会流向 Log Analytics。 这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

## <a name="view-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”窗格随即打开。 如果缺少更新，则会在“缺失更新”选项卡上显示缺失更新的列表。

在“信息链接”下选择更新链接即可在新窗口中打开更新的支持文章。 可以在此窗口中了解有关更新的重要信息。

![查看更新状态](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

单击更新的其他位置会打开所选更新的“日志搜索”窗格。 日志搜索的查询是为该特定更新预定义的。 可以修改此查询或创建自己的查询，以便查看环境中已部署更新或缺失更新的详细信息。

![查看更新状态](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>配置警报

在此步骤中，请设置一个警报，使其在更新成功部署时发出通知。 创建的该警报基于 Log Analytics 查询。 可以编写一个自定义查询来提供其他警报，以涵盖许多不同的方案。 在 Azure 门户中转到“监视器”，然后选择“创建警报”。 

在“创建规则”的“1.定义警报条件”下，选择“选择目标”。 在“按资源类型筛选”下，选择“Log Analytics”。 选择 Log Analytics 工作区，然后选择“完成”。

![创建警报](./media/automation-tutorial-update-management/create-alert.png)

选择“添加条件”。

在“配置信号逻辑”下的表中选择“自定义日志搜索”。 在“搜索查询”文本框中输入以下查询：

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```
此查询返回计算机以及在指定的时间范围内完成的更新运行名称。

在“警报逻辑”下，输入 **1** 作为“阈值”。 完成后，选择“完成”。

![配置信号逻辑](./media/automation-tutorial-update-management/signal-logic.png)

在“2. 定义警报详细信息”下，输入警报的名称和说明。 将“严重性”设置为“参考(严重性 2)”，因为此警报针对的是成功的运行。

![配置信号逻辑](./media/automation-tutorial-update-management/define-alert-details.png)

在“3. 定义操作组”下，选择“新建操作组”。 操作组是可以在多个警报中使用的一组操作。 这些操作可能包括但不限于电子邮件通知、Runbook、Webhook 以及其他操作。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)。

在“操作组名称”框中输入警报的名称和一个短名称。 使用此组发送通知时，短名称用来代替完整的操作组名称。

在“操作”下输入操作的名称，例如“电子邮件通知”。 在“操作类型”下，选择“电子邮件/短信/推送/语音”。 在“详细信息”下，选择“编辑详细信息”。

在“电子邮件/短信/推送/语音”窗格中，输入一个名称。 选中“电子邮件”复选框，然后输入有效的电子邮件地址。

![配置电子邮件操作组](./media/automation-tutorial-update-management/configure-email-action-group.png)

在“电子邮件/短信/推送/语音”窗格中，选择“确定”。 在“添加操作组”窗格中，选择“确定”。

若要自定义警报电子邮件的主题，请在“创建规则”下的“自定义操作”下选择“电子邮件主题”。 完成后，请选择“创建警报规则”。 此警报会指出更新部署成功的时间以及哪些计算机是该更新部署运行的一部分。

## <a name="schedule-an-update-deployment"></a>计划更新部署

接下来，请计划一个遵循发布时间和服务窗口的部署，以便安装更新。 可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

> [!WARNING]
> 如果更新要求重启，VM 会自动重启。

若要为 VM 计划新的更新部署，请转到“更新管理”，然后选择“计划更新部署”。

在“新建更新部署”下，指定以下信息：

* **名称**：输入用于更新部署的唯一名称。

* **操作系统**：选择更新部署的目标 OS。

* **更新分类**：选择更新部署包含在部署中的软件类型。 对于本教程，请保留所有选定的类型。

  分类类型：

   |操作系统  |Type  |
   |---------|---------|
   |Windows     | 关键更新</br>安全更新</br>更新汇总</br>功能包</br>服务包</br>定义更新</br>工具</br>更新        |
   |Linux     | 关键和安全更新</br>其他更新       |

   有关分类类型的说明，请参阅[更新分类](automation-update-management.md#update-classifications)。

* **计划设置**：“计划设置”窗格会打开。 默认开始时间为晚于当前时间 30 分钟。 可以将开始时间设置为 10 分钟之后的任何将来时间。

   还可以指定部署是否只发生一次，或者设置一个定期计划。 在“重复”下选择“一次”。 保留默认值“1 天”，然后选择“确定”。 这样会设置定期计划。

* **维护时段(分钟)**：保留默认值。 可以设置要进行更新部署的时间段。 此设置有助于确保在定义的服务时段内执行更改。

![更新“计划设置”窗格](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

配置完计划以后，选择“创建”。 此时会回到状态仪表板。 选择“计划性更新部署”即可显示所创建的部署计划。

## <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划性部署开始后，可以在“更新管理”下的“更新部署”选项卡上查看该部署的状态。 部署当前正在运行时，其状态为“正在进行”。 部署完成以后，如果成功，则状态更改为“成功”。 当部署中有一个或多个更新失败时，状态为“部分失败”。

选择已完成的更新部署，查看该更新部署的仪表板。

![特定部署的更新部署状态仪表板](./media/automation-tutorial-update-management/manageupdates-view-results.png)

“更新结果”下的摘要提供了 VM 上更新和部署结果的总数。 右侧的表显示了每个更新的细目以及安装结果。

以下列表显示可用值：

* **未尝试**：由于定义的维护时段时长不足，因而未安装更新。
* **成功**：更新成功。
* **失败**：更新失败。

若要查看部署创建的所有日志条目，请选择“所有日志”。

选择“输出”，查看负责管理目标 VM 更新部署的 Runbook 的作业流。

若要查看有关部署中错误的详细信息，请选择“错误”。

在更新部署成功后，会发送类似于以下示例的电子邮件来通知部署成功：

![配置电子邮件操作组](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 载入用于更新管理的 VM
> * 查看更新评估
> * 配置警报
> * 计划更新部署
> * 查看部署结果

继续阅读更新管理解决方案的概述。

> [!div class="nextstepaction"]
> [更新管理解决方案](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
