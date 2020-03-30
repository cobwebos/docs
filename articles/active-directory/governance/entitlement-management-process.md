---
title: 请求流程&通知 - Azure AD 授权管理
description: 了解访问包的请求过程以及 Azure 活动目录授权管理中发送电子邮件通知的时间。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128527"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中请求进程和电子邮件通知

当用户向访问包提交请求时，进程将开始传递该访问请求。 Azure AD 授权管理在过程中发生关键事件时向审批者和请求者发送电子邮件通知。 本文介绍了请求过程和发送的电子邮件通知。

## <a name="request-process"></a>请求过程

需要访问访问包的用户可以提交访问请求。 根据策略的配置，该请求可能需要审批。 批准请求后，将启动一个过程，以便为用户分配对访问包中每个资源的访问权限。 下图显示了流程和不同状态的概述：

![审批过程示意图](./media/entitlement-management-process/request-process.png)

| 状态 | 描述 |
| --- | --- |
| 已提交 | 用户提交了请求。 |
| 待审批 | 如果访问包的策略需要审批，请求将转为“等待审批”状态。 |
| 已过期 | 如果在审批请求超时期限内没有任何审批者审批请求，该请求将会过期。 若要重试，用户必须重新提交请求。 |
| 拒绝 | 审批者拒绝了请求。 |
| 已批准 | 审批者批准了请求。 |
| 传送 | **尚未**为用户分配对访问包中所有资源的访问权限。 如果这是外部用户，则用户可能尚未访问资源目录。 他们也可能不接受同意提示。 |
| 已交货 | 已经为用户分配了对访问包中所有资源的访问权限。 |
| 访问权限已延期 | 如果策略中允许延期，则表示用户已延期分配。 |
| 访问权限已过期 | 用户对访问包的访问权限已过期。 若要重新获得访问权限，用户必须提交请求。 |

## <a name="email-notifications"></a>电子邮件通知

如果您是审批人，则需要批准访问请求时向您发送电子邮件通知。 访问请求完成后，您还会收到通知。 如果您是请求者，还会向您发送电子邮件通知，指示请求的状态。

