---
title: "Azure Active Directory B2C：常见问题 | Microsoft Docs"
description: "有关 Azure Active Directory B2C 的常见问题"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: bc0b0b652312f6bcc27a981da766e19cded7bd6c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017


---
# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C：常见问题
此页面解答了有关 Azure Active Directory (Azure AD) B2C 的常见问题。 请随时返回查看更新信息。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>我可以在基于员工的现有 Azure AD 租户中使用 Azure AD B2C 功能吗？
目前无法在现有的 Azure AD 租户中启用 Azure AD B2C 功能。 建议创建一个单独的租户，使用 Azure AD B2C 功能来管理客户。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>我可以使用 Azure AD B2C 提供 Office 365 的社交登录（Facebook 和 Google+）吗？
Azure AD B2C 不能与 Microsoft Office 365 一起使用。 一般来说，它不能用于向任何 SaaS 应用程序（Office 365、Salesforce、Workday等）提供身份验证。 它仅为面向使用者的 Web 和移动应用程序提供标识和访问管理，不适用于员工或合作伙伴的情况。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>什么是 Azure AD B2C 中的本地帐户？ 它们与 Azure AD 中的工作或学校帐户有何不同？
在 Azure AD 租户中，租户中的每个用户（使用现有 Microsoft 帐户的用户除外）都使用 `<xyz>@<tenant domain>` 形式的电子邮件地址登录，其中 `<tenant domain>` 是租户中的一个已验证域或初始 `<...>.onmicrosoft.com` 域。 此类型的帐户是工作或学校帐户。

在 Azure AD B2C 租户中，大多数应用都希望用户使用任意电子邮件地址（例如 joe@comcast.net、bob@gmail.com、sarah@contoso.com 或 jim@live.com）登录。 此类型的帐户是本地帐户。 目前，我们还支持任意用户名（仅纯字符串）作为本地帐户（例如，joe、bob、sarah 或 jim）。 可以在 Azure AD B2C 服务中选择这两种本地帐户类型中的一种。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>现在支持哪些社交标识提供者？ 计划在未来支持哪些？
目前支持 Facebook、Google+、LinkedIn 和 Amazon。 我们将根据客户需求添加对其他流行社交标志提供者的支持。

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>我可以配置范围，从各种社交标识提供者收集更多使用者的相关信息吗？
不可以，但此功能在我们的计划之中。 一组受支持的社交标识提供者使用的默认范围是：

* Facebook：电子邮件
* Google+：电子邮件
* Microsoft 帐户：openid 电子邮件配置文件
* Amazon：配置文件
* LinkedIn：r_emailaddress、r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>必须在 Azure 上运行应用程序才能将其与 Azure AD B2C 一起使用吗？
不，可以在任何位置（在云中或本地）托管应用程序。 只要能在公共可访问的端点上发送和接收 HTTP 请求，它就可以与 Azure AD B2C 进行交互。

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>我有多个 Azure AD B2C 租户。 如何在 Azure 门户上管理它们？
每个 Azure AD B2C 租户在 Azure 门户上都有其自己的 B2C 边栏选项卡。 请参阅 [Azure AD B2C：注册应用程序](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)，了解如何导航到 Azure 门户上特定租户的 B2C 功能边栏选项卡。 在大多数浏览器中，在 Azure 门户上的 Azure AD B2C 目录之间进行切换并不能保持 B2C 功能边栏选项卡始终开启。

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>如何自定义 Azure AD B2C 发送的验证电子邮件（内容和“发件人:”字段）？
可以使用[公司品牌功能](../active-directory/active-directory-add-company-branding.md)来自定义验证电子邮件的内容。 具体来说，可以自定义电子邮件的下列两个元素：

