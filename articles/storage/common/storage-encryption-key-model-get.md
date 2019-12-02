---
title: 确定用于存储帐户的加密密钥模型-Azure 存储
description: 使用 Azure 门户、PowerShell 或 Azure CLI 检查如何为存储帐户管理加密密钥。 密钥可能由 Microsoft 管理（默认设置），或由客户管理。 客户管理的密钥必须存储在 Azure Key Vault 中。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 967e6f278008a59721d8d0c74e34c0252eeb1138
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666593"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>确定存储帐户使用的 Azure 存储加密密钥模型

存储帐户中的数据自动由 Azure 存储加密。 Azure 存储加密提供两个选项用于在存储帐户级别管理加密密钥：

- **Microsoft 管理的密钥。** 默认情况下，Microsoft 管理用于加密存储帐户的密钥。
- **客户管理的密钥。** 可以选择管理存储帐户的加密密钥。 客户管理的密钥必须存储在 Azure Key Vault 中。

此外，还可以在单个请求级别为某些 Blob 存储操作提供加密密钥。 当在请求上指定加密密钥时，该密钥会替代存储帐户上处于活动状态的加密密钥。 有关详细信息，请参阅为[Blob 存储的请求指定客户提供的密钥](../blobs/storage-blob-customer-provided-key.md)。

有关加密密钥的详细信息，请参阅[静态数据的 Azure 存储加密](storage-service-encryption.md)。

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>检查存储帐户的加密密钥模型

若要确定存储帐户使用的是 Microsoft 托管密钥还是使用客户托管密钥进行加密，请使用下列方法之一。

# <a name="azure-portaltabportal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户检查存储帐户的加密模型，请执行以下步骤：

1. 在 Azure 门户中，导航到存储帐户。
1. 选择**加密**设置，并记下设置。

下图显示了用于加密的客户托管密钥的存储帐户：

![显示 Azure 门户中的加密密钥设置的屏幕截图](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 检查存储帐户的加密模型，请调用[AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount)命令，然后检查该帐户的**KeySource**属性。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

如果**KeySource**属性的值 `Microsoft.Storage`，则该帐户将通过 Microsoft 托管密钥进行加密。 如果**KeySource**属性的值为 `Microsoft.Keyvault`，则帐户将通过客户托管的密钥进行加密。

# <a name="azure-clitabcli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 检查存储帐户的加密模型，请调用[az storage account show](/cli/azure/storage/account#az-storage-account-show)命令，然后检查该帐户的**keySource**属性。

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

如果**keySource**属性的值 `Microsoft.Storage`，则该帐户将通过 Microsoft 托管密钥进行加密。 如果**keySource**属性的值为 `Microsoft.Keyvault`，则帐户将通过客户托管的密钥进行加密。

---

## <a name="next-steps"></a>后续步骤

[静态数据的 Azure 存储加密](storage-service-encryption.md)
