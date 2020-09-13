---
title: 通过自定义策略进行电话注册和登录
titleSuffix: Azure AD B2C
description: 通过 Azure Active Directory B2C 中的自定义策略，将文本消息中的一次性密码 (OTP) 发送到你的应用程序用户的手机。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4a429314d4a992ea93f4c068203371cda769a4ff
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029119"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>在 Azure AD B2C 中设置自定义策略的手机注册和登录

通过使用 Azure Active Directory B2C (Azure AD B2C 的 "手机注册和登录") ，用户可以通过使用一次性密码 (OTP) 在手机中发送的一次性密码来注册和登录应用程序。 一次性密码可帮助最大程度地减少用户忘记密码或密码泄露的风险。

按照本文中的步骤使用自定义策略，使客户能够使用发送到其电话的一次性密码来注册和登录到你的应用程序。

## <a name="pricing"></a>定价

一次性密码会通过使用短信发送给用户，并且你可能会对每个发送的消息付费。 有关定价信息，请参阅[Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)的**单独费用**部分。

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>电话注册和登录的用户体验

通过电话注册和登录，用户可以使用电话号码作为其主要标识符注册应用。 下面介绍了注册和登录过程中的最终用户体验。

> [!NOTE]
> 我们强烈建议你在注册和登录体验中包含许可信息，这与下面的示例文本类似。 此示例文本仅用于提供信息。 请参阅 [CTIA 网站](https://www.ctia.org/programs) 上的简短代码监视手册，并向自己的法律或合规性专家咨询你的最终文本和功能配置指导，以满足你自己的符合性需求：
>
> *通过提供电话号码，你同意接收短信发送的一次性密码，以帮助你登录到* &lt; insert：你的应用程序名称 &gt; *。标准消息和数据速率可能适用。*
>
> *&lt;insert：隐私声明的链接&gt;*<br/>*&lt;insert：服务条款的链接&gt;*

若要添加你自己的许可信息，请自定义以下示例，并将其包含在 LocalizedResources 中，其中包含自断言页面所使用的 ContentDefinition 的，并将其 (显示在电话注册 & 登录初学者包中的 Phone-Email-Base.xml 文件) ：

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard messsage and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>电话注册体验

如果用户还没有应用程序的帐户，则可以通过选择 " **立即注册** " 链接创建一个帐户。 此时将显示注册页面，用户可在其中选择 **国家/地区**，输入电话号码，然后选择 " **发送代码**"。

![用户开始电话注册](media/phone-authentication/phone-signup-start.png)

一次性验证码会发送到用户的电话号码。 用户在注册页上输入 **验证代码** ，然后选择 " **验证代码**"。  (如果用户无法检索代码，则他们可以选择 " **发送新代码**"。 ) 

![用户在电话注册期间验证代码](media/phone-authentication/phone-signup-verify-code.png)

 用户输入注册页上请求的任何其他信息，例如 **显示名称**、 **名字**和 **姓氏** (国家/地区和电话号码保持) 。 如果用户想要使用其他电话号码，则他们可以选择 " **更改编号** " 以重新注册。 完成后，用户选择 " **继续**"。

![用户提供附加信息](media/phone-authentication/phone-signup-additional-info.png)

接下来，要求用户提供一个恢复电子邮件。 用户输入其电子邮件地址，然后选择 " **发送验证码**"。 代码将发送到用户的电子邮件收件箱，用户可以在 " **验证码** " 框中检索并输入该收件箱。 然后，用户选择 " **验证代码**"。 

验证代码后，用户可以选择 " **创建** " 来创建帐户。 如果用户想要使用不同的电子邮件地址，他们可以选择 " **更改电子邮件**"。

![用户创建帐户](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>手机登录体验

如果用户有一个使用电话号码作为标识符的现有帐户，则用户将输入其电话号码，然后选择 " **继续**"。 它们通过选择 " **继续**" 确认国家/地区和电话号码，并将一次性验证代码发送到其手机。 用户输入验证码，并选择 " **继续** " 以登录。

![电话登录用户体验](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>删除用户帐户

在某些情况下，你可能需要从 Azure AD B2C 目录中删除用户和关联的数据。 有关如何通过 Azure 门户删除用户帐户的详细信息，请参阅 [这些说明](https://docs.microsoft.com/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete)。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>先决条件

在设置 OTP 之前，需要准备好以下资源。

* [Azure AD B2C 租户](tutorial-create-tenant.md)
* 在租户中[注册的 Web 应用程序](tutorial-register-applications.md)
* 已上传到租户的[自定义策略](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>获取电话注册 & 登录初学者包

首先更新电话注册和登录自定义策略文件，以便与 Azure AD B2C 租户配合使用。

以下步骤假设已完成 [先决条件](#prerequisites) ，并已将 [自定义策略初学者包][starter-pack] 存储库克隆到本地计算机。

1. 在 starter pack 存储库的本地克隆中查找 [电话注册和登录自定义策略文件][starter-pack-phone] ，或直接下载它们。 XML 策略文件位于以下目录中：

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 在每个文件中，将字符串替换 `yourtenant` 为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 *contosob2c*，则的所有实例都将 `yourtenant.onmicrosoft.com` 变为 `contosob2c.onmicrosoft.com` 。

1. 完成[Azure Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的[将应用程序 id 添加到自定义策略](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)部分中的步骤。 在这种情况下，请更新 `/phone-number-passwordless/` **`Phone_Email_Base.xml`** **应用程序 (客户端) **在完成必备组件、 *IdentityExperienceFramework*和*ProxyIdentityExperienceFramework*时注册的两个应用程序的 id。

## <a name="upload-the-policy-files"></a>上传策略文件

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure AD B2C 租户。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择“上传自定义策略”。
1. 按以下顺序上传策略文件：
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

上传每个文件时，Azure 会添加前缀 `B2C_1A_` 。

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 在 " **自定义策略**" 下，选择 **B2C_1A_SignUpOrSignInWithPhone**。
1. 在 " **选择应用程序**" 下，选择在完成先决条件时注册的 *webapp1* 应用程序。
1. 对于 " **选择回复 url**"，请选择 `https://jwt.ms` 。
1. 选择 " **立即运行** " 并使用电子邮件地址或电话号码进行注册。
1. 选择 " **立即运行** " 并使用同一帐户登录，以确认配置正确。

## <a name="get-user-account-by-phone-number"></a>按电话号码获取用户帐户

使用电话号码注册但未提供恢复电子邮件地址的用户在 Azure AD B2C 目录中记录为其电话号码作为登录名。 如果用户希望更改其电话号码，则支持人员或支持团队必须首先查找其帐户，然后更新他们的电话号码。

您可以使用 [Microsoft Graph](manage-user-accounts-graph-api.md) (登录名) 查找用户的电话号码：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

例如：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>后续步骤

可在 GitHub 上找到电话注册和登录自定义策略初学者包 (和其他初学者包) ： [Azure 示例/active directory-active-directory-b2c-custom-policy-starterpack/方案/电话号码-无密码][starter-pack-phone] 初学者包策略文件使用多重身份验证技术配置文件和电话号码声明转换：
* [定义 Azure 多重身份验证技术配置文件](multi-factor-auth-technical-profile.md)
* [定义电话号码声明转换](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
