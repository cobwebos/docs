---
title: "Azure Active Directory B2B 协作常见问题解答 | Microsoft Docs"
description: "获取有关 Azure Active Directory B2B 协作的常见问题的解答。"
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
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 0e3ae7f176f41b4b0984dbef0748451c7ebff9e7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B 协作常见问题解答

有关 Azure Active Directory (Azure AD) 企业到企业 (B2B) 协作的常见问题解答 (FAQ) 会定期更新，以包含新主题。

### <a name="is-azure-ad-b2b-collaboration-available-in-the-azure-classic-portal"></a>Azure 经典门户中是否提供 Azure AD B2B 协作？
不会。 Azure AD B2B 协作功能仅在 [Azure 门户](https://portal.azure.com)和[访问面板](https://myapps.microsoft.com/)中提供。 

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>是否可以自定义登录页，以便 B2B 协作来宾用户获得更直观的体验？
当然！ 请参阅[有关此功能的博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)。 有关如何自定义组织登录页的详细信息，请参阅[在登录页和访问面板页中添加公司品牌](customize-branding.md)。

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 协作用户能否访问 SharePoint Online 和 OneDrive？
是的。 不过，在 SharePoint Online 中使用人员选取器搜索现有来宾用户的功能默认已**关闭**。 要启用该选项来搜索现有来宾用户，请将 **ShowPeoplePickerSuggestionsForGuestUsers** 为 **On**。 可以在租户级别或站点集合级别启用此设置。 可以使用 Set-SPOTenant 和 Set-SPOSite cmdlet 更改此设置。 使用这些 cmdlet，成员可以搜索目录中的所有现有来宾用户。 租户范围中的更改不会影响已经预配的 SharePoint Online 站点。

### <a name="is-the-csv-upload-feature-still-supported"></a>是否仍支持 CSV 上传功能？
是的。 有关使用 .csv 文件上传功能的详细信息，请参阅[此 PowerShell 示例](active-directory-b2b-code-samples.md)。

### <a name="how-can-i-customize-my-invitation-emails"></a>我能否自定义邀请电子邮件？
使用 [B2B 邀请 API](active-directory-b2b-api.md)，几乎可以自定义有关邀请者过程的所有内容。

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>受邀的外部用户在受邀后能否离开组织？
邀请方组织管理员可以从其目录中删除 B2B 协作来宾用户，但来宾用户不能自行离开邀请组织目录。 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>来宾用户是否可以重置其多重身份验证方法？
是的。 来宾用户可以像普通用户一样重置其多重身份验证方法。

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>哪家组织负责提供多重身份验证许可证？
邀请方组织执行多重身份验证。 邀请方组织必须确保为其使用多重身份验证的 B2B 用户提供足够的许可证。

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>如果合作伙伴组织已设置多重身份验证会怎样？ 我们是否可以信任他们的多重身份验证，而不使用我们自己的多重身份验证？
计划在将来的版本中推出此功能，这样一来就可以选择要从（邀请方组织的）多重身份验证中排除的特定合作伙伴。

### <a name="how-can-i-use-delayed-invitations"></a>如何使用延迟的邀请？
某家组织想要添加 B2B 协作用户，根据需要将这些用户预配到应用程序中，然后发送邀请。 可以使用 B2B 协作邀请 API 自定义载入工作流。

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>是否可将来宾用户指定为受限制的管理员？
绝对是。 有关详细信息，请参阅[将来宾用户添加到角色](active-directory-users-assign-role-azure-portal.md)。

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 协作是否允许 B2B 用户访问 Azure 门户？
除非 B2B 协作用户被分配了受限制的管理员或全局管理员角色，否则他们不需要对 Azure 门户的访问权限。 但是，被分配了受限制的管理员或全局管理员角色的 B2B 协作用户可以访问门户。 另外，如果未被分配这些管理员角色之一的来宾用户访问门户，该用户可以访问某些部分的体验。 来宾用户角色具有目录中的某些权限。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>我能否阻止来宾用户访问 Azure 门户？
能！ 配置此策略时请小心，避免意外阻止成员和管理员的访问。
若要阻止来宾用户访问 [Azure 门户](https://portal.azure.com)，可以在 Windows Azure 经典部署模型 API 中使用条件访问策略：
1. 修改“所有用户”组，使其仅包含成员。
  ![修改组屏幕截图](media/active-directory-b2b-faq/modify-all-users-group.png)
2. 创建包含来宾用户的动态组。
  ![创建组屏幕截图](media/active-directory-b2b-faq/group-with-guest-users.png)
3. 设置条件访问策略以阻止来宾用户访问门户，如以下视频中所示：
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Azure AD B2B 协作是否支持多重身份验证和使用者电子邮件帐户？
是的。 Azure AD B2B 协作同时支持多重身份验证和使用者电子邮件帐户。

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>是否计划支持 Azure AD B2B 协作用户的密码重置？
是的。 下面是从合作伙伴组织中邀请的 B2B 用户的自助服务密码重置 (SSPR) 重要详细信息：
 
* SSPR 只会在 B2B 用户的标识租户中发生。
* 如果标识租户是 Microsoft 帐户，则使用 Microsoft 帐户 SSPR 机制。
* 如果标识租户是实时 (JIT) 或“活跃”租户，则会发送密码重置电子邮件。
* 对于其他租户，将为 B2B 用户遵循标准的 SSPR 流程。 与 B2B 用户的成员 SSPR 类似，在资源上下文中，租户会被阻止。 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>密码重置是否适用于实时 (JIT) 或“活跃”租户中的来宾用户（这些用户使用工作或学校电子邮件地址接受邀请，但没有预先存在的 Azure AD 帐户）？
是的。 可发送密码重置邮件，允许用户在 JIT 租户中重置其密码。

### <a name="does-microsoft-dynamics-crm-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics CRM 是否为 Azure AD B2B 协作提供联机支持？
目前，Microsoft Dynamics CRM 不会为 Azure AD B2B 协作提供联机支持。 但是，我们计划将来支持此功能。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>新创建的 B2B 协作用户的初始密码的生存期是什么？
Azure AD 具有固定的字符集、密码强度和帐户锁定要求，同样适用于所有 Azure AD 云用户帐户。 云用户帐户是没有与其他标识提供者联合的帐户，例如  
* Microsoft 帐户
* Facebook
* Active Directory 联合身份验证服务
* 其他云租户（用于 B2B 协作）

对于联合帐户，密码策略取决于在本地租户中应用的策略和用户的 Microsoft 帐户设置。

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>组织可能希望在其应用程序中为租户用户和来宾用户提供不同的体验。 是否有针对此事项的标准指南？ 标识提供者声明是否是可用的适当模型？
 来宾用户可以使用任何标识提供者进行身份验证。 有关详细信息，请参阅 [B2B 协作用户的属性](active-directory-b2b-user-properties.md)。 使用 **UserType** 属性确定用户体验。 令牌中当前未包括 **UserType** 声明。 应用程序应当使用图形 API 从目录中查询用户并获取其 UserType。

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>可在何处找到 B2B 协作社区以共享解决方案和提交意见？
我们会在改进 B2B 协作的过程中不断听取反馈。 我们诚邀你分享用户方案和最佳做法并分享 Azure AD B2B 协作中让你钟意的方面。 欢迎在 [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)参与讨论。
 
我们还力邀你在 [B2B 协作意见](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)中提交意见并为未来的功能投票。

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>是否可以发送自动兑换的邀请，以便用户只需“准备前往”即可？ 或者，用户始终需要单击到达兑换 URL？
如果邀请方组织中发出邀请的用户同时也是合作伙伴组织的成员，则该邀请无需 B2B 用户兑换。

建议在合作伙伴组织中邀请一个用户加入邀请方组织。 [在资源组织中将此用户添加到来宾邀请者角色。](active-directory-b2b-add-guest-to-role.md) 该用户可以使用登录 UI、PowerShell 脚本或 API 邀请合作伙伴组织中的其他用户。 之后，该组织的 B2B 协作用户无需兑换其邀请。

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>受邀合作伙伴使用联合添加自己的本地身份验证时，B2B 协作如何工作？
如果合作伙伴具有联合到本地身份验证基础架构的 Azure AD 租户，则会自动实现本地单一登录 (SSO)。 如果合作伙伴没有 Azure AD 租户，则会为新用户创建 Azure AD 帐户。 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Azure AD B2B 是否不接受 gmail.com 和 outlook.com 电子邮件地址，此类帐户是否使用 B2C？
我们将去除 B2B 和企业到公司 (B2C) 在所支持的标识这方面的差异。 根据所使用的标识决定使用 B2B 还是 B2C 并不是一个合理的原因。 有关选择协作选项的信息，请参阅[在 Azure Active Directory 中比较 B2B 协作和 B2C](active-directory-b2b-compare-b2c.md)。

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>哪些应用程序和服务支持 Azure B2B 来宾用户？
所有集成了 Azure AD 的应用程序都支持 Azure B2B 来宾用户。 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>如果合作伙伴未启用多重身份验证，我们是否可以强制 B2B 来宾用户使用多重身份验证？
是的。 有关详细信息，请参阅 [B2B 协作用户的条件访问](active-directory-b2b-mfa-instructions.md)。

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>在 SharePoint 中，可以为外部用户定义“允许”和“拒绝”列表。 在 Azure 中是否可以执行此操作？
是的。 Azure AD B2B 协作支持允许列表和拒绝列表。 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>使用 Azure AD B2B 需要哪些许可证？
有关组织需要哪些许可证才能使用 Azure AD B2B 的信息，请参阅 [Azure Active Directory B2B 协作许可指南](active-directory-b2b-licensing.md)。

### <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure AD 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [排查 Azure AD B2B 协作问题](active-directory-b2b-troubleshooting.md)
* [Azure AD B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [有关 Azure AD 中应用程序管理的文章索引](active-directory-apps-index.md)
