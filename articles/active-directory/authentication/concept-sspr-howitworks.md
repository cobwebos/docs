---
title: Azure Active Directory 自助密码重置深入探讨
description: 自助密码重置的工作原理
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: efc62243370ff2cc5214a4ae235139bdb5965486
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248213"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>工作原理：Azure AD 自助密码重置

自助密码重置 (SSPR) 的工作原理 该选项在界面中意味着什么？ 请继续阅读，详细了解 Azure Active Directory (Azure AD) SSPR。

|     |
| --- |
| 将移动应用通知和移动应用代码用作 Azure AD 自助密码重置方法是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>密码重置门户的工作原理

当用户转到密码重置门户时，将启动一个工作流来确定：

   * 如何本地化该页面？
   * 用户帐户是否有效？
   * 该用户属于哪个组织？
   * 该用户的密码在哪个位置管理？
   * 是否已授权该用户使用该功能？

阅读以下步骤，了解有关密码重置页面背后的逻辑：

1. 用户选择“无法访问帐户”链接或直接转到 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)。
   * 根据浏览器的区域设置以相应的语言呈现体验内容。 密码重置体验已本地化为 Office 365 支持的相同语言。
   * 若要以不同的本地化语言查看密码重置门户，请将“?mkt=”追加到密码重置 URL 的末尾，后接本地化为西班牙语的示例 [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)。
2. 用户输入用户 ID 并传递验证码。
3. Azure AD 通过执行以下检查来验证用户是否能够使用此功能：
   * 检查用户是否启用了此功能并分配有 Azure AD 许可证。
     * 如果用户未启用此功能或未分配有许可证，则要求用户联系其管理员重置其密码。
   * 检查用户是否具有针对其帐户定义且符合管理员策略的正确身份验证方法。
     * 如果策略仅要求一种方法，则确保用户具有针对由管理员策略启用的至少一种身份验证方法定义的适当数据。
       * 如果未配置身份验证方法，则建议用户联系其管理员重置其密码。
     * 如果策略仅要求两种方法，则确保用户具有针对由管理员策略启用的至少两种身份验证方法定义的适当数据。
       * 如果未配置身份验证方法，则建议用户联系其管理员重置其密码。
   * 检查是否在本地管理用户密码（联合、直通身份验证或密码哈希同步）。
     * 如果已部署写回且在本地管理用户密码，则允许用户继续进行身份验证并重置其密码。
     * 如果未部署写回且在本地管理用户密码，则要求用户联系其管理员重置其密码。
4. 如果确定用户能够成功重置其密码，则将指导用户完成重置过程。

## <a name="authentication-methods"></a>身份验证方法

如果已启用 SSPR，则必须选择以下至少一个选项作为身份验证方法。 有时，这些选项也称为“门限”。 我们强烈建议**选择两种或更多种身份验证方法**，以便在用户无法使用所需的方法时，能够更灵活地选择其他方法。

* 移动应用通知（预览版）
* 移动应用代码（预览版）
* 电子邮件
* 移动电话
* 办公电话
* 安全提问

仅当用户在管理员已启用的身份验证方法中输入了数据时，他们才能重置其密码。

![身份验证][Authentication]

### <a name="number-of-authentication-methods-required"></a>所需身份验证方法的数量

此选项确定用户在执行重置或解锁密码过程时必须完成的可用身份验证方法或门限数下限。 可将其设置为 1 或 2。

用户可以选择提供更多的身份验证方法（如果管理员已启用该身份验证方法）。

如果没有为用户注册最少数目的所需方法，他们将看到一个错误页面，让他们请求管理员重置其密码。

#### <a name="mobile-app-and-sspr-preview"></a>移动应用和 SSPR（预览版）

使用移动应用（例如 Microsoft Authenticator 应用）作为密码重置方法时，用户应注意以下问题。 对于自助密码重置，如果只需使用一种方法来执行重置，则验证码是提供给用户的唯一选项。 如果需要两种方法，则用户可以使用通知或验证码进行重置，此外还能使用其他任何已启用的方法。

