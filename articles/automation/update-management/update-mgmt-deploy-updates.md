---
title: 如何为 Azure 自动化更新管理创建更新部署
description: 本文介绍如何计划更新部署并查看其状态。
services: automation
ms.subservice: update-management
ms.date: 09/16/2020
ms.topic: conceptual
ms.openlocfilehash: fa5cabd5410f0cbe7382db0289d98bc69d4a01fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294710"
---
# <a name="how-to-deploy-updates-and-review-results"></a>如何部署更新和查看结果

本文介绍如何计划更新部署并在部署完成后查看执行过程。 你可以从所选的启用 Arc 的服务器，或者在所有已配置的计算机和服务器上从自动化帐户配置更新部署。 

在每个方案下，你创建的部署目标为所选的计算机或服务器，或者在从自动化帐户创建部署的情况下，你可以将一个或多个计算机作为目标。 当你从 Azure VM 或启用了 Arc 的服务器计划更新部署时，步骤与从你的自动化帐户中部署的步骤相同，但以下情况例外：

* 系统会根据计算机的 OS 自动预选择操作系统
* 要更新的目标计算机自动设置为目标
* 配置计划时，可以指定 " **立即更新**" 或 "使用定期计划"。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>计划更新部署

计划更新部署将创建一个链接到**MicrosoftOMSComputers** runbook 的[计划](../shared-resources/schedules.md)资源，该 runbook 可处理目标计算机上的更新部署。 必须计划一个遵循发布时间和服务时段的部署，以便安装更新。 你可以选择要在部署中包括的更新类型。 例如，你可以包括关键或安全更新，并排除更新汇总。

>[!NOTE]
>如果在创建部署后通过 Azure 门户或 PowerShell 删除了该计划资源，该删除操作会破坏计划的更新部署，在你尝试通过门户重新配置该计划资源时会出现错误。 只能通过删除相应的部署计划来删除计划资源。  

若要计划新的更新部署，请执行以下步骤。 根据所选资源 (即自动化帐户、启用 Arc 的服务器、Azure VM) ，以下步骤适用于所有这些步骤，但在配置部署计划时有细微差别。

1. 在门户中，计划的部署：

   * 一台或多台计算机，请导航到 **自动化帐户** ，并从列表中选择更新管理启用的自动化帐户。
   * 对于 Azure VM，请导航到 " **虚拟机** "，然后从列表中选择你的 VM。
   * 对于启用了 Arc 的服务器，请导航到 " **服务器-Azure Arc** "，然后从列表中选择你的服务器。

2. 根据所选的资源，导航到更新管理：

   * 如果选择了自动化帐户，请在 "**更新管理**" 下中转到 "**更新管理**"，然后选择 "**计划更新部署**"。
   * 如果选择了 Azure VM，请参阅 " **来宾 + 主机更新**"，然后选择 " **前往更新管理**"。
   * 如果选择了启用了 Arc 的服务器，请参阅 " **更新管理**"，然后选择 " **计划更新部署**"。

3. 在“新建更新部署”下的“名称”字段中，输入部署的唯一名称 。

4. 选择更新部署的目标操作系统。

    > [!NOTE]
    > 如果选择了 Azure VM 或启用了 Arc 的服务器，则此选项不可用。 系统将自动标识操作系统。

5. 在“要更新的组（预览）”区域，定义组合了订阅、资源组、位置和标记的查询，以生成要在部署中加入的 Azure VM 动态组。 要了解详细信息，请参阅[将动态组与更新管理配合使用](update-mgmt-groups.md)。

    > [!NOTE]
    > 如果选择了 Azure VM 或启用了 Arc 的服务器，则此选项不可用。 计算机将自动针对计划的部署。

6. 在“要更新的计算机”区域，选择已保存的搜索、已导入的组或者从下拉菜单中选择“计算机”并选择各个计算机 。 通过此选项可以查看每台计算机的 Log Analytics 代理的准备情况。 若要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../../azure-monitor/platform/computer-groups.md)。

    > [!NOTE]
    > 如果选择了 Azure VM 或启用了 Arc 的服务器，则此选项不可用。 计算机将自动针对计划的部署。

