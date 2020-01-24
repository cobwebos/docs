---
title: 实例化机密客户端应用（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用用于 .NET 的 Microsoft 身份验证库（MSAL.NET）的配置选项实例化机密客户端应用程序。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c3e1539ba847c66bdc705e2c83a66a42e070235a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695511"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>使用 MSAL.NET 实例化包含配置选项的机密客户端应用程序

本文介绍如何使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）实例化[机密客户端应用程序](msal-client-applications.md)。  使用在设置文件中定义的配置选项对应用程序进行实例化。

在初始化应用程序之前，首先需要[注册](quickstart-register-app.md)它，以便您的应用程序可以与 Microsoft 标识平台集成。 注册后，你可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID （表示 GUID 的字符串）
- 应用程序的标识提供程序 URL （名为实例）和登录受众。 这两个参数统称为颁发机构。
- 如果你只是为你的组织编写一个业务线应用程序（也称为单租户应用程序），则租户 ID。
- 如果是机密客户端应用，则为应用程序密钥（客户端密码字符串）或证书（类型为 X509Certificate2）。
- 对于 web 应用，有时用于公共客户端应用程序（特别是当你的应用程序需要使用代理时），你还将设置 redirectUri，其中标识提供程序将使用安全令牌联系回你的应用程序。

## <a name="configure-the-application-from-the-config-file"></a>从配置文件配置应用程序
MSAL.NET 中选项的属性名称与 ASP.NET Core 中的 `AzureADOptions` 属性的名称相匹配，因此不需要编写任何粘附代码。

*Appsettings*文件中描述了一个 ASP.NET Core 应用程序配置：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

从 MSAL.NET v3. x 开始，你可以从配置文件配置你的机密客户端应用程序。

在要配置和实例化应用程序的类中，需要声明一个 `ConfidentialClientApplicationOptions` 对象。  使用 appconfig [nuget 包](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)中的 `IConfigurationRoot.Bind()` 方法，将源中读取的配置（包括文件）绑定到应用程序选项的实例：

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

这会使*appsettings*文件的 "AzureAD" 部分的内容绑定到 `ConfidentialClientApplicationOptions` 对象的相应属性。  接下来，生成一个 `ConfidentialClientApplication` 对象：

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>添加运行时配置
在机密客户端应用程序中，通常每个用户都有一个缓存。 因此，您将需要获取与用户关联的缓存，并通知应用程序生成器您要使用它。 同样，您可能有一个动态计算的重定向 URI。 在这种情况下，代码如下所示：

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

