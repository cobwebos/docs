---
title: 在 Azure Monitor 中移动 Log Analytics 工作区 | Microsoft Docs
description: 了解如何将 Log Analytics 工作区移到另一个订阅或资源组。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: d3937eaa5017ed66641d886ecd45e812f7070b83
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566278"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>将 Log Analytics 工作区移到其他订阅或资源组

本文介绍将 Log Analytics 工作区移动到同一区域中的另一个资源组或订阅的步骤。 可在以下文章中详细了解如何通过 Azure 门户、PowerShell、Azure CLI 或 REST API 移动 Azure 资源： [将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 

> [!IMPORTANT]
> 无法将工作区移到其他区域。

## <a name="verify-active-directory-tenant"></a>验证 Active Directory 租户
工作区源订阅与目标订阅必须在同一个 Azure Active Directory 租户中。 使用 Azure PowerShell 来验证这两个订阅是否具有相同的租户 ID。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>工作区移动注意事项
将使用 Log Analytics 工作区移动操作来移动工作区中安装的托管解决方案。 在移动后，连接的代理将保持连接状态并继续向工作区发送数据。 由于移动操作要求工作区中没有任何链接的服务，因此必须删除依赖于该链接的解决方案才能移动工作区。

在取消链接自动化帐户之前必须先删除的解决方案：

- 更新管理
- 更改跟踪
- 在非工作时间启动/停止 VM
- Azure 安全中心

>[!IMPORTANT]
> **Azure Sentinel 客户：**
> - 一旦部署到工作区上，Azure Sentinel **目前不支持** 将该工作区移动到其他资源组或订阅。 
>
>   如果已移动工作区，请禁用 **分析** 下的所有活动规则，并在五分钟后重新启用它们。 但在大多数情况下，这应该是有效的，但它不受支持，并且会自行承担。

### <a name="delete-solutions-in-azure-portal"></a>在 Azure 门户中删除解决方案
在 Azure 门户中使用以下过程删除解决方案：

1. 打开其中已安装任何解决方案的资源组的菜单。
2. 选择要删除的解决方案。
3. 单击“删除资源”，然后单击“删除”确认删除资源。 

![删除解决方案](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>使用 PowerShell 删除

若要使用 PowerShell 删除解决方案，请按以下示例中所示使用 [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) cmdlet：

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>删除“启动/停止 VM”解决方案的警报规则
若要删除“启动/停止 VM”解决方案，还需要删除该解决方案创建的警报规则。 在 Azure 门户中使用以下过程删除这些规则。

1. 打开“监视”菜单，然后选择“警报”。 
2. 单击“管理警报规则”。
3. 选择以下三个警报规则，然后单击“删除”。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![删除规则](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>取消链接自动化帐户
在 Azure 门户中使用以下过程从工作区取消链接自动化帐户：

1. 打开“自动化帐户”菜单，然后选择要删除的帐户。
2. 在菜单的“相关资源”部分，选择“链接的工作区”。  
3. 单击“取消链接工作区”，以从自动化帐户取消链接该工作区。

    ![取消工作区链接](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>移动工作区

### <a name="azure-portal"></a>Azure 门户
在 Azure 门户中使用以下过程移动工作区：

1. 打开“Log Analytics 工作区”菜单，然后选择你的工作区。
2. 在“概述”页中，单击“资源组”或“订阅”旁边的“更改”。   
3. 此时会打开一个新页，其中显示了与该工作区相关的资源列表。 选择要移到该工作区所在的同一目标订阅和资源组中的资源。 
4. 选择目标“订阅”和“资源组”。  如果将工作区移到同一订阅中的另一个资源组，则看不到“订阅”选项。
5. 单击“确定”以移动工作区和所选资源。

    ![门户](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
若要使用 PowerShell 移动工作区，请按以下示例中所示使用 [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource)：

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> 移动操作完成后，应重新配置已删除的解决方案和自动化帐户链接，使工作区恢复以前的状态。


## <a name="next-steps"></a>后续步骤
- 有关支持移动的资源列表，请参阅[资源的移动操作支持](../../azure-resource-manager/management/move-support-resources.md)。
