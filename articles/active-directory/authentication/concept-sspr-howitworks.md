---
title: 自助式密码重置深入探讨 - Azure Active Directory
description: 自助密码重置的工作原理
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a4e540ff6a81be8afa769d93b0649e0fce49882
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965091"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>工作原理：Azure AD 自助密码重置

Azure Active Directory (Azure AD) 自助式密码重置 (SSPR) 使用户能够更改或重置其密码，而不需要管理员或支持人员的干预。 如果用户的帐户被锁定或用户忘记了自己的密码，他们可以按照提示取消对自己的阻止，回到工作状态。 当用户无法登录到其设备或应用程序时，此功能可减少呼叫支持人员的次数，降低生产力损失。

> [!IMPORTANT]
> 此概念文章向管理员介绍了自助式密码重置的工作原理。 如果你是已注册了自助式密码重置的最终用户并且需要返回到你的帐户，请转到 [https://aka.ms/sspr](https://aka.ms/sspr)。
>
> 如果你的 IT 团队尚未启用重置自己密码的功能，请联系支持人员以获得更多帮助。

## <a name="how-does-the-password-reset-process-work"></a>密码重置过程如何工作？

用户可以使用 [SSPR 门户](https://aka.ms/sspr)来重置或更改其密码。 他们必须首先注册其所需的身份验证方法。 当用户访问 SSPR 门户时，Azure 平台会考虑以下因素：

* 如何本地化该页面？
* 用户帐户是否有效？
* 该用户属于哪个组织？
* 该用户的密码在哪个位置管理？
* 是否已授权该用户使用该功能？

如果用户从某个应用程序或页面选择了“无法访问你的帐户”链接，或直接转到了 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)，则 SSPR 门户中使用的语言基于以下选项：

* 默认情况下，将使用浏览器区域设置以相应的语言显示 SSPR。 密码重置体验已本地化为 [Microsoft 365 支持](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)的相同语言。
* 如果要链接到采用特定本地化语言的 SSPR，请将 `?mkt=` 连同所需的区域设置一起追加到密码重置 URL 末尾。
    * 例如，若要指定西班牙语 (es-us) 区域设置，请使用 `?mkt=es-us` - [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)。

使用所需的语言显示 SSPR 门户之后，系统会提示用户输入用户 ID 并传递 captcha。 Azure AD 现在通过执行以下检查来验证用户是否能够使用 SSPR：

* 检查用户是否启用了 SSPR 并分配有 Azure AD 许可证。
  * 如果用户未启用 SSPR 或未分配有许可证，则会要求用户联系其管理员来重置其密码。
* 检查用户是否具有针对其帐户定义且符合管理员策略的正确身份验证方法。
  * 如果策略仅要求使用一种方法，请检查用户是否为通过管理员策略启用的至少一种身份验证方法定义了合适的数据。
    * 如果未配置身份验证方法，则建议用户联系其管理员来重置其密码。
  * 如果策略要求使用两种方法，请检查用户是否为通过管理员策略启用的至少两种身份验证方法定义了合适的数据。
    * 如果未配置身份验证方法，则建议用户联系其管理员来重置其密码。
  * 如果为用户分配了 Azure 管理员角色，则会强制实施强双门密码策略。 有关详细信息，请参阅[管理员重置策略差异](concept-sspr-policy.md#administrator-reset-policy-differences)。
* 检查是否在本地管理用户密码，如 Azure AD 租户使用联合身份验证、传递身份验证或密码哈希同步：
  * 如果已配置 SSPR 写回且在本地管理用户密码，则允许用户继续进行身份验证并重置其密码。
  * 如果未部署 SSPR 写回且在本地管理用户密码，则要求用户联系其管理员重置其密码。

如果前面的所有检查都已成功完成，则指导用户完成重置或更改其密码的过程。

> [!NOTE]
> SSPR 可能会在密码重置过程中向用户发送电子邮件通知。 这些电子邮件是使用 SMTP 中继服务发送的，该服务跨多个区域以主动-主动模式运行。
>
> SMTP 中继服务接收并处理电子邮件正文，但不存储它。 可能包含客户提供的信息的 SSPR 电子邮件正文未存储在 SMTP 中继服务日志中。 这些日志只包含协议元数据。

若要开始使用 SSPR，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助式密码重置 (SSPR)](tutorial-enable-sspr.md)

## <a name="registration-options"></a>注册选项

用户必须先注册自己以及要使用的身份验证方法，然后才能使用 SSPR 重置或更改其密码。 如前一部分所述，用户必须注册 SSPR，并应用相应的许可证。

### <a name="require-users-to-register-when-they-sign-in"></a>要求用户在登录时注册

可以启用此选项在用户使用 Azure AD 登录到任何应用程序时要求用户完成 SSPR 注册。 此工作流包括以下应用程序：

* Microsoft 365
* Azure 门户
* 访问面板
* 联合应用程序
* 使用 Azure AD 的自定义应用程序

如果不需要注册，则在登录时不会提示用户，但用户可以手动注册。 用户可以访问 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)，也可以选择访问面板中“配置文件”选项卡下的“注册密码重置”链接 。

