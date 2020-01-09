---
title: Key Vault 带有模板的机密
description: 说明在部署期间如何以参数形式从密钥保管库传递机密。
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 75757d36be62289023f96adcdb2f81589b54281b
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689658"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署过程中使用 Azure Key Vault 传递安全参数值

你可以在部署过程中从[Azure Key Vault](../../key-vault/key-vault-overview.md)检索值，而不是直接在模板或参数文件中放置安全值（如密码）。 通过引用参数文件中的密钥保管库和密钥来检索值。 值永远不会公开，因为仅引用其密钥保管库 ID。 密钥保管库可以与要部署到的资源组位于不同的订阅中。

本文重点介绍如何将中的敏感值作为模板参数传递。 它不涉及将虚拟机属性设置为 Key Vault 中证书的 URL 的方案。 有关该方案的快速入门模板，请参阅在[虚拟机上安装 Azure Key Vault 的证书](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)。

## <a name="deploy-key-vaults-and-secrets"></a>部署密钥保管库和机密

若要在模板部署过程中访问密钥保管库，请将密钥保管库上的 `enabledForTemplateDeployment` 设置为 `true`。

如果你已经有 Key Vault，请确保它允许模板部署。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

若要创建新的 Key Vault 并添加机密，请使用：

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

作为密钥保管库的所有者，你可以自动获得创建机密的权限。 如果使用机密的用户不是密钥保管库的所有者，请使用以下内容授予访问权限：

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

有关创建密钥保管库和添加机密的详细信息，请参阅：

- [使用 CLI 设置和检索机密](../../key-vault/quick-create-cli.md)
- [使用 Powershell 设置和检索机密](../../key-vault/quick-create-powershell.md)
- [使用门户设置和检索机密](../../key-vault/quick-create-portal.md)
- [使用 .NET 设置和检索机密](../../key-vault/quick-create-net.md)
- [使用 Node.js 设置和检索机密](../../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>授予对机密的访问权限

部署模板的用户必须具有对资源组和密钥保管库的作用域的 `Microsoft.KeyVault/vaults/deploy/action` 权限。 [所有者](../../role-based-access-control/built-in-roles.md#owner)和[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色均应授予该权限。 如果创建了密钥保管库，则你是所有者，因此你具有权限。

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
    将 "00000000-0000-0000-0000-000000000000" 替换为订阅 ID。

2. 使用 JSON 文件创建新角色：

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    示例将自定义角色分配给资源组级别的用户。  

使用 Key Vault 部署[托管应用程序](../managed-applications/overview.md)的模板时，必须授予对设备资源提供程序服务主体的访问权限。 有关详细信息，请参阅[部署 Azure 托管应用程序时访问 Key Vault 机密](../managed-applications/key-vault-access.md)。

## <a name="reference-secrets-with-static-id"></a>通过静态 ID 引用机密

使用此方法，可以在参数文件（而不是在模板）中引用密钥保管库。 下图显示了参数文件如何引用机密并将该值传递到模板。

![资源管理器密钥保管库集成静态 ID 图](./media/key-vault-parameter/statickeyvault.png)

[教程：将 Azure Key Vault 资源管理器模板部署](./template-tutorial-use-key-vault.md)使用此方法。

以下模板部署包括管理员密码的 SQL server。 密码参数设置为安全字符串。 但是，模板不指定该值的来源。

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

现在，为上述模板创建参数文件。 在参数文件中，指定匹配模板中的参数名称的参数。 对于参数值，请从密钥保管库中引用机密。 可以通过传递密钥保管库的资源标识符和机密的名称来引用机密：

在下面的参数文件中，密钥保管库机密必须已存在，并为其资源 ID 提供静态值。

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
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
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
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

部署模板并传入参数文件：

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az group deployment create \
    --resource-group SqlGroup \
    --template-uri <template-file-URI> \
    --parameters <parameter-file>
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>通过动态 ID 引用机密

上一部分介绍了如何通过参数传递密钥保管库机密的静态资源 ID。 但是，在某些情况下，需要引用随当前部署而变的密钥保管库机密。 或者，你需要将参数值传递到模板而不在参数文件中创建引用参数。 在任一情况下，都可以使用链接的模板来动态生成密钥保管库机密的资源 ID。

无法在参数文件中动态生成资源 ID，因为参数文件中不允许使用模板表达式。 

在父模板中，添加嵌套模板并传入包含动态生成的资源 ID 的参数。 下图显示链接模板中的参数如何引用机密。

![动态 ID](./media/key-vault-parameter/dynamickeyvault.png)

以下模板会动态创建密钥保管库 ID 并将其作为参数传递。

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
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "adminLogin": {
                            "type": "string"
                        },
                        "adminPassword": {
                            "type": "securestring"
                        },
                        "location": {
                            "type": "string"
                        }
                    },
                    "variables": {
                        "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
                    },
                    "resources": [
                        {
                            "name": "[variables('sqlServerName')]",
                            "type": "Microsoft.Sql/servers",
                            "apiVersion": "2018-06-01-preview",
                            "location": "[parameters('location')]",
                            "properties": {
                                "administratorLogin": "[parameters('adminLogin')]",
                                "administratorLoginPassword": "[parameters('adminPassword')]"
                            }
                        }
                    ],
                    "outputs": {
                        "sqlFQDN": {
                            "type": "string",
                            "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
                        }
                    }
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
    }
}
```

## <a name="next-steps"></a>后续步骤

- 有关密钥保管库的一般信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/key-vault-overview.md)。
- 有关引用密钥机密的完整示例，请参阅[密钥保管库示例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
