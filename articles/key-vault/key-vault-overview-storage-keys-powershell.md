---
title: Azure Key Vault 托管存储帐户 - PowerShell 版本
description: 托管存储帐户功能提供 Azure Key Vault 和 Azure 存储帐户之间的无缝集成。
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 225d9b715c56e4813a8e26d881c876e7bd498155
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204218"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>用 Key Vault 和 Azure PowerShell 管理存储帐户密钥

Azure 存储帐户使用包含帐户名和密钥的凭据。 该键自动生成并用作密码，而不是作为加密密钥。 Key Vault 通过将存储帐户密钥存储为[Key Vault 机密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)来管理该密钥。 

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>服务主体应用程序 ID

Azure AD 租户为每个已注册的应用程序提供一个[服务主体](/azure/active-directory/develop/developer-glossary#service-principal-object)。 此服务主体充当应用程序 ID，在授权设置期间使用该 ID 通过 RBAC 访问其他 Azure 资源。

Key Vault 是在所有 Azure AD 租户中预先注册的 Microsoft 应用程序。 在每个 Azure 云中的同一应用程序 ID 上注册 Key Vault。

| 租户 | 云 | 应用程序 ID |
| --- | --- | --- |
| Azure AD | Azure 政府 | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>先决条件

若要完成本指南，必须首先执行以下操作：

- [安装 Azure PowerShell 模块](/powershell/azure/install-az-ps?view=azps-2.6.0)。
- [创建密钥保管库](quick-create-powershell.md)
- [创建 Azure 存储帐户](../storage/common/storage-quickstart-create-account.md?tabs=azure-powershell)。 存储帐户名称只能使用小写字母和数字。 名称长度必须介于3到24个字符之间。
      

## <a name="manage-storage-account-keys"></a>管理存储帐户密钥

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用[AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Cmdlet 对 PowerShell 会话进行身份验证。 

```azurepowershell-interactive
Connect-AzAccount
```
如果有多个 Azure 订阅，可以使用[AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet 列出这些订阅，并指定要与[AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet 一起使用的订阅。 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>设置变量

首先，在以下步骤中设置 PowerShell cmdlet 要使用的变量。 请确保<YourResourceGroupName>更新、 <YourStorageAccountName>和<YourKeyVaultName>占位符，并将 $keyVaultSpAppId 设置为`cfa8b339-82a2-471a-a3c9-0fc0be7a4093` （在上面的[服务主体应用程序 ID](#service-principal-application-id)中指定）。

我们还将使用 Azure PowerShell [AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0)和[AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) cmdlet 来获取你的用户 ID 和 Azure 存储帐户的上下文。

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>授予对存储帐户的 Key Vault 访问权限

只有在授权 Key Vault 访问你的存储帐户之后，它才可以访问和管理存储帐户密钥。 Key Vault 应用程序标识需要有权列出和重新生成存储帐户的密钥。 可通过内置的 RBAC 角色[存储帐户密钥操作员服务角色](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)启用这些权限。 

使用 Azure PowerShell [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet 将此角色分配到 Key Vault 服务主体，并将作用域限制为你的存储帐户。

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

成功完成角色分配后，应会看到类似于以下示例的输出：

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

如果 Key Vault 已添加到存储帐户中的角色，则你会收到“角色分配已存在” 错误。 还可以使用 Azure 门户中存储帐户的“访问控制(IAM)”页验证角色分配。  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>向托管存储帐户授予用户帐户权限

使用 Azure PowerShell [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet 来更新 Key Vault 访问策略，并向你的用户帐户授予存储帐户权限。

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

请注意，Azure 门户中存储帐户的“访问策略”页不会显示存储帐户的权限。

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>将托管存储帐户添加到 Key Vault 实例

使用 Azure PowerShell [AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet 在 Key Vault 实例中创建托管存储帐户。 `-DisableAutoRegenerateKey` 开关指定不重新生成存储帐户密钥。

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

成功添加不重新生成密钥的存储帐户后，应会看到类似于以下示例的输出：

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>启用密钥重新生成

如果希望 Key Vault 定期重新生成存储帐户密钥，可以使用 Azure PowerShell [AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet 来设置重新生成周期。 在此示例中，我们将重新生成期设置为三天。 三天后 Key Vault 将重新生成 "key2" 并将活动密钥从 "key2" 交换为 "key1"。

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

成功添加重新生成密钥的存储帐户后，应会看到类似于以下示例的输出：

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="shared-access-signature-tokens"></a>共享访问签名令牌

你还可以要求 Key Vault 生成共享访问签名令牌。 共享访问签名对存储帐户中的资源提供委托访问。 你可以授予客户端对存储帐户中的资源的访问权限，而无需共享帐户密钥。 共享访问签名提供了一种安全的方法来共享存储资源，而不会危及帐户密钥。

此部分中的命令完成了以下操作：

- 设置帐户共享访问签名定义。 
- 为 Blob、文件、表和队列服务创建帐户共享访问签名令牌。 为资源类型服务、容器和对象创建令牌。 令牌是通过 https 上的所有权限创建的，并且具有指定的开始日期和结束日期。
- 在保管库中设置 Key Vault 托管存储共享访问签名定义。 定义具有创建的共享访问签名令牌的模板 URI。 定义具有共享访问签名类型`account` ，且有效期为 N 天。
- 验证共享访问签名是否已作为机密保存在密钥保管库中。
- 
### <a name="set-variables"></a>设置变量

首先，在以下步骤中设置 PowerShell cmdlet 要使用的变量。 请确保更新<YourStorageAccountName>和<YourKeyVaultName>占位符。

我们还将使用 Azure PowerShell [AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlet 来获取 Azure 存储帐户的上下文。

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>创建共享访问签名令牌

使用 Azure PowerShell [AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlet 创建共享访问签名定义。
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$SasToken 的值将与此类似。

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>生成共享访问签名定义

使用 Azure PowerShell [AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet 来创建共享访问签名定义。  您可以为`-Name`参数提供您选择的名称。

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>验证共享访问签名定义

可以使用 Azure PowerShell [AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet 验证共享访问签名定义是否已存储在密钥保管库中。

首先，在密钥保管库中查找共享的访问签名定义。

```azurepowershell-interactive
Get-AzKeyVaultSecret -vault-name <YourKeyVaultName>
```

对应于 SAS 定义的机密将具有以下属性：

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

你现在可以使用[AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) cmdlet 和 secret `Name`属性来查看该机密的内容。

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

此命令的输出将显示 SAS 定义字符串。


## <a name="next-steps"></a>后续步骤

- [托管存储帐户密钥示例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [关于键、密钥和证书](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell 参考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
