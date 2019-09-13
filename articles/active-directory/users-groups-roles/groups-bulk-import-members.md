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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911004"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory 中的批量导入组成员（预览）

使用 Azure Active Directory （Azure AD）门户，你可以通过使用逗号分隔值（CSV）文件将大量成员添加到组中，从而大容量导入组成员。

> [!NOTE]
> Azure AD 批量操作是 Azure AD 的公共预览功能，适用于任何付费的 Azure AD 许可计划。 有关预览版使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="bulk-import-service-limits"></a>大容量导入服务限制

要导入组成员列表的每个批量活动最多可以运行一小时。 这将允许至少导入40000个成员的列表。

## <a name="to-bulk-import-group-members"></a>大容量导入组成员

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以大容量导入其拥有的组的成员。
1. 在 Azure AD 中，选择 "**组** > " "**所有组**"。
1. 打开要向其中添加成员的组，然后选择 "**成员**"。
1. 在 "**成员**" 页上，选择 "**导入成员**"，以下载、更新和上传一个 CSV 文件，其中列出了要导入到组中的成员。

   !["导入成员" 命令位于组的配置文件页上](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>检查导入状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![大容量操作的 "结果" 页显示大容量请求状态](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>后续步骤

- [批量删除组成员](groups-bulk-remove-members.md)
- [下载组的成员](groups-bulk-download-members.md)
