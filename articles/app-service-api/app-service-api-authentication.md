---
title: "Azure App Service 中 API 应用的身份验证和授权 | Microsoft Docs"
description: "了解 Azure 应用服务为 API 应用提供的身份验证和授权服务。"
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: d620b53a-5a6f-41c9-84c7-f7ef5ff02ae7
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c644f67c985ef1de13286bc1ef8c2bb84a2939e3


---
# <a name="authentication-and-authorization-for-api-apps-in-azure-app-service"></a>Azure 应用服务中 API 应用的身份验证和授权
## <a name="overview"></a>概述
> [!NOTE]
> 本主题将移入[应用服务身份验证/授权](../app-service/app-service-authentication-overview.md)合并主题，其中包括 Web 应用、移动应用和 API 应用。
> 
> 

Azure App Service 提供内置的身份验证与授权服务，可实现 [OAuth 2.0](#oauth) 和 [OpenID Connect](#oauth)。 本文介绍 Azure 应用服务中 API 应用可用的服务和选项。

下图演示了应用服务身份验证的几个重要特征：

* 预处理传入的 API 请求，这意味着，可以处理应用服务支持的任何语言或框架。
* 提供多个选项用于确定要在自身代码中完成多少身份验证工作。
* 适用于最终用户与服务帐户身份验证。 
* 支持五个标识提供者：Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帐户。
* 对 API 应用、Web 应用和移动应用的工作原理相同。

![](./media/app-service-api-authentication/api-apps-overview.png)

## <a name="language-agnostic"></a>不限语言
应用服务身份验证处理在请求进入 API 应用之前进行，这意味着，无论 API 应用是以哪种语言或框架编写，都可以使用身份验证功能。  API 可以基于 ASP.NET、Java、Node.js，或应用服务支持的任何框架。

应用服务在 HTTP 请求的 Authorization 标头中传递 JSON Web 令牌 (JWT)，以任何语言或框架编写的代码都可以从该令牌获取所需的信息。 此外，应用服务通过设置一些特殊标头（如下所示）来方便访问最常用的声明：

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

在 .NET API 中，可以使用 `Authorize` 属性，如果想要更精细的授权，可以基于声明轻松编写代码，因为 .NET 类中已经填充了声明信息。

## <a name="multiple-protection-options"></a>多个保护选项
应用服务可以防止匿名 HTTP 请求进入 API 应用、传递所有请求和验证请求中包含的令牌，或者不采取任何措施即放行所有请求：

1. 只允许经过身份验证的请求进入 API 应用。
   
    如果收到来自浏览器的匿名请求，应用服务会重定向到所选身份验证提供程序（Azure AD、Google、Twitter 等）的登录页。 
   
    使用此选项时，不需要在应用中编写任何身份验证代码。由于 HTTP 标头中已提供最重要的声明，因此授权代码变得相当简单。
2. 允许所有请求进入 API 应用，但会验证经过身份验证的请求，并在 HTTP 标头中传递身份验证信息。
   
    使用此选项可以更灵活地处理匿名请求，但如果想要防止匿名用户使用 API，则必须编写代码。 由于最常用的声明在 HTTP 请求的标头中传递，因此授权代码相对简单。
3. 允许所有请求进入 API，不处理请求中的身份验证信息。
   
    此选项将身份验证和授权任务全部交由应用程序代码来处理。

在 [Azure 门户](https://portal.azure.com/)上的“身份验证/授权”边栏选项卡中选择所需的选项。

![](./media/app-service-api-authentication/authblade.png)

如果选择选项 1 和 2，请打开“应用服务身份验证”，然后在“请求未经身份验证时需执行的操作”下拉列表中，选择“登录”或“允许请求(无操作)”。  如果选择“登录”，则必须选择身份验证提供程序并配置该提供程序。

![](./media/app-service-api-authentication/actiontotake.png)

有关如何配置身份验证的详细信息，请参阅[如何配置应用服务应用程序以使用 Azure Active Directory 登录](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。 此文章适用于 API 应用和移动应用，并链接到有关其他身份验证提供程序的其他文章。

## <a name="a-idinternala-service-account-authentication"></a><a id="internal"></a>服务帐户身份验证
应用服务身份验证适用于从某个 API 应用调用另一个 API 应用之类的内部方案。 在此方案中，可以使用服务帐户凭据（而不是用户凭据）来获取令牌。 在 Azure Active Directory 中，服务帐户也称为*服务主体*，使用此类帐户的身份验证也称为服务到服务方案。 

对于服务到服务方案，请使用 Azure Active Directory 保护所调用的 API 应用，并在调用 API 应用时提供 AAD 服务主体授权令牌。 通过提供客户端 ID 和客户端机密，可以从 AAD 应用程序获取令牌。 不需要像过去处理移动服务 Zumo 令牌时那样使用仅限 Azure 的特殊代码。 [API 应用的服务主体身份验证](app-service-api-dotnet-service-principal-auth.md)教程讲解了这种使用 ASP.NET API 应用的方案示例。

若要处理服务到服务方案但不使用应用服务身份验证，请使用客户端证书或基本身份验证。 有关 Azure 中客户端证书的信息，请参阅[如何为 Web 应用配置 TLS 相互身份验证](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。 有关 ASP.NET 中基本身份验证的信息，请参阅 [ASP.NET Web API 2 中的身份验证筛选器](http://www.asp.net/web-api/overview/security/authentication-filters)。

从应用服务逻辑应用到 API 应用的服务帐户身份验证是一个特殊情况，该情况在[在逻辑应用中使用在应用服务中托管的自定义 API](../app-service-logic/app-service-logic-custom-hosted-api.md) 中进行了介绍。

## <a name="mobile-client-authentication"></a>移动客户端身份验证
有关如何处理移动客户端的身份验证的信息，请参阅[有关移动应用的身份验证的文档](../app-service-mobile/app-service-mobile-ios-get-started-users.md)。 移动应用和 API 应用的应用服务身份验证运用相同的工作原理。

## <a name="more-information"></a>详细信息
有关 Azure 应用服务中的身份验证和授权的详细信息，请参阅以下资源：

* [扩展应用服务身份验证/授权](/blog/announcing-app-service-authentication-authorization/)
* [如何配置应用服务应用程序以使用 Azure Active Directory 登录](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)（页面顶部提供了其他身份验证提供程序的链接） 

有关 OAuth 2.0、OpenID Connect 和 JSON Web 令牌 (JWT) 的详细信息，请参阅以下资源。

* [OAuth 2.0 入门](http://shop.oreilly.com/product/0636920021810.do "OAuth 2.0 入门") 
* [Introduction to OAuth2, OpenID Connect and JSON Web Tokens (JWT) - PluralSight Course](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction)（OAuth2、OpenID Connect 和 JSON Web 令牌 (JWT) 简介 — PluralSight 课程） 
* [Building and Securing a RESTful API for Multiple Clients in ASP.NET - PluralSight course](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)（在 ASP.NET 中生成和保护多个客户端的 RESTful API - PluralSight 课程）

有关 Azure Active Directory 的详细信息，请参阅以下资源。

* [Azure AD scenarios](http://aka.ms/aadscenarios)（Azure AD 应用场景）
* [Azure AD developers' guide](http://aka.ms/aaddev)（Azure AD 开发人员指南）
* [Azure AD samples](http://aka.ms/aadsamples)（Azure AD 示例）

## <a name="next-steps"></a>后续步骤
本文说明了可用于 API 应用的应用服务身份验证和授权功能。 入门系列的下一个教程说明如何[在应用服务 API 应用中实现用户身份验证](app-service-api-dotnet-user-principal-auth.md)。




<!--HONumber=Nov16_HO3-->


