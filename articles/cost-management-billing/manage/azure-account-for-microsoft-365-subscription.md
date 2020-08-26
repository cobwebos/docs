---
title: 使用 Azure 帐户注册 Microsoft 365
description: 了解如何使用 Azure 帐户创建 Microsoft 365 订阅。 还可以将现有的 Azure 和 Microsoft 365 帐户彼此关联。
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: banders
ms.openlocfilehash: 050bd9c420ccecdb91a6c438782d9be1c6c23b88
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461878"
---
# <a name="sign-up-for-a-microsoft-365-subscription-with-your-azure-account"></a>使用 Azure 帐户注册 Microsoft 365 订阅

如果你是 Azure 订阅者，则可以使用 Azure 帐户注册 Microsoft 365 订阅。 如果你是拥有 Azure 订阅的组织的成员，可在现有 Azure Active Directory (Azure AD) 中为用户创建 Microsoft 365 订阅。 使用帐户（必须在 Azure Active Directory 租户中具有全局管理员或计费管理员权限）注册 Microsoft 365。 有关详细信息，请参阅[在 Azure AD 中检查我的帐户权限](#RoleInAzureAD)和[在 Azure Active Directory 中分配管理员角色](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

如果已具有 Microsoft 365 帐户和 Azure 订阅，则可以[将 Microsoft 365 租户与 Azure 订阅关联](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>使用 Azure 帐户获取 Microsoft 365 订阅

1. 转到 [Microsoft 365 产品页](https://www.microsoft.com/microsoft-365/business/all-business)，并选择计划。
2. 在页面右上角单击“登录”。

    ![Microsoft 365 试用页的屏幕截图](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. 使用 Azure 帐户凭据登录。 如果正在为组织创建订阅，用于登录的 Azure 帐户需是 Azure Active Directory 租户中全局管理员或计费管理员目录角色的成员。

    ![Microsoft 登录的屏幕截图](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. 单击“立即试用”。

    ![确认 Microsoft 365 订单的屏幕截图。](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. 在订单收据页上，单击“继续”。

    ![Microsoft 365 订单收据的屏幕截图](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

现在已全部准备就绪。 如果已为组织创建 Microsoft 365 订阅，请使用以下步骤检查确认 Azure AD 用户现已在 Microsoft 365 中。

1. 打开 Microsoft 365 管理中心。
2. 展开“用户”，并单击“活动用户”。 

    ![Microsoft 365 管理中心用户的屏幕截图](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

注册完以后，Microsoft 365 订阅将添加到 Azure 订阅所属的同一 Azure Active Directory 实例中。 有关详细信息，请参阅[有关 Azure 和 Microsoft 365 订阅的详细信息](microsoft-365-account-for-azure-subscription.md#more-about-subs)和 [Azure 订阅与 Azure Active Directory 的关联方式](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>查看 Azure AD 中的帐户权限
1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 单击“所有服务”，并搜索“Active Directory”。

    ![Azure 门户中的 Active Directory 屏幕截图](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. 单击“用户和组” > “所有用户”。
4. 选择用户名。

    ![其中显示有 Azure Active Directory 用户的屏幕截图](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. 单击“目录角色”。

    ![其中显示有 Azure 门户目录角色的屏幕截图](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  需要“全局管理员”或“受限管理员” > “计费管理员”角色才可在现有 Azure Active Directory 中为用户创建 Microsoft 365 订阅  。

    ![显示 Azure 门户目录角色计费管理员的屏幕截图](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [将 Microsoft 365 租户与 Azure 订阅关联](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
