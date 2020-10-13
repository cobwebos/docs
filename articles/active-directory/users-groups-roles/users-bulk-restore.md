---
title: 在 Azure Active Directory 门户中批量还原已删除的用户 | Microsoft Docs
description: 在 Azure Active Directory 中的 Azure AD 管理中心内批量还原已删除的用户
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
ms.openlocfilehash: 4fe5cacfec6ee85a5d61204ffce38c856b0d1baf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87423331"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>在 Azure Active Directory 中批量还原已删除的用户

Azure Active Directory (Azure AD) 支持批量用户还原操作，并支持下载用户、组和组成员的列表。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

下载并填写 CSV 模板，该模板可帮助你成功批量还原 Azure AD 用户。 下载的 CSV 模板可能如下例所示：

![用于上传和调出的电子表格，说明了每一行和每一列的用途和值](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：包含版本号的第一行必须包含在上传 CSV 中。
- **列标题**：列标题的格式为：&lt;项名称&gt; [PropertyName] &lt;必需或空白&gt;。 例如，`Object ID [objectId] Required` 。 某些较旧版本的模板可能会略有不同。
- **示例行**：我们已经在模板中包含了一行示例，展示了每个列的可接受值。 你必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不得删除或修改上传模板的前两行，否则无法处理上传。
- 所需的列会先列出。
- 建议不要将新列添加到模板。 所添加的任何其他列都会被忽略，不进行处理。
- 建议尽可能频繁地下载 CSV 模板的最新版本。

## <a name="to-bulk-restore-users"></a>如果要批量还原用户，请执行以下操作

1. 使用 Azure AD 组织中的用户管理员的帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择“用户” > “已删除” 。
1. 在“删除用户”页上，选择“批量还原”以上传要还原的用户属性的有效 CSV 文件 。

    ![在“已删除用户”页上选择“批量还原”命令](./media/users-bulk-restore/bulk-restore.png)

1. 打开 CSV 模板，并为每个要还原的用户添加一行。 唯一需要的值为 ObjectID。 然后保存文件。

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="选择要在其中列出你要添加的用户的本地 CSV 文件":::

1. 在“批量还原”页上，在“上传 csv 文件”下，浏览到该文件 。 选择该文件并单击“提交”后，将启动对 CSV 文件的验证。
1. 验证文件内容后，会看到“文件上传成功”消息。 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，请选择“提交”，以启动用于还原用户的 Azure 批量操作。
1. 还原操作完成后，会显示一条通知，指出批量操作成功。

如果有错误，可以在“批量操作结果”页下载并查看结果文件。 该文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

可在“批量操作结果”页面中查看所有挂起的批量请求的状态。

[![在“批量操作结果”页面中查看状态。](media/users-bulk-restore/bulk-center.png)](media/users-bulk-restore/bulk-center.png#lightbox)

接下来，可在 Azure 门户中或使用 PowerShell 查看已还原的用户是否存在于 Azure AD 组织中。

## <a name="view-restored-users-in-the-azure-portal"></a>在 Azure 门户中查看已还原用户

1. 使用组织中的用户管理员的帐户[登录到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在导航窗格中选择“Azure Active Directory”。
1. 在“管理”下，选择“用户” 。
1. 在“显示”下选择“所有用户”，并验证还原的用户是否已列出 。

### <a name="view-users-with-powershell"></a>使用 PowerShell 查看用户

运行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

应会看到已还原的用户已列出。

## <a name="next-steps"></a>后续步骤

- [批量导入用户](users-bulk-add.md)
- [批量删除用户](users-bulk-delete.md)
- [下载用户列表](users-bulk-download.md)
