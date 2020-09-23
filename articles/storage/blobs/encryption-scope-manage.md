---
title: 创建和管理加密范围（预览）
description: 了解如何创建加密范围以在容器或 blob 级别隔离 blob 数据。
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9210c54305427c82d5666d68573fd3af41e8cef7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972185"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>创建和管理加密范围（预览）

通过加密范围（预览），可在单个 blob 或容器级别管理加密。 加密范围将 blob 数据隔离在存储帐户内的安全 enclave 中。 可以使用加密范围在驻留在同一存储帐户中但属于不同客户的数据之间创建安全边界。 有关加密范围的详细信息，请参阅 [Blob 存储（预览）的加密范围](encryption-scope-overview.md)。

本文介绍如何创建加密范围。 本文还介绍如何在创建 blob 或容器时指定加密范围。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>创建加密范围

你可以使用 Microsoft 管理的密钥或使用客户管理的密钥（存储在 Azure Key Vault 或 Azure Key Vault 托管硬件安全模型 (HSM)  (preview) ）来创建加密作用域。 若要使用客户托管的密钥创建加密作用域，必须先创建密钥保管库或托管 HSM，并添加要用于作用域的密钥。 Key vault 或托管 HSM 必须启用清除保护，并且必须与存储帐户位于同一区域。

创建加密范围时会自动启用它。 创建加密范围后，可以在创建 blob 时指定它。 还可以在创建容器时指定默认的加密范围，它将自动应用于容器中的所有 blob。

# <a name="portal"></a>[Portal](#tab/portal)

若要在 Azure 门户中创建加密范围，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 选择“加密”设置。
1. 选择“加密范围”选项卡。
1. 单击“添加”按钮以添加新加密范围。
1. 在“创建加密范围”窗格输入新范围的名称。
1. 选择加密的类型，Microsoft 托管密钥或客户管理的密钥 。
    - 如果选择了“Microsoft 管理的密钥”，请单击“创建”以创建加密范围 。
    - 如果已选择 " **客户管理的密钥**"，请指定要用于此加密作用域的密钥保管库或托管 HSM、密钥和密钥版本，如下图所示。

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="显示如何在 Azure 门户中创建加密范围的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要使用 PowerShell 创建加密范围，请首先安装 Az.Storage 预览模块版本。 建议使用最新的预览版本，但 1.13.4 预览版及更高版本中也支持加密范围。 删除其他版本的 Az.Storage 模块。

以下命令安装 Az.Storage [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) 模块：

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>创建受 Microsoft 托管密钥保护的加密范围

若要创建受 Microsoft 托管密钥保护的新加密范围，请使用 `-StorageEncryption` 参数调用 New-AzStorageEncryptionScope 命令。

请务必将示例中的占位符值替换为你自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>创建由客户管理的密钥保护的加密范围

若要创建由存储在密钥保管库或托管 HSM 中的客户托管密钥保护的新加密范围，请首先为存储帐户配置客户管理的密钥。 必须将托管标识分配给存储帐户，然后使用托管标识为密钥保管库或托管 HSM 配置访问策略，以便存储帐户有权访问它。

若要配置客户管理的密钥以便与加密作用域一起使用，必须在密钥保管库或托管 HSM 上启用清除保护。 密钥保管库或托管 HSM 必须位于存储帐户所在的同一区域。

请务必将示例中的占位符值替换为你自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

接下来，使用 `-KeyvaultEncryption` 参数调用 New-AzStorageEncryptionScope 命令，并指定密钥 URI。 请确保在密钥 URI 上包括密钥版本。 请务必将示例中的占位符值替换为你自己的值：

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 创建加密范围，请先安装 Azure CLI 2.4.0 或更高版本。

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>创建受 Microsoft 托管密钥保护的加密范围

若要创建受 Microsoft 托管密钥保护的新加密范围，请调用 [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) 命令，并将 `--key-source` 参数指定为 `Microsoft.Storage`。 请务必将占位符值替换为你自己的值：

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>创建由客户管理的密钥保护的加密范围

若要创建受 Microsoft 托管密钥保护的新加密范围，请调用 [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) 命令，并将 `--key-source` 参数指定为 `Microsoft.Storage`。 请务必将占位符值替换为你自己的值：

