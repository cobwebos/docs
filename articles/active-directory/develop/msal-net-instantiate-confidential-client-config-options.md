---
title: 使用选项（适用于 .NET 的 Microsoft 身份验证库）实例化机密客户端应用 | Azure
description: 了解如何通过适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 使用配置选项实例化机密客户端应用程序。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a05959311b7f62f88a7b474b907982e005b98b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532631"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>通过 MSAL.NET 使用配置选项实例化机密客户端应用程序

本文介绍如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 实例化[机密客户端应用程序](msal-client-applications.md)。  应用程序使用设置文件中定义的配置选项进行实例化。

在初始化应用程序之前，首先需要[注册](quickstart-register-app.md)它，以便应用可以与 Microsoft 标识平台集成。 注册后，可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID（表示 GUID 的字符串）
- 标识提供者 URL（为实例命名）和应用程序的登录受众。 这两个参数统称为颁发机构。
- 租户 ID：如果你编写的业务线应用程序（也称为单租户应用程序）专用于自己的组织。
- 应用程序机密（客户端机密字符串）；对于机密客户端应用，需要获取证书（类型为 X509Certificate2）。
- 对于 Web 应用，有时对于公共客户端应用（特别是当你的应用需要使用中转站时），还将需要设置 redirectUri，标识提供者将在其中使用安全令牌联系你的应用程序。

## <a name="configure-the-application-from-the-config-file"></a>根据配置文件配置应用程序
MSAL.NET 中选项的属性名称与 ASP.NET Core 中 `AzureADOptions` 的属性名称匹配，因此不需编写任何粘附代码。

*appsettings.json* 文件中介绍了 ASP.NET Core 应用程序配置：

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

从 MSAL.NET v3.x 开始，可以根据配置文件配置机密客户端应用程序。

在需要配置和实例化应用程序的类中，需声明 `ConfidentialClientApplicationOptions` 对象。  使用 appconfig `IConfigurationRoot.Bind()` [nuget 包](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)中的方法将从源 (包括文件) 读取的配置绑定到应用程序选项的实例:

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

这样就可以将 *appsettings.json* 文件“AzureAD”节的内容绑定到 `ConfidentialClientApplicationOptions` 对象的相应属性。  接下来，构建 `ConfidentialClientApplication` 对象：

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>添加运行时配置
在机密客户端应用程序中，通常会为每个用户设置一个缓存。 因此，需将缓存关联到用户，并告知应用程序生成器你需要使用它。 同样，你可以有一个动态计算的重定向 URI。 在此示例中，代码如下：

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

