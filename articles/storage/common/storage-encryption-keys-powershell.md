---
title: 使用 PowerShell 将客户托管的密钥配置为 Azure Key Vault-Azure 存储
description: 了解如何使用 PowerShell 为 Azure 存储加密配置客户管理的密钥。 使用客户管理的密钥可以创建、轮换、禁用和撤消访问控制。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bd723787d9cea2d3b9d81ae9db63c70a21190854
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666216"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-powershell"></a>使用 PowerShell 配置 Azure 存储的客户托管密钥

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文介绍如何使用 PowerShell 配置使用客户管理的密钥的 Azure Key Vault。 若要了解如何使用 Azure CLI 创建密钥保管库，请参阅[快速入门：使用 PowerShell 从 Azure Key Vault 设置和检索机密](../../key-vault/quick-create-powershell.md)。

> [!IMPORTANT]
> 将客户托管的密钥用于 Azure 存储加密要求在 key vault 上设置两个属性，**软删除**并不**清除**。 默认情况下不启用这些属性。 若要启用这些属性，请使用 PowerShell 或 Azure CLI。
> 仅支持 RSA 密钥和密钥大小2048。

## <a name="assign-an-identity-to-the-storage-account"></a>为存储帐户分配标识

若要为你的存储帐户启用客户管理的密钥，请首先将系统分配的托管标识分配给存储帐户。 你将使用此托管标识向存储帐户授予访问密钥保管库的权限。

若要使用 PowerShell 分配托管标识，请调用[AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)。 请记得将占位符值替换为您自己的值。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

有关使用 PowerShell 配置系统分配的托管标识的详细信息，请参阅[使用 powershell 在 AZURE VM 上配置 azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>创建新的密钥保管库

若要使用 PowerShell 创建新的密钥保管库，请调用[AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 用于存储 Azure 存储加密的客户托管密钥的密钥保管库必须启用两个密钥保护设置，**软删除**并不**清除**。 

请记得将占位符值替换为您自己的值。 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>配置密钥保管库访问策略

接下来，为密钥保管库配置访问策略，以便存储帐户有权访问它。 在此步骤中，你将使用之前分配给存储帐户的托管标识。

若要设置密钥保管库的访问策略，请调用[AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 请记住，将占位符中的占位符值替换为自己的值，并使用在前面的示例中定义的变量。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>新建密钥

接下来，在密钥保管库中创建新密钥。 若要创建新密钥，请调用[AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请记住，将占位符中的占位符值替换为自己的值，并使用在前面的示例中定义的变量。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>通过客户托管的密钥配置加密

默认情况下，Azure 存储加密使用 Microsoft 托管密钥。 在此步骤中，请将 Azure 存储帐户配置为使用客户管理的密钥，并指定与存储帐户关联的密钥。

调用[AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)来更新存储帐户的加密设置。 请记住，将占位符中的占位符值替换为自己的值，并使用在前面的示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>更新密钥版本

当你创建新的密钥版本时，你将需要更新存储帐户以使用新版本。 首先，调用[AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)获取密钥的最新版本。 然后调用[AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) ，将存储帐户的加密设置更新为使用新的密钥版本，如前一部分中所示。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [什么是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
