---
title: 批准或拒绝请求的 Azure AD PIM-Azure Active Directory 中的角色 |Microsoft Docs
description: 了解如何批准或拒绝在 Azure AD Privileged Identity Management (PIM) 中的 Azure AD 角色的请求。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83cb38567feb51ba7959ada7730d66ded677bf9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476537"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>批准或拒绝在 PIM 中的 Azure AD 角色的请求

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以配置要用于激活、 需要批准的角色并选择一个或多个用户或组作为委派的审批者。 委派的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时的审批时间范围不可供配置。

按照这篇文章来批准或拒绝的 Azure AD 角色的请求中的步骤。

## <a name="view-pending-requests"></a>查看待处理请求

作为委派的审批者，您将获得电子邮件通知，当 Azure AD 角色请求正在等待你审批。 可在 PIM 中查看这些待处理的请求。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure AD 角色”。 

1. 单击“审批请求”  。

    ![Azure AD 角色的审批请求](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    将看到待审批的请求列表。

## <a name="approve-requests"></a>审批请求

1. 选中要审批的请求，然后单击“审批”以打开审批所选请求窗格  。

    ![批准请求列表，其中突出显示的批准选项](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在“审批原因”框中，键入原因  。

    ![批准所选的请求窗格中，批准原因](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 单击“批准”  。

    状态符号将随审批操作更新。

    ![单击批准按钮后批准所选的请求窗格](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒绝请求

1. 选中要拒绝的请求，然后单击“拒绝”以打开拒绝所选请求窗格  。

    ![批准请求列表，其中突出显示的拒绝选项](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在“拒绝原因”框中，键入原因  。

    ![拒绝所选的请求窗格中，拒绝原因](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 单击“拒绝”  。

    状态符号将随拒绝操作更新。

## <a name="next-steps"></a>后续步骤

- [PIM 中的电子邮件通知](pim-email-notifications.md)
- [在 PIM 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
