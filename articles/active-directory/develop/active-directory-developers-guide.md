---
title: "Azure Active Directory 开发人员指南 | Microsoft Docs"
description: "本文提供面向开发人员的 Azure Active Directory 资源的综合性指南。"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 9d50559d2b8228dca7265f6a28bb5274abdd3164
ms.openlocfilehash: 9d0d072c8b52cd2a5a2fb585365e6cdee8ed5190


---
# <a name="azure-active-directory-developers-guide"></a>Azure Active Directory 开发人员指南
## <a name="overview"></a>概述
作为标识管理即服务 (IDMaaS) 平台，Azure Active Directory (AD) 为开发人员提供了有效的方法将标识管理功能集成到其应用程序中。 以下文章概述了 Azure AD 的实现和主要功能。 建议你按顺序阅读这些文章。如果你要深入了解，请转到[入门](#getting-started)。

1. [Azure AD 集成的优点](active-directory-how-to-integrate.md)：了解为何与 Azure AD 集成是安全登录和授权的最佳解决方案。
2. [Azure AD 身份验证方案](active-directory-authentication-scenarios.md)：利用 Azure AD 中的简化身份验证来登录应用程序。
3. [将应用程序与 Azure AD 集成](active-directory-integrating-applications.md)：了解如何从 Azure AD 添加、更新和删除应用程序，以及有关集成应用的品牌准则。
4. [Azure AD 图形 API](active-directory-graph-api.md)：使用 Azure AD 图形 API 通过 REST API 终结点以编程方式访问 Azure AD。 也可以通过 [Microsoft Graph](https://graph.microsoft.io/)访问 Azure AD 图形 API。 Microsoft Graph 具有统一的 API，可让你通过单个 REST API 终结点和单个访问令牌访问多个 Microsoft 云服务 API。
5. [Azure AD 身份验证库](active-directory-authentication-libraries.md)：使用适用于 .NET、JavaScript、Objective-C、Android 和其他语言的 Azure AD 身份验证库轻松对用户进行身份验证以获取访问令牌。

## <a name="getting-started"></a>入门
以下教程是专门针对多种平台编写的，可帮助你快速开始使用 Azure Active Directory 进行开发。 作为先决条件，你必须 [获取一个 Azure Active Directory 租户](active-directory-howto-tenant.md)。

### <a name="mobile-and-pc-application-quick-start-guides"></a>移动和电脑应用程序快速入门指南
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | [![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows Universal](active-directory-devquickstarts-windowsstore.md) |[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[直接与 OAuth 2.0 集成](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Web 应用程序快速入门指南
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) | [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[直接与 OpenID Connect 集成](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Web API 快速入门指南
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>查询目录快速入门指南
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [Graph API](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>操作方法
以下文章介绍如何使用 Azure Active Directory 执行特定任务：

* [获取 Azure AD 租户](active-directory-howto-tenant.md)
* [使用多租户应用程序模式让任何 Azure AD 用户登录](active-directory-devhowto-multi-tenant-overview.md)
* [使用证书而不是机密对应用程序标识进行身份验证](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* 使用 ADAL 在 [Android](active-directory-sso-android.md) 和 [iOS](active-directory-sso-ios.md) 设备上实现跨应用 SSO
* [使应用程序 AppSource 通过 Azure AD 认证](active-directory-devhowto-appsource-certified.md)
* [列出 Azure AD 应用程序库中的应用程序](active-directory-app-gallery-listing.md)
* [将适用于 Office 365 的 Web 应用提交到卖家仪表板](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [使用 Azure 门户将应用程序注册到 Azure Active Directory](../active-directory-app-registration.md)
* [了解 Azure Active Directory 应用程序清单](active-directory-application-manifest.md)
* [了解客户端应用程序中的登录和应用程序获取按钮的品牌准则](active-directory-branding-guidelines.md)
* [预览：如何构建可以使用个人帐户和工作或学校帐户来登录用户的应用](active-directory-appmodel-v2-overview.md)
* [预览：如何构建可以注册和登录使用者的应用](../../active-directory-b2c/active-directory-b2c-overview.md)
* [预览：使用 PowerShell 在 Azure AD 中配置令牌生存期](../active-directory-configurable-token-lifetimes.md)。 请参阅[策略操作](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)和[策略实体](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)，了解有关通过 Azure AD 图形 API 进行配置的详细信息。

## <a name="reference"></a>引用
以下文章提供了有关 REST 和身份验证库 API、协议、错误、代码示例和终结点的基础参考信息。  

### <a name="support"></a>支持
* [已标记问题](http://stackoverflow.com/questions/tagged/azure-active-directory)：通过搜索标记 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 和 [adal](http://stackoverflow.com/questions/tagged/adal) 查找有关堆栈溢出的 Azure Active Directory 解决方案。
* 有关与应用程序开发和集成相关的一些常用术语定义，请参阅 [Azure AD 开发人员词汇表](active-directory-dev-glossary.md) 。

### <a name="code"></a>代码
* [Azure Active Directory 开放源代码库](http://github.com/AzureAD)：查找库源代码的最简单方法是使用我们的[库列表](active-directory-authentication-libraries.md)。
* [Azure Active Directory 示例](https://github.com/azure-samples?query=active-directory)：浏览示例列表的最简单方法是使用[代码示例的索引](active-directory-code-samples.md)。
* [用于 .NET 的 Active Directory 身份验证库 (ADAL)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - 适用于[最新主要版本](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)和[以前主要版本](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)的参考文档。

### <a name="graph-api"></a>Graph API
* [图形 API 参考](https://msdn.microsoft.com/library/azure/hh974476.aspx)：Azure Active Directory 图形 API 的 REST 参考。 [查看交互式图形 API 参考体验](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。
* [图形 API 权限范围](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)：用于控制应用对租户中目录数据的访问权限的 OAuth 2.0 权限范围。

### <a name="authentication-and-authorization-protocols"></a>身份验证和授权协议
* [Azure AD 中的签名密钥滚动更新](active-directory-signing-key-rollover.md)：了解 Azure AD 的签名密钥滚动更新频率，以及如何为最常见的应用程序应用场景更新密钥。
* [OAuth 2.0 协议：使用授权代码的授予](active-directory-protocols-oauth-code.md)：可以使用 OAuth 2.0 协议的授权代码的授予为 Azure Active Directory 租户中的 Web 应用和 Web API 的访问授权。
* [OAuth 2.0 协议：了解隐式授予](active-directory-dev-understanding-oauth2-implicit-grant.md)：了解有关隐式授权的授予，以及它是否适合你的应用程序的详细信息。
* [OAuth 2.0 协议：使用客户端凭据执行服务对服务的调用](active-directory-protocols-oauth-service-to-service.md)：OAuth 2.0 客户端凭据的授予允许 Web 服务（机密客户端）在调用另一个 Web 服务时使用自己的凭据进行身份验证，而不是模拟用户。 在这种情况下，客户端通常是中间层 Web 服务、后台程序服务或网站。
* [OpenID Connect 1.0 协议：登录和身份验证](active-directory-protocols-openid-connect-code.md)：OpenID Connect 1.0 协议扩展了 OAuth 2.0，以将其用作身份验证协议。 客户端应用程序可以接收 id_token 来管理登录过程，或增加授权代码流，同时接收 id_token 和授权代码。
* [SAML 2.0 协议参考](active-directory-saml-protocol-reference.md)：SAML 2.0 协议使应用程序能够为其用户提供单一登录体验。
* [WS-Federation 1.2 协议](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)：Azure Active Directory 根据 Web 服务联合身份验证版本 1.2 规范支持 WS-Federation 1.2。 有关联合元数据文档的详细信息，请参阅 [联合元数据](active-directory-federation-metadata.md)。
* [支持的令牌和声明类型](active-directory-token-and-claims.md)：可以通过本指南来了解和评估 SAML 2.0 令牌与 JSON Web 令牌 (JWT) 中的声明。

## <a name="videos"></a>视频
### <a name="build"></a>构建
这些概述演示文稿是关于使用本身就在工程团队中工作的 Azure Active Directory 功能发言人来开发应用。 演示文稿涵盖了各重要主题，包括 IDMaaS、身份验证、联合身份验证以及单一登录。

* [Microsoft Identity: State of the Union and Future Direction](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: Identity management as a service for modern applications](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Develop modern web applications with Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Develop modern native applications with Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) 是一个每周五播放的 1 对 1 视频系列，专门就各种 Azure 主题对专家进行简短的采访（10-15 分钟）。  使用页面上的服务筛选功能查看所有 Azure Active Directory 视频。

* [Azure 标识 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Azure 标识 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Azure 标识 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>社交
* [Active Directory 团队博客](http://blogs.technet.com/b/ad/)：Azure Active Directory 领域的最新开发情况。
* [Azure Active Directory Graph 团队博客](https://blogs.msdn.microsoft.com/aadgraphteam)：特定于图形 API 的 Azure Active Directory 信息。
* [云标识](http://www.cloudidentity.net)：从 Azure Active Directory PM 原理的角度讲解标识管理即服务的设计理念。  
* [Twitter 上的 Azure Active Directory](https://twitter.com/azuread)：以 140 个或更少的字符发布的 Azure Active Directory 公告。

## <a name="windows-server-on-premises-development"></a>Windows Server 本地开发
有关使用 Windows Server 和 Active Directory 联合身份验证服务 (ADFS) 开发的指南，请参阅：

* [适用于开发人员的 AD FS 方案](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers)：提供 AD FS 组件及其工作原理的概述，并提供有关受支持的身份验证/授权方案的详细信息。
* [AD FS 演练](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development)：演练文章的列表，提供有关实现相关身份验证/授权流程的分步说明。



<!--HONumber=Jan17_HO3-->


