---
title: 快速入门：使用 Azure CLI 创建蓝图
description: 在本快速入门中，通过 Azure CLI 使用 Azure 蓝图创建、定义和部署项目。
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 790e23897a24ea06565572163b8226af88e0bb7c
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057954"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>快速入门：使用 Azure CLI 定义和分配 Azure 蓝图

了解如何创建和分配蓝图来定义常见的模式，以便根据 Azure 资源管理器模板（ARM 模板）、策略、安全性等方面的要求开发可重复使用和可快速部署的配置。 本教程介绍如何使用 Azure 蓝图来执行某些与在组织中创建、发布和分配蓝图相关的常见任务，例如：

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>添加蓝图扩展

若要使 Azure CLI 能够管理蓝图定义和分配，则必须添加该扩展。
此扩展适用于可以使用 Azure CLI 的任何位置，包括 [Windows 10 上的 bash](/windows/wsl/install-win10)、[Cloud Shell](https://shell.azure.com)（独立的或位于门户内的）、[Azure CLI Docker 映像](https://hub.docker.com/_/microsoft-azure-cli)，也适用于在本地安装了 Azure CLI 的任何位置。

1. 请确保安装最新的 Azure CLI（至少为 2.0.76）。 若尚未安装，请遵循[这些说明](/cli/azure/install-azure-cli-windows)。

1. 在所选的 Azure CLI 环境中，使用以下命令导入该扩展：

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. 验证该扩展是否已安装以及是否为预期的版本（至少为 0.1.0）：

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>创建蓝图

定义符合性的标准模式的第一步是根据可用资源构建蓝图。 我们将创建名为“MyBlueprint”的蓝图，以配置订阅的角色和策略分配。 然后，我们将添加资源组、ARM 模板，并在该资源组上添加角色分配。

> [!NOTE]
> 使用 Azure CLI 时，首先创建 blueprint 对象。 对于每个要添加的具有参数的项目，需要在初始蓝图上提前定义该参数 。

1. 创建初始 blueprint 对象。 parameters 参数采用包含所有蓝图级别参数的 JSON 文件。 参数在分配过程中设置并由在后续步骤中添加的项目使用。

   - JSON 文件 - blueprintparms.json

     ```json
     {
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
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > 导入蓝图定义时，请使用文件名 blueprint.json。
     > 调用 [az blueprint import](/cli/azure/ext/blueprint/blueprint#ext-blueprint-az-blueprint-import) 时使用此文件名。

     默认情况下，会在默认订阅中创建蓝图对象。 若要指定管理组，请使用参数 managementgroup。 若要指定订阅，请使用参数 subscription。

1. 将存储项目的资源组添加到定义中。

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. 在订阅中添加角色分配。 在下面的示例中，主体标识被授予指定的角色，配置为蓝图分配过程中所设置的参数。 此示例使用 GUID 为 `b24988ac-6180-42a0-ab88-20f7382dd24c` 的“参与者”内置角色。

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. 在订阅中添加策略分配。 此示例使用 GUID 为 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` 的“将标记及其默认值应用于资源组”内置策略。

   - JSON 文件 - artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. 在订阅中为存储标记（重复使用 storageAccountType 参数）添加其他策略分配。 此附加的策略分配项目演示了蓝图上定义的参数可由多个项目使用。 在示例中，storageAccountType 用于在资源组上设置一个标记。 此值提供有关下一步骤中创建的存储帐户的信息。 此示例使用 GUID 为 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` 的“将标记及其默认值应用于资源组”内置策略。

   - JSON 文件 - artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. 在资源组下添加模板。 ARM 模板的 template 参数包含模板的标准 JSON 组件。 系统会向模板一一传递 **storageAccountType**、**tagName** 和 **tagValue** 蓝图参数，让模板重复使用这些参数。 通过使用参数 parameters 和在使用键-值对的模板 JSON 中注入值，模板可以使用蓝图参数。 蓝图和模板参数名称可以相同。

   - JSON ARM 模板文件 - artifacts\templateStorage.json

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

   - JSON ARM 模板参数文件 - artifacts\templateStorageParams.json

     ```json
     {
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
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. 在资源组下添加角色分配。 与上一角色分配项类似，以下示例对“所有者”角色使用定义标识符，并向其提供不同于蓝图参数的另一参数。 此示例使用 GUID 为 `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` 的“所有者”内置角色。

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>发布蓝图

现在，项目已添加到蓝图，可以将其发布了。 发布后，即可将其分配到订阅。

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

`{BlueprintVersion}` 的值为最大长度为 20 个字符的字母、数字和连字符（不含空格或其他特殊字符）字符串。 使用唯一且具有信息性的内容，如 v20200605-135541。

## <a name="assign-a-blueprint"></a>分配蓝图

使用 Azure CLI 发布蓝图后，即可将其分配给订阅。 将你创建的蓝图分配给管理组层次结构下的一个订阅。 如果蓝图保存到某个订阅，则只能将其分配给该订阅。 blueprint-name 参数指定要分配的蓝图。 若要提供名称、位置、标识、锁定和蓝图参数，请在 `az blueprint assignment create` 命令上使用匹配的 Azure CLI 参数，或在 parameters JSON 文件中提供这些参数。

1. 通过将蓝图部署分配到订阅，运行它。 由于“参与者”和“所有者”参数要求主体的 objectId 数组被授予角色分配，使用 [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) 来收集 objectId，以供自己的用户、组或服务主体用于参数中  。

   - JSON 文件 - blueprintAssignment.json

     ```json
     {
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
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - 用户分配的托管标识

     蓝图分配也可使用[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
     在这种情况下，identity-type 参数设置为 UserAssigned，user-assigned-identities 参数指定标识。 将 `{userIdentity}` 替换为用户分配的托管标识的名称。

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     **用户分配的托管标识**可以位于任何订阅和资源组中，只要分配蓝图的用户有权访问它即可。

     > [!IMPORTANT]
     > Azure 蓝图不管理用户分配的托管标识。 用户负责分配足够的角色和权限，否则蓝图分配会失败。

## <a name="clean-up-resources"></a>清理资源

### <a name="unassign-a-blueprint"></a>取消分配蓝图

可以从订阅中删除蓝图。 通常会在不再需要项目资源时将其删除。 删除蓝图时，作为该蓝图的一部分分配的项目将保留。 若要删除蓝图分配，请使用 `az blueprint assignment delete` 命令：

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Azure CLI 创建、分配和删除了蓝图。 若要详细了解 Azure 蓝图，请继续学习蓝图生命周期文章。

> [!div class="nextstepaction"]
> [了解蓝图生命周期](./concepts/lifecycle.md)