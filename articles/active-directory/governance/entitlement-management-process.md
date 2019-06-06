---
title: 请求在 Azure AD 授权管理 （预览版）-Azure Active Directory 中的过程和电子邮件通知
description: 了解有关访问包并在 Azure Active Directory 权利管理 （预览版） 中发送电子邮件通知时的请求过程的信息。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: aede5e315141251026867f7028ebf989d44da4d5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473054"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>请求在 Azure AD 授权管理 （预览版） 中的过程和电子邮件通知

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

当用户提交对访问包的请求时，启动过程以传送该请求。 Azure AD 授权管理还将电子邮件通知发送到审批者和请求者时过程中出现关键事件。

本文介绍了请求过程中，以及发送的电子邮件通知。

## <a name="request-process"></a>请求过程

需要有权访问包的用户可以提交访问请求。 根据策略的配置，该请求可能需要审批。 在请求得到批准，就会开始将用户访问权限分配给访问包中每个资源。 下图显示了过程的不同状态的概述。

![审批流程关系图](./media/entitlement-management-process/request-process.png)

| 状态 | 描述 |
| --- | --- |
| 提交 | 用户提交请求。 |
| 待审批 | 如果访问包的策略需要审批，请求将移到挂起的审批。 |
| 已过期 | 如果没有审批者批准请求在审批请求超时时间内的，请求的截止日期。 若要重试，用户将必须重新提交其请求。 |
| 被拒绝 | 审批者拒绝请求。 |
| 已批准 | 审批者批准请求。 |
| 传送 | 用户已**不**已分配访问权限访问包中的所有资源。 如果这是外部用户，用户尚不具有访问资源目录，并接受权限提示。 |
| 已交货 | 用户有权访问包中的所有资源。 |
| 访问扩展 | 如果在策略中允许扩展，用户将扩展分配。 |
| 访问已过期 | 用户的包的访问权限的访问已过期。 若要访问同样，用户必须提交请求。 |

## <a name="email-notifications"></a>电子邮件通知

如果你是审批者，您需要批准的访问请求时，当访问请求已完成发送电子邮件通知。 如果你是请求者，将发送电子邮件通知，指示请求的状态。 下图显示了这些电子邮件通知时发送。

![授权管理电子邮件过程](./media/entitlement-management-process/email-notifications.png)

下表提供了有关每个这些电子邮件通知的更多详细信息。

| # | 电子邮件主题 | 发送时 | 发送到 |
| --- | --- | --- | --- |
| 第 | 所需操作：从评审访问权限请求 *[请求者]* 到 *[访问包]* 通过 *[date]* | 当请求者提交对访问包的请求 | 所有审批者 |
| 2 | 所需操作：从评审访问权限请求 *[请求者]* 到 *[访问包]* 通过 *[date]* | X 天之前审批请求超时 | 所有审批者 |
| 3 | 状态通知： *[请求者]* 的对访问请求 *[访问包]* 已过期 | 当审批者都做不批准或拒绝请求持续时间内的访问请求 | 请求者 |
| 4 | 状态通知： *[请求者]* 对访问请求 *[访问包]* 已完成 | 当第一个审批者批准或拒绝访问请求 | 所有审批者 |
| 5 | 你无法访问 *[访问包]* | 当请求程序已被拒绝访问包的访问权限 | 请求者 |
| 6 | 现在有权访问 *[访问包]*  | 当请求者拥有访问程序包中每个资源的访问权限 | 请求者 |
| 7 | 你的访问 *[访问包]* X 天后到期 | 请求者的包的访问权限的访问之前 X 天内到期 | 请求者 |
| 8 | 你的访问 *[访问包]* 已过期 | 访问包请求者的访问的到期时 | 请求者 |

### <a name="access-request-emails"></a>访问请求电子邮件

当请求者提交时配置为要求批准访问包的访问请求时，在策略中配置的所有审批者收到包含请求的详细信息的电子邮件通知。 详细信息包括请求者的名称、 组织、 访问开始和结束日期，如果提供，业务理由，以及时已提交请求，请求将过期。 电子邮件包含一个链接，审批者批准或拒绝访问请求的位置。 下面是一个简单的电子邮件通知请求者提交访问请求时发送给审批者。

![检查访问请求电子邮件](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>已批准或拒绝电子邮件

当其访问请求已经过批准且可用于访问，或拒绝其访问请求时，请求者会收到通知。 当审批者收到访问请求提交的请求者时，他们可以批准或拒绝访问请求。 审批者需要添加其决策的业务理由。

批准访问请求，授权管理启动授予对每个访问包中的资源的请求者访问权限的过程。 请求者拥有访问程序包中每个资源的访问权限后，电子邮件通知发送给请求者已批准其访问请求，他们现在拥有访问包的访问权限。 下面是当他们有权访问包发送至请求者的示例电子邮件通知。

当访问请求被拒绝时，电子邮件通知发送给请求者。 下面是拒绝其访问请求时发送给请求者的示例电子邮件通知。

### <a name="expired-access-request-emails"></a>过期访问请求电子邮件

当他们访问的请求已过期时，请求者会收到通知。 当请求者提交访问请求时，请求将具有请求持续时间之后将会失效。 如果不有任何审批者提交的批准/拒绝的决定，请求将会继续保留在挂起的审批状态。 当请求到达其已配置的过期持续时间时，请求已过期，并可以不再批准或拒绝审批者。 在这种情况下，请求将进入过期状态。 不再可以批准或拒绝请求过期。 电子邮件通知发送到请求者其访问请求已过期，并且他们需要重新提交访问请求。 下面是当他们访问的请求已过期时发送给请求者的示例电子邮件通知。

![过期访问请求电子邮件](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>后续步骤

- [请求的访问权限访问包](entitlement-management-request-access.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)
