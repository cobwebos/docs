---
title: 用 Azure Key Vault 中存储的客户托管密钥配置加密
titleSuffix: Azure Storage
description: 了解如何使用 Azure 门户、PowerShell 或 Azure CLI 配置存储在 Azure Key Vault 中的客户托管密钥的 Azure 存储加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0991992a6138d263dfb4d200c9555a8d53366d70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994137"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>用 Azure Key Vault 中存储的客户托管密钥配置加密

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。 为了进一步控制加密密钥，你可以管理自己的密钥。 客户托管的密钥必须存储在 Azure Key Vault 或 Key Vault 托管硬件安全模型 (HSM)  (预览版) 中。

本文介绍如何使用 Azure 门户、PowerShell 或 Azure CLI 为存储在密钥保管库中的客户托管密钥配置加密。 若要了解如何使用托管 HSM 中存储的客户托管密钥配置加密，请参阅 [使用 Azure Key Vault 托管 hsm (preview) 中存储的客户托管密钥配置加密 ](customer-managed-keys-configure-key-vault-hsm.md)。

> [!NOTE]
> Azure Key Vault 和 Azure Key Vault 托管 HSM 支持相同的 Api 和管理接口进行配置。

## <a name="configure-a-key-vault"></a>配置密钥保管库

你可以使用新的或现有的密钥保管库来存储客户管理的密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。

将客户托管的密钥用于 Azure 存储加密需要为密钥保管库启用软删除和清除保护。 默认情况下，当你创建新的密钥保管库时，会启用软删除，并且无法禁用。 你可以在创建密钥保管库时或创建密钥保管库后启用清除保护。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要了解如何使用 Azure 门户创建密钥保管库，请参阅 [快速入门：使用 Azure 门户创建密钥保管库](../../key-vault/general/quick-create-portal.md)。 创建密钥保管库时，请选择 " **启用清除保护**"，如下图所示。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="显示如何在创建密钥保管库时启用清除保护的屏幕截图":::

若要对现有密钥保管库启用清除保护，请执行以下步骤：

1. 在 Azure 门户中导航到密钥保管库。
1. 在“设置”下面，选择“属性”。********
1. 在 " **清除保护** " 部分中，选择 " **启用清除保护**"。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建新的密钥保管库，请安装2.0.0 或更高版本的 [KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell 模块。 然后调用 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) 来创建新密钥保管库。 使用2.0.0 和 KeyVault 模块的版本，在创建新的密钥保管库时，会默认启用软删除。

以下示例创建一个启用了软删除和清除保护的新密钥保管库。 请记得将括号中的占位符值替换为你自己的值。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

若要了解如何使用 PowerShell 对现有密钥保管库启用清除保护，请参阅 [如何将软删除与 powershell 配合使用](../../key-vault/general/soft-delete-powershell.md)。

接下来，向存储帐户分配系统分配的托管标识。 将使用此托管标识授予存储帐户访问 Key Vault 的权限。 有关系统分配的托管标识的详细信息，请参阅 [Azure 资源的托管标识是什么？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 PowerShell 分配托管标识，请调用 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)：

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

最后，为密钥保管库配置访问策略，以便存储帐户有权访问它。 此步骤使用前面分配给存储帐户的托管标识。

若要设置密钥保管库的访问策略，请调用 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)：

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建新的 Key Vault，请调用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

若要了解如何在 Azure CLI 的现有密钥保管库上启用清除保护，请参阅 [如何将软删除与 CLI 配合使用](../../key-vault/general/soft-delete-cli.md)。

接下来，向存储帐户分配系统分配的托管标识。 将使用此托管标识授予存储帐户访问 Key Vault 的权限。 有关系统分配的托管标识的详细信息，请参阅 [Azure 资源的托管标识是什么？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 Azure CLI 分配托管标识，请调用 [az storage account update](/cli/azure/storage/account#az-storage-account-update)：

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

最后，为密钥保管库配置访问策略，以便存储帐户有权访问它。 此步骤使用前面分配给存储帐户的托管标识。

若要设置密钥保管库的访问策略，请调用 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)：

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>添加密钥

接下来，在密钥保管库中添加一个密钥。

Azure 存储加密支持2048、3072和4096大小的 RSA 和 RSA-HSM 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的“Key Vault 密钥”。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要了解如何使用 Azure 门户添加密钥，请参阅 [快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索密钥](../../key-vault/keys/quick-create-portal.md)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 添加密钥，请调用 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 添加密钥，请调用 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)。 请记得将括号中的占位符值替换为你自己的值。

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>配置使用客户管理的密钥进行加密

