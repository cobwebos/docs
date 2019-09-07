---
title: 用 Azure Key Vault 和 Azure CLI 管理存储帐户密钥
description: 存储帐户密钥在 Azure Key Vault 和基于密钥的访问权限之间提供对 Azure 存储帐户的无缝集成。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 251a7c21b671052a23f6ee18cb4278737464b25c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744872"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>用 Azure Key Vault 和 Azure CLI 管理存储帐户密钥 

Azure Key Vault 管理 Azure 存储帐户和经典存储帐户的密钥。 你可以使用 Key Vault 托管存储帐户功能为你完成多项重要管理功能。

[Azure 存储帐户](/azure/storage/storage-create-storage-account)使用由帐户名和密钥构成的凭据。 该键自动生成并用作密码，而不是作为加密密钥。 Key Vault 通过将存储帐户密钥存储为[Key Vault 机密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)来管理该密钥。 使用 Azure 存储帐户列出（同步）密钥并定期重新生成或_旋转_密钥。 

使用托管存储帐户密钥功能时，请注意以下几点：

- 在响应调用方时永远不会返回密钥值。
- 仅 Key Vault 应管理你的存储帐户密钥。 不要自己管理密钥，避免干扰 Key Vault 进程。
- 只有单个 Key Vault 对象应管理存储帐户密钥。 不允许从多个对象进行密钥管理。
- 可以请求 Key Vault 使用用户主体管理存储帐户，但不能使用服务主体来管理存储帐户。
- 仅使用 Key Vault 重新生成密钥。 请勿手动重新生成存储帐户密钥。 

> [!NOTE]
> 与 Azure Active Directory （Azure AD）的 Azure 存储集成是 Microsoft 基于云的标识和访问管理服务。
> Azure AD 集成适用于[Azure blob 和队列](../storage/common/storage-auth-aad.md)。
> 使用 Azure AD 进行身份验证和授权。
> Azure AD 提供对 Azure 存储的 OAuth2 令牌访问，就像 Azure Key Vault 一样。
>
> Azure AD 允许你使用应用程序或用户标识（而不是存储帐户凭据）对客户端应用程序进行身份验证。
> 在 Azure 上运行时，可以使用[Azure AD 托管标识](/azure/active-directory/managed-identities-azure-resources/)。 托管标识消除了对客户端身份验证的需求，并将凭据存储在或与应用程序一起存储。
> Azure AD 使用基于角色的访问控制（RBAC）来管理授权，Key Vault 也支持此方法。

### <a name="service-principal-application-id"></a>服务主体应用程序 ID

