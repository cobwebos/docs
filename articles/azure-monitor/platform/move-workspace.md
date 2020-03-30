---
title: 在 Azure 监视器中移动日志分析工作区 |微软文档
description: 了解如何将日志分析工作区移动到其他订阅或资源组。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659486"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>将日志分析工作区移动到不同的订阅或资源组

在本文中，您将学习将 Log Analytics 工作区移动到同一区域中的另一个资源组或订阅的步骤。 可以了解有关通过 Azure 门户、PowerShell、Azure CLI 或 REST API 移动 Azure 资源的更多信息。 将[资源移动到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 

> [!IMPORTANT]
> 不能将工作区移动到其他区域。

## <a name="verify-active-directory-tenant"></a>验证活动目录租户
工作区源和目标订阅必须存在于同一 Azure 活动目录租户中。 使用 Azure PowerShell 验证两个订阅是否具有相同的租户 ID。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>工作区移动注意事项
工作区中安装的托管解决方案将随日志分析工作区移动操作一起移动。 连接的代理将保持连接状态，并在移动后将数据发送到工作区。 由于移动操作要求从工作区到任何自动化帐户没有链接，因此必须删除依赖于该链接的解决方案。

必须先删除的解决方案，然后才能取消连接您的自动化帐户：

- 更新管理
- 更改跟踪
- 在非工作时间启动/停止 VM


### <a name="delete-in-azure-portal"></a>在 Azure 门户中进行删除
使用以下过程使用 Azure 门户删除解决方案：

1. 打开安装任何解决方案的资源组的菜单。
2. 选择要删除的解决方案。
3. 单击 **"删除资源**"，然后单击"**删除**"以确认要删除的资源。

![删除解决方案](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>使用 PowerShell 删除

要使用 PowerShell 删除解决方案，请使用["删除-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) cmdlet"，如以下示例所示：

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>删除警报规则
对于 **"开始/停止 VM"** 解决方案，还需要删除解决方案创建的警报规则。 在 Azure 门户中使用以下过程来删除这些规则。

1. 打开 **"监视器"** 菜单，然后选择 **"警报**"。
2. 单击 **"管理警报规则**"。
3. 选择以下三个警报规则，然后单击 **"删除**"。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![删除规则](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>取消链接自动化帐户
使用以下过程使用 Azure 门户从工作区取消链接自动化帐户：

1. 打开 **"自动化帐户"** 菜单，然后选择要删除的帐户。
2. 在菜单的 **"相关资源**"部分中，选择 **"链接工作区**"。 
3. 单击 **"取消链接工作区**"以从自动化帐户取消链接工作区。

    ![取消链接工作区](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>移动工作区

### <a name="azure-portal"></a>Azure 门户
使用以下过程使用 Azure 门户移动工作区：

1. 打开**日志分析工作区菜单**，然后选择工作区。
2. 在 **"概述"** 页中，单击 **"资源组**"或"**订阅**"旁边的**更改**。
3. 将打开一个新页面，其中列出了与工作区相关的资源。 选择要移动到与工作区相同的目标订阅和资源组的资源。 
4. 选择目标**订阅****和资源组**。 如果要将工作区移动到同一订阅中的另一个资源组，则看不到 **"订阅"** 选项。
5. 单击"**确定**"可移动工作区和所选资源。

    ![门户](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
要使用 PowerShell 移动工作区，请使用[移动-AzResource，](/powershell/module/AzureRM.Resources/Move-AzureRmResource)如以下示例所示：

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> 移动操作后，应重新配置已删除的解决方案和自动化帐户链接，以使工作区回到其以前的状态。


## <a name="next-steps"></a>后续步骤
- 有关支持移动的资源列表，请参阅[资源的移动操作支持](../../azure-resource-manager/management/move-support-resources.md)。
