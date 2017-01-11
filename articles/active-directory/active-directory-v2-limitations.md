---
title: "Azure Active Directory v2.0 终结点局限和限制 | Microsoft Docs"
description: "Azure AD v2.0 终结点的局限和限制列表。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8fabc37ad8c7a99a09083422f740e5352e403438


---
# <a name="should-i-use-the-v20-endpoint"></a>我是否应使用 v2.0 终结点？
构建与 Azure Active Directory (Azure AD) 集成的应用程序时，需确定 v2.0 终结点和身份验证协议是否满足需求。 原始 Azure AD 终结点仍完全受到支持，并且在某些方面比 v2.0 的功能更丰富。 但是，v2.0 终结点为开发人员[带来了极大的好处](active-directory-v2-compare.md)。 这些好处可能会吸引你使用新的编程模型。

下面是我们对现在使用 v2.0 终结点的建议：

* 如果要在应用程序中支持个人 Microsoft 帐户，请使用 v2.0 终结点。 执行操作前，请确保了解本文讨论的限制，尤其是那些适用于工作和学校帐户的限制。
* 如果应用程序仅需要支持工作和学校帐户，请使用[原始 Azure AD 终结点](active-directory-developers-guide.md)。

随着时间推移，v2.0 终结点将会逐步移除此处列出的限制，因此你只需要使用 v2.0 终结点。 在此同时，本文旨在帮助你判断 v2.0 终结点是否对你适用。 我们将持续更新本文，以反映 v2.0 终结点当前的状态。 可返回查看，重新评估对 v2.0 功能的要求。

如果现有的某个 Azure AD 应用未使用 v2.0 终结点，无需从头开始进行配置。 将来，我们会提供一种方法，将现有 Azure AD 应用程序与 v2.0 终结点配合使用。

## <a name="restrictions-on-app-types"></a>应用类型限制
v2.0 终结点目前不支持以下应用类型。 有关支持的应用类型的说明，请参阅 [Azure Active Directory v2.0 终结点的应用类型](active-directory-v2-flows.md)。

