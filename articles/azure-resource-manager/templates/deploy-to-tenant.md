---
title: 将资源部署到租户
description: 介绍如何在 Azure 资源管理器模板中的租户作用域中部署资源。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460256"
---
# <a name="create-resources-at-the-tenant-level"></a>在租户级别创建资源

随着组织的成熟，您可能需要跨 Azure AD 租户定义和分配[策略](../../governance/policy/overview.md)或[基于角色的访问控件](../../role-based-access-control/overview.md)。 使用租户级别模板，您可以声明性地应用策略并在全局级别分配角色。

## <a name="supported-resources"></a>支持的资源

您可以在租户级别部署以下资源类型：

* [部署](/azure/templates/microsoft.resources/deployments)- 用于部署到管理组或订阅的嵌套模板。
* [策略分配](/azure/templates/microsoft.authorization/policyassignments)
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

对于所有部署作用域，参数文件的架构都相同。 对于参数文件，请使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>所需访问权限

部署模板的主体必须具有在租户作用域中创建资源的权限。 主体必须具有执行部署操作 （`Microsoft.Resources/deployments/*`） 和创建模板中定义的资源的权限。 例如，要创建管理组，主体必须在租户作用域中具有"参与者"权限。 要创建角色分配，主体必须具有所有者权限。

Azure 活动目录的全局管理员不自动具有分配角色的权限。 要在租户范围内启用模板部署，全局管理员必须执行以下步骤：

1. 提升帐户访问权限，以便全局管理员可以分配角色。 有关详细信息，请参阅[提升管理所有 Azure 订阅和管理组的访问](../../role-based-access-control/elevate-access-global-admin.md)。

1. 将所有者或参与者分配给需要部署模板的主体。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

主体现在具有部署模板所需的权限。

## <a name="deployment-commands"></a>部署命令

租户部署的命令与资源组部署的命令不同。

对于 Azure CLI，使用[az 部署租户创建](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)：

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

对于 Azure 电源外壳，请使用[新租户部署](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

对于 REST API，请使用[部署 - 在租户作用域创建或更新](/rest/api/resources/deployments/createorupdateattenantscope)。

## <a name="deployment-location-and-name"></a>部署位置和名称

对于租户级别部署，必须为部署提供位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="use-template-functions"></a>使用模板函数

对于租户部署，使用模板函数时需要考虑一些重要事项：

* 不支持 [resourceGroup()](template-functions-resource.md#resourcegroup) 函数。****
* **不**支持 [subscription()](template-functions-resource.md#subscription) 函数。
* 支持 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函数。
* 使用[租户 ResourceId（）](template-functions-resource.md#tenantresourceid)函数获取在租户级别部署的资源的资源 ID。

  例如，要获取策略定义的资源 ID，请使用：
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  返回的资源 ID 具有以下格式：
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>创建管理组

[以下模板](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg)创建管理组。

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

[以下模板](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment)在租户作用域中分配角色。

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
* 您还可以在[订阅级别](deploy-to-subscription.md)或[管理组级别](deploy-to-management-group.md)部署模板。
