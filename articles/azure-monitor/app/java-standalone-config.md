---
title: 随时随地监视 Java 应用程序 - Azure 监视器应用程序见解
description: 无需检测应用程序即可在任何环境中运行的 Java 应用程序的无代码应用程序性能监视。 使用分布式跟踪和应用程序映射查找问题 d 的根本原因。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641883"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>配置选项 - 用于 Azure 监视器应用程序见解的 Java 独立代理



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

连接字符串是必需的，并且每当将不同应用程序的数据发送到同一应用程序见解资源时，角色名称都很重要。

有关更多详细信息，请参阅下面的更多详细信息和其他配置选项。

## <a name="configuration-file-path"></a>配置文件路径

默认情况下，应用程序见解 Java 3.0 预览版希望配置文件`ApplicationInsights.json`被命名为 ，并且应位于 与`applicationinsights-agent-3.0.0-PREVIEW.jar`的目录中。

您可以使用以下任一

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`环境变量，或
* `applicationinsights.configurationFile`Java系统属性

如果指定相对路径，则将相对于所在的目录解析该路径`applicationinsights-agent-3.0.0-PREVIEW.jar`。

## <a name="connection-string"></a>连接字符串

这是必填字段。 您可以在应用程序见解资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="应用程序见解连接字符串":::

您还可以使用环境变量`APPLICATIONINSIGHTS_CONNECTION_STRING`设置连接字符串。

## <a name="cloud-role-name"></a>云角色名称

云角色名称用于在应用程序映射上标记组件。

如果要设置云角色名称：

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

如果未设置云角色名称，则应用程序见解资源的名称将用于在应用程序映射上标记组件。

您还可以使用环境变量`APPLICATIONINSIGHTS_ROLE_NAME`设置云角色名称。

## <a name="cloud-role-instance"></a>云角色实例

云角色实例默认为计算机名称。

如果要将云角色实例设置为不同内容，而不是计算机名称：

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

您还可以使用环境变量`APPLICATIONINSIGHTS_ROLE_INSTANCE`设置云角色实例。

## <a name="application-log-capture"></a>应用程序日志捕获

应用程序见解 Java 3.0 预览版通过 Log4j、Logback 和 java.util.log 自动捕获应用程序日志记录。

默认情况下，它将捕获在`WARN`级别或以上级别执行的所有日志记录。

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

这些是可以在`ApplicationInsights.json`文件中指定`threshold`的有效值，以及它们如何对应于不同日志记录框架中的日志记录级别：

| `threshold`  | Log4j  | Logback | 7 月     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| 致命        | 致命  | ERROR   | SEVERE  |
| 错误/严重 | ERROR  | ERROR   | SEVERE  |
| 警告/警告 | 警告   | 警告    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| 调试/精细   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| 跟踪/精细 | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX 指标

如果您有一些 JMX 指标，您有兴趣捕获：

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

## <a name="micrometer"></a>Micrometer

默认情况下，如果应用程序使用[微数计](https://micrometer.io)，应用程序见解 3.0（从 Preview.2 开始）现在将自己添加到微米全局注册表并捕获微米指标。

如果要禁用此功能：

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

默认情况下，应用程序见解 Java 3.0 预览每 15 分钟发送一次检测信号指标。 如果使用检测信号指标触发警报，则可以增加此检测信号的频率：

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
> 您不能降低此检测信号的频率，因为检测信号数据也用于跟踪应用程序见解使用情况。

## <a name="sampling"></a>采样

如果需要降低成本，采样非常有用。
采样作为操作 ID（也称为跟踪 ID）上的函数执行，以便相同的操作 ID 始终导致相同的采样决策。 这可确保在采样分布式事务的其他部分时不会获取该部分。

例如，如果将采样设置为 10%，则只能看到 10% 的事务，但其中 10% 中的每一个都将具有完整的端到端事务详细信息。

下面是如何将采样设置为**所有事务的 10%** 的示例 - 请确保设置适合使用情况的采样率：

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

如果应用程序位于防火墙后面，并且无法直接连接到应用程序见解（请参阅[应用程序见解使用的 IP 地址](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)），则可以将应用程序见解 Java 3.0 预览配置为使用 HTTP 代理：

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

"自诊断"是指来自应用程序见解 Java 3.0 预览的内部日志记录。

这有助于发现和诊断应用程序见解本身的问题。

默认情况下，它登录到与级别 对应此`warn`配置的级别 的控制台：

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

有效级别为`OFF` `ERROR` `WARN`、、、、、、`INFO``DEBUG`和`TRACE`。

如果要登录到文件而不是登录到控制台：

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

使用文件日志记录时，一旦文件命中`maxSizeMB`，它将滚动，除了当前日志文件之外，仅保留最近完成的日志文件。
