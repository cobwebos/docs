---
ms.assetid: ''
title: Azure Key Vault 存储帐户密钥
description: 存储帐户密钥在 Azure Key Vault 与 Azure 存储帐户基于密钥的访问方式之间提供无缝集成。
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault 存储帐户密钥

在 Azure Key Vault 存储帐户密钥推出之前，开发人员必须管理其自己的 Azure 存储帐户 (ASA) 密钥，并手动或通过外部自动化功能轮换这些密钥。 现在，Key Vault 存储帐户密钥已实现为 [Key Vault 机密](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)，可用于通过 Azure 存储帐户进行身份验证。

Azure 存储帐户 (ASA) 密钥功能负责管理密钥轮换。 此外，它还提供共享访问签名 (SAS) 作为方法，这样用户就无需直接接触 ASA 密钥。

有关 Azure 存储帐户的其他常规信息，请参阅[关于 Azure 存储帐户](https://docs.microsoft.com/azure/storage/storage-create-storage-account)。

## <a name="supporting-interfaces"></a>支持接口

有关编程和脚本接口的完整列表和链接，请参阅 [Key Vault 开发者指南](key-vault-developers-guide.md#coding-with-key-vault)。


## <a name="what-key-vault-manages"></a>Key Vault 管理什么

当用户使用托管存储帐户密钥时，Key Vault 会代表用户执行多个内部管理功能。

- Azure Key Vault 管理 Azure 存储帐户 (ASA) 的密钥。
    - 在内部，Azure Key Vault 可以使用 Azure 存储帐户列出（同步）密钥。
    - Azure Key Vault 定期重新生成（轮换）密钥。
    - 响应调用方时永远不会返回密钥值。
    - Azure Key Vault 管理存储帐户和经典存储帐户的密钥。
- Azure Key Vault 允许保管库/对象所有者创建 SAS（帐户或服务 SAS）的定义。
    - 通过 REST URI 路径以机密形式返回使用 SAS 定义创建的 SAS 值。 有关详细信息，请参阅 [Azure Key Vault 存储帐户操作](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)。

## <a name="naming-guidance"></a>命名指南

- 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。
- SAS 定义名称的长度必须为 1-102 个字符，只能包含 0-9、a-z、A-Z。

## <a name="developer-experience"></a>开发人员体验

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault 存储密钥推出之前

在过去，开发人员往往需要使用存储帐户密钥采取以下做法才能访问 Azure 存储。
1. 在 Azure AppService 应用程序设置或另一个存储中存储连接字符串或 SAS 令牌。
1. 在应用程序启动时，提取连接字符串或 SAS 令牌。
1. 创建 [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount)，以便与存储进行交互。

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault 存储密钥推出之后

开发人员创建 [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) 并利用它获取其存储的 SAS 令牌。 然后，他们使用该令牌创建 [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount)。

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>开发者指南

- 只允许 Key Vault 管理 ASA 密钥。 不要尝试自行管理这些密钥，否则会干扰 Key Vault 的流程。
- 不要允许多个 Key Vault 对象管理 ASA 密钥。
- 如果需要手动重新生成 ASA 密钥，我们建议通过 Key Vault 重新生成。

## <a name="getting-started"></a>入门

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>基于角色的访问控制 (RBAC) 权限的设置

Azure Key Vault 应用程序标识需要有权列出和重新生成存储帐户的密钥。 可使用以下步骤设置这些权限：

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>有效示例

下面的示例展示了如何创建 Key Vault 托管 Azure 存储帐户和关联的共享访问签名 (SAS) 定义。

### <a name="prerequisite"></a>先决条件

确保已完成[基于角色的访问控制 (RBAC) 权限的设置](#setup-for-role-based-access-control-rbac-permissions)。

### <a name="setup"></a>设置

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

接下来，为**你的帐户**设置权限以确保你可以管理 Key Vault 中的所有存储权限。 在以下示例中，我们的 Azure 帐户是 _developer@contoso.com_。

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>创建 Key Vault 托管存储帐户

现在，在 Azure Key Vault 中创建托管存储帐户，并使用存储帐户的访问密钥创建 SAS 令牌。
- `-ActiveKeyName` 使用“key2”生成 SAS 令牌。
- `-AccountName` 用于标识托管存储帐户。 下面，我们将使用存储帐户名称来使其保持简单，但它可以是任何名称。
- `-DisableAutoRegenerateKey` 指定不重新生成存储帐户密钥。

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>重新生成密钥

如果想要 Key Vault 定期重新生成存储访问密钥，可以设置重新生成周期。 下面，我们将重新生成周期设置为 3 天。 3 天后，Key Vault 将重新生成“key1”，并将活动密钥从“key2”交换为“key1”。

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>设置 SAS 定义

帐户 SAS 提供不同的权限，协助访问 blob 服务。
在 Key Vault 中设置托管存储帐户的 SAS 定义。
- `-AccountName` 是 Key Vault 中托管存储帐户的名称。
- `-Name` 是存储中 SAS 令牌的标识符。
- `-ValidityPeriod` 设置生成的 SAS 令牌的到期日期。

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>获取 SAS 令牌

获取相应的 SAS 令牌，并对存储进行调用。 执行 [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition) 时，使用 `AccountName` 和 `Name` 参数的输入构造 `-SecretName`。

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>创建存储

请注意，尝试使用 *$readSasToken* 访问会失败，但可以使用 *$writeSasToken* 进行访问。

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

可以使用拥有写权限的 SAS 令牌访问存储 blob 内容。

### <a name="relevant-powershell-cmdlets"></a>相关的 Powershell cmdlet

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>存储帐户登记

示例：作为 Key Vault 对象所有者，将存储帐户对象添加到 Azure Key Vault 以载入存储帐户。

在载入期间，Key Vault 需要验证载入帐户的标识是否有权“列出”和“重新生成”存储密钥。 为验证这些权限，Key Vault 从身份验证服务获取 OBO（代表）令牌，将受众设置为 Azure 资源管理器，并执行对 Azure 存储服务的“列出密钥”调用。 如果 list 调用失败，则无法创建 Key Vault 对象，并返回 HTTP 状态代码“Forbidden”。 以这种方式列出的密钥将缓存到 Key Vault 实体存储中。

在获得重新生成密钥的所有权之前，Key Vault 必须验证该标识是否具有“重新生成”权限。 若要通过 OBO 令牌验证该标识，并验证 Key Vault 第一方标识是否具有这些权限：

- Key Vault 会列出对存储帐户资源的 RBAC 权限。
- Key Vault 会通过操作和非操作的正则表达式匹配来验证响应。

可在 [Key Vault - 托管存储帐户密钥示例](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167)中找到一些支持示例。

如果该标识没有“重新生成”权限，或者 Key Vault 第一方标识没有“列出”或“重新生成”权限，则登记请求会失败，并返回相应的错误代码和消息。

仅当使用 PowerShell 或 CLI 的第一方本机客户端应用程序时，OBO 令牌才能正常工作。

## <a name="other-applications"></a>其他应用程序

- 使用 Key Vault 存储帐户密钥构造的 SAS 令牌针对 Azure 存储帐户提供更高力度的访问控制。 有关详细信息，请参阅[使用共享访问签名](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)。

## <a name="see-also"></a>另请参阅

- [关于键、密钥和证书](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 团队博客](https://blogs.technet.microsoft.com/kv/)
