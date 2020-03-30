---
title: 将 Azure 自动化帐户移动到其他订阅
description: 本文介绍如何将自动化帐户移动到其他订阅
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969830"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>将 Azure 自动化帐户移动到其他订阅

Azure 使您能够将某些资源移动到新资源组或订阅。 您可以通过 Azure 门户、PowerShell、Azure CLI 或 REST API 移动资源。 要了解有关该过程的更多信息，请参阅[将资源移动到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

Azure 自动化帐户是可移动的资源之一。 在本文中，您将了解将自动化帐户移动到其他资源或订阅的步骤。

移动自动化帐户的高级步骤包括：

1. 删除您的解决方案。
2. 取消链接工作区。
3. 移动自动化帐户。
4. 删除并重新创建"以运行为帐户"。"帐户。
5. 重新启用解决方案。

## <a name="remove-solutions"></a>删除解决方案

要从自动化帐户取消链接工作区，必须从工作区中删除这些解决方案：
- **更改跟踪和库存**
- **更新管理**
- **在非工作时间启动/停止 VM**

在资源组中，查找每个解决方案并选择 **"删除**"。 在 **"删除资源"** 页上，确认要删除的资源，然后选择 **"删除**"。

![从 Azure 门户中删除解决方案](../media/move-account/delete-solutions.png)

您可以使用["删除 AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet"完成相同的任务，如以下示例所示：

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>启动/停止 VM 的其他步骤

对于 **"开始/停止 VM"** 解决方案，还需要删除解决方案创建的警报规则。

在 Azure 门户中，转到资源组并选择 **"监视** > **警报** > **管理警报规则**"。

![显示"管理警报"规则选择的警报页](../media/move-account/alert-rules.png)

在 **"规则"** 页上，您应该看到该资源组中配置的警报的列表。 **启动/停止 VM**解决方案创建三个警报规则：

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

选择这三个警报规则，然后选择 **"删除**"。 此操作将删除这些警报规则。

![规则页请求确认所选规则的删除](../media/move-account/delete-rules.png)

> [!NOTE]
> 如果在 **"规则"** 页上看不到任何警报规则，则更改**状态**以显示 **"已禁用**"警报，因为您可能已禁用这些警报。

删除警报规则后，删除为**开始/停止 VM**解决方案通知创建的操作组。

在 Azure 门户中，选择 **"监视** > **警报** > **管理操作组**"。

从列表中选择**StartStop_VM_Notification。** 在操作组页上，选择 **"删除**"。

![操作组页面，选择删除](../media/move-account/delete-action-group.png)

同样，您可以将 PowerShell 与[删除 AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet 一起使用来删除操作组，如以下示例所示：

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消链接工作区

在 Azure 门户中，选择 **"自动化帐户** > **相关资源** > **链接工作区**"。 选择 **"取消链接工作区**"以从自动化帐户取消链接工作区。

![从自动化帐户取消链接工作区](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移动您的自动化帐户

删除以前的项目后，可以继续删除自动化帐户及其运行簿。 在 Azure 门户中，浏览到自动化帐户的资源组。 选择 **"移动到** > **其他订阅**"。

![资源组页面，移动到其他订阅](../media/move-account/move-resources.png)

选择要移动的资源组中的资源。 确保您包括**您的自动化帐户****、Runbook**和**日志分析工作区**资源。

移动完成后，还需要执行其他步骤，以使一切正常工作。

## <a name="re-create-run-as-accounts"></a>重新创建"作为帐户运行"

[在](../manage-runas-account.md)Azure 活动目录中创建服务主体以使用 Azure 资源进行身份验证。 更改订阅时，自动化帐户不再使用现有的"运行为样"帐户。

转到新订阅中的自动化帐户，然后选择"**在帐户设置**下**作为帐户运行**"。 您将看到"运行为帐户"现在显示为不完整。

![运行帐户不完整](../media/move-account/run-as-accounts.png)

选择每个"以"身份运行"科目。 在 **"属性**"页上，选择 **"删除**"以删除"运行为帐户"。

> [!NOTE]
> 如果您没有创建或查看"运行为帐户"的权限，您将看到以下消息：`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`要了解配置"运行即"帐户所需的权限，请参阅[配置"作为运行帐户"所需的权限](../manage-runas-account.md#permissions)。

删除"运行为帐户"后，选择在**Azure 运行为帐户**下**创建**。 在"**添加 Azure 运行为帐户"** 页上，选择 **"创建**"以创建"以"以"身份运行"帐户和服务主体。 使用**Azure 经典运行作为帐户**重复上述步骤。

## <a name="enable-solutions"></a>启用解决方案

重新创建"运行为帐户"后，您将重新启用在移动之前删除的解决方案。 要打开 **"更改跟踪"和"库存**"和 **"更新管理**"，请在自动化帐户中选择相应的功能。 选择您移动的日志分析工作区，然后选择 **"启用**"。

![在移动的自动化帐户中重新启用解决方案](../media/move-account/reenable-solutions.png)

连接现有日志分析工作区后，使用解决方案随机操作的计算机将可见。

要在非工作时间解决方案期间打开 **"开始/停止 VM"，** 您需要重新部署解决方案。 在 **"相关资源**"下，选择 **"开始/停止 VM"** > **以了解有关详细信息并启用解决方案** > **"创建**"以启动部署。

在 **"添加解决方案**"页上，选择日志分析工作区和自动化帐户。

![添加解决方案菜单](../media/move-account/add-solution-vm.png)

有关配置解决方案的详细说明，请参阅[Azure 自动化 中的非工作时间解决方案中的开始/停止 VM。](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>移动后验证

移动完成后，请检查以下应验证的任务列表：

|功能|计数|故障排除链接|
|---|---|---|
|Runbook|Runbook 可以成功运行并连接到 Azure 资源。|[排除运行簿故障](../troubleshoot/runbooks.md)
|源代码管理|您可以在源代码管理回购上运行手动同步。|[源代码管理集成](../source-control-integration.md)|
|更改跟踪和库存|验证从计算机中看到当前库存数据。|[故障排除更改跟踪](../troubleshoot/change-tracking.md)|
|更新管理|验证您看到您的机器，并且它们是否正常。</br>运行测试软件更新部署。|[故障排除更新管理](../troubleshoot/update-management.md)|
|共享资源|验证是否看到所有共享资源，如[凭据](../shared-resources/credentials.md)、[变量](../shared-resources/variables.md)等。|

## <a name="next-steps"></a>后续步骤

要了解有关在 Azure 中移动资源的更多信息，请参阅[在 Azure 中移动资源](../../azure-resource-manager/management/move-support-resources.md)。
