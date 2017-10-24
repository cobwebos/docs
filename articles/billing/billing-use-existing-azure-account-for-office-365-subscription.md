---
title: "使用 Azure 帐户注册 Office 365 | Microsoft Docs"
description: "了解如何使用 Azure 帐户创建 Office 365 订阅"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/03/2017
ms.author: cjiang
ms.openlocfilehash: 6965765757d9a20dfb542bbb3cfae1c30276c238
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>使用 Azure 帐户注册 Office 365 订阅
如果是 Azure 订阅者，则可以使用 Azure 帐户注册 Office 365 订阅。 如果是拥有 Azure 订阅的组织的成员，可在现有 Azure Active Directory (Azure AD) 中为用户创建 Office 365 订阅。 使用帐户（必须在 Azure Active Directory 租户中具有全局管理员或计费管理员权限）注册 Office 365 订阅。 有关详细信息，请参阅[在 Azure AD 中检查我的帐户权限](#RoleInAzureAD)和[在 Azure Active Directory 中分配管理员角色](../active-directory/active-directory-assign-admin-roles.md)。

如果已具有 Office 365 帐户和 Azure 订阅，则可以[将 Office 365 租户与 Azure 订阅关联](billing-add-office-365-tenant-to-azure-subscription.md)。

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>使用 Azure 帐户获取 Office 365 订阅

1. 转到 [Office 365 产品页](https://products.office.com/business)，并选择计划。
2. 在页面右上角单击“登录”。

    ![Office 365 试用版页面屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. 使用 Azure 帐户凭据登录。 如果正在为组织创建订阅，用于登录的 Azure 帐户需是 Azure Active Directory 租户中全局管理员或计费管理员目录角色的成员。

    ![Office 365 登录的屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. 单击“立即试用”。

    ![确认 Office 365 订单的屏幕截图。](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. 在订单收据页上，单击“继续”。

    ![Office 365 订单收据屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

现在已全部准备就绪。 如果已为组织创建 Office 365 订阅，请使用以下步骤检查确认 Azure AD 用户现已在 Office 365 中。

1. 打开 Office 365 管理中心。
2. 展开“用户”，并单击“活动用户”。

    ![Office 365 管理中心用户的屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

注册完以后，Office 365 订阅将添加到 Azure 订阅所属的同一 Azure Active Directory 实例中。 有关详细信息，请参阅[有关 Azure 和 Office 365 订阅的详细信息](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs)和 [Azure 订阅如何与 Azure Active Directory 关联](../active-directory/active-directory-how-subscriptions-associated-directory.md)。

## <a id="RoleInAzureAD"></a>查看 Azure AD 中的帐户权限
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“更多服务”，并搜索“Active Directory”。

    ![Azure 门户中的 Active Directory 屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. 单击“用户和组” > “所有用户”。
4. 选择用户名。 

    ![其中显示有 Azure Active Directory 用户的屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. 单击“目录角色”。
  
    ![其中显示有 Azure 门户目录角色的屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  “全局管理员”或“受限管理员” > “计费管理员”角色需在现有 Azure Active Directory 中为用户创建 Office 365 订阅。

    ![显示 Azure 门户目录角色计费管理员的屏幕截图](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。 
