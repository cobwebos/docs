---
title: 管理 Azure Windows VM 的更新和修补程序
description: 本文概述了如何使用 Azure 自动化 - 更改管理来管理 Azure Windows VM 的更新和修补程序。
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054753"
---
# <a name="manage-windows-updates-with-azure-automation"></a>使用 Azure 自动化管理 Windows 更新

使用更新管理可以管理虚拟机的更新和修补程序。
本教程介绍了如何快速评估可用更新的状态、计划所需更新的安装、查看部署结果，以及创建警报来验证更新是否已成功应用。

有关定价信息，请参阅[更新管理自动化定价](https://azure.microsoft.com/pricing/details/automation/)

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
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 Runbook 和观察程序任务。
* 要载入的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-update-management"></a>启用更新管理

就本教程来说，首先需要在 VM 上启用更新管理。

1. 从 Azure 门户中，在左侧菜单上选择“虚拟机”，然后从列表中选择一台 VM。
2. 从 VM 页面中，在“操作”部分下单击“更新管理”。 “启用更新管理”页随即打开。

执行验证以确定是否为该虚拟机启用了更新管理。 此验证包括针对 Log Analytics 工作区和链接的自动化帐户进行检查，并检查更新管理解决方案是否在工作区中。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区用于收集由功能和服务（如更新管理）生成的数据。 工作区提供了一个位置来查看和分析来自多个数据源的数据。

验证过程还会检查 VM 是否预配了 Microsoft Monitoring Agent (MMA) 和自动化混合 Runbook 辅助角色。
此代理用于与 Azure 自动化进行通信并获取关于更新状态的信息。 代理要求打开端口 443 以便与 Azure 自动化服务进行通信以及下载更新。

如果在载入过程中发现缺少下列任何先决条件，则会自动添加这些条件：

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区
* [自动化帐户](./automation-offering-get-started.md)
* VM 上已启用[混合 runbook 辅助角色](./automation-hybrid-runbook-worker.md)

“更新管理”屏幕随即打开。 配置要使用的位置、Log Analytics 工作区和自动化帐户，然后单击“启用”。 如果这些字段灰显，则意味着已为 VM 启用其他自动化解决方案，因此必须使用同一工作区和自动化帐户。

![“启用更新管理解决方案”窗口](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

启用解决方案最多可能需要几分钟时间。 在此期间，不应关闭浏览器窗口。
启用该解决方案后，VM 中缺少的更新信息会流向 Log Analytics。
这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

## <a name="view-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”屏幕随即显示。
如果缺少更新，可在“缺失更新”选项卡上查看缺失更新的列表。

选择更新的“信息链接”即可在新窗口中打开更新的支持文章。 可以在此处了解有关更新的重要信息。

![查看更新状态](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

单击更新的其他位置会打开所选更新的“日志搜索”窗口。 日志搜索的查询是为该特定更新预定义的。 可以修改此查询或创建自己的查询，以便查看环境中已部署更新或缺失更新的详细信息。

![查看更新状态](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>配置警报

在此步骤中，将配置一个警报，使其在更新成功部署时发出通知。 创建的该警报基于 Log Analytics 查询。 还可以编写任何自定义查询来提供其他警报以涵盖许多不同的方案。 在 Azure 门户中，导航到“监视器”并单击“创建警报”。 这将打开“创建规则”页面。

在“1. 定义警报条件”下，单击“+ 选择目标”。 在“按资源类型筛选”下，选择“Log Analytics”。 选择你的 Log Analytics 工作区，并单击“完成”。

![创建警报](./media/automation-tutorial-update-management/create-alert.png)

单击“+ 添加条件”按钮以打开“配置信号逻辑”页面。 在表中选择“自定义日志搜索”。 在“搜索查询”文本框中输入以下查询。 此查询返回计算机以及在指定的时间范围内完成的更新运行名称。

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

输入 **1** 作为警报逻辑的**阈值**。 完成后，单击“完成”。

![配置信号逻辑](./media/automation-tutorial-update-management/signal-logic.png)

在“2. 定义警报详细信息“下，为警报提供一个友好名称和说明。 将“严重性”设置为“信息(Sev 2)”，因为此警报是针对成功运行的。

![配置信号逻辑](./media/automation-tutorial-update-management/define-alert-details.png)

在“3. 定义操作组”下，单击“+ 新建操作组”。 操作组是可以在多个警报中使用的一组操作。 这可能包括但不限于电子邮件通知、Runbook、Webhook 以及其他操作。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)。

在“操作组名称”框中，为其提供一个友好名称和短名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

在“操作”下，为操作提供一个友好名称（例如“电子邮件通知”），在“操作类型”下，选择“电子邮件/短信/推送/语音”。 在“详细信息”下，选择“编辑详细信息”。

在“电子邮件/短信/推送/语音”页面上，为其提供一个名称。 选中“电子邮件”复选框，然后输入要使用的有效电子邮件地址。

![配置电子邮件操作组](./media/automation-tutorial-update-management/configure-email-action-group.png)

单击“电子邮件/短信/推送/语音”页面上的“确定”以将其关闭，然后单击“确定”以关闭“添加操作组”页面。

还可以在“创建规则”页面上通过在“自定义操作”下单击“电子邮件主题”来自定义发送的电子邮件的主题。 完成后，单击“创建警报规则”。 这将创建一个规则，用以在更新部署成功时发出通知并且指明哪些计算机是该更新部署运行的一部分。

## <a name="schedule-an-update-deployment"></a>计划更新部署

现在已配置了该警报，请计划一个遵循你的发布时间和服务窗口的部署来安装更新。
可选择在部署中包括哪种更新类型。
例如，可包括关键或安全更新，排除更新汇总。

> [!WARNING]
> 如果更新要求重启，VM 会自动重启。

导航回到“更新管理”并选择屏幕顶部的“计划更新部署”即可计划用于 VM 的新的更新部署。

在“新建更新部署”屏幕中，指定以下信息：

* **名称** - 提供用于更新部署的唯一名称。

* **操作系统** - 选择更新部署的目标 OS。

* 更新分类- 选择部署中包含的更新部署的软件类型。 对于本教程，请保留所有选定的类型。

  分类类型：

   |操作系统  |Type  |
   |---------|---------|
   |Windows     | 关键更新</br>安全更新</br>更新汇总</br>功能包</br>服务包</br>定义更新</br>工具</br>更新        |
   |Linux     | 关键和安全更新</br>其他更新       |

   有关分类类型的说明，请参阅[更新分类](automation-update-management.md#update-classifications)。

* **计划设置** - 这将打开“计划设置”页面。 默认开始时间为晚于当前时间 30 分钟。 可以将其设置为 10 分钟之后的任何将来时间。

   还可以指定部署是发生一次还是设置定期计划。
   在“重复”下选择“一次”。 保留默认值“1 天”，然后单击“确定”。 这样会设置定期计划。

* **维护时段(分钟)** - 将此值保留为默认值。 可以指定要进行更新部署的时间段。 此设置有助于确保在定义的服务时段内执行更改。

![更新计划设置屏幕](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

完成计划配置以后，请单击“创建”按钮。 此时会回到状态仪表板。 选择“计划性更新部署”即可显示所创建的部署计划。

## <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划性部署开始后，可以在“更新管理”屏幕的“更新部署”选项卡上查看该部署的状态。
如果部署当前正在运行，则状态显示为“正在运行”。
如果部署已成功完成，则状态会更改为“成功”。
当部署中有一个或多个更新失败时，状态为“部分失败”。
单击已完成的更新部署，查看该更新部署的仪表板。

![特定部署的更新部署状态仪表板](./media/automation-tutorial-update-management/manageupdates-view-results.png)

“更新结果”磁贴中的摘要提供了 VM 上更新和部署结果的总数。
右侧的表显示了每个更新的细目以及安装结果。
以下列表显示可用值：

* 未尝试 - 由于定义的维护时段时长不足，因而未安装更新。
* 成功- 更新成功
* 失败- 更新失败

单击“所有日志”，查看部署创建的所有日志条目。

单击“输出”磁贴，查看负责管理目标虚拟机更新部署的 runbook 的作业流。

单击“错误”，查看有关部署中的任何错误的详细信息。

在更新部署成功后，将发送类似于下图的电子邮件来指明部署成功。

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
