---
ms.assetid: 
title: "Azure Key Vault 存储帐户密钥"
description: "存储帐户密钥在 Azure Key Vault 与 Azure 存储帐户的密钥访问方式之间提供无缝集成。"
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: b30f9601725cdf568f0f2e18bebdc4ae86a616f6
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault 存储帐户密钥

在 Azure Key Vault 存储帐户密钥推出之前，开发人员必须管理其自己的 Azure 存储帐户 (ASA) 密钥，并手动或通过外部自动化功能轮换这些密钥。 现在，Key Vault 存储帐户密钥已实现为 [Key Vault 机密](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)，可用于通过 Azure 存储帐户进行身份验证。 

使用 ASA 密钥功能可以自动管理机密，而不需要通过提供共享访问签名 (SAS) 的方法来直接与 SAS 密钥交互。 

有关 Azure 存储帐户的其他常规信息，请参阅[关于 Azure 存储帐户](https://docs.microsoft.com/azure/storage/storage-create-storage-account)。

## <a name="supporting-interfaces"></a>支持接口

Azure 存储帐户密钥功能最初是通过 REST、.NET/C# 和 PowerShell 接口提供的。 有关详细信息，请参阅 [Key Vault 参考](https://docs.microsoft.com/azure/key-vault/)。

## <a name="storage-account-keys-behavior"></a>存储帐户密钥的行为

### <a name="what-key-vault-manages"></a>Key Vault 管理什么

使用存储帐户密钥时，Key Vault 会自动执行多个内部管理功能。

1. Azure Key Vault 管理 Azure 存储帐户 (SAS) 的密钥。 
    - 在内部，Azure Key Vault 可以使用 Azure 存储帐户列出（同步）密钥。  
    - Azure Key Vault 定期重新生成（轮换）密钥。 
    - 响应调用方时永远不会返回密钥值。 
    - Azure Key Vault 管理存储帐户和经典存储帐户的密钥。 
2. Azure Key Vault 允许保管库/对象所有者创建 SAS（帐户或服务 SAS）的定义。 
    - 通过 REST URI 路径以机密形式返回使用 SAS 定义创建的 SAS 值。 有关详细信息，请参阅 [Azure Key Vault 存储帐户操作](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)。

### <a name="naming-guidance"></a>命名指南

存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。  
 
SAS 定义名称的长度必须为 1-102 个字符，只能包含 0-9、a-z、A-Z。

## <a name="developer-experience"></a>开发人员体验 

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault 存储密钥推出之前 

在过去，开发人员往往需要使用存储帐户密钥采取以下做法才能访问 Azure 存储。 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault 存储密钥推出之后 

```
//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOrHttp -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);

  ```
 
 ### <a name="developer-best-practices"></a>开发人员最佳做法 

- 只允许 Key Vault 管理 ASA 密钥。 不要尝试自行管理这些密钥，否则会干扰 Key Vault 的流程。 
- 不要允许多个 Key Vault 对象管理 ASA 密钥。 
- 如果需要手动重新生成 ASA 密钥，我们建议通过 Key Vault 重新生成。 

## <a name="getting-started"></a>入门

### <a name="setup-for-role-based-access-control-permissions"></a>基于角色的访问控制权限的设置

Key Vault 需要有权列出和重新生成存储帐户的密钥。 可使用以下步骤设置这些权限：

- 获取 Key Vault 的 ObjectId： 

    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- 将存储密钥操作员角色分配给 Azure Key Vault 标识： 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > 对于经典帐户类型，请将角色参数设置为“经典存储帐户密钥操作员服务角色”。

### <a name="storage-account-onboarding"></a>存储帐户登记 

#### <a name="example"></a>示例

Key Vault 对象所有者在 AzKV 上添加存储帐户对象，以登记存储帐户。

在登记期间，Key Vault 需要验证登记该帐户的标识是否有权“列出”和“重新生成”存储密钥。 Key Vault 从 EvoSTS 获取受众为 Azure Resource Manager 的 OBO 令牌，并向存储 RP 发出“列出密钥”的调用。 如果列出调用失败，Key Vault 对象创建将会失败并返回“禁止”http 状态代码。 以这种方式列出的密钥将缓存到 Key Vault 实体存储中。 

在获得重新生成密钥的所有权之前，Key Vault 必须验证该标识是否具有“重新生成”权限。 若要通过 OBO 令牌验证该标识，并验证 Key Vault 第一方标识是否具有这些权限：

- Key Vault 会列出对存储帐户资源的 RBAC 权限。
- Key Vault 会通过操作和非操作的正则表达式匹配来验证响应。 

部分支持示例： 

- [GitHub 示例](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167) 

如果通过 OBO 令牌验证后发现该标识没有“重新生成”权限，或者 Key Vault 第一方标识没有“列出”或“重新生成”权限，则登记请求将会失败，并返回相应的错误代码和消息。 

仅当使用 PowerShell 或 CLI 的第一方本机客户端应用程序时，OBO 令牌才能正常工作。

## <a name="other-applications"></a>其他应用程序

- 使用 Key Vault 存储帐户密钥构造的 SAS 令牌针对 Azure 存储帐户提供更高力度的访问控制。 有关详细信息，请参阅[使用共享访问签名](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)。

## <a name="see-also"></a>另请参阅

- [关于键、密钥和证书](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 团队博客](https://blogs.technet.microsoft.com/kv/)

