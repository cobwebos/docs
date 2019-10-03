---
title: 在 Azure Active Directory 门户中批量创建用户（预览） |Microsoft Docs
description: 在 Azure AD 管理中心的 Azure Active Directory 中批量添加用户
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c62cbe71f2e02c6f2c09620a8470a97ae57392
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146314"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>批量创建用户（预览） Azure Active Directory

Azure Active Directory （Azure AD）支持批量用户创建和删除操作，批量邀请来宾，并支持下载用户、组和组成员的列表。

## <a name="bulk-import-service-limits"></a>大容量导入服务限制

创建用户的每个批量活动最多可以运行一小时。 这样就可以批量创建至少50000的用户。

## <a name="required-permissions"></a>所需权限

若要在管理门户中批量创建用户，你必须以全局管理员或用户管理员身份登录。

## <a name="to-bulk-import-users"></a>批量导入用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择 "**用户** > **批量创建**"。
1. 在 "**批量创建用户**" 页上，选择 "**下载**" 以接收用户属性的有效逗号分隔值（CSV）文件，然后添加新用户。

   ![CSV 文件包含要创建的用户的名称和 Id](./media/users-bulk-add/add-csv-file.png)

1. 当你完成了 CSV 文件的编辑或者你拥有自己准备好上载的任何一个时，请选择 "**上传**要验证的 CSV 文件" 下的文件。

   ![选择要在其中列出你要添加的用户的本地 CSV 文件](./media/users-bulk-add/upload-button.png)

1. 验证文件内容时，必须先修复所有错误，然后才能开始上载作业。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动添加新用户信息的 Azure batch 作业。 如果有错误，则可以在 "批量操作结果" 页上下载并查看结果文件。 文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![在大容量操作结果页中检查上传状态](./media/users-bulk-add/bulk-center.png)

接下来，你可以查看在 Azure 门户中或使用 PowerShell 时，你创建的用户在 Azure AD 组织中是否存在。

## <a name="verify-users-in-the-azure-portal"></a>验证 Azure 门户中的用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在导航窗格中选择“Azure Active Directory”。
1. 在“管理”下，选择“用户”。
1. 在 "**显示**" 下，选择 "**所有用户**"，并验证是否列出了你创建的用户。

### <a name="verify-users-with-powershell"></a>通过 PowerShell 验证用户

运行下面的命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

应会看到所创建的用户已列出。

## <a name="next-steps"></a>后续步骤

- [批量删除用户](users-bulk-delete.md)
- [下载用户列表](users-bulk-download.md)
- [大容量还原用户](users-bulk-restore.md)
