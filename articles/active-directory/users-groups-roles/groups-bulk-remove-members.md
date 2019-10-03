---
title: 通过上传 csv 文件-Azure Active Directory 来批量删除组成员 |Microsoft Docs
description: 在 Azure 管理中心中批量添加用户。
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
ms.openlocfilehash: 08708c23f9c8f4c4a8fc9f2f0aa5cd20d8333a42
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146333"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>批量删除组成员（预览） Azure Active Directory

使用 Azure Active Directory （Azure AD）门户，你可以通过使用逗号分隔值（CSV）文件批量删除组成员，从组中删除大量成员。

> [!NOTE]
> Azure AD 批量操作是 Azure AD 的公共预览功能，适用于任何付费的 Azure AD 许可计划。 有关预览版使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="to-bulk-remove-group-members"></a>批量删除组成员

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以批量删除其拥有的组的成员。
1. 在 Azure AD 中，选择 "**组** > " "**所有组**"。
1. 打开要从中删除成员的组，然后选择 "**成员**"。
1. 在 "**成员**" 页上，选择 "**删除成员**"。
1. 在 "**批量删除组成员（预览）** " 页上，选择 "**下载**" 以获取具有所需组成员属性的 CSV 文件模板。

   !["删除成员" 命令位于组的配置文件页上](./media/groups-bulk-remove-members/remove-panel.png)

1. 打开 CSV 文件，并为要从组中删除的每个组成员添加一行（所需值为成员对象 ID 或用户主体名称）。 然后保存文件。

   ![CSV 文件包含要删除的成员的名称和 Id](./media/groups-bulk-remove-members/csv-file.png)

1. 在 "**批量删除组成员（预览）** " 页上的 "**上载 csv 文件**" 下，浏览到该文件。 选择该文件后，将启动对 .csv 文件的验证。
1. 验证文件内容时，大容量导入页会**成功上传文件**。 如果有错误，必须修复这些错误，然后才能提交作业。
1. 当你的文件通过验证时，请选择 "**提交**" 以启动 Azure 批量操作，该操作将从组中删除组成员。
1. 删除操作完成后，会看到一条通知，指出批量操作已成功。

## <a name="check-removal-status"></a>检查删除状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![大容量操作的 "结果" 页显示大容量请求状态](./media/groups-bulk-remove-members/bulk-center.png)

有关大容量操作中每个行项的详细信息，请选择 **# Success**、 **# 失败**或**请求总数**列下的值。 如果发生失败，则会列出失败的原因。

## <a name="bulk-removal-service-limits"></a>批量删除服务限制

要从中删除组成员列表的每个批量活动最多可以运行一小时。 这允许删除至少40000个成员的列表。

## <a name="next-steps"></a>后续步骤

- [大容量导入组成员](groups-bulk-import-members.md)
- [下载组的成员](groups-bulk-download-members.md)
- [下载所有组的列表](groups-bulk-download.md)
