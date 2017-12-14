---
title: "迁移自动化帐户和资源 | Microsoft Docs"
description: "本文介绍如何将 Azure 自动化中的自动化帐户及关联的资源从一个订阅移到另一个订阅。"
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: c13ee767cc2a1fb7880e6d0491cd6a247c737c13
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="migrate-automation-account-and-resources"></a>迁移自动化帐户和资源
对于已在 Azure 门户中创建并且想要从一个资源组迁移到另一个资源组或从一个订阅到另一个订阅的自动化帐户及其关联的资源（即资产、Runbook、模块等），可以使用 Azure 门户中提供的[移动资源](../azure-resource-manager/resource-group-move-resources.md)功能轻松实现此目的。 但是，在执行此操作前，应该先查看以下[移动资源前需查看的清单](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources)，以及下面的特定于自动化的列表。   

1. 目标订阅/资源组必须与源位于相同的区域。  这意味着，不能在区域间移动自动化帐户。
2. 移动资源（例如 Runbook、作业等）时，源组和目标组会被锁定，直到移动操作完成。 在完成移动之前，将阻止对组执行写入和删除操作。  
3. 迁移完成后，应更新从现有订阅中引用资源或订阅 ID 的任何 Runbook 或变量。   

> [!NOTE]
> 此功能不支持移动经典自动化资源。
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>使用门户移动自动化帐户
1. 在自动化帐户中，单击页顶部的“移动”。<br> ![移动选项](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. 请注意，在“移动资源”窗格中，它提供与自动化帐户和资源组相关的资源。  从下拉列表中选择“订阅”和“资源组”，或选择“创建新的资源组”选项，并在提供的字段中输入新的资源组名称。  
3. 查看并选中该复选框以确认你了解在移动资源后，将需要更新工具和脚本，以使用新的资源 ID，然后单击“确定”。<br> ![移动资源窗格](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

此操作可能需要几分钟才能完成。  “通知”中会显示发生的每个操作的状态 - 验证、迁移，然后最后是完成的时间。     

## <a name="to-move-the-automation-account-using-powershell"></a>使用 PowerShell 移动自动化帐户
要将现有自动化资源移到另一个资源组或订阅，请使用 **Get-AzureRmResource** cmdlet 获取特定自动化帐户，然后使用 **Move-AzureRmResource** cmdlet 执行移动。

第一个示例演示如何会自动化帐户移到新的资源组。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

执行上面的代码示例后，会提示确认要执行此操作。  单击“是”并允许继续执行脚本后，执行迁移时会不会收到任何通知。  

若要移动到新的订阅，请包含 *DestinationSubscriptionId* 参数的值。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

与前面的示例一样，会提示确认移动。  

## <a name="next-steps"></a>后续步骤
* 有关将资源移到新的资源组或订阅的详细信息，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)
* 有关 Azure 自动化中基于角色的访问控制的详细信息，请参阅 [Role-Based access control in Azure Automation](automation-role-based-access-control.md)（Azure 自动化中基于角色的访问控制）。
* 要了解管理订阅所需的 PowerShell cmdlet，请参阅[将 Azure PowerShell 与 Resource Manager 配合使用](../azure-resource-manager/powershell-azure-resource-manager.md)
* 若要了解管理订阅所需的门户功能，请参阅[使用 Azure 门户管理资源](../azure-resource-manager/resource-group-portal.md)。
