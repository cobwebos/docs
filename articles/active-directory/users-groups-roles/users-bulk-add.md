---
title: 批量在 Azure 活动目录门户中创建用户 |微软文档
description: 在 Azure 活动目录中的 Azure AD 管理中心批量添加用户
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532914"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>批量在 Azure 活动目录中创建用户

Azure 活动目录 （Azure AD） 支持批量用户创建和删除操作、批量邀请来宾，并支持下载用户、组和组成员的列表。

## <a name="required-permissions"></a>所需的权限

为了在管理门户中批量创建用户，您必须以全局管理员或用户管理员身份登录。

## <a name="to-create-users-in-bulk"></a>批量创建用户

1. 使用组织中的用户管理员的帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择 **"用户** > **批量创建**"。
1. 在 **"批量创建用户**"页上，选择 **"下载"** 以接收用户属性的有效逗号分隔值 （CSV） 文件，然后添加要创建的添加用户。

   ![选择本地 CSV 文件，其中列出要添加的用户](./media/users-bulk-add/upload-button.png)

1. 打开 CSV 文件，并为要创建的每个用户添加一行。 唯一需要的值是**名称**、**用户主体名称**、**初始密码**和**阻止登录（是/否）。** 然后保存文件。

   ![CSV 文件包含要创建的用户的名称和指示](./media/users-bulk-add/add-csv-file.png)

1. 在 **"批量创建用户**"页上，在"上传 CSV 文件"下，浏览到该文件。 当您选择该文件并单击"**提交**"时，将启动 CSV 文件的验证。
1. 验证文件内容后，您将看到**已成功上载的文件**。 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，选择 **"提交"** 以启动导入新用户的 Azure 批量操作。
1. 导入操作完成后，您将看到批量操作作业状态的通知。

如果存在错误，则可以在 **"批量操作结果**"页上下载并查看结果文件。 该文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

您可以在 **"批量操作结果"** 页中查看所有挂起的批量请求的状态。

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

接下来，可以检查所创建的用户是否存在于 Azure 门户或使用 PowerShell 中的 Azure AD 组织中。

## <a name="verify-users-in-the-azure-portal"></a>验证 Azure 门户中的用户

1. 使用组织中的用户管理员的帐户[登录到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在导航窗格中选择“Azure Active Directory”。****
1. 在“管理”下，选择“用户”   。
1. 在 **"显示**"下，选择 **"所有用户**"并验证是否列出了您创建的用户。

### <a name="verify-users-with-powershell"></a>使用 PowerShell 验证用户

运行下面的命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您应该会看到列出您创建的用户。

## <a name="bulk-import-service-limits"></a>批量导入服务限制

创建用户的每个批量活动最多可以运行一小时。 这允许批量创建至少 50，000 个用户。

## <a name="next-steps"></a>后续步骤

- [批量删除用户](users-bulk-delete.md)
- [下载用户列表](users-bulk-download.md)
- [批量还原用户](users-bulk-restore.md)