| 重置所需的方法数 | 一种 | 两种 |
| :---: | :---: | :---: |
| 可用的移动应用功能 | 代码 | 代码或通知 |

用户在注册自助密码重置时，无法选择注册其移动应用。 用户可以在 aka.ms/mfasetup 中，或者在安全信息注册预览版 (aka.ms/setupsecurityinfo) 中注册其移动应用。 

### <a name="change-authentication-methods"></a>更改身份验证方法

如果最初的策略仅注册了一种身份验证方法用于重置或解锁，将其更改为两种方法会发生什么情况？

| 注册的方法数 | 必选方法数 | 结果 |
| :---: | :---: | :---: |
| 大于等于 1 | 1 | 能够重置或解锁 |
| 1 | 2 | 不可重置或解锁 |
| 2 或更大 | 2 | 能够重置或解锁 |

如果更改了用户可用的身份验证方法类型，则可能会在无意间阻止用户使用 SSPR（如果不具有可用的最小数据量）。

示例：
1. 原始策略配置为需要两种身份验证方法。 该策略使用办公电话和安全提问。 
2. 管理员将策略更改为不再使用安全提问，而是允许使用移动电话和备用电子邮件。
3. 未填写移动电话或备用电子邮件字段的用户无法重置密码。

## <a name="registration"></a>注册

### <a name="require-users-to-register-when-they-sign-in"></a>要求用户在登录时注册

启用此选项需要用户在使用 Azure AD 登录到任何应用程序时完成密码重置注册。 这包括以下应用程序：

* Office 365
* Azure 门户
* 访问面板
* 联合应用程序
* 使用 Azure AD 的自定义应用程序

如果禁用了注册要求，用户可以手动注册。 他们可以访问 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)，或选择访问面板中“配置文件”选项卡下的“注册密码重置”链接。

> [!NOTE]
> 用户可以通过选择“取消”或关闭窗口来隐藏密码重置注册门户。 但是，在完成注册之前，每当他们登录时，系统都会提示他们注册。
>
> 如果用户已登录，此行为不会中断用户的连接。

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>设置用户必须在几天后重新确认其身份验证信息

此选项确定设置和重新确认身份验证信息之间的时间段，仅当已启用“要求用户在登录时注册”选项时才可用。

有效值为 0 到 730 天，“0”表示永远不要求用户重新确认其身份验证信息。

## <a name="notifications"></a>通知

### <a name="notify-users-on-password-resets"></a>重置密码时通知用户

如果此选项设置为“是”，则重置其密码的用户将收到一封电子邮件，告知其密码已更改。 该电子邮件通过 SSPR 门户发送到 Azure AD 中登记的主要和备用电子邮件地址。 不会向其他任何人告知已发生重置事件。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>当其他管理员重置其密码时通知所有管理员

如果此选项设置为“是”，则所有管理员的、在 Azure AD 中登记的主要电子邮件地址都会收到一封电子邮件。 该电子邮件告知另一位管理员已使用 SSPR 更改了他们的密码。

示例：某个环境中有四名管理员。 管理员 A 使用 SSPR 重置了其他管理员的密码。 管理员 B、C 和 D 将收到一封电子邮件，告知已发生密码重置。

## <a name="on-premises-integration"></a>本地集成

如果已安装、配置并启用 Azure AD Connect，可以使用以下附加选项进行本地集成。 如果这些选项灰显，则表示写回配置不正确。 有关详细信息，请参阅[配置密码写回](howto-sspr-writeback.md)。

![写回][Writeback]

本页提供了本地写回客户端的快速状态，根据当前配置将显示以下消息之一：

