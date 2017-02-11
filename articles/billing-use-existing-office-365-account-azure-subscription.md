---
title: "跨 Office 365 和 Azure 订阅共享单个 Azure AD 租户 | Microsoft Docs"
description: "了解如何通过 Azure 订阅共享 Office 365 Azure AD 租户及其用户，或者反过来这样做"
services: 
documentationcenter: 
author: JiangChen79
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: bf9cf92a53bd2db9970c03463cb417310e78224e
ms.openlocfilehash: edf236a3c9a28552b8c6831a8c7f57bab73c41ed


---
# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>对 Azure 订阅使用现有的 Office 365 帐户，或者相反
场景：用户已经有了一个 Office 365 订阅，准备进行 Azure 订阅，但想要使用现有的 Office 365 用户帐户进行 Azure 订阅。 或者，用户是 Azure 订户，想要为现有 Azure Active Directory 中的用户获取 Office 365 订阅。 本文介绍如何轻松地实现这两个目的。

> [!NOTE]
> 本文不适用于企业协议 (EA) 客户。 对于本文中的任何部分，如需更多帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
>
>

## <a name="quick-guidance"></a>快速指南
* 如果已经有了一个 Office 365 订阅并想要注册 Azure，可以使用“使用组织帐户登录”选项。 然后，继续通过 Office 365 帐户进行 Azure 注册。 请参阅[本文后面的详细步骤](#s1)。
* 如果已经有了一个 Azure 订阅并想要获取 Office 365 订阅，可以使用 Azure 帐户登录到 Office 365。 然后，继续执行注册步骤。 注册完以后，Office 365 订阅将添加到 Azure 订阅所属的同一 Azure Active Directory 实例中。 有关详细信息，请参阅[本文后面的详细步骤](#s2)部分。

> [!NOTE]
> 若要获取 Office 365 订阅，用于注册的帐户必须是 Azure Active Directory 租户中全局管理员或计费管理员目录角色的成员。 [了解如何确定 Azure Active Directory 中的角色](#how-to-know-your-role-in-your-azure-active-directory)。
>
>

若要了解向帐户添加订阅后发生的情况，请参阅本文后面的背景信息。

## <a name="detailed-steps"></a>详细步骤
<a id="s1"></a>

### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>场景 1：Office 365 用户计划购买 Azure
在此场景中，假定 Kelley Wall 是一位拥有 Office 365 订阅且计划订阅 Azure 的用户。 此外还有两位活动用户：Jane 和 Tricia。 Kelley 的帐户是 admin@contoso.onmicrosoft.com。

![Office 365 用户管理中心](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

若要注册 Azure，请按以下步骤操作：

1. 在 [Azure.com](https://azure.microsoft.com/) 注册 Azure。 单击“免费试用”。 在下一页中，单击“立即开始”。

    ![免费试用 Azure。](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)
2. 单击“使用组织帐户登录”。

    ![登录 Azure。](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)
3. 使用你的 Office 365 帐户登录。 在本示例中，它是 Kelley 的 Office 365 帐户。

    ![使用你的 Office 365 帐户登录。](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)
4. 填写信息并完成注册过程。

    ![填写信息并完成注册。](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![单击“开始管理我的服务”。](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

现在已全部准备就绪。 Azure 门户中会显示相同的用户。 若要验证，请执行以下步骤：

1. 在此前显示的屏幕中单击“开始管理我的服务”。
2. 单击“浏览”，然后单击“Active Directory”。

    ![单击“浏览”，然后单击“Active Directory”。](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. 单击“用户”。

    ![“用户”选项卡](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)
4. 此时会按预期方式列出包括 Kelley 在内的所有用户。

    ![用户列表](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>

### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>场景 2：Azure 用户计划购买 Office 365
在此场景中，Kelley Wall 是一位在 admin@contoso.onmicrosoft.com. 帐户下拥有 Azure 订阅的用户。Kelley 想要订阅 Office 365 并使用与 Azure 所用目录相同的目录。

> [!NOTE]
> 若要获取 Office 365 订阅，用于登录的帐户必须是 Azure Active Directory 租户中全局管理员或计费管理员目录角色的成员。 [了解如何知道 Azure Active Directory 中的角色](#how-to-know-your-role-in-your-azure-active-directory)。
>
>

![Azure 门户订阅设置](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Azure 门户 Active Directory 用户](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

若要订阅 Office 365，请按以下步骤操作：

1. 转到 [Office 365 产品页](https://products.office.com/business)，然后选择合适的计划。
2. 选择计划后，将显示以下页面。 请勿填写表单。 在页面右上角单击“登录”。

    ![Office 365 试用页](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)
3. 使用帐户凭据登录。 在本示例中，它是 Kelley 的帐户。

    ![Office 365 登录](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)
4. 单击“立即试用”。

    ![确认 Office 365 订单。](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)
5. 在订单收据页上，单击“继续”。

    ![Office 365 订单收据](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

现在已全部准备就绪。 在 Office 365 管理中心，会看到 Contoso 目录中的用户以活动用户的形式显示。 若要验证，请执行以下步骤：

1. 打开 Office 365 管理中心。
2. 展开“用户”，然后单击“活动用户”。

    ![Office 365 管理中心用户](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>如何知道你在 Azure Active Directory 中的角色
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“浏览”，然后单击“Active Directory”。

    ![Azure 门户中的 Active Directory](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. 单击“用户”。

    ![Azure 门户默认 Active Directory 用户](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)
4. 单击该用户。 在此示例中，用户为 Kelley Wall。

    请注意“组织角色”字段。

    ![Azure 门户用户标识](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>有关 Azure 和 Office 365 订阅的背景信息
Office 365 和 Azure 使用 Azure Active Directory 服务管理用户和订阅。 可以将 Azure 目录视为一个容器，在其中对用户和订阅分组。 若要将同一用户帐户用于 Azure 和 Office 365 订阅，需确保这些订阅是在同一目录中创建的。 请注意以下几点：

* 只能在目录下创建订阅，而不能在订阅中创建目录。
* 用户属于目录，不能属于其他。
* 订阅位于创建订阅的用户的目录中。 因此，使用 Azure 订阅的帐户创建 Office 365 订阅时，Office 365 订阅将绑定到同一 Azure 订阅帐户。

![背景信息](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

有关详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）。

> [!NOTE]
> Azure 订阅归目录中的各个用户所有。
>
> [!NOTE]
> Office 365 订阅归目录本身所有。 目录中的用户可以对这些订阅进行操作，前提是这些用户具有必需权限。
>
>

## <a name="next-steps"></a>后续步骤
如果在过去分别获得了 Azure 和 Office 365 订阅，现在想要能够从 Azure 订阅访问 Office 365 租户，则请参阅 [Associate an Office 365 tenant with an Azure subscription](billing-add-office-365-tenant-to-azure-subscription.md)（将 Office 365 租户与 Azure 订阅关联）。

> [!NOTE]
> 如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
>
>



<!--HONumber=Nov16_HO3-->


