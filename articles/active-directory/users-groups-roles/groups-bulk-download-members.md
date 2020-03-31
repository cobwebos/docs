---
title: 批量下载组成员身份列表 - Azure 活动目录门户 |微软文档
description: 在 Azure 管理中心批量添加用户。
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
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517159"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>批量下载 Azure 活动目录中的组（预览）成员

使用 Azure 活动目录 （Azure AD） 门户，可以将组织中组的成员批量下载到逗号分隔的值 （CSV） 文件。

## <a name="to-bulk-download-group-membership"></a>批量下载组成员身份

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以批量下载其拥有的组的成员。
1. 在 Azure AD 中，选择 **"对所有** > **组进行**分组"。
1. 打开要下载其成员资格的组，然后选择 **"成员**"。
1. 在 **"成员"** 页上，选择 **"下载成员"** 以下载列出组成员的 CSV 文件。

   !["下载成员"命令位于组的配置文件页上](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>检查下载状态

您可以在**批量操作结果（预览）** 页中查看所有挂起的批量请求的状态。

   ![批量操作结果页显示批量请求状态](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>批量下载服务限制

下载组成员列表的每个批量活动最多可以运行一小时。 这使您能够下载至少 500，000 个成员的列表。

## <a name="next-steps"></a>后续步骤

- [批量导入组成员](groups-bulk-import-members.md)
- [批量删除组成员](groups-bulk-download-members.md)
