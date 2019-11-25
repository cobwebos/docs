---
title: Logging in Microsoft Authentication Library (MSAL) applications
titleSuffix: Microsoft identity platform
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
ms.date: 11/11/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5960389389e4b75794a7334c0bff12ce3ac0f170
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452473"
---
# <a name="logging-in-msal-applications"></a>Logging in MSAL applications

Microsoft Authentication Library (MSAL) apps generate log messages that can help diagnose issues. 应用可以通过数行代码配置日志记录，并可对详细程度以及是否记录个人和组织数据进行自定义控制。 We recommend you create an MSAL logging callback and provide a way for users to submit logs when they have authentication issues.

## <a name="logging-levels"></a>日志记录级别

MSAL provides several levels of logging detail:

- Error: Indicates something has gone wrong and an error was generated. 用于调试并确定问题。
- Warning: There hasn't necessarily been an error or failure, but are intended for diagnostics and pinpointing problems.
- Info: MSAL will log events intended for informational purposes not necessarily intended for debugging.
- Verbose: Default. MSAL logs the full details of library behavior.

## <a name="personal-and-organizational-data"></a>个人和组织数据

By default, the MSAL logger doesn't capture any highly sensitive personal or organizational data. The library provides the option to enable logging personal and organizational data if you decide to do so.

For details about MSAL logging in a particular language, choose the tab matching your language:

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>在 MSAL.NET 中进行日志记录

 > [!NOTE]
 > See the [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) for samples of MSAL.NET logging and more.

在 MSAL 3.x 中，日志记录是在创建应用时使用 `.WithLogging` 生成器修饰符按应用程序设置的。 该方法采用以下可选参数：

- `Level` enables you to decide which level of logging you want. 将其设置为“Errors”时，就只会获得错误
- `PiiLoggingEnabled` enables you to log personal and organizational data if set to true. 默认情况下，此项设置为 false，不允许应用程序记录个人数据。
- `LogCallback` is set to a delegate that does the logging. If `PiiLoggingEnabled` is true, this method will receive the messages twice: once with the `containsPii` parameter equals false and the message without personal data, and a second time with the `containsPii` parameter equals to true and the message might contain personal data. 在某些情况下（消息不含个人数据），消息是相同的。
- `DefaultLoggingEnabled` enables the default logging for the platform. 默认为 false。 如果将它设置为 true，它会在桌面/UWP 应用程序中使用事件跟踪，在 iOS 上使用 NSLog，在 Android 上使用 logcat。

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

## <a name="logging-in-msal-for-android-using-java"></a>Logging in MSAL for Android using Java

Turn logging on at app creation by creating a logging callback. The callback takes these parameters:

- `tag` is a string passed to the callback by the library. It is associated with the log entry and can be used to sort logging messages.
- `logLevel` enables you to decide which level of logging you want. The supported log levels are: `Error`, `Warning`, `Info`, and `Verbose`.
- `message` is the content of the log entry.
- `containsPII` specifies whether messages containing personal data, or organizational data are logged. By default, this is set to false, so that your application doesn't log personal data. If `containsPII` is `true`, this method will receive the messages twice: once with the `containsPII` parameter set to `false` and the `message` without personal data, and a second time with the `containsPii` parameter set to `true` and the message might contain personal data. 在某些情况下（消息不含个人数据），消息是相同的。

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

By default, the MSAL logger will not capture any personal identifiable information or organizational identifiable information.
To enable the logging of personal identifiable information or organizational identifiable information:

```java
Logger.getInstance().setEnablePII(true);
```

To disable logging personal data and organization data:

```java
Logger.getInstance().setEnablePII(false);
```

By default logging to logcat is disabled. To enable:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

 Enable logging in MSAL.js (JavaScript) by passing a logger object during the configuration for creating a `UserAgentApplication` instance. 此记录器对象具有以下属性：

- `localCallback`: a Callback instance that can be provided by the developer to consume and publish logs in a custom manner. 根据所需要的重定向日志的方式，实现 localCallback 方法。
- `level` (optional): the configurable log level. The supported log levels are: `Error`, `Warning`, `Info`, and `Verbose`. 默认为 `Info`。
- `piiLoggingEnabled` (optional): if set to true, logs personal and organizational data. By default this is false so that your application doesn't log personal data. 个人数据日志不会写入到默认的输出（例如控制台、Logcat 或 NSLog）中。
- `correlationId` (optional): a unique identifier, used to map the request with the response for debugging purposes. 默认为 RFC4122 版本 4 guid（128 位）。

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

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL for iOS and macOS logging-ObjC

