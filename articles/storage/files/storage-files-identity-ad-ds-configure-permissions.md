---
title: 控制用户可以在文件级别执行的操作-Azure 文件共享
description: 了解如何为本地 AD DS 身份验证配置 Windows Acl 权限到 Azure 文件共享。 允许你利用粒度访问控制。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 5e293bb98405affd824d4bbc50b6f24c5a0e3c11
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999609"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>第三部分：通过 SMB 配置目录和文件级权限 

在开始本文之前，请确保已完成上一篇文章，[将共享级别权限分配给某个标识](storage-files-identity-ad-ds-assign-permissions.md)，以确保你的共享级权限已就位。

使用 RBAC 分配共享级权限后，必须在根、目录或文件级别配置适当的 Windows Acl，才能利用粒度访问控制。 将 RBAC 共享级别的权限视为用于确定用户是否可以访问共享的高级身份确认程序。 虽然 Windows Acl 以更精细的级别运行，以确定用户可以在目录或文件级别执行哪些操作。 当用户尝试访问文件/目录时，会强制执行共享级和文件/目录级别的权限，因此，如果其中一个文件/目录之间存在差异，则只会应用限制最严格的权限。 例如，如果用户在文件级别具有读/写访问权限，但只读取共享级别，则只能读取该文件。 同样，如果反转了它，并且用户对共享级别具有读/写访问权限，但只读取文件级别，则它们仍可以仅读取文件。

## <a name="supported-permissions"></a>支持的权限

Azure 文件支持完整的基本和高级 Windows Acl 集。 你可以通过装入并使用 Windows 文件资源管理器、运行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)命令或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl)命令来查看和配置 Azure 文件共享中的目录和文件的 Windows acl。 

若要使用超级用户权限配置 Acl，必须使用已加入域的 VM 中的存储帐户密钥装载共享。 按照下一部分中的说明从命令提示符装载 Azure 文件共享并配置 Windows Acl。

文件共享的根目录中包含以下权限：

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|用户|定义|
|---|---|
|BUILTIN\Administrators|作为本地 AD DS 环境的域管理员的所有用户。
|BUILTIN\Users|AD 中的内置安全组。 默认情况下，它包括 NT AUTHORITY\Authenticated 用户。 对于传统的文件服务器，你可以为每个服务器配置成员身份定义。 对于 Azure 文件，没有托管服务器，因此 BUILTIN\Users 包含与 NT AUTHORITY\Authenticated 用户相同的用户集。|
|NT AUTHORITY\SYSTEM|文件服务器的操作系统的服务帐户。 此类服务帐户在 Azure 文件上下文中不适用。 它包含在根目录中，以与 Windows 文件服务器体验混合方案一致。|
|NT AUTHORITY\Authenticated 用户|AD 中可获取有效 Kerberos 令牌的所有用户。|
|CREATOR OWNER|每个对象（目录或文件）都具有该对象的所有者。 如果已为该对象分配 Acl "创建者所有者"，则作为此对象所有者的用户具有对 ACL 定义的对象的权限。|



## <a name="mount-a-file-share-from-the-command-prompt"></a>从命令提示符装载文件共享

使用 Windows `net use` 命令装载 Azure 文件共享。 请记住，将以下示例中的占位符值替换为自己的值。 有关装载文件共享的详细信息，请参阅[将 Azure 文件共享用于 Windows](storage-how-to-use-files-windows.md)。 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

如果在连接到 Azure 文件时遇到问题，请参阅在[Windows 上为 Azure 文件安装错误发布的疑难解答工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我们还提供了有关在阻止端口445时解决方案的[指南](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access)。 

## <a name="configure-windows-acls"></a>配置 Windows Acl

当你的文件共享已装载到存储帐户密钥后，你必须配置 Windows Acl （也称为 NTFS 权限）。 可以使用 Windows 文件资源管理器或 icacls 配置 Windows Acl。

如果在配置了 Windows Dacl 的本地文件服务器中有目录或文件，并针对 AD DS 标识配置了这些目录或文件，则可以将其复制到 Azure 文件，使用 AzCopy 和[Azure](https://github.com/Azure/azure-storage-azcopy/releases) 如果通过 Azure 文件同步将目录和文件分层到 Azure 文件，则会以本机格式执行并保存 Acl。

### <a name="configure-windows-acls-with-windows-file-explorer"></a>通过 Windows 文件资源管理器配置 Windows Acl

使用 Windows 文件资源管理器向文件共享下的所有目录和文件（包括根目录）授予完全权限。

1. 打开 Windows 文件资源管理器，右键单击文件/目录，然后选择 "**属性**"。
1. 选择“安全”**** 选项卡。
1. 选择 "**编辑"。** 更改权限。
1. 您可以更改现有用户的权限，也可以选择 "**添加 ...** " 向新用户授予权限。
1. 在添加新用户的提示窗口中，在 "**输入要选择的对象名称**" 框中输入要向其授予权限的目标用户名，然后选择 "**检查名称**" 以查找目标用户的完整 UPN 名称。
1.    选择“确定”。
1.    在 "**安全**" 选项卡中，选择要授予新用户的所有权限。
1.    选择“应用”。

### <a name="configure-windows-acls-with-icacls"></a>通过 icacls 配置 Windows Acl

使用以下 Windows 命令为文件共享（包括根目录）下的所有目录和文件授予完全权限。 请务必将示例中的占位符值替换为你自己的值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

若要详细了解如何使用 icacls 来设置 Windows Acl 和不同类型的支持权限，请参阅[icacls 的命令行参考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="next-steps"></a>后续步骤

启用并配置此功能后，请继续学习下一篇文章，从已加入域的 VM 中装载 Azure 文件共享。

[第四部分：从已加入域的 VM 装载文件共享](storage-files-identity-ad-ds-mount-file-share.md)
