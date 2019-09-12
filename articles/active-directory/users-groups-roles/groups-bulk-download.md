---
title: 在 Azure Active Directory 门户中下载组的列表 |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 管理中心中批量下载组属性。
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
ms.openlocfilehash: 72f7776fde4558a49830ecbf14c77fa50e80bf36
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911067"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>在 Azure Active Directory 中批量下载组的列表（预览）

使用 Azure Active Directory （Azure AD）门户，你可以将组织中所有组的列表批量下载到逗号分隔值（CSV）文件中。

> [!NOTE]
> Azure AD 批量操作是 Azure AD 的公共预览功能，适用于任何付费的 Azure AD 许可计划。 有关预览版使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="bulk-download-service-limits"></a>大容量下载服务限制

用于创建组列表的每个批量活动最多可运行一小时。 这允许创建和下载至少300000组的列表。

## <a name="to-download-a-list-of-groups"></a>下载组列表

1. 使用组织中的管理员帐户登录到[Azure 门户](https://portal.azure.com)。
1. 在 Azure AD 中，选择 "**组** > " "**下载组**"。
1. 在 "**组下载**" 页上，选择 "**启动**" 以接收列出组的 CSV 文件。

   !["所有组" 页上的 "下载组" 命令](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>检查下载状态

你可以在 "**批量操作结果（预览版）** " 页中查看所有挂起的批量请求的状态。

   ![大容量操作的 "结果" 页显示大容量请求状态](./media/groups-bulk-download/bulk-center.png)

## <a name="next-steps"></a>后续步骤

- [批量删除组成员](groups-bulk-remove-members.md)
- [下载组的成员](groups-bulk-download-members.md)
