---
title: 将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心
description: 在 Azure 监视器中配置诊断扩展以将数据发送到 Azure 事件中心，以便可以将数据转发到 Azure 外部的位置。
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672525"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心
Azure 诊断扩展是 Azure 监视器中的代理，用于从 Azure 虚拟机和其他计算资源的来宾操作系统和工作负荷收集监视数据。 本文介绍如何将数据从 Windows Azure 诊断扩展 （WAD） 发送到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，以便可以转发到 Azure 外部的位置。

## <a name="supported-data"></a>支持的数据

从来宾操作系统收集的可发送到事件中心的数据包括以下内容。 WAD 收集的其他数据源（包括 IIS 日志和崩溃转储）无法发送到事件中心。

* Windows 事件跟踪 (ETW) 事件
* 性能计数器
* Windows 事件日志，包括 Windows 事件日志中的应用程序日志
* Azure 诊断基础结构日志

## <a name="prerequisites"></a>先决条件

* Windows 诊断扩展 1.6 或更高版本。 有关受支持资源的版本历史记录和[Azure 诊断扩展概述](diagnostics-extension-overview.md)，请参阅[Azure 诊断扩展配置架构版本和历史记录](diagnostics-extension-versions.md)。
* 必须始终预配事件中心命名空间。 有关详细信息[，请参阅从事件中心开始](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。


## <a name="configuration-schema"></a>配置架构
有关启用和配置诊断扩展和[Azure 诊断配置架构](diagnostics-extension-schema-windows.md)的不同选项，请参阅[安装和配置 Windows Azure 诊断扩展 （WAD），](diagnostics-extension-windows-install.md)以便参考配置架构。 本文的其余部分将介绍如何使用此配置将数据发送到事件中心。 

Azure 诊断始终向 Azure 存储帐户发送日志和指标。 您可以配置一个或多个将数据发送到其他位置*的数据接收器*。 每个接收器都在公共配置的[SinksConfig 元素](diagnostics-extension-schema-windows.md#sinksconfig-element)中定义，在私有配置中包含敏感信息。 事件中心此配置使用下表中的值。

| properties | 描述 |
|:---|:---|
| “属性” | 接收器的描述性名称。 在配置中用于指定要发送到接收器的数据源。 |
| URL  | 窗体\<事件中心-命名空间\>中的事件中心 url servicebus.windows.net/\<事件中心名称\>。          |
| 共享访问密钥名称 | 具有至少 **"发送**"权限的事件中心的共享访问策略的名称。 |
| SharedAccessKey     | 事件中心共享访问策略的主键或辅助键。 |

示例公共配置和私有配置如下所示。 这是一个最小配置，具有单个性能计数器和事件日志，用于说明如何配置和使用事件中心数据接收器。 有关更复杂的示例，请参阅[Azure 诊断配置架构](diagnostics-extension-schema-windows.md)。

### <a name="public-configuration"></a>公共配置

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
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
要将数据发送到数据接收器，请在数据源的节点上指定**接收器**属性。 放置**接收器**属性的位置决定了分配的范围。 在下面的示例中，**接收器**属性被定义为**性能计数器**节点，这将导致将所有子性能计数器发送到事件中心。

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


在下面的示例中，**接收器**属性直接应用于三个计数器，这将导致仅将这些性能计数器发送到事件中心。 

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
可以使用多种方法验证数据是否已发送到事件中心。 ne 简单方法是使用事件中心捕获，如在[Azure Blob 存储或 Azure 数据湖存储中通过 Azure 事件中心捕获事件](../../event-hubs/event-hubs-capture-overview.md)中所述。 


## <a name="troubleshoot-event-hubs-sinks"></a>排查事件中心接收器问题

- 查看 Azure 存储表**WAD 诊断基础结构日志表**，其中包含 Azure 诊断本身的日志和错误。 一个选项是使用 [Azure 存储资源管理器](https://www.storageexplorer.com)等工具连接到此存储帐户，查看此表，并添加过去 24 小时的时间戳查询。 可以使用此工具导出 .csv 文件，并在 Microsoft Excel 之类的应用程序中打开它。 Excel 可以轻松地搜索电话卡字符串（如 **EventHubs**），以便查看系统报告了哪些错误。  

- 检查是否已成功预配事件中心。 配置的 Private **Config**部分中的所有连接信息必须与门户中所看到的资源值匹配。 请确保门户中定义了 SAS 策略（示例中的*SendRule），* 并且已授予 *"发送*"权限。  

## <a name="next-steps"></a>后续步骤

* [事件中心概述](../../event-hubs/event-hubs-about.md)
* [创建事件中心](../../event-hubs/event-hubs-create.md)
* [事件中心常见问题](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



