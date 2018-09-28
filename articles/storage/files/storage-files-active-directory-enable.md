---
title: 通过 SMB 为 Azure 文件启用 Azure Active Directory 身份验证（预览）- Azure 存储
description: 了解如何借助 Azure Active Directory (Azure AD) 域服务，通过 SMB（服务器消息块）为 Azure 文件启用基于标识的身份验证（预览）。 然后，加入域的 Windows 虚拟机 (VM) 可以使用 Azure AD 凭据访问 Azure 文件共享。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 31bfcd99e23cc7fe0575a293e3dc22507dbd9e13
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973263"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>通过 SMB 为 Azure 文件启用 Azure Active Directory 身份验证（预览）

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

有关通过 SMB 为 Azure 文件启用 Azure AD 身份验证的概述，请参阅[通过 SMB 为 Azure 文件启用 Azure Active Directory身份验证（预览）概述](storage-files-active-directory-overview.md)。

## <a name="workflow-overview"></a>工作流概述

通过 SMB 为 Azure 文件启用 Azure AD 之前，请确认已正确配置 Azure AD 和 Azure 存储环境。 建议逐步完成[先决条件](#prerequisites)，确保已执行所有必需步骤。 

接下来，按照以下步骤授予使用 Azure AD 凭据访问 Azure 文件资源的权限： 

1. 通过 SMB 为存储帐户启用 Azure AD 身份验证，使用关联的 Azure AD 域服务部署注册存储帐户。
2. 将共享的访问权限分配给 Azure AD 标识（用户、组或服务主体）。
3. 通过 SMB 为目录和文件配置 NTFS 权限。
4. 从加入域的 VM 装载 Azure 文件共享。

下图说明了通过 SMB 为 Azure 文件启用 Azure AD 身份验证的端到端工作流。 

![显示通过 SMB 为 Azure 文件启用 Azure AD 的工作流的图表](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>先决条件 

1.  **选择或创建 Azure AD 租户。**

    可以使用新的或现有的租户进行通过 SMB 的 Azure AD 身份验证。 要访问的租户和文件共享必须与同一订阅相关联。

    若要创建一个新的 Azure AD 租户，可以[添加 Azure AD 租户和 Azure AD 订阅](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果已有一个 Azure AD 租户，但想要使用 Azure 文件创建新租户进行使用，请参阅[创建 Azure Active Directory 租户](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

2.  **启用 Azure AD 租户上的 Azure AD 域服务。**

    若要支持使用 Azure AD 凭据进行身份验证，必须启用 Azure AD 租户的 Azure AD 域服务。 如果你不是 Azure AD 租户的管理员，请与管理员联系并按照分步指南操作，以[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/active-directory-ds-getting-started.md)。

    Azure AD 域服务部署通常需要大约 15 分钟才能完成。 在继续执行下一步之前，请验证 Azure AD 域服务的运行状况状态是否显示“正在运行”，以及是否启用了密码哈希同步。

3.  **使用 Azure AD 域服务将 Azure VM 加入域。**

    要使用来自 VM 的 Azure AD 凭据访问文件共享，你的 VM 必须已通过 Azure AD 域服务加入域。 有关如何将 VM 加入域的详细信息，请参阅[将 Windows Server 虚拟机加入托管域](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md)。

    > [!NOTE]
    > 只有在运行在高于 Windows 7 或 Windows Server 2008 R2 版本的 OS 上的 Azure VM 上才支持通过 SMB 为 Azure 文件启用 Azure AD 身份验证。

4.  **选择或创建 Azure 文件共享。**

    选择与 Azure AD 租户相同订阅关联的新的或现有文件共享。 有关创建新的文件共享的信息，请参阅[在 Azure 文件中创建文件共享](storage-how-to-create-file-share.md)。 

    必须将 Azure AD 租户部署到支持通过 SMB 预览 Azure AD 的区域。 预览版适用于除以下区域之外的所有其他公共区域：美国西部、美国西部 2、美国中南部、美国东部、美国东部 2、美国中部、美国中北部、澳大利亚东部、西欧、北欧。

    为获得最佳性能，Microsoft 建议将文件共享与计划访问共享的 VM 放置在同一区域。

5.  **通过使用存储帐户密钥装载 Azure 文件共享来验证 Azure 文件连接。**

    要验证是否已正确配置 VM 和文件共享，请尝试使用存储帐户密钥装载文件共享。 有关详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问](storage-how-to-use-files-windows.md)。

## <a name="enable-azure-ad-authentication"></a>启用 Azure AD 身份验证

完成[先决条件](#prerequisites)后，就可以通过 SMB 启用 Azure AD 身份验证了。

### <a name="step-1-enable-azure-ad-authentication-over-smb-for-your-storage-account"></a>步骤 1：通过 SMB 为存储帐户启用 Azure AD 身份验证

要通过 SMB 为 Azure 文件启用 Azure AD 身份验证，可以使用 PowerShell 或 Azure CLI 中的 Azure 存储资源提供程序为 2018 年 8 月 29 日之后创建的存储帐户设置属性。 预览版不支持在 Azure 门户中设置属性。 

设置此属性会将存储帐户注册到关联的 Azure AD 域服务部署。 然后，为存储帐户中的所有新文件和现有文件共享启用通过 SMB 的 Azure AD 身份验证。 

请注意，只有在将 Azure AD 域服务成功部署到 Azure AD 租户后，才能通过 SMB 启用 Azure AD 身份验证。 有关详细信息，请参阅[先决条件](#prerequisites)。

**Powershell**

若要通过 SMB 启用 Azure AD 身份验证，请安装 `AzureRM.Storage 6.0.0-preview` PowerShell 模块。 有关如何安装 PowerShell 的详细信息，请参阅[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。

接下来，调用 [Set-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount) 并将 EnableAzureFilesAadIntegrationForSMB 参数设置为 true。 在下面的示例中，请务必将占位符值替换为你自己的值。

```powershell
# Create a new storage account
New-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
Set-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

**CLI**

“需要先添加存储预览扩展，然后按照下面的示例操作。 请务必将占位符值替换为你自己的值。 请参阅本文，详细了解如何添加 Azure CLI 扩展。”

若要通过 SMB 从 Azure CLI 2.0 启用 Azure AD 身份验证，请先安装存储预览扩展：

```azurecli-interactive
az extension add --name storage-preview
```

接下来，调用 [az 存储帐户更新](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)并将 `--file-aad` 属性设置为 true。 在下面的示例中，请务必将占位符值替换为你自己的值。

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

### <a name="step-2-assign-access-permissions-to-an-identity"></a>步骤 2：为标识分配访问权限 

要使用 Azure AD 凭据访问 Azure 文件资源，标识（用户、组或服务主体）必须具有共享级别的必要权限。 下面的分步指导演示了如何将文件共享的读取、写入或删除权限分配给标识。

> [!IMPORTANT]
> 对文件共享的完全管理控制（包括将角色分配给标识的控制权限）需要使用存储帐户密钥。 Azure AD 凭据不支持管理控制。 

#### <a name="step-21-define-a-custom-role"></a>步骤 2.1：定义自定义角色

要授予共享级别权限，请定义自定义 RBAC 角色并将其分配给标识，同时将其范围限定为特定文件共享。 此过程类似于指定 Windows 共享权限，可以在其中指定给定用户对文件共享的访问类型。  

以下各节中显示的模板为文件共享提供“读取”或“更改”权限。 要定义自定义角色，请创建一个 JSON 文件并将相应的模板复制到该文件。 有关定义自定义 RBAC 角色的详细信息，请参阅[在 Azure 中自定义角色](../../role-based-access-control/custom-roles.md)。

**共享级别更改权限的角色定义**

以下自定义角色模板提供共享级别更改权限，向标识授予对共享的读取、写入和删除访问权限。

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share",
  "Actions": [
    "*"
  ],
  "NotActions": [
      "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**共享级别读取权限的角色定义**

以下自定义角色模板提供共享级别读取权限，向标识授予对共享的读取访问权限。

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

#### <a name="step-22-create-the-custom-role-and-assign-it-to-the-target-identity"></a>步骤 2.2：创建自定义角色，并将其分配给目标标识

接下来，使用 PowerShell 或 Azure CLI 创建角色，并将其分配给 Azure AD 标识。 

**Powershell**

若要通过 SMB 启用 Azure AD 身份验证，请安装 `AzureRM.Storage 6.0.0-preview` PowerShell 模块。 有关如何安装 PowerShell 的详细信息，请参阅[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。

以下 PowerShell 命令创建自定义角色，并根据登录名将角色分配给 Azure AD 标识。 有关如何使用 PowerShell 分配 RBAC 角色的详细信息，请参阅[使用 RBAC 和 Azure PowerShell 管理访问权限](../../role-based-access-control/role-assignments-powershell.md)。

运行以下示例脚本时，请务必将占位符值替换为你自己的值。

```powershell
#Create a custom role based on the sample template above
New-AzureRmRoleDefinition -InputFile "<custom-role-def-json-path>"
#Get the name of the custom role
$FileShareContributorRole = Get-AzureRmRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzureRmRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

**CLI**

以下 CLI 2.0 命令创建自定义角色，并根据登录名将角色分配给 Azure AD 标识。 有关如何使用 Azure CLI 分配 RBAC 角色的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](../../role-based-access-control/role-assignments-cli.md)。 

运行以下示例脚本时，请务必将占位符值替换为你自己的值。

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

### <a name="step-3-configure-ntfs-permissions-over-smb"></a>步骤 3：通过 SMB 配置 NTFS 权限 

使用 RBAC 分配共享级别权限后，必须在根目录、目录或文件级别分配正确的 NTFS 权限。 将共享级别权限视为确定用户是否可以访问共享的高级网关守卫，而 NTFS 权限则更具体地执行操作，确定用户可以在目录或文件级别执行的操作。 

Azure 文件支持全套 NTFS 基本和高级权限。 可以通过装载共享，然后运行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 或 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 命令，在 Azure 文件共享中查看和配置目录和文件的 NTFS 权限。 

> [!NOTE]
> 预览版仅支持使用 Windows 文件资源管理器查看权限。 尚不支持编辑权限。

要使用超级用户权限配置 NTFS 权限，必须使用存储帐户密钥从已加入域的 VM 装载共享。 请按照下一节中的说明操作，从命令提示符装载 Azure 文件共享，并相应地配置 NTFS 权限。

文件共享的根目录支持以下权限集：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

#### <a name="step-31-mount-an-azure-file-share-from-the-command-prompt"></a>步骤 3.1：从命令提示符装载 Azure 文件共享

使用 Windows net use 命令装载 Azure 文件共享。 请务必将示例中的占位符值替换为你自己的值。 有关装载文件共享的详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问](storage-how-to-use-files-windows.md)。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

#### <a name="step-32-configure-ntfs-permissions-with-icacls"></a>步骤 3.2：使用 icacls 配置 NTFS 权限

使用以下 Windows 命令为文件共享（包括根目录）下的所有目录和文件授予完全权限。 请务必将示例中的占位符值替换为你自己的值。

```
icacls <mounted-drive-letter> /grant <user-email>:(f)
```

若要详细了解如何使用 icacls 设置 NTFS 权限，以及受支持的不同类型的权限，请参阅 [icacls 的命令行参考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

### <a name="step-4-mount-an-azure-file-share-from-a-domain-joined-vm"></a>步骤 4：从已加入域的 VM 装载 Azure 文件共享 

现在，你已准备好使用 Azure AD 凭据从已加入域的 VM 访问 Azure 文件共享，从而验证是否已成功完成上述步骤。 首先，使用已授予权限的 Azure AD 标识登录 VM，如下图所示。

![显示用户身份验证的 Azure AD 登录屏幕的屏幕截图](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

接下来，使用以下命令装载 Azure 文件共享。 请务必将占位符值替换为你自己的值。 由于你已通过身份验证，因此，无需提供存储帐户密钥或 Azure AD 用户名及密码。 通过 SMB 的 Azure AD 支持使用 Azure AD 凭据的单一登录体验。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

现在，你已经成功地通过 SMB 启用了 Azure AD 身份验证，并分配了一个自定义角色，该角色可以访问到 Azure AD 标识的文件共享。 若要向其他用户授予对文件共享的访问权限，请按照步骤 2 中提供的说明操作。

## <a name="next-steps"></a>后续步骤

有关 Azure 文件以及通过 SMB 使用 Azure AD 的详细信息，请参阅以下资源：

- [Azure 文件简介](storage-files-introduction.md)
- [通过 SMB 为 Azure 文件启用 Azure Active Directory 身份验证（预览）概述](storage-files-active-directory-overview.md)
- [常见问题](storage-files-faq.md)