---
title: 使用 Azure 策略以编程方式创建策略和查看符合性数据
description: 本文逐步讲解如何以编程方式创建和管理适用于 Azure 策略的策略。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 5405566b5254c553eac584acc1653449b51ddffc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195873"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>以编程方式创建策略和查看符合性数据

本文逐步讲解如何以编程方式创建和管理策略。 此外，还介绍如何查看资源符合性状态和策略。 策略定义对资源强制实施不同的规则和效果。 强制实施可确保资源始终符合企业标准和服务级别协议。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保满足以下先决条件：

1. 安装 [ARMClient](https://github.com/projectkudu/ARMClient)（如果尚未安装）。 该工具可将 HTTP 请求发送到基于 Azure 资源管理器的 API。
2. 将 AzureRM PowerShell 模块更新到最新版本。 有关最新版本的详细信息，请参阅 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。
3. 使用 Azure PowerShell 注册策略见解资源提供程序，以确保订阅可使用资源提供程序。 若要注册资源提供程序，必须具有为资源提供程序执行注册操作的权限。 此操作包含在“参与者”和“所有者”角色中。 运行以下命令，注册资源提供程序：

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  有关注册和查看资源提供程序的详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md)。
4. 安装 Azure CLI（如果尚未安装）。 可以通过[在 Windows 上安装 Azure CLI 2.0](/cli/azure/install-azure-cli-windows) 获取最新版本。

## <a name="create-and-assign-a-policy-definition"></a>创建并分配策略定义

更清晰地洞察资源的第一步是针对资源创建并分配策略。 下一步是了解如何以编程方式创建和分配策略。 示例策略使用 PowerShell、Azure CLI 和 HTTP 请求来审核向所有公共网络开放的存储帐户。

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>使用 PowerShell 创建并分配策略定义

1. 使用以下 JSON 代码片段创建名为 AuditStorageAccounts.json 的 JSON 文件。

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  有关编写策略定义的详细信息，请参阅 [Azure 策略定义结构](policy-definition.md)。
2. 运行以下命令，使用 AuditStorageAccounts.json 文件创建策略定义。

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  该命令创建名为 _Audit Storage Accounts Open to Public Networks_ 的策略定义。 有关可用的其他参数的详细信息，请参阅 [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition)。
3. 创建策略定义后，可运行以下命令创建策略分配：

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  将 _ContosoRG_ 替换为所需资源组的名称。

有关使用 Azure 资源管理器 PowerShell 模块管理资源策略的详细信息，请参阅 [AzureRM.Resources](/powershell/module/azurerm.resources/#policies)。

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>使用 ARMClient 创建并分配策略定义

使用以下过程创建策略定义。

1. 复制以下 JSON 代码片段以创建 JSON 文件。 在下一步骤中将会调用该文件。

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. 使用以下调用创建策略定义：

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  请将前置的 &lt;subscriptionId&gt; 替换为所需订阅的 ID。

有关查询结构的详细信息，请参阅[策略定义 – 创建或更新](/rest/api/resources/policydefinitions/createorupdate)。

使用以下过程创建策略分配，并在资源组级别分配策略定义。

1. 复制以下 JSON 代码片段以创建 JSON 策略分配文件。 请将 &lt;&gt; 符号中的示例信息替换为自己的值。

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      }
  }
  ```

2. 使用以下调用创建策略分配：

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  请将 &lt;&gt; 符号中的示例信息替换为自己的值。

  有关向 REST API 发出 HTTP 调用的详细信息，请参阅 [Azure REST API 资源](/rest/api/resources/)。

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>使用 Azure CLI 创建并分配策略定义

若要创建策略定义，请使用以下过程：

1. 复制以下 JSON 代码片段以创建 JSON 策略分配文件。

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. 运行以下命令创建策略定义：

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. 使用以下命令创建策略分配。 请将 &lt;&gt; 符号中的示例信息替换为自己的值。

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

可以在 PowerShell 中使用以下命令获取策略定义 ID：

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

创建的策略定义的策略定义 ID 应如以下示例所示：

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

有关如何使用 Azure CLI 管理资源策略的详细信息，请参阅 [Azure CLI 资源策略](/cli/azure/policy?view=azure-cli-latest)。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

在分配中，如果某个资源不遵循策略或计划规则，则该资源不合规。 下表显示了对于生成的符合性状态，不同的策略效果是如何与条件评估配合使用的：

| 资源状态 | 效果 | 策略评估 | 符合性状态 |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不合规 |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 符合 |
| 新建 | Audit、AuditIfNotExist\* | True | 不合规 |
| 新建 | Audit、AuditIfNotExist\* | False | 符合 |

\*Append、DeployIfNotExist 和 AuditIfNotExist 效果要求 IF 语句为 TRUE。 这些效果还要求存在条件为 FALSE 才能将资源判定为不合规。 如果为 TRUE，则 IF 条件会触发相关资源存在条件的计算。

为了更好地理解如何将资源标记为不合规，让我们使用前面创建的策略分配示例。

例如，假设有一个资源组 ContsoRG，其中包含一些向公共网络公开的存储帐户（以红色突出显示）。

![向公共网络公开的存储帐户](media/policy-insights/resource-group01.png)

在此示例中，需要慎重考虑安全风险。 创建策略分配后，将会针对 ContosoRG 资源组中的所有存储帐户评估该分配。 系统会审核三个不合规的存储帐户，因而将其状态更改为“不合规”。

![已审核不合规的存储帐户](media/policy-insights/resource-group03.png)

使用以下过程来识别资源组中不符合策略分配的资源。 在该示例中，资源是 ContosoRG 资源组中的存储帐户。

1. 运行以下命令获取策略分配 ID：

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks' }
  $policyAssignment.PolicyAssignmentId
  ```

  有关获取策略分配 ID 的详细信息，请参阅 [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment)。

2. 运行以下命令，获取已复制到 JSON 文件中的不合规资源的资源 ID：

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. 结果应如以下示例所示：

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

这些结果与 [Azure 门户视图](assign-policy-definition.md#identify-non-compliant-resources)中“不合规资源”下通常所列的结果类似。

目前，只能使用 Azure 门户和 HTTP 请求标识不合规的资源。 有关查询策略状态的详细信息，请参阅[策略状态](/rest/api/policy-insights/policystates) API 参考文章。

## <a name="view-policy-events"></a>查看策略事件

创建或更新资源时，将生成策略评估结果。 结果称为“策略事件”。 运行以下查询来查看与策略分配关联的所有策略事件。

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

结果应如以下示例所示：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

与处理策略状态时一样，只能使用 HTTP 请求查看策略事件。 有关查询策略事件的详细信息，请参阅[策略事件](/rest/api/policy-insights/policyevents)参考文章。

## <a name="next-steps"></a>后续步骤

查看以下文章，详细了解本文中所示的命令和查询。

- [Azure REST API 资源](/rest/api/resources/)
- [Azure RM PowerShell 模块](/powershell/module/azurerm.resources/#policies)
- [Azure CLI 策略命令](/cli/azure/policy?view=azure-cli-latest)
- [策略见解资源提供程序 REST API 参考](/rest/api/policy-insights)