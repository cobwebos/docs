---
title: Azure AD 授权管理 (预览版) 中的请求流程和电子邮件通知-Azure Active Directory
description: 了解访问包的请求过程以及何时在 Azure Active Directory 授权管理 (预览版) 中发送电子邮件通知。
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
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ad17f4c4d338e1d0b8118c1a172e0aaad7118e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489101"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Azure AD 授权管理 (预览版) 中的请求流程和电子邮件通知

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

当用户将请求提交到访问包时, 将启动一个进程来传送该请求。 如果在此过程中发生重要事件, Azure AD 的权利管理还会向审批者和请求者发送电子邮件通知。

本文介绍了请求过程以及发送的电子邮件通知。

## <a name="request-process"></a>请求进程

需要访问包的用户可以提交访问请求。 根据策略的配置, 请求可能需要批准。 批准请求后, 进程将开始为用户分配对访问包中每个资源的访问权限。 下图显示了进程和不同状态的概述。

![审批流程关系图](./media/entitlement-management-process/request-process.png)

| 状态 | 描述 |
| --- | --- |
| 已提交 | 用户提交请求。 |
| 待审批 | 如果访问包的策略需要审批, 请求将移动到 "等待审批"。 |
| 已到期 | 如果审批请求超时时间内没有审批者批准请求, 则请求将过期。 若要重试, 用户必须重新提交请求。 |
| 拒绝 | 审批者拒绝请求。 |
| 已审批 | 审批者批准请求。 |
| 正在传递 | 尚未为用户分配对访问包中所有资源的访问权限。 如果这是外部用户, 则用户尚未访问资源目录并已接受权限提示。 |
| 已发送 | 已为用户分配访问包中所有资源的访问权限。 |
| 访问扩展 | 如果策略中允许使用扩展, 则用户扩展了分配。 |
| 访问过期 | 用户对访问包的访问已过期。 若要重新获得访问权限, 用户必须提交请求。 |

## <a name="email-notifications"></a>电子邮件通知

如果你是审批者, 则当你需要批准访问请求和访问请求完成时, 你将收到电子邮件通知。 如果你是请求者, 则会向你发送电子邮件通知, 指出你的请求的状态。 下图显示了发送这些电子邮件通知的时间。

![权利管理电子邮件过程](./media/entitlement-management-process/email-notifications.png)

下表提供了有关这些电子邮件通知的更多详细信息。

| # | 电子邮件主题 | 发送时间 | 发送到 |
| --- | --- | --- | --- |
| 1 | 必需的操作:按 *[date]* 查看 [*请求方]* 到 *[access package]* 的访问请求 | 当请求程序提交访问包的请求时 | 所有审批者 |
| 2 | 必需的操作:按 *[date]* 查看 [*请求方]* 到 *[access package]* 的访问请求 | 批准请求超时前的 X 天 | 所有审批者 |
| 3 | 状态通知: *[请求方]* 对 *[access package]* 的访问请求已过期 | 当审批者未批准或拒绝请求持续时间内的访问请求时 | 请求者 |
| 4 | 状态通知: *[请求方]* 对 *[访问包]* 的访问请求已完成 | 第一个审批者批准或拒绝某个访问请求时 | 所有审批者 |
| 5 | 你已被拒绝访问 *[access package]* | 当请求者拒绝访问访问包时 | 请求者 |
| 6 | 你现在可以访问 *[access package]*  | 如果请求方已被授予访问包中每个资源的访问权限 | 请求者 |
| 7 | *[Access package]* 的访问权限在 X 天后过期 | 请求者访问访问包之前 X 天过期 | 请求者 |
| 8 | 你对 *[access package]* 的访问权限已过期 | 当请求者访问访问包过期时 | 请求者 |

### <a name="access-request-emails"></a>访问请求电子邮件

当请求者为配置为要求批准的访问包提交访问请求时, 在策略中配置的所有审批者都会收到包含请求详细信息的电子邮件通知。 详细信息包括请求者的名称、组织、访问开始和结束日期 (如果已提供)、业务理由、提交请求的时间以及请求将过期的时间。 电子邮件包含一个链接, 审批者可以在其中批准或拒绝访问请求。 下面是一个示例电子邮件通知, 当请求程序提交访问请求时, 该通知将发送给审批者。

![查看访问请求电子邮件](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>批准或拒绝的电子邮件

当请求获得批准并可用于访问, 或当其访问请求被拒绝时, 将通知请求者。 当审批者收到请求者提交的访问请求时, 他们可以批准或拒绝该访问请求。 审批者需要为决策添加业务理由。

当访问请求获得批准后, 授权管理将开始向请求者授予访问包中每个资源的访问权限。 向请求者授予访问包中每个资源的访问权限后, 将向请求者发送一封电子邮件通知, 告知其访问请求已获得批准, 并且他们现在有权访问该访问包。 下面是一个示例电子邮件通知, 该通知将在被授予访问包访问权限时发送给请求者。

拒绝访问请求时, 将向请求者发送电子邮件通知。 下面是一个示例电子邮件通知, 当其访问请求被拒绝时, 该通知将发送给请求者。

### <a name="expired-access-request-emails"></a>过期的访问请求电子邮件

当请求者的访问请求过期时, 会通知请求者。 请求程序提交访问请求时, 请求的持续时间将过期。 如果没有提交批准/拒绝决策的审批者, 请求将继续处于待定审批状态。 当请求到达其配置的过期时间时, 请求将过期, 并且审批者将无法再批准或拒绝该请求。 在这种情况下, 请求将进入过期状态。 无法再批准或拒绝过期的请求。 电子邮件通知将发送给请求者, 其访问请求已过期, 需要重新提交访问请求。 下面是一个示例电子邮件通知, 在其访问请求过期时将其发送给请求者。

![过期的访问请求电子邮件](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>后续步骤

- [请求访问访问包](entitlement-management-request-access.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)
