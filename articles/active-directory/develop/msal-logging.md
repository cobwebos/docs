---
title: MSAL 应用中的日志记录 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何在 Microsoft 身份验证库 (MSAL) 应用程序中进行日志记录。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 2d41b48613ef7ba883a6a51b0fa67407fb730719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87846218"
---
# <a name="logging-in-msal-applications"></a>MSAL 应用程序中的日志记录

用于生成日志消息的 Microsoft 身份验证库 (MSAL) 应用，这些消息可以用来诊断问题。 应用可以通过数行代码配置日志记录，并可对详细程度以及是否记录个人和组织数据进行自定义控制。 建议创建 MSAL 日志记录回调，并提供一种方式来让用户在遇到身份验证问题时提交日志。

## <a name="logging-levels"></a>日志记录级别

MSAL 提供多个日志记录详细级别：

- 错误：指示出现问题并已生成错误。 用于调试并确定问题。
- 警告：不一定会出现错误或故障，只是为了诊断和指出问题。
- 信息：MSAL 将要记录的事件可为用户提供信息，不一定用于调试。
- 详细：默认。 MSAL 将记录库行为的完整详细信息。

## <a name="personal-and-organizational-data"></a>个人和组织数据

默认情况下，MSAL 记录器不捕获任何高度敏感的个人或组织数据。 该库提供相关选项，允许你自行决定是否记录个人和组织数据。

若要详细了解特定语言的 MSAL 日志记录，请选择与你的语言匹配的选项卡：

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>在 MSAL.NET 中进行日志记录

 > [!NOTE]
 > 有关 MSAL.NET 日志记录的示例和其他信息，请参阅 [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)。

在 MSAL 3.x 中，日志记录是在创建应用时使用 `.WithLogging` 生成器修饰符按应用程序设置的。 该方法采用以下可选参数：

- `Level` 用于确定你需要哪种级别的日志记录。 将其设置为“Errors”时，就只会获得错误
- `PiiLoggingEnabled` 在设置为 true 的情况下可以记录个人和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。
- `LogCallback` 设置为一个执行日志记录的委托。 如果 `PiiLoggingEnabled` 为 true，则此方法会接收消息两次：第一次时 `containsPii` 参数为 false，消息没有个人数据；第二次时 `containsPii` 参数为 true，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。
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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>使用 Java 的 MSAL for Android 中的日志记录

创建应用时通过创建日志记录回调来启用日志记录。 该回调采用以下参数：

- `tag` 由库传递给回调的字符串。 它与日志条目相关联，可用于对日志记录消息排序。
- `logLevel` 用于确定所需的日志记录级别。 支持的日志级别为：`Error`、`Warning`、`Info` 和 `Verbose`。
- `message` 是日志条目的内容。
- `containsPII` 指定是要记录包含个人数据还是组织数据的消息。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。 如果 `containsPII` 为 `true`，则此方法会接收消息两次：第一次时 `containsPII` 参数设置为 `false`，`message` 不包含个人数据；第二次时 `containsPii` 参数设置为 `true`，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。

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
若要记录个人身份信息或组织身份信息：

```java
Logger.getInstance().setEnablePII(true);
```

若要禁止记录个人数据和组织数据：

```java
Logger.getInstance().setEnablePII(false);
```

默认情况下，将禁用记录到 logcat。 若要启用：

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

 可以在 MSAL.js (JavaScript) 中启用日志记录，方法是在配置过程中传递一个记录器对象，以便创建 `UserAgentApplication` 实例。 此记录器对象具有以下属性：

- `localCallback`：一个回调实例，可以由开发人员提供，目的是以自定义方式使用并发布日志。 根据所需要的重定向日志的方式，实现 localCallback 方法。
- `level`（可选）：可配置的日志级别。 支持的日志级别为：`Error`、`Warning`、`Info` 和 `Verbose`。 默认为 `Info`。
- `piiLoggingEnabled`（可选）：如果设置为 true，则记录个人数据和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。 个人数据日志不会写入到默认的输出（例如控制台、Logcat 或 NSLog）中。
- `correlationId`（可选）：唯一标识符，用于映射请求与响应，以便进行调试。 默认为 RFC4122 版本 4 guid（128 位）。

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

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>适用于 iOS 和 macOS 的 MSAL 日志记录 - ObjC

设置一个回调来捕获 MSAL 日志记录，并将其合并到自己应用程序的日志记录中。 回调的签名如下所示：

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

