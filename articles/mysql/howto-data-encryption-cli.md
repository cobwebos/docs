---
title: 数据加密 - Azure CLI - MySQL 的 Azure 数据库
description: 了解如何使用 Azure CLI 为 MySQL 设置和管理 Azure 数据库的数据加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 37f6f0dc9c1221207273110252bff445d2e1245b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459094"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>通过使用 Azure CLI 为 MySQL 的 Azure 数据库进行数据加密

了解如何使用 Azure CLI 为 MySQL 的 Azure 数据库设置和管理数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 创建用于客户管理的密钥的密钥保管库和密钥。 还在密钥保管库上启用清除保护和软删除。

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* 在创建的 Azure 密钥保管库中，创建将用于 MySQL Azure 数据库的数据加密的密钥。

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* 为了使用现有的密钥保管库，它必须具有以下属性才能用作客户管理的密钥：
  * [软删除](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保护](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 密钥必须具有以下属性才能用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 执行**获取**、**换行**、**解包**操作

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 有两种方法可以为 MySQL 获取 Azure 数据库的托管标识。

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>为具有托管标识的 MySQL 服务器创建新的 Azure 数据库。

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>更新 MySQL 服务器的现有 Azure 数据库以获取托管标识。

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. 为**主体**设置**密钥权限**（**获取**、**换行**、**解包**），这是 MySQL 服务器的名称。

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>为 MySQL 为 Azure 数据库设置数据加密

1. 使用 Azure 密钥保管库中创建的密钥为 MySQL 启用 Azure 数据库的数据加密。

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    密钥 URL：https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>对还原服务器或副本服务器使用数据加密

使用存储在密钥保管库中的客户托管密钥对 MySQL 的 Azure 数据库进行加密后，还将加密任何新创建的服务器副本。 您可以通过本地或异地还原操作或通过副本（本地/跨区域）操作制作此新副本。 因此，对于加密的 MySQL 服务器，可以使用以下步骤创建加密还原的服务器。

### <a name="creating-a-restoredreplica-server"></a>创建还原/复制服务器

  *  [创建还原服务器](howto-restore-server-cli.md) 
  *  [创建读取副本服务器](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>还原服务器后，重新验证还原的服务器的数据加密

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>用于 MySQL 的 Azure 数据库的密钥的其他功能

### <a name="get-the-key-used"></a>使用密钥

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>列出使用的密钥

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>删除正在使用的密钥

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>使用 Azure 资源管理器模板启用数据加密

除了 Azure 门户之外，您还可以使用新服务器和现有服务器的 Azure 资源管理器模板在 MySQL 服务器上启用 Azure 数据库上的数据加密。

### <a name="for-a-new-server"></a>对于新服务器

使用预先创建的 Azure 资源管理器模板之一为服务器预配启用了数据加密：[数据加密示例](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

此 Azure 资源管理器模板为 MySQL 服务器创建 Azure 数据库，并使用作为参数传递的**KeyVault**和**密钥**在服务器上启用数据加密。

### <a name="for-an-existing-server"></a>对于现有服务器
此外，可以使用 Azure 资源管理器模板在 MySQL 服务器的现有 Azure 数据库上启用数据加密。

* 传递之前在属性对象`Uri`中的属性下复制的 Azure 密钥保管库密钥的资源 ID。

* 使用*2020-01-01 预览*作为 API 版本。

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
      "type": "Microsoft.DBforMySQL/servers",
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
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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

 要了解有关数据加密的详细信息，请参阅[使用客户管理的密钥进行 MySQL 数据加密的 Azure 数据库](concepts-data-encryption-mysql.md)。
