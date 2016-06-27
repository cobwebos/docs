<properties
	pageTitle="跨 Office 365 和 Azure 订阅共享单个 Azure AD 租户 | Azure"
	description="了解如何通过 Azure 订阅共享 Office 365 Azure AD 租户及其用户，或者反过来这样做"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="billing"
	ms.date="05/12/2016"
	wacn.date=""/>

# 对 Azure 订阅使用现有的 Office 365 帐户，或者相反
场景：你已经有了一个 Office 365 订阅，准备进行 Azure 订阅，但想要使用现有的 Office 365 用户帐户进行 Azure 订阅。或者，你是 Azure 订户，想要为现有 Azure Active Directory 中的用户获取 Office 365 订阅。本文介绍如何轻松地实现这两个目的。

> [AZURE.NOTE] 本文不适用于企业协议 (EA) 客户。

## 快速指南

- 如果你已经有了一个 Office 365 订阅，想要注册 Azure，则可使用“使用你的组织帐户登录”选项，然后继续通过 Office 365 帐户进行 Azure 注册。请参阅[详细步骤](#s1)。
- 如果你已经有了一个 Azure 订阅，想要获取 Office 365 订阅，则可使用 Azure 帐户登录到 Office 365，然后继续执行注册步骤。完成后，Office 365 订阅将添加到 Azure 订阅所属的同一 Azure Active Directory 中。请参阅[详细步骤](#s2)。

>[AZURE.NOTE] 若要获取 Office 365 订阅，你用于登录的帐户必须是 Azure AD 租户中全局管理员或计费管理员目录角色中的成员。[了解如何知道你的 Azure Active Directory 的角色](#how-to-know-your-role-in-your-azure-active-directory)

若要了解向帐户添加订阅的原理，请参阅本文后面的[背景信息](#background-information)。

## 详细步骤
<a id="s1"></a>
### 场景 1：Office 365 用户计划购买 Azure
在此场景中，我们假定 Kelley Wall 是一位拥有 Office 365 订阅且计划订阅 Azure 的用户。此外还有两位活动用户：Jane 和 Tricia。Kelley 的帐户是 admin@contoso.onmicrosoft.com。

![office365-users-admin-center.png](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

若要注册 Azure，请按以下步骤操作：

1. 在 [Azure.com](https://azure.microsoft.com/) 注册 Azure。单击“免费试用”。在下一页中，单击“立即开始”。

	![azure-signup-try-free](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. 单击“使用你的组织帐户登录”。

	![sign-in-to-azure](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. 使用你的 Office 365 帐户登录。在本示例中，它是 Kelley 的 Office 365 帐户。

	![sign-in-with-org-account](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. 填写信息并完成注册过程。

	![azure-sign-up-fill-information](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

5. 单击“开始管理我的服务”，然后即可继续操作。

	![azure-start-managing-my-service](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

你现在已全部准备就绪。在 Azure 门户中，你会看到相同的用户显示在相同的目录中。若要验证，请执行以下步骤：

1. 单击以上屏幕截图中的“开始管理我的服务”。
2. 单击“浏览”，然后单击“Active Directory”。

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. 单击“用户”。

	![azure-portal-ad-users-tab](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. 此时会按预期方式列出包括 Kelley 在内的所有用户。

	![azure-portal-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### 场景 2：Azure 用户计划购买 Office 365

在此场景中，Kelley Wall 是一位在帐户 admin@contoso.onmicrosoft.com 下拥有 Azure 订阅的用户。Kelley 想要订阅 Office 365 并使用与 Azure 所用目录相同的目录。

>[AZURE.NOTE] 若要获取 Office 365 订阅，你用于登录的帐户必须是 Azure AD 租户中全局管理员或计费管理员目录角色中的成员。[了解如何知道你的 Azure Active Directory 的角色](#how-to-know-your-role-in-your-azure-active-directory)

![azure-portal-settings-subscription](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![azure-portal-ads-users](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

若要订阅 Office 365，请按以下步骤操作：

1. 转到 [Office 365 产品页](https://products.office.com/business)，然后选择适合你的计划。
2. 选择计划后，将显示以下页面。请勿填写表单。单击页面右上角的“登录”。

	![office-365-trial-page](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. 使用帐户凭据登录。在本示例中，它是 Kelley 的帐户。

	![office-365-sign-in](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. 单击“立即试用”。

	![office-365-confirm-your-order](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. 在订单收据页上，单击“继续”。

	![office-365-order-receipt](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

你现在已全部准备就绪。在 Office 365 管理中心，你会看到 Contoso 目录中那些相同的用户以活动用户的形式显示。若要验证，请执行以下步骤：

1. 打开 Office 365 管理中心。
2. 展开“用户”，然后单击“活动用户”。

	![office-365-admin-center-users](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### 如何知道你在 Azure Active Directory 中的角色

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“浏览”，然后单击“Active Directory”。

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. 单击“用户”。

	![azure-portal-default-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. 单击该用户。在此示例中为 Kelley Wall。
5. 请注意“组织角色”字段。

	![azure-portal-user-identity](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## 背景信息
Office 365 和 Azure 使用 Azure Active Directory (AAD) 服务来管理用户和订阅。可以将 Azure 目录视为一个容器，你可以通过该容器对用户和订阅分组。若要将同一用户帐户用于 Microsoft Azure 和 Office 365 订阅，需确保这些订阅是在同一目录中创建的。

- 只能在目录下创建订阅，没有其他方法。
- 用户属于目录，不能属于其他。
- 订阅位于创建该订阅的用户的目录中。因此，当你使用 Azure 订阅的帐户来创建 Office 365 订阅时，Office 365 订阅就绑定到了 Azure 订阅的帐户。

![background-information](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

有关详细信息，请参阅 [Azure 订阅与 Azure Active Directory 的关联方式](/documentation/articles/active-directory-how-subscriptions-associated-directory)。

**说明：**

- Azure 订阅由目录中的各个用户所有。
- Office 365 订阅由目录本身所有。目录中的用户可以对这些订阅进行操作，前提是这些用户具有必需权限。

<!---HONumber=Mooncake_0620_2016-->