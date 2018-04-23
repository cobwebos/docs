---
title: Azure Active Directory v2.0 终结点局限和限制 | Microsoft Docs
description: Azure AD v2.0 终结点的局限和限制列表。
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a36f55c57a75f671b3e5eeae3d91ff60483afd37
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="should-i-use-the-v20-endpoint"></a>我是否应使用 v2.0 终结点？
构建与 Azure Active Directory 集成的应用程序时，需确定 v2.0 终结点和身份验证协议是否满足需求。 Azure Active Directory 的原始终结点仍完全受支持，并且在某些方面比 v2.0 的功能更丰富。 但是，v2.0 终结点为开发人员[带来了极大的好处](active-directory-v2-compare.md)。

下面是目前为开发人员提供的几点建议：

* 如果必须在应用程序中支持个人 Microsoft 帐户，请使用 v2.0 终结点。 执行操作前，请确保了解本文讨论的限制。
* 如果应用程序只需支持 Microsoft 工作和学校帐户，则不要使用 v2.0 终结点。 请改为参阅我们的 [Azure AD 开发人员指南](active-directory-developers-guide.md)。

随着时间推移，v2.0 终结点会逐步移除此处列出的限制，因此，只需要使用 v2.0 终结点。 与此同时，本文旨在帮助你判断 v2.0 终结点是否适合你。 我们将持续更新本文，以反映 v2.0 终结点当前的状态。 可返回查看，重新评估对 v2.0 功能的要求。

如果现有的某个 Azure AD 应用未使用 v2.0 终结点，无需从头开始进行配置。 将来，我们会提供一种方法，将现有 Azure AD 应用程序与 v2.0 终结点配合使用。

## <a name="restrictions-on-app-types"></a>应用类型限制
v2.0 终结点目前不支持以下应用类型。 有关支持的应用类型的说明，请参阅 [Azure Active Directory v2.0 终结点的应用类型](active-directory-v2-flows.md)。