接下来，将 Azure 存储帐户配置为使用客户管理的密钥和 Azure Key Vault，并指定与存储帐户关联的密钥。

使用客户管理的密钥配置加密时，只要关联的密钥保管库中有可用的新版本，就可以选择自动更新用于 Azure 存储加密的密钥版本。 也可以显式指定在手动更新密钥版本之前要用于加密的密钥版本。

> [!NOTE]
> 若要轮换密钥，请在 Azure Key Vault 中创建密钥的新版本。 在 Azure Key Vault 中，Azure 存储不会处理密钥的旋转，因此你将需要手动轮换密钥，或创建一个函数以便按计划进行旋转。

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>配置加密以自动更新密钥版本

Azure 存储可以自动更新客户托管的密钥，该密钥用于加密以使用最新密钥版本。 当客户管理的密钥在 Azure Key Vault 中旋转时，Azure 存储将自动开始使用最新版本的密钥进行加密。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中配置客户管理的密钥版本的密钥版本的自动更新，请执行以下步骤：

1. 导航到存储帐户。
1. 在存储帐户的“设置”边栏选项卡上，单击“加密”。 选择“客户托管密钥”选项，如下图所示。

    ![显示加密选项的门户屏幕截图](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. 选择“从 Key Vault 中选择”选项。
1. 选择“选择密钥保管库和密钥”。
1. 选择包含要使用的密钥的密钥保管库。
1. 从密钥保管库中选择密钥。

   ![屏幕截图，显示如何选择密钥保管库和密钥](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. 保存所做更改。

指定密钥后，Azure 门户指示已启用密钥版本的自动更新，并显示当前用于加密的密钥版本。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="显示启用密钥版本自动更新的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要配置客户管理的密钥，并在 PowerShell 中自动更新密钥版本，请安装 [Az. Storage](https://www.powershellgallery.com/packages/Az.Storage) module 版本2.0.0 或更高版本。

若要自动更新客户托管密钥的密钥版本，请在使用存储帐户的客户托管密钥配置加密时省略密钥版本。 调用 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 以更新存储帐户的加密设置（如以下示例所示），并包含 -KeyvaultEncryption 选项，以便为存储帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要配置客户管理的密钥并通过 Azure CLI 自动更新密钥版本，请安装 [Azure CLI 版本 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要自动更新客户托管密钥的密钥版本，请在使用存储帐户的客户托管密钥配置加密时省略密钥版本。 请调用 [az storage account update](/cli/azure/storage/account#az-storage-account-update)，以便更新存储帐户的加密设置，如以下示例所示。 包括 `--encryption-key-source` 参数并将其设置为 `Microsoft.Keyvault` 即可为帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为你自己的值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>配置加密以手动更新密钥版本

如果希望手动更新密钥版本，请在使用客户管理的密钥配置加密时显式指定版本。 在这种情况下，在密钥保管库中创建新版本时，Azure 存储不会自动更新密钥版本。若要使用新的密钥版本，必须手动更新用于 Azure 存储加密的版本。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要配置客户管理的密钥并在 Azure 门户中手动更新密钥版本，请指定密钥 URI，包括版本。 若要将某个密钥指定为 URI，请执行下列步骤：

1. 若要在 Azure 门户中查找密钥 URI，请导航到 Key Vault，然后选择“密钥”设置。 选择所需的密钥，然后单击该密钥以查看其版本。 选择一个密钥版本，查看该版本的设置。
1. 复制“密钥标识符”字段的值（提供 URI）。

    ![显示 Key Vault 密钥 URI 的屏幕截图](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. 在存储帐户的“加密密钥”设置中，选择“输入密钥 URI”选项。 
1. 将复制的 URI 粘贴到“密钥 URI”字段中。 从 URI 中省略密钥版本以启用密钥版本的自动更新。

   ![显示如何输入密钥 URI 的屏幕截图](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. 指定包含密钥保管库的订阅。
1. 保存所做更改。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过手动更新密钥版本来配置客户管理的密钥，请在配置存储帐户的加密时显式提供密钥版本。 调用 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 以更新存储帐户的加密设置（如以下示例所示），并包含 -KeyvaultEncryption 选项，以便为存储帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

手动更新密钥版本时，需要将存储帐户的加密设置更新为使用新版本。 首先调用 [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 以获取最新密钥版本。 然后调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 来更新存储帐户的加密设置，以使用该密钥的新版本，如前面示例所示。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过手动更新密钥版本来配置客户管理的密钥，请在配置存储帐户的加密时显式提供密钥版本。 请调用 [az storage account update](/cli/azure/storage/account#az-storage-account-update)，以便更新存储帐户的加密设置，如以下示例所示。 包括 `--encryption-key-source` 参数并将其设置为 `Microsoft.Keyvault` 即可为帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为你自己的值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

手动更新密钥版本时，需要将存储帐户的加密设置更新为使用新版本。 首先，通过调用 [az keyvault show](/cli/azure/keyvault#az-keyvault-show) 查询 Key Vault URI，并通过调用 [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) 查询密钥版本。 然后调用 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 来更新存储帐户的加密设置，以使用新的密钥版本，如上一示例所示。

---

## <a name="change-the-key"></a>更改密钥

你可以随时更改用于 Azure 存储加密的密钥。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要更改 Azure 门户的密钥，请执行以下步骤：

1. 导航到你的存储帐户，并显示“加密”设置。
1. 选择密钥保管库并选择一个新密钥。
1. 保存所做更改。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要更改带有 PowerShell 的密钥，请调用 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) ，如 [通过客户托管的密钥配置加密](#configure-encryption-with-customer-managed-keys) 和提供新的密钥名称和版本中所示。 如果新密钥在其他密钥保管库中，则还必须更新密钥保管库 URI。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要更改 Azure CLI 的密钥，请调用 [az 存储帐户更新](/cli/azure/storage/account#az-storage-account-update) ，如用 [客户管理的密钥配置加密](#configure-encryption-with-customer-managed-keys) 和提供新的密钥名称和版本中所示。 如果新密钥在其他密钥保管库中，则还必须更新密钥保管库 URI。

---

## <a name="revoke-customer-managed-keys"></a>撤销客户托管密钥

撤消客户管理的密钥会删除存储帐户和密钥保管库之间的关联。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要撤销 Azure 门户的客户托管密钥，请按照 [禁用客户管理的密钥](#disable-customer-managed-keys)中所述禁用密钥。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

通过删除密钥保管库访问策略可以撤消客户管理的密钥。 若要使用 PowerShell 吊销客户托管的密钥，请调用 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) 命令，如以下示例中所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

通过删除密钥保管库访问策略可以撤消客户管理的密钥。 若要撤消 Azure CLI 的客户托管密钥，请调用 [az keyvault delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) 命令，如以下示例中所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>禁用客户托管密钥

禁用客户托管密钥时，将再次使用 Microsoft 托管密钥对存储帐户进行加密。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中禁用客户管理的密钥，请执行以下步骤：

1. 导航到你的存储帐户，并显示“加密”设置。
1. 取消选中“使用自己的密钥”设置旁边的复选框。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 禁用客户管理的密钥， [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)请通过选项调用 AzStorageAccount `-StorageEncryption` ，如下面的示例中所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过 Azure CLI 禁用客户管理的密钥，请调用 [az 存储帐户更新](/cli/azure/storage/account#az-storage-account-update) ，并将设置 `--encryption-key-source parameter` 为 `Microsoft.Storage` ，如下面的示例中所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户托管密钥](customer-managed-keys-overview.md)
- [用 Azure Key Vault 托管 HSM (预览版中存储的客户托管密钥配置加密) ](customer-managed-keys-configure-key-vault-hsm.md)
