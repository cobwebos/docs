---
title: "Azure Active Directory B2C：自定义策略故障排除 | Microsoft Docs"
description: "有关解决自定义策略错误的几种方法的指导"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0f783062cd674edb0b6b49660f5aee69af4b3adb
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-custom-policies-and-identity-experience-framework-troubleshooting"></a>Azure Active Directory B2C 自定义策略和标识体验框架故障排除
## <a name="the-basics"></a>基础知识

使用 Azure AD B2C 自定义策略的标识开发人员在以 XML 格式使用相应策略语言配置标识体验框架时经常会遇到难题。  本指南列出了用于快速发现和解决问题的工具及相关提示。  学习如何编写自定义策略类似于学习一种新语言。  
*本文侧重于如何排查 Azure AD B2C 自定义策略配置问题，而不是排查信赖方应用程序或其标识库问题。*



## <a name="xml-editing-inproperly-formatted-xml-is-the-most-common-error"></a>最常见错误的原因是编辑 XML 时设置了不正确的 XML 格式。

能够原生显示 XML、对内容进行色彩编码、预先填充常用字词、保留 XML 元素的索引以及验证架构的卓越 XML 编辑器可以说是必不可少。  下面是我们偏好使用的两个编辑器。

* [VS Code](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

从初学者包的根文件夹获取 XML 架构定义 `TrustFrameworkPolicy_0.3.0.0.xsd`。 XML 架构验证在上传之前会识别错误。  在 XML 编辑器的文档中查找 `XML tools` 和 `XML Validation`

由于 Azure AD B2C 会拒绝它检测到的任何格式错误，因此快速检查 XML 的规则可能很有帮助。  格式不当的 XML 有时会导致出现误导性的错误消息。

## <a name="uploading-policies-and-policy-validation"></a>上传策略和策略验证

 **上传**验证是自动化的过程，大多数错误会导致上传中止。  **请记住，验证包括尝试上传的策略文件，以及该文件引用的文件链。  `RP policy file > Extensions file > Base File` 常见的验证错误包括：

错误代码片段：`... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* claimType 可能拼写错误，或者在架构中不存在。
* 必须至少在策略中的一个文件内定义 ClaimType。  例如 ` <ClaimType Id="socialIdpUserId">`
* 如果 ClaimType 在扩展文件中定义，但在基本文件中的 TechnichalProfile 内使用，则上传基本文件会导致错误。

错误代码片段：`...males a reference to a ClaimsTransformation with id...`
* 同上。

错误代码片段：`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* 检查 **<TrustFrameworkPolicy>** 中的 TenantId 以及 **<BasePolicy>** 元素是否与目标 B2C 租户匹配。  



## <a name="runtime-troubleshooting"></a>运行时故障排除

* 使用 `Run Now` 和 `https://jwt.io` 独立于 Web 或移动应用程序测试策略。 此网站的作用类似于信赖方应用程序，显示 Azure AD B2C 策略生成的 JWT 令牌的内容。  在标识体验框架中创建测试应用程序。
    * 名称：TestApp
    * Web 应用/Web API：否
    * 本机客户端：否

* 使用 [fiddler](http://www.telerik.com/fiddler) 跟踪客户端浏览器与 Azure AD B2C 之间的消息交换。  结果将会指示用户旅程在业务流程步骤中的哪个位置失败。

* 使用处于**开发模式**的 **Application Insights** 来跟踪标识体验框架 (IEF) 用户旅程的行为。  在**开发模式**下，可以观察 IEF 与 TechnicalProfiles 定义的各种声明提供程序（例如标识提供者、基于 API 的服务、Azure AD B2C 用户目录（AAD 目录），以及多重身份验证等其他服务）之间的声明交换。  

## <a name="recommended-practies"></a>建议的做法

**保留方案的多个版本，并将这些版本连同应用程序一起分组到某个项目中。** 基本、扩展和信赖方 (RP) 文件直接相互依赖，请将它们保存为组，并保留单独的工作版本，因为新功能将在策略中实现。  使用这些文件交互的应用程序代码将它们暂存在你自己的文件系统中。  应用程序可以调用租户中的不同 RP 策略，因此依赖于 Azure AD B2C 策略预期提供的声明。

**使用已知的用户旅程开发并测试技术配置文件。**  使用经过测试的初学者包策略来配置技术配置文件，并在合并到自己的用户旅程之前单独对其进行测试

**使用经过测试的技术配置文件开发并测试用户旅程**逐步更改用户旅程的业务流程步骤，以渐进方式生成所需的方案







让我们开始吧：

1. 从 GitHub 下载“active-directory-b2c-custom-policy-starterpack”。  [下载 zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 或运行
### <a name="built-in"></a>内置



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
5. 单击“标识提供者”，然后选择“本地帐户登录”。 或者，也可以选择社交标识提供者（如果已配置）。 单击 **“确定”**。
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


