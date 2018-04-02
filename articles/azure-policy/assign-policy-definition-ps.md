---
title: 快速入门 - 使用 PowerShell 创建策略分配以识别 Azure 环境中的不合规资源 | Microsoft Docs
description: 本快速入门介绍如何使用 PowerShell 创建 Azure 策略分配以识别不合规的资源。
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 3/14/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 45c5ccd0f891a5592eee7400de108c5097f75286
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>快速入门：使用 Azure RM PowerShell 模块创建策略分配以识别不合规资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。 在本快速入门中，我们将创建一个策略分配，以识别未使用托管磁盘的虚拟机。 完成后，我们可以使用该策略分配来识别不合规的虚拟机。

AzureRM PowerShell 模块用于从命令行或脚本创建和管理 Azure 资源。 本指南介绍如何使用 AzureRM 来创建策略分配。 该策略可识别 Azure 环境中的不合规资源。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

- 在开始之前，请确保安装 PowerShell 的最新版本。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
- 将 AzureRM PowerShell 模块更新到最新版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="create-a-policy-assignment"></a>创建策略分配

在此快速入门中，将创建一个策略分配，分配“审核不带托管磁盘的虚拟机”定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

运行以下命令创建新的策略分配：

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"

$definition = Get-AzureRmPolicyDefinition -Name "Audit Virtual Machines without Managed Disks"

New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}

```

上述命令使用以下信息：

- **名称** - 策略分配的显示名称。 本例使用了“审核不带托管磁盘分配的虚拟机”。
- **定义** - 策略定义，用作创建分配的依据。 在本例中，此值为策略定义“审核不带托管磁盘的虚拟机”。
- **范围** - 范围确定在其中实施策略分配的资源或资源组。 它可以从订阅延伸至资源组。 请务必将 &lt;scope&gt; 替换为资源组的名称。
- **SKU** – 此命令创建使用标准层的策略分配。 使用标准层可以实现大规模管理、符合性评估和补救。 有关定价层的其他详细信息，请参阅 [Azure 策略定价](https://azure.microsoft.com/pricing/details/azure-policy)。


你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

使用以下信息来识别不符合所创建的策略分配的资源。 运行以下命令：

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

有关策略分配 ID 的详细信息，请参阅 [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment)。

接下来，运行以下命令，获取输出到 JSON 文件中的不合规资源的资源 ID：

```powershell
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```
结果应如以下示例所示：


```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}
```

这些结果与 Azure 门户视图中“不合规资源”下通常所列的结果类似。


## <a name="clean-up-resources"></a>清理资源

本教程系列中的后续指南建立在本快速入门的基础之上。 如何打算继续学习其他教程，请不要清除本快速入门中创建的资源。 如果不打算继续，可运行以下命令删除创建的分配：

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

若要详细了解如何分配策略并确保**将来**创建的资源合规，请继续学习以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./create-manage-policy.md)
