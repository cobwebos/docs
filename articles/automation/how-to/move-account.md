---
title: 将 Azure 自动化帐户移动到其他订阅
description: 本文介绍如何将自动化帐户移动到其他订阅。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681895"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>将 Azure 自动化帐户移动到其他订阅

Azure 自动化允许您将某些资源移动到新的资源组或订阅。 您可以通过 Azure 门户、PowerShell、Azure CLI 或 REST API 移动资源。 要了解有关该过程的更多信息，请参阅[将资源移动到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

Azure 自动化帐户是可以移动的资源之一。 在本文中，您将学习将自动化帐户移动到其他资源或订阅。 移动自动化帐户的高级步骤包括：

1. 删除您的解决方案。
2. 取消链接工作区。
3. 移动自动化帐户。
4. 删除并重新创建"以运行为帐户"。"帐户。
5. 重新启用解决方案。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="remove-solutions"></a>删除解决方案

要从自动化帐户取消链接工作区，必须从工作区中删除这些解决方案：

- 更改跟踪和库存
- 更新管理
- 在非工作时间启动/停止 VM

1. 在 Azure 门户中查找资源组。
2. 查找每个解决方案，然后单击"删除资源"页上的 **"删除**"。

    ![从 Azure 门户中删除解决方案](../media/move-account/delete-solutions.png)

    如果您愿意，可以使用["删除-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) cmdlet"删除解决方案：

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>删除工作时间解决方案期间启动/停止 VM 的警报规则

对于在非工作时间解决方案中启动/停止 VM，还需要删除解决方案创建的警报规则。

1. 在 Azure 门户中，转到资源组并选择 **"监视** > **警报** > **管理警报规则**"。

![显示"管理警报"规则选择的警报页](../media/move-account/alert-rules.png)

2. 在"规则"页上，您应该看到该资源组中配置的警报的列表。 解决方案创建以下规则：

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 一次选择一个规则，然后单击 **"删除**"以删除它们。

    ![规则页请求确认所选规则的删除](../media/move-account/delete-rules.png)

    > [!NOTE]
    > 如果在"规则"页上看不到任何警报规则，则将 **"状态**"字段更改为"已禁用"以显示已禁用的警报，因为您可能已禁用这些警报。

4. 删除警报规则时，必须删除在非工作时间解决方案通知期间为开始/停止 VM 创建的操作组。 在 Azure 门户中，选择 **"监视** > **警报** > **管理操作组**"。

5. 选择**StartStop_VM_Notification**。 

6. 在操作组页上，选择 **"删除**"。

    ![操作组页面](../media/move-account/delete-action-group.png)

    如果您愿意，可以使用["删除-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) cmdlet"删除操作组：

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>取消链接工作区

现在，您可以取消链接工作区：

1. 在 Azure 门户中，选择 **"自动化帐户** > **相关资源** > **链接工作区**"。 

2. 选择 **"取消链接工作区**"以从自动化帐户取消链接工作区。

    ![从自动化帐户取消链接工作区](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移动您的自动化帐户

您现在可以移动自动化帐户及其运行簿。 

1. 在 Azure 门户中，浏览到自动化帐户的资源组。 选择 **"移动到** > **其他订阅**"。

    ![资源组页面，移动到其他订阅](../media/move-account/move-resources.png)

2. 选择要移动的资源组中的资源。 确保您包括自动化帐户、运行簿和日志分析工作区资源。

## <a name="recreate-run-as-accounts"></a>重新创建"作为帐户运行"

[在](../manage-runas-account.md)Azure 活动目录中创建服务主体以使用 Azure 资源进行身份验证。 更改订阅时，自动化帐户不再使用现有的"运行为样"帐户。 要重新创建"以帐户身份运行"：

1. 转到新订阅中的自动化帐户，然后选择"**在帐户设置**下**作为帐户运行**"。 您将看到"运行为帐户"现在显示为不完整。

    ![运行帐户不完整](../media/move-account/run-as-accounts.png)

2. 使用"属性"页上的 **"删除**"按钮，一次删除"作为帐户运行"帐户一个。 

    > [!NOTE]
    > 如果您没有创建或查看"运行为帐户"的权限，请参阅以下消息：`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`要了解配置"运行为"帐户所需的权限，请参阅[配置"作为运行帐户"所需的权限](../manage-runas-account.md#permissions)。

3. 删除"以"身份运行帐户"后，选择"**在** **Azure 运行为帐户**下创建"。 

4. 在"添加 Azure 运行为帐户"页上，选择 **"创建**"以创建"以"以"身份运行"帐户和服务主体。 

5. 使用 Azure 经典运行作为帐户重复上述步骤。

## <a name="enable-solutions"></a>启用解决方案

重新创建"运行为帐户"后，必须重新启用在移动之前删除的解决方案： 

1. 要打开"更改跟踪和库存"解决方案，请在自动化帐户中选择"更改跟踪和库存"。 选择您移动的日志分析工作区，然后选择 **"启用**"。

2. 对更新管理解决方案重复步骤 1。

    ![在移动的自动化帐户中重新启用解决方案](../media/move-account/reenable-solutions.png)

3. 连接现有日志分析工作区后，使用解决方案随机操作的计算机可见。 要在非工作时间打开"开始/停止 VM"解决方案，必须重新部署解决方案。 在 **"相关资源**"下，选择 **"开始/停止 VM"** > **以了解有关详细信息并启用解决方案** > **"创建**"以启动部署。

4. 在"添加解决方案"页上，选择日志分析工作区和自动化帐户。

    ![添加解决方案菜单](../media/move-account/add-solution-vm.png)

5. 配置解决方案，如[Azure 自动化 中非工作时间解决方案中的开始/停止 VM 中](../automation-solution-vm-management.md)所述。

## <a name="verify-the-move"></a>验证移动

移动完成后，验证以下功能是否已启用。 

|功能|计数|疑难解答|
|---|---|---|
|Runbook|Runbook 可以成功运行并连接到 Azure 资源。|[排查 Runbook 问题](../troubleshoot/runbooks.md)
|源代码管理|您可以在源代码管理存储库上运行手动同步。|[源代码管理集成](../source-control-integration.md)|
|更改跟踪和库存|验证您是否从计算机中看到当前库存数据。|[故障排除更改跟踪](../troubleshoot/change-tracking.md)|
|更新管理|验证您看到计算机是否正常。</br>运行测试软件更新部署。|[故障排除更新管理](../troubleshoot/update-management.md)|
|共享资源|验证是否看到所有共享资源，如[凭据](../shared-resources/credentials.md)、[变量](../shared-resources/variables.md)等。|

## <a name="next-steps"></a>后续步骤

要了解有关在 Azure 中移动资源的更多信息，请参阅[在 Azure 中移动资源](../../azure-resource-manager/management/move-support-resources.md)。
