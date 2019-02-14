---
title: 通过 Azure 资源管理器模板使用 Key Vault 机密 | Microsoft Docs
description: 说明在部署期间如何以参数形式从密钥保管库传递机密。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: cd45384bfd548cb73c10306dfee942cbcf7c8ca1
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491903"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署过程中使用 Azure Key Vault 传递安全参数值

在部署过程中，可以从 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中检索一个安全值，而不是直接在参数文件中放置安全值（如密码）。 通过引用参数文件中的密钥保管库和密钥来检索值。 值永远不会公开，因为仅引用其密钥保管库 ID。 密钥保管库与部署到的资源组不需要位于同一订阅中。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-key-vaults-and-secrets"></a>部署密钥保管库和机密

若要创建 Key Vault 并添加机密，请参阅：

- [使用 CLI 设置和检索机密](../key-vault/quick-create-cli.md)
- [使用 Powershell 设置和检索机密](../key-vault/quick-create-powershell.md)
- [使用门户设置和检索机密](../key-vault/quick-create-portal.md)
- [使用 .NET 设置和检索机密](../key-vault/quick-create-net.md)
- [使用 Node.js 设置和检索机密](../key-vault/quick-create-node.md)

在将 Key Vault 与资源管理器模板部署集成时，有一些额外的注意事项和要求：

- `enabledForTemplateDeployment` 是一个密钥保管库属性。 若要从资源管理器部署访问此密钥保管库内的机密，`enabledForTemplateDeployment` 必须是 `true`。 
- 如果你不是密钥库所有者，则所有者必须更新密钥库安全策略设置，以便你添加机密。

下面的 Azure CLI 和 Azure PowerShell 示例演示如何执行该操作：

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>授予对机密的访问权限

部署模板的用户必须在包含 Key Vault 的范围内（包括资源组和 Key Vault）具有 `Microsoft.KeyVault/vaults/deploy/action` 权限。 [所有者](../role-based-access-control/built-in-roles.md#owner)和[参与者](../role-based-access-control/built-in-roles.md#contributor)角色均应授予该权限。 如果你创建密钥保管库，则你是所有者，因此你具有权限。 如果密钥保管库位于不同的订阅中，则密钥保管库的所有者必须授予访问权限。

以下过程展示了如何创建具有最小权限的角色，以及如何分配用户

1. 创建自定义角色定义 JSON 文件：

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    将“00000000-0000-0000-0000-000000000000”替换为需要部署模板的用户的订阅 ID。

2. 使用 JSON 文件创建新角色：

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzRoleAssignment` 示例在资源组级别为用户分配自定义角色。  

使用 Key Vault 部署[托管应用程序](../managed-applications/overview.md)的模板时，必须授予对设备资源提供程序服务主体的访问权限。 有关详细信息，请参阅[部署 Azure 托管应用程序时访问 Key Vault 机密](../managed-applications/key-vault-access.md)。

## <a name="reference-secrets-with-static-id"></a>通过静态 ID 引用机密

使用此方法，可以在参数文件（而不是在模板）中引用密钥保管库。 下图显示了参数文件如何引用机密并将该值传递到模板。

![资源管理器密钥保管库集成静态 ID 图](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[教程：在资源管理器模板部署中集成 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md) 使用了此方法。 本教程部署了一个包含管理员密码的虚拟机。 密码参数设置为安全字符串：

![资源管理器密钥保管库集成静态 ID 模板文件](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

现在，为上述模板创建参数文件。 在参数文件中，指定匹配模板中的参数名称的参数。 对于参数值，请从密钥保管库中引用机密。 可以通过传递密钥保管库的资源标识符和机密的名称来引用机密：

![资源管理器密钥保管库集成静态 ID 参数文件](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

如果需要使用当前版本以外的机密版本，请使用 `secretVersion` 属性。

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

部署模板并传入参数文件：

对于 Azure CLI，请使用：

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

对于 PowerShell，请使用：

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>通过动态 ID 引用机密

上一部分介绍了如何通过参数传递密钥保管库机密的静态资源 ID。 但是，在某些情况下，需要引用随当前部署而变的密钥保管库机密。 或者，你需要将参数值传递到模板而不在参数文件中创建引用参数。 在任一情况下，都可以使用链接的模板来动态生成密钥保管库机密的资源 ID。

无法在参数文件中动态生成资源 ID，因为参数文件中不允许使用模板表达式。 

在父模板中添加链接模板，然后传入包含动态生成的资源 ID 的参数。 下图显示链接模板中的参数如何引用机密。

![动态 ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[以下模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id)动态创建 Key Vault ID 并将其作为参数传递。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

部署前面的模板，并为参数提供值。 可以使用 GitHub 中的示例模板，但必须提供环境的参数值。

对于 Azure CLI，请使用：

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

对于 PowerShell，请使用：

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>后续步骤

- 有关密钥保管库的一般信息，请参阅 [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)。
- 有关引用密钥机密的完整示例，请参阅[密钥保管库示例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
