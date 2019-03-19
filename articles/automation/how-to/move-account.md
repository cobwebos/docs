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
ms.openlocfilehash: 01f3995a80375f2deada13c36c48600243a17623
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732787"
---
# <a name="move-your-automation-account-to-another-subscription"></a>将你的自动化帐户移到另一个订阅

Azure 提供了将某些资源移到新资源组或订阅与相同的租户以本机方式通过 Azure 门户、 PowerShell、 Azure CLI 或 REST API 的功能。 若要了解有关该过程的详细信息，请参阅[将资源移到新的资源组或订阅](../../azure-resource-manager/resource-group-move-resources.md)。 自动化帐户是一个可以移动的资源，但有移动自动化帐户时所需的特殊步骤。

移动到自动化帐户的高级步骤有：

* 删除你的解决方案
* 取消链接工作区
* 移动自动化帐户
* 删除并重新创建运行方式帐户
* 重新启用你的解决方案

## <a name="remove-solutions"></a>删除解决方案

若要取消链接工作区从自动化帐户、 更改和清单，更新管理，并在空闲时间解决方案启动/停止 Vm 必须删除从工作区。

在资源组中，选择每个**解决方案**然后单击**删除**。 上**删除资源**页上，确认资源被删除，然后单击**删除**。

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

有关**启动/停止 VM**解决方案中，您还需要删除解决方案所创建的警报规则。

在 Azure 门户中，导航到你的资源组并选择**警报**下**监视**。 上**警报**页上，选择**管理警报规则**

![警报页面，其中显示你单击管理警报规则](../media/move-account/alert-rules.png)

上**规则**页上，你应看到所有在该资源组中配置的警报的列表。 **启动/停止 Vm**解决方案创建 3 个警报规则

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

选择这些 3 个警报规则，然后单击**删除**。 此操作将删除这些警报规则。

![规则具有规则所选页面并被删除](../media/move-account/delete-rules.png)

> [!NOTE]
> 如果没有上看到任何警报规则**规则**页上，更改**状态**以显示**禁用**警报可能禁用了它们。

警报规则删除后，需要删除操作组创建的启动/停止 VM 解决方案的通知。

在 Azure 门户中，转到**监视器**，选择**警报**，然后单击**管理操作组**。

从列表中选择操作组，它将具有名称**StartStop_VM_Notification**。 在操作组页中，单击**删除**

![操作组页上，单击删除](../media/move-account/delete-action-group.png)

同样，可以删除与 PowerShell 操作组。 此操作通过[Remove-azurermactiongroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet，如以下示例中所示：

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消链接工作区

在 Azure 门户中，转到您**自动化帐户**。 下**相关的资源**，单击**链接工作区**。 单击**取消链接工作区**取消链接自动化帐户从工作区。

![取消链接自动化帐户中的工作区](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移动自动化帐户

一旦已删除以前的所有项，可以继续删除自动化帐户和它的 runbook。 在 Azure 门户中，导航到自动化帐户的资源组。 选择**移动**，然后**移到另一个订阅**。

![资源组页上选择移动到另一个订阅](../media/move-account/move-resources.png)

选择你想要移动的资源组中的资源。 请确保您包括您**自动化帐户**， **Runbook**，并**Log Analytics 工作区**资源。

移动完成后，有必须采取措施让一切工作的其他步骤。

## <a name="recreate-run-as-accounts"></a>重新运行方式帐户创建

[运行方式帐户](../manage-runas-account.md)在使用 Azure 资源进行身份验证的 Azure Active Directory 中创建服务主体。 更改订阅时，现有运行方式帐户不再可用的自动化帐户。

导航到自动化帐户中的新订阅，并选择**运行方式帐户**下**帐户设置**。 你将看到，现在运行方式帐户显示未完成。

![运行方式帐户显示为不完整](../media/move-account/run-as-accounts.png)

单击每个运行方式帐户，在**属性**页上，单击**删除**删除运行方式帐户。

> ![注意]如果您没有权限来创建或查看您将看到以下消息的运行方式帐户`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`。 若要了解如何配置运行方式帐户所需的权限，请参阅[所需权限以配置运行方式帐户](../manage-runas-account.md#permissions)。

运行方式帐户删除后，单击**创建**上**Azure 运行方式帐户**。 上**添加 Azure 运行方式帐户**页上，单击**创建**创建运行方式帐户和服务主体。 重复上述步骤与**Azure 经典运行方式帐户**。

## <a name="enable-solutions"></a>启用解决方案

已重新创建运行方式帐户后，将重新启用你在移动之前删除的解决方案。 若要启用**更改跟踪和清单**并**更新管理**，选择自动化帐户中的相应功能。 选择在 Log Analytics 工作区，然后单击**启用**。

![重新启用已移动自动化帐户中的解决方案](../media/move-account/reenable-solutions.png)

使用你的解决方案载入你的计算机会再次显示由于您正在连接现有的 Log Analytics 工作区。

若要在非工作时间期间重新启用启动/停止 Vm，你将需要重新部署解决方案。 下**相关的资源**，选择**启动/停止 VM**。 单击**了解详细信息，并启用该解决方案**然后单击**创建**以开始部署。

上**将解决方案添加**页上，选择你的 Log Analytics 工作区和自动化帐户。  

![运行方式帐户显示为不完整](../media/move-account/add-solution-vm.png)

配置解决方案的详细说明，请参阅[在 Azure 自动化中在非工作时间启动/停止 Vm](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>开机自检移动验证

移动完成后，请务必验证以确保一切按预期方式在自动化帐户中的不同方案。 下表显示了一系列移动完成后应验证的任务：

|功能|测试数|故障排除链接|
|---|---|---|
|Runbook|Runbook 可以成功运行，并连接到 Azure 资源。|[排查 Runbook 问题](../troubleshoot/runbooks.md)
| 源代码管理|可以在源代码管理存储库上运行手动同步。|[源代码管理集成](../source-control-integration.md)|
|更改跟踪和库存|验证计算机中看到新的清单数据。|[对更改跟踪进行故障排除](../troubleshoot/change-tracking.md)|
|更新管理|请参阅您计算机和它们的正常验证</br>运行测试软件更新部署。|[更新管理故障排除](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>后续步骤

若要了解有关在 Azure 中移动资源的详细信息，请参阅[在 Azure 中移动资源](../../azure-resource-manager/move-support-resources.md)
