---
title: 批准或拒绝访问请求-Azure AD 的权利管理
description: 了解如何使用 "我的访问门户" 批准或拒绝对 Azure Active Directory 授权管理中访问包的请求。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261718"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>在 Azure AD 的权利管理中批准或拒绝访问请求

使用 Azure AD 权限管理，你可以配置策略以要求访问包的批准，并选择一个或多个审批者。 本文介绍指定的审批者如何批准或拒绝访问包的请求。

## <a name="open-request"></a>打开请求

批准或拒绝访问请求的第一步是查找并打开访问请求等待审批。 可以通过两种方式打开访问请求。

**必备角色：** 审批者

1. 查找 Microsoft Azure 要求批准或拒绝请求的电子邮件。 下面是示例电子邮件：

    ![批准访问包电子邮件的请求](./media/entitlement-management-shared/approver-request-email.png)

1. 单击 "**批准" 或 "拒绝请求**" 链接以打开访问请求。

1. 登录到 "我的访问门户"。

如果没有电子邮件，可以通过执行以下步骤来找到待审批的访问请求。

1. 在[https://myaccess.microsoft.com](https://myaccess.microsoft.com)登录到 "我的访问门户"。  （对于美国政府版，将 `myaccess.microsoft.us`"我的 Access 门户" 链接中的域。）

1. 在左侧菜单中，单击 "**审批**" 以查看等待批准的访问请求的列表。

1. 在 "**挂起**" 选项卡上，找到请求。

## <a name="approve-or-deny-request"></a>批准或拒绝请求

打开请求等待批准后，可以查看详细信息，以帮助你做出批准或拒绝决策。

**必备角色：** 审批者

1. 单击“查看”链接打开“访问请求”窗格。

1. 单击 "**详细信息**" 以查看有关访问请求的详细信息。

    详细信息包括用户的名称、组织、访问开始和结束日期（如果已提供）、业务理由、提交请求的时间以及请求将过期的时间。

1. 单击 "**批准**" 或 "**拒绝**"。

1. 如有必要，请输入原因。

    ![我的访问权限门户 - 访问请求](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. 单击“提交”以提交所做的决定。

    如果为策略配置了多个审批者，则只有一个审批者需要做出有关待审批的决策。 当审批者向访问请求提交决策后，请求将完成，并且其他审批者将不再提供批准或拒绝该请求。 其他审批者可以在我的访问门户中看到请求决策和决策者。 目前仅支持单阶段批准。

    如果所有配置的审批者都无法批准或拒绝访问请求，则请求将在配置的请求持续时间后过期。 用户收到通知，告知他们的访问请求已过期，需要重新提交访问请求。

## <a name="next-steps"></a>后续步骤

- [请求访问访问包](entitlement-management-request-access.md)
- [请求进程和电子邮件通知](entitlement-management-process.md)
