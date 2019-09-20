---
title: 大容量下载组成员身份列表-Azure Active Directory 门户 |Microsoft Docs
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
ms.openlocfilehash: 94c2ac1d662851b5a0b44ec475becb5f5e0403c4
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146398"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>在 Azure Active Directory 中批量下载组的成员（预览）

使用 Azure Active Directory （Azure AD）门户，你可以将组织中组的成员批量下载到逗号分隔值（CSV）文件中。

> [!NOTE]
> Azure AD 批量操作是 Azure AD 的公共预览功能，适用于任何付费的 Azure AD 许可计划。 有关预览版使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="to-bulk-download-group-membership"></a>大容量下载组成员身份

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以批量下载它们所拥有的组的成员。
1. 在 Azure AD 中，选择 "**组** > " "**所有组**"。
1. 打开要下载其成员身份的组，然后选择 "**成员**"。
1. 在 "**成员**" 页上，选择 "**下载成员**" 以下载列出组成员的 CSV 文件。

   !["下载成员" 命令位于组的配置文件页上](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>检查下载状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![大容量操作的 "结果" 页显示大容量请求状态](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>大容量下载服务限制

用于下载组成员列表的每个批量活动最多可运行一小时。 这使你可以下载至少500000个成员的列表。

## <a name="next-steps"></a>后续步骤

- [大容量导入组成员](groups-bulk-import-members.md)
- [批量删除组成员](groups-bulk-download-members.md)
