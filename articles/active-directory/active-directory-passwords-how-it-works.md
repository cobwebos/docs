---
title: "自助密码重置工作原理 - Azure Active Directory"
description: "Azure AD 自助密码重置深入探讨"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 0cf26846a8f42238de09727a03dc6b50dff746b6
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Azure AD 中的自助密码重置深入探讨

自助密码重置 (SSPR) 的工作原理 该选项在界面中意味着什么？ 请继续阅读，详细了解 Azure Active Directory (Azure AD) SSPR。

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
2. 用户输入用户 ID 并传递验证码。
3. Azure AD 通过执行以下检查来验证用户是否能够使用此功能：
   * 检查用户是否启用了此功能并分配有 Azure AD 许可证。
     * 如果用户未启用此功能或未分配有许可证，则要求用户联系其管理员重置其密码。
   * 检查用户是否具有针对其帐户定义且符合管理员策略的正确质询数据。
     * 如果策略仅要求一个质询，则确保用户具有针对由管理员策略启用的至少一个质询定义的适当数据。
       * 如果未配置用户质询，则建议用户联系其管理员重置其密码。
     * 如果策略要求两次质询，则确保用户具有针对由管理员策略启用的至少两个质询定义的适当数据。
       * 如果未配置用户质询，则建议用户联系其管理员重置其密码。
   * 检查是否在本地管理用户密码（联合或密码哈希同步）。
     * 如果已部署写回且在本地管理用户密码，则允许用户继续进行身份验证并重置其密码。
     * 如果未部署写回且在本地管理用户密码，则要求用户联系其管理员重置其密码。
4. 如果确定用户能够成功重置其密码，则将指导用户完成重置过程。

## <a name="authentication-methods"></a>身份验证方法

如果已启用 SSPR，则必须选择以下至少一个选项作为身份验证方法。 有时，这些选项也称为“门限”。 我们强烈建议至少选择两个身份验证方法，以便为用户提供更大的灵活性。

* Email
* 移动电话
* 办公电话
* 安全提问

