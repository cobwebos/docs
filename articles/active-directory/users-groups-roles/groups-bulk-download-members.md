---
title: 大容量下载组成员身份列表-Azure Active Directory 门户 |Microsoft Docs
description: 在 Azure 管理中心中批量添加用户。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81533662"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>在 Azure Active Directory 中批量下载组的成员

使用 Azure Active Directory （Azure AD）门户，你可以将组织中组的成员批量下载到逗号分隔值（CSV）文件中。

## <a name="to-bulk-download-group-membership"></a>大容量下载组成员身份

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以批量下载它们所拥有的组的成员。
1. 在 Azure AD 中，选择 "**组** > " "**所有组**"。
1. 打开要下载其成员身份的组，然后选择 "**成员**"。
1. 在 "**成员**" 页上，选择 "**下载成员**" 以下载列出组成员的 CSV 文件。

   !["下载成员" 命令位于组的配置文件页上](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>检查下载状态

你可以在 "**批量操作结果**" 页中查看所有挂起的批量请求的状态。

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>大容量下载服务限制

用于下载组成员列表的每个批量活动最多可运行一小时。 这使你可以下载至少500000个成员的列表。

## <a name="next-steps"></a>后续步骤

- [大容量导入组成员](groups-bulk-import-members.md)
- [批量删除组成员](groups-bulk-download-members.md)