默认情况下，MSAL 不会捕获或记录任何个人数据 (PII)。 库允许应用开发人员通过 MSALLogger 类中的某个属性启用该功能。 启用 `pii.Enabled` 日志记录后，应用负责安全地处理高度敏感的数据并遵守任何法规要求。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>日志记录级别

若要在使用适用于 iOS 和 macOS 的 MSAL 进行日志记录时设置日志记录级别，请使用以下值之一：

|Level  |说明 |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日志记录 |
| `MSALLogLevelError` | 默认级别，仅在发生错误时输出信息 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  库入口点，包含参数和各种密钥链操作 |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>日志消息格式

MSAL 日志消息的消息部分采用 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message` 格式

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供关联 ID 和时间戳有助于跟踪问题。 时间戳和关联 ID 信息在日志消息中提供。 只能从 MSAL 日志记录消息中可靠地检索这些信息。

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>适用于 iOS 和 macOS 的 MSAL 日志记录 - Swift

设置一个回调来捕获 MSAL 日志记录，并将其合并到自己应用程序的日志记录中。 回调的签名（以 Objective-C 表示）如下所示：

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

默认情况下，MSAL 不会捕获或记录任何个人数据 (PII)。 库允许应用开发人员通过 MSALLogger 类中的某个属性启用该功能。 启用 `pii.Enabled` 日志记录后，应用负责安全地处理高度敏感的数据并遵守任何法规要求。

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>日志记录级别

若要在使用适用于 iOS 和 macOS 的 MSAL 进行日志记录时设置日志记录级别，请使用以下值之一：

|Level  |说明 |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日志记录 |
| `MSALLogLevelError` | 默认级别，仅在发生错误时输出信息 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  库入口点，包含参数和各种密钥链操作 |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>日志消息格式

MSAL 日志消息的消息部分采用 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message` 格式

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供关联 ID 和时间戳有助于跟踪问题。 时间戳和关联 ID 信息在日志消息中提供。 只能从 MSAL 日志记录消息中可靠地检索这些信息。

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>适用于 Java 的 MSAL 日志记录

适用于 Java 的 MSAL 允许使用已在应用中使用的日志记录库，只要该库与 SLF4J 兼容即可。 适用于 Java 的 MSAL 使用[适用于 Java 的简单日志记录结构](http://www.slf4j.org/) (SLF4J) 作为各种日志记录框架（例如 [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)、[Logback](http://logback.qos.ch/) 和 [Log4j](https://logging.apache.org/log4j/2.x/)）的简单结构或抽象层。 SLF4J 允许用户在部署时插入所需的日志记录框架。

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

有关如何绑定到其他日志记录框架的说明，请参阅 [SLF4J 手册](http://www.slf4j.org/manual.html)。

### <a name="personal-and-organization-information"></a>个人和组织信息

默认情况下，MSAL 日志记录不会捕获或者记录任何个人或组织数据。 在以下示例中，默认已禁用个人或组织数据的日志记录：

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

可以通过在客户端应用程序生成器中设置 `logPii()` 来启用个人和组织数据的日志记录。 如果启用个人或组织数据的日志记录，则应用必须负责安全处理高度敏感的数据，并遵守任何法规要求。

在以下示例中，默认已启用个人或组织数据的日志记录：

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>适用于 Python 的 MSAL 日志记录

MSAL Python 中的日志记录使用标准的 Python 日志记录机制，例如 `logging.info("msg")`。可按如下所示配置 MSAL 日志记录（并在 [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32) 中查看其运作方式）：

### <a name="enable-debug-logging-for-all-modules"></a>为所有模块启用调试日志记录

默认已禁用任何 Python 脚本中的日志记录。 若要对整个 Python 脚本中的所有模块启用调试日志记录，请使用：

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>仅消隐 MSAL 日志记录

若要仅消隐 MSAL 库日志记录，同时在 Python 脚本的所有其他模块中启用调试日志记录，请禁用 MSAL Python 使用的记录器：

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python 中的个人和组织数据

适用于 Python 的 MSAL 不会记录个人数据或组织数据。 没有任何属性可用于启用或者禁用个人或组织数据的日志记录。

你可以使用标准的 Python 日志记录来记录所需的任何内容，但需要责任安全处理敏感数据并遵守法规要求。

有关 Python 中的日志记录的详细信息，请参阅 Python 的[日志记录操作指南](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)。

---
