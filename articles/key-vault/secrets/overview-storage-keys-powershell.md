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
ms.openlocfilehash: 454420d9b2f4e3cf834490da79f3571691f25bc1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121110"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>使用 Key Vault 和 Azure PowerShell 管理存储帐户密钥

Azure 存储帐户使用由帐户名和密钥构成的凭据。 密钥是自动生成的，充当密码而不是加密密钥。 Key Vault 可以通过将存储帐户密钥存储为 Key Vault 机密来对其进行管理。 

可以使用 Key Vault 托管的存储帐户密钥功能列出（同步） Azure 存储帐户中的密钥，并定期重新生成（轮换）密钥。 可以管理存储帐户和经典存储帐户的密钥。

使用托管的存储帐户密钥功能时，请注意以下要点：

- 响应调用方时永远不会返回密钥值。
- 只有 Key Vault 能够管理存储帐户密钥。 不要自行管理密钥，并避免干扰 Key Vault 进程。
- 只有单个 Key Vault 对象能够管理存储帐户密钥。 不要允许从多个对象进行密钥管理。
- 可以请求 Key Vault 使用用户主体（而不要使用服务主体）管理存储帐户。
- 只使用 Key Vault 重新生成密钥。 不要手动重新生成存储帐户密钥。

我们建议使用 Azure 存储与 Azure Active Directory (Azure AD) 的集成，这是 Microsoft 推出的基于云的标识和访问管理服务。 Azure AD 集成适用于 [Azure Blob 和队列](../../storage/common/storage-auth-aad.md)，提供对 Azure 存储的基于 OAuth2 令牌的访问（类似于 Azure Key Vault）。

Azure AD 允许使用应用程序标识或用户标识（而不是存储帐户凭据）对客户端应用程序进行身份验证。 在 Azure 上运行时，可以使用 [Azure AD 托管标识](/azure/active-directory/managed-identities-azure-resources/)。 托管标识消除了客户端身份验证的需要，并可以在应用程序中存储凭据，或者将凭据与应用程序一同存储。

Azure AD 使用同样受 Key Vault 支持的基于角色的访问控制 (RBAC) 来管理授权。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>服务主体应用程序 ID

