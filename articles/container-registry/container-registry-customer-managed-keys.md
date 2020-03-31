---
title: 使用客户管理的密钥进行静态加密
description: 了解 Azure 容器注册表的静态加密，以及如何使用存储在 Azure 密钥保管库中的客户托管密钥加密注册表
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498946"
---
# <a name="encryption-using-customer-managed-keys"></a>使用客户管理的密钥进行加密

在 Azure 容器注册表中存储图像和其他项目时，Azure 会自动使用[服务管理的密钥](../security/fundamentals/encryption-atrest.md#data-encryption-models)对静态注册表内容进行加密。 您可以使用在 Azure 密钥保管库中创建和管理的密钥，使用其他加密层补充默认加密。 本文将引导您使用 Azure CLI 和 Azure 门户完成这些步骤。

通过与[Azure 密钥保管库](../key-vault/key-vault-overview.md)集成，支持使用客户管理密钥进行服务器端加密。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。 使用 Azure Key Vault 还可以审核密钥用法。

此功能在**高级**容器注册表服务层中可用。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表 SKU](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能当前处于预览状态，并应用一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>
   
## <a name="preview-limitations"></a>预览版限制 

* 当前，您只能在创建注册表时启用此功能。
* 在注册表上启用客户管理的密钥后，无法禁用它。
* 使用客户管理的密钥加密的注册表中当前不支持[内容信任](container-registry-content-trust.md)。
* 在使用客户管理的密钥加密的注册表中[，ACR 任务](container-registry-tasks-overview.md)的运行日志当前仅保留 24 小时。 如果需要将日志保留更长时间，请参阅[导出和存储任务运行日志](container-registry-tasks-logs.md#alternative-log-storage)的指导。

## <a name="prerequisites"></a>先决条件

要使用本文中的 Azure CLI 步骤，需要 Azure CLI 版本 2.2.0 或更高版本。 如果需要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="enable-customer-managed-key---cli"></a>启用客户管理密钥 - CLI

### <a name="create-a-resource-group"></a>创建资源组

如果需要，运行[az 组创建][az-group-create]命令以创建用于创建密钥保管库、容器注册表和其他必需资源的资源组。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

使用[az 标识创建][az-identity-create]命令为 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 注册表将使用此标识访问密钥保管库服务。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

在命令输出中，请注意以下值：`id`和`principalId`。 在后续步骤中，您需要这些值来配置对密钥保管库的注册表访问。

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

为方便起见，在环境变量中存储这些值：

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>创建 key vault

使用[az 密钥保管库创建][az-keyvault-create]密钥保管库以存储客户管理的密钥以进行注册表加密。 

为了防止意外删除密钥或密钥保管库而导致的数据丢失，必须启用以下设置：**软删除**和**清除保护**。 以下示例包括这些设置的参数： 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>添加密钥保管库访问策略

为密钥保管库配置策略，以便标识可以访问它。 在以下[az keyvault set 策略][az-keyvault-set-policy]命令中，您将传递以前存储在环境变量中的托管标识的主体 ID。 设置密钥权限**以获取**、**解包密钥**和**包装密钥**。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>创建密钥并获取密钥 ID

运行[az 密钥库密钥创建][az-keyvault-key-create]命令以在密钥保管库中创建密钥。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

在命令输出中，记下密钥的 ID `kid`。 在下一步中使用此 ID：

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
为方便起见，将此值存储在环境变量中：

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>使用客户管理的密钥创建注册表

运行[az acr 创建][az-acr-create]命令以创建注册表并启用客户管理的密钥。 传递以前存储在环境变量中的托管标识主体 ID 和密钥 ID：

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>显示加密状态

要显示是否启用了客户托管密钥的注册表加密，运行 az [acr 加密显示][az-acr-encryption-show]命令：

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>启用客户管理密钥 - 门户

### <a name="create-a-managed-identity"></a>创建托管标识

在 Azure 门户中为 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 有关步骤，请参阅[创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

请注意托管标识**的资源名称**。 在后面的步骤中需要此名称。

![在 Azure 门户中创建用户分配的托管标识](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>创建 key vault

有关创建密钥保管库的步骤，请参阅[快速入门：使用 Azure 门户从 Azure 密钥保管库设置和检索机密](../key-vault/quick-create-portal.md)。

在 **"基本"** 选项卡中为客户管理的密钥创建密钥保管库时，必须启用以下保护设置：**软删除**和**清除保护**。 这些设置有助于防止意外删除密钥或密钥保管库而导致的数据丢失。

![在 Azure 门户中创建密钥保管库](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>添加密钥保管库访问策略

为密钥保管库配置策略，以便标识可以访问它。

1. 导航到密钥保管库。
1. 选择 **"设置** > **访问策略> +添加访问策略**。
1. 选择 **"键"权限**，然后选择 **"获取**、**取消包装键**"和 **"包装键**"。
1. **选择"选择主体**"并选择用户分配的托管标识的资源名称。  
1. 选择 **"添加**"，然后选择 **"保存**"。

![创建密钥保管库访问策略](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>创建密钥

1. 导航到密钥保管库。
1. 选择**设置** > **键**。
1. 选择 **"生成/导入**"并输入键的唯一名称。
1. 接受剩余的默认值，然后选择 **"创建**"。
1. 创建后，选择密钥并记下当前密钥版本。

### <a name="create-azure-container-registry"></a>创建 Azure 容器注册表

1. 选择 **"创建资源** > **容器** > **容器注册表**"。
1. 在 **"基本"** 选项卡中，选择或创建资源组，然后输入注册表名称。 在**SKU**中，选择 **"高级**"。
1. 在 **"加密"** 选项卡中，在**客户管理的密钥**中，选择**启用**。
1. 在 **"标识"** 中，选择您创建的托管标识。
1. 在**加密密钥**中，**从密钥保管库中选择**。
1. 在"**从 Azure 密钥库中选择密钥"** 窗口中，选择在上一节中创建的密钥保管库、密钥和版本。
1. 在 **"加密**"选项卡中，选择 **"审阅 + 创建**"。
1. 选择 **"创建**"以部署注册表实例。

![在 Azure 门户中创建容器注册表](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

要查看门户中的注册表的加密状态，请导航到注册表。 在 **"设置"** 下，选择 **"加密（预览）"。**

## <a name="enable-customer-managed-key---template"></a>启用客户管理密钥 - 模板

您还可以使用资源管理器模板创建注册表并使用客户管理的密钥启用加密。 

以下模板将创建新的容器注册表和用户分配的托管标识。 将以下内容复制到新文件，并使用文件名（如`CMKtemplate.json`）保存它。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

按照前几节中的步骤创建以下资源：

* 按名称标识的密钥保管库
* 密钥保管库密钥，由密钥 ID 标识

运行以下[az 组部署创建][az-group-deployment-create]命令，使用前面的模板文件创建注册表。 如果指示，请提供新的注册表项名称和托管标识名称，以及您创建的密钥保管库名称和密钥 ID。 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>显示加密状态

要显示注册表加密的状态，运行 [az acr 加密显示状态][az-acr 加密-显示状态]命令：

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>使用注册表

启用注册表使用客户托管密钥加密数据后，可以执行与未使用客户托管密钥加密的注册表执行的相同注册表操作。 例如，您可以使用注册表进行身份验证并推送 Docker 映像。 请参阅["推送"中的示例命令并拉取图像](container-registry-get-started-docker-cli.md)。

## <a name="rotate-key"></a>旋转键

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 创建新密钥，然后更新注册表以使用新密钥加密数据。 可以使用 Azure CLI 或在门户中执行这些步骤。

例如，运行[az 密钥库键创建][az-keyvault-key-create]命令以创建新密钥：

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

然后运行[az acr 加密旋转键][az-acr-encryption-rotate-key]命令，传递您以前配置的托管标识的新密钥 ID 和主体 ID：

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>撤消密钥

通过更改密钥保管库上的访问策略或删除密钥来撤消客户管理的加密密钥。 例如，使用[az keyvault 删除策略][az-keyvault-delete-policy]命令更改注册表使用的托管标识的访问策略。 例如：

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

由于注册表无法访问加密密钥，因此撤消密钥可有效地阻止对所有注册表数据的访问。 如果启用了对密钥的访问或还原了已删除的密钥，则注册表将选取该密钥，以便您可以再次访问加密的注册表数据。

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 中静态加密](../security/fundamentals/encryption-atrest.md)的更多详细信息。
* 详细了解访问策略以及如何[保护对密钥保管库的访问](../key-vault/key-vault-secure-your-key-vault.md)。
* 要提供有关 Azure 容器注册表的客户管理密钥的反馈，请访问[ACR GitHub 网站](https://aka.ms/acr/issues)。


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
