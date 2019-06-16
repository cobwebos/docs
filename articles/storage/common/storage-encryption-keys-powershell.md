---
title: 通过 PowerShell 配置客户管理的密钥用于 Azure 存储加密
description: 了解如何使用 PowerShell 来配置客户管理的密钥用于 Azure 存储加密。 使用客户管理的密钥可以创建、轮换、禁用和撤销访问控制。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: be876b370cd476bee2af7d90a9f0433fd80de3b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233677"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>通过 PowerShell 配置客户管理的密钥用于 Azure 存储加密

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文介绍如何使用 PowerShell 配置包含客户管理的密钥的 Key Vault。

## <a name="assign-an-identity-to-the-storage-account"></a>将标识分配到存储帐户

若要为存储帐户启用客户管理的密钥，请先将一个系统分配的托管标识分配到该存储帐户。 将使用此托管标识授予存储帐户访问 Key Vault 的权限。

若要使用 PowerShell 分配托管标识，请调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)。 请记得将括号中的占位符值替换为你自己的值。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

有关使用 PowerShell 配置系统分配的托管的标识的详细信息，请参阅[配置托管在使用 PowerShell 为 Azure VM 上的 Azure 资源的标识](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>创建新的 Key Vault

若要使用 PowerShell 创建新的 Key Vault，请调用 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 必须为用来存储客户管理的密钥（用于 Azure 存储加密）的 Key Vault 启用两项密钥保护设置：“软删除”和“不要清除”。   

请记得将括号中的占位符值替换为你自己的值。 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>配置 Key Vault 访问策略

接下来，配置 Key Vault 的访问策略，使存储帐户有权访问 Key Vault。 此步骤使用前面分配给存储帐户的托管标识。

若要设置 Key Vault 的访问策略，请调用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>新建密钥

接下来，在 Key Vault 中创建新密钥。 若要创建新密钥，请调用 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>配置使用客户管理的密钥进行加密

Azure 存储加密默认使用 Microsoft 托管的密钥。 此步骤将 Azure 存储帐户配置为使用客户管理的密钥，并指定要与存储帐户关联的密钥。

调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 更新存储帐户的加密设置。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>更新密钥版本

创建密钥的新版本时，需将存储帐户更新为使用新版本。 首先调用 [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 以获取最新密钥版本。 然后调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 更新存储帐户的加密设置，以使用新的密钥版本，如上一部分中所示。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md) 
- [什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