* 已启动并运行本地写回客户端。
* Azure AD 处于联机状态并连接到本地写回客户端。 但是，似乎 Azure AD Connect 的已安装版本已经过期了。 请考虑[更新 Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md)，确保拥有最新连接功能和重要 bug 修复。
* 很遗憾，因为 Azure AD Connect 的已安装版本已过期，因此无法查看本地写回客户端状态。 [更新 Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) 可查看连接状态。
* 很遗憾，现在似乎无法连接到本地写回客户端。 [对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。
* 很遗憾，因为密码写回未正确配置，无法连接到本地写回客户端。 [配置密码写回](howto-sspr-writeback.md)以还原连接。
* 很遗憾，现在似乎无法连接到本地写回客户端。 这可能是因我们终端的临时问题导致。 如果问题仍然存在，[对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。

### <a name="write-back-passwords-to-your-on-premises-directory"></a>将密码写回到本地目录

此控制机制确定是否为此目录启用了密码写回。 如果已启用写回，则指示本地写回服务的状态。 如果想要暂时禁用密码写回且无需重新配置 Azure AD Connect，此设置将很有用。

* 如果此开关设置为“是”，则会启用写回，因此联合用户、直通身份验证用户或已进行密码哈希同步的用户能够重置其密码。
* 如果此开关设置为“否”，则会禁用写回，因此联合用户、直通身份验证用户或已进行密码哈希同步的用户无法重置其密码。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>允许用户在不重置密码的情况下解锁帐户

此控制机制指定是否应为浏览密码重置门户的用户提供选项，让他们在无需重置密码的情况下解锁本地 Active Directory 帐户。 默认情况下，Azure AD 在执行密码重置时会解锁帐户。 使用此设置可区分这两项操作。 

* 如果设置为“是”，将为用户提供重置其密码和解锁帐户的选项，或者在无需重置密码的情况下解锁其帐户的选项。
* 如果设置为“否”，用户只能同时执行密码重置和帐户解锁的操作。

## <a name="password-reset-for-b2b-users"></a>B2B 用户的密码重置

所有企业到企业 (B2B) 配置完全支持密码重置和更改。 以下三种情况支持 B2B 用户密码重置：

   * **已有 Azure AD 租户的合作伙伴组织中的用户**：如果与你合作的组织已有 Azure AD 租户，我们将遵守该租户中已启用的任何密码重置策略。 要使密码重置正常工作，合作伙伴组织只需确保启用 Azure AD SSPR。 这不会给 Office 365 客户造成额外的费用，可以遵循[密码管理入门](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)指南中的步骤启用 SSPR。
   * 通过自助注册**注册的用户**：如果合作组织使用[自助注册](../users-groups-roles/directory-self-service-signup.md)功能来访问租户，我们将允许他们使用已注册的电子邮件来重置密码。
   * **B2B 用户**：使用新的 [Azure AD B2B 功能](../active-directory-b2b-what-is-azure-ad-b2b.md)创建的任何新 B2B 用户也可以使用他们在邀请过程中注册的电子邮件来重置其密码。

若要测试此方案，请通过这些合作伙伴用户之一转到 http://passwordreset.microsoftonline.com。 如果他们定义了备用电子邮件或身份验证电子邮件，则密码重置就能按预期方式工作。

> [!NOTE]
> 已被授予 Azure AD 租户来宾访问权限的 Microsoft 帐户（例如 Hotmail.com、Outlook.com 的电子邮件地址或其他个人电子邮件地址）无法使用 Azure AD SSPR。 他们需要使用[当你无法登录到 Microsoft 帐户时](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)文章中的信息重置其密码。

## <a name="next-steps"></a>后续步骤

以下文章提供了有关通过 Azure AD 进行密码重置的更多信息：

* [如何成功推出 SSPR？](howto-sspr-deployment.md)
* [重置或更改密码](../user-help/active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](../user-help/active-directory-passwords-reset-register.md)
* [是否有许可问题？](concept-sspr-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](howto-sspr-authenticationdata.md)
* [哪些身份验证方法可供用户使用？](concept-sspr-howitworks.md#authentication-methods)
* [SSPR 有哪些策略选项？](concept-sspr-policy.md)
* [什么是密码写回？我为什么关心它？](howto-sspr-writeback.md)
* [如何报告 SSPR 中的活动？](howto-sspr-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](concept-sspr-howitworks.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "可用的 Azure AD 身份验证方法和所需数量"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "本地集成密码写回配置和故障排除信息"
