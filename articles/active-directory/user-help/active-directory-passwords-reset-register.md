---
title: 注册自助密码重置 - Azure Active Directory | Microsoft Docs
description: 注册身份验证数据，以便进行 Azure AD 自助密码重置
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.openlocfilehash: f8b4fb8861760d2b5b10f61b3ab6a8c718f849b6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346386"
---
# <a name="register-for-self-service-password-reset"></a>注册自助密码重置

> [!IMPORTANT]
> 你是因为无法登录而来到这里吗？ 如果是，请参阅[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)。

最终用户可以使用 Azure Active Directory (Azure AD) 自助密码重置 (SSPR) 自行重置密码或解锁帐户。 在使用此功能之前，必须注册身份验证方法或确认管理员填充的预定义身份验证方法。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>通过 SSPR 注册或确认身份验证数据

1. 在设备上打开 Web 浏览器，并转到[密码重置注册页](https://aka.ms/ssprsetup)。
2. 输入管理员提供的用户名和密码。
3. 可以使用以下一个或多个选项进行配置和验证，具体取决于 IT 人员的配置。 如果管理员有权使用你的信息，则可以填充其中的某些信息。
    * **办公电话**：只能由管理员设置此选项。
    * **身份验证电话**：将此选项设置为你可以访问的其他电话号码。 例如，可以接收短信或呼叫的手机号码。
    * **身份验证电子邮件**：将此选项设置为在不使用所要重置的密码的情况下可以访问的备用电子邮件地址。
    * **安全提问**：管理员已批准此问题列表供你回答。 不能多次使用同一问题或答案。
4. 提供并验证管理员所需的信息。 如果有多个选项可用，我们建议注册多个方法。 这样，在某个方法不可用时，可以灵活使用其他方法。 例如，在旅行时无法使用办公电话的情况下。

    ![注册身份验证方法并选择“完成”][Register]

5. 选择“完成”。 将来可根据需要使用 SSPR。

输入“身份验证电话”或“身份验证电子邮件”的数据时，这些数据在全局目录中不可见。 只有你和管理员能够看到该数据。 只有你能够查看安全提问的答案。

管理员可能会要求在一段时间过后确认身份验证方法，确保注册的方法仍然适用。

## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法

 以下是一些常见的错误案例及其解决方法：

| 错误案例| 看到什么错误？| 解决方案 |
| --- | --- | --- |
| 在输入我的用户 ID 后，出现了“请联系管理员”页面 | 请与管理员联系。 <br> <br> 我们检测到用户帐户密码不受 Microsoft 管理。 因此，我们无法自动重置密码。 <br> <br> 请与 IT 人员联系以获得任何进一步的帮助。 | 之所以看到此消息，是因为 IT 人员在本地环境中管理密码，而不允许从“无法访问帐户链接”重置密码。 <br> <br> 若要重置密码，请直接与 IT 人员联系以获得帮助。 告诉他们你想要重置密码，让他们启用此功能。|
| 在输入我的用户 ID 后，出现“帐户未针对密码重置进行启用”错误 | 帐户未启用密码重置。 <br> <br> 很抱歉，IT 人员尚未将帐户设置为可使用此服务。 <br> <br> 如果愿意，我们可以联系你所在组织的管理员重置密码。 | 之所以看到此消息，是因为 IT 人员未对组织启用从“无法访问帐户”链接重置密码的功能，或未授权你使用该功能。 <br> <br> 若要重置密码，请选择“联系管理员”链接。 随后会向公司 IT 人员发送一封电子邮件。 该电子邮件告诉他们你想要重置密码，让他们启用此功能。 |
| 在输入我的用户 ID 后，出现了“我们无法验证帐户”错误 | 我们无法验证帐户。 <br> <br> 如果愿意，我们可以联系你所在组织的管理员重置密码。 | 之所以看到此消息，是因为已经启用了密码重置，但你并未注册使用此服务。 若要通过注册进行密码重置，请在重新获取帐户访问权限后转到[密码重置注册页](https://aka.ms/ssprsetup)。 <br> <br> 若要重置密码，请选择“联系管理员”链接以向公司 IT 人员发送电子邮件。 |

## <a name="next-steps"></a>后续步骤

* [使用自助密码重置更改密码](active-directory-passwords-update-your-own-password.md)
* [密码重置注册页](https://aka.ms/ssprsetup)
* [密码重置门户](https://passwordreset.microsoftonline.com/)
* [无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "密码重置注册页，其中显示已注册的方法和“完成”按钮"

