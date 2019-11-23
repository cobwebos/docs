---
title: 使用 Azure Key Vault 和 Azure CLI 管理存储帐户密钥
description: 存储帐户密钥在 Azure Key Vault 与 Azure 存储帐户基于密钥的访问方式之间提供无缝集成。
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
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>使用 Key Vault 和 Azure CLI 管理存储帐户密钥

Azure 存储帐户使用由帐户名和密钥构成的凭据。 密钥是自动生成的，充当密码而不是加密密钥。 Key Vault 可以通过将存储帐户密钥存储为 [Key Vault 机密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)来对其进行管理。 

可以使用 Key Vault 托管的存储帐户密钥功能列出（同步） Azure 存储帐户中的密钥，并定期重新生成（轮换）密钥。 可以管理存储帐户和经典存储帐户的密钥。

使用托管的存储帐户密钥功能时，请注意以下要点：

- 响应调用方时永远不会返回密钥值。
- 只有 Key Vault 能够管理存储帐户密钥。 不要自行管理密钥，并避免干扰 Key Vault 进程。
- 只有单个 Key Vault 对象能够管理存储帐户密钥。 不要允许从多个对象进行密钥管理。
- 可以请求 Key Vault 使用用户主体（而不要使用服务主体）管理存储帐户。
- 只使用 Key Vault 重新生成密钥。 不要手动重新生成存储帐户密钥。

我们建议使用 Azure 存储与 Azure Active Directory (Azure AD) 的集成，这是 Microsoft 推出的基于云的标识和访问管理服务。 Azure AD 集成适用于 [Azure Blob 和队列](../storage/common/storage-auth-aad.md)，提供对 Azure 存储的基于 OAuth2 令牌的访问（类似于 Azure Key Vault）。

Azure AD 允许使用应用程序标识或用户标识（而不是存储帐户凭据）对客户端应用程序进行身份验证。 在 Azure 上运行时，可以使用 [Azure AD 托管标识](/azure/active-directory/managed-identities-azure-resources/)。 托管标识消除了客户端身份验证的需要，并可以在应用程序中存储凭据，或者将凭据与应用程序一同存储。

Azure AD 使用同样受 Key Vault 支持的基于角色的访问控制 (RBAC) 来管理授权。

## <a name="service-principal-application-id"></a>服务主体应用程序 ID

Azure AD 租户为每个已注册的应用程序提供[服务主体](/azure/active-directory/develop/developer-glossary#service-principal-object)。 该服务主体充当应用程序 ID，通过 RBAC 设置访问其他 Azure 资源的授权期间，将使用它。

Key Vault 是已在所有 Azure AD 租户中预先注册的 Microsoft 应用程序。 Key Vault 注册到每个 Azure 云中的同一个应用程序 ID 下。

| 租户 | 云 | 应用程序 ID |
| --- | --- | --- |
| Azure AD | Azure 政府 | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公共 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>先决条件

若要完成本指南，必须先执行以下操作：

- [安装 Azure CLI](/cli/azure/install-azure-cli)。
- [创建密钥保管库](quick-create-cli.md)
- [创建 Azure 存储帐户](../storage/common/storage-quickstart-create-account.md?tabs=azure-cli)。 存储帐户名必须仅使用小写字母和数字。 名称的长度必须为 3 到 24 个字符。
      
## <a name="manage-storage-account-keys"></a>管理存储帐户密钥

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用 [az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令对 Azure CLI 会话进行身份验证。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>向 Key Vault 授予对你的存储帐户的访问权限

使用 Azure CLI [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest) 命令授予 Key Vault 访问你的存储帐户的权限。 为该命令提供以下参数值：

- `--role`：传递 "存储帐户密钥操作员服务角色" RBAC 角色。 此角色将访问范围限制为你的存储帐户。 对于经典存储帐户，请改为传递“经典存储帐户密钥操作员服务角色”。
- `--assignee-object-id`：传递值 "93c27d83-f79b-4cb2-8dd4-4aa716542e74"，这是 Azure 公有云中 Key Vault 的对象 ID。 （若要获取 Azure 政府云中 Key Vault 的对象 ID，请参阅[服务主体应用程序 id](#service-principal-application-id)。）
- `--scope`：传递格式为 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`的存储帐户资源 ID。 若要查找订阅 ID，请使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 命令；若要查找存储帐户名称和存储帐户资源组，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 命令。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>创建 Key Vault 托管存储帐户

 使用 Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) 命令创建 Key Vault 托管的存储帐户。 将重新生成周期设置为 90 天。 90 天后，Key Vault 将重新生成 `key1`，并将活动密钥从 `key2` 交换为 `key1`。 然后，`key1` 将标记为活动密钥。 为该命令提供以下参数值：

- `--vault-name`：传递密钥保管库的名称。 若要查找 Key Vault 的名称，请使用 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) 命令。
- `-n`：传递您的存储帐户的名称。 若要查找存储帐户的名称，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 命令。
- `--resource-id`：传递格式为 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`的存储帐户资源 ID。 若要查找订阅 ID，请使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 命令；若要查找存储帐户名称和存储帐户资源组，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 命令。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>共享访问签名令牌

也可以要求 Key Vault 生成共享访问签名令牌。 共享访问签名对存储帐户中的资源提供委托访问。 可以授予客户端访问存储帐户中的资源的权限，而无需共享帐户密钥。 使用共享访问签名可以安全共享存储资源，而不会透露帐户密钥。

本部分所述的命令将完成以下操作：

- 设置帐户共享访问签名定义 `<YourSASDefinitionName>`。 该定义是在 Key Vault `<YourStorageAccountName>` 中的 Key Vault 托管存储帐户 `<YourKeyVaultName>` 上设置的。
- 为 Blob、文件、表和队列服务创建帐户共享访问签名令牌。 为“服务”、“容器”和“对象”资源类型创建令牌。 创建的令牌拥有所有权限、通过 HTTPS 访问并指定了开始和结束日期。
- 在保管库中设置 Key Vault 托管的存储共享访问签名定义。 该定义包含创建的共享访问签名令牌的模板 URI。 该定义使用共享访问签名类型 `account`，有效期为 N 天。
- 验证共享访问签名是否已作为机密保存在 Key Vault 中。

### <a name="create-a-shared-access-signature-token"></a>创建共享访问签名令牌

使用 Azure CLI [az storage account generate-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) 命令创建共享访问签名定义。 此操作需要 `storage` 和 `setsas` 权限。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
操作成功运行后，复制输出。

```console
"se=2020-01-01&sp=***"
```

此输出将在下一步骤中传递给 `--template-id` 参数。

### <a name="generate-a-shared-access-signature-definition"></a>生成共享访问签名定义

使用 Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) 命令并将上一步骤的输出传递给 `--template-id` 参数，以创建共享访问签名定义。  可将所选的名称提供给 `-n` 参数。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>验证共享访问签名定义

可以使用 Azure CLI [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 和 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令验证共享访问签名定义是否已存储在 Key Vault 中。

首先，使用 [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 命令查找 Key Vault 中的共享访问签名定义。

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

对应于 SAS 定义的机密包含以下属性：

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

现在，可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令和 `id` 属性查看该机密的内容。

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

此命令的输出会将 SAS 定义字符串显示为 `value`。


## <a name="next-steps"></a>后续步骤

- 详细了解[密钥、机密和证书](https://docs.microsoft.com/rest/api/keyvault/)。
- 查看 [Azure Key Vault 团队博客](https://blogs.technet.microsoft.com/kv/)中的文章。
- 参阅 [az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) 参考文档。