![Azure 门户中 SSPR 的注册选项][Registration]

> [!NOTE]
> 用户可以通过选择“取消”或关闭窗口来关闭 SSPR 注册门户。 但是，在完成注册之前，每当他们登录时，系统都会提示他们注册。
>
> 如果用户已登录，则用于注册 SSPR 的此中断不会中断用户的连接。

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>设置用户必须在几天后重新确认其身份验证信息

为了在需要使用身份验证方法来重置或更改用户的密码时确保身份验证方法正确，你可以要求用户在某段时间过后确认其已注册的信息。 仅当你启用了“要求用户在登录时注册”选项时，此选项才可用。

有效值（用于提示用户确认其注册的方法）的范围为 0 到 730 天。 将此值设置为 0 意味着永远不会要求用户确认其身份验证信息。

## <a name="authentication-methods"></a>身份验证方法

如果为用户启用了 SSPR，则他们必须注册至少一种身份验证方法。 强烈建议你选择两种或更多种身份验证方法，让用户在无法使用所需的一种方法时，能够更灵活地选择其他方法。 有关详细信息，请参阅 [什么是身份验证方法？](concept-authentication-methods.md)。

以下身份验证方法可用于 SSPR：

* 移动应用通知
* 移动应用代码
* 电子邮件
* 移动电话
* 办公电话
* 安全性问题

用户只有在注册了管理员启用的身份验证方法时才能重置其密码。

