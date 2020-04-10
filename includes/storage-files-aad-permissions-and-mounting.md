---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: c88f5a4dd4f2997ce01b1f6a3ae192c62f530e76
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011408"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. 向标识分配访问权限

要使用基于标识的身份验证访问 Azure 文件资源，标识（用户、组或服务主体）必须在共享级别具有必要的权限。 此过程类似于指定 Windows 共享权限，其中指定特定用户对文件共享的访问类型。 本部分中的指导演示如何将文件共享的读取、写入或删除权限分配给标识。 

我们引入了三个 Azure 内置角色，用于向用户授予共享级权限：

- **存储文件 SMB 共享读取器**允许通过 SMB 在 Azure 存储文件共享中读取访问。
- **存储文件 SMB 共享参与者**允许通过 SMB 在 Azure 存储文件共享中读取、写入和删除访问。
- **存储文件 SMB 共享提升参与者**允许通过 SMB 读取、写入、删除和修改 Azure 存储文件共享中的 NTFS 权限。

> [!IMPORTANT]
> 文件共享的完全管理控制（包括获取文件所有权的能力）需要使用存储帐户密钥。 Azure AD 凭据不支持管理控制。

可以使用 Azure 门户、PowerShell 或 Azure CLI 将内置角色分配给用户的 Azure AD 标识，以便授予共享级别权限。

> [!NOTE]
> 如果计划使用 AD 进行身份验证，请记住将 AD 凭据同步到 Azure AD。 从 AD 到 Azure AD 的密码哈希同步是可选的。 共享级别权限将授予从 AD 同步的 Azure AD 标识。

一般建议是，对表示一组用户和身份的 AD 组使用高级别访问管理共享级别权限，然后利用 NTFS 权限在目录/文件级别进行精细访问控制。 

#### <a name="azure-portal"></a>Azure 门户
要将 RBAC 角色分配给 Azure AD 标识，请使用[Azure 门户](https://portal.azure.com)，请按照以下步骤操作：

1. 在 Azure 门户中，转到文件共享，或[创建文件共享](../articles/storage/files/storage-how-to-create-file-share.md)。
2. 选择**访问控制 （IAM）。**
3. 选择 **"添加角色分配"**
4. 在 **"添加角色分配**"边栏选项卡中，从**角色**列表中选择适当的内置角色（存储文件数据 SMB 共享读取器、存储文件数据 SMB 共享参与者）。 在默认设置下保留**对的"分配"访问权限****：Azure AD 用户、组或服务主体**。 按名称或电子邮件地址选择目标 Azure AD 标识。
5. 选择 **"保存"** 以完成角色分配操作。

#### <a name="powershell"></a>PowerShell

下面的 PowerShell 示例演示如何根据登录名称将 RBAC 角色分配给 Azure AD 标识。 有关如何使用 PowerShell 分配 RBAC 角色的详细信息，请参阅[使用 RBAC 和 Azure PowerShell 管理访问权限](../articles/role-based-access-control/role-assignments-powershell.md)。

在运行以下示例脚本之前，请记住将占位符值（包括括号）替换为您自己的值。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
以下 CLI 2.0 命令演示如何根据登录名称将 RBAC 角色分配给 Azure AD 标识。 有关使用 Azure CLI 分配 RBAC 角色的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问](../articles/role-based-access-control/role-assignments-cli.md)。 

在运行以下示例脚本之前，请记住将占位符值（包括括号）替换为您自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. 通过 SMB 配置 NTFS 权限 
使用 RBAC 分配共享级别权限后，必须在根目录、目录或文件级别分配正确的 NTFS 权限。 将共享级别权限视为高级网守，用于确定用户是否可以访问共享。 而 NTFS 权限在更精细的级别执行，以确定用户可以在目录或文件级别执行哪些操作。

Azure 文件支持全套 NTFS 基本和高级权限。 您可以通过安装共享，然后使用 Windows 文件资源管理器或运行 Windows [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl)命令来查看和配置 Azure 文件共享中的目录和文件的 NTFS 权限。 

要配置具有超级用户权限的 NTFS，您必须使用域加入的 VM 中的存储帐户密钥来装载共享。 按照下一节中的说明从命令提示符装载 Azure 文件共享，并相应地配置 NTFS 权限。

文件共享的根目录支持以下权限集：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 配置 NTFS 权限
使用以下 Windows 命令为文件共享（包括根目录）下的所有目录和文件授予完全权限。 请务必将示例中的占位符值替换为你自己的值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

有关如何使用 Icacls 设置 NTFS 权限以及不同类型的受支持权限的详细信息，请参阅[icacls 的命令行引用](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

### <a name="mount-a-file-share-from-the-command-prompt"></a>从命令提示符装载文件共享

使用 Windows net use**** 命令装载 Azure 文件共享。 请记住，将以下示例中的占位符值替换为您自己的值。 有关安装文件共享的详细信息，请参阅使用[Windows 文件共享](../articles/storage/files/storage-how-to-use-files-windows.md)。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>使用 Windows 文件资源管理器配置 NTFS 权限
使用 Windows 文件资源管理器向文件共享下的所有目录和文件（包括根目录）授予完全权限。

1. 打开 Windows 文件资源管理器并右键单击文件/目录并选择**属性**
2. 单击"**安全**"选项卡
3. 单击**编辑.**. .按钮以更改权限
4. 您可以更改现有用户的权限，或单击"**添加..."** 向新用户授予权限
5. 在添加新用户的提示窗口中，在 **"输入要选择对象名称**"框中输入要授予权限的目标用户名，然后单击 **"选中名称"** 以查找目标用户的完整 UPN 名称。
7.  单击 **"确定"**
8.  在"安全"选项卡中，选择要授予新添加的用户的所有权限
9.  单击“应用”****

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. 从加入域的 VM 装载文件共享

以下过程验证文件共享和访问权限设置是否正确，以及可以从加入域的 VM 访问 Azure 文件共享。 请注意，共享级别 RBAC 角色分配可能需要一些时间才能生效。 

使用已授予权限的 Azure AD 标识登录到 VM，如下图所示。 如果已为 Azure 文件启用 AD 身份验证，请使用 AD 凭据。 对于 Azure AD DS 身份验证，请使用 Azure AD 凭据登录。

![显示用户身份验证的 Azure AD 登录屏幕的屏幕截图](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

使用以下命令装载 Azure 文件共享。 请务必将占位符值替换为你自己的值。 由于已过身份验证，因此无需提供存储帐户密钥、AD 凭据或 Azure AD 凭据。 支持使用 AD 或 Azure AD DS 进行身份验证的单一登录体验。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
