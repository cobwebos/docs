---
title: 初始化 MSAL.NET 客户端应用程序 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）初始化公共客户端和机密客户端应用程序。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15c0db66fd357ba150af1901a6b50a645fd1ca88
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915863"
---
# <a name="initialize-client-applications-using-msalnet"></a>使用 MSAL.NET 初始化客户端应用程序
本文介绍如何使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）初始化公共客户端应用程序和机密客户端应用程序。  若要了解有关客户端应用程序类型和应用程序配置选项的详细信息，请阅读[概述](msal-client-applications.md)。

对于 MSAL.NET 2.x，推荐的方法是使用应用程序生成器来实例化应用程序： `PublicClientApplicationBuilder` 和 `ConfidentialClientApplicationBuilder`。 它们提供了一种强大的机制，用于通过代码或配置文件配置应用程序，甚至混合使用这两种方法。

## <a name="prerequisites"></a>必备组件
在初始化应用程序之前，首先需要[注册它](quickstart-register-app.md)，以便您的应用程序可以与 Microsoft 标识平台集成。  注册后，你可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID （表示 GUID 的字符串）
- 应用程序的标识提供程序 URL （名为实例）和登录受众。 这两个参数统称为颁发机构。
- 如果你只是为你的组织编写一个业务线应用程序（也称为单租户应用程序），则租户 ID。
- 如果是机密客户端应用，则为应用程序密钥（客户端密码字符串）或证书（类型为 X509Certificate2）。
- 对于 web 应用，有时用于公共客户端应用程序（特别是当你的应用程序需要使用代理时），你还将设置 redirectUri，其中标识提供程序将使用安全令牌联系回你的应用程序。

## <a name="ways-to-initialize-applications"></a>初始化应用程序的方式
可以通过多种不同的方式来实例化客户端应用程序。

### <a name="initializing-a-public-client-application-from-code"></a>从代码初始化公共客户端应用程序

下面的代码实例化公用客户端应用程序、在 Microsoft Azure 公有云中登录用户，以及他们的工作和学校帐户或个人 Microsoft 帐户。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>从代码初始化机密客户端应用程序

同样，以下代码将实例化机密应用程序（位于 `https://myapp.azurewebsites.net`的 Web 应用），该应用程序处理来自 Microsoft Azure 公有云中用户的令牌、其工作和学校帐户，或者其个人 Microsoft 帐户。 应用程序通过标识提供者共享客户端机密进行标识：

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

如您所知，在生产环境中，你可能想要与 Azure AD 证书共享，而不是使用客户端机密。 然后，代码将如下所示：

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>从配置选项初始化公共客户端应用程序

下面的代码从一个配置对象中实例化一个公共客户端应用程序，该配置对象可以以编程方式填充或从配置文件中读取：

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>从配置选项初始化机密客户端应用程序

不同类型的模式适用于机密客户端应用程序。 你还可以使用 `.WithXXX` 修饰符（此处为证书）添加其他参数。

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>生成器修饰符

在使用应用程序生成器的代码段中，可以将许多 `.With` 方法应用为修饰符（例如 `.WithCertificate` 和 `.WithRedirectUri`）。 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>公共和机密客户端应用程序公用的修饰符

可在公共客户端或机密客户端应用程序生成器上设置的修饰符包括：

|修饰符 | 描述|
|--------- | --------- |
|`.WithAuthority()` 7 覆盖 | 为 Azure AD 机构设置应用程序默认授权，可以选择 Azure 云、受众、租户（租户 ID 或域名），也可以直接提供授权机构 URI。|
|`.WithAdfsAuthority(string)` | 将应用程序默认授权设置为 ADFS 颁发机构。|
|`.WithB2CAuthority(string)` | 将应用程序默认授权设置为 Azure AD B2C 颁发机构。|
|`.WithClientId(string)` | 替代客户端 ID。|
|`.WithComponent(string)` | 使用 MSAL.NET 设置库的名称（出于遥测原因）。 |
|`.WithDebugLoggingCallback()` | 如果调用，则应用程序将调用 `Debug.Write` 只启用调试跟踪。 有关详细信息，请参阅[日志记录](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 设置应用程序级额外查询参数，这些参数将在所有身份验证请求中发送。 这是每个令牌采集方法级别（具有相同的 `.WithExtraQueryParameters pattern`）的可重写。|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | 启用高级方案，例如为 HTTP 代理进行配置，或强制 MSAL 使用特定的 HttpClient （例如 ASP.NET Core web apps/Api 中）。|
|`.WithLogging()` | 如果调用，则应用程序将调用带有调试跟踪的回调。 有关详细信息，请参阅[日志记录](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithRedirectUri(string redirectUri)` | 重写默认的重定向 URI。 对于公共客户端应用程序，这对于涉及 broker 的方案非常有用。|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 设置用于发送遥测数据的委托。|
|`.WithTenantId(string tenantId)` | 替代租户 ID 或租户说明。|

### <a name="modifiers-specific-to-xamarinios-applications"></a>特定于 Xamarin iOS 应用程序的修饰符

可在 Xamarin 上的公共客户端应用程序生成器上设置的修饰符如下：

|修饰符 | 描述|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **仅限 Xamarin**：设置 iOS 密钥链安全组（用于缓存持久性）。|

### <a name="modifiers-specific-to-confidential-client-applications"></a>特定于机密客户端应用程序的修饰符

可在机密客户端应用程序生成器上设置的修饰符包括：

|修饰符 | 描述|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | 设置用 Azure AD 标识应用程序的证书。|
|`.WithClientSecret(string clientSecret)` | 设置用于标识具有 Azure AD 的应用程序的客户端密码（应用密码）。|

这些修饰符互相排斥。 如果同时提供这两种方法，MSAL 将引发有意义的异常。

### <a name="example-of-usage-of-modifiers"></a>修饰符的用法示例

假设你的应用程序是业务线应用程序，它只适用于你的组织。  然后，你可以编写：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

有趣的是，针对国家云的编程现在已简化。 如果你希望你的应用程序成为全国云中的多租户应用程序，可以编写，例如：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

还有一个 ADFS 替代（目前不支持 ADFS 2019）：
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最后，如果你是 Azure AD B2C 开发人员，则可以指定你的租户，如下所示：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
