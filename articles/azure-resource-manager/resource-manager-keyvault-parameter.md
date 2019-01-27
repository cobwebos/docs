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
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: e04ee6b74be0bb7ec81166f43295a8a9377f8cf8
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381518"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署过程中使用 Azure Key Vault 传递安全参数值

在部署过程中，需要将安全值（例如密码）作为参数传递时，可从 [Azure 密钥保管库](../key-vault/key-vault-whatis.md)检索值。 通过引用参数文件中的密钥保管库和密钥来检索值。 值永远不会公开，因为仅引用其密钥保管库 ID。 密钥保管库与部署到的资源组不需要位于同一订阅中。

## <a name="deploy-a-key-vault-and-secret"></a>部署密钥保管库和机密

若要创建密钥保管库和机密，请使用 Azure CLI 或 PowerShell。 `enabledForTemplateDeployment` 是一个密钥保管库属性。 若要从资源管理器部署访问此密钥保管库内的机密，`enabledForTemplateDeployment` 必须是 `true`。 

以下示例 Azure PowerShell 和 Azure CLI 脚本演示了如何创建密钥保管库和机密。

对于 Azure CLI，请使用：

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

对于 PowerShell，请使用：

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

如果是从 Cloud Shell 外部运行 PowerShell 脚本，请改用以下命令来生成密码：

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

对于使用资源管理器模板：有关分步说明，请参阅[教程：在资源管理器模板部署中集成 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault)。

> [!NOTE]
> 每个 Azure 服务具有特定的密码要求。 例如，Azure 虚拟机的要求可以在[创建 VM 时，密码有什么要求？](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)中找到。

## <a name="enable-access-to-the-secret"></a>启用密钥访问权限

不是将 `enabledForTemplateDeployment` 设置为 `true`，部署模板的用户必须在包含密钥保管库的范围内（包括资源组和密钥保管库）具有 `Microsoft.KeyVault/vaults/deploy/action` 权限。 [所有者](../role-based-access-control/built-in-roles.md#owner)和[参与者](../role-based-access-control/built-in-roles.md#contributor)角色均应授予该权限。 如果你创建密钥保管库，则你是所有者，因此你具有权限。 如果密钥保管库在不同的订阅下，则密钥保管库的所有者必须授予访问权限。

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
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzureRmRoleAssignment` 示例在资源组级别为用户分配自定义角色。  

使用 Key Vault 部署[托管应用程序](../managed-applications/overview.md)的模板时，必须授予对设备资源提供程序服务主体的访问权限。 有关详细信息，请参阅[部署 Azure 托管应用程序时访问 Key Vault 机密](../managed-applications/key-vault-access.md)。

## <a name="reference-a-secret-with-static-id"></a>通过静态 ID 引用机密

接收密钥保管库机密的模板与任何其他模板一样。 这是因为在参数文件（而不是在模板）中引用密钥保管库。 下图显示了参数文件如何引用机密并将该值传递到模板。

![静态 ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

例如，[以下模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json)部署包括管理员密码的 SQL 数据库。 密码参数设置为安全字符串。 但是，该模板未指定该值的来源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

现在，为上述模板创建参数文件。 在参数文件中，指定匹配模板中的参数名称的参数。 对于参数值，请从密钥保管库中引用机密。 可以通过传递密钥保管库的资源标识符和机密的名称来引用机密。 在[以下参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json)中，Key Vault 机密必须已存在，而且为其资源 ID 提供了静态值。 在本地复制此文件，并设置订阅 ID、保管库名称和 SQL server 名称。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

如果需要使用当前版本以外的机密版本，请使用 `secretVersion` 属性。

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

现在，部署模板并传入参数文件。 可以使用 GitHub 中的示例模板，但必须使用本地参数文件并将值设置为自己的环境。

对于 Azure CLI，请使用：

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

对于 PowerShell，请使用：

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>通过动态 ID 引用机密

上一部分介绍了如何通过参数传递密钥保管库机密的静态资源 ID。 但是，在某些情况下，需要引用随当前部署而变的密钥保管库机密。 或者，你可能只是希望将参数值传递到模板而不在参数文件中创建引用参数。 在任一情况下，都可以使用链接的模板来动态生成密钥保管库机密的资源 ID。

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

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

对于 PowerShell，请使用：

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>后续步骤
* 有关密钥保管库的一般信息，请参阅 [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)。
* 有关引用密钥机密的完整示例，请参阅[密钥保管库示例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。