---
title: "Azure Active Directory B2C：可扩展的策略框架 | Microsoft Docs"
description: "有关 Azure Active Directory B2C 可扩展的策略框架以及如何创建各种策略类型的主题"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 33521eca953c0e14a7fdaa99edef2997397286eb
ms.openlocfilehash: cd79a485feddc68924f9b7f5e7d8d614b4e28d28


---
# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active Directory B2C：可扩展的策略框架
## <a name="the-basics"></a>基础知识
Azure Active Directory (Azure AD) B2C可扩展的策略框架是服务的核心优势。 策略充分描述了使用者标识体验，例如注册、登录或配置文件编辑。 例如，注册策略允许你通过配置以下设置来控制行为：

* 使用者可以用来注册应用程序的帐户类型（社交帐户，如 Facebook；或本地帐户，如电子邮件地址）。
* 在注册过程中从使用者收集的属性（例如，名字、邮政编码和鞋码）。
* 使用多重身份验证。
* 所有注册页面的界面外观。
* 应用程序在策略运行完成时收到的信息（表现为令牌中的声明）。

你可以在租户中创建多个不同类型的策略，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用策略。 这允许开发人员定义和修改使用者标识体验，对其代码进行最小程度更改或不做更改。

可以通过简单的开发人员界面使用策略。 你的应用程序使用标准 HTTP 身份验证请求（在请求中传递策略参数）触发策略，并接收自定义令牌作为响应。 例如，调用注册策略的请求和调用登录策略的请求之间的唯一区别是在“p”查询字符串参数中使用的策略名称：

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

有关策略框架的详细信息，请参阅[博客文章](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)。

## <a name="create-a-sign-up-policy"></a>创建注册策略
要在应用程序上启用注册，你需要创建注册策略。 此策略描述了使用者在注册过程中将要体验的内容以及应用程序在成功注册时会接收到的令牌内容。

1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“注册策略”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. “名称”确定应用程序使用的注册策略名称。 例如，输入“SiUp”。
5. 单击“标识提供者”，然后选择“电子邮件注册”。 或者，也可以选择社交标识提供者（如果已配置）。 单击 **“确定”**。
6. 单击“注册属性”。 在这里，你可以选择要在注册期间从使用者收集的属性。 例如，选择“国家/地区”、“显示名称”和“邮政编码”。 单击 **“确定”**。
7. 单击“应用程序声明”。 在这里，可以选择希望在成功注册体验后发回到应用程序的令牌中返回的声明。 例如，选择“显示名称”、“标识提供者”、“邮政编码”、“用户是新用户”和“用户的对象 ID”。
8. 单击“创建” 。 请注意，刚刚创建的策略在“注册策略”边栏选项卡中显示为“**B2C_1_SiUp**”（自动添加 **B2C\_1\_**片段）。
9. 通过单击“**B2C_1_SiUp**”打开策略。
10. 在“应用程序”下拉列表中选择“Contoso B2C 应用”，然后在“回复 URL/重定向 URI”下拉列表中选择 `https://localhost:44321/`。
11. 单击“立即运行”。 将打开一个新的浏览器选项卡，你可以运行注册应用程序的使用者体验。
    
    > [!NOTE]
    > 策略创建和更新最多需要一分钟才能生效。
    > 
    > 

## <a name="create-a-sign-in-policy"></a>创建登录策略
要在应用程序上启用登录，你需要创建登录策略。 此策略描述了使用者在登录过程中将要体验的内容以及应用程序在成功登录时会接收到的令牌内容。

