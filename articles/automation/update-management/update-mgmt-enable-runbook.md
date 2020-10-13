---
title: 从 runbook 中启用 Azure 自动化更新管理
description: 本文介绍如何从 runbook 启用更新管理。
services: automation
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: 870345d244a7f23011c900154e1c486e039a6d8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854773"
---
# <a name="enable-update-management-from-a-runbook"></a>从 runbook 启用“更新管理”

本文介绍如何使用 runbook 为环境中的 VM 启用[更新管理](update-mgmt-overview.md)功能。 若要大规模启用 Azure Vm，必须启用更新管理的现有 VM。

> [!NOTE]
> 在启用更新管理时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

此方法使用两个 runbook：

* **Enable-multiplesolution** -主要 runbook，用于提示输入配置信息，查询指定的 VM 并执行其他验证检查，然后调用 **AutomationSolution** runbook 为指定资源组中的每个 VM 配置更新管理。
* **AutomationSolution** -为在目标资源组中指定的一个或多个 vm 启用更新管理。 它将验证是否满足先决条件，验证是否已安装并安装 Log Analytics VM 扩展（如果找不到），然后将 Vm 添加到链接到自动化帐户的指定 Log Analytics 工作区中的范围配置。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../automation-security-overview.md)。
* [Log Analytics 工作区](../../azure-monitor/platform/design-logs-deployment.md)
* [虚拟机](../../virtual-machines/windows/quick-create-portal.md)。
* **AutomationSolution** runbook 使用的两个自动化资产。 如果自动化帐户中尚不存在此 runbook，则此 runbook 会在其首次运行期间自动导入 **enable-multiplesolution** runbook。
    * *LASolutionSubscriptionId*： Log Analytics 工作区所在位置的订阅 ID。
    * *LASolutionWorkspaceId*：链接到自动化帐户的 Log Analytics 工作区的工作区 ID。

    这些变量用于配置载入 VM 的工作区。 如果未指定这些项，脚本将首先搜索要在其订阅中更新管理的任何 VM 载入，后跟自动化帐户所在的订阅，然后是用户帐户有权访问的所有其他订阅。 如果未正确配置，则可能会导致计算机载入到某些随机 Log Analytics 工作区。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-update-management"></a>启用更新管理

1. 在 Azure 门户中，导航到 " **Automation 帐户**"。 在 " **自动化帐户** " 页上，从列表中选择你的帐户。

2. 在自动化帐户中，选择“更新管理”下的“更新管理”。

3. 选择 Log Analytics 工作区，然后单击“启用”。 启用更新管理时，将显示标题。

    ![启用更新管理](media/update-mgmt-enable-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>安装和更新模块

需要将其更新到最新的 Azure 模块，并导入 [microsoft.operationalinsights](/powershell/module/az.operationalinsights) 模块，才能成功地通过 Runbook 为 vm 启用更新管理。

1. 在你的自动化帐户中的“共享资源”下选择“模块” 。

2. 选择“更新 Azure 模块”以更新到最新版本的 Azure 模块。

3. 单击“是”，将所有现有 Azure 模块更新到最新版本。

    ![更新模块](media/update-mgmt-enable-runbook/update-modules.png)

4. 返回“共享资源”下的“模块” 。

5. 选择“浏览库”以打开模块库。

6. 搜索 `Az.OperationalInsights` 并将此模块导入到自动化帐户中。

    ![导入 OperationalInsights 模块](media/update-mgmt-enable-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>选择要管理的 Azure VM

启用更新管理后，可以添加 Azure VM 以接收更新。

1. 在自动化帐户中，选择 "**更新管理**" 部分下的 "**更新管理**"。

2. 选择“添加 Azure VM”以添加你的 VM。

3. 从列表中选择 VM，然后单击 " **启用** " 以配置要管理的 vm。

   ![为 VM 启用更新管理](media/update-mgmt-enable-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > 如果在完成更新管理设置之前尝试启用另一个功能，则会收到以下消息：`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>导入 runbook 以启用更新管理

1. 在你的自动化帐户中的“流程自动化”下选择“Runbook”。

2. 选择“浏览库”。

3. 搜索 **更新和更改跟踪**。

4. 选择 runbook，然后单击 "**查看源**" 页上的 "**导入**"。

5. 单击“确定”，将 Runbook 导入到自动化帐户中。

   ![导入 Runbook 进行设置](media/update-mgmt-enable-runbook/import-from-gallery.png)

6. 在 " **Runbook** " 页上，选择 " **enable-multiplesolution** " runbook，然后单击 " **编辑**"。 在文本编辑器中，选择 "  **发布**"。

7. 出现确认提示时，单击 **"是"** 以发布 runbook。

## <a name="start-the-runbook"></a>启动 Runbook

必须已为 Azure VM 启用了更新管理，才能启动此 runbook。 它需要一个已启用该功能的现有 VM 和资源组，以便在目标资源组中配置一个或多个 Vm。

1. 打开“Enable-MultipleSolution”Runbook。

   ![多个解决方案 Runbook](media/update-mgmt-enable-runbook/runbook-overview.png)

2. 单击启动按钮，在以下字段中输入参数值：

   * VMNAME - 要添加到更新管理中的现有 VM 的名称。 将此字段留空可添加资源组中的所有 VM。
   * **VMRESOURCEGROUP** - 要启用的 VM 的资源组的名称。
   * **SUBSCRIPTIONID** - 要启用的新 VM 的订阅 ID。 将此字段留空可使用工作区的订阅。 使用不同的订阅 ID 时，请添加自动化帐户的运行方式帐户作为订阅的参与者。
   * ALREADYONBOARDEDVM - 已为更新手动启用的 VM 的名称。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所属的资源组的名称。
   * SOLUTIONTYPE - 输入“更新”。

   ![Enable-MultipleSolution Runbook 参数](media/update-mgmt-enable-runbook/runbook-parameters.png)

3. 选择“确定”启动 Runbook 作业。

4. 从 " **作业** " 页中监视 runbook 作业和任何错误的进度。

## <a name="next-steps"></a>后续步骤

* 若要对 VM 使用更新管理，请参阅[管理 VM 的更新和修补程序](update-mgmt-manage-updates-for-vm.md)。

* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](../troubleshoot/update-management.md)。