Azure AD 租户为每个已注册的应用程序提供一个[服务主体](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服务主体充当应用程序标识（ID）。 在授权设置期间，使用应用程序 ID 通过 RBAC 访问其他 Azure 资源。

Key Vault 是在所有 Azure AD 租户中预先注册的 Microsoft 应用程序。 Key Vault 在相同的应用程序 ID 和每个 Azure 云中进行注册。

| 租户 | 云 | 应用程序 ID |
| --- | --- | --- |
| Azure AD | Azure 政府 | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>先决条件

使用 Key Vault 管理存储帐户密钥之前，请先查看先决条件：

- 安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。
- 创建[Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 请按照[以下步骤](../storage/index.yml)操作。
- 存储帐户名称只能使用小写字母和数字。 名称长度必须介于3到24个字符之间。        
      
## <a name="manage-storage-account-keys"></a>管理存储帐户密钥

要使用 Key Vault 来管理存储帐户密钥，请执行以下四个基本步骤：

1. 获取现有的存储帐户。
1. 提取现有密钥保管库。
1. 将 Key Vault 托管存储帐户添加到保管库。 设置`key1`为活动密钥，其重新生成期为90天。
1. 使用`key1`设置指定存储帐户的存储上下文。

> [!NOTE]
> 为服务 Key Vault 分配了对你的存储帐户的操作员权限。
> 
> 设置 Azure Key Vault 托管存储帐户密钥后，只能使用 Key Vault 更改密钥。
> 对于托管存储帐户密钥，Key Vault 管理存储帐户密钥的旋转。

1. 创建存储帐户后，运行以下命令以获取要管理的存储帐户的资源 ID：
    ```
    az storage account show -n storageaccountname
    ```

    从命令输出中复制 "资源 ID" 值：
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    示例输出：
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. 将 "存储帐户密钥操作员服务角色" RBAC 角色分配给 Key Vault。 此角色将访问作用域限制为你的存储帐户。 对于经典存储帐户，请使用 "经典存储帐户密钥操作员服务角色" 角色。

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`是 Azure 公有云中 Key Vault 的对象 ID。 若要获取 Azure 政府云中 Key Vault 的对象 ID，请参阅[服务主体应用程序 ID](#service-principal-application-id)。
    
1. 创建 Key Vault 托管存储帐户：

    将重新生成周期设置为90天。 90天后 Key Vault 将活动`key1` `key2`密钥重新生成并交换到`key1`。 `key1`然后，将标记为活动密钥。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>创建和生成令牌

你还可以要求 Key Vault 生成共享访问签名令牌。 共享访问签名对存储帐户中的资源提供委托访问。 你可以授予客户端对存储帐户中的资源的访问权限，而无需共享帐户密钥。 共享访问签名提供了一种安全的方法来共享存储资源，而不会危及帐户密钥。

此部分中的命令完成了以下操作：

- 设置帐户共享访问签名定义`<YourSASDefinitionName>`。 在密钥保管库`<YourStorageAccountName>` `<VaultName>`中的 Key Vault 托管存储帐户上设置定义。
- 为 Blob、文件、表和队列服务创建帐户共享访问签名令牌。 为资源类型服务、容器和对象创建令牌。 令牌是通过 https 上的所有权限创建的，并且具有指定的开始日期和结束日期。
- 在保管库中设置 Key Vault 托管存储共享访问签名定义。 定义具有创建的共享访问签名令牌的模板 URI。 定义具有共享访问签名类型`account` ，且有效期为 N 天。
- 从与共享访问签名定义对应的 Key Vault 密钥中检索实际访问令牌。

完成上一部分中的步骤后，运行以下命令，请求 Key Vault 生成共享访问签名令牌。 

1. 创建共享访问签名定义。 创建共享访问签名定义之后，要求 Key Vault 生成更多的共享访问签名令牌。 此操作需要`storage`和`setsas`权限。
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    有关此操作的帮助，请参阅[az storage account 生成-sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)参考文档。

    操作成功运行后，复制输出。
    ```console
       "se=2020-01-01&sp=***"
    ```

1. 使用上`$sasToken`一命令生成的，并创建共享访问签名定义。 有关命令参数的详细信息，请参阅[az keyvault storage sas-定义创建](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)参考文档。
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    当用户无权访问存储帐户时，请先获取该用户的对象 ID：
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>在代码中提取令牌

通过从 Key Vault 获取[共享访问签名令牌](../storage/common/storage-dotnet-shared-access-signature-part-1.md)来对存储帐户执行操作。

有三种方法可以对 Key Vault 进行身份验证：

- 使用托管服务标识。 强烈建议使用此方法。
- 使用服务主体和证书。 
- 使用服务主体和密码。 不建议使用此方法。

有关详细信息，请[参阅 Azure Key Vault：基本概念](key-vault-whatis.md#basic-concepts)。

下面的示例演示如何获取共享访问签名令牌。 您可以在创建共享访问签名定义后提取令牌。 

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

如果共享访问签名令牌即将过期，请从 Key Vault 获取共享访问签名令牌，并更新代码。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI 命令

有关与托管存储帐户相关的 Azure CLI 命令的信息，请参阅[az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) reference 文档。

## <a name="next-steps"></a>后续步骤

- 详细了解[密钥、机密和证书](https://docs.microsoft.com/rest/api/keyvault/)。
- 查看[Azure Key Vault 团队博客](https://blogs.technet.microsoft.com/kv/)上的文章。
