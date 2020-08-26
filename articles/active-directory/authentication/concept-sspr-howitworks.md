---
title: 自助式密码重置深入探讨 - Azure Active Directory
description: 自助密码重置的工作原理
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce07575fe95ffbd4fd906bcde7d76d89e50d48b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716310"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>工作原理：Azure AD 自助密码重置

Azure Active Directory (Azure AD) 自助式密码重置 (SSPR) 使用户能够更改或重置其密码，而不需要管理员或支持人员的干预。 如果用户的帐户被锁定或用户忘记了自己的密码，他们可以按照提示取消对自己的阻止，回到工作状态。 当用户无法登录到其设备或应用程序时，此功能可减少呼叫支持人员的次数，降低生产力损失。

> [!IMPORTANT]
> 此概念文章向管理员说明了自助服务密码重置的工作方式。 如果你是已经注册自助密码重置的最终用户，并且需要返回到你的帐户，请转到 [https://aka.ms/sspr](https://aka.ms/sspr) 。
>
> 如果你的 IT 团队尚未启用重置自己密码的功能，请联系支持人员以获得更多帮助。

## <a name="how-does-the-password-reset-portal-work"></a>密码重置门户的工作原理

用户可以使用 [SSPR 门户](https://aka.ms/sspr)重置或更改其密码。 它们必须首先注册其所需的身份验证方法。 当用户访问 SSPR 门户时，Azure 平台会考虑以下因素：

* 如何本地化该页面？
* 用户帐户是否有效？
* 该用户属于哪个组织？
* 用户的密码管理在何处？
* 是否已授权该用户使用该功能？

当用户从应用程序或页面中选择 " **无法访问帐户** " 链接或直接转到时 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) ，SSPR 门户中使用的语言基于以下选项：

* 默认情况下，浏览器区域设置用于以适当的语言显示 SSPR。 密码重置体验已本地化为 [Office 365 支持](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)的相同语言。
* 如果要链接到特定本地化语言的 SSPR，请将附加 `?mkt=` 到密码重置 URL 的末尾，并连同所需的区域设置。
    * 例如，若要指定西班牙语（ *es* ）区域设置，请使用 `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) 。

使用所需的语言显示 SSPR 门户之后，系统会提示用户输入用户 ID 并传递 captcha。 Azure AD 现在通过执行以下检查来验证用户是否能够使用 SSPR：

* 检查用户是否启用了 SSPR 并为其分配了 Azure AD 许可证。
  * 如果未为用户启用 SSPR 或未分配许可证，则会要求用户联系其管理员重置其密码。
* 检查用户是否具有针对其帐户定义且符合管理员策略的正确身份验证方法。
  * 如果策略只需要一个方法，请检查用户是否具有为管理员策略启用的至少一个身份验证方法定义的适当数据。
    * 如果未配置身份验证方法，则建议用户联系其管理员重置其密码。
  * 如果策略需要两种方法，请检查用户是否具有为管理员策略启用的至少两个身份验证方法定义的适当数据。
    * 如果未配置身份验证方法，则建议用户联系其管理员重置其密码。
  * 如果为用户分配了 Azure 管理员角色，则会强制实施强双门密码策略。 有关详细信息，请参阅 [管理员重置策略差异](concept-sspr-policy.md#administrator-reset-policy-differences)。
* 检查是否在本地管理用户密码，如 Azure AD 租户使用联合身份验证、传递身份验证或密码哈希同步：
  * 如果已配置 SSPR 写回，并且用户的密码在本地管理，则允许用户继续进行身份验证并重置其密码。
  * 如果未部署 SSPR 写回且在本地管理用户密码，则要求用户联系其管理员重置其密码。

如果前面的所有检查都已成功完成，则将指导用户完成重置或更改其密码的过程。

若要开始使用 SSPR，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助式密码重置 (SSPR)](tutorial-enable-sspr.md)

## <a name="registration-options"></a>注册选项

用户必须先注册自己以及要使用的身份验证方法，然后用户才能使用 SSPR 重置或更改其密码。 如前一部分中所述，用户必须注册 SSPR，并应用相应的许可证。

### <a name="require-users-to-register-when-they-sign-in"></a>要求用户在登录时注册

如果用户使用 Azure AD 登录到任何应用程序，则可以启用此选项以要求用户完成 SSPR 注册。 此工作流包括以下应用程序：

* Office 365
* Azure 门户
* 访问面板
* 联合应用程序
* 使用 Azure AD 的自定义应用程序

如果不需要注册，则在登录时不会提示用户，但可以手动注册。 用户可以访问 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 或选择访问面板中 "**配置文件**" 选项卡下的 "**注册密码重置**" 链接。

![Azure 门户中的 SSPR 的注册选项][Registration]

> [!NOTE]
> 用户可以通过选择 " **取消** " 或关闭窗口来关闭 SSPR 注册门户。 但是，在他们完成注册前，系统会提示他们每次登录时注册。
>
> 如果用户已登录，此中断用于注册 SSPR 不会中断用户的连接。

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>设置用户必须在几天后重新确认其身份验证信息

若要确保在需要重置或更改其密码时身份验证方法正确，你可以要求用户在特定时间段后确认其信息已注册信息。 仅当你启用 " **要求用户在登录时注册** " 选项时，此选项才可用。

提示用户确认其已注册方法的有效值为 *0* 至 *730* 天。 如果将此值设置为 *0* ，则表示不要求用户确认其身份验证信息。

## <a name="authentication-methods"></a>身份验证方法

如果为用户启用了 SSPR，则必须注册至少一种身份验证方法。 我们强烈建议你选择两种或多种身份验证方法，以便用户在需要时能够更灵活地访问一种方法。 有关详细信息，请参阅 [什么是身份验证方法？](concept-authentication-methods.md)。

以下身份验证方法可用于 SSPR：

* 移动应用通知
* 移动应用代码
* 电子邮件
* 移动电话
* 办公电话
* 安全性问题

如果用户已注册管理员已启用的身份验证方法，则只能重置密码。

> [!WARNING]
> 分配有 Azure *管理员* 角色的帐户需要使用 [管理员重置策略差异](concept-sspr-policy.md#administrator-reset-policy-differences)部分中定义的方法。

![Azure 门户中的身份验证方法选择][Authentication]

### <a name="number-of-authentication-methods-required"></a>所需身份验证方法的数量

你可以配置用户为了重置或解锁其密码而必须提供的可用身份验证方法的数目。 此值可以设置为 *1* 或 *2*。

用户可以和应该注册多个身份验证方法。 同样，强烈建议用户注册两种或多种身份验证方法，以使它们在需要时能够更灵活地访问一种方法。

如果用户在尝试使用 SSPR 时没有注册所需的最少方法，他们将看到一个错误页面，指示他们请求管理员重置其密码。 如果已为 SSPR 注册了现有用户，并且这些用户无法使用该功能，请注意将一个方法的所需方法数增加到了两个。 有关详细信息，请参阅以下部分中的 [更改身份验证方法](#change-authentication-methods)。

#### <a name="mobile-app-and-sspr"></a>移动应用和 SSPR

使用移动应用作为密码重置方法（如 Microsoft Authenticator 应用）时，请注意以下事项：

* 当管理员要求使用一种方法来重置密码时，验证码是唯一可用的选项。
* 当管理员需要使用两种方法来重置密码时，用户除了可以使用任何其他已启用的方法之外，还可以使用通知 **或** 验证代码。

| 重置所需的方法数 | 一个 | 两个 |
| :---: | :---: | :---: |
| 可用的移动应用功能 | 代码 | 代码或通知 |

当注册自助服务密码重置时，用户无法选择注册其移动应用 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 。 用户可以在中注册其移动应用 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ，或者在中的组合安全信息注册中注册 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 。

> [!IMPORTANT]
> 当只需要方法时，无法选择身份验证器应用作为唯一的身份验证方法。 同样，如果需要两个方法，则不能选择验证器应用和另一种方法。
>
> 在配置包含验证器应用的 SSPR 策略作为方法时，至少应选择一种方法，而在需要配置两个方法时至少选择另外两个方法。
>
> 此要求是因为当前 SSPR 注册体验不包含用于注册验证器应用的选项。 用于注册验证器应用的选项包含在新的 [组合注册体验](./concept-registration-mfa-sspr-combined.md)中。
>
> 允许只使用验证器应用 (的策略) 或验证器应用时，如果需要) 两个方法，则仅使用一种附加方法 (，则在将用户配置为使用新的组合注册体验之前，将导致阻止用户注册 SSPR。

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
1. 如果用户未填写移动电话或备用电子邮件字段，则无法重置其密码。

## <a name="notifications"></a>通知

为了改善对密码事件的认识，SSPR 允许用户和标识管理员配置通知。

### <a name="notify-users-on-password-resets"></a>重置密码时通知用户

如果将此选项设置为 **"是"**，则重置其密码的用户将收到一封电子邮件，通知他们其密码已更改。 电子邮件通过 SSPR 门户发送到存储在 Azure AD 中的主电子邮件地址和备用电子邮件地址。 不会向其他任何人告知已发生重置事件。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>当其他管理员重置其密码时通知所有管理员

如果此选项设置为 **"是"**，则所有其他 Azure 管理员都将收到一封电子邮件，其中存储在 Azure AD 中的主电子邮件地址。 该电子邮件告知另一位管理员已使用 SSPR 更改了他们的密码。

请考虑以下示例场景：

* 某个环境中有四名管理员。
* 管理员 *A* 使用 SSPR 重置密码。
* 管理员 *B*、 *C*和 *D* 将收到一封电子邮件，提醒他们密码已重置。

## <a name="on-premises-integration"></a>本地集成

如果你有混合环境，则可以将 Azure AD Connect 配置为将密码更改事件从 Azure AD 写入到本地目录。

![验证密码写回是否已启用且正常工作][Writeback]

Azure AD 检查当前的混合连接，并在 Azure 门户中提供以下消息之一：

* 本地写回客户端已启动并正在运行。
* Azure AD 处于联机状态并连接到本地写回客户端。 但是，似乎 Azure AD Connect 的已安装版本已经过期了。 请考虑[更新 Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md)，确保拥有最新连接功能和重要 bug 修复。
* 遗憾的是，我们无法检查本地写回客户端状态，因为安装的 Azure AD Connect 版本已过期。 [更新 Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) 可查看连接状态。
* 很遗憾，现在似乎无法连接到本地写回客户端。 [对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。
* 很遗憾，因为密码写回未正确配置，无法连接到本地写回客户端。 [配置密码写回](./tutorial-enable-sspr-writeback.md)以还原连接。
* 很遗憾，现在似乎无法连接到本地写回客户端。 这可能是因我们终端的临时问题导致。 如果问题仍然存在，[对 Azure AD Connect 进行故障排除](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以还原连接。

若要开始 SSPR 写回，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助式密码重置 (SSPR) 写回](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>将密码写回到本地目录

可以使用 Azure 门户启用密码写回。 你还可以暂时禁用密码写回，无需重新配置 Azure AD Connect。

* 如果将此选项设置为 **"是"**，则将启用写回。 联合身份验证、传递身份验证或密码哈希同步的用户能够重置其密码。
* 如果将此选项设置为 " **否**"，则将禁用写回。 联合身份验证、传递身份验证或密码哈希同步的用户无法重置其密码。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>允许用户在不重置密码的情况下解锁帐户

默认情况下，Azure AD 在执行密码重置时会解锁帐户。 为了提供灵活性，你可以选择允许用户解锁其本地帐户，而无需重置其密码。 使用此设置来分隔这两个操作。

* 如果设置为 **"是"**，则将向用户提供重置其密码和解锁帐户的选项，或者在不重置密码的情况下解锁其帐户。
* 如果设置为 " **否**"，则用户只能执行组合的密码重置和帐户解锁操作。

### <a name="on-premises-active-directory-password-filters"></a>本地 Active Directory 密码筛选器

在 Active Directory 中，SSPR 执行管理员启动的密码重置的等效操作。 如果你使用第三方密码筛选器来强制使用自定义密码规则，并且你需要在 Azure AD 自助密码重置过程中检查此密码筛选器，请确保将第三方密码筛选器解决方案配置为在管理员密码重置方案中应用。 默认情况下支持[Active Directory 域服务 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="password-reset-for-b2b-users"></a>B2B 用户的密码重置

所有企业到企业 (B2B) 配置完全支持密码重置和更改。 以下三种情况支持 B2B 用户密码重置：

* **具有现有 Azure AD 租户的合作伙伴组织中的用户**：如果与之合作的组织拥有现有的 Azure AD 租户，我们将遵守该租户上启用的任何密码重置策略。 要使密码重置正常工作，合作伙伴组织只需确保启用 Azure AD SSPR。 对于 Office 365 客户，不收取额外费用。
* **通过** 自助注册注册的用户：如果你与之合作的组织使用 [自助](../users-groups-roles/directory-self-service-signup.md) 注册功能来访问租户，我们将允许他们使用注册的电子邮件来重置密码。
* **B2b 用户**：通过使用新的 [Azure AD b2b 功能](../external-identities/what-is-b2b.md) 创建的任何新 b2b 用户也可以使用他们在邀请过程中注册的电子邮件来重置其密码。

若要测试此方案，请通过这些合作伙伴用户之一转到 https://passwordreset.microsoftonline.com。 如果他们定义了备用电子邮件或身份验证电子邮件，则密码重置就能按预期方式工作。

> [!NOTE]
> 已获授权访问你的 Azure AD 租户的 Microsoft 帐户（例如来自 Hotmail.com、Outlook.com 或其他个人电子邮件地址的帐户）无法使用 Azure AD SSPR。 他们需要使用[当你无法登录到 Microsoft 帐户时](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)文章中的信息重置其密码。

## <a name="next-steps"></a>后续步骤

若要开始使用 SSPR，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助式密码重置 (SSPR)](tutorial-enable-sspr.md)

以下文章提供了有关通过 Azure AD 进行密码重置的更多信息：

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "可用的 Azure AD 身份验证方法和所需数量"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "在 Azure 门户中配置 SSPR 注册选项"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Azure 门户中的 SSPR 的本地集成"