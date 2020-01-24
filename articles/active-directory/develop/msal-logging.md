---
title: MSAL 应用中的日志记录 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何在 Microsoft 身份验证库 (MSAL) 应用程序中进行日志记录。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: cba213824cba86b4ea54e852b7e2f1ede83abfea
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695800"
---
# <a name="logging-in-msal-applications"></a>MSAL 应用程序中的日志记录

Microsoft 身份验证库（MSAL）应用程序生成有助于诊断问题的日志消息。 应用可以通过数行代码配置日志记录，并可对详细程度以及是否记录个人和组织数据进行自定义控制。 建议创建 MSAL 日志记录回拨，并为用户提供在发生身份验证问题时提交日志的方法。

## <a name="logging-levels"></a>日志记录级别

MSAL 提供了多个级别的日志记录详细信息：

- 错误：指示出现错误，并且生成了错误。 用于调试并确定问题。
- 警告：不一定存在错误或失败，但用于诊断和查明问题。
- Info： MSAL 将记录用于提供信息的事件，而不一定要用于调试。
- 详细：默认值。 MSAL 记录库行为的完整详细信息。

## <a name="personal-and-organizational-data"></a>个人和组织数据

默认情况下，MSAL 记录器不会捕获任何高度敏感的个人或组织数据。 如果你决定这样做，库将提供启用个人和组织数据日志记录的选项。

有关特定语言的 MSAL 日志记录的详细信息，请选择与你的语言相匹配的选项卡：

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>在 MSAL.NET 中进行日志记录

 > [!NOTE]
 > 有关 MSAL.NET 日志记录的示例，请参阅[MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) ，等等。

在 MSAL 3.x 中，日志记录是在创建应用时使用 `.WithLogging` 生成器修饰符按应用程序设置的。 该方法采用以下可选参数：

- `Level` 使你可以确定所需的日志记录级别。 将其设置为“Errors”时，就只会获得错误
- 如果设置为 true，则 `PiiLoggingEnabled` 允许您记录个人和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。
- `LogCallback` 设置为执行日志记录的委托。 如果 `PiiLoggingEnabled` 为 true，则此方法将接收两次消息：一次是将 `containsPii` 的参数设置为 false，将消息设置为不包含个人数据，第二次使用 `containsPii` 参数等于 true，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。
- `DefaultLoggingEnabled` 为平台启用默认日志记录。 默认为 false。 如果将它设置为 true，它会在桌面/UWP 应用程序中使用事件跟踪，在 iOS 上使用 NSLog，在 Android 上使用 logcat。

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

## <a name="androidtabandroid"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>使用 Java 登录 MSAL for Android

通过创建日志记录回拨，在创建应用程序时启用日志记录。 回调采用以下参数：

- `tag` 是由库传递到回调的字符串。 它与日志条目相关联，并且可用于对日志记录消息进行排序。
- `logLevel` 使你可以确定所需的日志记录级别。 支持的日志级别为： `Error`、`Warning`、`Info`和 `Verbose`。
- `message` 是日志条目的内容。
- `containsPII` 指定是否记录包含个人数据或组织数据的消息。 默认情况下，此设置为 "false"，以便您的应用程序不记录个人数据。 如果 `true``containsPII`，则此方法将接收两次消息：一次：将 `containsPII` 参数设置为 `false`，并将 `message` 参数设置为 `containsPii`，第二次将 `true` 参数设置为，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。

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

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

 通过在用于创建 `UserAgentApplication` 实例的配置期间传递记录器对象，在 MSAL （JavaScript）中启用日志记录。 此记录器对象具有以下属性：

- `localCallback`：开发人员可提供的回调实例，用于以自定义方式使用和发布日志。 根据所需要的重定向日志的方式，实现 localCallback 方法。
- `level` （可选）：可配置的日志级别。 支持的日志级别为： `Error`、`Warning`、`Info`和 `Verbose`。 默认为 `Info`。
- `piiLoggingEnabled` （可选）：如果设置为 true，则记录个人和组织数据。 默认情况下，此值为 false，以便您的应用程序不记录个人数据。 个人数据日志不会写入到默认的输出（例如控制台、Logcat 或 NSLog）中。
- `correlationId` （可选）：一个唯一标识符，用于将请求映射到用于调试目的的响应。 默认为 RFC4122 版本 4 guid（128 位）。

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
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

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL for iOS 和 macOS 日志记录-ObjC

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

### <a name="personal-data"></a>个人数据

