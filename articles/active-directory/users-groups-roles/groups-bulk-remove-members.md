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
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911084"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>批量删除组成员（预览） Azure Active Directory

使用 Azure Active Directory （Azure AD）门户，你可以通过使用逗号分隔值（CSV）文件批量删除组成员，从组中删除大量成员。

> [!NOTE]
> Azure AD 批量操作是 Azure AD 的公共预览功能，适用于任何付费的 Azure AD 许可计划。 有关预览版使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="bulk-removal-service-limits"></a>批量删除服务限制

要从中删除组成员列表的每个批量活动最多可以运行一小时。 这允许删除至少40000个成员的列表。

## <a name="to-bulk-remove-group-members"></a>批量删除组成员

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以批量删除其拥有的组的成员。
1. 在 Azure AD 中，选择 "**组** > " "**所有组**"。
1. 打开要从中删除成员的组，然后选择 "**成员**"。
1. 在 "**成员**" 页上，选择 "**删除成员**"，以下载、更新和上传一个 CSV 文件，其中列出了要从组中删除的成员。

   !["删除成员" 命令位于组的配置文件页上](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>检查删除状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![大容量操作的 "结果" 页显示大容量请求状态](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>后续步骤

- [大容量导入组成员](groups-bulk-import-members.md)
- [下载组的成员](groups-bulk-download-members.md)
