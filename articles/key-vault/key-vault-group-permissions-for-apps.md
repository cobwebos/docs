---
title: 向应用程序授予 Azure Key Vault 的访问权限 - Azure Key Vault | Microsoft Docs
description: 了解如何为多个应用程序授予密钥保管库的访问权限
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d5086377b0bb7f3ca2ece643f82a4e45156f1955
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184819"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>使用访问控制策略提供 Key Vault 身份验证

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在 Key Vault 中对基于云的应用程序进行身份验证的最简单方法是使用托管标识；有关详细信息，请参阅[使用应用服务托管标识访问 Azure Key Vault](managed-identity.md)。  创建本地应用程序、执行本地开发或者无法使用托管标识时，可以改为手动注册服务主体，并使用访问控制策略提供对 Key Vault 的访问权限。  

Key Vault 最多支持 1024 个访问策略条目，每个条目可向“主体”授予一组不同的权限： 例如，这是[适用于 .NET 的 Azure Key Vault 客户端库快速入门](quick-create-net.md)中的控制台应用访问 Key Vault 的方式。

有关 Key Vault 访问控制的完整详细信息，请参阅 [Azure Key Vault 安全性：标识和访问管理](overview-security.md#identity-and-access-management)。 有关[密钥、机密和证书](about-keys-secrets-and-certificates.md)访问控制的完整详细信息，请参阅： 

- [密钥访问控制](about-keys-secrets-and-certificates.md#key-access-control)
- [机密访问控制](about-keys-secrets-and-certificates.md#secret-access-control)
- [证书访问控制](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

- 一个 Key Vault。 可以使用现有的 Key Vault，或者遵循以下快速入门之一中的步骤创建一个新的 Key Vault：
   - [使用 Azure CLI 创建 Key Vault](quick-create-cli.md)
   - [使用 Azure PowerShell 创建 Key Vault](quick-create-powershell.md)
   - [使用 Azure 门户创建 Key Vault](quick-create-portal.md)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/overview)。 或者，可以使用 [Azure 门户](https://portal.azure.com)。

## <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

每个 Key Vault 访问策略条目向主体授予一组不同的权限：

- **应用程序**。如果应用程序基于云，则在可能的情况下，你应该改为[使用托管标识来访问 Azure Key Vault](managed-identity.md)
- **Azure AD 组**。尽管 Key Vault 仅支持 1024 个访问策略条目，但你可以将多个应用程序和用户添加到一个 Azure AD 组，然后将该组作为单个条目添加到访问控制策略。
- **用户**。**不建议**为用户授予对 Key Vault 的直接访问权限。 理想情况下，应将用户添加到 Azure AD 组，然后为该组授予对 Key Vault 的访问权限。 请参阅 [Azure Key Vault 安全性：标识和访问管理](overview-security.md#identity-and-access-management)。


### <a name="get-the-objectid"></a>获取 objectID

若要为应用程序、Azure AD 组或用户授予对 Key Vault 的访问权限，首先必须获取其 objectId。

#### <a name="applications"></a>应用程序

应用程序的 objectId 对应于其关联的服务主体。 有关服务主体的完整详细信息， 请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。 

可通过两种方式获取应用程序的 objectId。  第一种方式是将应用程序注册到 Azure Active Directory。 为此，请遵循快速入门[将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)中的步骤。 完成注册后，objectID 将作为“应用程序(客户端) ID”列出。

第二种方式是在终端窗口中创建服务主体。 在 Azure CLI 中使用 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令。

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

objectId 将在输出中作为 `clientID` 列出。

在 Azure PowerShell 中使用 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) cmdlet。


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

objectId 将在输出中作为 `Id`（而不是 `ApplicationId`）列出。

#### <a name="azure-ad-groups"></a>Azure AD 组

可将多个应用程序和用户添加到某个 Azure AD 组，然后为该组授予对 Key Vault 的访问权限。  有关更多详细信息，请参阅下面的[创建成员并将其添加到 Azure AD 组](#creating-and-adding-members-to-an-azure-ad-group)部分。

若要使用 Azure CLI 查找 Azure AD 组的 objectId，请使用 [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) 命令。 由于组织中可能存在大量的组，因此还应在 `--display-name` 参数中提供一个搜索字符串。

```azurecli-interactive
az ad group list --display-name <search-string>
```
objectId 将在 JSON 中返回：

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

若要使用 Azure PowerShell 查找 Azure AD 组的 objectId，请使用 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) cmdlet。 由于组织中可能存在大量的组，因此你可能需要在 `-SearchString` 参数中提供一个搜索字符串。

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

在输出中，objectId 作为 `Id` 列出：

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>用户

还可以将单个用户添加到 Key Vault 的访问控制策略。 **我们不建议这样做。** 我们建议将用户添加到某个 Azure AD 组，然后将该组添加到策略中。

如果你依然想要使用 Azure CLI 查找用户，请使用 [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) 命令，并将用户的电子邮件地址传递到 `--id` 参数。


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

用户的 objectId 将在输出中返回：

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

若要使用 Azure PowerShell 查找用户，请使用 [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) cmdlet，并将用户的电子邮件地址传递到 `-UserPrincipalName` 参数。

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

用户的 objectId 将在输出中作为 `Id` 返回：

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>为主体授予对 Key Vault 的访问权限

获取主体的 objectID 后，可为 Key Vault 创建一个访问策略，以便为该主体授予对密钥和机密的获取、列出、设置和删除权限，以及所需的任何其他权限。

在 Azure CLI 中，可以通过将 objectId 传递到 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令来实现此目的。

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

在 Azure PowerShell 中，可以通过将 objectId 传递到 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) cmdlet 来实现此目的。 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>创建成员并将其添加到 Azure AD 组

可以创建一个 Azure AD 组，将应用程序和用户添加到该组，然后为该组授予对 Key Vault 的访问权限。  这样，就可以将多个应用程序作为单个访问策略条目添加到 Key Vault，而无需为用户授予对 Key Vault 的直接访问权限（我们不建议这样做）。 有关更多详细信息，请参阅[使用 Azure Active Directory 组管理应用和资源访问](../active-directory/fundamentals/active-directory-manage-groups.md)。

### <a name="additional-prerequisites"></a>其他先决条件

除了[上述先决条件](#prerequisites)以外，还需要拥有在 Azure Active Directory 租户中创建/编辑组的权限。 如果没有权限，则可能需要与 Azure Active Directory 管理员联系。

若要使用 PowerShell，还需要安装 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>创建 Azure Active Directory 组

使用 Azure CLI [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) 命令或 Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) cmdlet 创建新的 Azure Active Directory 组。


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

无论使用哪种方式，都请记下新建组的 GroupId，因为在后面的步骤中需要用到。

### <a name="find-the-objectids-of-your-applications-and-users"></a>查找应用程序和用户的 objectId

可以在 Azure CLI 中结合 `--show-mine` 参数使用 [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 命令查找应用程序的 objectId。

```azurecli-interactive
az ad sp list --show-mine
```

在 Azure PowerShell 中，可以使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) cmdlet 并将搜索字符串传递到 `-SearchString` 参数来查找应用程序的 objectId。

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

若要查找用户的 objectId，请遵循前面[用户](#users)部分中的步骤。

### <a name="add-your-applications-and-users-to-the-group"></a>将应用程序和用户添加到组中

现在，请将 objectId 添加到新建的 Azure AD 组。

在 Azure CLI 中，请使用 [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add) 并将 objectId 传递到 `--member-id` 参数。


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

在 Azure PowerShell 中，请使用 [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) cmdlet，并将 objectId 传递到 `-MemberObjectId` 参数。

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>为 AD 组授予对 Key Vault 的访问权限

最后，使用 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令或 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) cmdlet，为 AD 组授予对 Key Vault 的权限。 有关示例，请参阅前面的[为应用程序、Azure AD 组或用户授予对 Key Vault 的访问权限](#give-the-principal-access-to-your-key-vault)。

应用程序还需要将至少一个标识和访问管理 (IAM) 角色分配给密钥保管库。 否则，它将无法登录并且会失败，因为没有足够权限来访问订阅。

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全性：标识和访问管理](overview-security.md#identity-and-access-management)
- [使用应用服务托管标识提供 Key Vault 身份验证](managed-identity.md)
- [关于键、密钥和证书](about-keys-secrets-and-certificates.md)
- [保护 Key Vault](key-vault-secure-your-key-vault.md)
- [Azure Key Vault 开发人员指南](key-vault-developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](key-vault-best-practices.md)
