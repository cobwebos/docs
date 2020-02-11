---
title: 将资源部署到管理组
description: 介绍如何在 Azure 资源管理器模板中的管理组范围内部署资源。
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0419f3daca6845c6809c9f66e870fdf884a7193f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117040"
---
# <a name="create-resources-at-the-management-group-level"></a>在管理组级别创建资源

通常情况下，你可将 Azure 资源部署到 Azure 订阅中的资源组。 但是，还可以在管理组级别创建资源。 使用管理组级别部署来执行在该级别有意义的操作，例如分配[基于角色的访问控制](../../role-based-access-control/overview.md)或应用[策略](../../governance/policy/overview.md)。

目前，若要在管理组级别部署模板，必须使用 REST API。

## <a name="supported-resources"></a>支持的资源

你可以在管理组级别部署以下资源类型：

* [方案](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>架构

用于管理组部署的架构不同于资源组部署的架构。

对于模板，请使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

对于参数文件，请使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

管理组部署的命令不同于用于资源组部署的命令。

对于 REST API，请使用[部署-在管理组范围内创建](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)。

## <a name="deployment-location-and-name"></a>部署位置和名称

对于管理组级别部署，你必须提供部署的位置。 部署的位置与部署的资源的位置不同。 部署位置指定部署数据的存储位置。

可以为部署提供名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

对于每个部署名称，位置是不可变的。 如果在不同的位置存在同名的现有部署，则不能在一个位置中创建部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="use-template-functions"></a>使用模板函数

对于管理组部署，使用模板功能时有一些重要的注意事项：

* 不支持 [resourceGroup()](template-functions-resource.md#resourcegroup) 函数。
* **不**支持[订阅（）](template-functions-resource.md#subscription)函数。
* 支持 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函数。
* 支持 [resourceId()](template-functions-resource.md#resourceid) 函数。 使用它可获取在管理组级别部署中使用的资源的资源 ID。 不要为资源组参数提供值。

  例如，若要获取策略定义的资源 ID，请使用：
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  返回的资源 ID 具有以下格式：
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>创建策略

### <a name="define-policy"></a>定义策略

下面的示例演示如何在管理组级别[定义](../../governance/policy/concepts/definition-structure.md)策略。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }
  ]
}
```

### <a name="assign-policy"></a>分配策略

下面的示例将现有策略定义分配给管理组。 如果策略使用参数，请将参数作为对象提供。 如果策略不使用参数，请使用默认的空对象。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>模板示例

* 创建资源组、策略和策略分配。  参阅[此处](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何分配角色，请参阅[使用 RBAC 和 azure 资源管理器模板管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-template.md)。
* 若要通过示例来了解如何为 Azure 安全中心部署工作区设置，请参阅 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 若要了解有关创建 Azure 资源管理器模板的信息，请参阅[创作模板](template-syntax.md)。
* 有关模板的可用函数列表，请参阅[模板函数](template-functions.md)。