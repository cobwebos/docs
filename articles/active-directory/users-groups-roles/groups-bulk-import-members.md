---
title: 批量上传以添加或创建组的成员-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 管理中心批量添加组成员。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8902c3147bbe142fc58d4e2c3fa83601c8ccbba3
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203502"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>批量导入组成员 Azure Active Directory

使用 Azure Active Directory （Azure AD）门户，你可以通过使用逗号分隔值（CSV）文件将大量成员添加到组中，从而大容量导入组成员。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

下载并填写批量上传 CSV 模板，以批量成功添加 Azure AD 组成员。 CSV 模板可能如下例所示：

![用于上传和调用的电子表格说明每个行和列的用途和值](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：上载 CSV 中必须包含包含版本号的第一行。
- **列标题**：列标题的&lt;*格式为*&gt; &lt;"*必需" 或 "空白*&gt;"。 例如，`Member object ID or user principal name [memberObjectIdOrUpn] Required` 。 某些较旧版本的模板可能会略有不同。 对于组成员身份更改，你可以选择要使用的标识符：成员对象 ID 或用户主体名称。
- **示例行**：我们已在模板中包含每个列可接受值的行示例。 你必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不能删除或修改上载模板的前两行，也不能处理上传。
- 首先列出所需的列。
- 建议不要将新列添加到模板。 你添加的任何其他列都将被忽略且不进行处理。
- 建议尽可能频繁地下载 CSV 模板的最新版本。

## <a name="to-bulk-import-group-members"></a>大容量导入组成员

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以大容量导入其拥有的组的成员。
1. 在 Azure AD 中，选择 "**组** > " "**所有组**"。
1. 打开要向其中添加成员的组，然后选择 "**成员**"。
1. 在 "**成员**" 页上，选择 "**导入成员**"。
1. 在 "**大容量导入组成员**" 页上，选择 "**下载**" 以获取具有所需组成员属性的 CSV 文件模板。

    !["导入成员" 命令位于组的配置文件页上](./media/groups-bulk-import-members/import-panel.png)

1. 打开 CSV 文件并为要导入到组中的每个组成员添加一行（所需的值可以是**成员对象 ID**或**用户主体名称**）。 然后保存文件。

   ![CSV 文件包含要导入的成员的名称和 Id](./media/groups-bulk-import-members/csv-file.png)

1. 在 "**大容量导入组成员**" 页上的 "**上传 csv 文件**" 下，浏览到该文件。 选择该文件后，将开始验证 CSV 文件。
1. 验证文件内容时，大容量导入页会**成功上传文件**。 如果有错误，必须修正错误，然后才能提交作业。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动 Azure 批量操作，该操作将组成员导入到组中。
1. 导入操作完成后，会看到一条通知，指出批量操作已成功。

## <a name="check-import-status"></a>检查导入状态

你可以在 "**批量操作结果**" 页中查看所有挂起的批量请求的状态。

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

有关大容量操作中每个行项的详细信息，请选择 **# Success**、 **# 失败**或**请求总数**列下的值。 如果失败，则会列出失败原因。

## <a name="bulk-import-service-limits"></a>大容量导入服务限制

要导入组成员列表的每个批量活动最多可以运行一小时。 这将允许至少导入40000个成员的列表。

## <a name="next-steps"></a>后续步骤

- [批量删除组成员](groups-bulk-remove-members.md)
- [下载组的成员](groups-bulk-download-members.md)
- [下载所有组的列表](groups-bulk-download.md)
