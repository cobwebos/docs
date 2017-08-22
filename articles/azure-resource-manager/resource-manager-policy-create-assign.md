---
title: "分配和管理 Azure 资源策略 | Microsoft Docs"
description: "介绍如何将 Azure 资源策略应用到订阅和资源组，以及如何查看资源策略。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: f461efbc2a23f85e8b6d3fdec156a0df1636708a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---
# <a name="assign-and-manage-resource-policies"></a>分配和管理资源策略

若要实现策略，必须按照以下步骤操作：

1. 检查策略定义（包括 Azure 提供的内置策略），确定订阅中是否已有满足需求的策略。
2. 如果有，获取此策略的名称。
3. 如果没有，使用 JSON 定义策略规则，并将它添加为订阅中的策略定义。 此步骤使策略可用于分配，但不向订阅应用规则。
4. 无论属于上述哪种情况，都将策略分配到作用域（如订阅或资源组）。 现可强制执行策略规则。

本文着重介绍通过 REST API、PowerShell 或 Azure CLI 创建策略定义以及将该定义分配到作用域的步骤。 如果想要通过门户分配策略，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)。 本文不关注用于创建策略定义的语法。 有关策略语法的信息，请参阅[资源策略概述](resource-manager-policy.md)。

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>创建策略定义

可以使用[用于策略定义的 REST API](/rest/api/resources/policydefinitions) 来创建策略。 借助 REST API，可创建和删除策略定义，以及获取现有定义的相关信息。

若要创建策略定义，请运行：

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

包括类似于下方示例的请求正文：

```json
{
  "properties": {
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

### <a name="assign-policy"></a>分配策略

可以通过[用于策略分配的 REST API](/rest/api/resources/policyassignments)，在所需范围内应用策略定义。 借助 REST API，可创建和删除策略分配，以及获取现有分配的相关信息。

若要创建策略分配，请运行：

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policy-assignment} 是策略分配的名称。

包括类似于下方示例的请求正文：

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>查看策略
若要获取策略，请使用[获取策略定义](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get)操作。

### <a name="get-aliases"></a>获取别名
可通过 REST API 检索别名：

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

以下示例介绍了别名的定义： 如你所见，别名定义不同 API 版本中的路径，无论属性名称是否更改。 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

在继续完成 PowerShell 示例之前，请确保[已安装最新版本](/powershell/azure/install-azurerm-ps)的 Azure PowerShell。 版本 3.6.0 中添加了策略参数。 如果使用较早版本，示例将返回一个错误，指示“找不到参数”。

### <a name="view-policy-definitions"></a>查看策略定义
若要查看订阅中的所有策略定义，请运行以下命令：

```powershell
Get-AzureRmPolicyDefinition
```

此命令可返回所有可用的策略定义，包括内置策略。 返回的每个策略的格式如下：

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

继续创建策略定义前，请先查看内置策略。 如果找到实施所需限制的内置策略，可以跳过创建策略定义这一步。 改为将内置策略分配到相应的作用域。

### <a name="create-policy-definition"></a>创建策略定义
可使用 `New-AzureRmPolicyDefinition` cmdlet 创建策略定义。

```powershell
$definition = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

输出存储在 `$definition` 对象中，这会在策略分配过程中使用。 

可提供包含策略规则的 .json 文件路径，而不是指定 JSON 作为参数。

```powershell
$definition = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy "c:\policies\coolAccessTier.json"
```

以下示例创建包含参数的策略定义：

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>分配策略

使用 `New-AzureRmPolicyAssignment` cmdlet 将策略应用于相应的作用域。 下面的示例展示了如何将策略分配到资源组。

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

若要分配需要参数的策略，请创建包含这些值的对象。 下面的示例展示了如何检索内置策略并传递参数值：

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>查看策略分配

若要获取特定策略分配，请运行以下命令：

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

若要查看某一策略定义的策略规则，请使用：

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>删除策略分配 

若要删除策略分配，请使用：

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Azure CLI

### <a name="view-policy-definitions"></a>查看策略定义
若要查看订阅中的所有策略定义，请运行以下命令：

```azurecli
az policy definition list
```

此命令可返回所有可用的策略定义，包括内置策略。 返回的每个策略的格式如下：

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

继续创建策略定义前，请先查看内置策略。 如果找到实施所需限制的内置策略，可以跳过创建策略定义这一步。 改为将内置策略分配到相应的作用域。

### <a name="create-policy-definition"></a>创建策略定义

可以将 Azure CLI 与策略定义命令结合使用来创建策略定义。

```azurecli
az policy definition create --name coolAccessTier --description "Policy to specify access tier." --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>分配策略

可以使用策略分配命令，将策略应用于相应的作用域。 下面的示例展示了如何将策略分配到资源组。

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>查看策略分配

若要查看策略分配，请提供策略分配名称和作用域：

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>删除策略分配 

若要删除策略分配，请使用：

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>后续步骤
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。


