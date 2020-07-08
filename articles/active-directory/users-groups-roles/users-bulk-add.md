---
title: 在 Azure Active Directory 门户中批量创建用户 | Microsoft Docs
description: 在 Azure Active Directory 中的 Azure AD 管理中心批量添加用户
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a2c9500ecefed02b28c066de80137d8f0882fd5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731493"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>在 Azure Active Directory 中批量创建用户

Azure Active Directory (Azure AD) 支持批量用户创建和删除操作，并支持下载用户列表。 只需填写可从 Azure AD 门户下载的逗号分隔值 (CSV) 模板即可。

## <a name="required-permissions"></a>所需的权限

若要在管理门户中批量创建用户，你必须以全局管理员或用户管理员身份登录。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

下载并填写批量上传 CSV 模板，帮助你成功批量创建 Azure AD 用户。 下载的 CSV 模板可能如下例所示：

![用于上传和调出的电子表格，说明了每一行和每一列的用途和值](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：包含版本号的第一行必须包含在上传的 CSV 中。
- 列标题：列标题的格式为：&lt;项名称&gt; [PropertyName] &lt;必需或空白&gt;。 例如，`Name [displayName] Required` 。 某些较旧版本的模板可能会略有不同。
- 示例行：我们已经在模板中包含了一行示例，说明每列可接受的值。 必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不得删除或修改上传模板的前两行，否则无法处理上传。
- 所需的列会先列出。
- 建议不要将新列添加到模板。 所添加的任何其他列都会被忽略，不进行处理。
- 建议尽可能经常下载 CSV 模板的最新版本。

## <a name="to-create-users-in-bulk"></a>批量创建用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择“用户” > “批量创建”。
1. 在“批量创建用户”页上，选择“下载”，以接收用户属性的有效逗号分隔值 (CSV) 文件，然后添加要创建的用户。

   ![选择要在其中列出你要添加的用户的本地 CSV 文件](./media/users-bulk-add/upload-button.png)

1. 打开 CSV 文件，并为每个要创建的用户添加一行。 只有“名称”、“用户主体名称”、“初始密码”和“阻止登录(是/否)”为必填项。 然后保存文件。

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. 在“批量创建用户”页中，在“上传 CSV 文件”下，浏览到该文件。 选择该文件并单击“提交”后，将启动对 CSV 文件的验证。
1. 验证文件内容后，会看到“文件上传成功”的消息。 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，请选择“提交”，以启动导入新用户的 Azure 批量操作。
1. 导入操作完成后，会显示一条通知，指示批量操作作业状态。

如果有错误，可以在“批量操作结果”页下载并查看结果文件。 该文件包含每个错误的原因。 文件提交必须与提供的模板匹配，并包含确切的列名称。

## <a name="check-status"></a>查看状态

可在“批量操作结果”页中查看所有挂起的批量请求的状态。

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

接下来，可在 Azure 门户中或使用 PowerShell 查看已创建的用户是否存在于 Azure AD 组织中。

## <a name="verify-users-in-the-azure-portal"></a>在 Azure 门户中验证用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在导航窗格中选择“Azure Active Directory”。
1. 在“管理”下，选择“用户” 。
1. 在“显示”下选择“所有用户”，并验证创建的用户是否已列出。

### <a name="verify-users-with-powershell"></a>使用 PowerShell 验证用户

运行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

应会看到你创建的用户已列出。

## <a name="bulk-import-service-limits"></a>“批量导入”服务限制

创建用户的每个批量活动最多可运行一小时。 这样就可以批量创建至少 50,000 个用户。

## <a name="next-steps"></a>后续步骤

- [批量删除用户](users-bulk-delete.md)
- [下载用户列表](users-bulk-download.md)
- [批量还原用户](users-bulk-restore.md)
