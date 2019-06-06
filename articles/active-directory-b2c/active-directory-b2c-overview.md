---
title: 什么是 Azure Active Directory B2C？ | Microsoft Docs
description: 了解如何使用 Azure Active Directory B2C 在应用程序中创建和管理标识体验，例如注册/登录和个人资料管理。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cceac260979b4322d41843038eab0998c8e8ba4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509747"
---
# <a name="what-is-azure-active-directory-b2c"></a>什么是 Azure Active Directory B2C？

Azure Active Directory (Azure AD) B2C 是一种企业对消费者标识管理服务。 使用此服务可以自定义和控制用户如何安全地与 Web、桌面、移动或单页应用程序交互。 用户可以使用 Azure AD B2C 注册、登录、重置密码和编辑个人资料。 Azure AD B2C 实现某种形式的 OpenID Connect 和 OAuth 2.0 协议。 这些协议的实现中的重要密钥是安全令牌及其声明，用于提供对资源的安全访问。

用户旅程是用于指定策略的请求，该策略控制用户和应用程序在与 Azure AD B2C 交互时的行为。  可通过两种途径在 Azure AD B2C 中定义用户旅程。 

如果你是具备或不具备标识专业知识的应用程序开发人员，可以选择使用 Azure 门户定义通用的标识用户流。 如果你是标识专家、系统集成商、顾问或者内部标识团队的成员，能够熟练使用 OpenID Connect 流，并且了解标识提供者和基于声明的身份验证，则可以选择基于 XML 的自定义策略。

在开始定义用户旅程之前，需要创建一个 Azure AD B2C 租户，并在该租户中注册你的应用程序和 API。 完成这些任务后，可以开始使用用户流或自定义策略定义用户旅程。 还可以选择性地添加或更改标识提供者，或者自定义用户对旅程的体验方式。

## <a name="protocols-and-tokens"></a>协议和令牌

Azure AD B2C 支持对用户旅程使用 [OpenID Connect 和 OAuth 2.0 协议](active-directory-b2c-reference-protocols.md)。 在 OpenID Connect 的 Azure AD B2C 实现中，应用程序通过向 Azure AD B2C 发出身份验证请求，来启动此用户旅程。 

向 Azure AD B2C 发出请求后会获得一个安全令牌，例如 [ID 令牌或访问令牌](active-directory-b2c-reference-tokens.md)。 此安全令牌定义用户的标识。 令牌是从 Azure AD B2C 终结点（例如 `/token` 或 `/authorize` 终结点）接收的。 通过这些令牌，可以访问用于验证标识以及允许访问安全资源的声明。

## <a name="tenants-and-applications"></a>租户和应用程序

在 Azure AD B2C 中，租户表示组织，它是用户的目录。  每个 Azure AD B2C 租户都是独特的，独立于其他 Azure AD B2C 租户。 你可能已有一个 Azure Active Directory 租户，Azure AD B2C 租户是不同的租户。 租户包含已注册使用你的应用程序的用户的信息。 例如，密码、个人资料数据和权限。 有关详细信息，请参阅[教程：创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)中的步骤创建一个。

在将应用程序配置为使用 Azure AD B2C 之前，首先需要使用 Azure 门户将它注册到租户中。 注册过程将收集一些值并将其分配给应用程序。 这些值包含用于唯一标识应用程序的应用程序 ID，以及用于将响应定向回到该应用程序的重定向 URI。

每个应用程序的交互遵循类似的高级模式：

1. 应用程序指示用户运行某个策略。
2. 用户根据策略定义完成策略。
3. 应用程序接收令牌。
4. 应用程序使用该令牌尝试访问资源。
5. 资源服务器验证令牌，确认是否可以授予访问权限。
6. 应用程序定期刷新令牌。

若要注册 Web 应用程序，请完成[教程：注册应用程序以便能够使用 Azure AD B2C 注册和登录](tutorial-register-applications.md)中的步骤。 还可以[将 Web API 应用程序添加到 Azure Active Directory B2C 租户](add-web-application.md)，或者[将本机客户端应用程序添加到 Azure Active Directory B2C 租户](add-native-application.md)。

## <a name="user-journeys"></a>用户旅程

用户旅程中的策略可以定义为[用户流](active-directory-b2c-reference-policies.md)或[自定义策略](active-directory-b2c-overview-custom.md)。 Azure 门户中提供了最常见标识任务（例如注册、登录和配置文件编辑）的预定义用户流。

用户旅程允许通过配置以下设置来控制行为：

- 用户用来注册应用程序的社交帐户
- 从用户收集的数据，例如名字或邮政编码
- 多重身份验证
- 页面的外观
- 返回给应用程序的信息

自定义策略是在 Azure AD B2C 租户中定义[标识体验框架](trustframeworkpolicy.md)行为的配置文件。 标识体验框架是建立多方信任和完成用户旅程中的步骤的底层平台。 

可以更改自定义策略以完成大量任务。 自定义策略是一个或多个采用 XML 格式的文件，这些文件在分层链中相互引用。 自定义策略可以使用[初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)来实现常见的标识任务。 

可根据需要在 Azure AD B2C 租户中使用不同类型的自定义策略或用户流，并可在不同的应用程序之间重复使用这些策略或用户流。 由于这种灵活性，只需对代码做出极少量的更改或根本不需要更改，即可定义和修改用户标识体验。 可以通过在 HTTP 身份验证请求中添加特殊的查询参数来使用策略。 若要创建自己的自定义策略，请参阅 [Azure Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)。

## <a name="identity-providers"></a>标识提供者 

