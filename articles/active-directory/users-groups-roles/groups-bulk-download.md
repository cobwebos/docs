---
title: 在 Azure Active Directory 门户中下载组的列表 |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 管理中心中批量下载组属性。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c0fccf965f4a4c9d05d82601d75e314aba7d73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421682"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>批量下载 Azure Active Directory 中的组列表

使用) 门户 Azure AD Azure Active Directory (，你可以将组织中所有组的列表大容量下载到以逗号分隔的值 (CSV) 文件。

## <a name="to-download-a-list-of-groups"></a>下载组列表

1. 使用组织中的管理员帐户登录到 [Azure 门户](https://portal.azure.com) 。
1. 在 Azure AD 中，选择 "**组**" "  >  **下载组**"。
1. 在 " **组下载** " 页上，选择 " **启动** " 以接收列出组的 CSV 文件。

   !["所有组" 页上的 "下载组" 命令](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>检查下载状态

可在“批量操作结果”页面中查看所有挂起的批量请求的状态。

[![在“批量操作结果”页面中查看状态。](media/groups-bulk-download/bulk-center.png)](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>批量下载服务限制

下载组列表的每个批量活动最多可以运行一小时。 这使你可以下载至少300000个组的列表。

## <a name="next-steps"></a>后续步骤

- [批量删除组成员](groups-bulk-remove-members.md)
- [下载组成员](groups-bulk-download-members.md)
