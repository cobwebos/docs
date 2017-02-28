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
ms.date: 02/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 944eafeb67df4baefa99172c1082259a95e84afe
ms.lasthandoff: 02/21/2017


---
# <a name="assign-and-manage-resource-policies"></a>分配和管理资源策略

若要实施策略，必须执行以下&3; 个步骤：

1. 使用 JSON 定义策略规则。
2. 通过上一步中创建的 JSON，在订阅中创建策略定义。 此步骤使策略可用于分配，但不向订阅应用规则。
3. 将策略分配到作用域（如订阅或资源组）。 现可强制执行策略规则。

Azure 提供了一些预定义策略，可降低要定义的策略数目。 如果预定义策略适用于你的方案，请跳过前两个步骤，直接将预定义策略分配到作用域。

本主题着重介绍创建策略定义以及将该定义分配到作用域的步骤。 而不是关注用于创建策略定义的语法。 有关策略语法的信息，请参阅[资源策略概述](resource-manager-policy.md)。

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>创建策略定义

可以使用[用于策略定义的 REST API](/rest/api/resources/policydefinitions) 来创建策略。 REST API 可让你创建和删除策略定义，以及获取现有定义的信息。

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

可以通过[用于策略分配的 REST API](/rest/api/resources/policyassignments)，在所需范围内应用策略定义。 REST API 可让你创建和删除策略分配，以及获取现有分配的信息。

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
      "listOfAllowedLocations": { "value": ["West US", "West US 2"] }
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

以下示例介绍了别名的定义： 如你所见，别名在不同的 API 版本中定义路径，无论属性名称是否更改。 

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

### <a name="create-policy-definition"></a>创建策略定义
可使用 `New-AzureRmPolicyDefinition` cmdlet 创建策略定义。 以下示例创建仅允许北欧和西欧资源的策略定义。

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'
```            

输出存储在 `$policy` 对象中，这将在策略分配过程中使用。 

可提供包含策略规则的 .json 文件路径，而不是指定 JSON 作为参数。

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>分配策略

通过 `New-AzureRmPolicyAssignment` cmdlet 将策略应用于所需作用域:

```powershell
New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>查看策略分配

若要获取策略，请使用以下 cmdlet：

```powershell
(Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json
```

这将返回策略定义的 JSON。

### <a name="remove-policy-assignment"></a>删除策略分配 

若要删除策略分配，请使用：

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>Azure CLI 2.0

### <a name="create-policy-definition"></a>创建策略定义

可结合策略定义命令使用 Azure CLI 2.0 来创建策略定义。 以下示例将创建仅允许北欧和西欧资源的策略。

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>分配策略

可以使用策略分配命令将策略应用到所需范围：

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>查看策略定义
若要获取策略定义，请使用以下命令：

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>删除策略分配 

若要删除策略分配，请使用：

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0

### <a name="create-policy-definition"></a>创建策略定义

可以结合策略定义命令使用 Azure CLI 来创建策略定义。 以下示例将创建仅允许北欧和西欧资源的策略。

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

可以指定包含策略的 .json 文件路径，而无需指定内联策略。

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>分配策略

可以使用策略分配命令将策略应用到所需范围：

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

此处的范围是指定的资源组的名称。 如果 policy-definition-id 参数的值未知，可以通过 Azure CLI 获取该值。 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>查看策略
若要获取策略，请使用以下命令：

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>删除策略分配 

若要删除策略分配，请使用：

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>后续步骤
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。


