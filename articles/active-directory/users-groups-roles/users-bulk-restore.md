---
title: 在 Azure Active Directory 门户中批量还原已删除的用户（预览版） |Microsoft Docs
description: 在 Azure AD 管理中心内批量还原已删除的用户 Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174246"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>在 Azure Active Directory 中批量还原已删除的用户（预览版）

Azure Active Directory （Azure AD）支持批量用户创建和删除操作，批量邀请来宾，并支持下载用户、组和组成员的列表。

## <a name="to-bulk-restore-users"></a>批量还原用户

1. 使用 Azure AD 组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择 "**用户**@no__t**已删除**-1"。
1. 在 "**已删除用户**" 页上，选择 "**批量还原**"，以上传要还原的用户的属性的有效 CSV 文件。

   ![在 "已删除用户" 页上选择 "批量还原" 命令](./media/users-bulk-restore/bulk-restore.png)

1. 打开 CSV 文件并为每个要还原的用户添加一行。 唯一必需的值为**ObjectID**。 然后保存文件。

   ![选择要在其中列出你要添加的用户的本地 CSV 文件](./media/users-bulk-restore/upload-button.png)

1. 在 "**批量还原（预览）** " 页上的 "**上载 csv 文件**" 下，浏览到该文件。 选择该文件并单击 "**提交**" 时，将启动对 CSV 文件的验证。
1. 验证文件内容后，会看到“文件上传成功”消息。 如果有错误，必须修正错误，然后才能提交作业。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动用于还原用户的 Azure 批量操作。
1. 还原操作完成后，会看到一条通知，指出批量操作已成功。

如果有错误，则可以在 "**批量操作结果**" 页上下载并查看结果文件。 文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![在大容量操作结果页中检查上传状态](./media/users-bulk-restore/bulk-center.png)

接下来，你可以查看已还原的用户是否存在于 Azure 门户中的 Azure AD 组织中或使用 PowerShell。

## <a name="view-restored-users-in-the-azure-portal"></a>查看 Azure 门户中的已还原用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在导航窗格中选择“Azure Active Directory”。
1. 在“管理”下，选择“用户”。
1. 在 "**显示**" 下，选择 "**所有用户**"，并验证是否列出了还原的用户。

### <a name="view-users-with-powershell"></a>通过 PowerShell 查看用户

运行下面的命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

应会看到已还原的用户已列出。

## <a name="next-steps"></a>后续步骤

- [批量导入用户](users-bulk-add.md)
- [批量删除用户](users-bulk-delete.md)
- [下载用户列表](users-bulk-download.md)