Set a callback to capture MSAL logging and incorporate it in your own application's logging. The signature for the callback looks like this:

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

### <a name="personal-data"></a>Personal data

By default, MSAL doesn't capture or log any personal data (PII). The library allows app developers to turn this on through a property in the MSALLogger class. By turning on `pii.Enabled`, the app takes responsibility for safely handling highly sensitive data and following regulatory requirements.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>日志记录级别

To set the logging level when you log using MSAL for iOS and macOS, use one of the following values:

|级别  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| Disable all logging |
| `MSALLogLevelError` | Default level, prints out information only when errors occur |
| `MSALLogLevelWarning` | Warnings |
| `MSALLogLevelInfo` |  Library entry points, with parameters and various keychain operations |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Log message format

The message portion of MSAL log messages is in the format of `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Providing correlation IDs and timestamps are helpful for tracking down issues. Timestamp and correlation ID information is available in the log message. The only reliable place to retrieve them is from MSAL logging messages.

## <a name="swifttabswift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL for iOS and macOS logging-Swift

Set a callback to capture MSAL logging and incorporate it in your own application's logging. The signature (represented in Objective-C) for the callback looks like this:

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

### <a name="personal-data"></a>Personal data

By default, MSAL doesn't capture or log any personal data (PII). The library allows app developers to turn this on through a property in the MSALLogger class. By turning on `pii.Enabled`, the app takes responsibility for safely handling highly sensitive data and following regulatory requirements.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>日志记录级别

To set the logging level when you log using MSAL for iOS and macOS, use one of the following values:

|级别  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| Disable all logging |
| `MSALLogLevelError` | Default level, prints out information only when errors occur |
| `MSALLogLevelWarning` | Warnings |
| `MSALLogLevelInfo` |  Library entry points, with parameters and various keychain operations |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Log message format

The message portion of MSAL log messages is in the format of `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Providing correlation IDs and timestamps are helpful for tracking down issues. Timestamp and correlation ID information is available in the log message. The only reliable place to retrieve them is from MSAL logging messages.

## <a name="javatabjava"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL for Java logging

MSAL for Java allows you to use the logging library that you are already using with your app, as long as it is compatible with SLF4J. MSAL for Java uses the [Simple Logging Facade for Java](http://www.slf4j.org/) (SLF4J) as a simple facade or abstraction for various logging frameworks, such as [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) and [Log4j](https://logging.apache.org/log4j/2.x/). SLF4J allows the user to plug in the desired logging framework at deployment time.

For example, to use Logback as the logging framework in your application, add the Logback dependency to the Maven pom file for your application:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Then add the Logback configuration file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J automatically binds to Logback at deployment time. MSAL logs will be written to the console.

For instructions on how to bind to other logging frameworks, see the [SLF4J manual](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Personal and organization information

By default, MSAL logging does not capture or log any personal or organizational data. In the following example, logging personal or organizational data is off by default:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Turn on personal and organizational data logging by setting `logPii()` on the client application builder. If you turn on personal or organizational data logging, your app must take responsibility for safely handling highly-sensitive data and complying with any regulatory requirements.

In the following example, logging personal or organizational data is enabled:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="pythontabpython"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL for Python logging

Logging in MSAL Python uses the standard Python logging mechanism, for example `logging.info("msg")` You can configure MSAL logging as follows (and see it in action in the [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Enable debug logging for all modules

By default, the logging in any Python script is turned off. If you want to enable debug logging for all of the modules in your entire Python script, use:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Silence only MSAL logging

To silence only MSAL library logging, while enabling debug logging in all of the other modules in your Python script, turn off the logger used by MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Personal and organizational data in Python

MSAL for Python does not log personal data or organizational data. There is no property to turn personal or organization data logging on or off.

You can use standard Python logging to log whatever you want, but you are responsible for safely handling sensitive data and following regulatory requirements.

For more information about logging in Python, please refer to Python's  [Logging HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
