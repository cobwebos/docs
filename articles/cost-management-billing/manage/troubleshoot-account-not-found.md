---
title: 在 Azure 门户中查看计费帐户时遇到问题
description: 本文将帮助你解决尝试在 Azure 门户中查看计费帐户时遇到的问题。
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: banders
ms.openlocfilehash: 671189a6a328a77653b61542e3e30c848a75cb65
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85364251"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>在 Azure 门户中查看计费帐户时遇到问题

当你注册使用 Azure 时，系统将为你创建计费帐户。 可以使用计费帐户管理发票、付款并跟踪成本。 可以访问多个计费帐户。 例如，可以注册 Azure 供个人使用。 也可通过组织的企业协议或 Microsoft 客户协议访问 Azure。 每个这样的方案都会有单独的计费帐户。 本文将帮助你解决尝试在 Azure 门户中查看计费帐户时遇到的问题。

可以在 [Azure 成本管理 + 计费](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade)页中查看你的计费帐户。

若要详细了解计费帐户并确定自己的计费帐户类型，请参阅[在 Azure 门户中查看计费帐户](view-all-accounts.md)。

如果无法在 Azure 门户中查看计费帐户，请尝试以下选项：

## <a name="sign-in-to-a-different-tenant"></a>登录到其他租户

计费帐户与单个 Azure Active Directory 租户相关联。 如果你登录到不正确的租户，则在“成本管理 + 计费”页中看不到计费帐户。 使用以下步骤切换到 Azure 门户中的其他租户，并查看该租户中的计费帐户。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择页面右上角的个人资料（电子邮件地址）。
1. 选择“切换目录”。  
    ![显示在门户中选择切换目录的屏幕截图](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. 从“所有目录”部分下选择一个目录。  
    ![显示在门户中选择目录的屏幕截图](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>使用其他电子邮件地址登录

某些用户有多个电子邮件地址可登录到 [Azure 门户](https://portal.azure.com)。 并非所有电子邮件地址都可以访问计费帐户。 如果你使用具有资源管理权限但没有查看计费帐户权限的电子邮件地址登录，则在 Azure 门户中的[成本管理 + 计费](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade)页中将看不到计费帐户。

使用具有计费帐户访问权限的电子邮件地址登录到 Azure 门户，以访问计费帐户。

## <a name="sign-in-with-a-different-identity"></a>使用其他标识登录

某些用户有两个相同电子邮件地址的标识 - 工作或学校帐户和个人帐户。 通常，其中只有一个标识具有查看计费帐户的权限。 一个电子邮件地址可能有两个标识。 当你使用无权查看计费帐户的标识登录时，在[成本管理 + 计费](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade)页中将看不到计费帐户。 使用以下步骤来切换标识：

1. 在 InPrivate/Incognito 窗口中，登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的电子邮件地址有两个标识，你将看到一个选择个人帐户或工作或学校帐户的选项。 选择其中一个帐户。
1. 如果在 Azure 门户的“成本管理 + 计费”页面中看不到计费帐户，请重复步骤 1 和 2 并选择其他标识。

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

阅读以下计费和订阅文章以帮助解决问题。

- [银行卡被拒绝](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [订阅登录问题](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [找不到任何订阅](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [企业成本视图已禁用](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
