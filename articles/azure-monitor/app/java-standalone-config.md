---
title: 随时随地监视 Java 应用程序-Azure Monitor Application Insights
description: 无代码置备应用程序性能监视，适用于在任何环境中运行的 Java 应用程序，而无需检测应用程序。 使用分布式跟踪和应用程序映射查找问题 d 的根本原因。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 478e42669339ac015076c89da103d91080090685
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509204"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>配置选项-Java 独立代理，用于 Azure Monitor Application Insights



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

连接字符串是必需的，无论何时从不同的应用程序将数据发送到同一个 Application Insights 资源，角色名称都非常重要。

有关更多详细信息，请查看下面的详细信息和其他配置选项。

## <a name="configuration-file-path"></a>配置文件路径

默认情况下，Application Insights Java 3.0 Preview 需要将配置文件命名`ApplicationInsights.json`为，并将其与位于同一个目录中`applicationinsights-agent-3.0.0-PREVIEW.4.jar`。

你可以使用以下任一方法指定你自己的配置文件路径：

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`环境变量或
* `applicationinsights.configurationFile`Java 系统属性

如果指定相对路径，则将相对于所在的目录`applicationinsights-agent-3.0.0-PREVIEW.4.jar`解析它。

## <a name="connection-string"></a>连接字符串

这是必填字段。 可以在 Application Insights 资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 连接字符串":::

你还可以使用环境变量`APPLICATIONINSIGHTS_CONNECTION_STRING`设置连接字符串。

## <a name="cloud-role-name"></a>云角色名称

云角色名称用于标记应用程序映射上的组件。

如果要设置云角色名称，请执行以下操作：

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

如果未设置云角色名称，则 Application Insights 资源的名称将用于在应用程序映射上标记该组件。

你还可以使用环境变量`APPLICATIONINSIGHTS_ROLE_NAME`设置云角色名称。

## <a name="cloud-role-instance"></a>云角色实例

云角色实例默认为计算机名称。

如果要将云角色实例设置为不同于计算机名称的名称，请执行以下操作：

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

你还可以使用环境变量`APPLICATIONINSIGHTS_ROLE_INSTANCE`设置云角色实例。

## <a name="application-log-capture"></a>应用程序日志捕获

Application Insights Java 3.0 Preview 通过 Log4j、Logback 和 util 自动捕获应用程序日志记录。

默认情况下，它将捕获在级别`WARN`或更高级别上执行的所有日志记录。

如果要更改此阈值：

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

这些是可以在`threshold` `ApplicationInsights.json`文件中指定的有效值，以及这些值如何与不同日志记录框架中的日志记录级别相对应：

| `threshold`  | Log4j  | Logback | 7 月     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| 出现        | 出现  | ERROR   | SEVERE  |
| 错误/严重 | ERROR  | ERROR   | SEVERE  |
| 警告/警告 | 不再   | 不再    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| 调试/精细   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| 跟踪/最佳 | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX 指标

如果你有想要捕获的某些 JMX 指标：

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
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

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer （包括弹簧 Boot 传动装置的指标）

如果你的应用程序使用[Micrometer](https://micrometer.io)，Application Insights 3.0 （从预览版开始）现在捕获发送到 Micrometer 全局注册表的指标。

如果你的应用程序使用[春季 Boot 制动器](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)，Application Insights 3.0 （从预览版开始）现在捕获由弹簧 Boot 传动装置（使用 Micrometer，但不使用 Micrometer 全局注册表）配置的指标。

如果要禁用这些功能：

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

默认情况下，Application Insights Java 3.0 Preview 每15分钟发送一次检测信号指标。 如果使用检测信号指标来触发警报，则可以增加此检测信号的频率：

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

如果需要降低成本，则采样非常有用。
采样作为函数对操作 ID （也称为跟踪 ID）执行，因此相同的操作 ID 将始终产生相同的采样决定。 这可确保在采样中的其他部分时不会获取在其中进行采样的分布式事务的一部分。

例如，如果将 "采样" 设置为10%，则只会看到10% 的事务，但每个事务中的每一个都有完整的端到端事务详细信息。

下面是一个示例，说明如何将采样设置为**所有事务的 10%** ，请确保为用例设置正确的采样率：

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

## <a name="http-proxy"></a>HTTP 代理

如果你的应用程序位于防火墙后面且无法直接连接到 Application Insights （请参阅[Application Insights 使用的 IP 地址](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)），则可以将 Application Insights Java 3.0 Preview 配置为使用 HTTP 代理：

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

## <a name="self-diagnostics"></a>自诊断

"自诊断" 指的是 Application Insights Java 3.0 Preview 的内部日志记录。

这对于发现和诊断 Application Insights 本身的问题很有帮助。

默认情况下，它会记录与此`warn`配置相对应的级别控制台：

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

有效级别为`OFF`、 `ERROR` `WARN`、、 `INFO`、 `DEBUG`和`TRACE`。

如果要记录到文件而不是日志记录到控制台：

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

使用文件日志记录时，文件命中`maxSizeMB`后，它将进行滚动更新，只保留最近完成的日志文件以及当前日志文件。
