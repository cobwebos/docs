---
title: Azure AD 授权管理-Azure Active Directory 中的请求流程和电子邮件通知
description: 了解访问包的请求过程以及何时在 Azure Active Directory 的授权管理中发送电子邮件通知。
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
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199896"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 权限管理中的请求流程和电子邮件通知

当用户向访问包提交请求时，进程将开始提供该访问请求。 在过程中发生重要事件时，Azure AD 的权利管理向审批者和请求者发送电子邮件通知。 本文介绍发送的请求过程和电子邮件通知。

## <a name="request-process"></a>请求进程

需要访问包的用户可以提交访问请求。 根据策略的配置，请求可能需要批准。 批准请求后，进程将开始为用户分配对访问包中每个资源的访问权限。 下图显示了进程和不同状态的概述：

![审批流程关系图](./media/entitlement-management-process/request-process.png)

| 状况 | 描述 |
| --- | --- |
| 提交 | 用户提交请求。 |
| 待审批 | 如果访问包的策略需要审批，请求将移动到 "等待审批"。 |
| 已过期 | 如果审批请求超时时间内没有审批者批准请求，则请求将过期。 若要重试，用户必须重新提交请求。 |
| 拒绝 | 审批者拒绝请求。 |
| 已批准 | 审批者批准请求。 |
| 传送 | **尚未为**用户分配对访问包中所有资源的访问权限。 如果这是外部用户，则用户可能尚未访问资源目录并已接受同意提示。 |
| 已交货 | 已为用户分配访问包中所有资源的访问权限。 |
| 访问扩展 | 如果策略中允许使用扩展，则用户扩展了分配。 |
| 访问过期 | 用户对访问包的访问已过期。 若要重新获得访问权限，用户必须提交请求。 |

## <a name="email-notifications"></a>电子邮件通知

如果你是审批者，则当你需要批准访问请求和访问请求完成时，你将收到电子邮件通知。 如果你是请求者，则会向你发送电子邮件通知，指出你的请求的状态。

