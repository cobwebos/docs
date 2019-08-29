---
title: 在 MSAL 应用程序中进行日志记录 | Azure
description: 了解如何在 Microsoft 身份验证库 (MSAL) 应用程序中进行日志记录。
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
ms.date: 04/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c093375ba2dc5c851a2deb35bdea28338ee982
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135771"
---
# <a name="logging"></a>日志记录
用于生成日志消息的 Microsoft 身份验证库 (MSAL) 应用，这些消息可以用来诊断问题并提供详细信息。 应用可以通过数行代码配置日志记录，并可对详细程度以及是否记录个人和组织数据进行自定义控制。 建议设置 MSAL 日志记录回调，并提供一种允许用户在遇到身份验证问题时提交日志的方法。

## <a name="logging-levels"></a>日志记录级别

MSAL 的记录器允许捕获多个级别的详细信息：

- 错误：指示出现问题并已生成错误。 用于调试并确定问题。
- 警告：出问题的事件，应用需要该事件的更多信息。 不一定会出现错误或故障，只是为了诊断和指出问题。
- 信息：MSAL 将要记录的事件可为用户提供信息，不一定用于调试。
- 详细：默认。 MSAL 会记录大量的信息，让用户全面且详细地了解库的具体行为。

## <a name="personal-and-organizational-data"></a>个人和组织数据
默认情况下，MSAL 记录器不捕获任何高度敏感的个人或组织数据。 该库提供相关选项，允许你自行决定是否记录个人和组织数据。

## <a name="logging-in-msalnet"></a>在 MSAL.NET 中进行日志记录

 > [!NOTE]
 > 有关 MSAL.NET 的详细信息, 请查看[MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)。 获取 MSAL.NET 日志记录的示例。 
 
在 MSAL 3.x 中，日志记录是在创建应用时使用 `.WithLogging` 生成器修饰符按应用程序设置的。 该方法采用以下可选参数：

- *Level*：用于确定你需要哪种级别的日志记录。 将其设置为“Errors”时，就只会获得错误
- *PiiLoggingEnabled* 在设置为 true 的情况下可以记录个人和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。
- *LogCallback* 设置为一个执行日志记录的委托。 如果 *PiiLoggingEnabled* 为 true，则此方法会接收消息两次：第一次时 *containsPii* 参数为 false，消息没有个人数据；第二次时 *containsPii* 参数为 true，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。
- *DefaultLoggingEnabled* 为平台启用默认日志记录。 默认为 false。 如果将它设置为 true，它会在桌面/UWP 应用程序中使用事件跟踪，在 iOS 上使用 NSLog，在 Android 上使用 logcat。

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>MSAL.js 中的日志记录

 可以在 MSAL.js 中启用日志记录，方法是在配置过程中传递一个记录器对象，以便创建 `UserAgentApplication` 实例。 此记录器对象具有以下属性：

- *localCallback*：一个回调实例，可以由开发人员提供，目的是以自定义方式使用并发布日志。 根据所需要的重定向日志的方式，实现 localCallback 方法。

- *level*（可选）：可配置的日志级别。 支持的日志级别为：错误、警告、信息、详细。 默认值为“信息”。

- *piiLoggingEnabled*（可选）：在设置为 true 的情况下可以记录个人和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。 个人数据日志不会写入到默认的输出（例如控制台、Logcat 或 NSLog）中。 默认设置为 false。

- *correlationId*（可选）： 唯一标识符，用于映射请求与响应，以便进行调试。 默认为 RFC4122 版本 4 guid（128 位）。

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
