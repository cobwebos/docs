---
title: Azure Key Vault 托管存储帐户 - PowerShell 版本
description: 托管存储帐户功能在 Azure Key Vault 与 Azure 存储帐户之间提供无缝集成。
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 530f38289afb3fce85bbb025e7b699862eedbadc
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382701"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault 托管存储帐户 - PowerShell

> [!NOTE]
> [与 Azure Active Directory (Azure AD) 集成的 Azure 存储目前以预览版提供](../storage/common/storage-auth-aad.md)。 我们建议使用 Azure AD 进行身份验证和授权。与 Azure Key Vault 一样，Azure AD 支持使用 OAuth2 令牌访问 Azure 存储。 这样，便可以：
> - 使用应用程序标识或用户标识（而不是存储帐户凭据）对客户端应用程序进行身份验证。 
> - 在 Azure 上运行时使用 [Azure AD 托管标识](/azure/active-directory/managed-identities-azure-resources/)。 托管标识完全消除了客户端身份验证的需要，并可以在应用程序中存储凭据，或者将凭据与应用程序一同存储。
> - 使用同样受 Key Vault 支持的基于角色的访问控制 (RBAC) 来管理授权。
> - 目前，AAD 访问存储帐户不能用于访问表。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure 存储帐户](/azure/storage/storage-create-storage-account)使用由帐户名和密钥构成的凭据。 密钥会自动生成，并主要充当“密码”而不是加密密钥。 Key Vault 可以通过将这些存储帐户密钥存储为 [Key Vault 机密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)来对其进行管理。 

## <a name="overview"></a>概述

Key Vault 托管存储帐户功能代你执行多种管理功能：

- 列出（同步）Azure 存储帐户的密钥。
- 定期重新生成（轮换）密钥。
- 管理存储帐户和经典存储帐户的密钥。
- 响应调用方时永远不会返回密钥值。

使用托管存储帐户密钥功能时：

- **只允许 Key Vault 管理存储帐户密钥。** 不要尝试自行管理这些密钥，否则会干扰 Key Vault 的流程。
- **不要允许多个 Key Vault 对象管理存储帐户密钥。**
- **不要手动重新生成存储帐户密钥。** 我们建议通过 Key Vault 重新生成这些密钥。

以下示例演示如何允许 Key Vault 管理存储帐户密钥。

## <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用[AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Cmdlet 对 PowerShell 会话进行身份验证。 
```azurepowershell-interactive
Connect-AzAccount
```
如果有多个 Azure 订阅，可以使用[AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet 列出这些订阅，并指定要与[AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet 一起使用的订阅。 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>授权 Key Vault 访问存储帐户

> [!IMPORTANT]
> Azure AD 租户为每个已注册的应用程序提供充当应用程序标识的[服务主体](/azure/active-directory/develop/developer-glossary#service-principal-object)。 通过基于角色的访问控制 (RBAC) 授权该服务主体访问其他 Azure 资源时，将使用它的应用程序 ID。 由于 Key Vault 是一个 Microsoft 应用程序，因此它已预先注册到每个 Azure 云内同一个应用程序 ID 下的所有 Azure AD 租户中：
> - Azure 政府云中的 Azure AD 租户使用应用程序 ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c`。
> - Azure 公有云及所有其他云中的 Azure AD 租户使用应用程序 ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`。

只有在授权 Key Vault 访问你的存储帐户之后，它才可以访问和管理存储帐户密钥。 Key Vault 应用程序标识需要有权列出和重新生成存储帐户的密钥。 可通过内置的 RBAC 角色[存储帐户密钥操作员服务角色](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)启用这些权限。 

使用以下步骤将此角色分配到 Key Vault 服务主体，以将范围限定为你的存储帐户。 运行该脚本之前，请务必更新 `$resourceGroupName`、`$storageAccountName`、`$storageAccountKey` 和 `$keyVaultName` 变量：

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Get your User Id for later commands
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

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

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>向托管存储帐户授予用户帐户权限

>[!TIP] 
> Azure AD 为应用程序的标识提供**服务主体**，同样，它也会为用户的标识提供**用户主体**。 然后，可以通过 Key Vault 访问策略权限授权用户主体访问 Key Vault。

使用同一 PowerShell 会话更新托管存储帐户的 Key Vault 访问策略。 此步骤将存储帐户权限应用到你的用户帐户，确保你可以访问托管存储帐户功能： 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

请注意，Azure 门户中存储帐户的“访问策略”页不会显示存储帐户的权限。

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>将托管存储帐户添加到 Key Vault 实例

使用同一 PowerShell 会话，在 Key Vault 实例中创建托管存储帐户。 `-DisableAutoRegenerateKey` 开关指定不重新生成存储帐户密钥。

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

如果希望 Key Vault 定期重新生成存储帐户密钥，可以设置重新生成周期。 以下示例将重新生成周期设置为 3 天。 三天后 Key Vault 将重新生成 "key2" 并将活动密钥从 "key2" 交换为 "key1"。

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

## <a name="next-steps"></a>后续步骤

- [托管存储帐户密钥示例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [关于键、密钥和证书](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell 参考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
