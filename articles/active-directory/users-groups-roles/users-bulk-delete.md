---
title: 在 Azure Active Directory 门户中批量删除用户（预览） |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 管理中心中批量删除用户
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
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174376"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>批量删除用户（预览） Azure Active Directory

使用 Azure Active Directory （Azure AD）门户，可以通过使用逗号分隔值（CSV）文件批量删除用户，删除组中的大量成员。

## <a name="to-bulk-delete-users"></a>批量删除用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择 "**用户** > **批量删除**"。
1. 在 "**批量删除用户**" 页上，选择 "**下载**" 以接收用户属性的有效 CSV 文件。

   ![选择要在其中列出要删除的用户的本地 CSV 文件](./media/users-bulk-delete/bulk-delete.png)

1. 打开 CSV 文件并为每个要删除的用户添加一行。 唯一必需的值是 "**用户主体名称**"。 然后保存文件。

   ![CSV 文件包含要删除的用户的名称和 Id](./media/users-bulk-delete/delete-csv-file.png)

1. 在 "**批量删除用户（预览）** " 页上的 "**上载 csv 文件**" 下，浏览到该文件。 选择该文件并单击 "提交" 时，将启动对 CSV 文件的验证。
1. 验证文件内容后，会看到“文件上传成功”消息。 如果有错误，必须修正错误，然后才能提交作业。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动用于删除用户的 Azure 批量操作。
1. 删除操作完成后，会看到一条通知，指出批量操作已成功。

如果有错误，则可以在 "**批量操作结果**" 页上下载并查看结果文件。 文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![在大容量操作结果页中检查上传状态](./media/users-bulk-delete/bulk-center.png)

接下来，你可以查看 Azure AD 组织中已删除的用户是否存在于 Azure 门户中或使用 PowerShell。

## <a name="verify-deleted-users-in-the-azure-portal"></a>验证 Azure 门户中的已删除用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
1. 在导航窗格中选择“Azure Active Directory”。
1. 在“管理”下，选择“用户”。
1. 在 "**显示**" 下，仅选择 "**所有用户**"，并验证删除的用户是否不再列出。

### <a name="verify-deleted-users-with-powershell"></a>通过 PowerShell 验证已删除的用户

运行下面的命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

验证你删除的用户是否不再列出。

## <a name="next-steps"></a>后续步骤

- [批量添加用户](users-bulk-add.md)
- [下载用户列表](users-bulk-download.md)
- [大容量还原用户](users-bulk-restore.md)