* **横幅徽标**：显示在右下角。
* **背景色**：显示在顶部。
  
    ![自定义验证电子邮件的屏幕截图](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

电子邮件签名包含首次创建 B2C 租户时提供的 B2C 租户名称。 可以使用以下说明更改名称：

* 以订阅管理员身份登录到 [Azure 经典门户](https://manage.windowsazure.com/)。
* 导航到 B2C 租户。
* 单击“ **配置** ”选项卡。
* 更改“目录属性”部分下的“名称”字段。
* 单击页面底部的“保存”  。

目前没有办法更改电子邮件中的“发件人:”字段。 如果对此功能以及完全自定义验证电子邮件的正文感兴趣，请在 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) 上投票支持相应功能。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>如何将我现有的用户名、密码和配置文件从数据库迁移到 Azure AD B2C？
可以使用 Azure AD 图形 API 编写迁移工具。 有关详细信息，请参阅[图形 API 示例](active-directory-b2c-devquickstarts-graph-dotnet.md)。 我们将在未来提供各种现成的迁移选项和工具。

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C 中的本地帐户使用什么密码策略？
本地帐户的 Azure AD B2C 密码策略以 Azure AD 的策略为基础。 Azure AD B2C 的注册、注册或登录和密码重置策略使用“强”密码强度，并且不会让任何密码过期。 有关详细信息，请阅读 [Azure AD 密码策略](https://msdn.microsoft.com/library/azure/jj943764.aspx)。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>我可以使用 Azure AD Connect 将存储在本地 Active Directory 中的使用者标识迁移到 Azure AD B2C 吗？
不可以，Azure AD Connect 不是为与 Azure AD B2C 一起使用而设计的。 我们将在未来提供各种现成的迁移选项和工具。

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>我的应用是否可在 iFrame 中打开 Azure AD B2C 页？
不可以，出于安全的考虑，无法在 iFrame 中打开 Azure AD B2C 页。  我们的服务将与浏览器通信以禁止此行为。  由于点击劫持的风险，安全社区和 OAUTH2 规范一般建议不要使用 iframe 进行标识体验。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C 是否可以与 Microsoft Dynamics 之类的 CRM 系统一起使用？
目前不可以。 集成这些系统在我们的计划之中。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C 是否可以与 SharePoint 本地 2016 或更早版本一起使用？
目前不可以。 Azure AD B2C 不支持在 SharePoint 本地上构建的门户和电子商务应用程序所需的 SAML 1.1 令牌。 请注意，Azure AD B2C 不适用于SharePoint 外部合作伙伴共享的情况；请转而参阅 [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx)。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>我应该使用 Azure AD B2C 还是 B2B 来管理外部标识？
请阅读有关[外部标识](../active-directory/active-directory-b2b-compare-external-identities.md)的文章，了解有关将适当功能应用于外部标识方案的详细信息。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C 提供哪些报告和审核功能？ 它们是否与 Azure AD Premium 中提供的功能相同？
否，Azure AD B2C 不支持与 Azure AD Premium 相同的报告集。 但是，有许多共性。  
* 登录报告提供每次登录的记录以及简短的详细信息。  
* 审核报告在 Azure 门户中的“Azure Active Directory”>“ACTIVITY-Audit日志”>“选择 B2C 并根据需要应用筛选器”下面提供。 其中包括管理活动和应用程序活动。 
* 包括用户数、登录次数和 MFA 数量的使用情况报告在“使用情况报告 API”中提供[](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-usage-reporting-api)

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>我可以本地化 Azure AD B2C 所提供页面的 UI 吗？ 支持哪些语言？
目前，Azure AD B2C 仅针对英语进行了优化。 我们计划尽快推出本地化功能。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>我可以在 Azure AD B2C 提供的注册和登录页面上使用自己的 URL 吗？ 例如，可以将 URL 从 login.microsoftonline.com 更改为 login.contoso.com 吗？
目前不可以。 该功能在我们的计划之中。 另请注意，在 Azure 经典门户上租户的“域”选项卡中验证域将不会执行此操作。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>如何删除 Azure AD B2C 租户？
请按照以下步骤删除 Azure AD B2C 租户：

* 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
* 导航到“应用程序”、“标识提供者”、“所有策略”边栏选项卡，然后删除其中的所有条目。
* 现在，以订阅管理员身份登录到 [Azure 经典门户](https://manage.windowsazure.com/) 。 （这是在注册 Azure 时使用的同一工作或学校帐户，或同一 Microsoft 帐户。）
* 导航到左侧的 Active Directory 扩展，然后单击 B2C 租户。
* 单击“用户”选项卡。
* 依次选择每个用户（排除当前登录的用户，即订阅管理员）。 单击页面底部的“删除”，并在出现提示时单击“是”。
* 单击“应用程序”选项卡。
* 在“显示”下拉菜单中选择“我公司拥有的应用程序”，然后单击复选标记。
* 将会看到下面列出了一个名为 **b2c-extensions-app** 的应用程序。 单击页面底部的“删除”，并在出现提示时单击“是”。
* 再次导航到 Active Directory 扩展，并选择 B2C 租户。
* 单击页面底部的“删除”。 按照屏幕上的说明完成该过程。

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>我可以将 Azure AD B2C 作为企业移动性套件的一部分吗？
不，Azure AD B2C 是即用即付 Azure 服务，不是企业移动套件的一部分。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>如何报告 Azure AD B2C 存在的问题？
请参阅[提出针对 Azure Active Directory B2C 的支持请求](active-directory-b2c-support.md)。

## <a name="more-information"></a>详细信息
你可能也想要查看当前的[服务限制、制约和约束](active-directory-b2c-limitations.md)。


