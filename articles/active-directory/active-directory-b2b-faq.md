---
title: "Azure Active Directory B2B 协作常见问题解答 | Microsoft 文档"
description: "有关 Azure Active Directory B2B 协作的常见问题"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/14/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4b7ed095729e810f7f1112d3b6becfaf186bf508
ms.lasthandoff: 03/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Azure Active Directory B2B 协作常见问题 (FAQ)

常见问题将定期更新以反映任何新兴趣。

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Azure 经典门户中是否提供此功能？
此 Azure AD B2B 协作公共预览版刷新中的新功能仅通过 [Azure 门户](https://portal.azure.com)和新的访问面板提供。 试试看！

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 协作用户能否访问 SharePoint Online 和 OneDrive？
B2B 协作来宾用户在目录中。 你可以将它们添加到有权访问 SharePoint Online 和 OneDrive 站点的组中，甚至可以直接从 SharePoint Online 的人员选取器中选取它们。 因为这些是来宾用户，SharePoint Online 站点必须已启用外部共享。

### <a name="is-the-csv-upload-mechanism-still-supported"></a>是否仍支持 CSV 上载机制？
是的。 请参阅我们提供的 PowerShell 示例。

### <a name="how-can-i-customize-my-invitation-emails"></a>我能否自定义邀请电子邮件？
使用 B2B 邀请 API，你几乎可以自定义有关邀请者过程的所有内容。

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>受邀的外部用户能否离开他受邀加入的组织？
目前在此公共预览版刷新中未提供此功能。

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>现在，多重身份验证 (MFA) 可用于来宾用户，他们是否也可以重置其 MFA 方法？
是的，方式与常规用户相同。

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>哪个组织负责发放 MFA 许可证？
邀请方组织是介入并执行 MFA 的组织。 因此，邀请方组织负责确保它们有足够的许可证可发放给要执行 MFA 的 B2B 用户。

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>如果我的合作伙伴组织已设置 MFA，怎么办？ 我们能否信任其 MFA 而不使用自己的 MFA？
在此公共预览版刷新中不支持，但我们将在未来版本中支持此功能。 发布该功能时，你将能够选择要从你的（邀请方组织的）MFA 中排除的特定合作伙伴。

### <a name="how-can-i-achieve-delayed-invitations"></a>如何实现延迟的邀请？
某些组织想要添加 B2B 协作用户，将其预配到需要预配的应用程序中，然后发送邀请。 如果你是这样，可以使用 B2B 协作邀请 API 自定义加入工作流。

### <a name="can-guest-users-and-contacts-co-exist"></a>来宾用户和联系人能否共存？
你的组织可能已添加代表外部协作者的联系人，因此他们会出现在全局地址列表中，并在电子邮件撰写期间作为电子邮件地址建议项。 你可能想知道现在将这些相同的协作者作为 B2B 协作用户添加到目录中，会发生什么情况？是吧？ 在未来版本中，B2B 协作用户和联系人对象将能够在你公司的目录中共存。 有关公告敬请期待！

### <a name="can-i-make-my-guest-users-limited-admins"></a>我能否将来宾用户设为受限制的管理员？
绝对是。 如果你的组织需要这样做，请在[将来宾用户添加到角色](active-directory-users-assign-role-azure-portal.md)中找到操作方法。

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 协作是否支持允许 B2B 用户访问 Azure 门户？
除非 B2B 协作用户被分配了受限制的管理员或全局管理员角色，否则他们应该不需要访问 Azure 门户。 在这种情况下，他们可以访问门户。 如果不具有这些角色的来宾用户访问门户，则他/她可以访问某些部分的体验，因为来宾用户角色具有目录中的某些权限，如前面部分中所述。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>我能否阻止来宾用户访问 Azure 门户？
能！ 但配置此策略时应小心，以避免意外阻止成员和管理员的访问。
你可以按照以下三个步骤通过设置基于 Windows Azure 服务管理 API 的条件访问策略，来阻止来宾用户访问 [Azure 门户](https://portal.azure.com)。
1. 修改“所有用户”组，使其仅包含成员 ![](media/active-directory-b2b-faq/modify-all-users-group.png)
2. 创建包含来宾用户的动态组 ![](media/active-directory-b2b-faq/group-with-guest-users.png)
3. 设置条件访问策略以阻止来宾用户访问门户，如以下视频中所示。

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player]

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Azure AD B2B 协作开始为 MFA 和使用者电子邮件帐户提供支持的日程表是怎样的？
在此公共预览版刷新中现在支持 MFA 和使用者电子邮件帐户。

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>Azure AD B2B 的正式发布日程表是什么？
我们何时进行此发布取决于当前功能集收到的客户反馈。

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>是否有计划支持 Azure AD B2B 协作用户的密码重置？
有，B2B 协作（来宾）用户都支持此功能。

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>是否也为病毒性租户中的用户启用此功能？
目前不可以。

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM 是否为 Azure AD B2B 协作提供联机支持？
CRM 将在 Azure AD B2B 协作可以广泛使用后为其提供支持。

### <a name="are-b2b-collaboration-guest-users-visible-in-sharepoint-onlineonedrive-people-picker"></a>B2B 协作来宾用户在 SharePoint Online/OneDrive 人员选取器中是否可见？
 
是！ 不过，默认情况下，搜索现有来宾用户的功能在 SharePoint Online 人员选取器中处于关闭状态以匹配旧行为。 你可以使用“ShowPeoplePickerSuggestionsForGuestUsers”设置在租户和网站集级别启用此功能。 这可以使用 Set-SPOTenant 和 Set-SPOSite cmdlet 进行设置，这将允许用户搜索目录中的所有现有来宾用户。 租户范围中的更改不会影响已经预配的 SharePoint Online 站点。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>新创建的 B2B 协作用户的初始密码的生存期是什么？
Azure AD 具有固定的字符集、密码强度和帐户锁定要求，同样适用于所有 Azure AD 云用户帐户。 云用户帐户是指未与其他标识提供者，例如 Microsoft 帐户、Facebook、ADFS 甚至另一个云租户（就 B2B 协作而言）联合的帐户。 对于联合帐户，密码策略取决于本地租户中的策略和用户的 Microsoft 帐户设置。

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>应用程序希望在其租户用户与来宾用户之间提供有差别的体验。 是否有针对此事项的标准指南？ 标识提供者声明是否是适用于此事项的确切模型？
 
如我们在 [B2B 协作用户的属性](active-directory-b2b-user-properties.md)中所讨论，来宾用户可以使用任何标识提供者进行身份验证。 因此，UserType 是决定此事项的确切属性。 令牌中当前未包括 UserType 声明。 应用程序应当使用 Graph API 从目录中查询用户并获取其 UserType。

### <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作故障排除](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)

