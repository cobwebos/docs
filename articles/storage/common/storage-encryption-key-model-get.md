---
title: 确定用于存储帐户的加密密钥模型
titleSuffix: Azure Storage
description: 使用 Azure 门户、PowerShell 或 Azure CLI 检查如何为存储帐户管理加密密钥。 密钥可能由 Microsoft 管理（默认设置），或由客户管理。 客户管理的密钥必须存储在 Azure Key Vault 中。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f59e4238c983cdb336500a68c52730ae5346b1c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612415"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>确定用于存储帐户的 Azure 存储加密密钥模型

存储帐户中的数据自动由 Azure 存储加密。 Azure 存储加密提供两个选项，用于在存储帐户级别管理加密密钥：

- **Microsoft 管理的密钥。** 默认情况下，Microsoft 管理用于加密存储帐户的密钥。
- **客户管理的密钥。** 可以选择管理存储帐户的加密密钥。 客户管理的密钥必须存储在 Azure Key Vault 中。

此外，还可以在单个请求级别为某些 Blob 存储操作提供加密密钥。 在请求上指定加密密钥时，该密钥会替代存储帐户上处于活动状态的加密密钥。 有关详细信息，请参阅[在对 Blob 存储的请求中指定客户提供的密钥](../blobs/storage-blob-customer-provided-key.md)。

有关加密密钥的详细信息，请参阅[静态数据的 Azure 存储加密](storage-service-encryption.md)。

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>检查存储帐户的加密密钥模型

若要确定存储帐户是使用 Microsoft 托管密钥还是客户托管密钥进行加密，请使用下列方法之一。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户检查存储帐户的加密模型，请执行以下步骤：

1. 在 Azure 门户中导航到存储帐户。
1. 选择“加密”  设置，并记下设置。

下图显示了使用 Microsoft 托管密钥加密的存储帐户：

![查看使用 Microsoft 托管密钥加密的帐户](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

下图显示了使用客户托管密钥加密的存储帐户：

![屏幕截图，显示 Azure 门户中的加密密钥设置](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 检查存储帐户的加密模型，请调用 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 命令，然后检查该帐户的 **KeySource** 属性。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

如果 **KeySource** 属性的值为 `Microsoft.Storage`，则该帐户将通过 Microsoft 托管密钥进行加密。 如果 **KeySource** 属性的值为 `Microsoft.Keyvault`，则该帐户将通过客户托管密钥进行加密。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 检查存储帐户的加密模型，请调用 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 命令，然后检查该帐户的 **keySource** 属性。

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

如果 **keySource** 属性的值为 `Microsoft.Storage`，则该帐户将通过 Microsoft 托管密钥进行加密。 如果 **keySource** 属性的值为 `Microsoft.Keyvault`，则该帐户将通过客户托管密钥进行加密。

---

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户托管密钥](customer-managed-keys-overview.md)
