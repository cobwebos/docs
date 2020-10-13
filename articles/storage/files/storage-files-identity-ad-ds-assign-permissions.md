---
title: 控制对 Azure 文件共享的访问-本地 AD DS 身份验证
description: 了解如何为表示你的存储帐户的 Active Directory 域服务标识分配权限。 这使你可以使用基于标识的身份验证来控制访问权限。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91716033"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>第二部分：向标识分配共享级权限

在开始本文之前，请确保已完成上一篇文章， [为你的帐户启用 AD DS 身份验证](storage-files-identity-ad-ds-enable.md)。

在存储帐户上启用 Active Directory 域服务 (AD DS) 身份验证后，必须配置共享级权限，才能访问文件共享。 要访问 Azure 文件共享资源的标识必须是同时存在于 AD DS 和 Azure AD 中的混合标识。 例如，假设你在 AD DS 中有一个用户，并且已 user1@onprem.contoso.com user1@contoso.com 使用 Azure AD Connect sync 同步到 Azure AD。若要允许此用户访问 Azure 文件，必须为分配共享级权限 user1@contoso.com 。 相同的概念适用于组或服务主体。 因此，你必须将 AD DS 中的用户和组同步到使用 Azure AD Connect 同步 Azure AD。 

共享级权限必须分配给表示 AD DS 中相同用户或组的 Azure AD 标识，以支持向 Azure 文件共享 AD DS 身份验证。 AD DS 身份验证不支持对仅存在于 Azure AD 中的身份进行身份验证和授权，例如 Azure 托管标识 (Msi) 。 本文演示如何将文件共享的共享级别权限分配给某个标识。


## <a name="share-level-permissions"></a>共享级权限

通常，我们建议使用 "高级访问管理" 的 "共享级别" 权限来管理代表一组用户和标识的 Azure AD 组，然后利用 Windows Acl 对目录/文件级别进行精细的访问控制。 

有三种 Azure 内置角色用于向用户授予共享级权限：

- **存储文件数据 Smb 共享读取器** 允许通过 Smb 在 Azure 存储文件共享中进行读取访问。
- **存储文件数据 SMB 共享参与者** 允许通过 SMB 在 Azure 存储文件共享中进行读取、写入和删除访问。
- **存储文件数据 SMB 共享提升的参与者** 允许通过 SMB 在 Azure 存储文件共享中读取、写入、删除和修改 Windows acl。

> [!IMPORTANT]
> 对文件共享的完全管理控制（包括获取文件所有权的能力）需要使用存储帐户密钥。 Azure AD 凭据不支持管理控制。

您可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 将内置角色分配到用户的 Azure AD 标识，以便授予共享级别权限。

## <a name="assign-an-azure-role"></a>分配 Azure 角色

### <a name="azure-portal"></a>Azure 门户

若要将 Azure 角色分配到 Azure AD 标识，请使用 [Azure 门户](https://portal.azure.com)，请执行以下步骤：

1. 在 Azure 门户中，请切换到文件共享，或 [创建文件共享](storage-how-to-create-file-share.md)。
1. 选择“访问控制 (IAM)”。
1. 选择 "**添加角色分配**"
1. 在 " **添加角色分配** " 边栏选项卡中，从 " **角色** " 列表中选择适当的内置角色 (存储文件数据 smb 共享读取器、存储文件数据 smb 共享参与者) 。 将 " **分配访问权限** " 设置为默认设置： **Azure AD 用户、组或服务主体**。 按名称或电子邮件地址选择目标 Azure AD 标识。 **所选 Azure AD 标识必须是混合标识，不能是仅限云的标识。** 这意味着同一标识也会在 AD DS 中表示。
1. 选择 " **保存** " 以完成角色分配操作。

### <a name="powershell"></a>PowerShell

以下 PowerShell 示例演示了如何基于登录名将 Azure 角色分配到 Azure AD 标识。 有关使用 PowerShell 分配 Azure 角色的详细信息，请参阅 [使用 Azure PowerShell 模块添加或删除 azure 角色分配](../../role-based-access-control/role-assignments-powershell.md)。

在运行下面的示例脚本之前，请将占位符值（包括括号）替换为你的值。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
以下 CLI 2.0 命令将基于登录名将 Azure 角色分配到 Azure AD 标识。 有关将 Azure 角色分配到 Azure CLI 的详细信息，请参阅 [使用 Azure CLI 添加或删除 azure 角色分配](../../role-based-access-control/role-assignments-cli.md)。 

在运行以下示例脚本之前，请记得将占位符值（包括括号）替换为自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>后续步骤

现在，已分配共享级权限，您必须配置目录和文件级权限。 转到下一篇文章。

[第三部分：通过 SMB 配置目录和文件级权限](storage-files-identity-ad-ds-configure-permissions.md)
