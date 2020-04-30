---
title: 在 Azure Active Directory 门户中批量创建用户 |Microsoft Docs
description: 在 Azure AD 管理中心的 Azure Active Directory 中批量添加用户
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03f6e3d6edde51598b1d148469aceb1ff3b3d636
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203382"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>在 Azure Active Directory 中批量创建用户

Azure Active Directory （Azure AD）支持批量用户创建和删除操作，并且支持下载用户列表。 只需填写逗号分隔值（CSV）模板即可从 Azure AD 门户下载。

## <a name="required-permissions"></a>所需的权限

若要在管理门户中批量创建用户，你必须以全局管理员或用户管理员身份登录。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

下载并填写批量上传 CSV 模板，以帮助你批量成功创建 Azure AD 的用户。 下载的 CSV 模板可能如下例所示：

![用于上传和调用的电子表格说明每个行和列的用途和值](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：上载 CSV 中必须包含包含版本号的第一行。
- **列标题**：列标题的&lt;*格式为*&gt; &lt;"*必需" 或 "空白*&gt;"。 例如，`Name [displayName] Required` 。 某些较旧版本的模板可能会略有不同。
- **示例行**：我们已在模板中包含每个列可接受值的行示例。 你必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不能删除或修改上载模板的前两行，也不能处理上传。
- 首先列出所需的列。
- 建议不要将新列添加到模板。 你添加的任何其他列都将被忽略且不进行处理。
- 建议尽可能频繁地下载 CSV 模板的最新版本。

## <a name="to-create-users-in-bulk"></a>批量创建用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择 "**用户** > **批量创建**"。
1. 在 "**批量创建用户**" 页上，选择 "**下载**" 以接收用户属性的有效逗号分隔值（CSV）文件，然后添加要创建的 "添加用户"。

   ![选择要在其中列出你要添加的用户的本地 CSV 文件](./media/users-bulk-add/upload-button.png)

1. 打开 CSV 文件并为每个要创建的用户添加一行。 唯一必需的值是**名称**、**用户主体名称**、**初始密码**和**阻止登录（是/否）**。 然后保存文件。

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. 在 "**批量创建用户**" 页上的 "上传 CSV 文件" 下，浏览到该文件。 选择该文件并单击 "**提交**" 时，将启动对 CSV 文件的验证。
1. 验证文件内容后，会看到**已成功上传文件**。 如果有错误，必须修正错误，然后才能提交作业。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动导入新用户的 Azure 批量操作。
1. 导入操作完成后，会显示大容量操作作业状态的通知。

如果有错误，则可以在 "**批量操作结果**" 页上下载并查看结果文件。 文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

你可以在 "**批量操作结果**" 页中查看所有挂起的批量请求的状态。

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

接下来，你可以查看在 Azure 门户中或使用 PowerShell 时，你创建的用户在 Azure AD 组织中是否存在。

## <a name="verify-users-in-the-azure-portal"></a>验证 Azure 门户中的用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在导航窗格中选择“Azure Active Directory”。 
1. 在“管理”下，选择“用户”   。
1. 在 "**显示**" 下，选择 "**所有用户**"，并验证是否列出了你创建的用户。

### <a name="verify-users-with-powershell"></a>通过 PowerShell 验证用户

运行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

应会看到所创建的用户已列出。

## <a name="bulk-import-service-limits"></a>大容量导入服务限制

创建用户的每个批量活动最多可以运行一小时。 这样就可以批量创建至少50000的用户。

## <a name="next-steps"></a>后续步骤

- [批量删除用户](users-bulk-delete.md)
- [下载用户列表](users-bulk-download.md)
- [批量还原用户](users-bulk-restore.md)
