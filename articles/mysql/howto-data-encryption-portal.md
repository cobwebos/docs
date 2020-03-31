---
title: 数据加密 - Azure 门户 - MySQL 的 Azure 数据库
description: 了解如何使用 Azure 门户为 MySQL 为 Azure 数据库设置和管理数据加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299033"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>通过使用 Azure 门户，MySQL Azure 数据库的数据加密

了解如何使用 Azure 门户为 MySQL 的 Azure 数据库设置和管理数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 在 Azure 密钥保管库中，创建用于客户管理的密钥的密钥保管库和密钥。
* 密钥保管库必须具有以下属性才能用作客户管理的密钥：
  * [软删除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保护](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 密钥必须具有以下属性才能用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 能够执行“获取”、“包装密钥”和“解包密钥”操作

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在密钥保管库中，选择**访问策略** > **添加访问策略**。

   ![突出显示了密钥保管库的屏幕截图，并突出显示了访问策略和添加访问策略](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 选择 **"密钥权限**"，然后选择 **"获取**、**换行**、**解包**"和 **"主体**"，这是 MySQL 服务器的名称。 如果在现有主体列表中找不到服务器主体，则需要注册它。 首次尝试设置数据加密时，系统会提示您注册服务器主体，但该加密失败。

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 选择“保存”。****

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>为 MySQL 为 Azure 数据库设置数据加密

1. 在 MySQL 的 Azure 数据库中，选择**数据加密**以设置客户管理的密钥。

   ![MySQL Azure 数据库的屏幕截图，并突出显示数据加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以选择密钥保管库和密钥对，也可以输入密钥标识符。

   ![MySQL Azure 数据库的屏幕截图，突出显示数据加密选项](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 选择“保存”。****

4. 为确保所有文件（包括临时文件）完全加密，请重新启动服务器。

## <a name="restore-or-create-a-replica-of-the-server"></a>还原或创建服务器副本

使用存储在密钥保管库中的客户托管密钥对 MySQL 的 Azure 数据库进行加密后，还将加密任何新创建的服务器副本。 您可以通过本地或异地还原操作或通过副本（本地/跨区域）操作制作此新副本。 因此，对于加密的 MySQL 服务器，可以使用以下步骤创建加密还原的服务器。

1. 在服务器上，选择 **"概述** > **还原**"。

   ![MySQL Azure 数据库的屏幕截图，突出显示了概述和还原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   或者，对于启用复制的服务器，在 **"设置"** 标题下，选择 **"复制**"。

   ![MySQL Azure 数据库的屏幕截图，并突出显示复制](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 还原操作完成后，创建的新服务器将使用主服务器的密钥进行加密。 但是，服务器上的功能和选项被禁用，并且无法访问服务器。 这可以防止任何数据操作，因为新服务器的标识尚未被授予访问密钥保管库的权限。

   ![MySQL Azure 数据库的屏幕截图，突出显示不可访问状态](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 要使服务器可访问，请重新验证还原服务器上的密钥。 选择**数据加密** > **重新验证密钥**。

   > [!NOTE]
   > 第一次重新验证的尝试将失败，因为需要授予新服务器的服务主体对密钥保管库的访问权限。 要生成服务主体，请选择 **"重新验证密钥**"，该键将显示错误，但会生成服务主体。 之后，请参阅[本文前面的步骤](#set-the-right-permissions-for-key-operations)。

   ![MySQL Azure 数据库的屏幕截图，重新验证步骤突出显示](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   您必须授予密钥保管库对新服务器的访问权限。

4. 注册服务主体后，再次重新验证密钥，服务器将恢复其正常功能。

   ![MySQL Azure 数据库的屏幕截图，显示还原的功能](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>使用 Azure 资源管理器模板启用数据加密

除了 Azure 门户之外，您还可以使用新服务器和现有服务器的 Azure 资源管理器模板在 MySQL 服务器上启用 Azure 数据库上的数据加密。

### <a name="for-a-new-server"></a>对于新服务器

使用预先创建的 Azure 资源管理器模板之一为服务器预配启用了数据加密：[数据加密示例](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

此 Azure 资源管理器模板为 MySQL 服务器创建 Azure 数据库，并使用作为参数传递的**KeyVault**和**密钥**在服务器上启用数据加密。

### <a name="for-an-existing-server"></a>对于现有服务器
此外，可以使用 Azure 资源管理器模板在 MySQL 服务器的现有 Azure 数据库上启用数据加密。

* 传递前面在属性对象`keyVaultKeyUri`中的属性下复制的 Azure 密钥保管库密钥的 URI。

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