默认情况下，MSAL 不会捕获或记录任何个人数据（PII）。 库允许应用程序开发人员通过 MSALLogger 类中的属性来启用此功能。 通过打开 `pii.Enabled`，应用程序负责安全地处理高度敏感的数据和遵守法规要求。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>日志记录级别

若要在使用 MSAL for iOS 和 macOS 进行记录时设置日志记录级别，请使用以下值之一：

|级别  |Description |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日志记录 |
| `MSALLogLevelError` | 默认级别，仅在发生错误时输出信息 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  库入口点，其中包含参数和各种密钥链操作 |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>日志消息格式

MSAL 日志消息的消息部分的格式为 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相关 Id 和时间戳有助于跟踪问题。 时间戳和相关 ID 信息在日志消息中可用。 唯一要检索它们的位置是从 MSAL 记录消息。

## <a name="swifttabswift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL for iOS 和 macOS 日志记录-Swift

设置一个回调来捕获 MSAL 日志记录，并将其合并到你自己的应用程序的日志记录中。 回调的签名（以目标-C 表示）如下所示：

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

### <a name="personal-data"></a>个人数据

默认情况下，MSAL 不会捕获或记录任何个人数据（PII）。 库允许应用程序开发人员通过 MSALLogger 类中的属性来启用此功能。 通过打开 `pii.Enabled`，应用程序负责安全地处理高度敏感的数据和遵守法规要求。

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>日志记录级别

若要在使用 MSAL for iOS 和 macOS 进行记录时设置日志记录级别，请使用以下值之一：

|级别  |Description |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日志记录 |
| `MSALLogLevelError` | 默认级别，仅在发生错误时输出信息 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  库入口点，其中包含参数和各种密钥链操作 |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>日志消息格式

MSAL 日志消息的消息部分的格式为 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相关 Id 和时间戳有助于跟踪问题。 时间戳和相关 ID 信息在日志消息中可用。 唯一要检索它们的位置是从 MSAL 记录消息。

## <a name="javatabjava"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>用于 Java 日志记录的 MSAL

MSAL for Java 允许你使用已在应用中使用的日志记录库，前提是它与 SLF4J 兼容。 MSAL for Java 使用 Java （SLF4J）的[简单日志记录外观](http://www.slf4j.org/)作为各种日志记录框架（如[util](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)、 [Logback](http://logback.qos.ch/)和[Log4j](https://logging.apache.org/log4j/2.x/)）的简单外观或抽象。 SLF4J 允许用户在部署时插入所需的日志记录框架。

例如，若要使用 Logback 作为应用程序中的日志记录框架，请将 Logback 依赖项添加到应用程序的 Maven pom 文件中：

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

然后添加 Logback 配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

在部署时，SLF4J 会自动绑定到 Logback。 MSAL 日志将写入控制台。

有关如何绑定到其他日志记录框架的说明，请参阅[SLF4J 手册](http://www.slf4j.org/manual.html)。

### <a name="personal-and-organization-information"></a>个人和组织信息

默认情况下，MSAL 日志记录不捕获或记录任何个人或组织数据。 在以下示例中，默认情况下，记录个人或组织数据已关闭：

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

通过在客户端应用程序生成器上设置 `logPii()` 来打开个人和组织数据日志记录。 如果你打开个人或组织数据日志记录，你的应用程序必须负责安全地处理高度敏感的数据，并遵守任何法规要求。

在以下示例中，启用了日志记录个人或组织数据：

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="pythontabpython"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>用于 Python 日志记录的 MSAL

MSAL Python 中的日志记录使用标准 Python 日志记录机制，例如 `logging.info("msg")` 可以按如下所示配置 MSAL 日志记录（并在[username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)中的操作中查看）：

### <a name="enable-debug-logging-for-all-modules"></a>为所有模块启用调试日志记录

默认情况下，将关闭任何 Python 脚本中的日志记录。 如果要对整个 Python 脚本中的所有模块启用调试日志记录，请使用：

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>仅静默 MSAL 日志记录

若要无提示 MSAL 库日志记录，同时在 Python 脚本的所有其他模块中启用调试日志记录，请关闭 MSAL Python 使用的记录器：

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python 中的个人和组织数据

用于 Python 的 MSAL 不会记录个人数据或组织数据。 没有用于启用或禁用个人或组织数据记录的属性。

你可以使用标准 Python 日志记录来记录你想要的任何内容，但你有责任安全地处理敏感数据和遵循法规要求。

有关 Python 中的日志记录的详细信息，请参阅 Python 的[日志记录如何](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)。

---
