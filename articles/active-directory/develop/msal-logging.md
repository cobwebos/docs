---
title: Microsoft 身份验证库（MSAL）应用程序中的日志记录 |Microsoft
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
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3235037d2b60322ab3e5c393c0a19b1a42bdc6c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678035"
---
# <a name="logging-in-msal-applications"></a>MSAL 应用程序中的日志记录

Microsoft 身份验证库（MSAL）应用程序生成有助于诊断问题的日志消息。 应用可以通过数行代码配置日志记录，并可对详细程度以及是否记录个人和组织数据进行自定义控制。 建议创建 MSAL 日志记录回拨，并为用户提供在发生身份验证问题时提交日志的方法。

## <a name="logging-levels"></a>日志记录级别

MSAL 提供了多个级别的日志记录详细信息：

- 错误：指示出现问题并已生成错误。 用于调试并确定问题。
- 警告：这并不一定是错误或故障，而是用于诊断和查明问题。
- 信息：MSAL 将要记录的事件可为用户提供信息，不一定用于调试。
- 详细：默认。 MSAL 记录库行为的完整详细信息。

## <a name="personal-and-organizational-data"></a>个人和组织数据

默认情况下，MSAL 记录器不会捕获任何高度敏感的个人或组织数据。 如果你决定这样做，库将提供启用个人和组织数据日志记录的选项。

## <a name="logging-in-msalnet"></a>在 MSAL.NET 中进行日志记录

 > [!NOTE]
 > 有关 MSAL.NET 日志记录的示例，请参阅[MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) ，等等。

在 MSAL 3.x 中，日志记录是在创建应用时使用 `.WithLogging` 生成器修饰符按应用程序设置的。 该方法采用以下可选参数：

- `Level` 使你可以确定所需的日志记录级别。 将其设置为“Errors”时，就只会获得错误
- 如果设置为 true，则 `PiiLoggingEnabled` 使你可以记录个人和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。
- `LogCallback` 设置为执行日志记录的委托。 如果 @no__t 为 true，则此方法将接收两次消息：一次是使用 `containsPii` 参数等于 false，不包含个人数据，第二次使用 `containsPii` 参数等于 true，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。
- @no__t 为平台启用默认日志记录。 默认为 false。 如果将它设置为 true，它会在桌面/UWP 应用程序中使用事件跟踪，在 iOS 上使用 NSLog，在 Android 上使用 logcat。

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

## <a name="logging-in-msal-for-android-using-java"></a>使用 Java 登录 MSAL for Android

通过创建日志记录回拨，在创建应用程序时启用日志记录。 回调采用以下参数：

- `tag` 是由库传递到回调的字符串。 它与日志条目相关联，并且可用于对日志记录消息进行排序。
- `logLevel` 使你可以确定所需的日志记录级别。 支持的日志级别为： `Error`、`Warning`、`Info` 和 @no__t 为3。
- @no__t 为日志条目的内容。
- @no__t 指定是否记录包含个人数据或组织数据的消息。 默认情况下，此设置为 "false"，以便您的应用程序不记录个人数据。 如果 `containsPII` @no__t 为-1，则此方法将接收两次消息：一次：将 `containsPII` 参数设置为 `false` 和不包含个人数据的 `message` 参数，第二次将 @no__t 参数设置为 `true`，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

默认情况下，MSAL 记录器将不会捕获任何个人身份信息或组织身份信息。
启用个人身份信息或组织身份信息的日志记录：

```java
Logger.getInstance().setEnablePII(true);
```

禁用日志记录个人数据和组织数据：

```java
Logger.getInstance().setEnablePII(false);
```

默认情况下，将禁用日志记录到 logcat。 若要启用： 
```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="logging-in-msaljs"></a>MSAL.js 中的日志记录

 在配置期间通过传递记录器对象来启用 MSAL 中的日志记录，以创建 @no__t 的实例。 此记录器对象具有以下属性：

- `localCallback`：一个回调实例，开发人员可以提供该实例以通过自定义方式使用和发布日志。 根据所需要的重定向日志的方式，实现 localCallback 方法。
- `level`（可选）：可配置的日志级别。 支持的日志级别为： `Error`、`Warning`、`Info` 和 @no__t 为3。 默认值为 `Info`。
- `piiLoggingEnabled` （可选）：如果设置为 true，则记录个人和组织数据。 默认情况下，此值为 false，以便您的应用程序不记录个人数据。 个人数据日志不会写入到默认的输出（例如控制台、Logcat 或 NSLog）中。
- `correlationId`（可选）：一个唯一标识符，用于将请求映射到用于调试目的的响应。 默认为 RFC4122 版本 4 guid（128 位）。

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “<Enter your client id>”,
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

## <a name="logging-in-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL 中的日志记录

设置一个回调来捕获 MSAL 日志记录，并将其合并到你自己的应用程序的日志记录中。 回调的签名如下所示：

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

例如：

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

Swift
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>个人身份信息（PII）

默认情况下，MSAL 不会捕获或记录任何 PII。 库允许应用程序开发人员通过 MSALLogger 类中的属性来启用此功能。 启用 PII 后，应用会负责安全地处理高度敏感的数据，并遵循法规要求。

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>日志记录级别

若要在使用 MSAL for iOS 和 macOS 进行记录时设置日志记录级别，请使用以下值之一：

|Level  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日志记录 |
| `MSALLogLevelError` | 默认级别，仅在发生错误时输出信息 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  库入口点，其中包含参数和各种密钥链操作 |
|`MSALLogLevelVerbose`     |  API 跟踪       |

例如：

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>日志消息格式

MSAL 日志消息的消息部分的格式为`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相关 Id 和时间戳有助于跟踪问题。 时间戳和相关 ID 信息在日志消息中可用。 唯一要检索它们的位置是从 MSAL 记录消息。