1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“登录策略”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. “名称”确定应用程序使用的登录策略名称。 例如，输入“SiIn”。
5. 单击“标识提供者”，然后选择“本地帐户登录”。 或者，也可以选择社交标识提供者（如果已配置）。 单击 **“确定”**。
6. 单击“应用程序声明”。 在这里，可以选择希望在成功登录体验后发回到应用程序的令牌中返回的声明。 例如，选择“显示名称”、“标识提供者”、“邮政编码”和“用户的对象 ID”。 单击 **“确定”**。
7. 单击“创建” 。 请注意，刚刚创建的策略在“登录策略”边栏选项卡中显示为“**B2C_1_SiIn**”（自动添加 **B2C\_1\_**片段）。
8. 通过单击“**B2C_1_SiIn**”打开策略。
9. 在“应用程序”下拉列表中选择“Contoso B2C 应用”，然后在“回复 URL/重定向 URI”下拉列表中选择 `https://localhost:44321/`。
10. 单击“立即运行”。 将打开一个新的浏览器选项卡，你可以运行登录到应用程序的使用者体验。
    
    > [!NOTE]
    > 策略创建和更新最多需要一分钟才能生效。
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>创建注册或登录策略
此策略通过单一配置处理使用者注册和登录体验。 根据上下文，使用者被引导至正确的路径（注册或登录）。 它还描述了应用程序在成功注册或登录时会接收到的令牌的内容。  注册或登录策略的代码示例[在此处可用](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。

1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“注册或登录策略”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. “名称”确定应用程序使用的注册策略名称。 例如，输入“SiUpIn”。
5. 单击“标识提供者”，然后选择“电子邮件注册”。 或者，也可以选择社交标识提供者（如果已配置）。 单击 **“确定”**。
6. 单击“注册属性”。 在这里，你可以选择要在注册期间从使用者收集的属性。 例如，选择“国家/地区”、“显示名称”和“邮政编码”。 单击 **“确定”**。
7. 单击“应用程序声明”。 在这里，可以选择希望在成功注册或登录体验后发回到应用程序的令牌中返回的声明。 例如，选择“显示名称”、“标识提供者”、“邮政编码”、“用户是新用户”和“用户的对象 ID”。
8. 单击“创建” 。 请注意，刚刚创建的策略在“注册或登录策略”边栏选项卡中显示为“**B2C_1_SiUpIn**”（自动添加 **B2C\_1\_**片段）。
9. 通过单击“**B2C_1_SiUpIn**”打开策略。
10. 在“应用程序”下拉列表中选择“Contoso B2C 应用”，然后在“回复 URL/重定向 URI”下拉列表中选择 `https://localhost:44321/`。
11. 单击“立即运行”。 将打开一个新的浏览器标签，你可以按照配置运行注册或登录使用者体验。
    
    > [!NOTE]
    > 策略创建和更新最多需要一分钟才能生效。
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>创建配置文件编辑策略
若要在应用程序上启用配置文件编辑，你需要创建配置文件编辑策略。 此策略描述了使用者在配置文件编辑过程中将要体验的内容以及应用程序在成功完成时会接收到的令牌内容。

1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“配置文件编辑策略”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. “名称”确定应用程序使用的配置文件编辑策略名称。 例如，输入“SiPe”。
5. 单击“标识提供者”，然后选择“电子邮件地址”。 或者，也可以选择社交标识提供者（如果已配置）。 单击 **“确定”**。
6. 单击“配置文件属性”。 在这里，可以选择使用者可以查看和编辑的属性。 例如，选择“国家/地区”、“显示名称”和“邮政编码”。 单击 **“确定”**。
7. 单击“应用程序声明”。 在这里，可以选择希望在成功配置文件编辑体验后发回到应用程序的令牌中返回的声明。 例如，选择“显示名称”和“邮政编码”。
8. 单击“创建” 。 请注意，刚刚创建的策略在“配置文件编辑策略”边栏选项卡中显示为“**B2C_1_SiPe**”（自动添加 **B2C\_1\_**片段）。
9. 通过单击“**B2C_1_SiPe**”打开策略。
10. 在“应用程序”下拉列表中选择“Contoso B2C 应用”，然后在“回复 URL/重定向 URI”下拉列表中选择 `https://localhost:44321/`。
11. 单击“立即运行”。 将打开一个新的浏览器选项卡，你可以在应用程序中运行通过配置文件编辑使用者体验。
    
    > [!NOTE]
    > 策略创建和更新最多需要一分钟才能生效。
    > 
    > 

## <a name="create-a-password-reset-policy"></a>创建密码重置策略
若要在应用程序上启用细化密码重置，你需要创建密码重置策略。 请注意，[此处](active-directory-b2c-reference-sspr.md)指定的租户密码重置选项仍适用于登录策略。 此策略描述了使用者在密码重置过程中将要体验的内容以及应用程序在成功完成时会接收到的令牌内容。

1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“密码重置策略”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. “名称”确定应用程序使用的密码重置策略名称。 例如，输入“SSPR”。
5. 单击“标识提供者”，然后选择“使用电子邮件地址重置密码”。 单击 **“确定”**。
6. 单击“应用程序声明”。 在这里，可以选择希望在成功密码重置体验后发回到应用程序的令牌中返回的声明。 例如，选择“用户的对象 ID”。
7. 单击“创建” 。 请注意，刚刚创建的策略在“密码重置策略”边栏选项卡中显示为“**B2C_1_SSPR**”（自动添加 **B2C\_1\_**片段）。
8. 通过单击“**B2C_1_SSPR**”打开策略。
9. 在“应用程序”下拉列表中选择“Contoso B2C 应用”，然后在“回复 URL/重定向 URI”下拉列表中选择 `https://localhost:44321/`。
10. 单击“立即运行”。 将打开一个新的浏览器选项卡，你可以在应用程序中运行通过密码重置使用者体验。
    
    > [!NOTE]
    > 策略创建和更新最多需要一分钟才能生效。
    > 
    > 

## <a name="how-to-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>如何将注册或登录策略链接到密码重置策略？
当你创建注册或登录策略时（使用本地帐户），使用者将看到“忘记了密码”？ 体验的第一页中的链接。 单击此链接不会自动触发密码重置策略， 而是向应用返回特定的错误代码 `AADB2C90118`。 应用需要处理此错误并调用特定的密码重置策略。 [此处](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)提供了演示这种策略链接方法的示例。

## <a name="additional-resources"></a>其他资源
* [令牌、会话和单一登录配置](active-directory-b2c-token-session-sso.md)。
* [在使用者注册期间禁用电子邮件验证](active-directory-b2c-reference-disable-ev.md)




<!--HONumber=Feb17_HO1-->


