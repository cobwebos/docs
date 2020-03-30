---
title: 将基于 OWIN 的 Web API 迁移到b2clogin.com
titleSuffix: Azure AD B2C
description: 了解如何启用 .NET Web API 以支持多个令牌颁发者在将应用程序迁移到b2clogin.com时颁发的令牌。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184088"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>将基于 OWIN 的 Web API 迁移到b2clogin.com

本文介绍了一种技术，用于在实现[.NET （OWIN） 的开放 Web 接口](http://owin.org/)的 Web API 中为多个令牌颁发者提供支持。 将 Azure 活动目录 B2C （Azure AD B2C） API 及其应用程序从*login.microsoftonline.com*迁移到*b2clogin.com*时，支持多个令牌终结点非常有用。

通过在 API 中添加支持以接受b2clogin.com和login.microsoftonline.com颁发的令牌，可以在从 API 中删除对login.microsoftonline.com颁发的令牌的支持之前分阶段迁移 Web 应用程序。

以下各节介绍了如何在使用[Microsoft OWIN][katana]中间件组件 （Katana） 的 Web API 中启用多个颁发者的示例。 尽管代码示例特定于 Microsoft OWIN 中间件，但常规技术应适用于其他 OWIN 库。

> [!NOTE]
> 本文面向 Azure AD B2C 客户，这些客户当前已部署 API`login.microsoftonline.com`和应用程序引用并希望迁移到建议的`b2clogin.com`终结点。 如果要设置新应用程序，请按照指示使用[b2clogin.com。](b2clogin.md)

## <a name="prerequisites"></a>先决条件

在继续执行本文中的步骤之前，您需要将以下 Azure AD B2C 资源到位：

* 在租户中创建[的用户流](tutorial-create-user-flows.md)或[自定义策略](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>获取令牌颁发者终结点

您首先需要获取要在 API 中支持的每个颁发者令牌颁发者终结点 URI。 要获取 Azure AD B2C 租户支持的*b2clogin.com**和login.microsoftonline.com*终结点，请使用 Azure 门户中的以下过程。

首先选择一个现有用户流：

1. 导航到[Azure 门户](https://portal.azure.com)中的 Azure AD B2C 租户
1. 在**策略**下，选择**用户流（策略）**
1. 选择现有策略，例如*B2C_1_signupsignin1*，然后选择 **"运行用户流"**
1. 在页面顶部附近的 **"运行用户流**"标题下，选择要导航到该用户流的 OpenID Connect 发现终结点的超链接。

    ![Azure 门户的“立即运行”页中的“已知 URI”超链接](media/multi-token-endpoints/portal-01-policy-link.png)

1. 在浏览器中打开的页上，记下 `issuer` 值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. 使用 **"选择域**下拉列表"选择其他域，然后再次执行前两个步骤并记录其`issuer`值。

现在，您应该记录两个类似于：

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>自定义策略

如果您有自定义策略而不是用户流，则可以使用类似的过程来获取颁发者 URI。

1. 导航到 Azure AD B2C 租户
1. 选择“标识体验框架”****
1. 选择依赖方策略之一，例如 *，B2C_1A_signup_signin*
1. 使用 **"选择域**"下拉列表选择域，例如*yourtenant.b2clogin.com*
1. 选择**OpenID 连接发现终结点**下显示的超链接
1. 记录`issuer`值
1. 为其他域执行步骤 4-6，例如*login.microsoftonline.com*

## <a name="get-the-sample-code"></a>获取示例代码

现在，您已经拥有了两个令牌终结点 URI，您需要更新代码以指定两个终结点都是有效的颁发者。 要演练示例，请下载或克隆示例应用程序，然后更新示例以支持两个终结点作为有效的颁发者。

下载存档：[活动目录-b2c-点网-webapp-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>在 Web API 中启用多个颁发者

在本节中，您将更新代码以指定两个令牌颁发者终结点都有效。

1. 在可视化工作室中打开**B2C-WebAPI-DotNet.sln**解决方案
1. 在**任务服务**项目中，在编辑器中打开*任务\\服务\\App_Start [Startup.Auth.cs*] 文件
1. 将以下`using`指令添加到文件顶部：

    `using System.Collections.Generic;`
1. 将[`ValidIssuers`][validissuers]属性添加到定义并[`TokenValidationParameters`][tokenvalidationparameters]指定在上一节中记录的两个 URI：

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`由MSAL.NET提供，并由*oWIN*中间件在 Startup.Auth.cs 的下一节中使用。 指定多个有效颁发器后，OWIN 应用程序管道将知道两个令牌终结点都是有效的颁发者。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

如前所述，其他 OWIN 库通常提供类似的功能来支持多个颁发者。 尽管为每个库提供示例超出了本文的范围，但您可以对大多数库使用类似的技术。

## <a name="switch-endpoints-in-web-app"></a>切换 Web 应用中的终结点

Web API 现在支持这两个 URI，现在需要更新 Web 应用程序，以便从b2clogin.com终结点检索令牌。

例如，您可以通过修改**TaskWebApp**项目的*TaskWebApp _WebApp _WebApp_Web.config\\*_ 文件中`ida:AadInstance`的值来配置示例 Web 应用程序以使用新终结点。

更改`ida:AadInstance`TaskWebApp 的*Web.config 中*的值，以便`{your-b2c-tenant-name}.b2clogin.com`它引用`login.microsoftonline.com`而不是 。

早于:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

之后（替换为`{your-b2c-tenant}`B2C 租户的名称）：

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

当在 Web 应用执行期间构造终结点字符串时，在请求令牌时将使用基于b2clogin.com终结点。

## <a name="next-steps"></a>后续步骤

本文介绍了一种配置实现 Microsoft OWIN 中间件 （Katana） 的 Web API 的方法，以接受来自多个颁发者终结点的令牌。 您可能注意到 *，Web.Config*文件中的其他几个字符串是 TaskService 和 TaskWebApp 项目的文件，如果要针对自己的租户生成和运行这些项目，则需要更改这些文件。 但是，如果您希望看到项目在操作中，欢迎您适当地修改这些项目，但是，这样做的完整演练不在本文的范围之内。

有关 Azure AD B2C 发出的不同类型的安全令牌的详细信息，请参阅 Azure[活动目录 B2C 中的令牌概述](tokens-overview.md)。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
