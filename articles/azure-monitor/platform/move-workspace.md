---
title: 将 Log Analytics 工作区移动到 Azure Monitor |Microsoft Docs
description: 了解如何将 Log Analytics 工作区移动到另一个订阅或资源组。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: ca9bb3853698b831fe87f48de346183e4bcd0976
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731696"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>将 Log Analytics 工作区移动到不同的订阅或资源组

本文介绍将 Log Analytics 工作区移动到同一区域中的另一个资源组或订阅的步骤。 可以通过 Azure 门户、PowerShell、Azure CLI 或 REST API 了解有关移动 Azure 资源的详细信息。 [将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 

> [!IMPORTANT]
> 不能将工作区移动到不同的区域。

## <a name="verify-active-directory-tenant"></a>验证 Active Directory 租户
工作区源订阅和目标订阅必须在同一个 Azure Active Directory 租户内。 使用 Azure PowerShell 来验证这两个订阅是否具有相同的租户 ID。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>工作区移动注意事项
工作区中安装的托管解决方案将随 Log Analytics 工作区移动操作一起移动。 在移动后，连接的代理将保持连接，并将数据发送到工作区。 由于移动操作要求工作区中没有任何链接的服务，因此必须删除依赖于该链接的解决方案以允许工作区移动。

取消链接自动化帐户之前必须先删除的解决方案：

- 更新管理
- 更改跟踪
- 在非工作时间启动/停止 VM
- Azure 安全中心


### <a name="delete-solutions-in-azure-portal"></a>删除 Azure 门户中的解决方案
使用以下过程删除使用 Azure 门户的解决方案：

1. 打开在其中安装了任何解决方案的资源组的菜单。
2. 选择要删除的解决方案。
3. 单击 "**删除资源**"，然后单击 "**删除**" 确认要删除的资源。

![删除解决方案](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>使用 PowerShell 删除

若要使用 PowerShell 删除解决方案，请使用[AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) cmdlet，如以下示例中所示：

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>删除启动/停止 Vm 解决方案的警报规则
若要删除**启动/停止 vm**解决方案，还需要删除解决方案创建的警报规则。 在 Azure 门户中使用以下过程删除这些规则。

1. 打开 "**监视**" 菜单，然后选择 "**警报**"。
2. 单击 "**管理警报规则**"。
3. 选择以下三个警报规则，然后单击 "**删除**"。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![删除规则](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>取消链接自动化帐户
使用以下过程从工作区中使用 Azure 门户取消链接自动化帐户：

1. 打开 "**自动化帐户**" 菜单，然后选择要删除的帐户。
2. 在菜单的 "**相关资源**" 部分中，选择 "**链接的工作区**"。 
3. 单击 "**取消链接工作区**" 以从自动化帐户取消链接工作区。

    ![取消链接工作区](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>移动工作区

### <a name="azure-portal"></a>Azure 门户
使用以下过程通过 Azure 门户移动工作区：

1. 打开 " **Log Analytics 工作区**" 菜单，然后选择工作区。
2. 在 "**概述**" 页中，单击 "**资源组**" 或 "**订阅**" 旁边的 "**更改**"。
3. 此时将打开一个新页面，其中包含与工作区相关的资源列表。 选择要移动到与工作区相同的目标订阅和资源组的资源。 
4. 选择目标**订阅**和**资源组**。 如果要将工作区移至同一订阅中的其他资源组，则不会看到 "**订阅**" 选项。
5. 单击 **"确定"** 移动工作区和所选资源。

    ![门户](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
若要使用 PowerShell 移动工作区，请使用[AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) ，如以下示例中所示：

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> 移动操作完成后，应重新配置删除的解决方案和自动化帐户链接，使工作区恢复为其以前的状态。


## <a name="next-steps"></a>后续步骤
- 有关支持移动的资源列表，请参阅[资源的移动操作支持](../../azure-resource-manager/management/move-support-resources.md)。
