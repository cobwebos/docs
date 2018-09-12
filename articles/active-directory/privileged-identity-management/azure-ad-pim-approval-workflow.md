---
title: 在 PIM 中批准或拒绝 Azure AD 目录角色的请求 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中批准或拒绝 Azure AD 目录角色的请求。
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
ms.component: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9402824540f965cb89aa00791d093bd87712a89a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665836"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>在 PIM 中批准或拒绝 Azure AD 目录角色的请求

利用 Azure AD Privileged Identity Management (PIM)，可以将角色配置为需要审批才可激活，还可选择一个或多个用户或组作为委派的审批者。 按照本文中的步骤，审批或拒绝有关 Azure AD 目录角色的请求。

## <a name="view-pending-requests"></a>查看待处理请求

有 Azure AD 目录角色请求等待审批时，委派的审批者将收到电子邮件通知。 可在 PIM 中查看这些待处理的请求。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 目录角色”。

1. 单击“审批请求”。

    ![PIM Azure AD 目录角色 - 角色](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    将看到待审批的请求列表。

## <a name="approve-requests"></a>审批请求

1. 选中要审批的请求，然后单击“审批”以打开审批所选请求窗格。

    ![PIM 审批请求列表](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在“审批原因”框中，键入原因。

    ![PIM 审批所选请求](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 单击“批准”。

    状态符号将随审批操作更新。

    ![PIM 审批所选请求](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒绝请求

1. 选中要拒绝的请求，然后单击“拒绝”以打开拒绝所选请求窗格。

    ![PIM 审批请求列表](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在“拒绝原因”框中，键入原因。

    ![PIM 拒绝所选请求](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 单击“拒绝”。

    状态符号将随拒绝操作更新。

## <a name="next-steps"></a>后续步骤

- [PIM 中的电子邮件通知](pim-email-notifications.md)
- [在 PIM 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
