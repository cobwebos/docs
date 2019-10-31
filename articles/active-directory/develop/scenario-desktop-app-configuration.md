---
title: 用于调用 web Api 的桌面应用程序（代码配置）-Microsoft 标识平台
description: 了解如何构建一个可调用 web Api 的桌面应用程序（应用程序的代码配置）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6baf7d21748b5b524745f26302e70612dab29a8d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175441"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>用于调用 web Api 的桌面应用程序-代码配置

现在，你已创建应用程序，你将了解如何在应用程序的坐标中配置代码。

## <a name="msal-libraries"></a>MSAL 库

支持桌面应用程序的 Microsoft 库包括：

  MSAL 库 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支持在多个平台中构建桌面应用程序-Linux、Windows 和 MacOS
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 支持在多个平台中构建桌面应用程序。 正在进行开发-公开预览版
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 支持在多个平台中构建桌面应用程序。 正在进行开发-公开预览版
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | 仅支持在 macOS 上运行的桌面应用程序

## <a name="public-client-application"></a>公共客户端应用程序

从代码角度来看，桌面应用程序是公用客户端应用程序。 根据是否使用交互式身份验证，配置将有所不同。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

需要生成并操作 MSAL.NET `IPublicClientApplication`。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>以独占方式通过代码

下面的代码实例化一个公共客户端应用程序，在 Microsoft Azure 公有云中使用工作和学校帐户或个人 Microsoft 帐户来登录用户。

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果要使用交互式身份验证或设备代码流（如上所示），请使用 `.WithRedirectUri` 修饰符：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>使用配置文件

下面的代码从一个配置对象中实例化一个公共客户端应用程序，该配置对象可以以编程方式填充或从配置文件中读取。

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更详细的配置

你可以通过添加多个修饰符来详细阐述应用程序生成。 例如，如果你希望你的应用程序成为国家/地区（美国政府）的多租户应用程序，你可以编写：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 还包含 ADFS 2019 的修饰符：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最后，如果要获取 Azure AD B2C 租户的令牌，可以指定租户，如以下代码片段所示：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>了解更多

若要详细了解如何配置 MSAL.NET 桌面应用程序：

- 有关 `PublicClientApplicationBuilder`上可用的所有修饰符的列表，请参阅参考文档[PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 有关 `PublicClientApplicationOptions` 中公开的所有选项的说明，请参阅参考文档中的[PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)

### <a name="complete-example-with-configuration-options"></a>带有配置选项的完整示例

假设有一个包含以下 `appsettings.json` 配置文件的 .NET Core 控制台应用程序：

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

你没有用 .NET 提供的配置框架读取此文件的代码，

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

现在，若要创建应用程序，只需编写以下代码：

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在调用 `.Build()` 方法之前，可以通过调用 `.WithXXX` 方法来重写配置，如前文所述。

# <a name="javatabjava"></a>[Java](#tab/java)

下面是 MSAL Java dev 示例中用于配置示例的类： [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java)。

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

下面的代码实例化一个公共客户端应用程序，在 Microsoft Azure 公有云中使用工作和学校帐户或个人 Microsoft 帐户来登录用户。

### <a name="quick-configuration"></a>快速配置

Objective-C：

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift：
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>更详细的配置

你可以通过添加多个修饰符来详细阐述应用程序生成。 例如，如果你希望你的应用程序成为国家/地区（美国政府）的多租户应用程序，你可以编写：

Objective-C：

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift：

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为桌面应用程序获取令牌](scenario-desktop-acquire-token.md)
