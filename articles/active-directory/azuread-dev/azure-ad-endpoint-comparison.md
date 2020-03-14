---
title: 为什么要更新到 Microsoft 标识平台（v2.0） |Microsoft
description: 了解 Microsoft 标识平台（v2.0）终结点与 Azure Active Directory （Azure AD） v2.0 终结点之间的差异，并了解更新到 v2.0 的好处。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.openlocfilehash: d910a76397a40a14d960b59f60561cbee470f5c4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263681"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>为什么要更新到 Microsoft 标识平台 (v2.0)？

开发新应用程序时，了解 Microsoft 标识平台（v2.0）与 Azure Active Directory （1.0）终结点之间的差异非常重要。 本文介绍了端点与 Microsoft 标识平台的一些现有限制之间的主要区别。

> [!NOTE]
> Microsoft 标识平台终结点并不支持所有 Azure AD 方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读[microsoft 标识平台限制](#limitations)。

## <a name="who-can-sign-in"></a>谁可以登录

![谁可以使用 v1.0 和 v2.0 终结点登录](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* v1.0 终结点仅允许使用工作和学校帐户登录到应用程序 (Azure AD)
* Microsoft 标识平台终结点允许 Azure AD 和个人 Microsoft 帐户（MSA）（如 hotmail.com、outlook.com 和 msn.com）的工作和学校帐户登录。
* 对于配置为 *[单租户](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* 的应用程序或配置为指向特定于租户的终结点（`https://login.microsoftonline.com/{TenantId_or_Name}`）的*多租户*应用程序，这两个终结点还接受 Azure AD 目录的 *[来宾用户](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* 的登录。

Microsoft 标识平台终结点允许你编写应用，这些应用接受来自 Microsoft 个人帐户和工作和学校帐户的登录。 这样，你便可以编写完全不区分帐户的应用。 例如，如果应用调用 [Microsoft Graph](https://graph.microsoft.io)，则工作帐户可以使用某些附加功能和数据，如 SharePoint 站点或目录数据。 但对于许多操作（例如[读取用户的邮件](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0)），相同的代码可以访问个人帐户以及工作和学校帐户的电子邮件。

对于 Microsoft 标识平台终结点，你可以使用 Microsoft 身份验证库（MSAL）来获取对消费者、教育和企业领域的访问权限。 Azure AD v1.0 终结点仅接受工作和学校帐户的登录。

## <a name="incremental-and-dynamic-consent"></a>增量同意和动态同意

使用 Azure AD v1.0 终结点的应用需要提前指定所需的 OAuth 2.0 权限，例如：

![显示权限注册 UI 的示例](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

直接在应用程序注册中设置的权限是**静态的**。 尽管在 Azure 门户中定义应用的静态权限能保持代码的简洁性，但可能会给开发人员带来几个问题：

* 应用需要在用户首次登录时请求可能需要的权限。 这可能会导致冗长的权限列表，而让最终用户在初始登录时打消审批应用程序访问权限的念头。

* 应用需要事先知道可能访问的所有资源。 很难创建能够访问任意数目的资源的应用程序。

使用 Microsoft 标识平台终结点，你可以忽略 Azure 门户中的应用注册信息中定义的静态权限，而改为以增量方式请求权限，这意味着提前要求最小的权限集随着时间的推移越来越多，客户会使用其他应用功能。 为此，可以在请求访问令牌时，通过在 `scope` 参数中包含新的范围指定应用所需的范围 - 无需在应用程序注册信息中预定义这些范围。 如果用户尚未许可添加到请求的新范围，则系统会提示他们仅许可新的权限。 有关详细信息，请参阅[权限、许可和范围](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

允许应用通过 `scope` 参数动态请求权限可让开发人员完全控制用户的体验。 还可以将许可体验提前，并在一个初始授权请求中请求所有的权限。 如果应用需要大量的权限，则你可以在用户尝试使用应用的某些功能过程中，以递增方式向用户收集这些权限。

代表组织执行的管理员许可仍然需要为应用注册的静态权限，因此，如果需要管理员代表整个组织提供许可，则应在应用注册门户中为应用设置这些权限。 这会减少组织管理员设置应用程序所需的周期数。

## <a name="scopes-not-resources"></a>范围而非资源

对于使用 v1.0 终结点的应用，应用可以充当**资源**或令牌接收者。 资源可定义它所了解的许多**范围**或 **oAuth2Permissions**，使客户端应用能够从该资源中为一组特定的范围请求令牌。 请考虑 Microsoft Graph API 作为资源的示例：

* 资源标识符，或 `AppID URI`：`https://graph.microsoft.com/`
* 范围或 `oAuth2Permissions`：`Directory.Read`、`Directory.Write` 等等。

这适用于 Microsoft 标识平台终结点。 应用仍可充当资源、定义范围并由 URI 标识。 客户端应用程序仍可请求访问这些范围。 但是，客户端请求这些权限的方式已更改。

对于 v1.0 终结点，Azure AD 的 OAuth 2.0 授权请求可能如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

此处的 **resource** 参数指示客户端应用请求授权的资源。 Azure AD 根据 Azure 门户中的静态设置计算应用程序所需的权限，并据以发出令牌。

对于使用 Microsoft 标识平台终结点的应用程序，相同的 OAuth 2.0 授权请求如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

此处的 **scope** 参数指示应用请求授权的资源和权限。 所需的资源仍然在请求中提供 - 它包含在 scope 参数的每个值中。 以这种方式使用 scope 参数可使 Microsoft 标识平台终结点更符合 OAuth 2.0 规范，并更密切地与常见的行业实践进行对齐。 它还使应用程序能够进行[增量许可](#incremental-and-dynamic-consent)-仅当应用程序要求它们（而不是前端）时请求权限。

## <a name="well-known-scopes"></a>已知的范围

### <a name="offline-access"></a>脱机访问

使用 Microsoft 标识平台终结点的应用可能需要针对应用使用新的已知权限-`offline_access` 范围。 如果应用程序需要长期表示用户访问资源，则所有应用程序都需要请求此权限，即使用户可能不主动使用此应用程序亦然。 在许可对话框中，`offline_access` 范围对用户显示为“随时访问数据”，而用户必须同意。 请求 `offline_access` 权限将允许 web 应用从 Microsoft 标识平台终结点接收 OAuth 2.0 refresh_tokens。 刷新令牌属于长效令牌，可用于交换新的 OAuth 2.0 访问令牌以延长访问期限。

如果你的应用未请求 `offline_access` 范围，则它不会接收刷新令牌。 这意味着，在 OAuth 2.0 授权代码流中兑换授权代码时，只从 `/token` 终结点接收访问令牌。 该访问令牌短时间维持有效（通常是一小时），但最后终将过期。 到时，应用必须将用户重定向回到 `/authorize` 终结点以检索新的授权代码。 在此重定向期间，根据应用程序的类型，用户或许无需再次输入其凭据或重新同意权限。

若要了解有关 OAuth 2.0、`refresh_tokens`和 `access_tokens`的详细信息，请参阅[Microsoft 标识平台协议参考](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

### <a name="openid-profile-and-email"></a>OpenID、profile 和 email

从历史上看，使用 Microsoft 标识平台的最基本的 OpenID Connect 登录流在生成的*id_token*中提供了大量有关用户的信息。 id_token 中的声明可以包含用户的名称、首选用户名、电子邮件地址和对象 ID 等等。

现在对 `openid` 范围允许应用访问的信息进行了限制。 `openid` 范围只允许应用将用户登录，并接收用户的应用特定标识符。 如果想要在应用中获取关于用户的个人数据，则应用需要向用户请求额外的权限。 两个新范围 `email` 和 `profile` 将允许请求额外的权限。

* 假设用户具有可寻址的电子邮件地址，则 `email` 范围允许应用通过 id_token 中的 `email` 声明访问用户的主要电子邮件地址。
* `profile` 范围使你的应用程序可以访问 id_token 中有关用户的所有其他基本信息，例如其名称、首选用户名、对象 ID 等等。

使用这些范围可在尽可以能透露最少信息的情况下为应用编写代码，因此，只能向用户请求应用执行其作业所需的信息集。 有关这些范围的详细信息，请参阅[Microsoft 标识平台范围引用](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

## <a name="token-claims"></a>令牌声明

默认情况下，Microsoft 标识平台终结点在其令牌中颁发一组较小的声明，以使负载保持较小。 如果你的应用程序和服务依赖于不在 Microsoft 标识平台令牌中默认提供的1.0 版令牌中的特定声明，请考虑使用[可选的声明](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)功能来包含该声明。

> [!IMPORTANT]
> v1.0 和 v2.0 终结点都可以颁发 v1.0 和 v2.0 令牌！ id_tokens*始终*匹配请求的终结点，并且访问令牌*始终*匹配客户端将使用该令牌调用的 Web API 所需的格式。  因此，如果你的应用程序使用 v2.0 终结点获取令牌来调用 Microsoft Graph，这需要使用 v1.0 格式的访问令牌，则你的应用程序将收到采用1.0 格式的令牌。  

## <a name="limitations"></a>限制

使用 Microsoft 标识平台时，有一些限制需要注意。

构建与 Microsoft 标识平台集成的应用程序时，需要确定 Microsoft 标识平台终结点和身份验证协议是否满足你的需求。 1\.0 版终结点和平台仍完全受支持，并且在某些方面比 Microsoft 标识平台功能更丰富。 但是，Microsoft 标识平台为开发人员[带来了极大的好处](azure-ad-endpoint-comparison.md)。

下面是针对开发人员的简单建议：

* 如果你希望或需要在你的应用程序中支持个人 Microsoft 帐户，或者你正在编写新应用程序，请使用 Microsoft 标识平台。 但在此之前，请确保了解本文所述的限制。
* 如果要迁移或更新依赖 SAML 的应用程序，则无法使用 Microsoft 标识平台。 请参阅[Azure AD v1.0 指南](v1-overview.md)。

Microsoft 标识平台终结点将发展以消除此处列出的限制，因此你只需使用 Microsoft 标识平台终结点。 同时，请使用本文确定 Microsoft 标识平台终结点是否适合你。 我们将继续更新本文，以反映 Microsoft 标识平台终结点的当前状态。 请返回以根据 Microsoft 标识平台功能重新评估你的要求。

### <a name="restrictions-on-app-registrations"></a>应用注册限制

对于想要与 Microsoft 标识平台终结点集成的每个应用，可以在 Azure 门户的新[**应用注册**体验](https://aka.ms/appregistrations)中创建应用注册。 现有 Microsoft 帐户的应用不与门户兼容，但所有 Azure AD 应用都是，不管它们是在何处注册的。

支持工作和学校帐户以及个人帐户的应用注册的注意事项如下：

* 每个应用程序 ID 只允许有两个应用密码。
* 未在租户中注册的应用程序只能由注册它的帐户管理。 不能与其他开发人员共享该应用程序。 在应用注册门户中使用 Microsoft 个人帐户注册的大多数应用都是如此。 如果要与多个开发人员共享应用注册，请使用 Azure 门户的新**应用注册**部分在租户中注册该应用程序。
* 允许的重定向 URL 格式存在一些限制。 有关重定向 URL 的详细信息，请参阅下一部分。

### <a name="restrictions-on-redirect-urls"></a>重定向 URL 的限制

为 Microsoft 标识平台注册的应用限制为一组有限的重定向 URL 值。 Web 应用和服务的重定向 URL 必须以方案 `https` 开头，并且所有重定向 URL 值必须共享一个 DNS 域。  注册系统会将现有重定向 URL 的完整 DNS 名称与要添加的重定向 URL 的 DNS 名称相比较。 也支持将 `http://localhost` 用作重定向 URL。  

如果满足以下任一条件，添加 DNS 名称的请求会失败：  

* 新的重定向 URL 的完整 DNS 名称与现有的重定向 URL 的 DNS 名称不匹配。
* 新重定向 URL 的完整 DNS 名称不是现有重定向 URL 的子域。

#### <a name="example-1"></a>示例 1

如果应用的重定向 URL 为 `https://login.contoso.com`，则你可以添加 DNS 名称完全匹配的重定向 URL，如以下示例所示：

`https://login.contoso.com/new`

或者，可以引用 login.contoso.com 的 DNS 子域，如以下示例所示：

`https://new.login.contoso.com`

#### <a name="example-2"></a>示例 2

若要在应用中包含 `login-east.contoso.com` 和 `login-west.contoso.com` 作为重定向 URL，必须按以下顺序添加这些重定向 URL：

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

可以添加后两个，因为它们是第一个重定向 URL contoso.com 的子域。

对于特定的应用程序，只能有20个答复 Url-此限制适用于注册支持的所有应用类型（单页应用程序（SPA）、本机客户端、web 应用和服务）。  

若要了解如何注册应用以与 Microsoft 标识平台一起使用，请参阅[使用全新的应用注册体验注册应用](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

### <a name="restrictions-on-libraries-and-sdks"></a>库和 SDK 限制

目前，Microsoft 标识平台终结点的库支持受到限制。 如果要在生产应用程序中使用 Microsoft 标识平台终结点，则可以使用以下选项：

* 如果要构建 web 应用程序，则可以安全地使用正式发布的服务器端中间件来执行登录和令牌验证。 其中包括适用于 ASP.NET 的 OWIN OpenID Connect 中间件和 Node.js Passport 插件。 有关使用 Microsoft 中间件的代码示例，请参阅[microsoft 标识平台入门](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started)部分。
* 如果要构建桌面或移动应用程序，可以使用 Microsoft 身份验证库（MSAL）之一。 这些库已公开发布或处于支持生产的预览版中，因此可以在生产应用程序中安全地使用它们。 有关预览版和可用库的术语的详细信息，请阅读[身份验证库参考](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)中的内容。
* 对于 Microsoft 库未涵盖的平台，可以通过直接在应用程序代码中发送和接收协议消息来与 Microsoft 标识平台终结点集成。 OpenID Connect 和 OAuth 协议[已明确](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)说明，可帮助您执行此类集成。
* 最后，可以使用开源 OpenID Connect 和 OAuth 库与 Microsoft 标识平台终结点集成。 Microsoft 标识平台终结点应与许多开源协议库兼容，且不会发生更改。 这些类型的库的可用性根据语言和平台而有所差异。 [OpenID Connect](https://openid.net/connect/) 和 [OAuth 2.0](https://oauth.net/2/) 网站将维护一份热门实现列表。 有关详细信息，请参阅[microsoft 标识平台和身份验证库](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)，以及已使用 Microsoft 标识平台终结点测试的开源客户端库和示例的列表。
* 作为参考，Microsoft 标识平台公用终结点的 `.well-known` 终结点 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`。 将 `common` 替换为你的租户 ID，以获取特定于你的租户的数据。  

### <a name="protocol-changes"></a>协议更改

Microsoft 标识平台终结点不支持 SAML 或 WS 联合身份验证;它仅支持 OpenID Connect 和 OAuth 2.0。  相比 v1.0 终结点，OAuth 2.0 协议的重大变化包括： 

* 如果配置了可选声明，`email`或者**在请求中指定了 scope=email，则返回**  声明。 
* 现在支持使用 `scope` 参数来取代 `resource` 参数。  
* 许多响应已经过修改，因此更符合 OAuth 2.0 规范，例如，可正确返回整数而不是字符串形式的 `expires_in`。  

若要更好地了解 Microsoft 标识平台终结点支持的协议功能范围，请参阅[OpenID connect 和 OAuth 2.0 协议参考](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

#### <a name="saml-restrictions"></a>SAML 限制

如果已在 Windows 应用程序中使用 Active Directory 身份验证库（ADAL），则可能已利用了使用安全断言标记语言（SAML）断言授予的 Windows 集成身份验证。 通过此授予，联合 Azure AD 租户的用户可以使用其本地 Active Directory 实例以无提示方式进行身份验证，而无需输入凭据。 Microsoft 标识平台终结点不支持 SAML 断言授予。
