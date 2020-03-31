---
title: Azure 活动目录门户中的批量删除用户（预览） |微软文档
description: 在 Azure 活动目录中的 Azure 管理中心批量删除用户
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174376"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure 活动目录中批量删除用户（预览）

使用 Azure 活动目录 （Azure AD） 门户，可以使用逗号分隔的值 （CSV） 文件批量删除用户，将大量成员删除到组。

## <a name="to-bulk-delete-users"></a>批量删除用户

1. 使用组织中的用户管理员的帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择 **"用户** > **批量删除**"。
1. 在**批量删除用户**页上，选择 **"下载"** 以接收用户属性的有效 CSV 文件。

   ![选择要删除的用户的本地 CSV 文件](./media/users-bulk-delete/bulk-delete.png)

1. 打开 CSV 文件，并为要删除的每个用户添加一行。 唯一必需的值是**用户主体名称**。 然后保存文件。

   ![CSV 文件包含要删除的用户的姓名和指示](./media/users-bulk-delete/delete-csv-file.png)

1. 在**批量删除用户（预览）** 页上，在 **"上传您的 csv 文件**"下，浏览到该文件。 当您选择该文件并单击"提交"时，CSV 文件的验证将开始。
1. 验证文件内容后，会看到“文件上传成功”消息。**** 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，选择 **"提交"** 以启动删除用户的 Azure 批量操作。
1. 删除操作完成后，您将看到批量操作成功的通知。

如果存在错误，则可以在 **"批量操作结果**"页上下载并查看结果文件。 该文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

您可以在**批量操作结果（预览）** 页中查看所有挂起的批量请求的状态。

   ![在"批量操作结果"页中检查上载状态](./media/users-bulk-delete/bulk-center.png)

接下来，可以检查已删除的用户是否存在于 Azure 门户或使用 PowerShell 中的 Azure AD 组织中。

## <a name="verify-deleted-users-in-the-azure-portal"></a>在 Azure 门户中验证已删除的用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
1. 在导航窗格中选择“Azure Active Directory”。****
1. 在“管理”下，选择“用户”********。
1. 在 **"显示**"下，选择"**所有用户**"并验证您删除的用户是否不再列出。

### <a name="verify-deleted-users-with-powershell"></a>使用 PowerShell 验证已删除的用户

运行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

验证已删除的用户是否不再列出。

## <a name="next-steps"></a>后续步骤

- [批量添加用户](users-bulk-add.md)
- [下载用户列表](users-bulk-download.md)
- [批量还原用户](users-bulk-restore.md)