![身份验证][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>在用于身份验证数据的目录中使用哪些字段？

* **办公电话**：对应于办公电话。
    * 用户无法自行设置此字段。 必须由管理员定义此字段。
* **移动电话**：对应于身份验证电话（不公开）或移动电话（公开）。
    * 服务首先查找身份验证电话，如果身份验证电话不存在，则回退到移动电话。
* **备用电子邮件地址**：对应于身份验证电子邮件（不公开）或备用电子邮件。
    * 服务首先查找身份验证电子邮件，然后回退到备用电子邮件。

默认情况下，只有“办公电话”和“移动电话”两个云属性才会从用于身份验证数据的本地目录同步到云目录。

仅当用户在管理员已启用并要求使用的身份验证方法中输入了数据时，他们才能重置其密码。

如果用户不希望在目录中公开其移动电话号码，但仍想使用该号码来重置密码，则管理员不应在目录中填充该号码。 用户应通过[密码重置注册门户](https://aka.ms/ssprsetup)填充其“身份验证电话”属性。 管理员可在用户的配置文件中看到此信息，但此信息不会发布到其他位置。

### <a name="the-number-of-authentication-methods-required"></a>所需身份验证方法的数量

此选项确定用户在执行重置或解锁密码过程时必须完成的可用身份验证方法或门限数下限。 可将其设置为 1 或 2。

用户可以选择提供更多的身份验证方法（如果管理员已启用该身份验证方法）。

如果没有为用户注册最少数目的所需方法，他们将看到一个错误页面，让他们请求管理员重置其密码。

#### <a name="change-authentication-methods"></a>更改身份验证方法

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
3. 未填写移动电话、备用电子邮件字段的用户无法重置密码。

### <a name="how-secure-are-my-security-questions"></a>如何保护安全提问？

如果使用安全提问，我们建议结合另一种方法使用这些提问。 因为某些人可能知道其他用户的问题的答案，因此这种方法可能比其他方法更不安全。

> [!NOTE] 
> 安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。
>

### <a name="security-question-localization"></a>安全问题本地化

下面的所有预定义问题都已根据用户的浏览器区域设置本地化为 Office 365 的完整语言集：

* 在哪个城市遇到了第一任配偶/伴侣？
* 父母在哪个城市相识？
* 与你年龄最近的兄弟姐妹居住在哪个城市？
* 父亲在哪个城市出生？
* 第一份工作是在哪个城市？
* 母亲在哪个城市出生？
* 2000 年元旦你在哪个城市？
* 中学时你最喜欢的老师姓什么？
* 填报了志愿，但未能入学的大学名称是什么？
* 举办第一次婚宴的地名是什么？
* 父亲的中间名是什么？
* 最喜欢的食品是什么？
* 外婆的姓氏和名字是什么？
* 母亲的中间名是什么？
* 最年长的兄弟姐妹的生日年份和月份是什么？ （例如 1985 年 11 月）
* 最年长的兄弟姐妹的中间名是什么？
* 爷爷的姓氏和名字是什么？
* 最年幼的兄弟姐妹的中间名是什么？
* 六年级时你就读哪所学校？
* 童年时最好的朋友的名字和姓氏是什么？
* 第一位伴侣的名字和姓氏是什么？
* 最喜欢的小学老师姓什么？
* 第一辆汽车或摩托车是哪个品牌和型号？
* 就读的第一所学校的校名是什么？
* 在哪家医院出生？
* 童年时第一个家庭地址的街道名称是什么？
* 童年时崇拜的人是谁？
* 最喜欢哪种填充动物玩具？
* 第一个宠物叫什么？
* 童年时的绰号是什么？
* 在中学最喜欢的运动是什么？
* 第一份工作是什么？
* 童年时的电话号码最后四位数是什么？
* 年少时，希望长大后当什么？
* 遇到过的最有名的人是谁？

### <a name="custom-security-questions"></a>自定义安全问题

自定义安全问题尚未根据不同的区域设置本地化。 所有自定义问题的显示语言是在管理用户界面中输入这些问题时所用的语言，即使用户浏览器的区域设置与此不同。 如果需要本地化的问题，应使用预定义的问题。

自定义安全问题的最大长度为 200 个字符。

### <a name="security-question-requirements"></a>安全问题要求

* 答案包含的字符数下限为 3 个字符。
* 答案包含的字符数上限为 40 个字符。
* 用户不能多次回答同一问题。
* 用户不能为多个问题提供相同的答案。
* 可以使用任何字符集来定义问题和答案（包括 Unicode 字符）。
* 所定义问题的数量必须大于或等于注册所需问题数量。

## <a name="registration"></a>注册

### <a name="require-users-to-register-when-they-sign-in"></a>要求用户在登录时注册

若要启用此选项，已启用密码重置的用户必须在使用 Azure AD 登录到应用程序时完成密码重置注册。 其中包括：

* Office 365
* Azure 门户
* 访问面板
* 联合应用程序
* 使用 Azure AD 的自定义应用程序

如果已禁用要求注册，用户仍可手动注册其联系信息。 他们可以访问 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)，或选择访问面板中“配置文件”选项卡下的“注册密码重置”链接。

> [!NOTE]
> 用户可以通过选择“取消”或关闭窗口来隐藏密码重置注册门户。 但是，在完成注册之前，每当他们登录时，系统都会提示他们注册。
>
> 如果用户已登录，此行为不会中断用户的连接。

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>设置用户必须在几天后重新确认其身份验证信息

此选项确定设置和重新确认身份验证信息之间的时间段，仅当已启用“要求用户在登录时注册”选项时才可用。

有效值为 0 到 730 天，“0”表示永远不要求用户重新确认其身份验证信息。

## <a name="notifications"></a>通知

### <a name="notify-users-on-password-resets"></a>重置密码时通知用户

如果此选项设置为“是”，则重置其密码的用户将收到一封电子邮件，告知其密码已更改。 该电子邮件通过 SSPR 门户发送到 Azure AD 中登记的主要和备用电子邮件地址。 不会向其他任何人告知已发生此重置事件。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>当其他管理员重置其密码时通知所有管理员

如果此选项设置为“是”，则所有管理员的、在 Azure AD 中登记的主要电子邮件地址都会收到一封电子邮件。 该电子邮件告知另一位管理员已使用 SSPR 更改了他们的密码。

示例：某个环境中有四名管理员。 管理员 A 使用 SSPR 重置了其他管理员的密码。 管理员 B、C 和 D 将收到一封电子邮件，告知已发生密码重置。

## <a name="on-premises-integration"></a>本地集成

如果已安装、配置并启用 Azure AD Connect，可以使用以下附加选项进行本地集成。 如果这些选项灰显，则表示写回配置不正确。 有关详细信息，请参阅[配置密码写回](active-directory-passwords-writeback.md#configure-password-writeback)。

![写回][Writeback]

本页提供了本地写回客户端的快速状态，根据当前配置将显示以下消息之一：

* 已启动并运行本地写回客户端。
* Azure AD 处于联机状态并连接到本地写回客户端。 但是，似乎 Azure AD Connect 的已安装版本已经过期了。 请考虑[更新 Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md)，确保拥有最新连接功能和重要 bug 修复。
* 很遗憾，因为 Azure AD Connect 的已安装版本已过期，因此无法查看本地写回客户端状态。 [更新 Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md) 可查看连接状态。
* 很遗憾，现在似乎无法连接到本地写回客户端。 [对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。
* 很遗憾，因为密码写回未正确配置，无法连接到本地写回客户端。 [配置密码写回](active-directory-passwords-writeback.md#configure-password-writeback)以还原连接。
* 很遗憾，现在似乎无法连接到本地写回客户端。 这可能是因我们终端的临时问题导致。 如果问题仍然存在，[对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。

### <a name="write-back-passwords-to-your-on-premises-directory"></a>将密码写回到本地目录

此控制机制确定是否为此目录启用了密码写回。 如果已启用写回，则指示本地写回服务的状态。 如果想要暂时禁用密码写回且无需重新配置 Azure AD Connect，此设置将很有用。

* 如果此开关设置为“是”，则会启用写回，因此联合用户和已进行密码哈希同步的用户能够重置其密码。
* 如果此开关设置为“否”，则会禁用写回，因此联合用户和已进行密码哈希同步的用户无法重置其密码。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>允许用户在不重置密码的情况下解锁帐户

此控制机制指定是否应为浏览密码重置门户的用户提供选项，让他们在无需重置密码的情况下解锁本地 Active Directory 帐户。 默认情况下，Azure AD 在执行密码重置时会解锁帐户。 使用此设置可区分这两项操作。 

* 如果设置为“是”，将为用户提供重置其密码和解锁帐户的选项，或者在无需重置密码的情况下解锁其帐户的选项。
* 如果设置为“否”，用户只能同时执行密码重置和帐户解锁的操作。

## <a name="how-does-password-reset-work-for-b2b-users"></a>B2B 用户如何使用密码重置？
所有企业到企业 (B2B) 配置完全支持密码重置和更改。 以下三种情况支持 B2B 用户密码重置：

   * **已有 Azure AD 租户的合作伙伴组织中的用户**：如果与你合作的组织已有 Azure AD 租户，我们将遵守该租户中已启用的任何密码重置策略。 要使密码重置正常工作，合作伙伴组织只需确保启用 Azure AD SSPR。 这不会给 Office 365 客户造成额外的费用，可以遵循[密码管理入门](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)指南中的步骤启用 SSPR。
   * 通过自助注册**注册的用户**：如果合作组织使用[自助注册](active-directory-self-service-signup.md)功能来访问租户，我们将允许他们使用已注册的电子邮件来重置密码。
   * **B2B 用户**：使用新的 [Azure AD B2B 功能](active-directory-b2b-what-is-azure-ad-b2b.md)创建的任何新 B2B 用户也可以使用他们在邀请过程中注册的电子邮件来重置其密码。

若要测试此方案，请让上述合作伙伴用户之一转到 http://passwordreset.microsoftonline.com。 如果他们定义了备用电子邮件或身份验证电子邮件，则密码重置就能按预期方式工作。

> [!NOTE]
> 已被授予 Azure AD 租户来宾访问权限的 Microsoft 帐户（例如 Hotmail.com、Outlook.com 的电子邮件地址或其他个人电子邮件地址）无法使用 Azure AD SSPR。 他们需要使用[当你无法登录到 Microsoft 帐户时](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)文章中的信息重置其密码。

## <a name="next-steps"></a>后续步骤

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

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "可用的 Azure AD 身份验证方法和所需数量"
[Writeback]: ./media/active-directory-passwords-how-it-works/troubleshoot-writeback-running.png "本地集成密码写回配置和故障排除信息"
