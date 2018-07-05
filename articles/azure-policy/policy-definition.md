---
title: Azure 策略定义结构
description: 介绍 Azure 策略如何使用资源策略定义，通过描述何时强制实施策略和要实现的效果为组织中的资源建立约定。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2f756d65fa167b3812772088aec7232d08b04b9f
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937326"
---
# <a name="azure-policy-definition-structure"></a>Azure 策略定义结构

Azure 策略使用的资源策略定义，可使你通过描述何时强制实施策略和要实现的效果为组织中的资源建立约定。 通过定义约定，可以控制成本并更轻松地管理资源。 例如，可指定仅允许特定类型的虚拟机。 或者，可要求所有资源都拥有特定标记。 策略由所有子资源继承。 因此，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。

可以在此处找到 Azure 策略使用的架构：[https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

使用 JSON 创建策略定义。 策略定义包含以下项的元素：

- mode
- parameters
- 显示名称
- description
- 策略规则
  - 逻辑评估
  - 效果

例如，以下 JSON 说明限制资源部署位置的策略：

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

所有 Azure 策略示例均位于[策略示例](json-samples.md)内。

## <a name="mode"></a>Mode

**模式**确定将对策略评估哪些资源类型。 支持的模式包括：

- `all`：评估资源组和所有资源类型
- `indexed`：仅评估支持标记和位置的资源类型

大多数情况下，建议将“mode”设置为 `all`。 通过门户创建的所有策略定义使用 `all` 模式。 如果使用 PowerShell 或 Azure CLI，则可以手动指定 **mode** 参数。 如果策略定义不包含 **mode** 值，为提供向后兼容性，在 Azure PowerShell 中默认为 `all`，在 Azure CLI 中默认为 `null`，该值等同于 `indexed`。

在创建强制执行标记或位置的策略时，应该使用 `indexed`。 这并不是必须的，但是它会阻止不支持标记和位置的资源，使其不会在符合性结果中显示为不兼容。 在这一点上，资源组是一个例外。 尝试在资源组上强制执行位置或标记的策略应将“mode”设为 `all`，并专门针对 `Microsoft.Resources/subscriptions/resourceGroup` 类型。 请在[强制执行资源组标记](scripts/enforce-tag-rg.md)查看相关示例。

## <a name="parameters"></a>parameters

参数可减少策略定义的数量，有助于简化策略管理。 使用类似窗体中字段的参数 - `name`、`address`、`city`、`state`。 这些参数始终不变，但其值会基于窗体中的各填写内容变化。 构建策略时，参数同样适用。 如果在策略定义中包括参数，就可以通过使用不同的值重新使用策略以执行不同方案。

例如，可以定义资源属性策略来限制资源的部署位置。 在这种情况下，创建策略时需声明以下参数：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

参数类型可以是字符串，也可以是数组。 Azure 门户等工具使用元数据属性显示用户友好信息。

在元数据属性中，可以使用 **strongType** 提供 Azure 门户中的选项多选列表。  **strongType** 的允许值目前包括：

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

在策略规则中，使用下列语法引用参数：

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>定义位置

创建计划或策略定义时，指定定义位置非常重要。

定义位置确定计划或策略定义可分配到的范围。 位置可以指定为管理组或订阅。

> [!NOTE]
> 若要将此策略定义应用到多个订阅，则位置必须是计划或策略要分配到的订阅所在的管理组。

## <a name="display-name-and-description"></a>显示名称和说明

可使用“显示名称”和“说明”来标识策略定义，并提供其使用上下文。

## <a name="policy-rule"></a>策略规则

策略规则包括 **If** 和 **Then** 块。 在 **If** 块中，定义强制执行策略时指定的一个或多个条件。 可以对这些条件应用逻辑运算符，以精确定义策略的方案。

在 **Then** 块中，定义满足 **If** 条件时产生的效果。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>逻辑运算符

支持的逻辑运算符为：

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 语法反转条件的结果。 **allOf** 语法（与逻辑 **And** 操作相似）要求所有条件为 true。 **anyOf** 语法（与逻辑 **Or** 操作相似）要求一个或多个条件为 true。

可以嵌套逻辑运算符。 以下示例显示了嵌套在 allOf 操作中的 not 操作。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>条件

条件评估字段是否符合特定的准则。 支持的条件有：

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

使用 like 和 notLike 条件时，可以在值中指定通配符 `*`。
值不得包含多个通配符 `*`。

当使用 match 和 notMatch 条件时，请提供 `#` 来表示数字，提供 `?` 来表示字母，提供任何其他字符来表示该实际字符。 例如，请参阅[允许多名称模式](scripts/allow-multiple-name-patterns.md)。

### <a name="fields"></a>字段

使用字段构成条件。 字段显示用于描述资源状态的资源请求负载属性。  

支持以下字段：

- `name`
- `fullName`
  - 返回资源全名。 资源全名是最前面为任意父资源名称的资源名称（例如“myServer/myDatabase”）。
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - 括号语法支持包含句点的标记名称
- 属性别名 - 有关列表，请参阅[别名](#aliases)。

### <a name="alternative-accessors"></a>可供选择的取值函数

“Field”是策略规则中使用的主要取值函数。 它会直接检查被评估的资源。 而策略支持另一个取值函数“source”。

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

“source”只支持一个值，就是“action”。 “action”返回被评估请求的授权操作。 [活动日志](../monitoring-and-diagnostics/monitoring-activity-log-schema.md)中的授权部分公开了授权操作。

当策略在后台评估现有资源时，它将“action”设为对此资源类型的 `/write` 授权操作。

### <a name="effect"></a>效果

策略支持以下类型的效果：

- **Deny**：会在审核日志中生成一个事件，并使请求失败
- **Audit**：会在审核日志中生成一个警告事件，但不会使请求失败
- **Append**：会将定义的字段集添加到请求
- **AuditIfNotExists**：如果资源不存在，则启用审核
- **DeployIfNotExists**：如果资源不存在，则部署一个资源。 目前，只支持通过内置的策略实现这种效果。

对于 **append**，必须提供以下详细信息：

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

值可以是字符串或 JSON 格式对象。

借助 AuditIfNotExists 和 DeployIfNotExists，可以评估相关资源是否存在，并在该资源不存在时应用规则和相应的作用。 例如，可以要求为所有虚拟网络部署网络观察程序。
有关未部署虚拟机扩展时的审核示例，请参阅[如果扩展不存在，则进行审核](scripts/audit-ext-not-exist.md)。

有关每种效果、评估顺序、属性和示例的完整详细信息，请参阅[了解策略效果](policy-effects.md)。

## <a name="aliases"></a>别名

可以使用属性别名来访问资源类型的特定属性。 通过别名，可限制允许用于特定资源属性的值和条件。 每个别名会映射到给定资源类型不同 API 版本的路径。 在策略评估期间，策略引擎会获取该 API 版本的属性路径。

别名列表始终不断增长。 要发现 Azure 策略当前支持哪些别名，请使用以下方法之一：

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>计划

使用计划可组合多个相关策略定义，以简化分配和管理，因为可将组作为单个项使用。 例如，可以将所有相关标记策略组合为单个计划。 将应用计划，而非单独分配每个策略。

下面的示例演示如何创建用于处理 `costCenter` 和 `productName` 这两个标记的计划。 它使用两个内置策略来应用默认标记值。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>后续步骤

- 有关更多示例，请参阅 [Azure 策略示例](json-samples.md)。
