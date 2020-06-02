---
title: 在 Azure 自动化中管理 Azure VM 的更新和修补程序
description: 本文介绍如何使用更新管理来管理 Azure VM 的更新和修补程序。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 5b5172df6ed6993742a08d5ac08cf700681dfc6a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829148"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>管理 Azure VM 的更新和修补程序

本文介绍如何使用 Azure 自动化[更新管理](automation-update-management.md)功能来管理 Azure VM 的更新和修补程序。 

有关定价信息，请参阅[更新管理自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

## <a name="prerequisites"></a>先决条件

* 为一个或多个 VM 启用了[更新管理](automation-update-management.md)功能。 
* 为更新管理启用了[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="view-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”页随即打开。 如果发现缺少任何更新，则会在“缺少的更新”选项卡上显示缺少的更新列表。

在“信息链接”下选择更新链接即可打开更新的支持文章。 可以了解有关更新的重要信息。

![查看更新状态](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

单击更新的其他位置会打开所选更新的“日志搜索”窗格。 日志搜索的查询是为该特定更新预定义的。 你可以修改此查询或创建自己的查询，以便查看环境中已部署或缺失的更新的详细信息。

![查看更新状态](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>配置警报

在此步骤中，了解如何设置警报以获知更新部署的状态。

### <a name="alert-conditions"></a>警报条件

在自动化帐户中，转到“监视”下的“警报”，然后单击“新建警报规则”  。

你的自动化帐户已被选为资源。 若要更改自动化帐户，请单击“选择”。 在“选择资源”页上，从“按资源类型筛选”下拉菜单中选择“自动化帐户” 。 选择你的自动化帐户，然后单击“完成”。

单击“添加条件”以选择适合更新部署的信号。 下表显示了两个可用信号的详细信息。

|信号名称|维度|说明|
|---|---|---|
|`Total Update Deployment Runs`|- 更新部署名称<br>- 状态|此信号针对更新部署的总体状态发出警报。|
|`Total Update Deployment Machine Runs`|- 更新部署名称</br>- 状态</br>- 目标计算机</br>- 更新部署运行 ID|此信号针对特定计算机的更新部署状态发出警报。|

对于维度，请从列表中选择一个有效值。 如果所需的值不在列表中，请单击维度旁边的 **\+** 符号，并键入自定义名称。 然后选择要查找的值。 若要为某个维度选择所有值，请单击“选择 \*”按钮。 如果没有为某个维度选择值，更新管理将忽略该维度。

![配置信号逻辑](./media/automation-tutorial-update-management/signal-logic.png)

在“警报逻辑”下， 输入 **1** 作为“阈值”。 完成后，选择“完成”。

### <a name="alert-details"></a>警报详细信息

在“2. 定义警报详细信息“下，下，输入警报的名称和说明。 对于成功的运行，请将“严重性”设置为“参考(严重性 2)”；对于失败的运行，请将其设置为“参考(严重性 1)”。

![配置信号逻辑](./media/automation-tutorial-update-management/define-alert-details.png)

在“操作组”下，选择“新建” 。 操作组是可以在多个警报中使用的一组操作。 这些操作可能包括电子邮件通知、Runbook、Webhook 等等。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

在“操作组名称”字段中，为警报输入一个名称和一个短名称。 使用指定的组发送通知时，更新管理将使用短名称而非使用完整的操作组名称。

在“操作”下，为操作输入一个名称，例如“电子邮件通知”。  对于“操作类型”，请选择“电子邮件/短信/推送/语音”。  对于“详细信息”，请选择“编辑详细信息”。 

在“电子邮件/短信/推送/语音”窗格中，输入一个名称。 选中“电子邮件”复选框，然后输入有效的电子邮件地址。

![配置电子邮件操作组](./media/automation-tutorial-update-management/configure-email-action-group.png)

在“电子邮件/短信/推送/语音”窗格中，单击“确定”。 在“添加操作组”窗格中，单击“确定”。

若要自定义警报电子邮件的主题，请在“创建规则”下的“自定义操作”下选择“电子邮件主题”。   完成后，请选择“创建警报规则”。 此警报会指出更新部署成功的时间以及哪些计算机是更新部署运行的一部分。

## <a name="schedule-an-update-deployment"></a>计划更新部署

计划一个遵循发布时间和服务窗口的部署，以便安装更新。 你可以选择要在部署中包括的更新类型。 例如，可包括关键或安全更新，排除更新汇总。

>[!NOTE]
>计划某个更新部署会创建链接到 Patch-MicrosoftOMSComputers Runbook 的[计划](shared-resources/schedules.md)资源，该 Runbook 处理目标计算机上的更新部署。 如果在创建部署后通过 Azure 门户或 PowerShell 删除了该计划资源，该删除操作会破坏计划的更新部署，在你尝试通过门户重新配置该计划资源时会出现错误。 只能通过删除相应的部署计划来删除计划资源。  

若要为 VM 计划新的更新部署，请转到“更新管理”，然后选择“计划更新部署” 。

在“新建更新部署”下，指定以下信息：

* **Name**：输入用于更新部署的唯一名称。

* **操作系统**：选择更新部署的目标 OS。

* **要更新的组（预览）** ：定义一个查询，其中组合了订阅、资源组、位置和标记，以便生成要包含在部署中的 Azure VM 动态组。 有关详细信息，请参阅[动态组](automation-update-management-groups.md)。

* **要更新的计算机**：选择已保存的搜索、已导入的组或者从下拉菜单中选择“计算机”并选择各个计算机。 如果选择“计算机”，则每个计算机的就绪状态将显示在“更新代理准备情况”列中 。 若要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../azure-monitor/platform/computer-groups.md)。

* **更新分类**：对于每个产品，请取消选择所有受支持的更新分类，但要包含在更新部署中的分类除外。 有关分类类型的说明，请参阅[更新分类](automation-view-update-assessments.md#work-with-update-classifications)。

* **要包含/排除的更新** - 打开“包含/排除”页。 要包含或排除的更新位于单独的选项卡上，其中指定了对应的知识库文章 ID 号。 指定一个或多个 ID 号时，需要删除或取消选中更新部署中的所有分类。 这可以确保在指定更新 ID 时，不会将任何其他更新包含在你的更新包中。

> [!NOTE]
> 请务必注意，排除项会替代包含项。 例如，如果定义了 `*` 排除规则，则更新管理不会安装任何修补程序或包，因为已将它们全部排除。 已排除的修补程序仍显示为在计算机中缺少。 对于 Linux 计算机，如果包含一个具有依赖包的包，而该依赖包已被排除，则更新管理不会安装主包。

> [!NOTE]
> 无法指定已在更新部署中由包含的包取代的更新。
>
* **计划设置**：“计划设置”窗格将打开。 默认开始时间为晚于当前时间 30 分钟。 可以将开始时间设置为 10 分钟之后的任何将来时间。

   还可以指定部署是否只发生一次，或者设置一个定期计划。 在“重复”下选择“一次”。  保留默认值（1 天），然后单击“确定”。 这些条目将设置重复计划。

* **前脚本 + 后脚本**：选择要在部署前和部署后运行的脚本。 若要了解详细信息，请参阅[管理前脚本和后脚本](pre-post-scripts.md)。

* **维护时段(分钟)** ：保留默认值。 维护时段控制允许安装更新的时间。 指定维护时段时，请考虑以下详细信息：

  * 维护时段控制安装的更新数量。
  * 如果维护时段即将结束，更新管理不会停止安装新的更新。
  * 如果超出了维护时段，更新管理不会终止正在进行的更新。
  * 如果超出了 Windows 上的维护时段，通常是因为服务包更新需要很长时间才能安装。

  > [!NOTE]
  > 若要避免在 Ubuntu 上的维护时段外应用更新，请重新配置无人参与升级包，禁用自动更新。 有关如何配置此包的信息，请参阅 [Ubuntu Server 指南中的自动更新主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

* **重启选项**:用于指定重新启动的处理选项。 提供了以下选项：
  * 必要时重新启动（默认）
  * 永远重启
  * 永不重启
  * 仅重新启动 - 不安装更新

> [!NOTE]
> 如果“重新启动选项”设置为“永不重新启动”，则[用于管理重启的注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)下列出的注册表项可能会导致重新启动事件 。

配置完计划后，单击“创建”。

![更新“计划设置”窗格](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

此时会回到状态仪表板。 选择“计划的更新部署”以显示刚刚创建的部署计划。

> [!NOTE]
> 更新管理支持部署第一方更新和预先下载修补程序。 要实现此项支持，需要在要修补的系统上进行更改。 请参阅[第一方和预先下载支持](automation-configure-windows-update.md)，了解如何在系统上配置这些设置。

还能够以编程方式创建更新部署。 若要了解如何使用 REST API 创建更新部署，请参阅[软件更新配置 - 创建](/rest/api/automation/softwareupdateconfigurations/create)。 还可以使用一个示例 Runbook 来创建每周更新部署。 若要了解有关此 Runbook 的详细信息，请参阅[为资源组中的一个或多个 VM 创建每周更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)。

## <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划的部署开始后，可以在“更新管理”下的“更新部署”选项卡上查看该部署的状态。 部署当前正在运行时，其状态为“正在进行”。 部署成功完成后，状态将更改为“成功”。 如果部署中有一个或多个更新失败，状态将是“部分失败”。

选择已完成的更新部署以查看其仪表板。

![特定部署的更新部署状态仪表板](./media/automation-tutorial-update-management/manageupdates-view-results.png)

“更新结果”下的摘要提供了 VM 上更新和部署结果的总数。 右侧的表显示了每个更新的细目以及安装结果。

可用值有：

* **未尝试**：根据定义的维护时段持续时间，可用时间不足，因此未安装该更新。
* **成功**：更新成功。
* **失败**：更新失败。

选择“所有日志”来查看部署创建的所有日志条目。

选择“输出”，查看负责管理目标 VM 更新部署的 Runbook 的作业流。

若要查看有关部署中错误的详细信息，请选择“错误”。

更新部署成功后，你会收到如下所示的确认电子邮件：

![配置电子邮件操作组](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>后续步骤

* 有关更新管理的详细信息，请参阅[更新管理概述](automation-update-management.md)。