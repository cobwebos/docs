---
title: "Azure AD：SSPR 注册 | Microsoft Docs"
description: "注册身份验证数据，以便进行 Azure AD 自助密码重置"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: bd0175558a94a146a365e8c1a32522f1e54c184e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


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

## <a name="next-steps"></a>后续步骤

* [如何使用自助密码重置更改密码](active-directory-passwords-update-your-own-password.md)
* [密码重置注册页](http://aka.ms/ssprsetup)
* [密码重置门户](https://passwordreset.microsoftonline.com/)
* [无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "密码重置注册页，其中显示已注册的方法和“完成”按钮"


