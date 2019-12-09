---
title: 实例化公用客户端应用（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用用于 .NET 的 Microsoft 身份验证库（MSAL.NET）的配置选项实例化公用客户端应用程序。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4052c67b019b5ca6d3936e34ec8a5b2b1aff89b1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915841"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>使用 MSAL.NET 的配置选项实例化公用客户端应用程序

本文介绍如何使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）实例化[公用客户端应用程序](msal-client-applications.md)。  使用在设置文件中定义的配置选项对应用程序进行实例化。

在初始化应用程序之前，首先需要[注册](quickstart-register-app.md)它，以便您的应用程序可以与 Microsoft 标识平台集成。 注册后，你可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID （表示 GUID 的字符串）
- 应用程序的标识提供程序 URL （名为实例）和登录受众。 这两个参数统称为颁发机构。
- 如果你只是为你的组织编写一个业务线应用程序（也称为单租户应用程序），则租户 ID。
- 对于 web 应用，有时用于公共客户端应用程序（特别是当你的应用程序需要使用代理时），你还将设置 redirectUri，其中标识提供程序将使用安全令牌联系回你的应用程序。


.NET Core 控制台应用程序可以具有以下*appsettings*配置文件：

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

以下代码使用 .NET 配置框架读取此文件：

```csharp
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
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

下面的代码使用设置文件中的配置创建应用程序：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

