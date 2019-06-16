---
title: 管理存储帐户密钥，使用 Azure 密钥保管库和 Azure CLI
description: 存储帐户密钥提供 Azure 密钥保管库与基于密钥的访问的 Azure 存储帐户之间的无缝集成。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476151"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>管理存储帐户密钥，使用 Azure 密钥保管库和 Azure CLI 

Azure Key Vault 管理 Azure 存储帐户和经典存储帐户的密钥。 可以使用 Key Vault 托管的存储帐户功能为你完成多个密钥管理功能。

[Azure 存储帐户](/azure/storage/storage-create-storage-account)使用由帐户名和密钥构成的凭据。 该密钥是自动生成的并作为密码，而不是作为加密密钥。 Key Vault 通过将其作为存储管理存储帐户密钥[Key Vault 机密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)。 密钥列出 （同步） 的 Azure 存储帐户，并且会定期重新生成或_旋转_。 

当您使用托管的存储帐户密钥功能时，请考虑以下几点：

- 永远不会给调用方的响应中返回密钥值。
- 只有密钥保管库应管理存储帐户密钥。 不自行管理密钥，并避免干扰 Key Vault 的进程。
- 仅单个密钥保管库对象应管理存储帐户密钥。 不允许从多个对象的密钥管理。
- 你可以请求密钥保管库来管理你的存储帐户的用户主体，但不是与服务主体。
- 通过密钥保管库仅重新生成密钥。 不要手动重新生成存储帐户密钥。 

