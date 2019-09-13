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
ms.openlocfilehash: 6c2204c572ca1f74f8060d0b6176df69359fe69a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901706"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>批量删除用户（预览） Azure Active Directory

Azure Active Directory （Azure AD）支持批量用户创建和删除操作，批量邀请来宾，并支持下载用户、组和组成员的列表。

## <a name="to-bulk-delete-users"></a>批量删除用户

1. 使用组织中的用户管理员帐户登录到 Azure AD 组织。
1. 在 Azure AD 中，选择 "**用户** > **批量删除**"。
1. 在 "**批量删除用户**" 页上，选择 "**下载**" 以接收用户属性的有效 CSV 文件，然后添加要删除的用户。
1. 当你完成了 CSV 文件的编辑或者你拥有自己准备好上载的任何一个时，请选择 "**上传**要验证的 CSV 文件" 下的文件。

   ![选择要在其中列出要删除的用户的本地 CSV 文件](./media/users-bulk-delete/bulk-delete.png)

1. 验证文件内容时，必须在提交作业之前修复任何错误。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动用于删除用户的 Azure batch 作业。 如果有错误，则可以在 "批量操作结果" 页上下载并查看结果文件。 文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![在大容量操作结果页中检查上传状态](./media/users-bulk-delete/bulk-center.png)

接下来，你可以查看 Azure AD 组织中已删除的用户是否存在于 Azure 门户中或使用 PowerShell。

## <a name="verify-deleted-users-in-the-azure-portal"></a>验证 Azure 门户中的已删除用户

1. 使用组织中的用户管理员帐户登录到 Azure 门户。
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
