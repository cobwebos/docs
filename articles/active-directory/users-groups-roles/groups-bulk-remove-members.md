---
title: 通过上传 CSV 文件来批量删除组成员 - Azure Active Directory | Microsoft Docs
description: 在 Azure 管理中心使用批量操作删除组成员。
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
ms.openlocfilehash: acf3cba5046a20b99212e89b378327765d22fa23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421631"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>在 Azure Active Directory 中批量删除组成员

在 Azure Active Directory (Azure AD) 门户中，可使用逗号分隔值 (CSV) 文件批量删除组成员，从而从组中删除大量成员。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

要成功地批量添加 Azure AD 组成员，请下载并填写批量上传 CSV 模板。 CSV 模板可能如下例所示：

![用于上传和调用的电子表格，说明了每一行和每一列的用途和值](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：包含版本号的第一行必须包含在上传 CSV 中。
- **列标题**：列标题的格式为：&lt;项名称&gt; [PropertyName] &lt;必需或空白&gt;。 例如，`Member object ID or user principal name [memberObjectIdOrUpn] Required` 。 模板的一些较旧版本可能略有不同。 对于组成员身份更改，你可选择要使用的标识符：成员对象 ID 还是用户主体名称。
- **示例行**：我们已经在模板中包含了一行示例，展示了每个列的可接受值。 你必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不得删除或修改上传模板的前两行，否则无法处理上传。
- 所需的列会先列出。
- 建议不要将新列添加到模板。 所添加的任何其他列都会被忽略，不进行处理。
- 建议尽可能频繁地下载 CSV 模板的最新版本。

## <a name="to-bulk-remove-group-members"></a>批量删除组成员

1. 使用组织中的用户管理员帐户登录到 [Azure 门户](https://portal.azure.com)。 组所有者还可批量删除其拥有的组的成员。
1. 在 Azure AD 中，选择“组” > “所有组” 。
1. 打开要从中删除成员的组，然后选择“成员”。
1. 在“成员”页面上，选择“删除成员” 。
1. 在“批量删除组成员”页面上，选择“下载”以获取具有所需组成员属性的 CSV 文件模板 。

   ![“删除成员”命令位于组的配置文件页上](./media/groups-bulk-remove-members/remove-panel.png)

1. 打开 CSV 文件，并为要从组中删除的每个组成员添加一行（所需值为成员对象 ID 或用户主体名称）。 然后保存文件。

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV 文件包含要删除的组成员的姓名和 ID":::

1. 在“批量删除组成员”页面上的“上传 csv 文件”下，浏览到该文件 。 选择 CSV 文件后立即开始对它的验证。
1. 验证文件内容后，批量导入页将显示“已成功上传文件”。 如果有错误，必须修正错误，然后才能提交作业。
1. 当文件通过验证时，选择“提交”，开始从组中删除组成员的 Azure 批量操作。
1. 删除操作完成后，会显示一条通知，指出批量操作成功。

## <a name="check-removal-status"></a>检查删除状态

可在“批量操作结果”页面中查看所有挂起的批量请求的状态。

[![查看批量操作结果页中的状态](media/groups-bulk-remove-members/bulk-center.png)](media/groups-bulk-remove-members/bulk-center.png#lightbox)

要详细了解批量操作中每个行项，请选择“成功数”、“失败数”或“请求总数”列下的值  。 如果失败，则会列出失败原因。

## <a name="bulk-removal-service-limits"></a>批量删除服务限制

删除组成员列表的每个批量活动最多可运行一小时。 这样可删除至少包含 40,000 名成员的列表。

## <a name="next-steps"></a>后续步骤

- [批量导入组成员](groups-bulk-import-members.md)
- [下载组成员](groups-bulk-download-members.md)
- [下载所有组的列表](groups-bulk-download.md)
