---
title: 管理对 Azure 账单信息的访问权限 | Microsoft Docs
description: ''
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: cwatson
ms.openlocfilehash: 7a4e19ae5bf770949623f4cee7fa0d3033ccfa29
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582609"
---
# <a name="manage-access-to-billing-information-for-azure"></a>管理对 Azure 账单信息的访问权限

就大多数订阅来说，可以在 Azure 门户中通过“订阅”将账单信息访问权限授予团队的成员。 如果你是签署企业协议的 Azure 客户（EA 客户），并且是企业管理员，则可在企业门户中将权限授予部门管理员和帐户所有者。

## <a name="give-access-to-billing"></a>授予对账单信息的访问权限

除 EA 客户之外的所有人员都可以授予对 Azure 账单信息的访问权限，方法是将下述用户角色之一分配给团队的成员：

- 帐户管理员
- 服务管理员
- 共同管理员
- 所有者
- 参与者
- 读取器
- 计费读者

若要分配角色，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

这些角色可以在 [Azure 门户](https://portal.azure.com/)中访问账单信息。 被分配了这些角色的人员也可使用[计费 API](billing-usage-rate-card-overview.md) 以编程方式获取发票和用法详细信息。 有关详细信息，请参阅 [Azure RBAC 中的角色](../role-based-access-control/built-in-roles.md)。

### <a name="opt-in"></a> 允许用户下载发票

在将适当角色分配给团队成员后，帐户管理员必须启用在 Azure 门户中下载发票所需的访问权限。 早于 2016 年 12 月的发票仅供帐户管理员查看。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 作为帐户管理员，请从 Azure 门户的[“订阅”边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择订阅。

1. 选择“发票”，并“访问发票”。

    ![显示如何委托对发票的访问权限的屏幕截图](./media/billing-manage-access/AA-optin.png)

1. 选择“启用”，然后进行保存。

    ![屏幕截图，显示用于委托对发票的访问权限的开关](./media/billing-manage-access/AA-optinAllow.png)

帐户管理员还可配置通过电子邮件发送发票。 若要了解详细信息，请参阅[在电子邮件中获取发票](billing-download-azure-invoice-daily-usage-date.md)。

## <a name="give-read-only-access-to-billing"></a>授予对账单信息的只读访问权限

将“账单读取者”角色分配给需要以只读方式访问订阅账单信息的人员，而不是需要 Azure 服务管理或创建功能的人员。 此角色适用于组织中负责 Azure 订阅的财务和成本管理的用户。

如果你是 EA 客户，则帐户所有者或部门管理员可以将“账单读取者”角色分配给团队成员。 但是，企业管理员必须在企业门户中启用“AO 视图费用”或“DA 视图费用”策略，该账单读取者才能查看部门或帐户的账单信息。

计费读取者功能处于预览状态，尚不支持非全局云。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 从 Azure 门户的[“订阅”边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择订阅。

1. 选择“访问控制 (IAM)”。
1. 选择“角色分配”以查看此订阅的所有角色分配。
1. 选择“添加” > “添加角色分配”。
1. 在“角色”下拉列表中，选择“账单读取者”。
1. 在“选择”文本框中，键入要添加的用户的名称或电子邮件。
1. 选择“保存”。
1. 片刻之后，会在订阅范围内为该用户分配“账单读取者”角色。
1. 账单读取者会收到一封电子邮件，其中包含登录链接。

    ![显示计费读取者可在 Azure 门户中所见内容的屏幕截图](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>允许部门管理员或帐户所有者访问账单信息

企业管理员可以允许部门管理员和帐户所有者查看与他们管理的部门和帐户相关联的使用详细信息和成本。

1. 以企业管理员身份登录到 [EA 门户](https://ea.azure.com/)。
1. 选择“管理”。
1. 在“注册”下将“DA 视图费用”更改为“启用”，以便部门管理员可以查看使用情况和成本。
1. 将“AO 视图费用”更改为“启用”，以便帐户所有者可以查看使用情况和成本。


有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。

## <a name="only-account-admins-can-access-account-center"></a>只有帐户管理员可以访问帐户中心

帐户管理员是订阅的合法所有者。 默认情况下，注册或购买 Azure 订阅的人员即为帐户管理员，除非[订阅所有权被转让](billing-subscription-transfer.md)给了其他人。 帐户管理员可以通过[帐户中心](https://account.azure.com/Subscriptions)创建订阅、取消订阅、更改订阅的账单地址和管理订阅的访问策略。

## <a name="next-steps"></a>后续步骤

- 其他角色的用户（如所有者或参与者）不仅可访问计费信息，还可访问 Azure 服务。 若要管理这些角色，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
- 有关角色的详细信息，请参阅 [Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果你有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
