---
title: 注册身份验证信息以重置你自己的密码 - Azure AD
description: 注册验证方法信息以实现 Azure AD 自助服务密码重置，以便在没有管理员帮助的情况下重置自己的密码。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88799020"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>注册验证方法信息以重置你自己的密码

如果忘记了工作或学校密码、从来没有从组织处得到过密码，或者帐户被锁定，则可使用安全信息和移动设备来重置工作或学校密码。

你的管理员必须打开此功能，你才能注册你的信息并重置你的密码。 如果看不到“忘记密码”选项，则表明管理员尚未为你的组织打开此功能。 如果你认为这是错误，请联系支持人员以获取帮助。

>[!Important]
>本文适用于尝试使用自助服务密码重置注册的用户。 这意味着，你将能够重置自己的工作或学校密码 (例如 alain@contoso.com) ，而无需管理员的帮助。 如果你是管理员，并且正在查找有关如何为员工或其他用户打开自助式密码重置的详细信息，请参阅[部署 Azure AD 自助式密码重置](../authentication/howto-sspr-deployment.md)以及其他文章。

## <a name="set-up-your-password-reset-verification-method"></a>设置密码重置验证方法

1. 在设备上打开 Web 浏览器，并转到[安全信息页](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)。

2. 根据管理员对组织的设置，你可以将以下一个或多个选项设置为安全验证方法。 如果有多个选项可用，强烈建议使用多个安全验证方法，以防其中之一出现故障。

    - **身份验证应用。** 选择使用 Microsoft Authenticator 应用或其他验证器应用作为安全验证方法。 有关设置应用的详细信息，请参阅[设置 Microsoft Authenticator 应用作为验证方法](security-info-setup-auth-app.md)。

    - **短信。** 选择将你自己的短信发送到移动设备。 有关设置短信的详细信息，请参阅[设置短信作为验证方法](security-info-setup-text-msg.md)。

    - **电话呼叫。** 选择给已注册的电话号码打电话。 有关设置电话呼叫的详细信息，请参阅[设置电话号码作为验证方法](security-info-setup-phone-number.md)。

    - **安全密钥。** 选择使用与 Microsoft 兼容的安全密钥。 有关详细信息，请参阅[设置安全密钥作为验证方法](security-info-setup-security-key.md)。

    - **电子邮件地址。** 选择使用备用电子邮件地址，当你忘记或丢失密码时，不需要密码也可使用该地址。 这仅适用于密码重置，而不能用作安全验证方法。 有关设置电子邮件地址的详细信息，请参阅[设置电子邮件地址作为验证方法](security-info-setup-email.md)。

    - **安全性问题。** 选择设置并回答管理员设置的预定义安全问题。 这仅适用于密码重置，而不能用作安全验证方法。 有关安全问题的详细信息，请参阅[设置安全问题作为验证方法](security-info-setup-questions.md)。

3. 选择并设置方法后，选择“完成”以完成此过程。

    > [!Note]
    > 为你的电话号码或电子邮件地址添加的信息未与组织的全局目录共享。 只有你和管理员能够看到该信息。 只有你能够查看安全性问题的答案。

## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法

 以下是一些常见的错误案例及其解决方法：

| 错误消息 |  可能的解决方案 |
| --- | --- | --- |
| 请与管理员联系。<br>我们检测到用户帐户密码不受 Microsoft 管理。 因此，我们无法自动重置密码。<br>请与 IT 人员联系以获得任何进一步的帮助。| 如果在键入用户 ID 后收到此错误消息，则表示组织在内部管理你的密码，并且不希望你从“无法访问你的帐户”链接重置你的密码。 在这种情况下，若要重置密码，必须与组织的支持人员或管理员联系以获取帮助。 |
| 帐户未启用密码重置。<br>很抱歉，IT 人员尚未将帐户设置为可使用此服务。<br>如果愿意，我们可以联系你所在组织的管理员重置密码。 | 如果在键入用户 ID 后收到此错误消息，则表示你的组织尚未启用密码重置功能，或者组织不允许你使用该功能。 在这种情况下，若要重置密码，必须选择“联系管理员”链接。 单击该链接后，会向组织的支持人员或管理员发送电子邮件，让他们知道你想重置密码。 |
| 我们无法验证帐户。<br>如果愿意，我们可以联系你所在组织的管理员重置密码。 | 如果在键入用户 ID 后收到此错误消息，则表示你的组织已启用密码重置，并且你可以使用它，但你尚未注册该服务。 在这种情况下，必须与组织的支持人员或管理员联系以重置密码。 有关在返回到设备后注册密码重置的信息，请参阅本文上面部分的过程。 |

## <a name="next-steps"></a>后续步骤

- [使用自助密码重置更改密码](active-directory-passwords-update-your-own-password.md)

- [安全信息页](https://mysignins.microsoft.com/security-info)

- [密码重置门户](https://passwordreset.microsoftonline.com/)

- [无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)