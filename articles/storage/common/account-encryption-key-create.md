---
title: 创建支持表和队列的客户托管密钥的帐户
titleSuffix: Azure Storage
description: 了解如何创建支持为表和队列配置客户托管密钥的存储帐户。 使用 Azure CLI 或 Azure 资源管理器模板来创建一个存储帐户，该帐户依赖于 Azure 存储加密的帐户加密密钥。 然后，可以为帐户配置客户管理的密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8cf1f8ecb68e31f93c19d93d6ebc4f8ef37724e7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028449"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>创建支持表和队列的客户托管密钥的帐户

Azure 存储加密静态存储帐户中的所有数据。 默认情况下，队列存储和表存储使用的密钥的作用域为服务，由 Microsoft 管理。 你还可以选择使用客户管理的密钥来加密队列或表数据。 若要对队列和表使用客户托管的密钥，必须首先创建一个存储帐户，该帐户使用范围限定为帐户的加密密钥，而不是使用服务。 创建使用帐户加密密钥作为队列和表数据的帐户后，可以使用该存储帐户的 Azure Key Vault 来配置客户管理的密钥。

本文介绍如何创建一个存储帐户，该帐户依赖于该帐户的作用域。 首次创建帐户时，Microsoft 使用帐户密钥对帐户中的数据进行加密，而 Microsoft 管理密钥。 接下来，你可以为帐户配置客户管理的密钥，以利用这些权益，包括提供你自己的密钥、更新密钥版本、轮换密钥和吊销访问控制。

## <a name="about-the-feature"></a>关于功能

若要创建依赖于队列和表存储的帐户加密密钥的存储帐户，必须首先注册以便将此功能与 Azure 结合使用。 由于容量有限，请注意，批准请求之前可能需要几个月的时间。

你可以创建一个存储帐户，该帐户依赖于以下区域中队列和表存储的帐户加密密钥：

- 美国东部
- 美国中南部
- 美国西部 2  

### <a name="register-to-use-the-account-encryption-key"></a>注册以使用帐户加密密钥

若要注册 Azure CLI，请调用[az feature register](/cli/azure/feature#az-feature-register)命令。

注册以将帐户加密密钥用于队列存储：

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

注册以将帐户加密密钥用于表存储：

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>检查注册状态

若要检查队列存储注册的状态，请执行以下操作：

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

检查表存储的注册状态：

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>重新注册 Azure 存储资源提供程序

批准注册后，你必须重新注册 Azure 存储资源提供程序。 调用[az provider register](/cli/azure/provider#az-provider-register)命令：

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>创建使用帐户加密密钥的帐户

你必须配置新的存储帐户，以便在创建存储帐户时为队列和表使用帐户加密密钥。 创建帐户后，不能更改加密密钥的作用域。

存储帐户的类型必须是常规用途 v2。 可以通过使用 Azure CLI 或 Azure 资源管理器模板，来创建存储帐户并将其配置为依赖帐户加密密钥。

> [!NOTE]
> 只有在创建存储帐户时，才可以选择配置 "队列" 和 "表存储"，以通过帐户加密密钥来加密数据。 Blob 存储和 Azure 文件始终使用帐户加密密钥来加密数据。

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建依赖于帐户加密密钥的存储帐户，请确保已安装 Azure CLI 版本2.0.80 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

接下来，通过使用适当的参数调用[az storage account create](/cli/azure/storage/account#az-storage-account-create)命令创建常规用途 v2 存储帐户：

- 包括 `--encryption-key-type-for-queue` 选项，并将其值设置为 `Account`，以便使用帐户加密密钥加密队列存储中的数据。
- 包括 `--encryption-key-type-for-table` 选项，并将其值设置为 `Account`，以便使用帐户加密密钥加密表存储中的数据。

下面的示例演示如何创建一个常规用途 v2 存储帐户，该帐户配置为 LRS，并使用帐户加密密钥来加密队列和表存储的数据。 请记住，用自己的值替换括号中的占位符值：

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[模板](#tab/template)

下面的 JSON 示例创建一个常规用途 v2 存储帐户，该帐户配置为 LRS，并使用帐户加密密钥来加密队列和表存储的数据。 请记住，用自己的值替换尖括号中的占位符值：

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_LRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

创建了依赖于帐户加密密钥的帐户后，请参阅以下文章之一，使用 Azure Key Vault 配置客户托管的密钥：

- [使用 Azure 门户配置客户管理的密钥 Azure Key Vault](storage-encryption-keys-portal.md)
- [使用 PowerShell 配置使用 Azure Key Vault 的客户托管密钥](storage-encryption-keys-powershell.md)
- [使用 Azure CLI 配置客户管理的密钥 Azure Key Vault](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>验证帐户加密密钥

若要验证存储帐户中的服务是否正在使用帐户加密密钥，请调用 Azure CLI [az storage account](/cli/azure/storage/account#az-storage-account-show)命令。 此命令返回一组存储帐户属性及其值。 在 "加密" 属性中查找每个服务的 "`keyType`" 字段，并验证其是否设置为 "`Account`"。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md) 
- [什么是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
