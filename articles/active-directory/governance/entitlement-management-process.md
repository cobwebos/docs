---
title: 请求过程和通知 - Azure AD 权利管理
description: 了解访问包请求过程，以及 Azure Active Directory 权利管理中何时发送电子邮件通知。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80128527"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 权利管理中的请求过程和电子邮件通知

当用户将请求提交到访问包时，将启动一个传送该访问请求的过程。 如果在此过程中发生重要事件，Azure AD 权利管理会向审批者和请求者发送电子邮件通知。 本文介绍请求过程以及发送的电子邮件通知。

## <a name="request-process"></a>请求过程

需要访问访问包的用户可以提交访问请求。 根据策略的配置，该请求可能需要审批。 批准请求后，将启动一个过程，以便为用户分配对访问包中每个资源的访问权限。 下图显示了该过程和不同状态的概览：

![审批过程示意图](./media/entitlement-management-process/request-process.png)

| 状态 | 描述 |
| --- | --- |
| 已提交 | 用户提交了请求。 |
| 待审批 | 如果访问包的策略需要审批，请求将转为“等待审批”状态。 |
| Expired | 如果在审批请求超时期限内没有任何审批者审批请求，该请求将会过期。 若要重试，用户必须重新提交请求。 |
| 拒绝 | 审批者拒绝了请求。 |
| 已批准 | 审批者批准了请求。 |
| 传送 | **尚未**为用户分配对访问包中所有资源的访问权限。 如果这是外部用户，则用户可能尚未访问资源目录。 并且他们可能尚未接受同意提示。 |
| 已交货 | 已经为用户分配了对访问包中所有资源的访问权限。 |
| 访问权限已延期 | 如果策略中允许延期，则表示用户已延期分配。 |
| 访问权限已过期 | 用户对访问包的访问权限已过期。 若要重新获得访问权限，用户必须提交请求。 |

## <a name="email-notifications"></a>电子邮件通知

如果你是审批者，则在你需要批准访问请求时将收到电子邮件通知。 完成访问请求时，你也会收到通知。 如果你是请求者，你也会收到电子邮件通知，其中指出了请求的状态。

