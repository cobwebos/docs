---
title: Azure AD v2.0 终结点与 v1.0 终结点的比较 | Microsoft Docs
description: 了解 Azure AD v2.0 终结点与 v1.0 终结点之间的差异
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: andret
ms.reviewer: hirsin, andret
ms.custom: aaddev
ms.openlocfilehash: 215e0abe196620624dcca7f430aec4ee9b9612f2
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288197"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Azure AD v2.0 终结点与 v1.0 终结点的比较

开发新应用程序时，必须知道 v1.0 与 v2.0 终结点之间的差异。 下面是两者的主要差异，以及 v2.0 终结点现存的一些限制。

> [!NOTE]
> v2.0 终结点并不支持所有 Azure Active Directory (Azure AD) 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](#limitations)。

## <a name="who-can-sign-in"></a>谁可以登录

![谁可以使用 v1.0 和 v2.0 终结点登录](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* v1.0 终结点仅允许使用工作和学校帐户登录到应用程序 (Azure AD)

* v2.0 终结点允许使用 Azure AD 中的工作和学校帐户以及使用个人帐户 (MSA)（hotmail.com、outlook.com、msn.com）登录。

* 对于配置为[单租户](single-and-multi-tenant-apps.md)的应用程序，或者配置为指向租户特定的终结点 (`https://login.microsoftonline.com/{TenantId_or_Name}`) 的多租户应用程序，v1.0 和 v2.0 终结点还接受 Azure AD 目录的[来宾用户](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)登录。

v2.0 终结点允许编写接受个人帐户以及工作和学校帐户登录的应用，使你能够编写完全不区分帐户的应用。 例如，如果应用调用 [Microsoft Graph](https://developer.microsoft.com/graph)，则工作帐户可以使用某些附加功能和数据，如 SharePoint 站点或目录数据。 但对于许多操作（例如[读取用户的邮件](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/message)），相同的代码可以访问个人帐户以及工作和学校帐户的电子邮件。

对于 v2.0 终结点，可以使用单个库 (MSAL) 来获取消费者、教育和企业领域的访问权限。

 Azure AD v1.0 终结点仅接受工作和学校帐户的登录。

## <a name="incremental-and-dynamic-consent"></a>增量同意和动态同意

使用 Azure AD v1.0 终结点的应用需要提前指定所需的 OAuth 2.0 权限，例如：

![权限注册 UI](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

直接在应用程序注册中设置的权限是**静态的**。 尽管在 Azure 门户中定义应用的静态权限能保持代码的简洁性，但可能会给开发人员带来几个问题：

* 在创建时，应用需要知道可能需要的所有权限。 随着时间添加权限是一个繁琐的过程。

* 应用需要事先知道可能访问的所有资源。 很难创建能够访问任意数目的资源的应用程序。

* 应用需要在用户首次登录时请求可能需要的权限。 在某些情况下，这会导致冗长的权限列表，而让最终用户在初始登录时打消审批应用程序访问权限的念头。

使用 v2.0 终结点可以忽略 Azure 门户中在应用程序注册信息中定义的静态权限，并在日常使用应用的过程中，在运行时**动态**指定应用所需的权限，而不管在应用程序注册信息中静态定义了哪些权限。

为此，可以在请求访问令牌时，通过在 `scope` 参数中包含新的范围，在应用时间中的任意给定时间点指定应用所需的范围 - 无需在应用程序注册信息中预定义这些范围。

如果用户尚未许可添加到请求的新范围，则系统会提示他们仅许可新的权限。 若要了解详细信息，可以阅读[权限、同意和范围](v2-permissions-and-consent.md)。

允许应用通过 `scope` 参数动态请求权限可让开发人员完全控制用户的体验。 如果需要，还可以选择将许可体验提前，并在一个初始授权请求中请求所有的权限。 或者，如果应用程序需要大量的权限，可以选择在尝试使用应用程序的某些功能时，以递增方式向用户收集这些权限。

请注意，代表组织执行的管理员许可仍使用为应用注册的静态权限，因此，如果需要管理员代表整个组织提供许可，则我们建议使用 v2.0 终结点为应用设置这些权限。 这会减少组织管理员设置应用程序所需的周期数

## <a name="scopes-not-resources"></a>范围而非资源

对于使用 v1.0 终结点的应用，应用可以充当**资源**或令牌接收者。 资源可定义它所了解的许多**范围**或 **oAuth2Permissions**，让客户端应用得以针对一组特定范围请求该资源的令牌。 请考虑以 Azure AD 图形 API 作为资源的示例：

* 资源标识符，或 `AppID URI`：`https://graph.windows.net/`

* 范围或 `OAuth2Permissions`：`Directory.Read`、`Directory.Write` 等等。

这一切都适用于 v2.0 终结点。 应用程序仍可作为资源、定义范围并由 URI 标识。 客户端应用程序仍可请求访问这些范围。 但是，客户端用于请求这些权限的方式已改变。 对于 v1.0 终结点，Azure AD 的 OAuth 2.0 授权请求可能如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

其中 **resource** 参数指示客户端应用请求授权的资源。 Azure AD 根据 Azure 门户中的静态设置计算应用程序所需的权限，并据以发出令牌。 对于使用 v2.0 终结点的应用程序，相同的 OAuth 2.0 授权请求如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

其中 **scope** 参数指示应用请求授权的资源和权限。 所需的资源仍然在请求中提供 - 它只包含在 scope 参数的每个值中。 以此方式使用 scope 参数可让 v2.0 终结点更符合 OAuth 2.0 规范，并且更贴近常见的行业实践。 它还可以让应用执行前面所述的[增量许可](#incremental-and-dynamic-consent)。

## <a name="well-known-scopes"></a>已知的范围

### <a name="offline-access"></a>脱机访问

使用 v2.0 终结点的应用可能需要针对应用使用新的已知权限 - `offline_access` 范围。 如果应用程序需要长期表示用户访问资源，则所有应用程序都需要请求此权限，即使用户可能不主动使用此应用程序亦然。 在许可对话框中，`offline_access` 范围对用户显示为“随时访问数据”，而用户必须同意。 请求 `offline_access` 权限可让 Web 应用从 v2.0 终结点接收 OAuth 2.0 refresh_tokens。 刷新令牌属于长效令牌，可用于交换新的 OAuth 2.0 访问令牌以延长访问期限。

如果应用未请求 `offline_access` 范围，则收不到刷新令牌。 这意味着，在 OAuth 2.0 授权代码流中兑换授权代码时，只从 `/token` 终结点接收访问令牌。 该访问令牌短时间维持有效（通常是一小时），但最后终将过期。 到时，应用必须将用户重定向回到 `/authorize` 终结点以检索新的授权代码。 在此重定向期间，根据应用程序的类型，用户或许无需再次输入其凭据或重新同意权限。

若要详细了解 OAuth 2.0、`refresh_tokens` 和 `access_tokens`，请查看 [v2.0 协议参考](active-directory-v2-protocols.md)。

### <a name="openid-profile-and-email"></a>OpenID、profile 和 email

以前，使用 Azure AD 的最基本型 OpenID Connect 登录流会在生成的 *id_token* 中提供丰富的用户相关信息。 *id_token* 中的声明可以包含用户的名称、首选用户名、电子邮件地址和对象 ID 等等。

现在对 `openid` 范围允许应用访问的信息进行了限制。 `openid` 范围只允许应用将用户登录，并接收用户的应用特定标识符。 如果想要在应用中获取关于用户的个人数据，则应用需要向用户请求额外的权限。 两个新范围 `email` 和 `profile` 将允许请求额外的权限。

`email` 范围允许应用通过 id_token 中的 `email` 声明访问用户的主要电子邮件地址。 `profile` 范围可让应用访问用户的所有其他基本信息 — 其名称、首选用户名、对象 ID 等等。

这样便可在尽可以能透露最少信息的情况下为应用编码 - 只可以向用户请求应用执行其作业所需的信息集。 有关这些范围的详细信息，请参阅 [v2.0 范围参考](v2-permissions-and-consent.md)。

## <a name="token-claims"></a>令牌声明

v2.0 终结点颁发的令牌中的声明与正式发布的 Azure AD 终结点颁发的令牌不会完全相同。 要迁移到新服务的应用不应当假定特定的声明将存在于 id_tokens 或 access_tokens 中。 有关 v2.0 终结点中使用的不同类型令牌的更多详细信息，请参阅[访问令牌](access-tokens.md)参考和 [`id_token` 参考](id-tokens.md)

## <a name="limitations"></a>限制

使用 v2.0 时有一些要注意的限制。

生成与 Microsoft 标识平台集成的应用程序时，需确定 v2.0 终结点和身份验证协议是否满足需求。 v1.0 终结点和平台仍完全受支持，并且在某些方面比 v2.0 的功能更丰富。 但是，v2.0 为开发人员[带来了极大的好处](azure-ad-endpoint-comparison.md)。

下面是目前为开发人员提供的几点建议：

* 如果必须在应用程序中支持个人 Microsoft 帐户，请使用 v2.0。 但在执行操作前，请确保了解本文讨论的限制。

* 如果应用程序只需支持 Microsoft 工作和学校帐户，则不要使用 v2.0。 相反，请参阅 [v1.0 指南](v1-overview.md)。

v2.0 终结点将演变为消除此处列出的限制，因此你只需要使用 v2.0 终结点。 在此期间，使用本文来确定 v2.0 终结点是否适合你。 我们将持续更新本文，以反映 v2.0 终结点当前的状态。 可返回查看，重新评估对 v2.0 功能的要求。

### <a name="restrictions-on-app-types"></a>应用类型限制

v2.0 终结点目前不支持以下应用类型。 有关受支持应用类型的说明，请参阅 [v2.0 中的应用类型](v2-app-types.md)。

#### <a name="standalone-web-apis"></a>独立 Web API

可以使用 v2.0 终结点[生成 OAuth 2.0 保护的 Web API](v2-app-types.md#web-apis)。 但是，该 Web API 只能从应用程序 ID 相同的应用程序接收令牌。 不能从应用程序 ID 不同的客户端访问 Web API。 该客户端无法请求或获取对 Web API 的权限。

若要了解如何构建从应用程序 ID 相同的客户端接受令牌的 Web API，请参阅 [v2.0 入门](v2-overview.md#getting-started)部分中的 v2.0 终结点 Web API 示例。

### <a name="restrictions-on-app-registrations"></a>应用注册限制

目前，对于每个想要与 v2.0 终结点集成的应用，必须在新的 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中创建应用注册。 现有的 Azure AD 或 Microsoft 帐户应用与 v2.0 终结点不兼容。 在应用程序注册门户以外的任何门户中注册的应用均与 v2.0 终结点不兼容。

此外，在[应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中创建的应用注册具有以下注意事项：

* 每个应用程序 ID 只允许有两个应用密码。

* 对于用户使用个人 Microsoft 帐户注册的应用注册，只能使用一个开发人员帐户进行查看和管理。 不能在多个开发人员之间共享。 如果希望多名开发人员共享应用注册，可以通过使用 Azure AD 帐户登录注册门户来创建应用程序。

* 允许的重定向 URL 格式存在一些限制。 有关重定向 URL 的详细信息，请参阅下一部分。

### <a name="restrictions-on-redirect-urls"></a>重定向 URL 的限制

在应用程序注册门户中注册的应用限制为一组有限的重定向 URL 值。 Web 应用和服务的重定向 URL 必须以方案 `https` 开头，并且所有重定向 URL 值必须共享一个 DNS 域。 例如，不能注册具有以下重定向 URL 的 Web 应用：

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

注册系统会将现有重定向 URL 的完整 DNS 名称与要添加的重定向 URL 的 DNS 名称相比较。 如果满足以下任一条件，添加 DNS 名称的请求会失败：  

* 新的重定向 URL 的完整 DNS 名称与现有的重定向 URL 的 DNS 名称不匹配。

* 新重定向 URL 的完整 DNS 名称不是现有重定向 URL 的子域。

例如，如果应用包含以下重定向 URL：

`https://login.contoso.com`

可以向其添加，如下所示：

`https://login.contoso.com/new`

在这种情况下，DNS 名称将完全匹配。 或者，可以执行下面的操作：

`https://new.login.contoso.com`

在这种情况下，将引用 login.contoso.com 的 DNS 子域。 若要在应用中包含 `login-east.contoso.com` 和 `login-west.contoso.com` 作为重定向 URL，必须按顺序添加以下重定向 URL：

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

可以添加后两个重定向 URL，因为它们是第一个重定向 URL (contoso.com) 的子域。 即将发布的版本中将取消此限制。

另请注意，只能为特定应用程序设置 20 个答复 URL。

若要了解如何在应用程序注册门户中注册应用，请参阅[如何使用 v2.0 终结点注册应用](quickstart-v2-register-an-app.md)。

### <a name="restrictions-on-libraries-and-sdks"></a>库和 SDK 限制

当前，对 v2.0 终结点的库支持有所限制。 如果想要在生产应用程序中使用 v2.0 终结点，可使用以下选项：

* 如果要构建 Web 应用程序，可以放心使用 Microsoft 正式版服务器端中间件来执行登录和令牌验证。 其中包括适用于 ASP.NET 的 OWIN Open ID Connect 中间件和 Node.js Passport 插件。 有关使用 Microsoft 中间件的代码示例，请参阅 [v2.0 入门](v2-overview.md#getting-started)部分。

* 若要构建桌面或移动应用程序，可以使用一个预览版 Microsoft 身份验证库 (MSAL)。 这些库当前是支持生产的预览版，因此可在生产应用程序中放心使用。 有关预览版和可用库的术语的详细信息，请阅读[身份验证库参考](reference-v2-libraries.md)中的内容。

* 对于 Microsoft 库不支持的平台，可以通过直接在应用程序代码中发送和接收协议消息来与 v2.0 终结点进行集成。 v2.0 OpenID Connect 和 OAuth 协议[有明确的说明文档](active-directory-v2-protocols.md)，可帮助执行此类集成。

* 最后，可以使用开源 Open ID Connect 和 OAuth 库来与 v2.0 终结点集成。 v2.0 协议应与许多开源协议库兼容，不需要进行重大更改。 这些类型的库的可用性根据语言和平台而有所差异。 [Open ID Connect](http://openid.net/connect/) 和 [OAuth 2.0](http://oauth.net/2/) 网站将维护一份热门实现列表。 有关详细信息和经过 v2.0 终结点检验的开放源代码客户端库和示例列表，请参阅 [Azure Active Directory v2.0 身份验证库](reference-v2-libraries.md)。

* 作为参考，v2.0 公共终结点的 `.well-known` 终结点为 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`。  将 `common` 替换为你的租户 ID，以获取特定于你的租户的数据。  

### <a name="restrictions-on-protocols"></a>协议限制

v2.0 终结点不支持 SAML 或 WS 联合身份验证；它仅支持 Open ID Connect 和 OAuth 2.0。 并非每个 OAuth 协议的所有特性与功能都已合并到 v2.0 终结点中。

在 v2.0 终结点中，以下协议特性和功能目前*不可用*或*不受支持*：

* 仅当配置了可选声明并在请求中指定了范围为 scope=email 时，才会返回 `email` 声明。 但是，此行为应会随着 v2.0 终结点的更新而更改，以进一步符合 Open ID Connect 和 OAuth2.0 标准。

* v2.0 终结点不支持在 ID 令牌中发布角色或组声明。

* v2.0 终结点不支持 [OAuth 2.0 资源所有者密码凭据授予](https://tools.ietf.org/html/rfc6749#section-4.3)。

若要进一步了解 v2.0 终结点支持的协议功能范围，请阅读 [OpenID Connect 和 OAuth 2.0 协议参考](active-directory-v2-protocols.md)。

#### <a name="saml-restrictions"></a>SAML 限制

如果已在 Windows 应用程序中使用了 Active Directory 身份验证库 (ADAL)，则可能已利用了使用安全断言标记语言 (SAML) 断言授予的 Windows 集成身份验证。 通过此授予，联合 Azure AD 租户的用户可以使用其本地 Active Directory 实例以无提示方式进行身份验证，而无需输入凭据。 v2.0 终结点当前不支持 SAML 断言授予。