若要在密钥保管库或托管 HSM 中创建受客户管理的密钥保护的新加密范围，请首先为存储帐户配置客户管理的密钥。 必须为存储帐户分配一个托管标识，然后使用该托管标识配置密钥保管库的访问策略，使存储帐户有权访问该密钥保管库。 有关详细信息，请参阅 [Azure 存储加密的客户托管密钥](../common/customer-managed-keys-overview.md)。

若要配置客户管理的密钥以便与加密作用域一起使用，必须在密钥保管库或托管 HSM 上启用清除保护。 密钥保管库或托管 HSM 必须位于存储帐户所在的同一区域。

请务必将示例中的占位符值替换为你自己的值：

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

接下来，使用 `--key-uri` 参数调用 az storage account encryption-scope create 命令，并指定密钥 URI。 请确保在密钥 URI 上包括密钥版本。 请务必将示例中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

若要了解如何使用密钥保管库中客户托管的密钥配置 Azure 存储加密，请参阅 [使用 Azure Key Vault 中存储的客户托管密钥配置加密](../common/customer-managed-keys-configure-key-vault.md)。 若要在托管 HSM 中配置客户管理的密钥，请参阅 [使用 Azure Key Vault 托管 hsm (预览版) 中存储的客户托管密钥配置加密 ](../common/customer-managed-keys-configure-key-vault-hsm.md)。

## <a name="list-encryption-scopes-for-storage-account"></a>列出存储帐户的加密范围

# <a name="portal"></a>[Portal](#tab/portal)

若要在 Azure 门户中查看存储帐户的加密范围，请导航到该存储帐户的“加密范围”设置。 在此窗格中，你可以启用或禁用加密范围，或者更改加密范围的密钥。

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="显示 Azure 门户中的加密范围列表的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要列出适用于 PowerShell 的存储帐户的加密范围，请调用 **AzStorageEncryptionScope** 命令。 请务必将示例中的占位符值替换为你自己的值：

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

若要按存储帐户列出资源组中的所有加密范围，请使用管道语法：

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要列出可用于 Azure CLI 的存储帐户的加密范围，请调用 [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) 命令。 请务必将示例中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>创建具有默认加密范围的容器

创建容器时，可以指定默认的加密作用域。 默认情况下，该容器中的 blob 将使用该作用域。

除非容器配置为要求所有 blob 使用其默认范围，否则可以使用其自己的加密范围创建单个 blob。

# <a name="portal"></a>[Portal](#tab/portal)

