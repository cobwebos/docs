---
title: 快速入门：使用 REST API 创建蓝图
description: 在本快速入门中，通过 REST API 使用 Azure 蓝图创建、定义和部署项目。
ms.date: 02/26/2020
ms.topic: quickstart
ms.openlocfilehash: ec84e8396ad65aa01f73414b971f27bc95396e2f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745098"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>快速入门：使用 REST API 定义和分配 Azure 蓝图

了解如何创建和分配蓝图以后即可定义常见的模式，以便根据资源管理器模板、策略、安全性等方面的要求开发可重复使用和可快速部署的配置。 本教程介绍如何使用 Azure 蓝图来执行某些与在组织中创建、发布和分配蓝图相关的常见任务，例如：

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。
- 注册 `Microsoft.Blueprint` 资源提供程序。 有关用法说明，请参阅[资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>REST API 入门

如果不熟悉 REST API，请首先查看 [Azure REST API 参考](/rest/api/azure/)，大致了解 REST API，尤其是请求 URI 和请求正文。 本文使用这些概念来提供有关如何使用 Azure 蓝图的说明，并假定具有相关的实践经验。 [ARMClient](https://github.com/projectkudu/ARMClient) 和其他工具可自动处理授权，建议初学者使用。

有关 Azure 蓝图规范，请参阅 [Azure 蓝图 REST API](/rest/api/blueprints/)。

### <a name="rest-api-and-powershell"></a>REST API 和 PowerShell

如果尚无用于进行 REST API 调用的工具，请考虑使用 PowerShell 获取说明。 下面是使用 Azure 进行身份验证的示例标头。 生成身份验证标头，有时称为持有者令牌，然后向要连接到的 REST API URI 提供任何参数或请求正文   ：

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

替换上面 $restUri 变量中的 `{subscriptionId}`，以获取订阅的相关信息  。 $Response 变量可保留 `Invoke-RestMethod` cmdlet 的结果，后者可使用 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 之类的 cmdlet 进行分析。 如果 REST API 服务终结点需要请求正文，请向 `Invoke-RestMethod` 的 `-Body` 参数提供 JSON 格式的变量  。

## <a name="create-a-blueprint"></a>创建蓝图

定义符合性的标准模式的第一步是根据可用资源构建蓝图。 我们将创建名为“MyBlueprint”的蓝图，以配置订阅的角色和策略分配。 然后，我们将添加资源组、资源管理器模板，然后在资源组上添加角色分配。

> [!NOTE]
> 使用 REST API 时，首先创建 blueprint 对象  。 对于每个要添加的具有参数的项目，需要在初始蓝图上提前定义该参数   。

在每个 REST API URI 中，包含替换为自己的值所使用的变量：

- `{YourMG}` - 替换为管理组的 ID
- `{subscriptionId}` - 替换为订阅 ID

> [!NOTE]
> 也可以在订阅级别创建蓝图。 要查看示例，请参阅[在订阅示例级别创建蓝图](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint)。

1. 创建初始 blueprint 对象  。 请求正文包括有关蓝图的属性、要创建的任何资源组，以及所有蓝图级别参数  。 参数在分配过程中设置并由在后续步骤中添加的项目使用。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - 请求正文

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
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

1. 在订阅中添加角色分配。 请求正文可定义项目的种类、与角色定义标识符一致的属性以及以值的数组形式传递的主体标识   。 在下面的示例中，主体标识被授予指定的角色，配置为蓝图分配过程中所设置的参数。 此示例使用 GUID 为 `b24988ac-6180-42a0-ab88-20f7382dd24c` 的“参与者”  内置角色。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - 请求正文

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. 在订阅中添加策略分配。 请求正文可定义项目的种类、与策略或计划定义一致的属性，并配置策略分配，以使用要在蓝图分配过程中配置的已定义蓝图参数   。 此示例使用 GUID 为 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` 的“将标记及其默认值应用于资源组”内置策略。 

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - 请求正文

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. 在订阅中为存储标记（重复使用 storageAccountType 参数）添加其他策略分配  。 此附加的策略分配项目演示了蓝图上定义的参数可由多个项目使用。 在示例中，storageAccountType 用于在资源组上设置一个标记  。 此值提供有关下一步骤中创建的存储帐户的信息。 此示例使用 GUID 为 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` 的“将标记及其默认值应用于资源组”内置策略。 

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - 请求正文

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. 在资源组下添加模板。 资源管理器模板的请求正文包括模板的常规 JSON 组件，并使用 properties.resourceGroup 定义目标资源组   。 系统会向模板一一传递 **storageAccountType**、**tagName** 和 **tagValue** 蓝图参数，让模板重复使用这些参数。 通过定义 properties.parameters 并置于键/值对用于插入值的模板 JSON 内，蓝图参数可供模板使用  。 蓝图和模板参数名称可以相同，但对于如何分别从蓝图项目传入模板项目的说明有所区别。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - 请求正文

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
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
                     "location": "[resourceGroups('storageRG').location]",
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
             },
             "resourceGroup": "storageRG",
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
     }
     ```

1. 在资源组下添加角色分配。 与上一角色分配项类似，以下示例对“所有者”角色使用定义标识符，并向其提供不同于蓝图参数的另一参数  。 此示例使用 GUID 为 `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` 的“所有者”  内置角色。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - 请求正文

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

## <a name="publish-a-blueprint"></a>发布蓝图

现在，项目已添加到蓝图，可以将其发布了。 发布后，即可将其分配到订阅。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

`{BlueprintVersion}` 的值为最大长度为 20 个字符的字母、数字和连字符（不含空格或其他特殊字符）字符串。 使用唯一且具有信息性的内容，如 v20180622-135541  。

## <a name="assign-a-blueprint"></a>分配蓝图

使用 REST API 发布蓝图后，即可将其分配给订阅。 将你创建的蓝图分配给管理组层次结构下的一个订阅。 如果蓝图保存到某个订阅，则只能将其分配给该订阅。 请求正文可指定要分配的蓝图、向蓝图定义中的任何资源组提供名称和位置，并且提供在蓝图上定义的所有参数并供一个或多个附加项目使用  。

在每个 REST API URI 中，包含替换为自己的值所使用的变量：

- `{tenantId}` - 替换为租户 ID
- `{YourMG}` - 替换为管理组的 ID
- `{subscriptionId}` - 替换为订阅 ID

1. 在目标订阅上，向 Azure 蓝图服务主体提供“所有者”角色  。 AppId 是静态的 (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`)，但服务主体 ID 根据租户各有不同。 可以使用以下 REST API 请求租户的详细信息。 它可使用具有不同授权的 [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md)。

   - REST API URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. 通过将蓝图部署分配到订阅，运行它。 由于“参与者”和“所有者”参数要求主体的 objectId 数组被授予角色分配，使用 [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) 来收集 objectId，以供自己的用户、组或服务主体用于请求正文中    。

   - REST API URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - 请求正文

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

   - 用户分配的托管标识

     蓝图分配也可使用[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
     在此示例中，请求正文的 **identity** 部分更改如下： 将 `{yourRG}` 和 `{userIdentity}` 分别替换为资源组名称和用户分配托管标识的名称。

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

可以从订阅中删除蓝图。 通常会在不再需要项目资源时将其删除。 删除蓝图时，作为该蓝图的一部分分配的项目将保留。 若要删除蓝图分配，请使用以下 REST API 操作：

- REST API URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>删除蓝图

若要删除蓝图本身，请使用以下 REST API 操作：

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 REST API 创建、分配并删除了蓝图。 若要详细了解 Azure 蓝图，请继续学习蓝图生命周期文章。

> [!div class="nextstepaction"]
> [了解蓝图生命周期](./concepts/lifecycle.md)