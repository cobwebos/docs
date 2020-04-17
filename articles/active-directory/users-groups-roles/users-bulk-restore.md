---
title: Azure 活动目录门户中的批量还原已删除用户 |微软文档
description: 在 Azure 活动目录中的 Azure AD 管理中心批量还原已删除的用户
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: f75fe224491c2853f819a45db678e87849dc72d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532693"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>批量还原 Azure 活动目录中已删除的用户

Azure 活动目录 （Azure AD） 支持批量用户创建和删除操作、批量邀请来宾，并支持下载用户、组和组成员的列表。

## <a name="to-bulk-restore-users"></a>批量还原用户

1. 使用 Azure AD 组织中的用户管理员的帐户[登录到 Azure AD](https://aad.portal.azure.com)组织。
1. 在 Azure AD 中，选择**已删除****的用户** > 。
1. 在 **"已删除用户"** 页上，选择**批量还原**以上载要还原的用户的属性的有效 CSV 文件。

   ![在"已删除用户"页上选择批量还原命令](./media/users-bulk-restore/bulk-restore.png)

1. 打开 CSV 文件，并为要还原的每个用户添加一行。 唯一必需的值是**ObjectID**。 然后保存文件。

   ![选择本地 CSV 文件，其中列出要添加的用户](./media/users-bulk-restore/upload-button.png)

1. 在**批量还原**页上，**在"上传 csv 文件**"下，浏览到该文件。 当您选择该文件并单击"**提交**"时，将启动 CSV 文件的验证。
1. 验证文件内容后，会看到“文件上传成功”消息。**** 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，选择 **"提交"** 以启动还原用户的 Azure 批量操作。
1. 还原操作完成后，您将看到批量操作成功的通知。

如果存在错误，则可以在 **"批量操作结果**"页上下载并查看结果文件。 该文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

您可以在 **"批量操作结果"** 页中查看所有挂起的批量请求的状态。

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

接下来，可以检查还原的用户是否存在 Azure 门户或使用 PowerShell 的 Azure AD 组织。

## <a name="view-restored-users-in-the-azure-portal"></a>在 Azure 门户中查看还原的用户

1. 使用组织中的用户管理员的帐户[登录到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在导航窗格中选择“Azure Active Directory”。****
1. 在“管理”下，选择“用户”   。
1. 在 **"显示**"下，选择 **"所有用户**"并验证已列出还原的用户。

### <a name="view-users-with-powershell"></a>使用 PowerShell 查看用户

运行下面的命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您应该会看到列出您还原的用户。

## <a name="next-steps"></a>后续步骤

- [批量导入用户](users-bulk-add.md)
- [批量删除用户](users-bulk-delete.md)
- [下载用户列表](users-bulk-download.md)
