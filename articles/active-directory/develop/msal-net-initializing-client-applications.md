---
title: 初始化客户端应用程序（适用于 .NET 的 Microsoft 身份验证库）| Azure
description: 了解如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 初始化公共客户端和机密客户端应用程序。
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
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5012da8f2ff41971df674fd35162fe14e1de8fc9
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532639"
---
# <a name="initialize-client-applications-using-msalnet"></a>使用 MSAL.NET 初始化客户端应用程序
本文介绍如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 初始化公共客户端和机密客户端应用程序。  若要详细了解客户端应用程序类型和应用程序配置选项，请阅读[概述](msal-client-applications.md)。

使用 MSAL.NET 3.x 实例化应用程序的建议方式是使用应用程序生成器 `PublicClientApplicationBuilder` 和 `ConfidentialClientApplicationBuilder`。 这些生成器提供强大的机制用于通过代码、配置文件甚至两者的混合来配置应用程序。

## <a name="prerequisites"></a>先决条件
在初始化应用程序之前，首先需要[将其注册](quickstart-register-app.md)，使应用能够与 Microsoft 标识平台集成。  注册后，可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID（表示 GUID 的字符串）
- 标识提供者 URL（为实例命名）和应用程序的登录受众。 这两个参数统称为颁发机构。
- 租户 ID：如果你编写的业务线应用程序（也称为单租户应用程序）专用于自己的组织。
- 应用程序机密（客户端机密字符串）；对于机密客户端应用，需要获取证书（类型为 X509Certificate2）。
- 对于 Web 应用或者公共客户端应用（特别是当你的应用需要使用中转站时），还将需要设置 redirectUri，标识提供者将在其中使用安全令牌联系你的应用程序。

## <a name="ways-to-initialize-applications"></a>初始化应用程序的方式
可通过多种不同的方式来实例化客户端应用程序。

### <a name="initializing-a-public-client-application-from-code"></a>通过代码初始化公共客户端应用程序

下面的代码实例化公用客户端应用程序、在 Microsoft Azure 公有云中登录用户, 以及他们的工作和学校帐户或个人 Microsoft 帐户。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>通过代码初始化机密客户端应用程序

同样, 以下代码将实例化机密应用程序 (位于`https://myapp.azurewebsites.net`中的 Web 应用), 用于处理来自 Microsoft Azure 公有云中用户的令牌、其工作和学校帐户, 或者其个人 Microsoft 帐户。 标识提供者通过共享客户端机密标识该应用程序：

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

你可能已知道，在生产环境中，最好是与 Azure AD 共享证书，而不要使用客户端机密。 根据此要求，代码如下所示：

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>通过配置选项初始化公共客户端应用程序

以下代码通过一个配置对象实例化公共客户端应用程序，该对象可以通过编程方式进行填充，也可以从配置文件读取：

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>通过配置选项初始化机密客户端应用程序

可对机密客户端应用程序应用上述相同的模式。 还可以使用 `.WithXXX` 修饰符（此处为证书）添加其他参数。

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>生成器修饰符

在使用应用程序生成器的代码片段中，可将许多 `.With` 方法应用为修饰符（例如 `.WithCertificate` 和 `.WithRedirectUri`）。 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>公共和机密客户端应用程序通用的修饰符

可在公共客户端或机密客户端应用程序生成器中设置的修饰符包括：

|参数 | 描述|
|--------- | --------- |
|`.WithAuthority()` 7 个重写 | 将应用程序默认颁发机构设置为 Azure AD 颁发机构，有时还可以选择 Azure 云、受众、租户（租户 ID 或域名），或直接提供颁发机构 URI。|
|`.WithAdfsAuthority(string)` | 将应用程序默认颁发机构设置为 ADFS 颁发机构。|
|`.WithB2CAuthority(string)` | 将应用程序默认颁发机构设置为 Azure AD B2C 颁发机构。|
|`.WithClientId(string)` | 重写客户端 ID。|
|`.WithComponent(string)` | 使用 MSAL.NET 设置库的名称（出于遥测原因）。 |
|`.WithDebugLoggingCallback()` | 调用后，应用程序将调用 `Debug.Write`，目的只是启用调试跟踪。 有关详细信息，请参阅[日志记录](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 设置要在所有身份验证请求中发送的应用程序级附加查询参数。 可在每个令牌获取方法级别重写此值（具有相同的 `.WithExtraQueryParameters pattern`）。|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | 启用高级方案（例如对 HTTP 代理进行配置），或者强制 MSAL 使用特定的 HttpClient（例如，在 ASP.NET Core Web 应用/API 中）。|
|`.WithLogging()` | 调用后，应用程序将结合调试跟踪调用某个回调。 有关详细信息，请参阅[日志记录](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithRedirectUri(string redirectUri)` | 重写默认的重定向 URI。 此操作对于公共客户端应用程序中涉及代理的方案非常有用。|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 设置用于发送遥测数据的委托。|
|`.WithTenantId(string tenantId)` | 重写租户 ID 或租户说明。|

### <a name="modifiers-specific-to-xamarinios-applications"></a>特定于 Xamarin.iOS 应用程序的修饰符

可在 Xamarin.iOS 上的公共客户端应用程序生成器中设置的修饰符包括：

|参数 | 描述|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **仅限 Xamarin.iOS**：设置 iOS 密钥链安全组（为实现缓存持久性）。|

### <a name="modifiers-specific-to-confidential-client-applications"></a>特定于机密客户端应用程序的修饰符

可在机密客户端应用程序生成器中设置的修饰符包括：

|参数 | 描述|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | 设置用于在 Azure AD 中识别应用程序的证书。|
|`.WithClientSecret(string clientSecret)` | 设置用于在 Azure AD 中识别应用程序的客户端机密（应用密码）。|

这些修饰符是互斥的。 如果同时提供两者，MSAL 会引发有含义的异常。

### <a name="example-of-usage-of-modifiers"></a>修饰符用法示例

假设你的应用程序是一个仅供你的组织使用的业务线应用程序。  那么，可编写以下代码：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

有趣的是, 针对国家云的编程现在已简化。 如果你希望你的应用程序成为全国云中的多租户应用程序, 可以编写, 例如:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS 也有一个重写（目前不支持 ADFS 2019）：
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最后，如果你是 Azure AD B2C 开发人员，可按如下所示指定租户：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
