---
title: 使用客户管理的密钥进行静态加密
description: 了解 Azure 容器注册表的静态加密，以及如何使用 Azure Key Vault 中存储的客户管理的密钥来加密注册表
ms.topic: article
ms.date: 05/01/2020
ms.custom: ''
ms.openlocfilehash: d9cd10401e7f645a8edd269184a56dc27544a8c8
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927304"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>使用客户管理的密钥加密注册表

当你在 Azure 容器注册表中存储映像和其他项目时，Azure 会自动使用[服务托管的密钥](../security/fundamentals/encryption-atrest.md#data-encryption-models)对注册表内容进行静态加密。 可以使用在 Azure Key Vault 中创建和管理的密钥，通过一个附加的加密层来补充默认加密。 本文将引导你使用 Azure CLI 和 Azure 门户完成这些步骤。

使用客户管理的密钥进行服务器端加密，是通过与 [Azure Key Vault](../key-vault/general/overview.md) 的集成来支持的。 你可以创建自己的加密密钥并将其存储在密钥保管库中，或使用 Azure Key Vault 的 API 来生成密钥。 使用 Azure Key Vault 还可以审核密钥的使用情况。

此功能在“高级”容器注册表服务层级中可用。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表 SKU](container-registry-skus.md)。

   
## <a name="things-to-know"></a>使用须知

