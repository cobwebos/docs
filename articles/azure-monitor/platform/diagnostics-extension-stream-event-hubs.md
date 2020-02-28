---
title: 将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心
description: 将 Azure Monitor 中的诊断扩展配置为将数据发送到 Azure 事件中心，以便可以将其转发到 Azure 之外的位置。
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672525"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心
Azure 诊断扩展是 Azure Monitor 中的一个代理，它收集来宾操作系统中的监视数据以及 Azure 虚拟机和其他计算资源的工作负荷。 本文介绍如何将数据从 Windows Azure 诊断扩展（WAD）发送到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，以便可以转发到 azure 之外的位置。

## <a name="supported-data"></a>支持的数据

从可发送到事件中心的来宾操作系统收集的数据包括以下内容。 WAD 收集的其他数据源（包括 IIS 日志和故障转储）无法发送到事件中心。

* Windows 事件跟踪 (ETW) 事件
* 性能计数器
* Windows 事件日志，包括 Windows 事件日志中的应用程序日志
* Azure 诊断基础结构日志

## <a name="prerequisites"></a>必备条件

* Windows 诊断扩展1.6 或更高版本。 有关支持的资源的版本历史记录和[Azure 诊断扩展概述](diagnostics-extension-overview.md)，请参阅[Azure 诊断扩展配置架构版本和历史记录](diagnostics-extension-versions.md)。
* 必须始终预配事件中心命名空间。 有关详细信息，请参阅[事件中心入门](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。


## <a name="configuration-schema"></a>配置架构
有关启用和配置诊断 Azure 诊断扩展的不同选项，请参阅[安装和配置 Windows Azure 诊断扩展（WAD）](diagnostics-extension-windows-install.md) ，有关配置架构的引用，请参阅配置[架构](diagnostics-extension-schema-windows.md)。 本文的其余部分将介绍如何使用此配置向事件中心发送数据。 

Azure 诊断始终将日志和指标发送到 Azure 存储帐户。 可以配置一个或多个*数据接收器*，将数据发送到其他位置。 在公共配置的[SinksConfig 元素](diagnostics-extension-schema-windows.md#sinksconfig-element)中定义每个接收器，并在专用配置中包含敏感信息。 此事件中心配置使用下表中的值。

| properties | 说明 |
|:---|:---|
| 名称 | 接收器的描述性名称。 在配置中用于指定要发送到接收器的数据源。 |
| URL  | 事件中心的 Url，格式 \<事件中心-命名空间\>servicebus.windows.net/\<事件中心名称\>。          |
| SharedAccessKeyName | 至少具有**发送**授权的事件中心的共享访问策略的名称。 |
| SharedAccessKey     | 事件中心的共享访问策略中的主密钥或辅助密钥。 |

下面显示了 "公共" 和 "专用" 配置示例。 这是一个最小配置，其中包含一个性能计数器和事件日志，用于说明如何配置和使用事件中心数据接收器。 有关更复杂的示例，请参阅[Azure 诊断配置架构](diagnostics-extension-schema-windows.md)。

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
若要将数据发送到数据接收器，请在数据源的节点上指定**接收器**属性。 放入**接收器**属性的位置决定了赋值的作用域。 在下面的示例中，**接收器**属性定义为**PerformanceCounters**节点，这将导致所有子性能计数器都发送到事件中心。

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


在下面的示例中，**接收器**特性直接应用于三个计数器，这些计数器只会导致将这些性能计数器发送到事件中心。 

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

## <a name="validating-configuration"></a>正在验证配置
您可以使用多种方法来验证数据是否发送到事件中心。 ne 简单的方法是使用事件中心捕获，如[通过 Azure Blob 存储中的 Azure 事件中心捕获事件或 Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)中所述。 


## <a name="troubleshoot-event-hubs-sinks"></a>排查事件中心接收器问题

- 查看 Azure 存储表**WADDiagnosticInfrastructureLogsTable** ，其中包含 Azure 诊断本身的日志和错误。 一个选项是使用 [Azure 存储资源管理器](https://www.storageexplorer.com)等工具连接到此存储帐户，查看此表，并添加过去 24 小时的时间戳查询。 可以使用此工具导出 .csv 文件，并在 Microsoft Excel 之类的应用程序中打开它。 Excel 可以轻松地搜索电话卡字符串（如 **EventHubs**），以便查看系统报告了哪些错误。  

- 检查是否已成功预配事件中心。 配置的**privateconfig.json**节中的所有连接信息都必须与在门户中显示的资源的值匹配。 请确保已在门户中定义 SAS 策略（示例中为*SendRule* ），并且授予了*发送*权限。  

## <a name="next-steps"></a>后续步骤

* [事件中心概述](../../event-hubs/event-hubs-about.md)
* [创建事件中心](../../event-hubs/event-hubs-create.md)
* [事件中心常见问题解答](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



