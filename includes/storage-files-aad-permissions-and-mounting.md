---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: a168b9f721cd9c3d4ab0e8b6a56b764fec3b1fe3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779060"
---
## <a name="assign-access-permissions-to-an-identity"></a>为标识分配访问权限

若要使用基于身份的身份验证访问 Azure 文件资源， (用户、组或服务主体) 的标识必须在共享级别具有必要的权限。 此过程类似于指定 Windows 共享权限，你可以在其中指定特定用户对文件共享的访问类型。 本部分中的指导演示如何将文件共享的读取、写入或删除权限分配给标识。 

我们引入了三个 Azure 内置角色用于向用户授予共享级权限：

- **存储文件数据 Smb 共享读取器** 允许通过 Smb 在 Azure 存储文件共享中进行读取访问。
- **存储文件数据 SMB 共享参与者** 允许通过 SMB 在 Azure 存储文件共享中进行读取、写入和删除访问。
- **存储文件数据 SMB 共享提升的参与者** 允许通过 SMB 在 Azure 存储文件共享中读取、写入、删除和修改 NTFS 权限。

> [!IMPORTANT]
> 对文件共享的完全管理控制（包括获取文件所有权的能力）需要使用存储帐户密钥。 Azure AD 凭据不支持管理控制。

你可以使用 Azure 门户、PowerShell 或 Azure CLI 将内置角色分配给用户的 Azure AD 标识，以便授予共享级别权限。 请注意，共享级别 Azure 角色分配可能需要一些时间才能生效。 

> [!NOTE]
> 如果计划使用本地 AD DS 进行身份验证，请记住将 [AD DS 凭据同步到 Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) 。 从 AD DS 到 Azure AD 的密码哈希同步是可选的。 将向从本地 AD DS 同步的 Azure AD 标识授予共享级权限。

一般的建议是使用对 AD 组（代表一组用户和标识）的高级访问权限管理的共享级别权限，然后利用 NTFS 权限对目录/文件级别上的粒度访问控制。 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>将 Azure 角色分配给 AD 标识

# <a name="portal"></a>[门户](#tab/azure-portal)
若要将 Azure 角色分配到 Azure AD 标识，请使用 [Azure 门户](https://portal.azure.com)，请执行以下步骤：

1. 在 Azure 门户中，请切换到文件共享，或 [创建文件共享](../articles/storage/files/storage-how-to-create-file-share.md)。
2. 选择“访问控制 (IAM)”。
3. 选择 "**添加角色分配**"
4. 在 " **添加角色分配** " 边栏选项卡中，从 " **角色** " 列表中选择适当的内置角色 (存储文件数据 smb 共享读取器、存储文件数据 smb 共享参与者) 。 将 " **分配访问权限** " 设置为默认设置： **Azure AD 用户、组或服务主体**。 按名称或电子邮件地址选择目标 Azure AD 标识。
5. 选择 " **保存** " 以完成角色分配操作。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

以下 PowerShell 示例演示了如何基于登录名将 Azure 角色分配到 Azure AD 标识。 有关使用 PowerShell 分配 Azure 角色的详细信息，请参阅 [使用 RBAC 和 Azure PowerShell 管理访问权限](../articles/role-based-access-control/role-assignments-powershell.md)。

在运行以下示例脚本之前，请记得将占位符值（包括括号）替换为自己的值。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
以下 CLI 2.0 命令显示了如何基于登录名将 Azure 角色分配到 Azure AD 标识。 有关将 Azure 角色分配到 Azure CLI 的详细信息，请参阅 [使用 RBAC 和 Azure CLI 管理访问权限](../articles/role-based-access-control/role-assignments-cli.md)。 

在运行以下示例脚本之前，请记得将占位符值（包括括号）替换为自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>通过 SMB 配置 NTFS 权限

使用 RBAC 分配共享级别权限后，必须在根目录、目录或文件级别分配正确的 NTFS 权限。 将共享级权限视为用于确定用户是否可以访问共享的高级身份确认程序。 NTFS 权限的作用更精细，以确定用户可以在目录或文件级别执行哪些操作。

Azure 文件支持全套 NTFS 基本和高级权限。 你可以通过装入并使用 Windows 文件资源管理器或运行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 或 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) 命令来查看和配置 Azure 文件共享中的目录和文件的 NTFS 权限。 

若要使用超级用户权限配置 NTFS，必须使用已加入域的 VM 中的存储帐户密钥装载共享。 按照下一部分中的说明从命令提示符装载 Azure 文件共享，并相应地配置 NTFS 权限。

文件共享的根目录支持以下权限集：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>从命令提示符装载文件共享

使用 Windows net use**** 命令装载 Azure 文件共享。 请记住，将以下示例中的占位符值替换为自己的值。 有关装载文件共享的详细信息，请参阅 [将 Azure 文件共享用于 Windows](../articles/storage/files/storage-how-to-use-files-windows.md)。 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

如果在连接到 Azure 文件时遇到问题，请参阅在 [Windows 上为 Azure 文件装载错误发布的疑难解答工具](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)。 我们还提供了有关在阻止端口445时解决方案的 [指南](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) 。 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>为 Windows 文件资源管理器配置 NTFS 权限

使用 Windows 文件资源管理器向文件共享下的所有目录和文件（包括根目录）授予完全权限。

1. 打开 Windows 文件资源管理器，右键单击文件/目录，然后选择 " **属性**"。
2. 选择“安全”**** 选项卡。
3. 选择 " **编辑"。** 更改权限。
4. 您可以更改现有用户的权限，也可以选择 " **添加 ...** " 向新用户授予权限。
5. 在添加新用户的提示窗口中，在 " **输入要选择的对象名称** " 框中输入要向其授予权限的目标用户名，然后选择 " **检查名称** " 以查找目标用户的完整 UPN 名称。
7.    选择“确定”  。
8.    在 " **安全** " 选项卡中，选择要授予新用户的所有权限。
9.    选择“应用”。 

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 配置 NTFS 权限

使用以下 Windows 命令为文件共享（包括根目录）下的所有目录和文件授予完全权限。 请务必将示例中的占位符值替换为你自己的值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

有关如何使用 icacls 设置 NTFS 权限以及不同类型的支持权限的详细信息，请参阅 [icacls 的命令行参考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>从加入域的 VM 装载文件共享

以下过程验证是否正确设置了文件共享和访问权限，并且可以从已加入域的 VM 访问 Azure 文件共享。 请注意，共享级别 Azure 角色分配可能需要一些时间才能生效。 

使用已向其授予权限的 Azure AD 标识登录到 VM，如下图所示。 如果为 Azure 文件启用了本地 AD DS 身份验证，请使用 AD DS 凭据。 对于 Azure AD DS 身份验证，请用 Azure AD 凭据登录。

![显示用户身份验证的 Azure AD 登录屏幕的屏幕截图](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

使用以下命令装载 Azure 文件共享。 请务必将占位符值替换为你自己的值。 由于已经过身份验证，因此不需要提供存储帐户密钥、本地 AD DS 凭据或 Azure AD DS 凭据。 通过本地 AD DS 或 Azure AD DS 进行身份验证时，支持单一登录体验。 如果遇到 AD DS 凭据装入问题，请参阅 [在 Windows 中对 Azure 文件问题进行故障排除](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 以获得指导。

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```
