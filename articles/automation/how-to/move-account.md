---
title: 将 Azure 自动化帐户移到另一个订阅
description: 本文介绍如何将你的自动化帐户移到另一个订阅
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225954"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>将 Azure 自动化帐户移到另一个订阅

Azure 提供了一些资源移动到新的资源组或订阅的功能。 可以移动通过 Azure 门户、 PowerShell、 Azure CLI 或 REST API 的资源。 若要了解有关该过程的详细信息，请参阅[将资源移到新的资源组或订阅](../../azure-resource-manager/resource-group-move-resources.md)。 

Azure 自动化帐户是一个可以移动的资源。 在本文中，您将学习将自动化帐户移到另一个资源或订阅的步骤。

移动自动化帐户中的高级步骤有：

1. 删除你的解决方案。
2. 取消链接工作区。
3. 移动自动化帐户。
4. 删除并重新创建运行方式帐户。
5. 重新启用你的解决方案。

## <a name="remove-solutions"></a>删除解决方案

若要取消链接工作区从自动化帐户，必须从你的工作区中删除这些解决方案：
- **更改跟踪和清单**
- **更新管理** 
- **在空闲时间启动/停止 Vm** 

在资源组中，找到每个解决方案并选择**删除**。 上**删除资源**页上，确认要删除，并且选择的资源**删除**。

![Azure 门户中删除解决方案](../media/move-account/delete-solutions.png)

您可以实现相同的任务[Remove-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet，如以下示例所示：

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>有关启动/停止 Vm 的其他步骤

有关**启动/停止 Vm**解决方案中，您还需要删除解决方案所创建的警报规则。

在 Azure 门户中，转到你的资源组并选择**监视** > **警报** > **管理警报规则**。

![警报页显示所选内容的管理警报规则](../media/move-account/alert-rules.png)

上**规则**页上，你应看到该资源组中配置的警报的列表。 **启动/停止 Vm**解决方案会创建三个警报规则：

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

选择这三个警报规则，并选择**删除**。 此操作将删除这些警报规则。

![请求确认删除所选规则的规则页](../media/move-account/delete-rules.png)

> [!NOTE]
> 如果看不到任何警报规则在**规则**页上，更改**状态**以显示**禁用**警报，因为你可能已禁用它们。

删除警报规则，删除操作组创建有关**启动/停止 Vm**解决方案通知。

在 Azure 门户中，选择**监视器** > **警报** > **管理操作组**。

选择**StartStop_VM_Notification**从列表中。 在操作组页上选择**删除**。

![操作组页上，选择删除](../media/move-account/delete-action-group.png)

同样，可以使用 PowerShell 删除操作组[Remove-azurermactiongroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet，如以下示例中所示：

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消链接工作区

在 Azure 门户中，选择**自动化帐户** > **相关资源** > **链接工作区**。 选择**取消链接工作区**取消链接自动化帐户从工作区。

![取消链接自动化帐户中的工作区](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移动自动化帐户

删除上一项之后, 可以继续删除自动化帐户和它的 runbook。 在 Azure 门户中，浏览到你的自动化帐户的资源组。 选择**移动** > **移到另一个订阅**。

![资源组页上，移动到另一个订阅](../media/move-account/move-resources.png)

选择你想要移动的资源组中的资源。 请确保您包括您**自动化帐户**， **Runbook**，并**Log Analytics 工作区**资源。

在移动操作完成后，还有使一切正常工作所需的其他步骤。

## <a name="re-create-run-as-accounts"></a>重新创建运行方式帐户

[运行方式帐户](../manage-runas-account.md)在使用 Azure 资源进行身份验证的 Azure Active Directory 中创建服务主体。 更改订阅时，自动化帐户将不再使用现有运行方式帐户。

转到新的订阅中的自动化帐户并选择**运行方式帐户**下**帐户设置**。 你将看到，现在的运行方式帐户显示未完成。

![运行方式帐户不完整](../media/move-account/run-as-accounts.png)

选择每个运行方式帐户。 上**属性**页上，选择**删除**删除运行方式帐户。

> [!NOTE]
> 如果还没有创建或查看运行方式帐户的权限，您将看到以下消息：`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 若要了解如何配置运行方式帐户所需的权限，请参阅[所需权限以配置运行方式帐户](../manage-runas-account.md#permissions)。

删除运行方式帐户后，选择**创建**下**Azure 运行方式帐户**。 上**添加 Azure 运行方式帐户**页上，选择**创建**创建运行方式帐户和服务主体。 重复上述步骤与**Azure 经典运行方式帐户**。

## <a name="enable-solutions"></a>启用解决方案

重新创建运行方式帐户后，将重新启用之前移动删除的解决方案。 若要开启**更改跟踪和清单**并**更新管理**，选择自动化帐户中的相应功能。 选择在 Log Analytics 工作区，然后选择**启用**。

![重新启用已移动自动化帐户中的解决方案](../media/move-account/reenable-solutions.png)

连接现有的 Log Analytics 工作区，使用你的解决方案载入的计算机将会显示。

若要开启**启动/停止 Vm**在非工作时间的解决方案，你将需要重新部署解决方案。 下**相关的资源**，选择**启动/停止 Vm** > **了解详细信息，并启用该解决方案** > **创建**以开始部署。

上**将解决方案添加**页上，选择你的 Log Analytics 工作区和自动化帐户。  

![添加解决方案菜单](../media/move-account/add-solution-vm.png)

配置解决方案的详细说明，请参阅[在 Azure 自动化中在非工作时间启动/停止 Vm](../automation-solution-vm-management.md)。

## <a name="post-move-verification"></a>移动后验证

移动完成后，检查下面列出的任务应验证：

|功能|测试数|故障排除链接|
|---|---|---|
|Runbook|Runbook 可以成功运行，并连接到 Azure 资源。|[排查 Runbook 问题](../troubleshoot/runbooks.md)
| 源代码管理|可以在源代码管理存储库上运行手动同步。|[源代码管理集成](../source-control-integration.md)|
|更改跟踪和清单|验证计算机中看到新的清单数据。|[对更改跟踪进行故障排除](../troubleshoot/change-tracking.md)|
|更新管理|验证您看到您的机而且它们正常运行。</br>运行测试软件更新部署。|[更新管理故障排除](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>后续步骤

若要了解有关在 Azure 中移动资源的详细信息，请参阅[在 Azure 中移动资源](../../azure-resource-manager/move-support-resources.md)。
