---
title: 快速入门 - 使用 PowerShell 创建策略分配以识别 Azure 环境中的不合规资源
description: 本快速入门介绍如何使用 PowerShell 创建 Azure 策略分配以识别不合规的资源。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 50c6a0cc268e2b638a03494bed96d294789da9ab
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794660"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>快速入门：使用 Azure RM PowerShell 模块创建策略分配以识别不合规资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。 在本快速入门中，我们将创建一个策略分配，以识别未使用托管磁盘的虚拟机。 完成后，我们可以使用该策略分配来识别不合规的虚拟机。

AzureRM PowerShell 模块用于从命令行或脚本创建和管理 Azure 资源。 本指南介绍如何使用 AzureRM 来创建策略分配。 该策略可识别 Azure 环境中的不合规资源。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

- 安装 [ARMClient](https://github.com/projectkudu/ARMClient)（如果尚未安装）。 该工具可将 HTTP 请求发送到基于 Azure 资源管理器的 API。
- 在开始之前，请确保安装 PowerShell 的最新版本。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
- 将 AzureRM PowerShell 模块更新到最新版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。
- 使用 Azure PowerShell 注册 Policy Insights 资源提供程序。 注册此资源提供程序可确保订阅能够使用它。 若要注册资源提供程序，必须具有为资源提供程序执行注册操作的权限。 此操作包含在“参与者”和“所有者”角色中。 运行以下命令，注册资源提供程序：

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  有关注册和查看资源提供程序的详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>创建策略分配

在此快速入门中，将创建一个策略分配，分配“审核不带托管磁盘的虚拟机”定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

运行以下命令创建新的策略分配：

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit Virtual Machines without Managed Disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

上述命令使用以下信息：

- **名称** - 分配的实际名称。  对于此示例，使用 *audit-vm-manageddisks*。
- **显示名称** - 策略分配的显示名称。 本例使用了“审核不带托管磁盘分配的虚拟机”。
- **定义** - 策略定义，用作创建分配的依据。 在本例中，此值为策略定义“审核未使用托管磁盘的 VM”的 ID。
- **范围** - 范围确定在其中实施策略分配的资源或资源组。 它可以从订阅延伸至资源组。 请务必将 &lt;scope&gt; 替换为资源组的名称。

你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

使用以下信息来识别不符合所创建的策略分配的资源。 运行以下命令：

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit Virtual Machines without Managed Disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

有关策略分配 ID 的详细信息，请参阅 [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment)。

接下来，运行以下命令，获取输出到 JSON 文件中的不合规资源的资源 ID：

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

结果应如以下示例所示：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
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

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

若要详细了解如何分配策略并确保**将来**创建的资源合规，请继续学习以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](create-manage-policy.md)