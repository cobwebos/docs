---
title: Azure Active Directory B2C 中的内置策略 | Microsoft Docs
description: 有关 Azure Active Directory B2C 可扩展的策略框架以及如何创建各种策略类型的主题。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5c89f39b2f94309ea3d99230f5265d834c7093d9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444807"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C：内置策略


Azure Active Directory (Azure AD) B2C可扩展的策略框架是服务的核心优势。 策略充分描述了使用者标识体验，例如注册、登录或配置文件编辑。 例如，注册策略允许通过配置以下设置来控制行为：

* 使用者可以用来注册应用程序的帐户类型（社交帐户，如 Facebook；或本地帐户，如电子邮件地址）
* 在注册过程中从使用者收集的属性（例如，名字、邮政编码和鞋码）
* Azure 多重身份验证的使用
* 所有注册页面的界面外观
* 应用程序在策略运行完成时收到的信息（表现为令牌中的声明）

可以在租户中创建多个不同类型的策略，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用策略。 这种灵活性使开发人员可以定义和修改使用者标识体验，对其代码进行最小程度更改或不做更改。

可以通过简单的开发人员界面使用策略。 应用程序使用标准 HTTP 身份验证请求（在请求中传递策略参数）触发策略，并接收自定义令牌作为响应。 例如，调用注册策略的请求和调用登录策略的请求之间的唯一区别是在“p”查询字符串参数中使用的策略名称：

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>创建注册或登录策略

此策略通过单一配置处理使用者注册和登录体验。 根据上下文，使用者被引导至正确的路径（注册或登录）。 它还描述了应用程序在成功注册或登录时会接收到的令牌的内容。[此处提供了](active-directory-b2c-devquickstarts-web-dotnet-susi.md)**注册或登录**策略的代码示例。  建议优先使用此策略，而不是使用**注册策略**或**登录**策略。  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>创建注册策略

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>创建登录策略

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>创建配置文件编辑策略

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>创建密码重置策略

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>预览策略

因为我们将发布新功能，所以其中的某些功能可能在现有策略中不可用。  在这些策略供常规使用后，我们计划将较早版本替换为具有同一类型的最新版本。  你的现有策略将不会更改，为了利用这些新功能，必须创建新策略。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>如何将注册或登录策略链接到密码重置策略？
创建**注册或登录**策略时（使用本地帐户），会在体验的第一个页面上看到“忘记了密码?”链接。 单击此链接不会自动触发密码重置策略。 

而是会将错误代码 **`AADB2C90118`** 返回给应用。 应用需要通过调用特定密码重置策略来处理此错误代码。 有关详细信息，请参阅[演示策略链接方法的示例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)。

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>应使用注册或登录策略还是注册策略和登录策略？
建议优先使用**注册或登录**策略，而不是使用**注册**策略或**登录**策略。  

**注册或登录**策略具有比**登录**策略更多的功能。 它还使你可以使用页面 UI 自定义，并具有更好的本地化支持。 

如果无需对策略进行本地化，而只需将次要自定义功能用于品牌打造，并且要在其中内置密码重置，则建议使用**登录**策略。

## <a name="next-steps"></a>后续步骤
* [令牌、会话和单一登录配置](active-directory-b2c-token-session-sso.md)
* [在使用者注册期间禁用电子邮件验证](active-directory-b2c-reference-disable-ev.md)

