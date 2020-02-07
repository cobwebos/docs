---
title: 注册身份验证信息以重置你自己的密码-Azure AD
description: 注册验证方法信息以 Azure AD 自助服务密码重置，以便您可以在没有管理员帮助的情况下重置自己的密码。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062636"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>注册验证方法信息以重置你自己的密码

如果忘记了工作或学校密码、从来没有从组织处得到过密码，或者帐户被锁定，则可使用安全信息和移动设备来重置工作或学校密码。

您的管理员必须打开此功能才能注册您的信息和重置自己的密码。 如果看不到 "**忘记了密码**" 选项，则表示你的管理员未为你的组织启用此功能。 如果你认为这是错误，请联系支持人员以获取帮助。

>[!Important]
>本文适用于尝试使用注册自助服务密码重置的用户。 这意味着你将能够重置自己的工作或学校密码（如 alain@contoso.com），而无需管理员的帮助。 如果你是一名管理员，想要了解有关如何为你的员工或其他用户启用自助服务密码重置的信息，请参阅[部署 Azure AD 自助服务密码重置和其他文章](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)。

## <a name="set-up-your-password-reset-verification-method"></a>设置密码重置验证方法

1. 在设备上打开 web 浏览器，并中转到 "[安全信息" 页](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)。

2. 根据你的管理员设置组织的方式，你可以将以下一个或多个选项设置为安全验证方法。 如果有多个选项可用，我们强烈建议使用多个选项作为安全验证方法，以防其中一个方法不可用。

    - **身份验证应用。** 选择使用 Microsoft Authenticator 应用或其他验证器应用作为安全验证方法。 有关设置应用的详细信息，请参阅[将 Microsoft Authenticator 应用设置为验证方法](security-info-setup-auth-app.md)。

    - **文本消息。** 选择将自己的短信发送到移动设备。 有关设置短信的详细信息，请参阅[设置短信作为验证方法](security-info-setup-text-msg.md)。

    - **电话呼叫。** 选择拨打注册电话号码的电话号码。 有关设置电话呼叫的详细信息，请参阅[将电话号码设置为验证方法](security-info-setup-phone-number.md)。

    - **安全密钥。** 选择使用与 Microsoft 兼容的安全密钥。 有关详细信息，请参阅[将安全密钥设置为验证方法](security-info-setup-security-key.md)。

    - **电子邮件地址。** 选择使用备用电子邮件地址，该地址可以使用，而无需忘记或丢失密码。 这仅适用于密码重置，而不是作为一种安全验证方法。 有关设置电子邮件地址的详细信息，请参阅[将电子邮件地址设置为验证方法](security-info-setup-email.md)。

    - **安全性问题。** 选择设置并回答管理员设置的预定义安全问题。 这仅适用于密码重置，而不是作为一种安全验证方法。 有关安全问题的详细信息，请参阅[将安全问题设置为验证方法](security-info-setup-questions.md)。

3. 选择并设置了方法后，选择 "**完成**" 以完成该过程。

    > [!Note]
    > 为你的电话号码或电子邮件地址添加的信息不与你组织的全局目录共享。 只有您和您的管理员才能看到此信息。 只有你能够查看安全性问题的答案。

## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法

 以下是一些常见的错误案例及其解决方法：

| 错误消息 |  可能的解决方案 |
| --- | --- | --- |
| 请与管理员联系。<br>我们检测到用户帐户密码不受 Microsoft 管理。 因此，我们无法自动重置密码。<br>请与 IT 人员联系以获得任何进一步的帮助。| 如果在键入你的用户 ID 后收到此错误消息，则表示你的组织在内部管理你的密码，并且不希望你从 "**无法访问帐户**" 链接重置密码。 若要在这种情况下重置密码，你必须与组织的技术支持或管理员联系以获取帮助。 |
| 帐户未启用密码重置。<br>很抱歉，IT 人员尚未将帐户设置为可使用此服务。<br>如果你愿意，我们可以联系你所在组织的管理员为你重置密码。 | 如果在键入你的用户 ID 后收到此错误消息，则表示你的组织尚未启用密码重置功能，或者你不允许使用该功能。 若要在这种情况下重置密码，你必须选择 "**联系管理员**" 链接。 单击该链接后，会向组织的技术支持人员或管理员发送一封电子邮件，让他们知道您要重置密码。 |
| 我们无法验证帐户。<br>如果你愿意，我们可以联系你所在组织的管理员为你重置密码。 | 如果在键入你的用户 ID 后收到此错误消息，则表示你的组织已启用密码重置，并且你可以使用它，但你尚未注册该服务。 在这种情况下，你必须与组织的技术支持或管理员联系以重置你的密码。 有关在返回到设备后要注册密码重置的信息，请参阅本文前面的过程。 |

## <a name="next-steps"></a>后续步骤

- [使用自助密码重置更改密码](active-directory-passwords-update-your-own-password.md)

- [安全信息页](https://mysignins.microsoft.com/security-info)

- [密码重置门户](https://passwordreset.microsoftonline.com/)

- [无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
