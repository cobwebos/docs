---
title: 调用 Web API 的桌面应用（代码配置）- Microsoft 标识平台
description: 了解如何构建调用 Web API 的桌面应用（应用的代码配置）
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 600b6db1eb3d3b422d62e49c5bc816a1a56370f9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798514"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>调用 Web API 的桌面应用 - 代码配置

创建应用程序以后，即可了解如何使用应用程序的坐标来配置代码。

## <a name="msal-libraries"></a>MSAL 库

目前，支持桌面应用程序的唯一 MSAL 库是 MSAL.NET

## <a name="public-client-application"></a>公共客户端应用程序

从代码角度来看，桌面应用程序是公共客户端应用程序，因此需构建并操作 MSAL.NET `IPublicClientApplication`。 同样，是否使用交互式身份验证会存在一些差异。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>以独占方式通过代码来完成

下面的代码实例化的公共客户端应用程序中，在 Microsoft Azure 公有云，与工作和学校帐户或个人 Microsoft 帐户登录用户。

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果你想要使用交互式身份验证或设备代码流，如上图所示，你想要使用`.WithRedirectUri`修饰符：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>使用配置文件

以下代码实例化配置对象中的公共客户端应用程序，该对象可以通过编程方式进行填充，也可以从配置文件读取。

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更详细的配置

可以通过添加多个修饰符来详细阐述应用程序的构建。 例如，如果你希望应用程序是国家/地区云 （此处美国政府版） 中的多租户应用程序，您可以编写：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 也包含 ADFS 2019 的修饰符：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最后，如果需要获取 Azure AD B2C 租户的令牌，可以指定租户，如以下代码片段所示：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>了解详细信息

若要详细了解如何配置 MSAL.NET 桌面应用程序，请执行以下操作：

- 如需 `PublicClientApplicationBuilder` 上提供的所有修饰符的列表，请参阅参考文档 [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 如需 `PublicClientApplicationOptions` 中公开的所有选项的说明，请参阅参考文档中的 [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)

## <a name="complete-example-with-configuration-options"></a>包含配置选项的完整示例

假设有一个 .NET Core 控制台应用程序，其中包含以下 `appsettings.json` 配置文件：

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

你没有什么代码来使用 .NET 提供的配置框架读取此文件；

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

现在，若要创建应用程序，需编写以下代码：

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在调用 `.Build()` 方法之前，可以使用对 `.WithXXX` 方法的调用来重写配置，如前所示。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取桌面应用的令牌](scenario-desktop-acquire-token.md)
