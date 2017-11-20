---
title: "通过 Azure 资源管理器模板使用 Key Vault 机密 | Microsoft Docs"
description: "说明在部署期间如何以参数形式从密钥保管库传递机密。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署过程中使用 Azure Key Vault 传递安全参数值

在部署过程中，需要将安全值（例如密码）作为参数传递时，可从 [Azure 密钥保管库](../key-vault/key-vault-whatis.md)检索值。 通过引用参数文件中的密钥保管库和密钥来检索值。 值永远不会公开，因为仅引用其密钥保管库 ID。 不需要每次部署资源时手动输入机密的值。 密钥保管库与部署到的资源组不需要位于同一订阅中。 引用密钥保管库时，需要包括订阅 ID。

创建密钥保管库时，将 enabledForTemplateDeployment 属性设置为 true。 通过将该值设置为 true，在部署过程中可允许来自资源管理器模板的访问。

## <a name="deploy-a-key-vault-and-secret"></a>部署密钥保管库和机密

若要创建密钥保管库和机密，请使用 Azure CLI 或 PowerShell。 请注意，已为模板部署启用密钥保管库。 

对于 Azure CLI，请使用：

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

对于 PowerShell，请使用：

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>启用密钥访问权限

无论使用的是新密钥保管库还是现有密钥保管库，请确保部署模板的用户可以访问密钥。 部署引用某个密钥的模板的用户必须具有密钥保管库的 `Microsoft.KeyVault/vaults/deploy/action` 权限。 [所有者](../active-directory/role-based-access-built-in-roles.md#owner)和[参与者](../active-directory/role-based-access-built-in-roles.md#contributor)角色均应授予该权限。

## <a name="reference-a-secret-with-static-id"></a>通过静态 ID 引用机密

接收密钥保管库机密的模板与任何其他模板一样。 这是因为在参数文件（而不是在模板）中引用密钥保管库。 例如，以下模板部署包括管理员密码的 SQL 数据库。 密码参数设置为安全字符串。 但是，该模板未指定该值的来源。

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

现在，为上述模板创建参数文件。 在参数文件中，指定匹配模板中的参数名称的参数。 对于参数值，请从密钥保管库中引用机密。 可以通过传递密钥保管库的资源标识符和机密的名称来引用机密。 在下面示例中，密钥保管库机密必须已存在，而且为其资源 ID 提供了静态值。

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

现在，部署模板并传入参数文件。 对于 Azure CLI，请使用：

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

对于 PowerShell，请使用：

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>通过动态 ID 引用机密

上一节介绍了如何传递密钥保管库机密的静态资源 ID。 但是，在某些情况下，需要引用随当前部署而变的密钥保管库机密。 在这种情况下，不能在参数文件中对资源 ID 进行硬编码。 遗憾的是，不能在参数文件中动态生成资源 ID，因为参数文件中不允许模板表达式。

要动态生成密钥保管库机密的资源 ID，必须将需要机密的资源迁移到嵌套式模板。 在主模板中添加嵌套式模板，然后传入包含动态生成的资源 ID 的参数。 嵌套模板必须通过外部 URI 提供。 本文的其余部分假定已将前面的模板添加到存储帐户，并可通过 URI (`https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`) 获得该模板。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

部署前面的模板，并为参数提供值。

## <a name="next-steps"></a>后续步骤
* 有关密钥保管库的一般信息，请参阅 [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)。
* 有关引用密钥机密的完整示例，请参阅[密钥保管库示例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
