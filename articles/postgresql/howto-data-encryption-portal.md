---
title: 数据加密-Azure 门户-Azure Database for PostgreSQL 单一服务器
description: 了解如何使用 Azure 门户设置和管理 Azure Database for PostgreSQL 单一服务器的数据加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fe4c69787b606c601d2dc8b31cadc6dcf57458da
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297061"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>使用 Azure 门户 Azure Database for PostgreSQL 单个服务器的数据加密

了解如何使用 Azure 门户设置和管理 Azure Database for PostgreSQL 单一服务器的数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 在 Azure Key Vault 中，创建要用于客户托管密钥的密钥保管库和密钥。
* Key vault 必须具有以下属性以用作客户管理的密钥：
  * [软删除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除保护](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 此密钥必须具有以下属性以用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 能够执行 get、wrap key 和解包密钥操作

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在 Key Vault 中，选择 "**访问策略**" > "**添加访问策略**"。

   ![突出显示了 "访问策略" 和 "添加访问策略" Key Vault 屏幕截图](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 选择 "**密钥权限**"，然后选择 "**获取**"、"**换行**"、"**解包**" 和 "**主体**"，这是 PostgreSQL 服务器的名称。 如果在现有主体列表中找不到你的服务器主体，则需要注册它。 当你首次尝试设置数据加密时，系统将提示你注册服务器主体，并且失败。  

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 选择“保存”。

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>为 Azure Database for PostgreSQL 单个服务器设置数据加密

1. 在 Azure Database for PostgreSQL 中，选择 "**数据加密**" 设置客户管理的密钥。

   ![Azure Database for PostgreSQL 的屏幕截图，其中突出显示了数据加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 可以选择密钥保管库和密钥对，也可以输入密钥标识符。

   ![Azure Database for PostgreSQL 的屏幕截图，其中突出显示了数据加密选项](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 选择“保存”。

4. 若要确保所有文件（包括临时文件）都是完全加密的，请重新启动服务器。

## <a name="restore-or-create-a-replica-of-the-server"></a>还原或创建服务器的副本

使用在 Key Vault 中存储的客户托管密钥对 Azure Database for PostgreSQL 单个服务器进行加密后，任何新创建的服务器副本也会加密。 你可以通过本地或异地还原操作或通过副本（本地/跨区域）操作进行此新复制。 因此，对于加密的 PostgreSQL 服务器，你可以使用以下步骤来创建加密还原服务器。

1. 在服务器上，选择 "**概述** > **还原**"。

   ![Azure Database for PostgreSQL 的屏幕截图，其中突出显示了概述和还原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   对于启用了复制的服务器，在 "**设置**" 标题下选择 "**复制**"。

   ![Azure Database for PostgreSQL 的屏幕截图，其中突出显示了复制](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 还原操作完成后，创建的新服务器将用主服务器的密钥进行加密。 但服务器上的功能和选项已禁用，并且服务器不可访问。 这会阻止任何数据操作，因为尚未向新服务器的标识授予访问密钥保管库的权限。

   ![突出显示状态为 "不可访问" 的 Azure Database for PostgreSQL 屏幕截图](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 若要使服务器可访问，请重新验证已还原服务器上的密钥。 选择 "**数据加密**" > 重新**验证密钥**。

   > [!NOTE]
   > 重新验证的第一次尝试将失败，因为需要为新服务器的服务主体授予对密钥保管库的访问权限。 若要生成服务主体，请选择 "重新**验证密钥**"，它将显示错误，但会生成服务主体。 之后，请参阅本文前面的[步骤](#set-the-right-permissions-for-key-operations)。

   ![突出显示重新验证步骤的 Azure Database for PostgreSQL 屏幕截图](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   你将需要为密钥保管库访问新服务器。

4. 注册服务主体后再次重新验证密钥，然后服务器恢复其正常功能。

   ![显示已还原功能的 Azure Database for PostgreSQL 屏幕截图](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>使用 Azure 资源管理器模板启用数据加密

除了 Azure 门户之外，还可以使用 Azure 资源管理器模板为新服务器和现有服务器启用 Azure Database for PostgreSQL 单一服务器上的数据加密。

### <a name="for-a-new-server"></a>对于新服务器

使用预先创建的 Azure 资源管理器模板之一来预配启用了数据加密的服务器：[包含数据加密的示例](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

此 Azure 资源管理器模板创建 Azure Database for PostgreSQL 单一服务器，并使用作为参数传递的**KeyVault**和**密钥**来启用服务器上的数据加密。

### <a name="for-an-existing-server"></a>对于现有服务器
此外，还可以使用 Azure 资源管理器模板在现有 Azure Database for PostgreSQL 单一服务器上启用数据加密。

* 传递先前在 properties 对象的 `keyVaultKeyUri` 属性下复制的 Azure Key Vault 密钥的 URI。

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
