---
title: 在 Azure 自动化中管理 Azure VM 的更新和修补程序
description: 本文介绍如何使用更新管理来管理 Azure VM 的更新和修补程序。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 79cffa7aedd0fc04dd4a747ef28bc67cacf37905
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204882"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>管理 Azure VM 的更新和修补程序

本文介绍如何使用 Azure 自动化[更新管理](automation-update-management.md)功能来管理 Azure VM 的更新和修补程序。 有关定价信息，请参阅[更新管理自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

> [!NOTE]
> 更新管理支持部署第一方更新和预先下载修补程序。 要实现此项支持，需要在要修补的系统上进行更改。 请参阅[为 Azure 自动化更新管理配置 Windows 更新设置](automation-configure-windows-update.md)，了解如何在系统上配置这些设置。

使用本文中的步骤之前，请确保已使用以下一种方法在 VM 上启用了更新管理：

* [从自动化帐户启用更新管理](automation-onboard-solutions-from-automation-account.md)
* [通过浏览 Azure 门户启用更新管理](automation-onboard-solutions-from-browse.md)
* [从 runbook 启用更新管理](automation-onboard-solutions.md)
* [从 Azure VM 启用更新管理](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>限制部署的范围

更新管理在工作区中使用范围配置来确定要接收更新的计算机。 有关详细信息，请参阅[限制更新管理的部署范围](automation-scope-configurations-update-management.md)。

## <a name="view-update-assessment"></a>查看更新评估

要查看更新评估，请执行以下操作：

1. 在自动化帐户中，在“更新管理”下选择“更新管理” 。 

2. “更新管理”页上会列出你的环境所具有的更新。 如果发现缺少任何更新，则会在“缺少的更新”选项卡上显示缺少的更新列表。

3. 在“信息链接”下，选择更新的链接以打开支持文章，获取有关更新的重要信息。

    ![查看更新状态](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. 单击更新的其他位置，打开“日志搜索”窗格。 日志搜索的查询是为该特定更新预定义的。 可以修改此查询或创建自己的查询，以查看详细信息。

    ![查看更新状态](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>配置警报

按照以下步骤设置警报，以获取更新部署的状态：

1. 在自动化帐户中，转到“监视”下的“警报”，然后单击“新建警报规则”  。

2. 在“创建警报规则”页上，系统已选择你的自动化帐户作为资源。 如果要对其进行更改，请单击“编辑资源”。 

3. 在“选择资源”页上，从“按资源类型筛选”下拉菜单中选择“自动化帐户” 。 

4. 选择要使用的自动化帐户，然后单击“完成”。

5. 单击“添加条件”以选择适合更新部署的信号。 下表显示了两个可用信号的详细信息。

    |信号名称|维度|说明
    |---|---|---|
    |`Total Update Deployment Runs`|- 更新部署名称<br>- 状态    |针对更新部署的总体状态的警报。|
    |`Total Update Deployment Machine Runs`|- 更新部署名称</br>- 状态</br>- 目标计算机</br>- 更新部署运行 ID    |针对特定计算机的更新部署状态的警报。|

6. 对于维度，请从列表中选择一个有效值。 如果所需的值不在列表中，请单击维度旁边的 \+，并键入自定义名称。 然后选择要查找的值。 若要为某个维度选择所有值，请单击“选择 \*”按钮。 如果没有为某个维度选择值，更新管理将忽略该维度。

    ![配置信号逻辑](./media/automation-tutorial-update-management/signal-logic.png)

7. 在“警报逻辑”中的“时间聚合”和“阈值”字段中输入值，然后单击“完成”   。

8. 在下一窗格中，输入警报的名称和描述。

9. 对于成功的运行，请将“严重性”字段设置为“信息(严重性 2)”；对于失败的运行，请将其设置为“信息(严重性 1)”  。

    ![配置信号逻辑](./media/automation-tutorial-update-management/define-alert-details.png)

10. 单击“是”或“否”，具体取决于你想要如何启用警报规则 。

11. 如果你不希望为此规则配置警报，请选择“取消警报”。

## <a name="configure-action-groups-for-your-alerts"></a>配置警报对应的操作组

配置警报后，可以设置操作组，即用于多个警报的一组操作。 这些操作可能包括电子邮件通知、Runbook、Webhook 等等。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

1. 选择警报，然后在“操作组”下选择“新建” 。 

2. 输入操作组的全名和短名称。 使用指定的组发送通知时，更新管理将使用短名称。

3. 在“操作”下，输入指定操作的名称，例如“电子邮件通知” 。 

4. 对于“操作类型”，选择适当的类型，例如“电子邮件/短信/推送/语音” 。 

5. 单击“编辑详细信息”。

6. 填写操作类型的窗格。 例如，如果使用“电子邮件/短信/推送/语音”，请输入操作名称，选择“电子邮件”复选框，输入有效的电子邮件地址，然后单击“确定”  。

    ![配置电子邮件操作组](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. 在“添加操作组”窗格中，单击“确定”。

8. 对于警报电子邮件，你可以自定义电子邮件主题。 在“创建规则”下选择“自定义操作”，然后选择“电子邮件主题”  。 

9. 完成后，请单击“创建警报规则”。 

## <a name="schedule-an-update-deployment"></a>计划更新部署

计划某个更新部署会创建链接到 Patch-MicrosoftOMSComputers Runbook 的[计划](shared-resources/schedules.md)资源，该 Runbook 处理目标计算机上的更新部署。 必须计划一个遵循发布时间和服务时段的部署，以便安装更新。 你可以选择要在部署中包括的更新类型。 例如，可包括关键或安全更新，排除更新汇总。

>[!NOTE]
>如果在创建部署后通过 Azure 门户或 PowerShell 删除了该计划资源，该删除操作会破坏计划的更新部署，在你尝试通过门户重新配置该计划资源时会出现错误。 只能通过删除相应的部署计划来删除计划资源。  

要计划新的更新部署，请执行以下操作：

1. 在自动化帐户中，转到“更新管理”下的“更新管理”，然后选择“安排更新部署”  。

2. 在“新建更新部署”下的“名称”字段中输入部署的唯一名称 。

3. 选择更新部署的目标操作系统。

4. 在“要更新的组（预览）”区域，定义组合了订阅、资源组、位置和标记的查询，以生成要在部署中加入的 Azure VM 动态组。 要了解详细信息，请参阅[将动态组与更新管理配合使用](automation-update-management-groups.md)。

5. 在“要更新的计算机”区域，选择已保存的搜索、已导入的组或者从下拉菜单中选择“计算机”并选择各个计算机 。 通过此选项可以查看每台计算机的 Log Analytics 代理的准备情况。 若要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../azure-monitor/platform/computer-groups.md)。

6. 使用“更新分类”区域为产品指定[更新分类](automation-view-update-assessments.md#work-with-update-classifications)。 对于每个产品，请取消选择所有受支持的更新分类，但要包含在更新部署中的分类除外。

7. 使用“包括/排除更新”区域来选择要部署的特定更新。 “包括/排除”页按知识库文章 ID 号显示要包括或排除的更新。 
    
   > [!IMPORTANT]
   > 请记住，排除项会替代包含项。 例如，如果定义了 `*` 排除规则，更新管理会从安装中排除所有修补程序或包。 已排除的修补程序仍显示为计算机缺少此项。 对于 Linux 计算机，如果包含一个具有依赖包的包，而该依赖包已被排除，则更新管理不会安装主包。

   > [!NOTE]
   > 你不能指定将已被取代的更新包含在更新部署中。

8. 选择“计划设置”。 默认开始时间为晚于当前时间 30 分钟。 可以将开始时间设置为 10 分钟之后的任何将来时间。

9. 使用“重复周期”字段指定部署是发生一次还是使用定期计划，然后单击“确定” 。

10. 在“前脚本 + 后脚本(预览)”区域，选择要在部署前和部署后运行的脚本。 若要了解详细信息，请参阅[管理前脚本和后脚本](pre-post-scripts.md)。
    
11. 使用“维护时段(分钟)”字段来指定安装更新的允许时长。 指定维护时段时，请考虑以下详细信息：

    * 维护时段控制安装的更新数量。
    * 如果维护时段即将结束，更新管理不会停止安装新的更新。
    * 如果超出了维护时段，更新管理不会终止正在进行的更新。
    * 如果超出了 Windows 上的维护时段，通常是因为服务包更新需要很长时间才能安装。

    > [!NOTE]
    > 若要避免在 Ubuntu 上的维护时段外应用更新，请重新配置 `Unattended-Upgrade` 包，禁用自动更新。 有关如何配置此包的信息，请参阅 [Ubuntu Server 指南中的自动更新主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

12. 使用“重新启动选项”字段指定部署过程中处理重新启动的方式。 提供了以下选项： 
    * 必要时重新启动（默认）
    * 永远重启
    * 永不重启
    * 仅重新启动；此选项不安装更新

    > [!NOTE]
    > 如果“重新启动选项”设置为“永不重新启动”，则[用于管理重启的注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)下列出的注册表项可能会导致重新启动事件 。

13. 完成部署计划配置后，单击“创建”。

    ![更新“计划设置”窗格](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. 此时会回到状态仪表板。 选择“计划的更新部署”以显示创建的部署计划。

## <a name="schedule-an-update-deployment-programmatically"></a>以编程方式计划更新部署

若要了解如何使用 REST API 创建更新部署，请参阅[软件更新配置 - 创建](/rest/api/automation/softwareupdateconfigurations/create)。 

可以使用示例 runbook 创建每周更新部署。 若要了解有关此 Runbook 的详细信息，请参阅[为资源组中的一个或多个 VM 创建每周更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)。

## <a name="check-deployment-status"></a>检查部署状态

在计划的部署开始后，可以在“更新管理”下的“更新部署”选项卡上查看其状态 。 部署当前正在运行时，其状态为“正在进行”。 部署成功完成后，状态将更改为“成功”。 如果部署中有一个或多个更新失败，状态将是“部分失败”。

## <a name="view-results-of-a-completed-update-deployment"></a>查看已完成的更新部署的结果

部署完成后，可以选择它以查看其面板。

![特定部署的更新部署状态仪表板](./media/automation-tutorial-update-management/manageupdates-view-results.png)

“更新结果”下的摘要提供了目标 VM 上的更新和部署结果的总数。 右侧的表显示了更新的细目以及每个更新的安装结果。

可用值有：

* **未尝试** - 由于定义的维护时段时长不足，因为而未安装该更新。
* **未选择** - 未选择要部署的更新。
* **成功** - 更新成功。
* **失败** - 更新失败。

选择“所有日志”来查看部署创建的所有日志条目。

选择“输出”，查看负责管理目标 VM 更新部署的 runbook 的作业流。

若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="view-the-deployment-alert"></a>查看部署警报

完成更新部署后，你会收到在部署安装过程中指定的警报。 例如，下面是一封确认修补程序的电子邮件。

![配置电子邮件操作组](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>后续步骤

* 有关范围配置的信息，请参阅[限制更新管理的部署范围](automation-scope-configurations-update-management.md)。
* 如果需要搜索 Log Analytics 工作区中存储的日志，请参阅 [Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)。
* 如果已完成部署，请参阅[取消工作区与自动化帐户的链接](automation-unlink-workspace-update-management.md)。
* 若要从更新管理中删除 VM，请参阅[从更新管理中删除 VM](automation-remove-vms-from-update-management.md)。
* 若要对常规更新管理错误进行故障排除，请参阅[更新管理问题故障排除](troubleshoot/update-management.md)。
* 若要对 Windows 更新代理的问题进行故障排除，请参阅 [Windows 更新代理问题故障排除](troubleshoot/update-agent-issues.md)。
* 若要排查 Linux 更新代理的问题，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。