若要在 Azure 门户中创建具有默认加密范围的容器，请先创建加密范围，如[创建加密范围](#create-an-encryption-scope)中所述。 接下来，请按照以下步骤创建容器：

1. 导航到存储帐户中的容器列表，然后选择“添加”按钮创建一个新容器。
1. 在“新容器”窗格中展开“高级”设置 。
1. 在“加密范围”下拉列表中，为容器选择默认的加密范围。
1. 若要要求容器中的所有 blob 使用默认加密范围，请选中“对容器中的所有 blob 使用此加密范围”的复选框。 如果选中此复选框，则容器中的单个 blob 无法重写默认的加密范围。

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="显示具有默认加密范围的容器的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要使用 PowerShell 创建具有默认加密范围的容器，请调用 [New-AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) 命令，并指定 `-DefaultEncryptionScope` 参数的范围。 New-AzRmStorageContainer 命令使用 Azure 存储资源提供程序创建一个容器，使用该容器可配置加密范围和其他资源管理操作。

若要强制容器中的所有 blob 使用容器的默认范围，请将 `-PreventEncryptionScopeOverride` 参数设置为 `true`。

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 创建具有默认加密范围的容器，请调用 [az storage container create](/cli/azure/storage/container#az-storage-container-create) 命令，并指定 `--default-encryption-scope` 参数的范围。 若要强制容器中的所有 blob 使用容器的默认范围，请将 `--prevent-encryption-scope-override` 参数设置为 `true`。

下面的示例使用 Azure AD 帐户授权操作创建容器。 还可以使用帐户访问密钥。 有关详细信息，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](../common/authorize-data-operations-cli.md)。

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

如果客户端在将 blob 上传到具有默认加密范围的容器时尝试指定范围，并且容器配置为阻止 blob 重写默认范围，则操作将失败，并显示一条消息，指示容器加密策略禁止该请求。

## <a name="upload-a-blob-with-an-encryption-scope"></a>上传具有加密范围的 blob

上传 blob 时，可以指定该 blob 的加密范围，或者使用容器的默认加密范围（如果已指定）。

# <a name="portal"></a>[Portal](#tab/portal)

若要上传具有 Azure 门户中指定的加密范围的 blob，请首先按照[创建加密范围](#create-an-encryption-scope)中所述创建加密范围。 接下来，请按照以下步骤创建 blob：

1. 导航到要将 blob 上传到的容器。
1. 选择“上传”按钮，然后找到要上传的 blob。
1. 在“上传 blob”窗格中展开“高级”设置 。
1. 找到“加密范围”下拉部分。 默认情况下，将使用容器的默认加密范围（如果已指定）创建 blob。 如果容器要求 blob 使用默认加密范围，则禁用此部分。
1. 若要为要上传的 blob 指定其他范围，请选择“选择现有范围”，然后从下拉列表中选择所需的范围。

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="显示如何上传具有加密范围的 blob 的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要上传使用 PowerShell 指定加密范围的 blob，请调用 [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 命令并提供该 blob 的加密范围。

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要上传使用 Azure CLI 指定加密范围的 blob，请调用 [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) 命令并提供该 blob 的加密范围。

如果使用 Azure Cloud Shell，请按照 [上传 blob](storage-quickstart-blobs-cli.md#upload-a-blob) 中所述的步骤在根目录中创建文件。 然后，可以使用以下示例将此文件上传到 blob。

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>更改范围的加密密钥

若要将保护加密作用域的密钥从 Microsoft 托管的密钥更改为客户托管的密钥，请首先确保已启用客户管理的密钥，Azure Key Vault 或 Key Vault HSM 用于存储帐户。 有关详细信息，请参阅 [Azure Key Vault 中存储的客户托管密钥的配置加密](../common/customer-managed-keys-configure-key-vault.md) 或 [Azure Key Vault 中存储的客户托管密钥配置加密](../common/customer-managed-keys-configure-key-vault.md)。

# <a name="portal"></a>[Portal](#tab/portal)

若要更改保护 Azure 门户范围的密钥，请执行以下步骤：

1. 导航到“加密范围”选项卡，查看存储帐户的加密范围列表。
1. 选择要修改的范围旁边的“更多”按钮。
1. 在“编辑加密范围”窗格中，可以将加密类型从 Microsoft 托管密钥更改为客户管理的密钥，反之亦然。
1. 要选择新的客户管理的密钥，请选择“使用新密钥”并指定密钥保管库、密钥和密钥版本。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 将保护加密范围的密钥从客户管理的密钥更改为 Microsoft 托管密钥，请调用 Update-AzStorageEncryptionScope 命令并传入 `-StorageEncryption` 参数：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

接下来，调用 Update-AzStorageEncryptionScope 命令并传入 `-KeyUri` 和 `-KeyvaultEncryption` 参数：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 将保护加密范围的密钥从客户管理的密钥更改为 Microsoft 托管密钥，请调用 [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) 命令并传入值为 `Microsoft.Storage` 的 `--key-source` 参数：

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

接下来，调用 az storage account encryption-scope update 命令，传入 `--key-uri` 参数，并传入值为 `Microsoft.KeyVault` 的 `--key-source` 参数：

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>禁用加密范围

禁用加密范围后，将不再为此付费。 禁用不需要的任何加密范围以避免不必要的费用。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../common/storage-service-encryption.md)。

# <a name="portal"></a>[Portal](#tab/portal)

若要在 Azure 门户中禁用加密范围，请导航到该存储帐户的“加密范围”设置，选择所需的加密范围，然后选择“禁用” 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 禁用加密范围，请调用 Update-AzStorageEncryptionScope 命令，并包括值为 `disabled` 的 `-State` 参数，如以下示例中所示。 若要重新启用加密范围，请调用相同的命令，并将 `-State` 参数设置为 `enabled`。 请务必将示例中的占位符值替换为你自己的值：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 禁用加密范围，请调用 [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) 命令，并包括值为 `Disabled` 的 `--state` 参数，如以下示例中所示。 若要重新启用加密范围，请调用相同的命令，并将 `--state` 参数设置为 `Enabled`。 请务必将示例中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
- [Blob 存储的加密范围（预览）](encryption-scope-overview.md)
- [用于 Azure 存储加密的客户托管密钥](../common/customer-managed-keys-overview.md)