下图显示了将这些电子邮件通知发送到审批者或请求者的时间。 引用 "[电子邮件通知" 表](entitlement-management-process.md#email-notifications-table)，查找关系图中显示的电子邮件通知的相应数字。

### <a name="primary-approvers-and-alternate-approvers"></a>主审批者和备用审批者
下图显示了主审批者和备用审批者的体验，以及他们在请求过程中收到的电子邮件通知：

![主要和备用审批者流程](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>请求者
下图显示请求过程中请求者的体验和他们收到的电子邮件通知：

![请求者处理流](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>电子邮件通知表
下表提供了有关这些电子邮件通知的更多详细信息。 若要管理这些电子邮件，你可以使用规则。 例如，在 Outlook 中，你可以创建规则，以将电子邮件移动到文件夹（如果主题包含此表中的字词）：

| # | 电子邮件主题 | 发送时间 | 发送到 |
| --- | --- | --- | --- |
| 第 | 必需的操作：按 *[date]* 批准或拒绝转发的请求 | 此电子邮件将发送到第1阶段备用审批者（在请求升级之后）以采取措施。 | 第1阶段备用审批者 |
| 2 | 必需的操作：按 *[date]* 批准或拒绝请求 | 如果禁用呈报，此电子邮件将发送到第1阶段的主审批者，以采取措施。 | 阶段-1 主审批者 |
| 3 | 提醒： [ *date]* 批准或拒绝 *[请求方]* 的请求 | 如果 "已禁用升级"，则此提醒电子邮件将发送给第1阶段的主审批者，仅在他们尚未执行操作时才采取措施。 | 阶段-1 主审批者 |
| 4 | [ *Date* ] 上的 *[time]* 批准或拒绝请求 | 此电子邮件将发送到第1阶段（如果启用了升级），以采取措施。 | 阶段-1 主审批者 |
| 5 | 需要操作提醒： [date] 批准或拒绝请求 *[* *date]* | 如果启用了呈报功能，则此提醒电子邮件将发送给第1阶段的主审批者，仅在他们尚未采取操作时才采取措施。 | 阶段-1 主审批者 |
| 6 | 请求已过期 *[access_package]* | 请求过期后，此电子邮件将发送到单阶段或多阶段请求的第1个主审批者和/或第1阶段备用审批者。 | 阶段-1 个主审批者，第1阶段备用审批者 |
| 7 | 为 [*请求方]* 批准的请求 *[access_package]* | 完成请求后，此电子邮件将发送到第1阶段的主审批者和/或第1阶段备用审批者。 | 阶段-1 个主审批者，第1阶段备用审批者 |
| 8 | 为 [*请求方]* 批准的请求 *[access_package]* | 此电子邮件将发送到2阶段请求的第1个主审批者和/或第1阶段-第1个审批者，仅限在第1阶段获得批准。 | 阶段-1 个主审批者，第1阶段备用审批者 |
| 9 | 拒绝的请求 *[access_package]* | 只有在拒绝请求时，才会将此电子邮件发送给请求者 | 者 |
| 10 | 你的请求已过期 *[access_package]* | 此电子邮件将在请求过期后发送到单阶段或多阶段请求的第1阶段末尾的请求者。 | 者 |
| 18 | 你现在可以访问 *[access_package]* | 此电子邮件将发送给最终用户，以便开始使用他们的访问权限。 | 者 |
| 19 | *[Access_package]* 扩展对 [ *date]* 的访问 | 此电子邮件将在其访问权限过期之前发送给最终用户。 | 者 |
| 20 | 访问已结束 *[access_package]* | 此电子邮件将在其访问过期后发送给最终用户。 | 者 |

### <a name="access-request-emails"></a>访问请求电子邮件

当请求者提交配置为 "需要审批" 的访问包的访问请求时，添加到该策略的所有审批者将收到包含请求详细信息的电子邮件通知。 详细信息包括请求者的名称、组织、访问开始和结束日期（如果已提供）、业务理由、提交请求的时间以及请求将过期的时间。

电子邮件包含链接审批者可以单击以访问 Myaccess，以批准或拒绝该访问请求。 下面是一个示例电子邮件通知，当请求程序提交访问请求时，该通知将发送给审批者：

![批准访问包电子邮件的请求](./media/entitlement-management-shared/approver-request-email.png)

主审批者还会发送一封电子邮件通知，其中包含用于执行操作并制定请求决策的提醒。 下面是通知主审批者接收的电子邮件示例电子邮件，以提醒他们采取措施：

![提醒访问请求电子邮件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>备用审批者请求电子邮件

如果按转发策略启用了到备用审批者的转发，则请求仍处于挂起状态时，将转发该请求。 备用审批者将收到一封通知电子邮件以批准或拒绝该请求。 下面是备用审批者收到的通知的示例电子邮件：

![备用审批者请求电子邮件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

主审批者和备用审批者都可以批准或拒绝该请求。

### <a name="approved-or-denied-emails"></a>批准或拒绝的电子邮件

当请求获得批准并可用于访问，或当其访问请求被拒绝时，将通知请求者。 当审批者收到请求者提交的访问请求时，他们可以批准或拒绝该访问请求。 审批者需要为决策添加业务理由。 下面是在批准请求后发送给主要或备用审批者的电子邮件示例：

![查看访问请求电子邮件](./media/entitlement-management-process/approver-request-email-approved.png)

批准访问请求并设置其访问权限后，会向请求者发送一封电子邮件通知，告知他们现在有权访问访问包。 下面是一个示例电子邮件通知，当他们被授予访问包访问权限时，该通知将发送给请求者：

![过期的访问请求电子邮件](./media/entitlement-management-process/requestor-email-approved.png)

拒绝访问请求时，将向请求者发送电子邮件通知。 下面是一个示例电子邮件通知，当其访问请求被拒绝时，该通知将发送给请求者：

![请求者请求拒绝电子邮件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>过期的访问请求电子邮件

如果没有审批者批准或拒绝请求，则访问请求可能会过期。 

当请求达到其配置的到期日期且过期后，审批者将无法再批准或拒绝该请求。 下面是发送到所有主要和备用审批者的通知的示例电子邮件：

 ![审批者已过期访问请求电子邮件](./media/entitlement-management-process/approver-request-email-expired.png)

 电子邮件通知还会发送给请求者，通知他们其访问请求已过期，并且需要重新提交访问请求。 下面是一个示例电子邮件通知，在其访问请求过期时将其发送给请求者：

![请求者已过期访问请求电子邮件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>后续步骤

- [请求访问访问包](entitlement-management-request-access.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)