下图显示了何时将这些电子邮件通知发送给审批人或请求者。 引用[电子邮件通知表](entitlement-management-process.md#email-notifications-table)，查找图表中显示的电子邮件通知的相应编号。

### <a name="first-approvers-and-alternate-approvers"></a>第一批人和候补审批人
下图显示了第一批审批人和候补审批人的经验，以及他们在请求过程中收到的电子邮件通知：

![第一个和备用审批人流程流程](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>请求者
下图显示了请求者的经验以及他们在请求过程中收到的电子邮件通知：

![请求者流程](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2 阶段批准
下图显示了阶段 1 和阶段 2 审批者的经验以及他们在请求过程中收到的电子邮件通知：

![2 阶段审批流程流程](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>电子邮件通知表
下表提供了有关这些电子邮件通知的更多详细信息。 要管理这些电子邮件，可以使用规则。 例如，在 Outlook 中，如果主题包含此表中的单词，则可以创建规则将电子邮件移动到文件夹：

| # | 电子邮件主题 | 发送时间 | 收件人 |
| --- | --- | --- | --- |
| 1 | 所需操作：在 *[日期]* 前批准或拒绝转发的请求 | 此电子邮件将发送给阶段 1 备用审批人（在请求升级后）以执行操作。 | 阶段 1 备用审批人 |
| 2 | 所需操作：在 *[日期]* 前批准或拒绝请求 | 如果禁用上报，此电子邮件将发送给第一个审批人，以执行操作。 | 第一个审批者 |
| 3 | 提醒：在 *[日期]* 前批准或拒绝 *[请求者]* 请求 | 如果禁用上报，此提醒电子邮件将发送给第一个审批人。 电子邮件要求他们采取行动，如果他们没有。 | 第一个审批者 |
| 4 | 在 *[日期*] 上[*时间]* 批准或拒绝请求 | 此电子邮件将发送给第一个审批人（如果启用了上报）以执行操作。 | 第一个审批者 |
| 5 | 需要操作的提醒：在 *[日期]* 前批准或拒绝 *[请求者]* 请求 | 如果启用了上报，此提醒电子邮件将发送给第一个审批人。 电子邮件要求他们采取行动，如果他们没有。 | 第一个审批者 |
| 6 | *[access_package]* 的请求已过期 | 此电子邮件将在请求过期后发送给第一个审批人和阶段 1 备用审批人。 | 第一个审批人，第 1 阶段备用审批人 |
| 7 | [*请求者]* 请求 *[access_package]* | 此电子邮件将在请求完成时发送给第一个审批人和第 1 阶段备用审批人。 | 第一个审批人，第 1 阶段备用审批人 |
| 8 | [*请求者]* 请求 *[access_package]* | 当阶段-1 请求获得批准时，此电子邮件将发送给第一个审批人和阶段 1 备用审批人。 | 第一个审批人，第 1 阶段备用审批人 |
| 9 | 拒绝 *[access_package]* 的请求 | 此电子邮件将在请求被拒绝时发送给请求者 | 请求者 |
| 10 | 您的请求已过期 *[access_package]* | 此电子邮件将在单个或 2 阶段请求结束时发送给请求者。 电子邮件通知请求者请求已过期。 | 请求者 |
| 11 | 所需操作：在 *[日期]* 前批准或拒绝请求 | 如果禁用上报，此电子邮件将发送给第二个审批人，以执行操作。 | 第二个审批人 |
| 12 | 需要操作的提醒：在 *[日期]* 前批准或拒绝请求 | 如果禁用上报，此提醒电子邮件将发送给第二个审批人。 通知要求他们采取行动，如果他们还没有。 | 第二个审批人 |
| 13 | 所需操作：在 *[日期]* 前批准或拒绝 *[请求者]* 请求 | 如果启用上报，此电子邮件将发送给第二个审批人，以便执行操作。 | 第二个审批人 |
| 14 | 需要操作的提醒：在 *[日期]* 前批准或拒绝 *[请求者]* 请求 | 如果启用了上报，此提醒电子邮件将发送给第二个审批人。 通知要求他们采取行动，如果他们还没有。 | 第二个审批人 |
| 15 | 所需操作：在 *[日期]* 前批准或拒绝转发的请求 | 如果启用上报，此电子邮件将发送给阶段 2 备用审批人，以便执行操作。 | 阶段 2 备用审批人 |
| 16 | [*请求者]* 请求 *[access_package]* | 此电子邮件将在批准请求时发送给第二个审批人和第 2 阶段备用审批人。 | 第二个审批者，第 2 阶段备用审批人 |
| 17 | 请求已过期 *[access_package]* | 此电子邮件将在请求过期后发送给第二个审批人或候补审批人。 | 第二个审批者，第 2 阶段候补审批人 |
| 18 | 您现在可以访问 *[access_package]* | 此电子邮件将发送给最终用户，以开始使用他们的访问权限。 | 请求者 |
| 19 | 按 *[日期]* 扩展 *[access_package]* 的访问 | 此电子邮件将在最终用户访问到期之前发送给其。 | 请求者 |
| 20 | *[access_package]* 的访问已结束 | 此电子邮件将在最终用户的访问过期后发送给其。 | 请求者 |

### <a name="access-request-emails"></a>访问请求电子邮件

当请求者提交配置为需要审批的访问包的访问请求时，添加到策略中的所有审批者都将收到包含请求详细信息的电子邮件通知。 电子邮件中的详细信息包括：申请人的名称组织，以及业务理由;以及请求的访问开始日期和结束日期（如果提供）。 详细信息还包括提交请求的时间和请求何时过期。

电子邮件包含一个链接，审批者可以单击以转到"我的访问权限"以批准或拒绝访问请求。 下面是发送给第一个审批人或第二个审批人（如果启用了 2 阶段批准）以完成访问请求的电子邮件通知示例：

![批准对访问包的请求的电子邮件](./media/entitlement-management-shared/approver-request-email.png)

审批者也可以收到提醒电子邮件。 电子邮件要求审批人就请求作出决定。 下面是审批者收到的电子邮件通知示例，以提醒他们采取行动：

![提醒访问请求电子邮件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>备用审批人请求电子邮件

如果启用了备用审批人设置，并且请求仍然挂起，则将转发该请求。 候补审批人将收到一封电子邮件以批准或拒绝请求。 您可以在阶段 1 和阶段 2 中启用备用审批人。 下面是备用审批人收到的通知示例电子邮件：

![候补审批人请求电子邮件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

审批人和候补审批人都可以批准或拒绝请求。

### <a name="approved-or-denied-emails"></a>已批准或已拒绝电子邮件

 当审批者收到请求者提交的访问请求时，可以批准或拒绝该访问请求。 审批者需要添加其决策的业务理由。 以下是在批准请求后发送给审批人和候补审批人的示例电子邮件：

![访问包电子邮件的已批准请求](./media/entitlement-management-process/approver-request-email-approved.png)

当访问请求获得批准并预配其访问权限时，将通过电子邮件通知请求者，通知他们现在有权访问访问包。 下面是一个示例电子邮件通知，该通知在请求者被授予访问包访问权限时发送给他们：

![已批准的请求人访问请求电子邮件](./media/entitlement-management-process/requestor-email-approved.png)

拒绝访问请求时，将向请求者发送电子邮件通知。 下面是在请求者访问请求被拒绝时发送给其电子邮件通知的示例通知：

![请求者请求被拒绝的电子邮件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2 阶段审批访问请求电子邮件

如果启用了 2 阶段批准，则至少两个审批者必须批准请求，一个来自每个阶段，然后请求者才能接收访问权限。

在第 1 阶段期间，第一个审批者将收到访问请求电子邮件并做出决定。 如果他们批准请求，则阶段 1 中的所有先批准人和候补审批人（如果启用了上报）将收到阶段 1 已完成的通知。 下面是阶段 1 完成后发送的通知示例电子邮件：

![2 阶段访问请求电子邮件](./media/entitlement-management-process/approver-request-email-2stage.png)

在第一个或备用审批人在第 1 阶段批准请求后，阶段-2 开始。 在第 2 阶段期间，第二个审批人将收到访问请求通知电子邮件。 在第 2 阶段的第二个审批人或候补审批人（如果启用了上报）决定批准或拒绝请求后，通知电子邮件将发送给第一和第二批审批人，以及阶段 1 和阶段 2 中的所有备用审批人以及请求者。

### <a name="expired-access-request-emails"></a>已过期访问请求电子邮件

如果没有审批人批准或拒绝该请求，访问请求可能会过期。 

当请求达到其配置的到期日期并过期时，审批者无法再批准或拒绝该请求。 下面是发送给所有第一、第二（如果启用了 2 阶段批准）和备用审批人的通知的示例电子邮件：

![审批者过期访问请求电子邮件](./media/entitlement-management-process/approver-request-email-expired.png)

还会向请求者发送电子邮件通知，通知他们其访问请求已过期，并且他们需要重新提交访问请求。 下图显示了请求者的经验以及他们请求扩展访问权限时收到的电子邮件通知：

![请求者扩展访问流程流](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

下面是在访问请求过期时发送给请求者的电子邮件通知示例：

![请求者过期访问请求电子邮件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>后续步骤

- [请求访问访问包](entitlement-management-request-access.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)