Azure AD 租户为每个已注册的应用程序提供[服务主体](/azure/active-directory/develop/developer-glossary#service-principal-object)。 该服务主体充当应用程序 ID，通过 RBAC 设置访问其他 Azure 资源的授权期间，将使用它。

Key Vault 是已在所有 Azure AD 租户中预先注册的 Microsoft 应用程序。 Key Vault 注册到每个 Azure 云中的同一个应用程序 ID 下。

| 租户 | 云 | 应用程序 ID |
| --- | --- | --- |
| Azure AD | Azure Government  | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公有云 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>先决条件

若要完成本指南，必须先执行以下操作：

- [安装 Azure PowerShell 模块](/powershell/azure/install-az-ps?view=azps-2.6.0)。
- [创建密钥保管库](quick-create-powershell.md)
- [创建 Azure 存储帐户](../../storage/common/storage-account-create.md?tabs=azure-powershell)。 存储帐户名必须仅使用小写字母和数字。 名称的长度必须为 3 到 24 个字符。
      

## <a name="manage-storage-account-keys"></a>管理存储帐户密钥

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet 对 PowerShell 会话进行身份验证。 

```azurepowershell-interactive
Connect-AzAccount
```
如果你有多个 Azure 订阅，可以使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet 列出这些订阅，并指定要与 [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet 结合使用的订阅。 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>设置变量

首先，通过以下步骤设置 PowerShell cmdlet 使用的变量。 请务必更新 <YourResourceGroupName>、<YourStorageAccountName> 和 <YourKeyVaultName> 占位符，并将 $keyVaultSpAppId 设置为 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`（在上面的[服务主体应用程序 ID](#service-principal-application-id) 中指定）。

我们还将使用 Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) 和 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) cmdlet 来获取你的用户 ID，以及 Azure 存储帐户的上下文。

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> 对于经典存储帐户，请使用 "主要" 和 "辅助" 作为 $storageAccountKey <br>
> 对于经典存储帐户，请使用 "AzResource-Name" ClassicStorageAccountName "-ResourceGroupName $resourceGroupName" 改为 "of'Get-AzStorageAccount"

### <a name="give-key-vault-access-to-your-storage-account"></a>向 Key Vault 授予对你的存储帐户的访问权限

只有在授权 Key Vault 访问你的存储帐户之后，它才可以访问和管理存储帐户密钥。 Key Vault 应用程序标识需要有权列出  和重新生成  存储帐户的密钥。 可通过内置的 RBAC 角色[存储帐户密钥操作员服务角色](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)启用这些权限。 

使用 Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet 将此角色分配到 Key Vault 服务主体，以将范围限定为你的存储帐户。

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

如果 Key Vault 已添加到存储帐户中的角色，则你会收到“角色分配已存在”  ”错误。 还可以使用 Azure 门户中存储帐户的“访问控制(IAM)”页验证角色分配。  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>向托管存储帐户授予用户帐户权限

使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet 更新 Key Vault 访问策略，并向用户帐户授予存储帐户权限。

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

请注意，Azure 门户中存储帐户的“访问策略”页不会显示存储帐户的权限。

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>将托管存储帐户添加到 Key Vault 实例

使用 Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet 在 Key Vault 实例中创建托管存储帐户。 `-DisableAutoRegenerateKey` 开关指定不重新生成存储帐户密钥。

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

如果希望 Key Vault 定期重新生成存储帐户密钥，可以使用 Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet 设置重新生成周期。 此示例将重新生成周期设置为 3 天。 三天后 Key Vault 将重新生成 "key2"，并将活动密钥从 "key2" 交换为 "key1" （替换为经典存储帐户的 "primary" 和 "辅助"）。

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

也可以要求 Key Vault 生成共享访问签名令牌。 共享访问签名对存储帐户中的资源提供委托访问。 可以授予客户端访问存储帐户中的资源的权限，而无需共享帐户密钥。 使用共享访问签名可以安全共享存储资源，而不会透露帐户密钥。

本部分所述的命令将完成以下操作：

- 设置帐户共享访问签名定义。 
- 为 Blob、文件、表和队列服务创建帐户共享访问签名令牌。 为“服务”、“容器”和“对象”资源类型创建令牌。 创建的令牌拥有所有权限、通过 HTTPS 访问并指定了开始和结束日期。
- 在保管库中设置 Key Vault 托管的存储共享访问签名定义。 该定义包含创建的共享访问签名令牌的模板 URI。 该定义使用共享访问签名类型 `account`，有效期为 N 天。
- 验证共享访问签名是否已作为机密保存在 Key Vault 中。
- 
### <a name="set-variables"></a>设置变量

首先，通过以下步骤设置 PowerShell cmdlet 使用的变量。 请务必更新 <YourStorageAccountName> 和 <YourKeyVaultName> 占位符。

我们还将使用 Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlet 来获取 Azure 存储帐户的上下文。

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>创建共享访问签名令牌

使用 Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlet 创建共享访问签名定义。
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$sasToken 的值如下所示。

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>生成共享访问签名定义

使用 Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet 创建共享访问签名定义。  可将所选的名称提供给 `-Name` 参数。

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>验证共享访问签名定义

可以使用 Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet 验证共享访问签名定义是否已存储在 Key Vault 中。

首先，在 Key Vault 中找到共享访问签名定义。

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

对应于 SAS 定义的机密包含以下属性：

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

现在，可以使用 [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) cmdlet 和机密 `Name` 属性查看该机密的内容。

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

此命令的输出将显示 SAS 定义字符串。


## <a name="next-steps"></a>后续步骤

- [托管存储帐户密钥示例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell 参考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
