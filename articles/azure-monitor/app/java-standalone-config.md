---
title: 在任意位置监视 Java 应用程序 - Azure Monitor Application Insights
description: 对在任何环境中运行的 Java 应用程序进行无代码应用程序性能监视，而不需要检测该应用。 使用分布式跟踪和应用程序映射查找问题的根本原因。
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93b0b89cff7e48ddc4eb9173c9423961f96ec4bb
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371297"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>配置选项 - Azure Monitor Application Insights 的 Java 独立代理



## <a name="connection-string-and-role-name"></a>连接字符串和角色名称

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

将数据从不同的应用程序发送到同一个 Application Insights 资源时，必须使用连接字符串和角色名称。

下面提供了更多详细信息和其他配置选项。

## <a name="configuration-file-path"></a>配置文件路径

默认情况下，Application Insights Java 3.0 Preview 要求将配置文件命名为 `ApplicationInsights.json` 并置于 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` 所在的目录中。

可以使用以下任一方法指定你自己的配置文件路径：

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 环境变量，或者
* `applicationinsights.configurationFile` Java 系统属性

如果你指定相对路径，系统会相对于 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` 所在的目录对其进行解析。

## <a name="connection-string"></a>连接字符串

这是必填字段。 可以在 Application Insights 资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 连接字符串":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

还可以使用环境变量 `APPLICATIONINSIGHTS_CONNECTION_STRING` 设置连接字符串。

如果未设置连接字符串，则将禁用 Java 代理。

## <a name="cloud-role-name"></a>云角色名称

云角色名称用于标记应用程序映射上的组件。

如果要设置云角色名称，请执行以下代码：

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

如果你未设置云角色名称，系统会使用 Application Insights 资源的名称在应用程序映射上标记该组件。

还可以使用环境变量 `APPLICATIONINSIGHTS_ROLE_NAME` 设置云角色名称。

## <a name="cloud-role-instance"></a>云角色实例

云角色实例默认为计算机名称。

若要将云角色实例设置为不同于计算机名称的名称，请执行以下代码：

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

还可以使用环境变量 `APPLICATIONINSIGHTS_ROLE_INSTANCE` 设置云角色实例。

## <a name="application-log-capture"></a>应用程序日志捕获

Application Insights Java 3.0 Preview 通过 Log4j、Logback 和 java.util.logging 自动捕获应用程序日志记录。

默认情况下，它会捕获在 `INFO` 级别或更高级别执行的所有日志记录。

若要更改此阈值，请执行以下代码：

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

还可以使用环境变量设置日志记录阈值 `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` 。

下面介绍了你可以在 `ApplicationInsights.json` 文件中指定的有效的 `threshold` 值，以及这些值如何对应于不同日志记录框架中的日志记录级别：

| 阈值   | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | SEVERE  |
| 错误 (或严重)  | ERROR  | ERROR   | SEVERE  |
| 警告 (或警告)  | WARN   | WARN    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| 调试 (或精细)    | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| 跟踪 (或最佳)  | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX 指标

如果有想要捕获的某些 JMX 指标，请执行以下代码：

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

还可以使用环境变量设置 JMX 指标 `APPLICATIONINSIGHTS_JMX_METRICS` 。

此环境变量内容必须是匹配上述结构的 json 数据，例如 `[{"objectName": "java.lang:type=Runtime", "attribute": "Uptime", "display": "JVM uptime (millis)"}, {"objectName": "java.lang:type=MemoryPool,name=Code Cache", "attribute": "Usage.used", "display": "Code Cache Used"}]`

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer（包括 Spring Boot Actuator 中的指标）

如果应用程序使用 [Micrometer](https://micrometer.io)，你可以使用 Application Insights 3.0（从 Preview.2 开始）来捕获发送到 Micrometer 全局注册表的指标。

如果应用程序使用 [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)，你可以使用 Application Insights 3.0（从 Preview.4 开始）捕获由 Spring Boot Actuator（使用 Micrometer，但不使用 Micrometer 全局注册表）配置的指标。

若要禁用这些功能，请执行以下代码：

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>检测信号

默认情况下，Application Insights Java 3.0 Preview 每 15 分钟发送一个检测信号指标。 如果使用检测信号指标来触发警报，则可增加此检测信号的频率：

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> 不能降低此检测信号的频率，因为检测信号数据也用于跟踪 Application Insights 使用情况。

## <a name="sampling"></a>采样

如果需要降低成本，则可进行采样。
采样将作为一个函数来执行，该函数基于操作 ID（也称跟踪 ID）。因此，相同的操作 ID 始终会产生相同的采样决定。 这可确保你不会在采样过程中采用分布式事务的某些部分而排除其他部分。

例如，如果将采样率设置为 10%，则只会看到 10% 的事务，但在这 10% 的事务中，每个事务都有完整的端到端事务详细信息。

下面是一个示例，演示了如何将采样设置为 **10% 的所有事务** - 请确保你设置的采样率适合自己的用例：

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

还可以使用环境变量设置采样百分比 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` 。

## <a name="http-proxy"></a>HTTP 代理

如果应用程序位于防火墙后面，无法直接连接到 Application Insights（请参阅 [Application Insights 使用的 IP 地址](./ip-addresses.md)），则可将 Application Insights Java 3.0 Preview 配置为使用 HTTP 代理：

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>自我诊断

“自我诊断”指的是 Application Insights Java 3.0 Preview 的内部日志记录。

此功能可用于发现和诊断 Application Insights 本身的问题。

默认情况下，它会将数据记录到控制台，使用的级别为 `warn`，相应的配置如下：

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

有效的级别为 `OFF`、`ERROR`、`WARN`、`INFO`、`DEBUG`、`TRACE`。

如果要记录到文件而不是记录到控制台，请执行以下代码：

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

使用文件日志记录时，一旦文件达到 `maxSizeMB` 大小，就会进行滚动更新，只保留最近完成的日志文件和当前日志文件。
