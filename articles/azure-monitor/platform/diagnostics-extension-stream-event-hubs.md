---
title: 将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心
description: 将 Azure Monitor 中的诊断扩展配置为将数据发送到 Azure 事件中心，以便将其转发到 Azure 之外的位置。
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 979535b1f9a237f6975908178fb1e5ed819181b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82233459"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心
Azure 诊断扩展是 Azure Monitor 中的一个代理，可从 Azure 虚拟机的来宾操作系统和工作负荷及其他计算资源收集监视数据。 本文介绍如何将数据从 Windows Azure 诊断扩展 (WAD) 发送到 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，以便将其转发到 Azure 之外的位置。

## <a name="supported-data"></a>支持的数据

从来宾操作系统收集的可发送到事件中心的数据包括以下内容。 WAD 收集的其他数据源（包括 IIS 日志和故障转储）无法发送到事件中心。

* Windows 事件跟踪 (ETW) 事件
* 性能计数器
* Windows 事件日志（包括 Windows 事件日志中的应用程序日志）
* Azure 诊断基础结构日志

## <a name="prerequisites"></a>先决条件

* Windows 诊断扩展 1.6 或更高版本。 请参阅 [Azure 诊断扩展配置架构版本和历史记录](diagnostics-extension-versions.md)以获取版本历史记录，参阅 [Azure 诊断扩展概述](diagnostics-extension-overview.md)以获取支持的资源。
* 必须始终预配事件中心命名空间。 请参阅[事件中心入门](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)以获取详细信息。


## <a name="configuration-schema"></a>配置架构
请参阅[安装并配置 Windows Azure 诊断扩展 (WAD)](diagnostics-extension-windows-install.md)，获取启用和配置诊断扩展的不同选项；参阅 [Azure 诊断配置架构](diagnostics-extension-schema-windows.md)，获取配置架构参考。 本文的其余部分将介绍如何使用此配置向事件中心发送数据。 

Azure 诊断始终将日志和指标发送到 Azure 存储帐户。 可以配置一个或多个可将数据发送到其他位置的数据接收器。 每个接收器都在公共配置的 [SinksConfig 元素](diagnostics-extension-schema-windows.md#sinksconfig-element)中定义，而敏感信息则包含在专用配置中。 事件中心的此配置使用下表中的值。

| 属性 | 说明 |
|:---|:---|
| 名称 | 接收器的说明性名称。 在配置中用于指定要发送到接收器的数据源。 |
| Url  | 事件中心的 Url，格式为 \<event-hubs-namespace\> servicebus.windows.net/ \<event-hub-name\> 。          |
| SharedAccessKeyName | 事件中心的至少具有“发送”权限的共享访问策略的名称。 |
| SharedAccessKey     | 事件中心的共享访问策略中的主密钥或辅助密钥。 |

下面显示了公共和专用配置示例。 这是一个最小配置，其中包含一个性能计数器和事件日志，用于说明如何配置和使用事件中心数据接收器。 如需更复杂的示例，请参阅 [Azure 诊断配置架构](diagnostics-extension-schema-windows.md)。

### <a name="public-configuration"></a>公共配置

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>专用配置

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>配置选项
若要将数据发送到数据接收器，请在数据源的节点上指定 **sinks** 特性。 在何处放置 **sinks** 特性决定了分配的范围。 在以下示例中，**sinks** 特性定义到 **PerformanceCounters** 节点，这会导致系统将所有子性能计数器都发送到事件中心。

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


在以下示例中，**sinks** 特性直接应用到三个计数器，这会导致系统仅将这些性能计数器发送到事件中心。 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>验证配置
可以使用多种方法来验证数据是否发送到事件中心。 使用事件中心捕获是一种直接的方法，如[通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件](../../event-hubs/event-hubs-capture-overview.md)中所述。 


## <a name="troubleshoot-event-hubs-sinks"></a>排查事件中心接收器问题

- 查看 Azure 存储表 **WADDiagnosticInfrastructureLogsTable**，其中包含 Azure 诊断本身的日志和错误。 可使用 [Azure 存储资源管理器](https://www.storageexplorer.com) 等工具连接到此存储帐户，查看此表，并添加过去 24 小时的时间戳查询。 可以使用此工具导出 .csv 文件，并在 Microsoft Excel 之类的应用程序中打开它。 Excel 可轻松搜索电话卡字符串（如 **EventHubs**），查看系统报告了哪些错误。  

- 检查是否已成功预配事件中心。 配置的 **PrivateConfig** 节的所有连接信息必须与门户中显示的资源值匹配。 请确保已在门户中定义 SAS 策略（本示例中为 *SendRule*），并为其授予“发送”权限。  

## <a name="next-steps"></a>后续步骤

* [事件中心概述](../../event-hubs/event-hubs-about.md)
* [创建事件中心](../../event-hubs/event-hubs-create.md)
* [事件中心常见问题](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