在应用程序中，不妨允许用户使用不同的标识提供程序登录。 标识提供程序  创建、维护和管理标识信息，同时向应用程序提供身份验证服务。 可以使用 Azure 门户添加 Azure AD B2C 支持的标识提供程序。 

通常在应用程序中只使用一个标识提供者，但可以视需要添加更多标识提供者。 若要在 Azure AD B2C 租户中配置标识提供者，请先在标识提供者开发人员站点上创建一个应用程序，然后记录所创建的标识提供者应用程序中的应用程序标识符或客户端标识符，以及密码或客户端机密。 然后，使用此标识符和密码来配置应用程序。 

以下文章介绍了将某些常见标识提供者添加到用户流的步骤：

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft 帐户](active-directory-b2c-setup-msa-app.md)

以下文章介绍了将某些常见标识提供者添加到自定义策略的步骤：
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft 帐户](active-directory-b2c-custom-setup-msa-idp.md)

有关详细信息，请参阅[教程：将标识提供者添加到 Azure Active Directory B2C 中的应用程序](tutorial-add-identity-providers.md)。


## <a name="page-customization"></a>页面自定义

可以控制呈现给用户旅程中的客户的大部分 HTML 和 CSS 内容。 使用页面自定义可对任何自定义策略或用户流的外观进行自定义。 可以通过此自定义功能在应用程序与 Azure AD B2C 之间保持品牌和视觉一致性。 

Azure AD B2C 在用户的浏览器中运行代码，并使用称为“跨域资源共享”(CORS) 的新式方法。 首先，在包含自定义 HTML 内容的策略中指定 URL。 Azure AD B2C 将用户界面元素与从 URL 加载的 HTML 内容合并，然后向用户显示页面。

可以在查询字符串中向 Azure AD B2C 发送参数。 通过将该参数传递到 HTML 终结点，可以动态更改页面内容。 例如，可以基于从 Web 或移动应用程序传递的参数，更改注册或登录页面上的背景图像。

若要自定义用户流中的页面，请参阅[教程：在 Azure Active Directory B2C 中自定义用户界面体验](tutorial-customize-ui.md)。 若要自定义自定义策略中的页面，请参阅[在 Azure Active Directory B2C 中使用自定义策略自定义应用程序的用户界面](active-directory-b2c-ui-customization-custom.md)，或[在 Azure Active Directory B2C 中使用自定义策略配置包含动态内容的 UI](active-directory-b2c-ui-customization-custom-dynamic.md)。

## <a name="developer-resources"></a>开发人员资源

### <a name="client-applications"></a>客户端应用程序

可以选择适用于 [iOS](active-directory-b2c-devquickstarts-ios.md)、[Android](active-directory-b2c-devquickstarts-android.md) 和 .NET 等系统的应用程序。 使用 Azure AD B2C，可以在执行这些操作的同时保护用户标识。

如果你是 ASP.NET Web 应用程序开发人员，请使用[教程：使 Web 应用程序能够使用 Azure AD B2C 进行帐户身份验证](active-directory-b2c-tutorials-web-app.md)中的步骤，将应用程序设置为对帐户进行身份验证。

如果你是桌面应用程序开发人员，请使用[教程：使桌面应用程序能够使用 Azure AD B2C 进行帐户身份验证](active-directory-b2c-tutorials-desktop-app.md)中的步骤，将应用程序设置为对帐户进行身份验证。

如果你是使用 Node.js 的单页应用程序开发人员，请使用[教程：使单页应用程序能够使用 Azure AD B2C 进行帐户身份验证](active-directory-b2c-tutorials-spa.md)中的步骤，将应用程序设置为对帐户进行身份验证。

### <a name="apis"></a>API
如果客户端或 Web 应用程序需要调用 API，可以在 Azure AD B2C 中设置对这些资源的安全访问。

如果你是 ASP.NET Web 应用程序开发人员，请使用以下文章中的步骤将应用程序设置为调用受保护的 API：[教程：使用 Azure Active Directory B2C 授予对 ASP.NET Web API 的访问权限](active-directory-b2c-tutorials-web-api.md)。

如果你是桌面应用程序开发人员，请使用以下文章中的步骤将应用程序设置为调用受保护的 API：[教程：使用 Azure Active Directory B2C 从桌面应用授予对 Node.js Web API 的访问权限](active-directory-b2c-tutorials-desktop-app-webapi.md)。

如果你是使用 Node.js 的单页应用程序开发人员，请使用[教程：从单页应用程序使用 Azure Active Directory B2C 授予对 ASP.NET Core Web API 的访问权限](active-directory-b2c-tutorials-spa-webapi.md)。

### <a name="javascript"></a>JavaScript

可将自己的 JavaScript 客户端代码添加到 Azure AD B2C 中的应用程序。 若要在应用程序中设置 JavaScript，可以定义[页面合同](page-contract.md)，并在用户流或自定义策略中启用 [JavaScript](javascript-samples.md)。

### <a name="user-accounts"></a>用户帐户

许多常见的租户管理任务需要以编程方式执行。 用户管理是一个主要示例。 可能需要将现有用户存储迁移到 Azure AD B2C 租户。 可能想要在自己的页面上托管用户注册，并在 Azure AD B2C 目录后台创建用户帐户。 这些类型的任务需要能够创建、读取、更新和删除用户帐户。 可以使用 [Azure AD 图形 API](active-directory-b2c-devquickstarts-graph-dotnet.md) 来执行这些任务。

## <a name="next-steps"></a>后续步骤

请继续学习教程，开始在应用程序中配置注册和登录体验。

> [!div class="nextstepaction"]
> [教程：创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)