### <a name="standalone-web-apis"></a>独立 Web API
可以使用 v2.0 终结点[生成 OAuth 2.0 保护的 Web API](active-directory-v2-flows.md#web-apis)。 但是，该 Web API 只能从应用程序 ID 相同的应用程序接收令牌。 不能从应用程序 ID 不同的客户端访问 Web API。 该客户端无法请求或获取对 Web API 的权限。

若要了解如何构建从应用程序 ID 相同的客户端接受令牌的 Web API，请参阅[入门](active-directory-appmodel-v2-overview.md#getting-started)部分中的 v2.0 终结点 Web API 示例。

## <a name="restrictions-on-app-registrations"></a>应用注册限制
目前，对于每个想要与 v2.0 终结点集成的应用，必须在新的 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中创建应用注册。 现有的 Azure AD 或 Microsoft 帐户应用与 v2.0 终结点不兼容。 在应用程序注册门户以外的任何门户中注册的应用均与 v2.0 终结点不兼容。 未来，我们计划提供一种方法，将现有应用程序用作 v2.0 应用。 但是目前没有使现有应用配合 v2.0 终结点使用的迁移路径。

此外，在[应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中创建的应用注册具有以下注意事项：

* 每个应用程序 ID 只允许有两个应用密码。
* 对于用户使用个人 Microsoft 帐户注册的应用注册，只能使用一个开发人员帐户进行查看和管理。 不能在多个开发人员之间共享。  如果希望多名开发人员共享应用注册，可以通过使用 Azure AD 帐户登录注册门户来创建应用程序。
* 允许的重定向 URI 格式存在一些限制。 有关重定向 URI 的详细信息，请参阅下一节。

## <a name="restrictions-on-redirect-uris"></a>重定向 URI 的限制
在应用程序注册门户中注册的应用目前限制为一组有限的重定向 URI 值。 Web 应用和服务的重定向 URI 必须以方案 `https` 开头，并且所有重定向 URI 值必须共享一个 DNS 域。 例如，不能注册具有以下重定向 URI 的 Web 应用：

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

注册系统会将现有重定向 URI 的完整 DNS 名称与要添加的重定向 URI 的 DNS 名称相比较。 如果满足以下任一条件，添加 DNS 名称的请求会失败：  

* 新的重定向 URI 的完整 DNS 名称与现有的重定向 URI 的 DNS 名称不匹配。
* 新的重定向 URI 的完整 DNS 名称不是现有的重定向 URI 的子域。

例如，如果应用拥有以下重定向 URI：

`https://login.contoso.com`

可以向其添加，如下所示：

`https://login.contoso.com/new`

在这种情况下，DNS 名称将完全匹配。 或者，可以执行下面的操作：

`https://new.login.contoso.com`

在这种情况下，将引用 login.contoso.com 的 DNS 子域。如果希望拥有使用 login-east.contoso.com 和 login-west.contoso.com 作为重定向 URI 的应用，则必须按以下顺序添加这些重定向 URI：

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

可以添加后两个重定向 URI，因为它们是第一个重定向 URI (contoso.com) 的子域。即将发布的版本中将取消此限制。

另请注意，只能为特定应用程序设置 20 个答复 URL。

若要了解如何在应用程序注册门户中注册应用，请参阅[如何使用 v2.0 终结点注册应用](active-directory-v2-app-registration.md)。

## <a name="restrictions-on-libraries-and-sdks"></a>库和 SDK 限制
当前，对 v2.0 终结点的库支持有所限制。 如果想要在生产应用程序中使用 v2.0 终结点，可使用以下选项：

* 如果要构建 Web 应用程序，可以放心使用 Microsoft 正式版服务器端中间件来执行登录和令牌验证。 其中包括适用于 ASP.NET 的 OWIN Open ID Connect 中间件和 Node.js Passport 插件。 有关使用 Microsoft 中间件的代码示例，请参阅[入门](active-directory-appmodel-v2-overview.md#getting-started)一节。
* 如果要构建桌面或移动应用程序，可以使用一个预览版 Microsoft 身份验证库 (MSAL)。  这些库当前是支持生产的预览版，因此可在生产应用程序中放心使用。 有关预览版和可用库的术语的详细信息，请阅读[身份验证库参考](active-directory-v2-libraries.md)中的内容。
* 对于 Microsoft 库不支持的平台，可以通过直接在应用程序代码中发送和接收协议消息来与 v2.0 终结点进行集成。 v2.0 OpenID Connect 和 OAuth 协议[有明确的说明文档](active-directory-v2-protocols.md)，可帮助执行此类集成。
* 最后，可以使用开源 Open ID Connect 和 OAuth 库来与 v2.0 终结点集成。 v2.0 协议应与许多开源协议库兼容，不需要进行重大更改。 这些类型的库的可用性根据语言和平台而有所差异。 [Open ID Connect](http://openid.net/connect/) 和 [OAuth 2.0](http://oauth.net/2/) 网站将维护一份热门实现列表。 有关详细信息和经过 v2.0 终结点检验的开放源代码客户端库和示例列表，请参阅 [Azure Active Directory v2.0 身份验证库](active-directory-v2-libraries.md)。

## <a name="restrictions-on-protocols"></a>协议限制
v2.0 终结点不支持 SAML 或 WS 联合身份验证；它仅支持 Open ID Connect 和 OAuth 2.0。  并非每个 OAuth 协议的所有特性与功能都已合并到 v2.0 终结点中。 以下协议的特性和功能目前在 v2.0 终结点中不可用：

* 即使从用户获取了查看其电子邮件的权限，v2.0 终结点颁发的 ID 令牌也不包含该用户的 `email` 声明。
* 未在 v2.0 终结点上实现 OpenID Connect UserInfo 终结点。 但是，在该终结点上可能收到的所有用户配置文件数据都可从 Microsoft Graph `/me` 终结点获取。
* v2.0 终结点不支持在 ID 令牌中发布角色或组声明。
* v2.0 终结点不支持 [OAuth 2.0 资源所有者密码凭据授予](https://tools.ietf.org/html/rfc6749#section-4.3)。

此外，v2.0 终结点不支持任何形式的 SAML 或 WS 联合身份验证协议。

若要进一步了解 v2.0 终结点支持的协议功能范围，请阅读 [OpenID Connect 和 OAuth 2.0 协议参考](active-directory-v2-protocols.md)。

## <a name="restrictions-for-work-and-school-accounts"></a>工作和学校帐户限制
如果已在 Windows 应用程序中使用了 Active Directory 身份验证库 (ADAL)，则可能已利用了使用安全断言标记语言 (SAML) 断言授予的 Windows 集成身份验证。 通过此授予，联合 Azure AD 租户的用户可以使用其本地 Active Directory 实例以无提示方式进行身份验证，而无需输入凭据。 v2.0 终结点当前不支持 SAML 断言授予。