下图显示了向审批者或请求者发送这些电子邮件通知的时间。 参考[电子邮件通知表](entitlement-management-process.md#email-notifications-table)，查找图表中显示的电子邮件通知的对应数字。

### <a name="first-approvers-and-alternate-approvers"></a>第一个审批者和候补审批者
下图显示了第一个审批者和候补审批者的体验，以及他们在请求过程中收到的电子邮件通知：

![第一个审批者和候补审批者过程流](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>请求者
下图显示了请求者的体验，以及他们在请求过程中收到的电子邮件通知：

![请求者过程流](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>两阶段审批
下图显示了第一阶段和第二阶段审批者的体验，以及他们在请求过程中收到的电子邮件通知：

![两阶段审批过程流](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>电子邮件通知表
下表提供了有关这些电子邮件通知的更多详细信息。 若要管理这些电子邮件，可以使用规则。 例如，在 Outlook 中，可以创建规则，将电子邮件移动到文件夹（如果主题包含此表中的字词）：

| # | 电子邮件主题 | 发送时间 | 收件人 |
| --- | --- | --- | --- |
| 1 | 所需操作：在 [date] 之前批准或拒绝转发的请求 | 请求升级之后，第一阶段候补审批者将收到此电子邮件，提醒其采取操作。 | 第一阶段候补审批者 |
| 2 | 所需操作：在 [date] 之前批准或拒绝请求 | 如果已禁用升级，第一个审批者将收到此电子邮件，提醒其采取操作。 | 第一个审批者 |
| 3 | 提醒：在 [date] 之前批准或拒绝 [requestor] 的请求  | 如果已禁用升级，第一个审批者将收到此提醒电子邮件。 如果他们未采取任何操作，电子邮件将提醒他们采取操作。 | 第一个审批者 |
| 4 | 在 [date] [time] 之前批准或拒绝请求  | 如果已启用升级，第一个审批者将收到此电子邮件，提醒其采取操作。 | 第一个审批者 |
| 5 | 所需操作提醒：在 [date] 之前批准或拒绝 [requestor] 的请求  | 如果已启用升级，第一个审批者将收到此提醒电子邮件。 如果他们未采取任何操作，电子邮件将提醒他们采取操作。 | 第一个审批者 |
| 6 | [access_package] 的请求已过期 | 请求过期后，第一个审批者和第一阶段候补审批者将收到此电子邮件。 | 第一个审批者、第一阶段候补审批者 |
| 7 | [requestor] 对 [access_package] 的请求获得批准  | 请求完成时，第一个审批者和第一阶段候补审批者将收到此电子邮件。 | 第一个审批者、第一阶段候补审批者 |
| 8 | [requestor] 对 [access_package] 的请求获得批准  | 第一阶段请求获得批准时，第一个审批者和两阶段请求的第一阶段候补审批者将收到此电子邮件。 | 第一个审批者、第一阶段候补审批者 |
| 9 | 对 [access_package] 的请求被拒绝 | 当请求者的请求被拒绝时，这些请求者将收到此电子邮件 | 请求者 |
| 10 | 你的 [access_package] 请求已过期 | 单阶段或两阶段请求结束时，请求者将收到此电子邮件。 该电子邮件通知请求者请求已过期。 | 请求者 |
| 11 | 所需操作：在 [date] 之前批准或拒绝请求 | 如果已禁用升级，第二个审批者将收到此电子邮件，提醒其采取操作。 | 第二个审批者 |
| 12 | 所需操作提醒：在 [date] 之前批准或拒绝请求 | 如果已禁用升级，第二个审批者将收到此提醒电子邮件。 如果他们未采取任何操作，此通知将要求他们采取操作。 | 第二个审批者 |
| 13 | 所需操作：在 [date] 之前批准或拒绝 [requestor] 的请求  | 如果已启用升级，第二个审批者将收到此电子邮件，提醒其采取操作。 | 第二个审批者 |
| 14 | 所需操作提醒：在 [date] 之前批准或拒绝 [requestor] 的请求  | 如果已启用升级，第二个审批者将收到此提醒电子邮件。 如果他们未采取任何操作，此通知将要求他们采取操作。 | 第二个审批者 |
| 15 | 所需操作：在 [date] 之前批准或拒绝转发的请求 | 如果已启用升级，第二阶段候补审批者将收到此电子邮件，提醒其采取操作。 | 第二阶段候补审批者 |
| 16 | [requestor] 对 [access_package] 的请求获得批准  | 批准请求时，第二个审批者和第二阶段候补审批者将收到此电子邮件。 | 第二个审批者、第二阶段候补审批者 |
| 17 | [access_package] 的请求已过期 | 请求过期后，第二个审批者或候补审批者将收到此电子邮件。 | 第二个审批者、第二阶段候补审批者 |
| 18 | 现在你可以访问 [access_package] | 此电子邮件将发送给最终用户，以便他们可以开始使用自己的访问权限。 | 请求者 |
| 19 | 将 [access_package] 的访问权限延长至 [date]  | 在最终用户的访问权限过期之前，他们将收到此电子邮件。 | 请求者 |
| 20 | [access_package] 的访问权限已到期 | 在最终用户的访问权限过期之后，他们将收到此电子邮件。 | 请求者 |

### <a name="access-request-emails"></a>访问请求电子邮件

当请求者对配置为要求审批的访问包提交访问请求时，添加到策略中的所有审批者都会收到包含请求详细信息的电子邮件通知。 电子邮件中的详细信息包括：请求者的名称组织和业务理由；以及所请求的访问权限的开始和结束日期（如果已提供）。 此外，详细信息还包括提交请求的时间和请求过期的时间。

电子邮件包含一个链接，审批者单击该链接即可转到“我的访问权限”以批准或拒绝访问请求。 下面是一个发送给第一个审批者或第二个审批者（如果启用了两阶段审批）以完成访问请求的电子邮件通知示例：

![批准对访问包的请求的电子邮件](./media/entitlement-management-shared/approver-request-email.png)

审批者还可能收到一个提醒电子邮件。 该电子邮件会要求审批者对请求做出决定。 下面是审批者收到的提醒他们采取措施的电子邮件通知的示例：

![提醒访问请求电子邮件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>候补审批者请求电子邮件

如果已启用候补审批者设置，并且请求仍处于待处理状态，则会转发该电子邮件。 候补审批者将收到一封电子邮件，提醒其批准或拒绝该请求。 可以在第一阶段和第二阶段中启用候补审批者。 下面是候补审批者收到的通知的电子邮件示例：

![候补审批者请求电子邮件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

审批者和候补审批者都可以批准或拒绝该请求。

### <a name="approved-or-denied-emails"></a>已批准或已拒绝电子邮件

 当审批者收到请求者提交的访问请求时，可以批准或拒绝该访问请求。 审批者需要添加其决策的业务理由。 下面是请求获得批准后，审批者和候补审批者收到的电子邮件的示例：

![已批准对访问包的请求的电子邮件](./media/entitlement-management-process/approver-request-email-approved.png)

当访问请求获得批准并且其访问权限已进行了预配之后，请求者将收到一封电子邮件通知，告知他们现在有权访问访问包。 下面是在向请求者授予访问包的访问权限时，发送给请求者的示例电子邮件通知：

![“已批准请求者访问请求”的电子邮件](./media/entitlement-management-process/requestor-email-approved.png)

拒绝访问请求时，将向请求者发送电子邮件通知。 下面是拒绝请求者的访问请求时发送给请求者的示例电子邮件通知：

![“请求者请求被拒绝”的电子邮件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>两阶段审批访问请求电子邮件

如果启用了两阶段审批，则至少有两个审批者（每个阶段一个）必须批准请求，请求者才能收到访问权限。

在第一阶段中，第一个审批者将收到访问请求电子邮件并做出决定。 如果他们批准请求，则第一阶段（如果启用了升级）中的所有第一个审批者和候补审批者都将收到第一阶段已完成的通知。 下面是在第一阶段完成时发送的通知的电子邮件示例：

![两阶段访问请求电子邮件](./media/entitlement-management-process/approver-request-email-2stage.png)

当第一阶段中的第一个或候补审批者批准请求之后，第二阶段将开始。 在第二阶段中，第二个审批者将收到访问请求通知电子邮件。 在第二阶段中的第二个审批者或候补审批者（如果启用了升级）决定批准或拒绝该请求之后，第一个和第二个审批者、第一阶段和第二阶段中的所有候补审批者，以及请求者都将收到通知电子邮件。

### <a name="expired-access-request-emails"></a>已过期访问请求电子邮件

如果没有审批者批准或拒绝请求，访问请求可能会过期。 

当请求达到配置的到期日期并且过期时，审批者不再可以批准或拒绝该请求。 下面是所有第一个、第二个（如果启用了两阶段审批）和候补审批者收到的通知的电子邮件示例：

![审批者已过期访问请求电子邮件](./media/entitlement-management-process/approver-request-email-expired.png)

请求者也会收到电子邮件通知，告知他们其访问请求已过期，需要重新提交。 下图显示了请求者的体验，以及他们在请求延长访问时收到的电子邮件通知：

![请求者延长访问过程流](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

下面是请求者的访问请求过期时发送给请求者的示例电子邮件通知：

![“请求者已过期访问请求”电子邮件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>后续步骤

- [请求访问访问包](entitlement-management-request-access.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)
