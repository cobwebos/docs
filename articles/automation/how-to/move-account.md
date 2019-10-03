---
title: 将你的 Azure 自动化帐户移到另一个订阅
description: 本文介绍如何将自动化帐户移到另一个订阅
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8187e4c6f2c7dc721c178bad50b6c3ada2a65367
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717230"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>将你的 Azure 自动化帐户移到另一个订阅

Azure 使你能够将某些资源移动到新的资源组或订阅。 可以通过 Azure 门户、PowerShell、Azure CLI 或 REST API 移动资源。 若要了解有关此过程的详细信息, 请参阅[将资源移到新的资源组或订阅](../../azure-resource-manager/resource-group-move-resources.md)。

Azure 自动化帐户是可移动的资源之一。 在本文中, 你将了解将自动化帐户移动到其他资源或订阅的步骤。

移动自动化帐户的高级步骤如下:

1. 删除解决方案。
2. 取消链接工作区。
3. 移动自动化帐户。
4. 删除并重新创建运行方式帐户。
5. 重新启用解决方案。

## <a name="remove-solutions"></a>删除解决方案

若要取消工作区与自动化帐户的链接, 必须从工作区中删除这些解决方案:
- **更改跟踪和清单**
- **更新管理**
- **在非工作时间启动/停止 Vm**

在资源组中, 找到每个解决方案, 然后选择 "**删除**"。 在 "**删除资源**" 页上, 确认要删除的资源, 然后选择 "**删除**"。

![从 Azure 门户中删除解决方案](../media/move-account/delete-solutions.png)

可以通过[move-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet 完成同一任务, 如以下示例中所示:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>启动/停止 Vm 的其他步骤

对于 "**启动/停止 vm** " 解决方案, 还需要删除解决方案创建的警报规则。

在 Azure 门户中, 请前往资源组, 并选择 "**监视** > **警报** > " "**管理警报规则**"。

![显示管理警报规则选择的警报页](../media/move-account/alert-rules.png)

在 "**规则**" 页上, 应会看到该资源组中配置的警报的列表。 "**启动/停止 vm** " 解决方案创建三个警报规则:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

选择这三个警报规则, 然后选择 "**删除**"。 此操作将删除这些警报规则。

![请求确认删除所选规则的规则页面](../media/move-account/delete-rules.png)

> [!NOTE]
> 如果在 "**规则**" 页上看不到任何警报规则, 则更改**状态**以显示**禁用**的警报, 因为可能已禁用了警报。

删除警报规则后, 删除为 "**启动/停止 vm**解决方案" 通知创建的操作组。

在 Azure 门户中, 选择 "**监视** > " "**警报** > " "**管理操作组**"。

从列表中选择 " **StartStop_VM_Notification** "。 在 "操作组" 页上, 选择 "**删除**"。

![操作组页上, 选择 "删除"](../media/move-account/delete-action-group.png)

同样, 你可以使用 PowerShell 和[AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet 删除操作组, 如以下示例中所示:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消链接工作区

在 Azure 门户中, 选择 "**自动化帐户** > **相关资源** > "**链接工作区**。 选择 "**取消链接工作区**" 以从自动化帐户取消链接工作区。

![取消工作区与自动化帐户的链接](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移动自动化帐户

删除以前的项目后, 你可以继续删除你的自动化帐户及其 runbook。 在 Azure 门户中, 浏览到自动化帐户的资源组。 选择 "**移动** > **到另一个订阅**"。

![资源组页, 移到另一个订阅](../media/move-account/move-resources.png)

选择要移动的资源组中的资源。 确保包括你的**自动化帐户**、 **Runbook**和**Log Analytics 工作区**资源。

移动完成后, 还需要执行其他步骤才能完成所有工作。

## <a name="re-create-run-as-accounts"></a>重新创建运行方式帐户

[运行方式帐户](../manage-runas-account.md)在 Azure Active Directory 中创建一个服务主体, 以便向 Azure 资源进行身份验证。 更改订阅后, Automation 帐户将不再使用现有的运行方式帐户。

在新订阅中转到自动化帐户, 并选择 "**帐户设置**" 下的 "**运行方式帐户**"。 你会看到运行方式帐户现在显示为 "未完成"。

![运行方式帐户不完整](../media/move-account/run-as-accounts.png)

选择每个运行方式帐户。 在 "**属性**" 页上, 选择 "**删除**" 以删除运行方式帐户。

> [!NOTE]
> 如果你没有创建或查看运行方式帐户的权限, 你将看到以下消息:`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`若要了解配置运行方式帐户所需的权限, 请参阅[配置运行方式帐户所需的权限](../manage-runas-account.md#permissions)。

删除运行方式帐户后, 请在 " **Azure 运行方式帐户**" 下选择 "**创建**"。 在 "**添加 Azure 运行方式帐户**" 页上, 选择 "**创建**" 以创建运行方式帐户和服务主体。 在**Azure 经典运行方式帐户**中重复上述步骤。

## <a name="enable-solutions"></a>启用解决方案

重新创建运行方式帐户后, 你将重新启用在移动之前删除的解决方案。 若要打开**更改跟踪和清单**并**更新管理**, 请在自动化帐户中选择相应的功能。 选择你已移动的 "Log Analytics" 工作区, 然后选择 "**启用**"。

![重新启用移动的自动化帐户中的解决方案](../media/move-account/reenable-solutions.png)

当你连接现有 Log Analytics 工作区时, 将显示与你的解决方案载入的计算机。

若要打开 "在非工作时间**启动/停止 vm** " 解决方案, 需要重新部署解决方案。 在 "**相关资源**" 下, 选择 "**启动/停止 vm** > "**了解有关的详细信息并启用解决方案** > **创建**以启动部署。

在 "**添加解决方案**" 页上, 选择 Log Analytics 工作区和自动化帐户。

![添加解决方案菜单](../media/move-account/add-solution-vm.png)

有关配置解决方案的详细说明, 请参阅[在 Azure 自动化中的非工作时间启动/停止 vm 解决方案](../automation-solution-vm-management.md)。

## <a name="post-move-verification"></a>后移动验证

移动完成后, 检查应验证的以下任务列表:

|能力|测试|疑难解答链接|
|---|---|---|
|runbook|Runbook 可以成功运行并连接到 Azure 资源。|[排查 Runbook 问题](../troubleshoot/runbooks.md)
|源代码管理|你可以在源代码管理存储库上运行手动同步。|[源代码管理集成](../source-control-integration.md)|
|更改跟踪和清单|验证你是否看到了计算机的当前清单数据。|[更改跟踪疑难解答](../troubleshoot/change-tracking.md)|
|更新管理|验证你的计算机是否正常运行。</br>运行测试软件更新部署。|[更新管理疑难解答](../troubleshoot/update-management.md)|
|共享资源|验证你是否看到了所有共享资源, 例如[凭据](../shared-resources/credentials.md)、[变量](../shared-resources/variables.md)等。|

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Azure 中移动资源, 请参阅[在 azure 中移动资源](../../azure-resource-manager/move-support-resources.md)。
