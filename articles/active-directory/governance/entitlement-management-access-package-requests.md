---
title: 查看和管理 Azure AD 授权管理（预览版）中访问包的请求-Azure Active Directory
description: 了解如何在 Azure Active Directory 授权管理（预览版）中查看、重新处理和取消访问包的请求。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430279"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>查看和管理 Azure AD 授权管理（预览版）中访问包的请求

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在 Azure AD 授权管理中，可以看到谁已请求访问包、其策略和状态。 本文介绍如何查看、重新处理和取消访问包的请求。

## <a name="view-requests"></a>查看请求

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 单击 "**请求**"。

1. 单击特定的请求以查看更多详细信息。

    ![访问包的请求列表](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>查看请求的传递错误

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 单击 "**请求**"。

1. 选择要查看的请求。

    如果请求有任何传递错误，**将无法传递**或**部分传递**请求状态。

    如果有任何传递错误，则会在请求的详细信息窗格中出现传递错误计数。

1. 单击 "计数" 以查看请求的所有传递错误。

## <a name="reprocess-a-request"></a>重新处理请求

如果某一请求遇到错误，则可以重新处理该请求以重试。 您只能重新处理状态为 "传递" 的请求已**失败**或**部分传递**且完成时间不到一周。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 单击 "**请求**"。

1. 单击要重新处理的请求。

1. 在 "请求详细信息" 窗格中，单击 "重新处理**请求**"。

    ![重新处理失败的请求](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>取消挂起的请求

您只能取消尚未传递或传递失败的挂起的请求。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 单击 "**请求**"。

1. 单击要取消的请求。

1. 在 "请求详细信息" 窗格中，单击 "**取消请求**"。

## <a name="next-steps"></a>后续步骤

- [更改访问包的请求和审批设置](entitlement-management-access-package-request-policy.md)
- [查看、添加和删除访问包的分配](entitlement-management-access-package-assignments.md)