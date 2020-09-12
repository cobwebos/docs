---
title: 将资源部署到管理组
description: 介绍如何通过 Azure 资源管理器模板在管理组范围部署资源。
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 2265f1d31176052c7e7c358ee8ed4cb06fb50ee7
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469789"
---
# <a name="create-resources-at-the-management-group-level"></a>在管理组级别创建资源

随着组织的不断发展，可以部署 Azure 资源管理器模板（ARM 模板）来创建管理组级别的资源。 例如，你可能需要为管理组定义和分配[策略](../../governance/policy/overview.md)或 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。 使用管理组级别的模板，可以声明方式在管理组级别应用策略和分配角色。

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到管理组级别。 本部分列出了支持的资源类型。

对于 Azure 蓝图，请使用：

* [项目](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [蓝图](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

对于 Azure 策略，请使用：

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

对于基于角色的访问控制，请使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

对于部署到订阅或资源组的嵌套模板，请使用：

* [部署](/azure/templates/microsoft.resources/deployments)

若要管理资源，请使用：

* [标记](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>架构

用于管理组部署的架构不同于资源组部署的架构。

对于模板，请使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

对于所有部署范围，参数文件的架构都相同。 对于参数文件，请使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

管理组部署的命令与资源组部署的命令不同。

对于 Azure CLI，请使用 [az deployment mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)：

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

对于 Azure PowerShell，请使用 [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment)。

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

对于 REST API，请使用[部署 - 在管理组范围内创建](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)。

## <a name="deployment-location-and-name"></a>部署位置和名称

对于管理组级别部署，必须为部署提供位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="deployment-scopes"></a>部署范围

部署到管理组时，可以将部署命令中指定的管理组或租户中的其他管理组作为目标。 还可以将管理组中的订阅或资源组作为目标。 部署模板的用户必须有权访问指定的作用域。

将通过部署命令对管理组应用模板的资源部分中定义的资源。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

若要以另一个管理组为目标，请添加嵌套部署并指定 `scope` 属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template-with-resources-in-different-mg
                }
            }
        }
    ],
    "outputs": {}
}
```

若要以管理组中的订阅为目标，请使用嵌套部署和 `subscriptionId` 属性。 若要以订阅中的资源组为目标，请添加另一个嵌套部署和 `resourceGroup` 属性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template-with-resources-in-resource-group
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

若要使用管理组部署在订阅中创建资源组，并将存储帐户部署到该资源组，请参阅 [部署到订阅和资源组](#deploy-to-subscription-and-resource-group)。

## <a name="use-template-functions"></a>使用模板函数

对于管理组部署，在使用模板函数时有一些重要注意事项：

* 不支持 [resourceGroup()](template-functions-resource.md#resourcegroup) 函数。
* 不支持 [subscription()](template-functions-resource.md#subscription) 函数。
* 支持 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函数。
* 请勿对部署到管理组的资源使用 [resourceId ( # B1 ](template-functions-resource.md#resourceid) 函数。

  相反，请使用 [extensionResourceId ( # B1 ](template-functions-resource.md#extensionresourceid) 函数作为管理组扩展实现的资源。 部署到管理组的自定义策略定义是管理组的扩展。

  若要获取管理组级别的自定义策略定义的资源 ID，请使用：
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  对管理组中可用的租户资源使用 [tenantResourceId](template-functions-resource.md#tenantresourceid) 函数。 内置策略定义是租户级别资源。

  若要获取内置策略定义的资源 ID，请使用：
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

下面的示例演示如何在管理组级别 [定义](../../governance/policy/concepts/definition-structure.md) 策略并对其进行分配。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
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
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>部署到订阅和资源组

在管理组级别的部署中，可以以管理组中的订阅为目标。 以下示例在一个订阅中创建资源组，并向该资源组部署存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何分配角色，请参阅[使用 Azure 资源管理器模板添加 Azure 角色分配](../../role-based-access-control/role-assignments-template.md)。
* 若要通过示例来了解如何为 Azure 安全中心部署工作区设置，请参阅 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 还可以在[订阅级别](deploy-to-subscription.md)和[租户级别](deploy-to-tenant.md)部署模板。
