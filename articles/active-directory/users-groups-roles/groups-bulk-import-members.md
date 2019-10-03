---
title: 批量导入将成员添加到组-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 管理中心批量添加组成员。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4412bc9ce8d78b5810b25b60724575af66774127
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146257"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory 中的批量导入组成员（预览）

使用 Azure Active Directory （Azure AD）门户，你可以通过使用逗号分隔值（CSV）文件将大量成员添加到组中，从而大容量导入组成员。

> [!NOTE]
> Azure AD 批量操作是 Azure AD 的公共预览功能，适用于任何付费的 Azure AD 许可计划。 有关预览版使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="to-bulk-import-group-members"></a>大容量导入组成员

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以大容量导入其拥有的组的成员。
1. 在 Azure AD 中，选择 "**组** > " "**所有组**"。
1. 打开要向其中添加成员的组，然后选择 "**成员**"。
1. 在 "**成员**" 页上，选择 "**导入成员**"。
1. 在 "**大容量导入组成员（预览）** " 页上，选择 "**下载**" 以获取具有所需组成员属性的 CSV 文件模板。

    !["导入成员" 命令位于组的配置文件页上](./media/groups-bulk-import-members/import-panel.png)

1. 打开 CSV 文件并为要导入到组中的每个组成员添加一行（所需的值可以是**成员对象 ID**或**用户主体名称**）。 然后保存文件。

   ![CSV 文件包含要导入的成员的名称和 Id](./media/groups-bulk-import-members/csv-file.png)

1. 在 "**大容量导入组成员（预览）** " 页上的 "**上载 csv 文件**" 下，浏览到该文件。 选择该文件后，将开始验证 CSV 文件。
1. 验证文件内容时，大容量导入页会**成功上传文件**。 如果有错误，必须修复这些错误，然后才能提交作业。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动 Azure 批量操作，该操作将组成员导入到组中。
1. 导入操作完成后，会看到一条通知，指出批量操作已成功。

## <a name="check-import-status"></a>检查导入状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![大容量操作的 "结果" 页显示大容量请求状态](./media/groups-bulk-import-members/bulk-center.png)

有关大容量操作中每个行项的详细信息，请选择 **# Success**、 **# 失败**或**请求总数**列下的值。 如果发生失败，则会列出失败的原因。

## <a name="bulk-import-service-limits"></a>大容量导入服务限制

要导入组成员列表的每个批量活动最多可以运行一小时。 这将允许至少导入40000个成员的列表。

## <a name="next-steps"></a>后续步骤

- [批量删除组成员](groups-bulk-remove-members.md)
- [下载组的成员](groups-bulk-download-members.md)
- [下载所有组的列表](groups-bulk-download.md)
