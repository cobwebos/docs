---
title: "Azure AD：SSPR 注册 | Microsoft Docs"
description: "注册身份验证数据，以便进行 Azure AD 自助密码重置"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 485dfa067bd64312f6ae68a533117db1556318ed
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---
# <a name="register-for-self-service-password-reset"></a>注册自助密码重置

作为最终用户，你可以使用自助密码重置 (SSPR) 来重置密码或解锁帐户，而不需求助他人。 在使用此功能之前，必须注册身份验证方法或确认管理员填充的预定义身份验证方法。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>通过 SSPR 注册或确认身份验证数据

1. 在设备上打开 Web 浏览器，然后转到[密码重置注册页](http://aka.ms/ssprsetup)
2. 输入管理员提供的用户名和密码
3. 你可以使用以下一个或多个选项进行配置和验证，具体取决于 IT 人员的配置。 管理员可以为你填充其中某些项（如果他们有权使用这些信息）。
    * 办公电话只能由管理员设置
    * 身份验证电话应设置为你可以访问的其他电话号码，如可以接收短信或呼叫的手机。
    * 身份验证电子邮件应设置为你可以在不需重置密码的情况下访问的备用电子邮件地址。
    * 安全问题为你提供一系列需要回答且已经过管理员批准的问题。 不得多次使用同一问题或答案。
4. 提供并验证管理员所需的信息。 如果有多个选项可用，我们建议你注册多个方法，这样当某个方法不可用时（例如，旅行时无法使用办公电话），就可以灵活使用其他方法

    ![注册身份验证方法，然后单击“完成”][Register]

5. 完成步骤 4 以后，选择“完成”，这样就可以在将来根据需要使用自助密码重置。

在“身份验证电话”或“身份验证电子邮件”中输入数据时，这些数据在全局目录中不可见。 只有你和你的管理员能够看到该数据。 只有你能够看到安全问题的答案。

管理员可能会要求你在一段时间过后确认身份验证方法，确保你注册的方法仍然适用。

## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法

 以下是一些常见的错误案例及其解决方法：

| 错误案例| 看到什么错误？| 解决方案 |
| --- | --- | --- |
| 在输入我的用户 ID 后，出现了“请联系管理员”页面 | 请与管理员联系 <br> <br> 我们检测到你的用户帐户密码不受 Microsoft 管理。 因此，我们无法自动重置密码。 <br> <br> 你需要与 IT 人员联系以获得任何进一步帮助。 | 你之所以看到此消息，是因为 IT 人员在本地环境中管理你的密码，而不允许你从“无法访问帐户链接”重置密码。 <br> <br> 若要重置密码，请直接向 IT 人员寻求帮助，使其了解你想要重置密码，从而为你启用此功能。|
| 在输入我的用户 ID 后，出现“你的帐户未针对密码重置进行启用”错误 | 未针对密码重置启用帐户 <br> <br> 很抱歉， IT 人员尚未将你的帐户设置为可使用此服务。 <br> <br> 如果你愿意，我们可以联系你所在组织的管理员为你重置密码。 | 你之所以看到此消息，是因为 IT 人员未为你的组织启用“从无法访问帐户”链接重置密码的功能，或未授权你使用该功能。 <br> <br> 若要重置密码，请单击“联系管理员”链接，向公司 IT 人员发送电子邮件，使其了解你想要重置密码，从而为你启用此功能。 |
| 在输入我的用户 ID 后，出现了“我们无法验证你的帐户”错误 | 我们无法验证你的帐户 <br> <br> 如果你愿意，我们可以联系你所在组织的管理员为你重置密码。 | 你之所以看到此消息，是因为已经为你启用了密码重置，但你并未注册使用此服务。 若要注册密码重置，请在重新获取帐户访问权限后转到 http://aka.ms/ssprsetup。 <br> <br> 若要重置密码，请单击“联系管理员”链接以向公司 IT 人员发送电子邮件。 |

## <a name="next-steps"></a>后续步骤

* [如何使用自助密码重置更改密码](active-directory-passwords-update-your-own-password.md)
* [密码重置注册页](http://aka.ms/ssprsetup)
* [密码重置门户](https://passwordreset.microsoftonline.com/)
* [无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "密码重置注册页，其中显示已注册的方法和“完成”按钮"


