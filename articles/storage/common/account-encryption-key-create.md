---
title: 创建支持表和队列的客户管理密钥的帐户
titleSuffix: Azure Storage
description: 了解如何创建支持为表和队列配置客户管理的密钥的存储帐户。 使用 Azure CLI 或 Azure 资源管理器模板创建依赖于 Azure 存储加密的帐户加密密钥的存储帐户。 然后，您可以为帐户配置客户管理的密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083569"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>创建支持表和队列的客户管理密钥的帐户

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，队列存储和表存储使用限定为服务并由 Microsoft 管理的密钥。 您还可以选择使用客户管理的密钥来加密队列或表数据。 要将客户管理的密钥用于队列和表，必须首先创建一个存储帐户，该存储帐户使用限定到帐户的加密密钥，而不是服务。 创建对队列和表数据使用帐户加密密钥的帐户后，可以使用该存储帐户的 Azure 密钥保管库配置客户管理的密钥。

本文介绍如何创建依赖于限定到该帐户的密钥的存储帐户。 首次创建帐户时，Microsoft 使用帐户密钥对帐户中的数据进行加密，Microsoft 管理该密钥。 随后，您可以为帐户配置客户管理的密钥，以利用这些优势，包括提供您自己的密钥、更新密钥版本、旋转密钥和撤消访问控制的能力。

## <a name="about-the-feature"></a>关于功能

要创建依赖于队列和表存储的帐户加密密钥的存储帐户，必须首先注册以将此功能与 Azure 一起使用。 由于容量有限，请注意，可能需要几个月才能批准访问请求。

您可以创建一个存储帐户，该帐户依赖于以下区域中的队列和表存储的帐户加密密钥：

- 美国东部
- 美国中南部
- 美国西部 2  

### <a name="register-to-use-the-account-encryption-key"></a>注册以使用帐户加密密钥

要注册以将帐户加密密钥与队列或表存储一起使用，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[电源外壳](#tab/powershell)

要向 PowerShell 注册，请调用[获取-AzProviderFeature 命令](/powershell/module/az.resources/get-azproviderfeature)。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要向 Azure CLI 注册，请调用[az 要素寄存器](/cli/azure/feature#az-feature-register)命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[模板](#tab/template)

空值

---

### <a name="check-the-status-of-your-registration"></a>检查注册状态

要检查队列或表存储的注册状态，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[电源外壳](#tab/powershell)

要检查您在 PowerShell 的注册状态，请致电[获取-AzProvider功能](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要检查 Azure CLI 注册的状态，请调用[az 要素](/cli/azure/feature#az-feature-show)命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[模板](#tab/template)

空值

---

### <a name="re-register-the-azure-storage-resource-provider"></a>重新注册 Azure 存储资源提供程序

注册获得批准后，必须重新注册 Azure 存储资源提供程序。 使用 PowerShell 或 Azure CLI 重新注册资源提供程序。

# <a name="powershell"></a>[电源外壳](#tab/powershell)

要使用 PowerShell 重新注册资源提供程序，请调用[注册-AzResourceProvider 命令](/powershell/module/az.resources/register-azresourceprovider)。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 重新注册资源提供程序，请调用[az 提供程序寄存器](/cli/azure/provider#az-provider-register)命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[模板](#tab/template)

空值

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>创建使用帐户加密密钥的帐户

您必须配置新的存储帐户，以便在创建存储帐户时对队列和表使用帐户加密密钥。 创建帐户后，无法更改加密密钥的范围。

存储帐户的类型必须为通用 v2。 可以使用 Azure CLI 或 Azure 资源管理器模板创建存储帐户并将其配置为依赖帐户加密密钥。

> [!NOTE]
> 只能选择队列和表存储来配置，以在创建存储帐户时使用帐户加密密钥加密数据。 Blob 存储和 Azure 文件始终使用帐户加密密钥来加密数据。

# <a name="powershell"></a>[电源外壳](#tab/powershell)

要使用 PowerShell 创建依赖于帐户加密密钥的存储帐户，请确保已安装 Azure PowerShell 模块（版本 3.4.0 或更高版本）。 有关详细信息，请参阅安装[Azure PowerShell 模块](/powershell/azure/install-az-ps)。

接下来，通过调用[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)命令创建通用 v2 存储帐户，并具有相应的参数：

- 包括`-EncryptionKeyTypeForQueue`该选项并将其值`Account`设置为使用帐户加密密钥加密队列存储中的数据。
- 包括`-EncryptionKeyTypeForTable`该选项并将其值`Account`设置为使用帐户加密密钥加密表存储中的数据。

下面的示例演示如何创建为读取访问异地冗余存储 （RA-GRS） 配置的通用 v2 存储帐户，并使用帐户加密密钥加密队列和表存储的数据。 请记住将括号中的占位符值替换为您自己的值：

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 创建依赖于帐户加密密钥的存储帐户，请确保已安装 Azure CLI 版本 2.0.80 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

接下来，通过调用[az 存储帐户创建](/cli/azure/storage/account#az-storage-account-create)命令创建具有相应参数，创建通用 v2 存储帐户：

- 包括`--encryption-key-type-for-queue`该选项并将其值`Account`设置为使用帐户加密密钥加密队列存储中的数据。
- 包括`--encryption-key-type-for-table`该选项并将其值`Account`设置为使用帐户加密密钥加密表存储中的数据。

下面的示例演示如何创建为读取访问异地冗余存储 （RA-GRS） 配置的通用 v2 存储帐户，并使用帐户加密密钥加密队列和表存储的数据。 请记住将括号中的占位符值替换为您自己的值：

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[模板](#tab/template)

下面的 JSON 示例创建一个通用 v2 存储帐户，该帐户配置为读取访问异地冗余存储 （RA-GRS），并使用帐户加密密钥加密队列和表存储的数据。 请记住将角度括号中的占位符值替换为您自己的值：

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
            "name": "[parameters('Standard_RAGRS')]"
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

创建依赖于帐户加密密钥的帐户后，请参阅以下一些文章，以便使用 Azure 密钥保管库配置客户管理的密钥：

- [通过 Azure 门户使用 Azure Key Vault 配置客户管理的密钥](storage-encryption-keys-portal.md)
- [通过 PowerShell 使用 Azure Key Vault 配置客户管理的密钥](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 使用 Azure Key Vault 配置客户管理的密钥](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>验证帐户加密密钥

要验证存储帐户中的服务是否使用帐户加密密钥，请调用 Azure CLI [az 存储帐户命令](/cli/azure/storage/account#az-storage-account-show)。 此命令返回一组存储帐户属性及其值。 查找加密属性`keyType`中每个服务的字段，并验证其设置为`Account`。

# <a name="powershell"></a>[电源外壳](#tab/powershell)

要验证存储帐户中的服务是否使用帐户加密密钥，请调用[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount)命令。 此命令返回一组存储帐户属性及其值。 查找属性中`KeyType`每个服务的`Encryption`字段，并验证其设置为`Account`。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要验证存储帐户中的服务是否使用帐户加密密钥，请调用[az 存储帐户](/cli/azure/storage/account#az-storage-account-show)命令。 此命令返回一组存储帐户属性及其值。 查找加密属性`keyType`中每个服务的字段，并验证其设置为`Account`。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[模板](#tab/template)

空值

---

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md) 
- [什么是 Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