* 目前只能在创建注册表时启用客户管理的密钥。
* 对注册表启用客户管理的密钥后，无法禁用此功能。
* 使用客户管理的密钥加密的注册表目前不支持[内容信任](container-registry-content-trust.md)。
* 在使用客户管理的密钥加密的注册表中，[ACR 任务](container-registry-tasks-overview.md)的运行日志目前只会保留 24 小时。 如果需要将日志保留更长时间，请参阅有关[导出和存储任务运行日志](container-registry-tasks-logs.md#alternative-log-storage)的指南。

## <a name="prerequisites"></a>先决条件

若要使用本文中所述的 Azure CLI 步骤，需要安装 Azure CLI 2.2.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="enable-customer-managed-key---cli"></a>启用客户管理的密钥 - CLI

### <a name="create-a-resource-group"></a>创建资源组

如果需要，请运行 [az group create][az-group-create] 命令创建一个资源组，以用于创建密钥保管库、容器注册表和其他所需资源。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

使用 [az identity create][az-identity-create] 命令为 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 注册表将使用此标识来访问 Key Vault 服务。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

记下命令输出中的以下值：`id` 和 `principalId`。 在后续步骤中，需要使用这些值来配置注册表对密钥保管库的访问。

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

使用 [az keyvault create][az-keyvault-create] 创建一个密钥保管库来存储用于加密注册表的客户管理的密钥。 

为了防止意外删除密钥或密钥保管库而导致数据丢失，必须启用以下设置：“软删除”和“清除保护”。  以下示例包含这些设置的参数： 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>添加密钥保管库访问策略

配置针对密钥保管库的策略，使标识可以访问密钥保管库。 在以下 [az keyvault set-policy][az-keyvault-set-policy] 命令中，请传递前面创建并存储在环境变量中的托管标识的主体 ID。 将密钥权限设置为 **get**、**unwrapKey** 和 **wrapKey**。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>创建密钥并获取密钥 ID

运行 [az keyvault key create][az-keyvault-key-create] 命令以在密钥保管库中创建密钥。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

记下命令输出中的密钥 ID `kid`。 在下一步骤中将会用到此 ID：

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
为方便起见，请将此值存储在环境变量中：

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>使用客户管理的密钥创建注册表

运行 [az acr create][az-acr-create] 命令，以在“高级”服务层级创建注册表并启用客户管理的密钥。 传递前面已存储在环境变量中的托管标识主体 ID 和密钥 ID：

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>显示加密状态

若要显示是否启用了使用客户管理的密钥进行注册表加密，请运行 [az acr encryption show][az-acr-encryption-show] 命令：

```azurecli
az acr encryption show --name <registry-name> 
```

输出类似于：

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>启用客户管理的密钥 - 门户

### <a name="create-a-managed-identity"></a>创建托管标识

在 Azure 门户中为 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 有关步骤，请参阅[创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

在后续步骤中需要用到该标识的名称。

![在 Azure 门户中创建用户分配的托管标识](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>创建 key vault

有关创建密钥保管库的步骤，请参阅[快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密](../key-vault/secrets/quick-create-portal.md)。

为客户管理的密钥创建密钥保管库时，请在“基本信息”选项卡中启用以下保护设置：“软删除”和“清除保护”。  这些设置可以帮助防止因意外删除密钥或密钥保管库而导致的数据丢失。

![在 Azure 门户中创建密钥保管库](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>添加密钥保管库访问策略

配置针对密钥保管库的策略，使标识可以访问密钥保管库。

1. 导航到你的密钥保管库。
1. 选择“设置” > “访问策略”>“+添加访问策略”。 
1. 选择“密钥权限”，然后选择“获取”、“解包密钥”和“包装密钥”。   
1. 选择“选择主体”，然后选择用户分配的托管标识的资源名称。  
1. 依次选择“添加”、“保存”。 

![创建密钥保管库访问策略](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>创建密钥

1. 导航到你的密钥保管库。
1. 选择“设置” > “密钥”。 
1. 选择“+生成/导入”并输入密钥的唯一名称。
1. 接受剩余的默认值，然后选择“创建”。
1. 创建后，选择该密钥并记下当前密钥版本。

### <a name="create-azure-container-registry"></a>创建 Azure 容器注册表

1. 选择“创建资源” > “容器” > “容器注册表”。  
1. 在“基本信息”选项卡中选择或创建一个资源组，然后输入注册表名称。 在“SKU”中选择“高级”。 
1. 在“加密”选项卡上的“客户管理的密钥”中，选择“已启用”。  
1. 在“标识”中，选择你创建的托管标识。
1. 在“加密”中，选择“从 Key Vault 中选择”。 
1. 在“从 Azure Key Vault 中选择密钥”窗口中，选择在上一部分创建的密钥保管库、密钥和版本。
1. 在“加密”选项卡中，选择“查看 + 创建”。 
1. 选择“创建”以部署注册表实例。

![在 Azure 门户中创建容器注册表](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

若要在门户中查看注册表的加密状态，请导航到注册表。 在“设置”下，选择“加密”。 

## <a name="enable-customer-managed-key---template"></a>启用客户管理的密钥 - 模板

还可以使用资源管理器模板来创建注册表，并启用使用客户管理的密钥进行加密。 

以下模板创建新的容器注册表和用户分配的托管标识。 将以下内容复制到新文件，并使用类似于 `CMKtemplate.json` 的文件名保存该文件。

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
* 密钥保管库密钥，按密钥 ID 标识

运行以下 [az group deployment create][az-group-deployment-create] 命令，以使用上述模板文件创建注册表。 根据指示提供新的注册表名称和托管标识名称，以及你创建的密钥保管库名称和密钥 ID。 

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

若要显示注册表加密的状态，请运行 [az acr encryption show][az-acr-encryption-show] 命令：

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="use-the-registry"></a>使用注册表

在注册表中启用客户管理的密钥后，可以像在未使用客户管理的密钥加密的注册表中执行操作一样，执行相同的注册表操作。 例如，可向注册表进行身份验证，以及推送 Docker 映像。 请参阅[推送和拉取映像](container-registry-get-started-docker-cli.md)中的示例命令。

## <a name="rotate-key"></a>轮换密钥

在合规策略中轮换用于加密注册表的客户管理密钥。 创建新密钥或更新密钥版本，然后更新注册表以使用该密钥加密数据。 可以使用 Azure CLI 或者在门户中执行这些步骤。

轮换密钥时，通常需要指定在创建注册表时所用的同一标识。 （可选）配置新的用户分配标识以用于进行密钥访问，或者启用并指定注册表的系统分配标识。

> [!NOTE]
> 确保针对为进行密钥访问而配置的标识设置了所需的[密钥保管库访问策略](#add-key-vault-access-policy)。 

### <a name="azure-cli"></a>Azure CLI

使用 [az keyvault key][az-keyvault-key] 命令来创建或管理密钥保管库密钥。 例如，若要创建新密钥版本或密钥，请运行 [az keyvault key create][az-keyvault-key-create] 命令：

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name> 

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name> 
```

然后运行 [az acr encryption rotate-key][az-acr-encryption-rotate-key] 命令（传递新密钥 ID 以及想要配置的标识）：

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>门户

使用注册表的“加密”设置来更新客户管理的密钥所用的密钥版本、密钥、密钥保管库或标识设置。 

例如，若要生成并配置新的密钥版本：

1. 在门户中导航到你的注册表。 
1. 在“设置”下，选择“加密” > “更改密钥”。  
1. 选择“选择密钥”
    
    ![在 Azure 门户中轮换密钥](./media/container-registry-customer-managed-keys/rotate-key.png)
1. 在“从 Azure Key Vault 中选择密钥”窗口中，选择前面配置的密钥保管库和密钥，然后在“版本”中选择“新建”。  
1. 在“创建密钥”窗口中，依次选择“生成”、“创建”。  
1. 完成密钥选择，然后选择“保存”。

## <a name="revoke-key"></a>撤销密钥

通过更改针对密钥保管库的访问策略或者通过删除密钥，来撤销客户管理的加密密钥。 例如，使用 [az keyvault delete-policy][az-keyvault-delete-policy] 命令更改注册表使用的托管标识的访问策略：

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

撤销密钥会有效阻止对所有注册表数据的访问，因为注册表无法访问加密密钥。 如果启用了对密钥的访问或者还原了已删除的密钥，则注册表将选取该密钥，使你可以再次访问已加密的注册表数据。

## <a name="advanced-scenarios"></a>高级方案

### <a name="system-assigned-identity"></a>系统分配的标识

可以配置注册表的系统分配的托管标识，以访问密钥保管库中的加密密钥。 如果你不熟悉 Azure 资源的各种托管标识，请参阅[概述](../active-directory/managed-identities-azure-resources/overview.md)。

若要在门户中启用注册表的系统分配的标识，请执行以下操作：

1. 在门户中导航到你的注册表。 
1. 选择“设置” >  “标识”。 
1. 在“系统分配”下，将“状态”设置为“开”。   选择“保存”。
1. 复制标识的“对象 ID”。

若要授予标识对密钥保管库的访问权限，请执行以下操作：

1. 导航到你的密钥保管库。
1. 选择“设置” > “访问策略”>“+添加访问策略”。 
1. 选择“密钥权限”，然后选择“获取”、“解包密钥”和“包装密钥”。   
1. 选择“选择主体”，并搜索系统分配的托管标识的对象 ID，或注册表的名称。  
1. 依次选择“添加”、“保存”。 

若要将注册表的加密设置更新为使用该标识，请执行以下操作：

1. 在门户中导航到你的注册表。 
1. 在“设置”下，选择“加密” > “更改密钥”。  
1. 在“标识”中选择“系统分配”，然后选择“保存”。  

### <a name="key-vault-firewall"></a>Key Vault 防火墙

如果你的 Azure 密钥保管库部署在带有 Key Vault 防火墙的虚拟网络中，请执行以下步骤：

1. 将注册表加密配置为使用注册表的系统分配的标识。 请参阅前一部分。
2. 将密钥保管库配置为允许任何[受信任的服务](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)进行访问。 

有关详细步骤，请参阅[配置 Azure Key Vault 防火墙和虚拟网络](../key-vault/general/network-security.md)。 

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)。
* 详细了解访问策略以及如何[保护对密钥保管库的访问](../key-vault/general/secure-your-key-vault.md)。


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