7. 使用“更新分类”区域为产品指定[更新分类](update-mgmt-view-update-assessments.md#work-with-update-classifications)。 对于每个产品，请取消选择所有受支持的更新分类，但要包含在更新部署中的分类除外。

    如果你的部署打算只应用一组选定的更新，那么在配置“包含/排除更新”选项时，有必要取消选择所有预先选择的更新分类，如下一步所述。 这将确保在目标计算机上只安装你指定添加在此部署中的更新。

8. 使用“包含/排除更新”区域可从部署中添加或排除选定的更新。 在“包括/排除”页上，你可以输入知识库文章 ID 号来进行添加或排除。

   > [!IMPORTANT]
   > 请记住，排除项会替代包含项。 例如，如果定义了 `*` 排除规则，更新管理会从安装中排除所有修补程序或包。 已排除的修补程序仍显示为计算机缺少此项。 对于 Linux 计算机，如果包含一个具有依赖包的包，而该依赖包已被排除，则更新管理不会安装主包。

   > [!NOTE]
   > 你不能指定将已被取代的更新包含在更新部署中。

9. 选择“计划设置”。 默认开始时间为晚于当前时间 30 分钟。 可以将开始时间设置为 10 分钟之后的任何将来时间。

    > [!NOTE]
    > 如果选择了启用了 Arc 的服务器，此选项将有所不同。 你可以选择 " **立即更新** " 或在未来20分钟开始时间。

10. 使用 " **重复周期** " 指定部署是发生一次还是使用定期计划，然后选择 **"确定"**。

11. 在“前脚本 + 后脚本(预览)”区域，选择要在部署前和部署后运行的脚本。 若要了解详细信息，请参阅[管理前脚本和后脚本](update-mgmt-pre-post-scripts.md)。

12. 使用“维护时段(分钟)”字段来指定安装更新的允许时长。 指定维护时段时，请考虑以下详细信息：

    * 维护时段控制安装的更新数量。
    * 如果维护时段即将结束，更新管理不会停止安装新的更新。
    * 如果超出了维护时段，更新管理不会终止正在进行的更新。
    * 如果超出了 Windows 上的维护时段，通常是因为服务包更新需要很长时间才能安装。

    > [!NOTE]
    > 若要避免在 Ubuntu 上的维护时段外应用更新，请重新配置 `Unattended-Upgrade` 包，禁用自动更新。 有关如何配置此包的信息，请参阅 [Ubuntu Server 指南中的自动更新主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

13. 使用“重新启动选项”字段指定部署过程中处理重新启动的方式。 提供了以下选项： 
    * 必要时重新启动（默认）
    * 永远重启
    * 永不重启
    * 仅重新启动；此选项不安装更新

    > [!NOTE]
    > 如果“重新启动选项”设置为“永不重新启动”，则[用于管理重启的注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)下列出的注册表项可能会导致重新启动事件 。

14. 配置部署计划后，选择“创建”。

    ![更新“计划设置”窗格](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > 为启用了所选 Arc 的服务器配置完部署计划后，请选择 " **查看 + 创建**"。

15. 此时会回到状态仪表板。 选择 " **部署计划** " 以显示已创建的部署计划。

## <a name="schedule-an-update-deployment-programmatically"></a>以编程方式计划更新部署

若要了解如何使用 REST API 创建更新部署，请参阅[软件更新配置 - 创建](/rest/api/automation/softwareupdateconfigurations/create)。

可以使用示例 runbook 创建每周更新部署。 若要了解有关此 Runbook 的详细信息，请参阅[为资源组中的一个或多个 VM 创建每周更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)。

## <a name="check-deployment-status"></a>检查部署状态

在计划的部署开始后，可以在 "**更新管理**" 下的 "**历史记录**" 选项卡上查看其状态。 部署当前正在运行时，其状态为“正在进行”。 部署成功完成后，状态将更改为“成功”。 如果部署中的一个或多个更新失败，则状态为 " **失败**"。

## <a name="view-results-of-a-completed-update-deployment"></a>查看已完成的更新部署的结果

部署完成后，可选择它以查看其结果。

[ ![特定部署的更新部署状态仪表板](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

“更新结果”下的摘要提供了目标 VM 上的更新和部署结果的总数。 右侧的表显示了更新的细目以及每个更新的安装结果。

可用值有：

* **未尝试** - 由于定义的维护时段时长不足，因为而未安装该更新。
* **未选择** - 未选择要部署的更新。
* **成功** - 更新成功。
* **失败** - 更新失败。

选择“所有日志”来查看部署创建的所有日志条目。

选择“输出”，查看负责管理目标 VM 更新部署的 runbook 的作业流。

若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="next-steps"></a>后续步骤

若要了解如何创建警报来就更新部署结果进行通知，请参阅[为更新管理创建警报](update-mgmt-configure-alerts.md)。