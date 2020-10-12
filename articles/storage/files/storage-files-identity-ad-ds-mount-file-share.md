---
title: 将 Azure 文件共享装载到 AD DS 联接的 VM
description: 了解如何将文件共享装入本地 Active Directory 域服务联接的计算机。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 9807563c768b82c823ff754aaa679ddc917bf62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87535053"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>第四部分：从已加入域的 VM 装载文件共享

在开始本文之前，请确保已完成上一篇文章， [通过 SMB 配置目录和文件级权限](storage-files-identity-ad-ds-configure-permissions.md)。

本文中所述的过程验证是否正确设置了文件共享和访问权限，并且可以从已加入域的 VM 访问 Azure 文件共享。 共享级 Azure 角色分配可能需要一些时间才能生效。 

使用你向其授予权限的凭据登录到客户端，如下图所示。

![显示用户身份验证的 Azure AD 登录屏幕的屏幕截图](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>挂载必备组件

在可以装载文件共享之前，请确保已完成以下先决条件：

- 如果要从以前使用存储帐户密钥安装了文件共享的客户端装载文件共享，请确保已断开共享、删除了存储帐户密钥的永久凭据，并且当前正在使用 AD DS 凭据进行身份验证。
- 你的客户端必须对你的 AD DS 有线路视觉。 如果你的计算机或 VM 不在你的 AD DS 管理的网络中，你将需要启用 VPN 来访问 AD DS 以进行身份验证。

将占位符值替换为你自己的值，然后使用以下命令装载 Azure 文件共享：

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
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

如果在加载时遇到 AD DS 凭据问题，请参阅 [无法装载具有 AD 凭据的 Azure 文件](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) 以获得指导。

如果已成功安装文件共享，则已成功为 Azure 文件共享启用并配置了本地 AD DS 身份验证。

## <a name="next-steps"></a>后续步骤

如果在 AD DS 中创建的用于表示存储帐户的标识位于强制密码轮换的域或 OU 中，请转到下一文章，了解有关更新密码的说明：

[更新中的存储帐户标识的密码 AD DS](storage-files-identity-ad-ds-update-password.md)
