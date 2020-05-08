---
title: 配置调用 Web API 的桌面应用 - Microsoft 标识平台 | Azure
description: 了解如何配置调用 Web API 的桌面应用的代码
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d07add7950da531330fe9f64629299cef9fad1ac
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734580"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>调用 Web API 的桌面应用：代码配置

创建应用程序以后，即可了解如何使用应用程序的坐标来配置代码。

## <a name="microsoft-authentication-libraries"></a>Microsoft 身份验证库

以下 Microsoft 身份验证库 (MSAL) 支持桌面应用程序。

  Microsoft 身份验证库 | 说明
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支持在多个平台中（例如 Linux、Windows 和 macOS）构建桌面应用程序。
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 支持在多个平台中构建桌面应用程序。
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 支持在多个平台中构建桌面应用程序。
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | 仅支持在 macOS 上运行的桌面应用程序。

## <a name="public-client-application"></a>公共客户端应用程序

从代码的角度看，桌面应用程序是公共客户端应用程序。 根据是否使用交互式身份验证，配置将略有不同。

# <a name="net"></a>[.NET](#tab/dotnet)

需要生成并操作 MSAL.NET `IPublicClientApplication`。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>以独占方式通过代码来完成

下面的代码实例化公用客户端应用程序，并使用工作或学校帐户或个人 Microsoft 帐户 Microsoft Azure 公有云中的用户登录。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果打算使用交互式身份验证或设备代码流，如上所示，请使用 `.WithRedirectUri` 修饰符。

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>使用配置文件

以下代码通过一个配置对象实例化公共客户端应用程序，该对象可以通过编程方式进行填充，也可以从配置文件读取。

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更详细的配置

可以通过添加多个修饰符来详细阐述应用程序的构建。 例如，如果希望应用程序成为国家/地区云（例如此处显示的美国政府）中的多租户应用程序，可以编写：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 还包含 Active Directory 联合身份验证服务 2019 的修饰符：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最后，如果要获取 Azure Active Directory (Azure AD) B2C 租户的令牌，请指定你的租户，如以下代码片段所示：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>了解详细信息

若要详细了解如何配置 MSAL.NET 桌面应用程序，请执行以下操作：

- 如需 `PublicClientApplicationBuilder` 上提供的所有修饰符的列表，请参阅参考文档 [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。
- 如需 `PublicClientApplicationOptions` 中公开的所有选项的说明，请参阅参考文档中的 [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

### <a name="complete-example-with-configuration-options"></a>包含配置选项的完整示例

假设有一个 .NET Core 控制台应用程序，其中包含以下 `appsettings.json` 配置文件：

```json
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

使用 .NET 提供的配置框架时，此文件中只有少量代码可以读取：

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

现在，若要创建你的应用程序，请编写以下代码：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在调用 `.Build()` 方法之前，可以通过调用 `.WithXXX` 方法来重写配置，如前所示。

# <a name="java"></a>[Java](#tab/java)

下面是 MSAL Java 开发示例中用于配置示例的类： [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

下面的代码实例化公用客户端应用程序，并使用工作或学校帐户或个人 Microsoft 帐户 Microsoft Azure 公有云中的用户登录。

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

可以通过添加多个修饰符来详细阐述应用程序的构建。 例如，如果希望应用程序成为国家/地区云（例如此处显示的美国政府）中的多租户应用程序，可以编写：

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
> [获取桌面应用的令牌](scenario-desktop-acquire-token.md)
