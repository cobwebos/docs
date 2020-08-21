---
title: 如何为 Azure 自动化更新管理创建更新部署
description: 本文介绍如何计划更新部署并查看其状态。
services: automation
ms.subservice: update-management
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 4336ba272dd83ad2a35060c1c7524a564b928484
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717687"
---
# <a name="how-to-deploy-updates-and-review-results"></a>如何部署更新和查看结果

本文介绍如何计划更新部署并在部署完成后查看执行过程。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>计划更新部署

计划某个更新部署会创建链接到 Patch-MicrosoftOMSComputers Runbook 的[计划](../shared-resources/schedules.md)资源，该 Runbook 处理目标计算机上的更新部署。 必须计划一个遵循发布时间和服务时段的部署，以便安装更新。 你可以选择要在部署中包括的更新类型。 例如，可包括关键或安全更新，排除更新汇总。

>[!NOTE]
>如果在创建部署后通过 Azure 门户或 PowerShell 删除了该计划资源，该删除操作会破坏计划的更新部署，在你尝试通过门户重新配置该计划资源时会出现错误。 只能通过删除相应的部署计划来删除计划资源。  

要计划新的更新部署，请执行以下操作：

1. 在自动化帐户中，转到“更新管理”下的“更新管理”，然后选择“安排更新部署”  。

2. 在“新建更新部署”下的“名称”字段中，输入部署的唯一名称 。

3. 选择更新部署的目标操作系统。

4. 在“要更新的组（预览）”区域，定义组合了订阅、资源组、位置和标记的查询，以生成要在部署中加入的 Azure VM 动态组。 要了解详细信息，请参阅[将动态组与更新管理配合使用](update-mgmt-groups.md)。

5. 在“要更新的计算机”区域，选择已保存的搜索、已导入的组或者从下拉菜单中选择“计算机”并选择各个计算机 。 通过此选项可以查看每台计算机的 Log Analytics 代理的准备情况。 若要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../../azure-monitor/platform/computer-groups.md)。

6. 使用“更新分类”区域为产品指定[更新分类](update-mgmt-view-update-assessments.md#work-with-update-classifications)。 对于每个产品，请取消选择所有受支持的更新分类，但要包含在更新部署中的分类除外。

    如果你的部署只是要应用一组选择的更新，则必须在配置 " **包括/排除更新** " 选项时取消选择所有预选的更新分类，如下一步中所述。 这可确保目标计算机上仅安装已指定要 *包括* 在此部署中的更新。

7. 使用 " **包括/排除更新** " 区域可以在部署中添加或排除所选更新。 在 " **包括/排除** " 页上，输入要包括或排除的知识库文章 ID 号。

   > [!IMPORTANT]
   > 请记住，排除项会替代包含项。 例如，如果定义了 `*` 排除规则，更新管理会从安装中排除所有修补程序或包。 已排除的修补程序仍显示为计算机缺少此项。 对于 Linux 计算机，如果包含一个具有依赖包的包，而该依赖包已被排除，则更新管理不会安装主包。

   > [!NOTE]
   > 你不能指定将已被取代的更新包含在更新部署中。

8. 选择“计划设置”。 默认开始时间为晚于当前时间 30 分钟。 可以将开始时间设置为 10 分钟之后的任何将来时间。

9. 使用“重复周期”字段指定是部署一次还是定期部署，然后选择“确定” 。

10. 在“前脚本 + 后脚本(预览)”区域，选择要在部署前和部署后运行的脚本。 若要了解详细信息，请参阅[管理前脚本和后脚本](update-mgmt-pre-post-scripts.md)。
    
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

13. 配置部署计划后，选择“创建”。

    ![更新“计划设置”窗格](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. 此时会回到状态仪表板。 选择“计划的更新部署”以显示创建的部署计划。

## <a name="schedule-an-update-deployment-programmatically"></a>以编程方式计划更新部署

若要了解如何使用 REST API 创建更新部署，请参阅[软件更新配置 - 创建](/rest/api/automation/softwareupdateconfigurations/create)。

可以使用示例 runbook 创建每周更新部署。 若要了解有关此 Runbook 的详细信息，请参阅[为资源组中的一个或多个 VM 创建每周更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)。

## <a name="check-deployment-status"></a>检查部署状态

在计划的部署开始后，可以在“更新管理”下的“更新部署”选项卡上查看其状态 。 部署当前正在运行时，其状态为“正在进行”。 部署成功完成后，状态将更改为“成功”。 如果部署中有一个或多个更新失败，状态将是“部分失败”。

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