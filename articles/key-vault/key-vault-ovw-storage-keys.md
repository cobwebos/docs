---
title: 用 Azure Key Vault 和 Azure CLI 管理存储帐户密钥
description: 存储帐户密钥在 Azure Key Vault 和基于密钥的访问权限之间提供对 Azure 存储帐户的无缝集成。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 62faf33dc8b3690036407972e12633e741a85d78
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176753"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>用 Key Vault 和 Azure CLI 管理存储帐户密钥

Azure 存储帐户使用包含帐户名和密钥的凭据。 密钥是自动生成的，用作密码，而不是作为加密密钥。 Key Vault 通过将存储帐户密钥存储为[Key Vault 机密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)来管理该密钥。 

可以使用 "Key Vault 托管存储帐户密钥" 功能，通过 Azure 存储帐户列出（同步）密钥，并定期重新生成（轮换）密钥。 你可以管理存储帐户和经典存储帐户的密钥。

使用托管存储帐户密钥功能时，请注意以下几点：

- 在响应调用方时永远不会返回密钥值。
- 仅 Key Vault 应管理你的存储帐户密钥。 不要自己管理密钥，避免干扰 Key Vault 进程。
- 只有单个 Key Vault 对象应管理存储帐户密钥。 不允许从多个对象进行密钥管理。
- 可以请求 Key Vault 使用用户主体管理存储帐户，但不能使用服务主体来管理存储帐户。
- 仅使用 Key Vault 重新生成密钥。 请勿手动重新生成存储帐户密钥。

建议结合使用 Azure 存储与 Azure Active Directory （Azure AD）、Microsoft 的基于云的标识和访问管理服务。 Azure AD 集成适用于[azure blob 和队列](../storage/common/storage-auth-aad.md)，并提供基于令牌的 OAuth2 访问 azure 存储（就像 Azure Key Vault）。

Azure AD 允许你使用应用程序或用户标识（而不是存储帐户凭据）对客户端应用程序进行身份验证。 在 Azure 上运行时，可以使用[Azure AD 托管标识](/azure/active-directory/managed-identities-azure-resources/)。 托管标识消除了对客户端身份验证的需求，并将凭据存储在或与应用程序一起存储。

Azure AD 使用基于角色的访问控制（RBAC）来管理授权，Key Vault 也支持此方法。

## <a name="service-principal-application-id"></a>服务主体应用程序 ID

Azure AD 租户为每个已注册的应用程序提供一个[服务主体](/azure/active-directory/develop/developer-glossary#service-principal-object)。 此服务主体充当应用程序 ID，在授权设置期间使用该 ID 通过 RBAC 访问其他 Azure 资源。

Key Vault 是在所有 Azure AD 租户中预先注册的 Microsoft 应用程序。 在每个 Azure 云中的同一应用程序 ID 上注册 Key Vault。

| 租户 | 云 | 应用程序 ID |
| --- | --- | --- |
| Azure AD | Azure 政府 | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公共 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>先决条件

若要完成本指南，必须首先执行以下操作：

- [安装 Azure CLI](/cli/azure/install-azure-cli)。
- [创建密钥保管库](quick-create-cli.md)
- [创建 Azure 存储帐户](../storage/common/storage-quickstart-create-account.md?tabs=azure-cli)。 存储帐户名称只能使用小写字母和数字。 名称长度必须介于3到24个字符之间。
      
## <a name="manage-storage-account-keys"></a>管理存储帐户密钥

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用[az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令对 Azure CLI 会话进行身份验证。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>授予对存储帐户的 Key Vault 访问权限

使用 Azure CLI [az role create create](/cli/azure/role/assignment?view=azure-cli-latest)命令为 Key Vault 访问你的存储帐户。 为命令提供以下参数值：

- `--role`：传递 "存储帐户密钥操作员服务角色" RBAC 角色。 此角色将访问作用域限制为你的存储帐户。 对于经典存储帐户，请改为传递 "经典存储帐户密钥操作员服务角色"。
- `--assignee-object-id`：传递值 "93c27d83-f79b-4cb2-8dd4-4aa716542e74"，这是 Azure 公有云中 Key Vault 的对象 ID。 （若要获取 Azure 政府云中 Key Vault 的对象 ID，请参阅[服务主体应用程序 id](#service-principal-application-id)。）
- `--scope`：传递格式为 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 的存储帐户资源 ID。 若要查找订阅 ID，请使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list)命令;若要查找存储帐户名称和存储帐户资源组，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>创建 Key Vault 托管存储帐户

 使用 Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add)命令创建 Key Vault 托管存储帐户。 将重新生成周期设置为90天。 90天后，Key Vault 重新生成 @no__t，并将活动密钥从 `key2` 交换为 `key1`。 @no__t，然后将其标记为活动密钥。 为命令提供以下参数值：

- `--vault-name`：传递密钥保管库的名称。 若要查找密钥保管库的名称，请使用 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)命令。
- `-n`：传递存储帐户的名称。 若要查找存储帐户的名称，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
- `--resource-id`：传递格式为 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 的存储帐户资源 ID。 若要查找订阅 ID，请使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list)命令;若要查找存储帐户名称和存储帐户资源组，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>共享访问签名令牌

你还可以要求 Key Vault 生成共享访问签名令牌。 共享访问签名对存储帐户中的资源提供委托访问。 你可以授予客户端对存储帐户中的资源的访问权限，而无需共享帐户密钥。 共享访问签名提供了一种安全的方法来共享存储资源，而不会危及帐户密钥。

此部分中的命令完成了以下操作：

- 设置帐户共享访问签名定义 `<YourSASDefinitionName>`。 在密钥保管库中的 Key Vault 托管存储帐户 `<YourStorageAccountName>` 上设置定义 `<YourKeyVaultName>`。
- 为 Blob、文件、表和队列服务创建帐户共享访问签名令牌。 为资源类型服务、容器和对象创建令牌。 令牌是通过 https 上的所有权限创建的，并且具有指定的开始日期和结束日期。
- 在保管库中设置 Key Vault 托管存储共享访问签名定义。 定义具有创建的共享访问签名令牌的模板 URI。 定义的共享访问签名类型为 `account`，有效期为 N 天。
- 验证共享访问签名是否已作为机密保存在密钥保管库中。

### <a name="create-a-shared-access-signature-token"></a>创建共享访问签名令牌

使用 Azure CLI [az storage account Create sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) command 创建共享访问签名定义。 此操作需要 @no__t 0 和 @no__t 权限。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
操作成功运行后，复制输出。

```console
"se=2020-01-01&sp=***"
```

此输出将在下一步传递到 `--template-id` 参数。

### <a name="generate-a-shared-access-signature-definition"></a>生成共享访问签名定义

使用 Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create)命令，将上一步的输出传递到 `--template-id` 参数，以创建共享访问签名定义。  您可以为 @no__t 参数提供您选择的名称。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>验证共享访问签名定义

可以使用 Azure CLI [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)和[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令验证共享访问签名定义是否已存储在密钥保管库中。

首先，使用[az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)命令查找密钥保管库中的共享访问签名定义。

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

对应于 SAS 定义的机密将具有以下属性：

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

你现在可以使用[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令和 `id` 属性来查看该机密的内容。

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

此命令的输出会将 SAS 定义字符串显示为 @ no__t-0。


## <a name="next-steps"></a>后续步骤

- 详细了解[密钥、机密和证书](https://docs.microsoft.com/rest/api/keyvault/)。
- 查看[Azure Key Vault 团队博客](https://blogs.technet.microsoft.com/kv/)上的文章。
- 请参阅[az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) reference 文档。
