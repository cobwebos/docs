---
title: 将 Azure 自动化帐户移到另一个订阅
description: 本文介绍如何将自动化帐户移到另一个订阅。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 562ea5e0e9e4851ed59bd3ef917be2f9c48cd2a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185545"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>将 Azure 自动化帐户移到另一个订阅

Azure 自动化允许将一些资源移到新的资源组或订阅。 可通过 Azure 门户、PowerShell、Azure CLI 或 REST API 移动资源。 若要了解有关此过程的详细信息，请参阅[将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

自动化帐户是可以移动的资源之一。 在本文中，你将学习如何将自动化帐户移到另一个资源或订阅。 移动自动化帐户的概括性步骤如下：

1. 禁用功能。
2. 取消链接工作区。
3. 移动自动化帐户。
4. 删除并重新创建运行方式帐户。
5. 重新启用功能。

## <a name="remove-features"></a>删除功能

若要取消工作区与自动化帐户的链接，必须删除工作区中的功能资源：

- 更改跟踪和库存
- 更新管理
- 在非工作时间启动/停止 VM

1. 在 Azure 门户中查找资源组。
2. 找到每个功能，然后在“删除资源”页上选择“删除” 。

    ![从 Azure 门户删除功能资源的屏幕截图](../media/move-account/delete-solutions.png)

如果愿意，可以使用 [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) cmdlet 来删除资源：

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>删除“在空闲时间启动/停止 VM”的警报规则

对于“在空闲时间启动/停止 VM”，还需要删除该功能创建的警报规则。

1. 在 Azure 门户中，转到资源组，然后选择“监视” > “警报” > “管理警报规则”  。

   ![“警报”页的屏幕截图，其中显示了“管理警报规则”选择](../media/move-account/alert-rules.png)

2. 在“规则”页上，应会看到在该资源组中配置的警报的列表。 该功能创建以下规则：

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 一次选择一个规则，然后选择“删除”将其删除。

    ![“规则”页的屏幕截图，其中显示了请求确认删除所选规则](../media/move-account/delete-rules.png)

    > [!NOTE]
    > 如果在“规则”页上没有看到任何警报规则，请将“状态”字段更改为“禁用”以显示禁用的警报 。 

4. 删除警报规则时，必须删除为“在空闲时间启动/停止 VM”通知创建的操作组。 在 Azure 门户中，选择“监视器” > “警报” > “管理操作组”  。

5. 选择“StartStop_VM_Notification”。 

6. 在“操作组”页上，选择“删除”。

    ![操作组页的屏幕截图](../media/move-account/delete-action-group.png)

如果愿意，可使用 [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) cmdlet 删除操作组：

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消链接工作区

现在，可以取消链接工作区：

1. 在 Azure 门户中，选择“自动化帐户” > “相关资源” > “链接的工作区”  。 

2. 选择“取消链接工作区”以从自动化帐户中取消链接工作区。

    ![从自动化帐户中取消链接工作区的屏幕截图](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移动自动化帐户

现在可以移动你的自动化帐户及其 runbook 了。 

1. 在 Azure 门户中，浏览到自动化帐户的资源组。 选择“移动” > “移到另一个订阅” 。

    ![“资源组”页屏幕截图，显示了移到另一个订阅](../media/move-account/move-resources.png)

2. 在资源组中选择要移动的资源。 确保包含了自动化帐户、runbook 和 Log Analytics 工作区资源。

## <a name="re-create-run-as-accounts"></a>重新创建运行方式帐户

[运行方式帐户](../manage-runas-account.md)在 Azure Active Directory 中创建服务主体，用于对 Azure 资源进行身份验证。 更改订阅后，自动化帐户将不再使用现有的运行方式帐户。 重新创建运行方式帐户的方法如下：

1. 转到新订阅中的自动化帐户，然后在“帐户设置”下选择“运行方式帐户” 。 你会看到运行方式帐户现在显示为“不完整”。

    ![运行方式帐户的屏幕截图，其中该账户显示为“不完整”](../media/move-account/run-as-accounts.png)

2. 通过选择“属性”页上的“删除”，一次删除一个运行方式帐户 。 

    > [!NOTE]
    > 如果没有创建或查看运行方式帐户的权限，会看到以下消息：`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 有关详细信息，请参阅[配置运行方式帐户时所需的权限](../manage-runas-account.md#permissions)。

3. 删除运行方式帐户后，请选择“Azure 运行方式帐户”下的“创建” 。 

4. 在“添加 Azure 运行方式帐户”页上，选择“创建”以创建运行方式帐户和服务主体。 

5. 使用 Azure 经典运行方式帐户重复上述步骤。

## <a name="enable-features"></a>启用功能

重新创建运行方式帐户后，必须重新启用在移动之前删除的功能：

1. 若要启用更改跟踪和库存，请在自动化帐户中选择“更改跟踪和库存”。 选择已移到的 Log Analytics 工作区，然后选择“启用”。

2. 对更新管理重复步骤 1。

    ![屏幕截图显示了重新启用已移动的自动化帐户中的功能](../media/move-account/reenable-solutions.png)

3. 连接到现有 Log Analytics 工作区后，会显示已启用了功能的计算机。 若要打开“在空闲时间启动/停止 VM”功能，必须重新启用它。 在“相关资源”下，选择“启动/停止 VM” > “详细了解并启用解决方案” > “创建”以启动部署   。

4. 在“添加解决方案”页上，选择 Log Analytics 工作区和自动化帐户。

    ![“添加解决方案”菜单的屏幕截图](../media/move-account/add-solution-vm.png)

5. 按照[“在空闲时间启动/停止 VM”概述](../automation-solution-vm-management.md)中所述配置该功能。

## <a name="verify-the-move"></a>验证移动

移动完成后，验证以下列出的功能是否已启用。 

|功能|测试|疑难解答|
|---|---|---|
|Runbook|Runbook 可以成功运行并连接到 Azure 资源。|[排查 Runbook 问题](../troubleshoot/runbooks.md)
|源代码管理|可以在源代码管理存储库中运行手动同步。|[源代码管理集成](../source-control-integration.md)|
|更改跟踪和库存|验证是否可从计算机中看到当前库存数据。|[对“更改跟踪”进行故障排除](../troubleshoot/change-tracking.md)|
|更新管理|验证是否可看到计算机且其正常运行。</br>运行测试软件更新部署。|[对“更新管理”进行故障排除](../troubleshoot/update-management.md)|
|共享资源|验证是否可看到所有共享资源，如[凭据](../shared-resources/credentials.md)和[变量](../shared-resources/variables.md)。|

## <a name="next-steps"></a>后续步骤

若要了解如何在 Azure 中移动资源，请参阅[在 Azure 中移动资源](../../azure-resource-manager/management/move-support-resources.md)。
