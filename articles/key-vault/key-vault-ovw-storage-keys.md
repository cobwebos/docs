---
ms.assetid: 
title: "Azure Key Vault 存储帐户密钥"
description: "存储帐户密钥在 Azure Key Vault 与 Azure 存储帐户基于密钥的访问方式之间提供无缝集成。"
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: a87877f4b213365442400d113a67964ef942341f
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
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
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault 存储密钥推出之后 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>开发者指南

- 只允许 Key Vault 管理 ASA 密钥。 不要尝试自行管理这些密钥，否则会干扰 Key Vault 的流程。 
- 不要允许多个 Key Vault 对象管理 ASA 密钥。 
- 如果需要手动重新生成 ASA 密钥，我们建议通过 Key Vault 重新生成。 

## <a name="getting-started"></a>入门

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>基于角色的访问控制 (RBAC) 权限的设置

Azure Key Vault 应用程序标识需要有权列出和重新生成存储帐户的密钥。 可使用以下步骤设置这些权限：

- 获取 Azure Key Vault 标识的 ObjectId： 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- 将存储密钥操作员角色分配给 Azure Key Vault 标识： 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > 对于经典帐户类型，请将角色参数设置为“经典存储帐户密钥操作员服务角色”。

## <a name="working-example"></a>有效示例

下面的示例展示了如何创建 Key Vault 托管 Azure 存储帐户和关联的共享访问签名 (SAS) 定义。

### <a name="assumptions"></a>假设

下面提供了此有效示例的语句。

- 存储资源位置：/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1

- 密钥保管库名称：yourtest1

### <a name="get-a-service-principal"></a>获取服务主体

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

上一命令的输出包含 ServicePrincipal，我们称之为 yourKeyVaultServicePrincipalId。 

### <a name="set-permissions"></a>设置权限

请务必将存储权限设置为“全部”。 可以使用以下命令获取 yourUserPrincipalId 并在保管库中设置权限。

```powershell
$youruserPrincipalId = (Get-AzureRmADUser -SearchString "your user principal name").Id
```
现在搜索名称并获取相关 ObjectId（在保管库中设置权限时会用到）。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $youruserPrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>允许访问

必须先为 Key Vault 服务提供对存储帐户的访问权限，然后才能创建托管存储帐户和 SAS 定义。

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>创建存储帐户

现在，创建托管存储帐户和两个 SAS 定义。 帐户 SAS 提供不同的权限，协助访问 blob 服务。

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>重新生成

运行以下命令，设置重新生成周期。

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>设置 SAS 定义

在 Key Vault 中设置托管存储帐户的 SAS 定义。

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>获取令牌

获取相应的 SAS 令牌，并对存储进行调用。

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>创建存储

请注意，无法尝试使用 $sastoken1 进行访问，但可以使用 $sastoken2 进行访问。 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>示例摘要

可以使用拥有写权限的 SAS 令牌访问存储 blob 内容。

### <a name="relevant-powershell-cmdlets"></a>相关的 Powershell cmdlet

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

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