> [!NOTE]
> Azure 存储集成与 Azure Active Directory (Azure AD) 是 Microsoft 的基于云的标识和访问管理服务。
> Azure AD 的集成是可用于[Azure blob 和队列](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。
> 使用 Azure AD 进行身份验证和授权。
> Azure AD 到 Azure 存储，就像 Azure 密钥保管库提供 OAuth2 基于令牌的访问权限。
>
> Azure AD，可使用应用程序或用户标识，而不存储帐户凭据在客户端应用程序进行身份验证。
> 可以使用[Azure AD 托管标识](/azure/active-directory/managed-identities-azure-resources/)在 Azure 上运行时。 管理的标识中删除客户端身份验证和将凭据存储中或与你的应用程序的需要。
> Azure AD 使用基于角色的访问控制 (RBAC) 来管理授权，还支持由密钥保管库。

### <a name="service-principal-application-id"></a>服务主体应用程序 ID

为每个已注册应用程序提供的 Azure AD 租户[服务主体](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服务主体可作为应用程序标识 (ID)。 应用程序 ID 用于授权安装过程中通过 RBAC 其他 Azure 资源的访问权限。

密钥保管库是在所有 Azure AD 租户中预注册的 Microsoft 应用程序。 在相同的应用程序 ID 和每个 Azure 云内注册密钥保管库。

| 租户 | 云 | 应用程序 ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公共 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>必备组件

使用密钥保管库来管理你的存储帐户密钥之前，请查看系统必备组件：

- 安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。
- 创建[Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 请按照[这些步骤](https://docs.microsoft.com/azure/storage/)。
- 存储帐户名称必须使用小写字母和数字。 名称的长度必须介于 3 到 24 个字符之间。        
      
## <a name="manage-storage-account-keys"></a>管理存储帐户密钥

有四个基本步骤来使用密钥保管库用于管理存储帐户密钥：

1. 获取现有的存储帐户。
1. 提取现有密钥保管库。
1. 将 Key Vault 托管存储帐户添加到保管库。 设置`key1`作为活动密钥重新生成期为 180 天。
1. 使用`key1`设置指定的存储帐户的存储上下文。

> [!NOTE]
> 作为一项服务的密钥保管库分配操作员在存储帐户上的权限。
> 
> 设置 Azure Key Vault 托管存储帐户密钥后，只能更改密钥通过密钥保管库。
> 对于托管的存储帐户密钥，密钥保管库管理存储帐户密钥的轮换。

1. 创建存储帐户后，运行以下命令来获取存储帐户的资源 ID，以管理：
    ```
    az storage account show -n storageaccountname
    ```

    从命令输出中复制的资源 ID 值：
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    示例输出：
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. 将"存储帐户密钥操作员服务角色"RBAC 角色分配到密钥保管库中。 此角色限制到存储帐户的访问作用域。 对于经典存储帐户，请使用"经典存储帐户密钥操作员服务角色"角色。

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` 是 Azure 公有云中的密钥保管库的对象 ID。 若要在 Azure 政府版云中的密钥保管库中获取的对象 ID，请参阅[服务主体应用程序 ID](#service-principal-application-id)。
    
1. 创建管理密钥保管库的存储帐户：

    设置为 90 天重新生成周期。 90 天后，将密钥保管库重新生成`key1`并将活动密钥从交换`key2`到`key1`。 `key1` 然后会标记为活动密钥。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>创建和生成令牌

您还可以要求 Key Vault 来生成共享的访问签名令牌。 共享访问签名对存储帐户中的资源提供委托访问。 您可以授予客户端对资源的访问存储帐户中而无需共享帐户密钥。 共享的访问签名提供共享存储资源，而不会危及帐户密钥的安全方式。

在本部分中的命令完成以下操作：

- 设置帐户共享访问签名定义`<YourSASDefinitionName>`。 定义已设置为上托管的密钥保管库存储帐户`<YourStorageAccountName>`在 key vault 中`<VaultName>`。
- 创建 Blob、 文件、 表和队列服务的帐户共享的访问签名令牌。 服务、 容器和对象，该令牌创建为资源类型。 标记创建具有所有权限，通过 https，并使用指定的开始和结束日期。
- 设置保管库中托管的密钥保管库存储共享访问签名定义。 定义具有模板创建的共享的访问签名令牌的 URI。 定义具有共享的访问签名类型`account`和有效期为 N 天。
- 从共享的访问签名定义对应的密钥保管库机密检索实际访问令牌。

完成上一节中的步骤后，运行以下命令，以请求密钥保管库生成共享的访问签名令牌。 

1. 创建共享的访问签名定义。 创建共享的访问签名定义后，请求密钥保管库来生成更多的共享的访问签名令牌。 此操作需要`storage`和`setsas`权限。
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    有关操作的帮助，请参阅[az 存储帐户生成 sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)参考文档。

    该操作成功运行后，复制的输出。
    ```console
       "se=2020-01-01&sp=***"
    ```

1. 使用`$sasToken`由前一个命令生成并创建共享的访问签名定义。 有关命令参数的详细信息，请参阅[az keyvault 存储 sas 定义创建](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)参考文档。
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    当用户不具备对存储帐户的权限时，首先获取用户的对象 ID:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>在代码中提取令牌

通过提取执行你的存储帐户上的操作[共享访问签名令牌](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)从密钥保管库。

有三种方法对密钥保管库进行身份验证：

- 使用托管的服务标识。 强烈建议使用此方法。
- 使用服务主体和证书。 
- 使用服务主体和密码。 不建议使用此方法。

有关详细信息，请参阅[Azure 密钥保管库：基本概念](key-vault-whatis.md#basic-concepts)。

下面的示例演示如何提取共享的访问签名令牌。 在创建共享的访问签名定义后提取令牌。 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

如果你的共享的访问签名令牌即将过期，从密钥保管库重新提取共享的访问签名令牌，并更新代码。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI 命令

有关与被管理的存储帐户的 Azure CLI 命令的信息，请参阅[az keyvault 存储](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)参考文档。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[密钥、 机密和证书](https://docs.microsoft.com/rest/api/keyvault/)。
- 在查看文章[Azure Key Vault 团队博客](https://blogs.technet.microsoft.com/kv/)。
