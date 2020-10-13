---
title: Microsoft 标识平台中的 OAuth 2.0 客户端凭据流 | Azure
description: 使用 OAuth 2.0 身份验证协议的 Microsoft 标识平台实现生成 Web 应用程序。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/2/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 89a4c62044e3be849650de703d2daa9ca3e2a975
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932577"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft 标识平台和 OAuth 2.0 客户端凭据流

可通过 RFC 6749 中指定的 [OAuth 2.0 客户端凭据授予](https://tools.ietf.org/html/rfc6749#section-4.4)（有时称为“双重 OAuth”），使用应用程序标识来访问 Web 托管的资源。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 此类应用程序通常称为守护程序或服务帐户。

本文介绍如何在应用程序中直接针对协议进行编程。 如果可能，建议你改用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

OAuth 2.0 客户端凭据授权流允许 Web 服务（机密客户端）在调用其他 Web 服务时使用它自己的凭据（而不是模拟用户）进行身份验证。 在这种情况下，客户端通常是中间层 Web 服务、后台程序服务或网站。 为了进行更高级别的保证，Microsoft 标识平台还允许调用服务将证书（而不是共享机密）用作凭据。

在客户端凭据流中，由管理员向应用程序本身直接授予权限。 当应用程序向资源出示令牌时，资源强制应用本身具有执行操作的授权，因为没有用户参与身份验证。  本文介绍[授权应用程序调用 API](#application-permissions) 所需的步骤，以及[如何获取调用该 API 所需的令牌](#get-a-token)。

## <a name="protocol-diagram"></a>协议图

整个客户端凭据流类似于下图。 本文稍后介绍每个步骤。

![显示客户端凭据流的关系图](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>获取直接授权

应用往往通过以下两种方式之一接收直接授权来访问资源：

* [通过资源中的访问控制列表 (ACL)](#access-control-lists)
* [通过 Azure AD 中的应用程序权限分配](#application-permissions)

这两种方法在 Azure AD 中很常见，建议用于执行客户端凭据流的客户端和资源。 资源也可选择以其他方式向其客户端授权。 每个资源服务器可选择最适合其应用程序的方法。

### <a name="access-control-lists"></a>访问控制列表

资源提供程序可根据它所知并对其授予特定级别访问权限的应用程序（客户端）ID 列表，强制实施授权检查。 资源从 Microsoft 标识平台终结点接收令牌时，可以将此令牌解码，并从 `appid` 和 `iss` 声明中提取客户端的应用程序 ID。 然后将应用程序与它所维护的访问控制列表 (ACL) 相比较。 ACL 的粒度和方法可能因资源不同而有较大差异。

常见用例是使用 ACL 对 Web 应用程序或 Web API 运行测试。 Web API 可能仅向特定客户端授予部分完全权限。 若要在 API 上运行端到端测试，请创建测试客户端，以便从 Microsoft 标识平台终结点获取令牌并将令牌发送到 API。 然后，API 会检查测试客户端应用程序 ID 的 ACL，以获取对 API 整个功能的完全访问权限。 如果使用这种 ACL，不仅需要验证调用方的 `appid` 值，而且还要验证令牌的 `iss` 值是否受信任。

对于需要访问使用者用户（拥有个人 Microsoft 帐户）所拥有数据的守护程序和服务帐户而言，这种授权类型很常见。 对于组织拥有的数据，建议通过应用程序权限获取必要的授权。

#### <a name="controlling-tokens-without-the-roles-claim"></a>控制没有声明的令牌 `roles`

若要启用此基于 ACL 的授权模式，Azure AD 不要求应用程序获得另一个应用程序的令牌。 因此，无需声明即可颁发仅限应用的令牌 `roles` 。 公开 API 的应用程序必须实现权限检查才能接受令牌。

如果你想要防止应用程序为应用程序获取不受限制的仅限应用的访问令牌，请 [确保为你的应用启用了用户分配要求](../manage-apps/assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment)。 这会阻止未分配角色的用户和应用程序能够获取此应用程序的令牌。 

### <a name="application-permissions"></a>应用程序权限

可使用 API 公开一组应用程序权限，而不是使用 ACL。 应用程序权限由组织管理员向应用程序授予，并且只可用于访问该组织与其员工所拥有的数据。 例如，Microsoft Graph 公开多个应用程序权限来执行以下操作：

* 读取所有邮箱中的邮件
* 在所有邮箱中读取和写入邮件
* 以任何用户的身份发送邮件
* 读取目录数据

若要将应用程序权限用于自己的 API（与 Microsoft Graph 相反），必须首先通过在 Azure 门户中 API 的应用注册中定义作用域来[公开 API](quickstart-configure-app-expose-web-apis.md)。 然后，通过在客户端应用程序的应用注册中选择这些权限来[配置对 API 的访问](quickstart-configure-app-access-web-apis.md)。 如果你在 API 的应用注册中没有公开任何作用域，则将无法在 Azure 门户中的客户端应用程序的应用注册中为该 API 指定应用程序权限。

以应用程序形式（不同于与以用户身份）进行身份验证时，不能使用“委托的权限”（用户授予的范围）。 必须使用“应用程序权限”（也称为“角色”），这些权限是由管理员为应用程序授予的（或通过 Web API 预授权来授予的）。

有关应用程序权限的详细信息，请参阅[权限和许可](v2-permissions-and-consent.md#permission-types)。

#### <a name="recommended-sign-the-user-into-your-app"></a>建议：让用户登录到应用

在构建使用应用程序权限的应用程序时，应用通常需要一个页面或视图，使管理员能够批准应用的权限。 此页面可以是应用登录流的一部分、应用设置的一部分，或者专用的“连接”流。 在许多情况下，合理的结果是应用只在用户使用工作或学校 Microsoft 帐户登录之后才显示此“连接”视图。

如果让用户登录到应用，可以在请求用户批准应用程序权限之前识别该用户所属组织。 尽管在严格意义上不需要这样做，但这有助于为用户带来更直观的体验。 若要将用户登录，请遵循 [Microsoft 标识平台协议教程](active-directory-v2-protocols.md)。

#### <a name="request-the-permissions-from-a-directory-admin"></a>向目录管理员请求权限

准备好向组织管理员请求权限时，可将用户重定向到 Microsoft 标识平台*管理员许可终结点*。

> [!TIP]
> 尝试在 Postman 中执行此请求！ （为获得最佳效果，请使用自己的应用 ID - 教程应用程序不会请求有用的权限。）[![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

专业提示：请尝试将以下请求粘贴到浏览器中。

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必须 | 要向其请求权限的目录租户。 此参数可采用 GUID 或友好名称格式。 如果不知道用户属于哪个租户并想让他们登录到任一租户，请使用 `common`。 |
| `client_id` | 必须 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验分配给应用的应用（客户端）ID。 |
| `redirect_uri` | 必须 | 要向其发送响应，供应用处理的重定向 URI。 它必须与门户中注册的其中一个重定向 URI 完全匹配，否则必须经过 URL 编码并可包含其他路径段。 |
| `state` | 建议 | 同时随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 该 state 用于在身份验证请求出现之前，于应用中编码用户的状态信息，例如之前所在的页面或视图。 |

此时，Azure AD 强制要求只有租户管理员可以登录来完成请求。 系统会要求管理员批准在应用注册门户中针对应用请求的所有直接应用程序权限。

##### <a name="successful-response"></a>成功的响应

如果管理员批准了应用程序的权限，成功响应如下所示：

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 参数 | 说明 |
| --- | --- |
| `tenant` | 向应用程序授予所请求权限的目录租户（采用 GUID 格式）。 |
| `state` | 同样随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 该 state 用于在身份验证请求出现之前，于应用中编码用户的状态信息，例如之前所在的页面或视图。 |
| `admin_consent` | 设置为 **True**。 |

##### <a name="error-response"></a>错误响应

如果管理员未批准应用程序的权限，失败响应如下所示：

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 参数 | 说明 |
| --- | --- |
| `error` | 可用于将错误分类，以及对错误做出反应的错误代码字符串。 |
| `error_description` | 可帮助识别错误根本原因的特定错误消息。 |

从应用预配终结点收到成功响应后，应用便已获得它所请求的直接应用程序权限。 现在，可以请求所需的资源令牌。

## <a name="get-a-token"></a>获取令牌

获取应用程序的必要授权后，可以继续获取 API 的访问令牌。 若要使用客户端凭据授予获取令牌，请将 POST 请求发送到 `/token` Microsoft 标识平台终结点：

> [!TIP]
> 尝试在 Postman 中执行此请求！ （为获得最佳效果，请使用自己的应用 ID - 教程应用程序不会请求有用的权限。）[![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一种情况：使用共享机密访问令牌请求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必须 | 应用程序计划对其进行操作的目录租户，采用 GUID 或域名格式。 |
| `client_id` | 必须 | 分配给应用的应用程序 ID。 可以在注册应用的门户中找到此信息。 |
| `scope` | 必须 | 在此请求中针对 `scope` 参数传递的值应该是所需资源的资源标识符（应用程序 ID URI），并附有 `.default` 后缀。 在 Microsoft Graph 示例中，该值为 `https://graph.microsoft.com/.default`。 <br/>此值告知 Microsoft 标识平台终结点：在为应用配置的所有直接应用程序权限中，终结点应该为与要使用的资源关联的权限颁发令牌。 若要了解有关 `/.default` 范围的详细信息，请参阅[许可文档](v2-permissions-and-consent.md#the-default-scope)。 |
| `client_secret` | 必须 | 在应用注册门户中为应用生成的客户端机密。 在发送客户端密码之前必须对其进行 URL 编码。 |
| `grant_type` | 必须 | 必须设置为 `client_credentials`。 |

### <a name="second-case-access-token-request-with-a-certificate"></a>第二种情况：使用证书访问令牌请求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必须 | 应用程序计划对其进行操作的目录租户，采用 GUID 或域名格式。 |
| `client_id` | 必须 |分配给应用的应用程序（客户端）ID。 |
| `scope` | 必须 | 在此请求中针对 `scope` 参数传递的值应该是所需资源的资源标识符（应用程序 ID URI），并附有 `.default` 后缀。 在 Microsoft Graph 示例中，该值为 `https://graph.microsoft.com/.default`。 <br/>此值告知 Microsoft 标识平台终结点：在为应用配置的所有直接应用程序权限中，终结点应该为与要使用的资源关联的权限颁发令牌。 若要了解有关 `/.default` 范围的详细信息，请参阅[许可文档](v2-permissions-and-consent.md#the-default-scope)。 |
| `client_assertion_type` | 必须 | 该值必须设置为 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`。 |
| `client_assertion` | 必须 | 断言（JSON Web 令牌），需使用作为凭据向应用程序注册的证书进行创建和签名。 有关如何注册证书以及断言的格式，请阅读[证书凭据](active-directory-certificate-credentials.md)的相关信息。|
| `grant_type` | 必须 | 必须设置为 `client_credentials`。 |

请注意，参数几乎与共享密钥请求的参数相同，只不过 client_secret 参数替换为两个参数：client_assertion_type 和 client_assertion。

### <a name="successful-response"></a>成功的响应

成功响应如下所示：

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| 参数 | 说明 |
| --- | --- |
| `access_token` | 请求的访问令牌。 应用可以使用此令牌向受保护的资源（例如 Web API）进行身份验证。 |
| `token_type` | 指示令牌类型值。 Microsoft 标识平台支持的唯一类型是 `bearer`。 |
| `expires_in` | 访问令牌有效的时间长短（以秒为单位）。 |

### <a name="error-response"></a>错误响应

错误响应如下所示：

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数 | 说明 |
| --- | --- |
| `error` | 可用于对发生的错误分类以及对错误做出反应的错误代码字符串。 |
| `error_description` | 可帮助识别身份验证错误根本原因的特定错误消息。 |
| `error_codes` | 可帮助诊断的 STS 特定错误代码列表。 |
| `timestamp` | 发生错误的时间。 |
| `trace_id` | 可帮助进行诊断的请求的唯一标识符。 |
| `correlation_id` | 可帮助跨组件进行诊断的请求的唯一标识符。 |

## <a name="use-a-token"></a>使用令牌

获取令牌后，使用该令牌对资源发出请求。 令牌过期时，向 `/token` 终结点重复该请求，即可获取全新的访问令牌。

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>代码示例和其他文档

阅读 Microsoft 身份验证库中的[客户端凭据概述文档](https://aka.ms/msal-net-client-credentials)

| 示例 | 平台 |说明 |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1 控制台 | 一个简单的 .NET Core 应用程序，该应用程序显示某个租户的用户在使用应用程序的标识查询 Microsoft Graph，而不是代表用户来查询。 该示例还演示了使用证书进行身份验证的变体。 |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | 一个 Web 应用程序，该应用程序使用应用程序的标识来同步 Microsoft Graph 的数据，而不是代表用户来同步。 |
