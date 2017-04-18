---
title: "Azure AD：自助密码重置注册 | Microsoft Docs"
description: "注册身份验证数据，以便进行自助密码重置"
services: active-directory
keywords: "Active Directory 密码管理, 密码管理, Azure AD 自助密码重置, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>注册自助密码重置

作为最终用户，在管理员已启用自助密码重置 (SSPR) 的情况下，你可以通过它来重置密码或解锁帐户，而不需求助他人。 在使用此功能之前，必须注册身份验证方法或确认管理员填充的预定义身份验证方法。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>通过 SSPR 注册或确认身份验证数据

1. 在设备上打开 Web 浏览器，然后转到[密码重置注册页](http://aka.ms/ssprsetup)
2. 输入管理员提供的用户名和密码
3. 根据管理员批准的选项，你会看到一个或多个下述可供配置或验证的项目，可以在需要重置密码时使用
    * 办公电话 - 此选项只能由管理员设置
    * 身份验证电话 - 此选项应设置为你可以使用的另一电话号码，例如可以接收短信或通话的手机（管理员可能会使用你的手机号码为你填充 此项，前提是他们已获得你的许可，能够使用该信息）
    * 身份验证电子邮件 - 此选项应设置为你可以在不需重置密码的情况下访问的备用电子邮件地址
    * 安全问题 - 此选项为你提供一系列需要回答且已经过管理员批准的问题。 不得将同一答案用于多个问题。
4. 提供并验证管理员所需的信息。 如果多个选项可用，我们建议你注册多个方法，这样当某个方法不可用时（例如，旅行时无法使用办公电话），就可以灵活使用其他方法

    ![注册身份验证方法，然后单击“完成”][Register]

5. 完成步骤 4 以后，选择“完成”，这样就可以在将来根据需要使用自助密码重置。

在“身份验证电话”或“身份验证电子邮件”中输入数据时，这些数据在全局目录中不可见。 只有你和你的管理员能够看到该数据。 只有你能够看到安全问题的答案。

管理员可能会要求你在一段时间过后确认身份验证方法，确保你注册的方法仍然适用。

## <a name="next-steps"></a>后续步骤

* [如何使用自助密码重置更改密码](active-directory-passwords-update-your-own-password.md)
* [密码重置注册页](http://aka.ms/ssprsetup)
* [密码重置门户](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "密码重置注册页，其中显示已注册的方法和“完成”按钮"

