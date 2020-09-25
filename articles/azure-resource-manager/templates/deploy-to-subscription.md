---
title: 将资源部署到订阅
description: 介绍了如何在 Azure 资源管理器模板中创建资源组。 它还展示了如何在 Azure 订阅范围内部署资源。
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: cd1d0a05fc1039d8e99b0af6fc8019face4516bf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284782"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>在订阅级别创建资源组和资源

若要简化资源管理，可以使用 Azure 资源管理器模板（ARM 模板）在 Azure 订阅级别部署资源。 例如，可以将[策略](../../governance/policy/overview.md)和 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 部署到你的订阅中，从而将它们应用于整个订阅。 还可以在订阅中创建资源组，然后将资源部署到订阅中的资源组。

> [!NOTE]
> 可在订阅级别部署中部署到 800 个不同的资源组。

若要在订阅级别部署模板，请使用 Azure CLI、PowerShell、REST API 或门户。

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到订阅级别。 本部分列出了支持的资源类型。

对于 Azure 蓝图，请使用：

* [项目](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [蓝图](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [ (蓝图) 版本 ](/azure/templates/microsoft.blueprint/blueprints/versions)

对于 Azure 策略，请使用：

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

对于基于角色的访问控制，请使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

对于部署到资源组的嵌套模板，请使用：

* [部署](/azure/templates/microsoft.resources/deployments)

若要创建新的资源组，请使用：

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

若要管理订阅，请使用：

* [预算](/azure/templates/microsoft.consumption/budgets)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [标记](/azure/templates/microsoft.resources/tags)

其他支持的类型包括：

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>架构

用于订阅级别部署的架构不同于资源组部署的架构。

对于模板，请使用：

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

对于所有部署范围，参数文件的架构都相同。 对于参数文件，请使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>部署范围

部署到订阅时，可以将一个订阅和订阅中的任何资源组作为目标。 无法部署到与目标订阅不同的订阅。 部署模板的用户必须有权访问指定的作用域。

将对订阅应用模板的资源部分中定义的资源。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

若要以订阅中的资源组为目标，请添加嵌套部署并包括 `resourceGroup` 属性。 在以下示例中，嵌套部署以名为 `rg2` 的资源组为目标。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

本文介绍如何将资源部署到不同范围的模板。 对于创建资源组并向其部署存储帐户的模板，请参阅 [创建资源组和资源](#create-resource-group-and-resources)。 对于创建资源组的模板，对其应用锁，并为资源组分配角色，请参阅 [访问控制](#access-control)。

## <a name="deployment-commands"></a>部署命令

用于订阅级别部署的命令与资源组部署使用的命令不同。

对于 Azure CLI，请使用 [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create)。 以下示例会部署一个模板来创建资源组：

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

对于 PowerShell 部署命令，请使用 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 或 **New-AzSubscriptionDeployment**。 以下示例会部署一个模板来创建资源组：

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

对于 REST API，请使用[部署 - 在订阅范围内创建](/rest/api/resources/deployments/createorupdateatsubscriptionscope)。

## <a name="deployment-location-and-name"></a>部署位置和名称

对于订阅级别部署，必须为部署提供位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="use-template-functions"></a>使用模板函数

对于订阅级别部署，在使用模板函数时有一些重要注意事项：

* 不支持 [resourceGroup()](template-functions-resource.md#resourcegroup) 函数。
* 支持 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函数。
* 请勿使用 [resourceId ( # B1 ](template-functions-resource.md#resourceid) 获取在订阅级别部署的资源的资源 ID。 相反，请使用 [subscriptionResourceId ( # B1 ](template-functions-resource.md#subscriptionresourceid) 函数。

  例如，若要获取部署到订阅的策略定义的资源 ID，请使用：

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  返回的资源 ID 具有以下格式：

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="resource-groups"></a>资源组

### <a name="create-resource-groups"></a>创建资源组

若要在 ARM 模板中创建资源组，请使用资源组的名称和位置定义一个 [resourceGroups/](/azure/templates/microsoft.resources/allversions) 资源组。

以下模板创建空资源组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

结合使用 [copy 元素](copy-resources.md)与资源组来创建多个资源组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

有关资源迭代的信息，请参阅[在 Azure 资源管理器模板中部署资源的多个实例](./copy-resources.md)，以及[教程：使用资源管理器模板创建多个资源实例](./template-tutorial-create-multiple-instances.md)。

### <a name="create-resource-group-and-resources"></a>创建资源组和资源

若要创建资源组并向其部署资源，请使用嵌套模板。 嵌套模板定义要部署到资源组的资源。 将嵌套模板设置为依赖于资源组，确保资源组存在，然后再部署资源。 最多可部署到 800 个资源组。

以下示例将创建一个资源组，并向该资源组部署存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>分配策略定义

以下示例将现有的策略定义分配到订阅。 如果策略定义使用参数，请将参数作为对象提供。 如果策略定义不使用参数，请使用默认的空对象。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

若要使用 Azure CLI 部署此模板，请使用：

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

若要使用 PowerShell 部署此模板，请使用：

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>创建和分配策略定义

可在同一模板中[定义](../../governance/policy/concepts/definition-structure.md)和分配策略定义。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

若要在订阅中创建策略定义，然后将其分配到订阅，请使用以下 CLI 命令：

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

若要使用 PowerShell 部署此模板，请使用：

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure 蓝图

### <a name="create-blueprint-definition"></a>创建蓝图定义

可通过模板[创建](../../governance/blueprints/tutorials/create-from-sample.md)蓝图定义。

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

若要在订阅中创建蓝图定义，请使用以下 CLI 命令：

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

若要使用 PowerShell 部署此模板，请使用：

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>访问控制

若要了解如何分配角色，请参阅[使用 Azure 资源管理器模板添加 Azure 角色分配](../../role-based-access-control/role-assignments-template.md)。

以下示例创建一个资源组，对其应用锁定，并为主体分配一个角色。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>后续步骤

* 若要通过示例来了解如何为 Azure 安全中心部署工作区设置，请参阅 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 示例模板可在 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments) 找到。
* 还可在[管理组级别](deploy-to-management-group.md)和[租户级别](deploy-to-tenant.md)部署模板。
