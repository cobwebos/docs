---
title: Azure Key Vault 托管存储帐户 - PowerShell 版本
description: 托管存储帐户功能在 Azure Key Vault 与 Azure 存储帐户之间提供无缝集成。
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81431262"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>通过编写代码提取共享访问签名令牌

可以使用密钥保管库中的[共享访问签名令牌](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)来管理存储帐户。 本文提供了提取 SAS 令牌并对其执行操作的 C# 代码示例。  有关如何创建和存储 SAS 令牌的信息，请参阅[使用密钥保管库和 Azure CLI 管理存储帐户密钥](overview-storage-keys.md)或[使用密钥保管库和 Azure PowerShell 管理存储帐户密钥](overview-storage-keys-powershell.md)。

## <a name="code-samples"></a>代码示例

在此示例中，代码从密钥保管库中提取 SAS 令牌，使用它创建新的存储帐户，并创建新的 Blob 服务客户端。  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

如果共享访问签名令牌即将过期，则可以从密钥保管库中提取共享访问签名令牌，并更新代码。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>后续步骤
- 了解如何[使用密钥保管库和 Azure CLI 管理存储帐户密钥](overview-storage-keys.md)或 [Azure PowerShell](overview-storage-keys-powershell.md)。
- 请参阅[托管存储帐户密钥示例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell 参考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
