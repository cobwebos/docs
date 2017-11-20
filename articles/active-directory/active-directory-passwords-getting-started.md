---
title: "快速入门：Azure AD SSPR | Microsoft Docs"
description: "快速部署 Azure AD 自助密码重置"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: c5ba922175a25776c6e27ee03fae9b2941235881
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Azure AD 自助密码重置快速部署

> [!IMPORTANT]
> 你是否因登录时遇到问题而浏览至此？ 如果是这样，请参阅[请为我提供帮助，我忘记了 Azure AD 密码](active-directory-passwords-update-your-own-password.md)。

IT 管理员可以让用户通过自助密码重置 (SSPR) 这种简单的方法来重置或解锁其密码或帐户。 系统提供详细的报告，用于跟踪用户访问系统的时间，同时还提供通知，提醒用户存在误用或滥用情况。

本指南假定已经有了一个可以运行的试用版或许可版 Azure Active Directory (Azure AD) 租户。 如需 Azure AD 设置帮助，请参阅 [Azure AD 入门](https://azure.microsoft.com/trial/get-started-active-directory/)。

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>为 Azure AD 租户启用 SSPR

1. 在现有的 Azure AD 租户中选择“密码重置”。

2. 在“属性”页的“启用自助密码重置”选项下，选择以下选项之一：
    * **无**：无人能够使用 SSPR 功能。
    * **选定** - 仅所选特定 Azure AD 组的成员可以使用 SSPR 功能。 建议定义一组用户，在为概念证明部署此功能时使用此设置。
    * **所有** - 在 Azure AD 租户中有帐户的所有用户都可以使用 SSPR 功能。 建议你在完成概念证明，可以向整个租户部署此功能时使用此设置。

3. 从“身份验证方法”页选择以下设置：
    * **进行重置所需的方法数**：我们支持的最小数目为 1，最大数目为 2。
    * **可供用户使用的方法**：至少需要一种方法，也可提供额外的选项。
        * **电子邮件**：向用户配置的身份验证电子邮件地址发送含代码的电子邮件。
        * **手机**：允许用户选择是通过其配置的手机号码接电话，还是接收含代码的短信。
        * **办公电话**：通过用户配置的办公电话号码呼叫用户，告知其代码。
        * **安全问题**：要求选择：
            * **注册所需的问题数**：成功注册的最低要求。 用户可以选择回答更多问题，从而创建一个可以从中抽取问题的问题池。 此选项可以设置为三到五个问题，该数目必须大于或等于重置密码所需的问题数。 如果在选择安全问题时选择了“自定义”按钮，用户可以添加自定义问题。
            * **重置所需的问题数**：可以设置为三到五个问题，必须正确回答这些问题才能重置或解锁用户密码。
            
    ![身份验证][Authentication]

4. 建议：可以在“自定义”下更改“联系管理员”链接，使之指向定义的页面或电子邮件地址。 建议将此链接设置为用户用来提问支持问题的电子邮件地址或网站等内容。

5. 可选：“注册”页为管理员提供以下选项：
    * 要求用户在登录时注册。
    * 设置用户必须在几天后重新确认其身份验证信息。

6. 可选：“通知”页为管理员提供以下选项：
    * 重置密码时通知用户。
    * 当其他管理员重置其密码时通知所有管理员。

你目前已为 Azure AD 租户配置 SSPR。 用户现在可以根据[注册自助密码重置](active-directory-passwords-reset-register.md)和[重置或更改密码](active-directory-passwords-update-your-own-password.md)这两篇文章中的说明来更新其密码，不需求助管理员。 如果只需执行仅限云的操作，则可在此打住。 如果要将密码配置为同步到本地 Active Directory 域，则可继续阅读下一部分。

> [!IMPORTANT]
> 请使用用户而不是管理员来测试 SSPR，因为 Microsoft 对 Azure 管理员帐户强制实施强身份验证要求。 有关管理员密码策略的详细信息，请参阅[密码策略](active-directory-passwords-policy.md#administrator-password-policy-differences)文章。

## <a name="configure-synchronization-to-an-existing-identity-source"></a>配置到现有标识源的同步

若要启用本地标识到 Azure AD 的同步，需在组织的服务器上安装并配置 [Azure AD Connect](./connect/active-directory-aadconnect.md)。 该应用程序负责将用户和组从现有的标识源同步到 Azure AD 租户。 有关详细信息，请参阅：

* [从 DirSync 或 Azure AD Sync 升级到 Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [通过快速设置开始使用 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)
* [配置密码写回](active-directory-passwords-writeback.md#configuring-password-writeback)，将密码从 Azure AD 写回到本地目录

### <a name="on-premises-policy-change"></a>本地策略更改

如果是从本地 Active Directory 域同步用户，并且希望允许用户即时重置其密码，则请对本地密码策略进行以下更改：

1. 浏览到“计算机配置” > “策略” > “Windows 设置” > “安全设置” > “帐户策略” > “密码策略”。

2. 将“最短密码期限”设置为“0 天”。

此安全设置决定了密码必须使用一定的时间（单位：天）才允许用户对其进行更改。 如果将最短使用时间设置为“0 天”，则用户可以在其密码被支持团队更改后使用 SSPR。

![策略][Policy]

## <a name="disable-self-service-password-reset"></a>禁用自助密码重置

禁用自助密码重置很容易。 打开 Azure AD 租户，转到“密码重置” > “属性”，然后在“启用自助密码重置”下选择“无”。

### <a name="learn-more"></a>了解详细信息
以下文章提供了有关通过 Azure AD 进行密码重置的更多信息：

* [如何成功推出 SSPR？](active-directory-passwords-best-practices.md)
* [重置或更改密码](active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](active-directory-passwords-reset-register.md)
* [是否有许可问题？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](active-directory-passwords-data.md)
* [哪些身份验证方法可供用户使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR 有哪些策略选项？](active-directory-passwords-policy.md)
* [什么是密码写回？我为什么关心它？](active-directory-passwords-writeback.md)
* [如何报告 SSPR 中的活动？](active-directory-passwords-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](active-directory-passwords-how-it-works.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何为用户配置自助密码重置。 若要完成这些步骤，请转到 Azure 门户：

> [!div class="nextstepaction"]
> [启用自助服务密码重置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "可用的 Azure AD 身份验证方法和所需数量"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "本地密码组策略设置为 0 天"

