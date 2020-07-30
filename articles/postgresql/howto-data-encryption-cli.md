---
title: 数据加密-Azure CLI-Azure Database for PostgreSQL 单一服务器
description: 了解如何使用 Azure CLI 设置和管理 Azure Database for PostgreSQL 单一服务器的数据加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.openlocfilehash: 64cf2568b448c74748be63901cafb51305eab713
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386361"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>使用 Azure CLI Azure Database for PostgreSQL 单个服务器的数据加密

了解如何使用 Azure CLI 设置和管理 Azure Database for PostgreSQL 单一服务器的数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 创建密钥保管库和密钥，以用于客户管理的密钥。 同时启用密钥保管库上的 "清除保护" 和 "软删除"。

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* 在创建的 Azure Key Vault 中，创建将用于 Azure Database for PostgreSQL 单一服务器的数据加密的密钥。

   ```azurecli-interactive
   az keyvault key create --name <key_name> -p software --vault-name <vault_name>
   ```

* 为了使用现有的密钥保管库，它必须具有以下属性以用作客户管理的密钥：
  * [软删除](../key-vault/general/soft-delete-overview.md)

      ```azurecli-interactive
      az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
      ```

  * [清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)

      ```azurecli-interactive
      az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
      ```

* 此密钥必须具有以下属性以用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 执行**get**、 **wrap**和**解包**操作

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 可以通过两种方式获取 Azure Database for PostgreSQL 单一服务器的托管标识。

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>使用托管标识创建新的 Azure Database for PostgreSQL 服务器。

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>更新现有 Azure Database for PostgreSQL 服务器以获取托管标识。

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. 设置**主体**的**密钥权限**（**Get**、 **Wrap**、**解包**），这是 PostgreSQL 单一服务器服务器的名称。

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>为 Azure Database for PostgreSQL 单个服务器设置数据加密

1. 使用 Azure Key Vault 中创建的密钥为 Azure Database for PostgreSQL 单一服务器启用数据加密。

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    密钥 url：`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>对还原服务器或副本服务器使用数据加密

在使用 Key Vault 中存储的客户管理的密钥对 Azure Database for PostgreSQL 单一服务器进行加密后，还将所有新创建的服务器副本进行加密。 你可以通过本地或异地还原操作或通过副本（本地/跨区域）操作进行此新复制。 因此，对于加密的 PostgreSQL 单服务器服务器，可以使用以下步骤来创建加密还原服务器。

### <a name="creating-a-restoredreplica-server"></a>创建还原/副本服务器

* [创建还原服务器](howto-restore-server-cli.md)
* [创建读取副本服务器](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>还原服务器后，重新验证还原的服务器的数据加密

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>用于 Azure Database for PostgreSQL 单一服务器的密钥的附加功能

### <a name="get-the-key-used"></a>获取使用的密钥

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

密钥 url：`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>列出使用的密钥

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>删除正在使用的密钥

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>使用 Azure 资源管理器模板启用数据加密

除了 Azure 门户之外，还可以使用 Azure 资源管理器模板为新服务器和现有服务器启用 Azure Database for PostgreSQL 单一服务器上的数据加密。

### <a name="for-a-new-server"></a>对于新服务器

使用预先创建的 Azure 资源管理器模板之一来预配启用了数据加密的服务器：[包含数据加密的示例](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

此 Azure 资源管理器模板创建 Azure Database for PostgreSQL 单一服务器，并使用作为参数传递的**KeyVault**和**密钥**来启用服务器上的数据加密。

### <a name="for-an-existing-server"></a>对于现有服务器
此外，还可以使用 Azure 资源管理器模板在现有 Azure Database for PostgreSQL 单一服务器上启用数据加密。

* 传递先前在 properties 对象中的属性下复制的 Azure Key Vault 密钥的资源 ID `Uri` 。

* 使用*2020-01-01-preview*作为 API 版本。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

 若要了解有关数据加密的详细信息，请参阅[Azure Database for PostgreSQL 通过客户托管的密钥进行单服务器数据加密](concepts-data-encryption-postgresql.md)。
