---
title: 电话身份验证方法-Azure Active Directory
description: 了解如何在 Azure Active Directory 中使用手机身份验证方法，以帮助改进和保护登录事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09093a5dbd2142d3542e86a99e24f228320453d4
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532593"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Azure Active Directory 电话选项中的身份验证方法

对于使用文本消息的直接身份验证，可以[为用户配置和启用基于 SMS 的身份验证（预览）](howto-authentication-sms-signin.md)。 基于 SMS 的登录方式对于一线工作人员来说非常适合。 使用基于 SMS 的登录，用户无需知道用户名和密码即可访问应用程序和服务。 用户的验证方式改为：输入其注册的手机号码，收到带有验证码的短信，并在登录界面中输入该验证码。

用户还可以在 Azure 多重身份验证或自助服务密码重置 (SSPR) 期间使用移动电话或办公室电话号码作为辅助的身份验证形式进行身份验证。

若要正常工作，电话号码必须采用“+国家/地区代码 电话号码”格式，例如 +1 4251234567 。

> [!NOTE]
> 在国家/地区代码和电话号码之间需要有一个空格。
>
> 密码重置不支持电话分机。 即使采用“1 4251234567X12345”格式，在拨出电话前也会删除分机。

## <a name="mobile-phone-verification"></a>手机验证

对于 Azure 多重身份验证或 SSPR，用户可以选择接收短信，其中含有要在登录界面中输入的验证码，或者选择接听电话呼叫，接听后会听到一个提示，提示其输入事先定义的 PIN 码。

如果用户不希望在目录中公开其移动电话号码，但想使用该号码来重置密码，则管理员不应在目录中填充该号码。 而是由用户通过组合的安全信息注册 ([https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)) 来填充“身份验证电话”属性。 管理员可在用户的配置文件中看到此信息，但此信息不会发布到其他位置。

![屏幕截图显示已填充电话号码的身份验证方法的 Azure 门户](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft 不保证相同号码传送的短信或基于语音的 Azure 多重身份验证提示一致。 为了用户的利益，我们在做出路线调整期间可能随时添加或删除简短代码，以提高短信传送能力。 Microsoft 不支持除美国和加拿大以外的国家/地区的简短代码。

### <a name="text-message-verification"></a>短信验证

如果在 SSPR 或 Azure 多重身份验证期间使用短信验证方法，相应的手机号会接收到包含验证码的 SMS。 若要完成登录过程，需要在登录界面中输入提供的验证码。

### <a name="phone-call-verification"></a>电话呼叫验证

如果在 SSPR 或 Azure 多重身份验证期间使用电话呼叫验证方法，用户注册的电话号码会接收到一个自动语音呼叫。 为了完成登录过程，系统会提示用户在其键盘上输入其 pin 号码并按 # 键。

## <a name="office-phone-verification"></a>办公电话验证

办公电话属性由 Azure AD 管理员管理，用户本身无法注册。

如果在 SSPR 或 Azure 多重身份验证期间使用电话呼叫验证方法，用户注册的电话号码会接收到一个自动语音呼叫。 为了完成登录过程，系统会提示用户在其键盘上输入其 pin 号码并按 # 键。

## <a name="troubleshooting-phone-options"></a>排查电话选项问题

如果在使用 Azure AD 的手机身份验证时出现问题，请查看以下故障排除步骤：

* 单个设备上阻止了呼叫方 ID。
   * 查看设备上配置的任何被阻拦的号码。
* 电话号码错误或国家/地区代码不正确，或混淆个人电话号码和工作电话号码。
   * 对用户对象和配置的身份验证方法进行故障排除。 确保注册了正确的电话号码。
* 输入了错误的 PIN。
   * 确认用户使用的是注册帐户时所用的正确的 PIN。
* 呼叫被转到语音信箱。
   * 确保用户的电话处于开启状态，并且所需服务在其区域中可用，或使用替代方法。
* 用户被阻止
   * 让 Azure AD 管理员在 Azure 门户中取消阻止该用户。
* 设备上未订阅 SMS。
   * 让用户更改方法或在设备上激活 SMS。
* 电信提供程序有问题，如检测不到电话输入、DTMF 音调缺失的问题、在多台设备上阻止呼叫方 ID 或在多台设备阻止 SMS。
   * Microsoft 使用多个电信提供程序来路由电话呼叫和短信以进行身份验证。 如果发生上述任何问题，请让用户尝试在 5 分钟内至少使用此方法 5 次，并在联系 Microsoft 支持人员时提供该用户的信息。

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[自助式密码重置 (SSPR) 的教程][tutorial-sspr]和 [Azure 多重身份验证][tutorial-azure-mfa]。

要详细了解 SSPR 概念，请参阅[ Azure AD 自助式密码重置的工作原理][concept-sspr]。

要详细了解 MFA 概念，请参阅[ Azure 多重身份验证的工作原理][concept-mfa]。

详细了解如何使用 [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)配置身份验证方法。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
