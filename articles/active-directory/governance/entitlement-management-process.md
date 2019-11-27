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
ms.date: 11/11/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: f336e9f2bdf1553a72bdc35fecc1b0b735fad274
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206907"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 权限管理中的请求流程和电子邮件通知

当用户向访问包提交请求时，进程将开始提供该访问请求。 在过程中发生重要事件时，Azure AD 的权利管理向审批者和请求者发送电子邮件通知。 本文介绍发送的请求过程和电子邮件通知。

## <a name="request-process"></a>请求过程

需要访问访问包的用户可以提交访问请求。 根据策略的配置，该请求可能需要审批。 批准请求后，将启动一个过程，以便为用户分配对访问包中每个资源的访问权限。 下图显示了进程和不同状态的概述：

![审批过程示意图](./media/entitlement-management-process/request-process.png)

| 状态 | 说明 |
| --- | --- |
| 已提交 | 用户提交了请求。 |
| 待审批 | 如果访问包的策略需要审批，请求将转为“等待审批”状态。 |
| Expired | 如果在审批请求超时期限内没有任何审批者审批请求，该请求将会过期。 若要重试，用户必须重新提交请求。 |
| 已拒绝 | 审批者拒绝了请求。 |
| 已批准 | 审批者批准了请求。 |
| 传送 | **尚未**为用户分配对访问包中所有资源的访问权限。 如果这是外部用户，则用户可能尚未访问资源目录。 它们还可能未接受同意提示。 |
| 已交货 | 已经为用户分配了对访问包中所有资源的访问权限。 |
| 访问权限已延期 | 如果策略中允许延期，则表示用户已延期分配。 |
| 访问权限已过期 | 用户对访问包的访问权限已过期。 若要重新获得访问权限，用户必须提交请求。 |

## <a name="email-notifications"></a>电子邮件通知

如果你是审批者，则会在你需要批准访问请求时向你发送电子邮件通知。 完成访问请求后，还会收到通知。 如果你是请求者，还会向你发送电子邮件通知，指出你的请求的状态。

