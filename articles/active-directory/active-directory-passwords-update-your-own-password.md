---
title: "Azure AD：重置密码 | Microsoft Docs"
description: "使用自助密码重置重新获取帐户访问权限"
services: active-directory
keywords: "Active Directory 密码管理, 密码管理, Azure AD 自助密码重置, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: bb62d6973dff00932e72ddb4b2c344f070537828
ms.lasthandoff: 04/14/2017


---
# <a name="help-i-forgot-my-password"></a>帮助 我忘记了密码

步骤 1... 勿慌

如果你是属于以下情形，则我们可以提供帮助

* 你不确定如何访问帐户，也不记得密码
* 没有指定密码，管理员将你引导到这里

## <a name="unlock-my-account"></a>解锁我的帐户

如果你在这里是要解锁自己的帐户，请执行下面的步骤。 在下面的步骤 6 看到“选择新密码”时，即可解锁或更改密码，然后你的帐户就解锁了。

## <a name="reset-my-password"></a>重置我的密码

若要重新登录帐户，请执行以下步骤。
1. 在任何工作或学校登录页中，单击“无法访问你的帐户?”链接，然后单击“工作或学校帐户”，或者直接转到[密码重置页](https://passwordreset.microsoftonline.com/)

    ![无法访问你的帐户？][Login]

2. 输入工作或学校**用户 ID** 并证明你不是机器人，方法是通过 CAPTCHA 质询，并输入显示的文本，然后单击“下一步”。

   > [!NOTE]
   > 如果管理员尚未启用此功能，此时会显示“联系管理员”链接，这样你就可以要求管理员通过电子邮件或他们自己的 Web 门户提供帮助。
   >

3. 根据管理员的配置方式，会出现以下一个或多个选项
    * **向我的备用电子邮件发送电子邮件** - 将包含 6 位数代码的电子邮件发送到你的备用电子邮件或身份验证电子邮件（由你选择）。
    * **向我的移动电话发送短信** - 将包含 6 位数代码的短信发送到你的移动电话或身份验证电子电话（由你选择）。
    * **拨打我的移动电话** - 拨打你的移动电话或身份验证电话（由你选择）。 按 # 键确认呼叫。
    * **拨打我的办公电话** - 拨打你的办公电话。 按 # 键确认呼叫。
    * **回答我的安全问题** - 显示预先注册的需要你回答的安全问题。
4. 用质询信息的答案填充必填字段，然后单击“下一步”

    ![验证身份验证数据][Verification]

5. 管理员可能要求你完成其他验证步骤，你可能必须再次重复步骤 4，选择不同的选项
6. 在“选择新密码”页上，输入符合组织要求的新密码，对密码进行确认，然后单击“完成”

    ![更改密码][Change]

7. 看到“你的密码已重置”后，即可使用新密码登录。

    ![你的密码已重置][Complete]

使用此方法解锁或重置密码以后，你会收到一封确认此过程已完成的电子邮件，该邮件来自“Microsoft 在此代表你的组织”之类的帐户。 如果你收到这样的电子邮件，且未使用自助密码重置重新获取帐户访问权限，请与管理员联系。

## <a name="change-my-password"></a>更改我的密码

如果你已经知道自己的密码，需要对其进行更改，则可尝试下述步骤

### <a name="change-your-password-from-the-office-365-portal"></a>从 Office 365 门户更改密码

1. 单击右上角的个人资料，然后单击“查看帐户”
2. **安全和隐私**
3. **密码**
4. 输入旧密码，然后设置并确认新密码
5. **提交**

### <a name="change-your-password-from-the-azure-access-panel"></a>从 Azure 访问面板更改密码

1. 使用现有密码登录到 [Azure 访问门户](https://myapps.microsoft.com/)
2. 单击右上角的个人资料，然后单击“个人资料”
3. **更改密码**
4. 输入旧密码，然后设置并确认新密码
5. **提交**

## <a name="next-steps"></a>后续步骤

* [如何通过注册来使用自助密码重置](active-directory-passwords-reset-register.md)
* [密码重置注册页](http://aka.ms/ssprsetup)
* [密码重置门户](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "登录页无法访问你的帐户？"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "验证身份验证数据"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "更改密码"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "密码已重置"

