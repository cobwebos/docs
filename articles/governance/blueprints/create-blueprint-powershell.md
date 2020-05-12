---
title: 快速入门：使用 PowerShell 创建蓝图
description: 本快速入门中通过 PowerShell 使用 Azure 蓝图创建、定义和部署项目。
ms.date: 05/06/2020
ms.topic: quickstart
ms.openlocfilehash: 79feafa48d5d180949b8a23163f2ee9b686e6076
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864107"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>快速入门：使用 PowerShell 定义和分配 Azure 蓝图

了解如何创建和分配蓝图以后即可定义常见的模式，以便根据资源管理器模板、策略、安全性等方面的要求开发可重复使用和可快速部署的配置。 本教程介绍如何使用 Azure 蓝图来执行某些与在组织中创建、发布和分配蓝图相关的常见任务，例如：

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

如果尚未安装，请按照[添加 Az.Blueprint 模块](./how-to/manage-assignments-ps.md#add-the-azblueprint-module)中的说明安装并验证 PowerShell 库中的 Az.Blueprint 模块  。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>创建蓝图

定义符合性的标准模式的第一步是根据可用资源构建蓝图。 我们将创建名为“MyBlueprint”的蓝图，以配置订阅的角色和策略分配。 然后，我们将添加资源组、资源管理器模板，然后在资源组上添加角色分配。

> [!NOTE]
> 使用 PowerShell 时，首先创建 blueprint 对象  。 对于每个要添加的具有参数的项目，需要在初始蓝图上提前定义该参数   。

1. 创建初始 blueprint 对象  。 BlueprintFile  参数接受一个 JSON 文件，该文件包含有关蓝图、要创建的任何资源组和所有蓝图级别参数的属性。 参数在分配过程中设置并由在后续步骤中添加的项目使用。

   - JSON 文件 - blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell 命令

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > 以编程方式创建蓝图定义时，请使用文件名 _blueprint.json_。
     > 调用 [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact) 时使用此文件名。

     默认情况下，会在默认订阅中创建蓝图对象。 若要指定管理组，请使用参数 **ManagementGroupId**。 若要指定订阅，请使用参数 **SubscriptionId**。

1. 在订阅中添加角色分配。 **ArtifactFile** 定义项目的种类、与角色定义标识符一致的属性以及以值的数组形式传递的主体标识  。 在下面的示例中，主体标识被授予指定的角色，配置为蓝图分配过程中所设置的参数。 此示例使用 GUID 为 `b24988ac-6180-42a0-ab88-20f7382dd24c` 的“参与者”  内置角色。

   - JSON 文件 - \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell 命令

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. 在订阅中添加策略分配。 **ArtifactFile** 定义项目的种类、与策略或计划定义一致的属性，并配置策略分配，以使用要在蓝图分配过程中配置的已定义蓝图参数  。 此示例使用 GUID 为 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` 的“将标记及其默认值应用于资源组”内置策略。 

   - JSON 文件 - \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell 命令

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. 在订阅中为存储标记（重复使用 storageAccountType 参数）添加其他策略分配  。 此附加的策略分配项目演示了蓝图上定义的参数可由多个项目使用。 在示例中，storageAccountType 用于在资源组上设置一个标记  。 此值提供有关下一步骤中创建的存储帐户的信息。 此示例使用 GUID 为 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` 的“将标记及其默认值应用于资源组”内置策略。 

   - JSON 文件 - \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell 命令

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. 在资源组下添加模板。 资源管理器模板的 **TemplateFile** 包含模板的标准 JSON 组件。 系统会向模板一一传递 **storageAccountType**、**tagName** 和 **tagValue** 蓝图参数，让模板重复使用这些参数。 通过使用参数 **TemplateParameterFile** 和在使用键-值对的模板 JSON 中来注入值，模板可以使用蓝图参数。 蓝图和模板参数名称可以相同。

   - JSON Azure 资源管理器模板文件 - \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON Azure 资源管理器模板参数文件 - \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell 命令

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. 在资源组下添加角色分配。 与上一角色分配项类似，以下示例对“所有者”角色使用定义标识符，并向其提供不同于蓝图参数的另一参数  。 此示例使用 GUID 为 `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` 的“所有者”  内置角色。

   - JSON 文件 - \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell 命令

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>发布蓝图

现在，项目已添加到蓝图，可以将其发布了。 发布后，即可将其分配到订阅。

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

`{BlueprintVersion}` 的值为最大长度为 20 个字符的字母、数字和连字符（不含空格或其他特殊字符）字符串。 使用唯一且具有信息性的内容，如 v20180622-135541  。

## <a name="assign-a-blueprint"></a>分配蓝图

使用 PowerShell 发布蓝图后，即可将其分配给订阅。 将你创建的蓝图分配给管理组层次结构下的一个订阅。 如果蓝图保存到某个订阅，则只能将其分配给该订阅。 **Blueprint** 参数指定要分配的蓝图。 若要提供名称、位置、标识、锁定和蓝图参数，请在 `New-AzBlueprintAssignment` cmdlet 上使用匹配的 PowerShell 参数，或在 **AssignmentFile** 参数 JSON 文件中提供这些参数。

1. 通过将蓝图部署分配到订阅，运行它。 由于“参与者”和“所有者”参数需要主体的 objectId 数组才能授予角色分配，因此请使用 [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) 来收集 objectId，以供自己的用户、组或服务主体在 **AssignmentFile** 中使用   。

   - JSON 文件 - blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell 命令

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - 用户分配的托管标识

     蓝图分配也可使用[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
     在此示例中，JSON 分配文件的 **identity** 部分更改如下。 将 `{tenantId}`、`{subscriptionId}`、`{yourRG}` 和 `{userIdentity}` 分别替换为你的 tenantId、subscriptionId、资源组名称和用户分配的托管标识的名称。

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     **用户分配的托管标识**可以位于任何订阅和资源组中，只要分配蓝图的用户有权访问它即可。

     > [!IMPORTANT]
     > Azure 蓝图不管理用户分配的托管标识。 用户负责分配足够的角色和权限，否则蓝图分配会失败。

## <a name="clean-up-resources"></a>清理资源

### <a name="unassign-a-blueprint"></a>取消分配蓝图

可以从订阅中删除蓝图。 通常会在不再需要项目资源时将其删除。 删除蓝图时，作为该蓝图的一部分分配的项目将保留。 若要删除蓝图分配，请使用 `Remove-AzBlueprintAssignment` cmdlet：

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 PowerShell 创建、分配并删除了蓝图。 若要详细了解 Azure 蓝图，请继续学习蓝图生命周期文章。

> [!div class="nextstepaction"]
> [了解蓝图生命周期](./concepts/lifecycle.md)