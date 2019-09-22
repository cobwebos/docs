---
title: 使用 RBAC 和 Azure 资源管理器模板管理对 Azure 资源的访问权限 | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 和 Azure 资源管理器模板来管理用户、组和应用程序对 Azure 资源的访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b7f701cd3ce07099d80bca40e506108bcc9a9da9
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178107"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>使用 RBAC 和 Azure 资源管理器模板管理对 Azure 资源的访问权限

可以通过[基于角色的访问控制 (RBAC)](overview.md) 管理对 Azure 资源的访问权限。 除了使用 Azure PowerShell 或 Azure CLI 以外，还可以使用[azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)管理对 azure 资源的访问权限。 如果需要一致且重复地部署资源，模板会很有用。 本文介绍如何使用 RBAC 和模板管理访问权限。

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>在资源组作用域中创建角色分配（不包含参数）

在 RBAC 中，若要授予访问权限，请创建角色分配。 以下模板演示了创建角色分配的基本方法。 某些值在模板中指定。 以下模板演示：

-  如何将[读者](built-in-roles.md#reader)角色分配给资源组范围内的用户、组或应用程序

若要使用模板，必须执行以下操作：

- 创建新的 JSON 文件并复制模板
- 替换`<your-principal-id>`为要向其分配角色的用户、组或应用程序的唯一标识符。 标识符的格式为：`11111111-1111-1111-1111-111111111111`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

下面是有关如何在名为 ExampleGroup 的资源组中启动部署的示例[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)命令。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

下面演示了在部署模板后，为资源组的用户分配的 "读取者" 角色的示例。

![资源组范围内的角色分配](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>在资源组或订阅范围内创建角色分配

上一个模板不太灵活。 以下模板使用参数，并可在不同的范围内使用。 以下模板演示：

- 如何将角色分配给资源组或订阅范围内的用户、组或应用程序
- 如何将“所有者”、“参与者”和“读者”角色指定为参数

若要使用模板，必须指定以下输入：

- 要为其分配角色的用户、组或应用程序的唯一标识符
- 要分配的角色
- 用于角色分配的唯一标识符；你也可以使用默认的标识符

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

若要获取要向其分配角色的用户的唯一标识符，可以使用[AzADUser](/powershell/module/az.resources/get-azaduser)或[az ad user show](/cli/azure/ad/user#az-ad-user-show)命令。

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

角色分配范围是根据部署级别确定的。 下面是有关如何在资源组范围内启动部署的[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment](/cli/azure/group/deployment#az-group-deployment-create)的示例。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

下面是示例[AzDeployment](/powershell/module/az.resources/new-azdeployment)和[az deployment create](/cli/azure/deployment#az-deployment-create)命令，用于在订阅范围内启动部署并指定位置。

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

## <a name="create-a-role-assignment-at-a-resource-scope"></a>在资源范围中创建角色分配

如果需要在资源级别创建角色分配，角色分配的格式将会不同。 需提供要将角色分配到的资源的资源提供程序命名空间和资源类型。 还需在角色分配名称中包含该资源的名称。

对于角色分配的类型和名称，请使用以下格式：

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

以下模板演示：

- 如何新建存储帐户
- 如何将角色分配给存储帐户范围内的用户、组或应用程序
- 如何将“所有者”、“参与者”和“读者”角色指定为参数

若要使用模板，必须指定以下输入：

- 要为其分配角色的用户、组或应用程序的唯一标识符
- 要分配的角色
- 用于角色分配的唯一标识符；你也可以使用默认的标识符


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

若要部署上一个模板，请使用资源组命令。 下面是有关如何在资源范围中启动部署的示例[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)命令。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

下面显示了部署模板后，为存储帐户的用户分配 "参与者" 角色的示例。

![资源范围内的角色分配](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>创建新服务主体的角色分配

如果创建新的服务主体并立即尝试将角色分配给该服务主体，则在某些情况下该角色分配可能会失败。 例如，如果创建新的托管标识，然后尝试将角色分配给同一 Azure 资源管理器模板中的服务主体，则角色分配可能会失败。 此故障的原因可能是复制延迟。 在一个区域中创建服务主体;但是，角色分配可能发生在尚未复制服务主体的其他区域中。 若要解决这种情况，应在`principalType`创建角色`ServicePrincipal`分配时将属性设置为。

以下模板演示：

- 如何创建新的托管标识服务主体
- 如何指定`principalType`
- 如何将参与者角色分配给资源组作用域上的服务主体

若要使用模板，必须指定以下输入：

- 托管标识的基名称，或者可以使用默认字符串

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

下面是有关如何在资源组范围内启动部署的[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment](/cli/azure/group/deployment#az-group-deployment-create)的示例。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

下面的示例演示了在部署模板后，将参与者角色分配给新的托管标识服务主体的示例。

![新托管标识服务主体的角色分配](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)
- [在订阅级别创建资源组和资源](../azure-resource-manager/deploy-to-subscription.md)
- [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?term=rbac)
