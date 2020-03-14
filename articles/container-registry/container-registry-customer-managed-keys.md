---
title: 通过客户托管的密钥进行静态加密
description: 了解 Azure 容器注册表的静态加密，以及如何使用存储在 Azure Key Vault 中的客户托管密钥来加密注册表
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 7bfc4e9a73280ab330efbeeba51a5dcb0a80da10
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365333"
---
# <a name="encryption-using-customer-managed-keys"></a>使用客户托管的密钥进行加密

当你在 Azure 容器注册表中存储图像和其他项目时，Azure 会自动使用[服务托管的密钥](../security/fundamentals/encryption-atrest.md#data-encryption-models)来加密静态注册表内容。 你可以使用在 Azure Key Vault 中创建和管理的密钥来补充使用额外加密层的默认加密。 本文将指导你完成使用 "Azure CLI" 和 "Azure 门户" 的步骤。

使用客户托管密钥的服务器端加密通过与[Azure Key Vault](../key-vault/key-vault-overview.md)的集成支持。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。 使用 Azure Key Vault，还可以审核密钥用法。

此功能在**高级**容器注册表服务层中提供。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表 sku](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前处于预览阶段，但有一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>
   
## <a name="preview-limitations"></a>预览版限制 

* 当前只能在创建注册表时启用此功能。
* 在注册表中启用客户托管的密钥后，无法将其禁用。
* 使用客户管理的密钥加密的注册表中当前不支持[内容信任](container-registry-content-trust.md)。
* 在使用客户管理的密钥加密的注册表中，运行[ACR 任务](container-registry-tasks-overview.md)的日志当前只保留24小时。 如果需要长时间保留日志，请参阅有关[导出和存储任务运行日志](container-registry-tasks-logs.md#alternative-log-storage)的指南。

## <a name="prerequisites"></a>必备条件

若要使用本文中 Azure CLI 的步骤，需要 Azure CLI 版本2.2.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="enable-customer-managed-key---cli"></a>启用客户托管的密钥-CLI

### <a name="create-a-resource-group"></a>创建资源组

如果需要，请运行[az group create][az-group-create]命令创建资源组，用于创建密钥保管库、容器注册表和其他所需资源。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

使用[az identity create][az-identity-create]命令为 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 注册表将使用此标识来访问 Key Vault 服务。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

在命令输出中，记下以下值： `id` 和 `principalId`。 后续步骤中需要使用这些值来配置对密钥保管库的注册表访问。

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

为方便起见，请将这些值存储在环境变量中：

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>创建 key vault

使用[az keyvault create][az-keyvault-create]创建密钥保管库，以存储用于注册表加密的客户托管密钥。 

若要防止意外删除密钥或密钥保管库导致的数据丢失，必须启用以下设置：**软删除**和**清除保护**。 下面的示例包含这些设置的参数： 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>添加密钥保管库访问策略

为密钥保管库配置策略，以便标识可以访问它。 在下面的[az keyvault][az-keyvault-set-policy]命令中，传递所创建的托管标识的主体 ID，该 ID 存储在环境变量中。 设置**get**、 **unwrapKey**和**wrapKey**的密钥权限。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>创建密钥并获取密钥 ID

运行[az keyvault key create][az-keyvault-key-create]命令，在 key vault 中创建密钥。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

在命令输出中，记下密钥的 ID，`kid`。 在下一步中使用此 ID：

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
为方便起见，请将此值存储在一个环境变量中：

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>使用客户管理的密钥创建注册表

运行[az acr create][az-acr-create]命令，创建一个注册表并启用客户管理的密钥。 传递先前在环境变量中存储的托管标识主体 ID 和密钥 ID：

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>显示加密状态

若要显示是否启用了使用客户托管密钥的注册表加密，请运行[az acr encryption show][az-acr-encryption-show]命令：

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>启用客户托管的密钥-门户

### <a name="create-a-managed-identity"></a>创建托管标识

为 Azure 门户中的 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 有关步骤，请参阅[创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

记下托管标识的**资源名称**。 后面的步骤需要用到此名称。

![在 Azure 门户中创建用户分配的托管标识](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>创建 key vault

有关创建密钥保管库的步骤，请参阅[快速入门：使用 Azure 门户设置和检索 Azure Key Vault 中的机密](../key-vault/quick-create-portal.md)。

为客户管理的密钥创建密钥保管库时，必须在 "**基本**信息" 选项卡中启用以下保护设置： "**软删除**" 和 "**清除保护**"。 这些设置可帮助防止意外删除密钥或密钥保管库导致的数据丢失。

![在 Azure 门户中创建密钥保管库](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>添加密钥保管库访问策略

为密钥保管库配置策略，以便标识可以访问它。

1. 导航到密钥保管库。
1. 选择 "**设置**" > **访问策略 > "+ 添加访问策略**"。
1. 选择 "**密钥权限**"，然后选择 "**获取**"、"**解包密钥**" 和 "**环绕键**"。
1. 选择 "**选择主体**"，并选择用户分配的托管标识的资源名称。  
1. 选择 "**添加**"，然后选择 "**保存**"。

![创建密钥保管库访问策略](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>创建密钥

1. 导航到密钥保管库。
1. 选择 "**设置**" > **项**。
1. 选择 " **+ 生成/导入**"，并输入密钥的唯一名称。
1. 接受剩余的默认值并选择 "**创建**"。
1. 创建后，选择密钥并记下当前密钥版本。

### <a name="create-azure-container-registry"></a>创建 Azure 容器注册表

1. 选择“创建资源” **“容器”** “容器注册表”。 >  > 
1. 在 "**基本**信息" 选项卡中，选择或创建一个资源组，然后输入注册表名称。 在**SKU**中，选择 "**高级**"。
1. 在 "**加密**" 选项卡上的 "**客户管理密钥**" 中，选择 "**已启用**"。
1. 在 "**标识**" 中，选择你创建的托管标识。
1. 在 "**加密密钥**" 中，选择 "**从 Key Vault 选择**"。
1. 在 "**从 Azure Key Vault 选择密钥**" 窗口中，选择在上一部分中创建的密钥保管库、密钥和版本。
1. 在 "**加密**" 选项卡中，选择 "**查看 + 创建**"。
1. 选择 "**创建**" 以部署注册表实例。

![在 Azure 门户中创建容器注册表](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

若要在门户中查看注册表的加密状态，请导航到注册表。 在 "**设置**" 下，选择 "**加密（预览）** "。

## <a name="enable-customer-managed-key---template"></a>启用客户托管的密钥模板

你还可以使用资源管理器模板来创建注册表，并使用客户管理的密钥来启用加密。 

以下模板创建新的容器注册表和用户分配的托管标识。 将以下内容复制到新文件，并使用文件名（如 `CMKtemplate.json`）保存该文件。

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

按照前面部分中的步骤创建以下资源：

* 密钥保管库，按名称标识
* Key vault 密钥，由密钥 ID 标识

运行以下[az group deployment create][az-group-deployment-create]命令以使用上述模板文件创建注册表。 如果指示，请提供新的注册表名称和托管标识名称，以及创建的密钥保管库名称和密钥 ID。 

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

若要显示注册表加密的状态，请运行 [az acr encryption show-status] [az-acr-encryption-status] 命令：

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>使用注册表

使用客户管理的密钥启用注册表加密数据后，可以执行在未使用客户托管的密钥进行加密的注册表中执行的相同注册表操作。 例如，你可以对注册表进行身份验证并推送 Docker 映像。 请参阅[推送和拉取映像](container-registry-get-started-docker-cli.md)中的示例命令。

## <a name="rotate-key"></a>旋转键

你可以根据你的符合性策略，在 Azure Key Vault 中轮替客户管理的密钥。 创建新密钥，然后更新注册表以使用新密钥对数据进行加密。 你可以使用 Azure CLI 或在门户中执行这些步骤。

例如，运行[az keyvault key create][az-keyvault-key-create]命令创建新密钥：

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

然后，运行[az acr encryption 轮换密钥][az-acr-encryption-rotate-key]命令，传递前面配置的托管标识的新密钥 id 和主体 id：

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>废除密钥

通过更改密钥保管库上的访问策略或通过删除密钥来撤消客户管理的加密密钥。 例如，使用[az keyvault delete-policy][az-keyvault-delete-policy]命令更改注册表使用的托管标识的访问策略。 例如：

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

由于注册表无法访问加密密钥，因此，吊销密钥会有效地阻止对所有注册表数据的访问。 如果启用了对密钥的访问或还原了删除的密钥，则注册表将选取该密钥，这样你就可以再次访问已加密的注册表数据了。

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)。
* 详细了解访问策略，以及如何[保护对密钥保管库的访问](../key-vault/key-vault-secure-your-key-vault.md)。
* 若要提供有关 Azure 容器注册表的客户托管密钥的反馈，请访问[ACR GitHub 站点](https://aka.ms/acr/issues)。


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
[az-keyvault-key-create]: /cli/azure/keyvault/keyt#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault/keyt#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault/keyt#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
