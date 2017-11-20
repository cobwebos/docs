---
title: "Azure AD：重置密码 | Microsoft Docs"
description: "使用自助式密码重置功能重新获取工作用户帐户或学校用户帐户的访问权限"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 9595b318b8bd974f778dc12b38cccd949a4e7a1a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="reset-your-work-or-school-password"></a>重置工作或学校密码

如果忘记了密码、从来没有从公司支持人员处收到过密码、帐户被锁定，或者需要更改密码，则可从我们这里寻求帮助。 如果知道密码并且只需要更改密码，请继续查看以下[更改我的密码](#change-my-password)部分。

   > [!NOTE]
   > 若要取回个人帐户（例如 Xbox、hotmail.com 或 outlook.com），请尝试[此文提供的建议](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>重置或解锁工作或学校帐户的密码

如果因以下原因之一无法访问帐户：

* 密码不起作用并想要重置。
* 知道密码但帐户被锁定，想要解锁帐户。

按照以下步骤访问 Azure AD 自助式密码重置功能（简称为 SSPR），取回帐户。

1. 在任何工作或学校登录页中，单击“无法访问帐户?”链接，然后单击“工作或学校帐户”，或者直接转到[密码重置页](https://passwordreset.microsoftonline.com/)。

    ![无法访问帐户？][Login]

2. 输入工作或学校**用户 ID**，证明自己不是机器人，方法是：输入在屏幕上看到的字符，并单击“下一步”。

   > [!NOTE]
   > 如果 IT 人员尚未启用此功能，则会显示“联系管理员”链接，可以要求 IT 人员通过其电子邮件或 Web 门户提供帮助。
   >
   > 如果需要解锁帐户，此时应选择“我知道密码，但是仍然无法登录”选项。
   >

3. 根据 IT 人员配置 SSPR 的方式，会出现以下一个或多个身份验证方法。 使用[注册自助密码重置](active-directory-passwords-reset-register.md)一文之前，你或者 IT 人员已填充了其中一些信息。

   * **向我的备用电子邮件发送电子邮件**
   * **向我的手机发送短信**
   * **拨打我的手机电话**
   * **拨打我的办公电话**
   * **回答我的安全提问**

   选择一个选项，提供正确的响应，并单击“下一步”。

   ![验证身份验证数据][Verification]

4. IT 人员可能需要你进行更多验证，可能必须使用其他选择再次重复步骤 3 的操作。
5. 在“选择新密码”页上，输入新密码并确认，然后单击“完成”。 工作或学校密码可能有特定的要求；我们建议密码采用 8-16 位字符，并且包含大小写字符、数字和特殊字符。
6. 看到“密码已重置”后，即可使用新密码登录。

    ![密码已重置][Complete]

此时应可访问帐户，否则应联系组织的 IT 人员，寻求进一步的帮助。

你可能会收到一封确认电子邮件，该邮件来自“Microsoft 代表\<组织>”之类的帐户。 如果收到这样的电子邮件，但并未使用自助密码重置重新获取帐户访问权限，请与组织 IT 人员联系。

## <a name="change-my-password"></a>更改我的密码

如果已经知道自己的密码，需要对其进行更改，则可使用下述步骤来更改密码。

### <a name="change-your-password-from-the-office-365-portal"></a>从 Office 365 门户更改密码

如果通常使用 Office 门户来访问应用程序，则请使用此方法

1. 使用现有密码登录到 [Office 365 帐户](https://www.office.com)
2. 单击右上角的个人资料，并单击“查看帐户”
3. 单击“安全和隐私” > “密码”
4. 输入旧密码，设置并确认新密码，再单击“提交”

### <a name="change-your-password-from-the-azure-access-panel"></a>从 Azure 访问面板更改密码

如果通常使用 Azure 访问门户来访问应用程序，则请使用此方法

1. 使用现有密码登录到 [Azure 访问门户](https://myapps.microsoft.com/)
2. 单击右上角的个人资料，并单击“个人资料”
3. 单击“更改密码”
4. 输入旧密码，设置并确认新密码，再单击“提交”

## <a name="reset-password-at-login"></a>登录时重置密码

如果管理员已启用该功能，Windows 10 Fall Creators Update 登录屏幕上现在会出现“重置密码”链接。

![LoginScreen][LoginScreen]

单击“重置密码”链接会在登录屏幕上打开 SSPR 体验，以便可以重置密码，而无需登录访问普通的基于 Web 的体验。

1. 确认用户 ID，单击“下一步”
2. 选择并确认用于验证的联系方式。 IT 人员可能要求进行其他验证，在此情况下，必须使用不同的选项再次重复此步骤。

   ![ContactMethod][ContactMethod]

3. 在“创建新密码”页上，输入新密码并确认，然后单击“下一步”。 建议密码采用 8-16 位字符，并且包含大小写字符、数字和特殊字符。

   ![ResetPassword][ResetPassword]

4. 出现“密码已重置”时，请单击“完成”

此时应可访问帐户，否则应联系组织的 IT 人员，寻求进一步的帮助。

## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法

 以下是一些常见的错误案例及其解决方法：

| 错误案例| 看到什么错误？| 解决方案 |
| --- | --- | --- |
| 在输入我的用户 ID 后，出现了“请联系管理员”页面 | 请与管理员联系 <br> <br> 我们检测到用户帐户密码不受 Microsoft 管理。 因此，我们无法自动重置密码。 <br> <br> 需要与 IT 人员联系以获得任何进一步帮助。 | 之所以看到此消息，是因为 IT 人员在本地环境中管理密码，而不允许从“无法访问帐户链接”重置密码。 <br> <br> 若要重置密码，请直接联系 IT 人员获取帮助，并告知想要重置密码，以便他们能够为你启用此功能。|
| 在输入我的用户 ID 后，出现“帐户未针对密码重置进行启用”错误 | 未针对密码重置启用帐户 <br> <br> 很抱歉，IT 人员尚未将帐户设置为可使用此服务。 <br> <br> 如果愿意，我们可以联系你所在组织的管理员重置密码。 | 之所以看到此消息，是因为 IT 人员未为组织启用“从无法访问帐户”链接重置密码的功能，或未授权你使用该功能。 <br> <br> 若要重置密码，请单击“联系管理员”链接，向公司 IT 人员发送电子邮件并告知想要重置密码，以便他们能够为你启用此功能。 |
| 在输入我的用户 ID 后，出现了“我们无法验证帐户”错误 | 我们无法验证帐户 <br> <br> 如果愿意，我们可以联系你所在组织的管理员重置密码。 | 之所以看到此消息，是因为已经启用了密码重置，但你并未注册使用此服务。 若要注册密码重置，请在重新获取帐户访问权限后转到 http://aka.ms/ssprsetup。 <br> <br> 若要重置密码，请单击“联系管理员”链接以向公司 IT 人员发送电子邮件。 |

## <a name="next-steps"></a>后续步骤

* [如何通过注册来使用自助密码重置](active-directory-passwords-reset-register.md)
* [密码重置注册页](http://aka.ms/ssprsetup)
* [密码重置门户](https://passwordreset.microsoftonline.com/)
* [无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "登录页无法访问你的帐户？"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "验证身份验证数据"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "更改密码"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "密码已重置"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Windows 10 Fall Creators Update 登录屏幕中的“重置密码”链接"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "验证身份验证数据"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "更改密码"
