---
title: 注册身份验证信息以重置自己的密码 - Azure AD
description: 注册 Azure AD 自助式密码重置的验证方法信息，以便在没有管理员帮助的情况下重置自己的密码。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062636"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>注册验证方法信息以重置自己的密码

如果忘记了工作或学校密码、从来没有从组织处得到过密码，或者帐户被锁定，则可使用安全信息和移动设备来重置工作或学校密码。

管理员必须打开此功能，才能注册信息并重置自己的密码。 如果您没有看到"**忘记我的密码**"选项，则意味着管理员尚未打开组织的功能。 如果你认为这是错误，请联系支持人员以获取帮助。

>[!Important]
>本文适用于尝试使用注册进行自助服务密码重置的用户。 这意味着您将能够重置自己的工作或学校密码（例如），alain@contoso.com而无需管理员的帮助。 如果您是管理员，正在查找有关如何为员工或其他用户打开自助服务密码重置的信息，请参阅[部署 Azure AD 自助服务密码重置和其他文章](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)。

## <a name="set-up-your-password-reset-verification-method"></a>设置密码重置验证方法

1. 打开设备上的 Web 浏览器，然后转到[安全信息页面](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)。

2. 根据管理员如何设置组织，以下一个或多个选项将可供您设置为安全验证方法。 如果有多个选项可用，我们强烈建议您使用多个选项作为安全验证方法，以防其中一种方法不可用。

    - **身份验证应用。** 选择使用 Microsoft 身份验证器应用或其他身份验证器应用作为安全验证方法。 有关设置应用的详细信息，请参阅将 Microsoft[身份验证器应用设置为验证方法](security-info-setup-auth-app.md)。

    - **短信。** 选择将自己发送短信到移动设备。 有关设置文本消息的详细信息，请参阅[将文本消息设置为验证方法](security-info-setup-text-msg.md)。

    - **电话。** 选择拨打您注册的电话号码。 有关设置电话呼叫的详细信息，请参阅[设置电话号码作为验证方法](security-info-setup-phone-number.md)。

    - **安全密钥。** 选择使用与 Microsoft 兼容的安全密钥。 有关详细信息，请参阅[将安全密钥设置为验证方法](security-info-setup-security-key.md)。

    - **电子邮件地址。** 选择使用备用电子邮件地址，无需忘记或缺少密码即可使用。 这仅适用于密码重置，而不是作为安全验证方法。 有关设置电子邮件地址的详细信息，请参阅[设置电子邮件地址作为验证方法](security-info-setup-email.md)。

    - **安全问题。** 选择设置和回答管理员设置的预定义的安全问题。 这仅适用于密码重置，而不是作为安全验证方法。 有关安全问题的详细信息，请参阅[将安全问题设置为验证方法](security-info-setup-questions.md)。

3. 选择和设置方法后，选择 **"完成"** 以完成该过程。

    > [!Note]
    > 为电话号码或电子邮件地址添加的信息不会与组织的全局目录共享。 唯一能看到此信息的人是您和您的管理员。 只有你能够查看安全性问题的答案。

## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法

 以下是一些常见的错误案例及其解决方法：

| 错误消息 |  可能的解决方法 |
| --- | --- | --- |
| 请与管理员联系。<br>我们检测到用户帐户密码不受 Microsoft 管理。 因此，我们无法自动重置密码。<br>请与 IT 人员联系以获得任何进一步的帮助。| 如果您在键入用户 ID 后收到此错误消息，则意味着您的组织在内部管理您的密码，并且不希望您从 **"无法访问您的帐户**"链接重置密码。 要在此情况下重置密码，您必须联系组织的帮助台或管理员寻求帮助。 |
| 帐户未启用密码重置。<br>很抱歉，IT 人员尚未将帐户设置为可使用此服务。<br>如果愿意，我们可以联系你所在组织的管理员重置密码。 | 如果在键入用户 ID 后收到此错误消息，则表示您的组织未打开密码重置功能，或者不允许使用该功能。 要在此情况下重置密码，必须选择 **"联系管理员**"链接。 单击该链接后，将向组织的帮助台或管理员发送电子邮件，让他们知道要重置密码。 |
| 我们无法验证帐户。<br>如果愿意，我们可以联系你所在组织的管理员重置密码。 | 如果在键入用户 ID 后收到此错误消息，则表示您的组织已打开密码重置，您可以使用它，但尚未注册该服务。 在此情况下，您必须与组织的帮助台或管理员联系以重置密码。 有关在设备上重新注册密码重置的信息，请参阅本文中的上述过程。 |

## <a name="next-steps"></a>后续步骤

- [使用自助密码重置更改密码](active-directory-passwords-update-your-own-password.md)

- [安全信息页面](https://mysignins.microsoft.com/security-info)

- [密码重置门户](https://passwordreset.microsoftonline.com/)

- [无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
