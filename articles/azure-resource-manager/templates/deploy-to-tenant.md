---
title: 将资源部署到租户
description: 介绍如何在 Azure 资源管理器模板中的租户范围内部署资源。
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 45541bcbea5a80e55dbc9f80e1eae8e17189bf6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945437"
---
# <a name="create-resources-at-the-tenant-level"></a>在租户级别创建资源

随着组织的成熟，你可能需要跨整个 Azure AD 租户定义和分配[策略](../../governance/policy/overview.md)或[基于角色的访问控制](../../role-based-access-control/overview.md)。 通过租户级模板，可以声明的方式在全局级别应用策略和分配角色。

## <a name="supported-resources"></a>支持的资源

可在租户级别部署以下资源类型：

* [deployments](/azure/templates/microsoft.resources/deployments) - 适用于部署到管理组或订阅的嵌套模板。
* [managementGroups](/azure/templates/microsoft.management/managementgroups)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>架构

用于租户部署的架构与资源组部署的架构不同。

对于模板，请使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

在所有部署范围内，参数文件的架构都是相同的。 对于参数文件，请使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>必需访问权限

部署模板的主体必须具有在租户范围中创建资源的权限。 该主体必须有权执行部署操作 (`Microsoft.Resources/deployments/*`) 和创建模板中定义的资源。 例如，若要创建管理组，主体必须在租户范围内具有参与者权限。 若要创建角色分配，主体则必须具有所有者权限。

Azure Active Directory 的全局管理员不自动拥有分配角色的权限。 若要在租户范围内实现模板部署，全局管理员必须执行以下步骤：

1. 提升帐户访问权限，使其自身可分配角色。 有关详细信息，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。

1. 向需要部署模板的主体分配所有者或参与者角色。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

主体现已具有部署模板所需的权限。

## <a name="deployment-commands"></a>部署命令

用于租户部署的命令与资源组部署使用的命令不同。

对于 Azure CLI，请使用 [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)：

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

对于 Azure PowerShell，请使用 [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

对于 REST API，请使用[部署 - 在租户范围内创建或更新](/rest/api/resources/deployments/createorupdateattenantscope)。

## <a name="deployment-location-and-name"></a>部署位置和名称

对于租户级别的部署，必须提供部署位置。 部署位置与你部署的资源的位置不同。 部署位置指定的是存储部署数据的位置。

可为部署提供名称，也可使用默认的部署名称。 默认名称就是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称对应的位置必须相同。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="use-template-functions"></a>使用模板函数

对于租户部署，在使用模板函数时有一些重要注意事项：

* 不支持 [resourceGroup()](template-functions-resource.md#resourcegroup) 函数。
* 不支持 [subscription()](template-functions-resource.md#subscription) 函数。
* 支持 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函数。
* 使用 [tenantResourceId()](template-functions-resource.md#tenantresourceid) 函数可获得在租户级别部署的资源的 ID。

  例如，若要获取策略定义的资源 ID，可使用：

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  返回的资源 ID 具有以下格式：

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>创建管理组

[以下模板](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg)用于创建管理组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>分配角色

[以下模板](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment)用于在租户范围内分配角色。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何分配角色，请参阅[使用 RBAC 和 Azure 资源管理器模板管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-template.md)。
* 还可在[订阅级别](deploy-to-subscription.md)或[管理组级别](deploy-to-management-group.md)部署模板。