以下关系图显示了将这些电子邮件通知发送到审批者或请求者的时间。 引用 "[电子邮件通知" 表](entitlement-management-process.md#email-notifications-table)，查找关系图中显示的电子邮件通知的相应数字。

### <a name="first-approvers-and-alternate-approvers"></a>首个审批者和备用审批者
下图显示了首个审批者和备用审批者的体验，以及他们在请求过程中收到的电子邮件通知：

![第一个和备用审批者流程](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>请求者
下图显示请求过程中请求者的体验和他们收到的电子邮件通知：

![请求者处理流](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2阶段审批
下图显示了阶段1和阶段2审批者的体验，以及他们在请求过程中收到的电子邮件通知：

![2阶段审批流程](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>电子邮件通知表
下表提供了有关这些电子邮件通知的更多详细信息。 若要管理这些电子邮件，你可以使用规则。 例如，在 Outlook 中，你可以创建规则，以将电子邮件移动到文件夹（如果主题包含此表中的字词）：

| # | 电子邮件主题 | 发送时间 | 收件人 |
| --- | --- | --- | --- |
| 1 | 必需的操作：按 *[date]* 批准或拒绝转发的请求 | 此电子邮件将发送到第1阶段备用审批者（在请求升级之后）以采取措施。 | 第1阶段备用审批者 |
| 2 | 必需的操作：按 *[date]* 批准或拒绝请求 | 如果已禁用呈报，此电子邮件将发送到第一个审批者，以采取措施。 | 首个审批者 |
| 3 | 提醒： [ *date]* 批准或拒绝 *[请求方]* 的请求 | 如果已禁用呈报，此提醒电子邮件将发送到第一个审批者。 电子邮件会要求他们执行操作（如果尚未这样做）。 | 首个审批者 |
| 4 | [ *Date* ] 上的 *[time]* 批准或拒绝请求 | 此电子邮件将发送到第一个审批者（如果启用了呈报）以采取措施。 | 首个审批者 |
| 5 | 需要操作提醒： [date] 批准或拒绝请求 *[* *date]* | 如果启用了呈报，此提醒电子邮件将发送到第一个审批者。 电子邮件会要求他们执行操作（如果尚未这样做）。 | 首个审批者 |
| 6 | *[Access_package]* 的请求已过期 | 此电子邮件将在请求过期后发送到第一个审批者和第1阶段备用审批者。 | 第一审批者，第1阶段备用审批者 |
| 7 | 向 *[access_package]* 的 *[请求方]* 批准的请求 | 请求完成后，此电子邮件将发送到第一个审批者和第1阶段备用审批者。 | 第一审批者，第1阶段备用审批者 |
| 8 | 向 *[access_package]* 的 *[请求方]* 批准的请求 | 当批准第1阶段请求时，此电子邮件将发送到第一个审批者和第1阶段的备用审批者。 | 第一审批者，第1阶段备用审批者 |
| 9 | 请求拒绝 *[access_package]* | 当请求被拒绝时，此电子邮件将发送给请求者 | 请求者 |
| 10 | 你的请求已过期 *[access_package]* | 此电子邮件将在一个或两个阶段请求结束时发送给请求者。 电子邮件通知请求者请求已过期。 | 请求者 |
| 11 | 必需的操作：按 *[date]* 批准或拒绝请求 | 如果已禁用呈报，此电子邮件将发送给第二个审批者，以采取措施。 | 第二审批者 |
| 12 | 需要操作提醒：按 *[date]* 批准或拒绝请求 | 如果已禁用呈报，此提醒电子邮件将发送给第二个审批者。 通知会询问他们尚未执行的操作。 | 第二审批者 |
| 13 | 所需操作：按 [ *date]* 批准或拒绝 [请求*方]* 请求 | 如果启用了升级，则此电子邮件将发送给第二个审批者，以采取措施。 | 第二审批者 |
| 14 | 需要操作提醒： [date] 批准或拒绝请求 *[* *date]* | 如果启用了呈报，此提醒电子邮件将发送给第二个审批者。 通知会询问他们尚未执行的操作。 | 第二审批者 |
| 15 | 必需的操作：按 *[date]* 批准或拒绝转发的请求 | 如果启用了升级，则此电子邮件将发送到第2阶段备用审批者，以采取措施。 | 第2阶段备用审批者 |
| 16 | 向 *[access_package]* 的 *[请求方]* 批准的请求 | 此电子邮件将发送给第二个审批者，并在批准请求时第2阶段备用审批者。 | 第二个审批者，第2阶段备用审批者 |
| 17 | *[Access_package]* 的请求已过期 | 请求过期后，此电子邮件将发送给第二个审批者或备用审批者。 | 第二个审批者，第2阶段备用审批者 |
| 18 | 你现在可以访问 *[access_package]* | 此电子邮件将发送给最终用户，以便开始使用他们的访问权限。 | 请求者 |
| 19 | 按 *[date]* 扩展 *[access_package]* 的访问权限 | 此电子邮件将在其访问权限到期之前发送给最终用户。 | 请求者 |
| 20 | *[Access_package]* 的访问已结束 | 此电子邮件将在其访问过期后发送给最终用户。 | 请求者 |

### <a name="access-request-emails"></a>访问请求电子邮件

当请求者提交配置为 "需要审批" 的访问包的访问请求时，添加到该策略的所有审批者将收到包含请求详细信息的电子邮件通知。 电子邮件中的详细信息包括：请求者的名称组织和业务理由;以及请求的访问开始和结束日期（如果已提供）。 详细信息还包括提交请求的时间和请求过期的时间。

电子邮件包含链接审批者可以单击以访问 "我的访问权限" 以批准或拒绝该访问请求。 下面是一个示例电子邮件通知，该通知将发送到第一个审批者或第二个审批者（如果启用了2阶段批准）来完成访问请求：

![批准对访问包的请求的电子邮件](./media/entitlement-management-shared/approver-request-email.png)

审批者还可以收到提醒电子邮件。 电子邮件会要求审批者对请求做出决定。 下面是审批者收到的电子邮件通知示例，提醒他们采取措施：

![提醒访问请求电子邮件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>备用审批者请求电子邮件

如果已启用备用审批者设置，并且请求仍处于挂起状态，则会将其转发。 备用审批者将收到一封电子邮件，以批准或拒绝该请求。 可以在阶段1和阶段2中启用备用审批者。 下面是备用审批者收到的通知的示例电子邮件：

![备用审批者请求电子邮件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

审批者和备用审批者都可以批准或拒绝该请求。

### <a name="approved-or-denied-emails"></a>已批准或已拒绝电子邮件

 当审批者收到请求者提交的访问请求时，可以批准或拒绝该访问请求。 审批者需要添加其决策的业务理由。 下面是批准请求后发送给审批者和备用审批者的电子邮件示例：

![访问包电子邮件的批准的请求](./media/entitlement-management-process/approver-request-email-approved.png)

批准访问请求并设置其访问权限后，会向请求者发送一封电子邮件通知，告知他们现在有权访问访问包。 下面是一个示例电子邮件通知，当他们被授予访问包访问权限时，该通知将发送给请求者：

![批准的请求者访问请求电子邮件](./media/entitlement-management-process/requestor-email-approved.png)

拒绝访问请求时，将向请求者发送电子邮件通知。 下面是一个示例电子邮件通知，当其访问请求被拒绝时，该通知将发送给请求者：

![请求者请求拒绝电子邮件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2阶段批准访问请求电子邮件

如果启用了2阶段批准，则至少有两个审批者必须批准请求，每个阶段一个，请求者可以接收访问权限。

在阶段1中，第一个审批者将收到访问请求电子邮件并做出决定。 如果他们批准请求，则第1阶段（如果启用了升级）中的所有第一个审批者和备用审批者将收到第1阶段完成的通知。 下面是在第1阶段完成时发送的通知的示例电子邮件：

![2阶段访问请求电子邮件](./media/entitlement-management-process/approver-request-email-2stage.png)

第一个或第二个审批者在第1阶段批准请求后，第2阶段开始。 在阶段2中，第二个审批者将收到访问请求通知电子邮件。 在阶段2中的第二个审批者或备用审批者（如果启用了升级功能）决定批准或拒绝该请求后，会将通知电子邮件发送给第一和第二个审批者，并将所有备用审批者发送到第1和第2阶段，以及请求者。

### <a name="expired-access-request-emails"></a>已过期访问请求电子邮件

如果没有审批者批准或拒绝请求，则访问请求可能会过期。 

当请求达到其配置的到期日期且过期后，审批者将无法再批准或拒绝该请求。 下面是发送到第一个、第二个（如果启用了2阶段批准）和备用审批者的通知的示例电子邮件：

![审批者已过期访问请求电子邮件](./media/entitlement-management-process/approver-request-email-expired.png)

电子邮件通知还会发送给请求者，通知他们其访问请求已过期，并且需要重新提交访问请求。 下图显示了请求者在请求扩展访问时收到的请求和电子邮件通知的体验：

![请求程序扩展访问处理流](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

下面是一个示例电子邮件通知，在其访问请求过期时将其发送给请求者：

![请求者已过期访问请求电子邮件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>后续步骤

- [请求访问访问包](entitlement-management-request-access.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)
