---
title: 创建一个存储帐户，该帐户具有启用了基础结构加密的数据进行双重加密
titleSuffix: Azure Storage
description: 如果客户要求更高级别的保证数据的安全，则还可以在 Azure 存储基础结构级别启用256位 AES 加密。 启用基础结构加密后，存储帐户中的数据将使用两个不同的加密算法和两个不同的密钥进行两次加密。
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3164de9c3e44001d58d46eab9f823041b440960b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984178"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>创建一个存储帐户，该帐户具有启用了基础结构加密的数据进行双重加密

Azure 存储使用256位 AES 加密（可用的最强块密码之一），在服务级别自动加密存储帐户中的所有数据，并且符合 FIPS 140-2。 如果客户要求更高级别的保证数据的安全，则还可以在 Azure 存储基础结构级别启用256位 AES 加密。 启用基础结构加密后，存储帐户中的数据将在服务级别加密两次， &mdash; 一次 &mdash; 使用两种不同的加密算法和两个不同的密钥在基础结构级别进行加密。 对 Azure 存储数据进行双重加密可防止某个加密算法或密钥泄露的情况。 在这种情况下，额外的加密层将继续保护你的数据。

服务级别加密支持将 Microsoft 管理的密钥或客户托管的密钥与 Azure Key Vault 或 Key Vault 托管的硬件安全模型 (HSM)  (预览版) 一起使用。 基础结构级别的加密依赖于 Microsoft 托管的密钥并始终使用单独的密钥。 有关 Azure 存储加密的密钥管理的详细信息，请参阅 [关于加密密钥管理](storage-service-encryption.md#about-encryption-key-management)。

若要对数据进行双重加密，必须首先创建为基础结构加密配置的存储帐户。 本文介绍如何创建启用基础结构加密的存储帐户。

## <a name="register-to-use-infrastructure-encryption"></a>注册以使用基础结构加密

若要创建启用了基础结构加密的存储帐户，必须首先注册以便使用 PowerShell 或 Azure CLI 将此功能与 Azure 结合使用。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不适用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 注册，请调用 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

若要通过 PowerShell 检查注册状态，请调用 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

批准注册后，你必须重新注册 Azure 存储资源提供程序。 若要通过 PowerShell 重新注册资源提供程序，请调用 [AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 命令。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要注册 Azure CLI，请调用 [az feature register](/cli/azure/feature#az-feature-register) 命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

若要查看 Azure CLI 的注册状态，请调用 [az 功能](/cli/azure/feature#az-feature-show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

批准注册后，你必须重新注册 Azure 存储资源提供程序。 若要用 Azure CLI 重新注册资源提供程序，请调用 [az provider register](/cli/azure/provider#az-provider-register) 命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[模板](#tab/template)

不适用

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>创建启用了基础结构加密的帐户

你必须配置一个存储帐户，以便在创建帐户时使用基础结构加密。 存储帐户的类型必须是常规用途 v2。

创建帐户后，无法启用或禁用基础结构加密。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 PowerShell 创建启用了基础结构加密的存储帐户，请执行以下步骤：

1. 在 Azure 门户中，导航到 " **存储帐户** " 页。
1. 选择 " **添加** " 按钮，添加新的常规用途 v2 存储帐户。
1. 在 " **高级** " 选项卡上，找到 " **基础结构** 加密"，并选择 " **启用**"。
1. 选择 " **查看 + 创建** "，完成存储帐户的创建。

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="显示如何在创建帐户时启用基础结构加密的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建启用了基础结构加密的存储帐户，请确保已安装 [Az PowerShell 模块](https://www.powershellgallery.com/packages/Az.Storage)版本2.2.0 或更高版本。 有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

接下来，通过调用 [AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 命令创建常规用途 v2 存储帐户。 包括 `-RequireInfrastructureEncryption` 启用基础结构加密的选项。

下面的示例演示如何创建一个常规用途 v2 存储帐户，该帐户配置为读取访问地域冗余存储 (GRS) ，并为数据的双加密启用基础结构加密。 请记住，用自己的值替换括号中的占位符值：

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建启用了基础结构加密的存储帐户，请确保已安装 Azure CLI 版本2.8.0 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

接下来，通过调用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令创建常规用途 v2 存储帐户，并包括 `--require-infrastructure-encryption option` 来启用基础结构加密。

下面的示例演示如何创建一个常规用途 v2 存储帐户，该帐户配置为读取访问地域冗余存储 (GRS) ，并为数据的双加密启用基础结构加密。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[模板](#tab/template)

下面的 JSON 示例创建一个常规用途 v2 存储帐户，该帐户配置为读取访问地域冗余存储 (GRS) ，并为数据的双加密启用基础结构加密。 请记住，用自己的值替换括号中的占位符值：

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>验证是否已启用基础结构加密

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要验证是否为具有 Azure 门户的存储帐户启用了基础结构加密，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在 " **设置**" 下，选择 " **加密**"。

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="显示如何在创建帐户时启用基础结构加密的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 验证是否已为存储帐户启用基础结构加密，请调用 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 命令。 此命令返回一组存储帐户属性及其值。 检索 `RequireInfrastructureEncryption` 属性中的字段 `Encryption` ，并验证其是否设置为 `True` 。

下面的示例检索属性的值 `RequireInfrastructureEncryption` 。 请记住，用自己的值替换尖括号中的占位符值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要验证是否为具有 Azure CLI 的存储帐户启用基础结构加密，请调用 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 命令。 此命令返回一组存储帐户属性及其值。 查找 `requireInfrastructureEncryption` 属性中的字段 `encryption` ，并验证其是否设置为 `true` 。

下面的示例检索属性的值 `requireInfrastructureEncryption` 。 请记住，用自己的值替换尖括号中的占位符值：

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[模板](#tab/template)

不适用

---

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户托管密钥](customer-managed-keys-overview.md)