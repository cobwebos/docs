---
title: Azure 指南-使用 Azure 资源管理器模板创建 Azure 密钥保管库和保管库访问策略 |Microsoft Docs
description: 演示如何使用 Azure 资源管理器模板创建 Azure 密钥保管库和保管库访问策略。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804308"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>如何使用资源管理器模板创建 Azure Key Vault 和保管库访问策略

[Azure Key Vault](../general/overview.md) 是为密钥、密码、证书等机密及其他机密提供安全存储的云服务。 本指南重点介绍部署 Azure 资源管理器模板 (ARM 模板) 创建密钥保管库的过程。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="create-key-vault-resource-manager-template"></a>创建资源管理器模板 Key Vault

以下模板显示了创建密钥保管库的基本方法。 某些值在模板中指定。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

有关 Key Vault 模板设置的详细信息，请参阅 [KEY VAULT ARM 模板参考](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults)。

> [!IMPORTANT]
> 如果重新部署了模板，则会替代密钥保管库中的任何现有访问策略。 建议 `accessPolicies` 使用现有的访问策略填充属性，以避免失去对密钥保管库的访问权限。 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>将访问策略添加到 Key Vault 资源管理器模板

可以将访问策略部署到现有密钥保管库，无需重新部署整个密钥保管库模板。 以下模板显示了创建访问策略的基本方法。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
有关 Key Vault 模板设置的详细信息，请参阅 [KEY VAULT ARM 模板参考](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies)。

## <a name="other-available-key-vault-resource-manager-templates"></a>其他可用 Key Vault 资源管理器模板

还有其他资源管理器模板可用于 Key Vault 对象：

| 机密 | 键 | 证书 |
|--|--|--|
|[快速入门](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[引用](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|空值|空值|

可在此处找到的更多 Key Vault 模板： [Key Vault 资源管理器引用](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>部署模板

你可以使用以下指南中的 "在编辑器中生成自己的模板" 选项来使用 Azure 门户部署以上模板： [从自定义模板部署资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

你还可以将上述模板保存到文件中，并使用以下命令：  [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，可以就地保留这些资源。 当不再需要资源时，请删除资源组，该资源组将删除密钥保管库和相关资源。 若要使用 Azure CLI 或 Azure PowerShell 删除资源组，请使用以下步骤。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>资源

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的详细信息
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)

## <a name="next-steps"></a>后续步骤

- [保护对密钥保管库的访问](secure-your-key-vault.md)
- [对密钥保管库进行身份验证](authentication.md)
- [Azure Key Vault 开发人员指南](developers-guide.md)