> [!WARNING]
> 若要使用[管理员重置策略差异](concept-sspr-policy.md#administrator-reset-policy-differences)部分定义的方法，需要使用分配了 Azure 管理员角色的帐户。

![Azure 门户中的身份验证方法选择][Authentication]

### <a name="number-of-authentication-methods-required"></a>所需身份验证方法的数量

你可以配置用户为了重置或解锁其密码而必须提供的可用身份验证方法的数目。 可将此值设置为 1 或 2。 

用户可以并且应当注册多种身份验证方法。 同样，强烈建议用户注册两种或更多种身份验证方法，以便在无法使用所需的一种方法时，能够更灵活地选择其他方法。

如果用户没有注册最低数目的必需方法，则会在尝试使用 SSPR 时看到一个错误页面，该页面会引导他们请求管理员重置其密码。 如果你有已注册 SSPR 的现有用户，但这些用户无法使用该功能，则在将所需方法的数量从一种增加到两种时应小心谨慎。 有关详细信息，请参阅下一部分：[更改身份验证方法](#change-authentication-methods)。

#### <a name="mobile-app-and-sspr"></a>移动应用和 SSPR

使用移动应用（例如 Microsoft Authenticator 应用）作为密码重置方法时，应注意以下问题：

* 当管理员要求使用一种方法来重置密码时，验证码是唯一可用的选项。
* 当管理员要求使用两种方法来重置密码时，用户可以使用通知或验证码进行重置，此外还能使用其他任何已启用的方法。

| 重置所需的方法数 | 一个 | 两个 |
| :---: | :---: | :---: |
| 可用的移动应用功能 | 代码 | 代码或通知 |

用户通过 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 注册自助式密码重置时，无法选择注册其移动应用。 用户可以在中注册其移动应用 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ，或者在中的组合安全信息注册中注册 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 。

> [!IMPORTANT]
> 当只要求使用一种方法时，不能选择 Authenticator 应用作为唯一的身份验证方法。 同样，如果要求使用两种方法，则不能选择 Authenticator 应用和另一种额外的方法。
>
> 在配置将 Authenticator 应用作为一种方法的 SSPR 策略时，如果要求在使用一种方法时应选择至少一种额外的方法，则当要求配置两种方法时，应选择至少两种额外的方法。
>
> 之所以有此要求，是因为当前 SSPR 注册体验未包含用于注册 Authenticator 应用的选项。 用于注册验证器应用的选项包含在新的 [组合注册体验](./concept-registration-mfa-sspr-combined.md)中。
>
> 如果允许的策略仅使用 Authenticator 应用（要求使用一种方法时）或使用 Authenticator 应用和另一种额外的方法（要求使用两种方法时），则可能会导致系统阻止用户注册 SSPR，直到将其配置为使用新的组合注册体验为止。

### <a name="change-authentication-methods"></a>更改身份验证方法

如果最初的策略仅注册了一种身份验证方法用于重置或解锁，将其更改为两种方法会发生什么情况？

| 注册的方法数 | 必选方法数 | 结果 |
| :---: | :---: | :---: |
| 大于等于 1 | 1 | 能够重置或解锁**** |
| 1 | 2 | 不可重置或解锁**** |
| 2 或更大 | 2 | 能够重置或解锁**** |

更改可用的身份验证方法也可能会给用户带来问题。 如果更改了用户可用的身份验证方法类型，则可能会在无意间阻止用户使用 SSPR（如果不具有可用的最小数据量）。

请考虑以下示例场景：

1. 原始策略配置为需要两种身份验证方法。 该策略使用办公电话和安全提问。
1. 管理员将策略更改为不再使用安全提问，而是允许使用移动电话和备用电子邮件。
1. 未填写移动电话或备用电子邮件字段的用户现在无法重置密码。

## <a name="notifications"></a>通知

SSPR 允许你为用户和标识管理员配置通知，以便及时获知密码事件。

### <a name="notify-users-on-password-resets"></a>重置密码时通知用户

如果此选项设置为“是”，则重置其密码的用户会收到一封告知其密码已更改的电子邮件。 该电子邮件通过 SSPR 门户发送到 Azure AD 中存储的主要和备用电子邮件地址。 不会向其他任何人告知已发生重置事件。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>当其他管理员重置其密码时通知所有管理员

如果此选项设置为“是”，则所有其他 Azure 管理员都会通过他们在 Azure AD 中存储的主要电子邮件地址收到一封电子邮件。 该电子邮件告知另一位管理员已使用 SSPR 更改了他们的密码。

请考虑以下示例场景：

* 某个环境中有四名管理员。
* 管理员 A 使用 SSPR 重置了其他管理员的密码。
* 管理员 B、C、D 会收到一封电子邮件，告知其已发生密码重置。  

## <a name="on-premises-integration"></a>本地集成

如果你有一个混合环境，则可将 Azure AD Connect 配置为将密码更改事件从 Azure AD 写回到本地目录。

![验证密码写回是否已启用且正常工作][Writeback]

Azure AD 会检查当前的混合连接，并在 Azure 门户中提供以下消息之一：

* 已启动并运行本地写回客户端。
* Azure AD 处于联机状态并连接到本地写回客户端。 但是，似乎 Azure AD Connect 的已安装版本已经过期了。 请考虑[更新 Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md)，确保拥有最新连接功能和重要 bug 修复。
* 很遗憾，因为 Azure AD Connect 的已安装版本已过期，我们无法查看你的本地写回客户端状态。 [更新 Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) 可查看连接状态。
* 很遗憾，现在似乎无法连接到本地写回客户端。 [对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。
* 很遗憾，因为密码写回未正确配置，无法连接到本地写回客户端。 [配置密码写回](./tutorial-enable-sspr-writeback.md)以还原连接。
* 很遗憾，现在似乎无法连接到本地写回客户端。 这可能是因我们终端的临时问题导致。 如果问题仍然存在，[对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。

若要开始 SSPR 写回，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助式密码重置 (SSPR) 写回](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>将密码写回到本地目录

可以通过 Azure 门户启用密码写回。 还可以暂时禁用密码写回，无需重新配置 Azure AD Connect。

* 如果将此选项设置为“是”，则会启用写回。 联合身份验证、传递身份验证或密码哈希同步的用户能够重置其密码。
* 如果将此选项设置为“否”，则会禁用写回。 联合身份验证、传递身份验证或密码哈希同步的用户无法重置其密码。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>允许用户在不重置密码的情况下解锁帐户

默认情况下，Azure AD 在执行密码重置时会解锁帐户。 为了提供灵活性，你可以选择允许用户在不需重置其密码的情况下解锁其本地帐户。 使用此设置可区分这两项操作。

* 如果设置为“是”，将为用户提供重置其密码和解锁帐户的选项，或者在无需重置密码的情况下解锁其帐户的选项。
* 如果设置为“否”，用户只能同时执行密码重置和帐户解锁的操作。

### <a name="on-premises-active-directory-password-filters"></a>本地 Active Directory 密码筛选器

SSPR 在 Active Directory 中执行管理员发起的密码重置的等效操作。 如果你使用第三方密码筛选器来强制实施自定义密码规则，并且你要求在 Azure AD 自助式密码重置期间检查此密码筛选器，请确保将第三方密码筛选器解决方案配置为应用于管理员密码重置场景。 默认情况下支持[Active Directory 域服务 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="password-reset-for-b2b-users"></a>B2B 用户的密码重置

所有企业到企业 (B2B) 配置完全支持密码重置和更改。 以下三种情况支持 B2B 用户密码重置：

* **已有 Azure AD 租户的合作伙伴组织中的用户**：如果与你合作的组织已有 Azure AD 租户，我们将遵守该租户中已启用的任何密码重置策略。 要使密码重置正常工作，合作伙伴组织只需确保启用 Azure AD SSPR。 对于 Microsoft 365 客户，不会额外收费。
* 通过自助注册**注册的用户**：如果与你合作的组织使用[自助注册](../users-groups-roles/directory-self-service-signup.md)功能来访问租户，我们会允许他们使用已注册的电子邮件来重置密码。
* **B2B 用户**：使用新的 [Azure AD B2B 功能](../external-identities/what-is-b2b.md)创建的任何新的 B2B 用户也可以使用他们在邀请过程中注册的电子邮件来重置其密码。

若要测试此方案，请通过这些合作伙伴用户之一转到 https://passwordreset.microsoftonline.com。 如果他们定义了备用电子邮件或身份验证电子邮件，则密码重置就能按预期方式工作。

> [!NOTE]
> 已被授予 Azure AD 租户来宾访问权限的 Microsoft 帐户（例如 Hotmail.com、Outlook.com 的电子邮件地址或其他个人电子邮件地址）无法使用 Azure AD SSPR。 他们需要使用[当你无法登录到 Microsoft 帐户时](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)文章中的信息重置其密码。

## <a name="next-steps"></a>后续步骤

若要开始使用 SSPR，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助式密码重置 (SSPR)](tutorial-enable-sspr.md)

以下文章提供了有关通过 Azure AD 进行密码重置的更多信息：

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "可用的 Azure AD 身份验证方法和所需数量"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "在 Azure 门户中配置 SSPR 注册选项"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Azure 门户中的 SSPR 的本地集成"