### <a name="standalone-web-apis"></a>独立 Web API
可以使用 v2.0 终结点[生成 OAuth 2.0 保护的 Web API](active-directory-v2-flows.md#web-apis)。 但是，该 Web API 只能从应用程序 ID 相同的应用程序接收令牌。 不能从应用程序 ID 不同的客户端访问 Web API。 该客户端无法请求或获取对 Web API 的权限。

若要了解如何构建从应用程序 ID 相同的客户端接受令牌的 Web API，请参阅[入门](active-directory-appmodel-v2-overview.md#getting-started)部分中的 v2.0 终结点 Web API 示例。

### <a name="web-api-on-behalf-of-flow"></a>Web API 代理流
许多体系结构包含需要调用另一个下游 Web API 的 Web API，这两者都受 v2.0 终结点的保护。 此情况常见于具有 Web API 后端的本机客户端，该后端反过来调用 Microsoft Online Services 的实例或另一个支持 Azure AD 的自定义构建的 Web API。

可以使用 OAuth 2.0 JSON Web 令牌 (JWT) 持有者凭据授权（也称为“代理流”）创建此方案。 但是，v2.0 终结点目前不支持代理流。 若要查看此流在正式版 Azure AD 服务中的工作原理，请参阅 [GitHub 上的代理代码示例](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)。

## <a name="restrictions-on-app-registrations"></a>应用注册限制
目前，对于每个想要与 v2.0 终结点集成的应用，必须在新的 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中创建应用注册。 现有的 Azure AD 或 Microsoft 帐户应用与 v2.0 终结点不兼容。 在应用程序注册门户以外的任何门户中注册的应用均与 v2.0 终结点不兼容。 未来，我们计划提供一种方法，将现有应用程序用作 v2.0 应用。 但是目前没有使现有应用配合 v2.0 终结点使用的迁移路径。

在应用注册门户中注册的应用将无法配合原有 Azure AD 身份验证终结点使用。 但是，可以使用在应用程序注册门户中创建的应用，以成功与 Microsoft 帐户身份验证终结点 `https://login.live.com` 集成。

此外，在[应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中创建的应用注册具有以下注意事项：

* 不支持**主页**属性（也称为*登录 URL*）。 在没有主页的情况下，这些应用程序不会在 Office MyApps 面板中出现。
* 当前每个应用程序 ID 只允许有两个应用密码。
* 应用注册只能由一个开发人员帐户查看和管理。 不能在多个开发人员之间共享。
* 允许的重定向 URI 格式存在一些限制。 有关重定向 URI 的详细信息，请参阅下一节。

## <a name="restrictions-on-redirect-uris"></a>重定向 URI 的限制
在应用程序注册门户中注册的应用目前限制为一组有限的重定向 URI 值。 Web 应用和服务的重定向 URI 必须以方案 `https` 开头，并且所有重定向 URI 值必须共享一个 DNS 域。 例如，无法注册具有以下定向 URI 的 Web 应用：

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

注册系统会将现有重定向 URI 的完整 DNS 名称与要添加的重定向 URI 的 DNS 名称相比较。 如果满足以下任一条件，添加 DNS 名称的请求将失败：  

* 新的重定向 URI 的完整 DNS 名称与现有的重定向 URI 的 DNS 名称不匹配。
* 新的重定向 URI 的完整 DNS 名称不是现有的重定向 URI 的子域。

例如，如果应用拥有以下重定向 URI：

`https://login.contoso.com`

你可以向其添加，如下所示：

`https://login.contoso.com/new`

在这种情况下，DNS 名称将完全匹配。 或者，你可以执行下面的操作：

`https://new.login.contoso.com`

在这种情况下，你将引用 login.contoso.com 的 DNS 子域。 如果希望拥有使用 login-east.contoso.com 和 login-west.contoso.com 作为重定向 URI 的应用，则必须按以下顺序添加这些重定向 URI：

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

可以添加后两个重定向 URI，因为它们是第一个重定向 URI (contoso.com) 的子域。 即将发布的版本中将取消此限制。

若要了解如何在应用程序注册门户中注册应用，请参阅[如何使用 v2.0 终结点注册应用](active-directory-v2-app-registration.md)。

## <a name="restrictions-on-services-and-apis"></a>服务和 API 限制
v2.0 终结点目前支持登录所有已在应用程序注册门户中注册的应用，并且该应用已在[支持的身份验证流](active-directory-v2-flows.md)列表中列出。 但这些应用可获取 OAuth 2.0 访问令牌来访问非常有限的资源集。 V2.0 终结点仅对以下应用颁发访问令牌：

* 请求令牌的应用。 如果逻辑应用包含多个不同的组件或层，则应用可为自身获取访问令牌。 若要查看此方案的工作方式，请参阅[入门](active-directory-appmodel-v2-overview.md#getting-started)教程。
* Outlook 邮件、日历和联系人 REST API，全都位于 https://outlook.office.com。 若要了解如何编写访问这些 API 的应用，请参阅 [Office 入门](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)教程。
* Microsoft Graph API。 可以详细了解 [Microsoft Graph](https://graph.microsoft.io) 以及可用的数据。

目前不支持其他服务。 将来会添加更多的 Microsoft Online Services，并支持自定义构建的 Web API 和服务。

## <a name="restrictions-on-libraries-and-sdks"></a>库和 SDK 限制
当前，对 v2.0 终结点的库支持有所限制。 如果想要在生产应用程序中使用 v2.0 终结点，可使用以下选项：

* 如果要构建 Web 应用程序，可以放心使用 Microsoft 正式版服务器端中间件来执行登录和令牌验证。 其中包括适用于 ASP.NET 的 OWIN Open ID Connect 中间件和 Node.js Passport 插件。 有关使用 Microsoft 中间件的代码示例，请参阅[入门](active-directory-appmodel-v2-overview.md#getting-started)一节。
* 对于其他平台以及本机与移动应用程序，可以通过直接在应用程序代码中发送和接收协议消息来与 v2.0 终结点进行集成。 v2.0 OpenID Connect 和 OAuth 协议[有明确的说明文档](active-directory-v2-protocols.md)，可帮助执行此类集成。
* 最后，可以使用开源 Open ID Connect 和 OAuth 库来与 v2.0 终结点集成。 v2.0 协议应与许多开源协议库兼容，不需要进行重大更改。 这些类型的库的可用性根据语言和平台而有所差异。 [Open ID Connect](http://openid.net/connect/) 和 [OAuth 2.0](http://oauth.net/2/) 网站将维护一份热门实现列表。 有关详细信息和经过 v2.0 终结点检验的开放源代码客户端库和示例列表，请参阅 [Azure Active Directory v2.0 身份验证库](active-directory-v2-libraries.md)。

我们发布了仅用于 .NET 的 [Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 的初始预览版。 欢迎在 .NET 客户端和服务器应用程序中试用此库，但作为预览库，它不具备正式版 (GA) 质量支持。

## <a name="restrictions-on-protocols"></a>协议限制
v2.0 终结点仅支持 Open ID Connect 和 OAuth 2.0。 但是，并非每个协议的所有特性与功能都已合并到 v2.0 终结点中。

这些典型协议的特性和功能目前在 v2.0 终结点中不可用：

* v2.0 终结点不提供允许应用结束用户会话的 OpenID Connect `end_session_endpoint` 参数。
* v2.0 终结点签发的 ID 令牌只包含用户的成对标识符。 这意味着两个不同的应用程序针对同一用户将收到不同 ID。 请注意，通过查询 Microsoft Graph `/me` 终结点，能够获得用户可跨应用程序使用的相关 ID。
* 即使从用户获取了查看其电子邮件的权限，v2.0 终结点颁发的 ID 令牌也不包含该用户的 `email` 声明。
* 未在 v2.0 终结点上实现 OpenID Connect UserInfo 终结点。 但是，在该终结点上可能收到的所有用户配置文件数据都可从 Microsoft Graph `/me` 终结点获取。
* v2.0 终结点不支持在 ID 令牌中发布角色或组声明。

若要进一步了解 v2.0 终结点支持的协议功能范围，请阅读 [OpenID Connect 和 OAuth 2.0 协议参考](active-directory-v2-protocols.md)。

## <a name="restrictions-for-work-and-school-accounts"></a>工作和学校帐户限制
v2.0 终结点尚不支持某些特定于 Microsoft 企业用户的功能。 有关详细信息，请阅读后面各节。

### <a name="device-based-conditional-access-native-and-mobile-apps-and-microsoft-graph"></a>基于设备的条件访问、本机和移动应用，以及 Microsoft Graph
v2.0 终结点尚不支持针对移动和本机应用程序的设备身份验证，如在 iOS 或 Android 上运行的本机应用。 对某些组织而言，这可能会阻止本机应用程序调用 Microsoft Graph。 如果管理员在应用程序上设置了基于设备的条件性访问策略，则需要设备身份验证。 对于 v2.0 终结点，基于设备的条件访问最可能的情形是管理员要对 Microsoft Graph，例如 Outlook API 中的一个资源设置策略。 如果管理员设置了此策略，并且本机应用程序向 Microsoft Graph 请求令牌，则请求最终将失败，因为尚不支持设备身份验证。 但是，如果配置了基于设备的策略，则支持向 Microsoft Graph 请求令牌的 Web 应用程序。 在 Web 应用场景中，通过用户的 Web 浏览器来执行设备身份验证。

作为开发人员，很可能无法控制何时对 Microsoft Graph 资源设置策略。 甚至可能不知道是何时进行的设置。 如果要为工作和学校用户生成应用程序，那么在 v2.0 终结点支持设备身份验证之前，都应使用[原始 Azure AD 终结点](active-directory-developers-guide.md)。 可以了解更多 [Azure AD 中基于设备的条件访问](active-directory-conditional-access.md#device-based-conditional-access)的相关信息。

### <a name="windows-integrated-authentication-for-federated-tenants"></a>针对联合租户的 Windows 集成身份验证
如果已在 Windows 应用程序中使用 Active Directory 身份验证库 (ADAL)（通过原始 Azure AD 终结点），则可能已利用所谓的安全断言标记语言 (SAML) 断言授予。 通过此授予，联合 Azure AD 租户的用户可以使用其本地 Active Directory 实例以无提示方式进行身份验证，而无需输入凭据。 v2.0 终结点当前不支持 SAML 断言授予。




<!--HONumber=Nov16_HO3-->


