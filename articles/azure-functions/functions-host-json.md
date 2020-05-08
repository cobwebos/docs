---
title: Azure Functions 2.x 的 host.json 参考
description: 使用 v2 运行时的 Azure Functions host.json 文件的参考文档。
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 39e6ce5d6807a554cc1714a3970bed8303c31ce8
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690888"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 及更高版本的 host.json 参考 

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-host-json-v1.md)
> * [版本 2+](functions-host-json.md)

*host.json* 元数据文件包含对函数应用的所有函数产生影响的全局配置选项。 本文列出了从 Azure Functions 运行时 2.x 版开始可用的设置。  

> [!NOTE]
> 本文适用于 Azure Functions 2.x 及更高版本。  有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

其他函数应用配置选项在[应用设置](functions-app-settings.md)（适用于已部署的应用）或 [local.settings.json](functions-run-local.md#local-settings-file) 文件（用于本地开发）中进行管理。

host.json 中与绑定相关的配置将同样地应用于函数应用中的每个函数。 

你还可以使用应用程序设置来[覆盖或应用每个环境的设置](#override-hostjson-values)。

## <a name="sample-hostjson-file"></a>示例 host.json 文件

下面的版本 2.x + 的示例*host*文件中指定了所有可能的选项（排除仅供内部使用的选项）。

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

本文的以下各部分解释了每个顶级属性。 除非另有说明，否则其中的所有属性都是可选的。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

此设置是[日志记录](#logging)的子项。

控制 Application Insights 的选项，包括[采样选项](./functions-monitoring.md#configure-sampling)。

有关完整的 JSON 结构，请参阅前面的[示例主机 JSON 文件](#sample-hostjson-file)。

> [!NOTE]
> 日志采样可能会导致一些执行不会显示在 Application Insights 监视器边栏选项卡中。 若要避免日志采样， `excludedTypes: "Request"`请将`samplingSettings`添加到值。

| 属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| samplingSettings | 不适用 | 请参阅[applicationinsights.config. samplingSettings](#applicationinsightssamplingsettings)。 |
| enableLiveMetrics | 是 | 启用实时指标收集。 |
| enableDependencyTracking | 是 | 启用依赖项跟踪。 |
| enablePerformanceCountersCollection | 是 | 启用 Kudu 性能计数器集合。 |
| liveMetricsInitializationDelay | 00:00:15 | 仅供内部使用。 |
| httpAutoCollectionOptions | 不适用 | 请参阅[applicationinsights.config. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions)。 |
| snapshotConfiguration | 不适用 | 请参阅[applicationinsights.config. snapshotConfiguration](#applicationinsightssnapshotconfiguration)。 |

### <a name="applicationinsightssamplingsettings"></a>Applicationinsights.config. samplingSettings

|属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| isEnabled | 是 | 启用或禁用采样。 | 
| maxTelemetryItemsPerSecond | 20 | 每个服务器主机上每秒记录的遥测项的目标数目。 如果你的应用程序在多个主机上运行，请将此值降低到流量的总体目标值中。 | 
| evaluationInterval | 01:00:00 | 会重新评估当前遥测速率的间隔。 评估以移动平均线形式进行执行。 可能想要缩短此间隔（如果遥测很容易就激增）。 |
| initialSamplingPercentage| 1.0 | 采样过程开始时应用的初始采样百分比，以动态改变百分比。 不要在调试时减小值。 |
| samplingPercentageIncreaseTimeout | 00:00:01 | 采样百分比值更改时，此属性将确定允许之后 Application Insights 再次引发采样百分比以捕获更多数据。 |
| samplingPercentageDecreaseTimeout | 00:00:01 | 采样百分比值更改时，此属性将确定允许之后 Application Insights 再次降低采样百分比以捕获更少的数据。 |
| minSamplingPercentage | 0.1 | 随着采样百分比的变化，此属性将确定允许的最小采样百分比。 |
| maxSamplingPercentage | 0.1 | 随着采样百分比的变化，此属性将确定允许的最大采样百分比。 |
| movingAverageRatio | 1.0 | 在移动平均线的计算中，权重分配给最新的值。 使用等于或小于 1 的值。 较小的值会使算法不易受突然的更改影响。 |
| excludedTypes | null | 不希望对其进行采样的以分号分隔的类型列表。 可识别的类型为`Dependency`： `Event`、 `Exception`、 `PageView`、 `Request`、和`Trace`。 传输指定类型的所有实例;对未指定的类型进行采样。 |
| includedTypes | null | 要进行采样的以分号分隔的类型列表。空列表隐含了所有类型。 此处列出的`excludedTypes`替代类型中列出了类型。 可识别的类型为`Dependency`： `Event`、 `Exception`、 `PageView`、 `Request`、和`Trace`。 采样指定类型的实例;未指定或隐含的类型将在不采样的情况下传输。 |

### <a name="applicationinsightshttpautocollectionoptions"></a>Applicationinsights.config. httpAutoCollectionOptions

|属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | 是 | 启用或禁用 HTTP 触发器的扩展 HTTP 请求信息：传入请求相关标头，多检测密钥支持，HTTP 方法，路径，以及响应。 |
| enableW3CDistributedTracing | 是 | 启用或禁用对 W3C 分布式跟踪协议的支持（并打开旧版相关架构）。 如果`enableHttpTriggerExtendedInfoCollection`为 true，则默认情况下启用。 如果`enableHttpTriggerExtendedInfoCollection`为 false，则此标志仅适用于传出请求，而不应用于传入请求。 |
| enableResponseHeaderInjection | 是 | 启用或禁用向响应中注入多组件相关标头。 启用注入允许 Application Insights 在使用多个检测密钥时构造应用程序映射。 如果`enableHttpTriggerExtendedInfoCollection`为 true，则默认情况下启用。 如果`enableHttpTriggerExtendedInfoCollection`为 false，则此设置不适用。 |

### <a name="applicationinsightssnapshotconfiguration"></a>Applicationinsights.config. snapshotConfiguration

有关快照的详细信息，请参阅[调试 .net 应用中的异常](/azure/azure-monitor/app/snapshot-debugger)和[排查 Application Insights Snapshot Debugger 或查看快照](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)中的异常。

|属性 | 默认 | 说明 |
| --------- | --------- | --------- | 
| agentEndpoint | null | 用于连接到 Application Insights Snapshot Debugger 服务的终结点。 如果为 null，则使用默认终结点。 |
| captureSnapshotMemoryWeight | 0.5 | 检查是否有足够的内存来拍摄快照时，为当前进程内存大小指定的权重。 预期值为大于0的正确分数（0 < CaptureSnapshotMemoryWeight < 1）。 |
| failedRequestLimit | 3 | 禁用遥测处理器之前请求快照的失败请求数的限制。|
| handleUntrackedExceptions | 是 | 启用或禁用 Application Insights 遥测未跟踪的异常跟踪。 |
| isEnabled | 是 | 启用或禁用快照收集 | 
| isEnabledInDeveloperMode | false | 启用或禁用快照收集在开发人员模式下启用。 |
| isEnabledWhenProfiling | 是 | 启用或禁用快照创建，即使 Application Insights Profiler 正在收集详细分析会话也是如此。 |
| isExceptionSnappointsEnabled | false | 启用或禁用异常筛选。 |
| isLowPrioritySnapshotUploader | 是 | 确定是否按正常优先级运行 SnapshotUploader 进程。 |
| maximumCollectionPlanSize | 50 | 在从一到9999的范围内随时可以跟踪的问题的最大数目。 |
| maximumSnapshotsRequired | 3 | 为单个问题收集的最大快照数，范围为1到999。 在应用程序中，可能会将问题视为单个 throw 语句。 为问题收集的快照数达到此值后，就不会再为该问题收集更多的快照（请参阅`problemCounterResetInterval`），并再次达到`thresholdForSnapshotting`限制。 |
| problemCounterResetInterval | 24:00:00 | 在一分钟到7天内重置问题计数器的频率。 达到此间隔后，所有问题计数都将重置为零。 已达到用于执行快照的阈值但尚未在中`maximumSnapshotsRequired`生成快照数的现有问题仍处于活动状态。 |
| provideAnonymousTelemetry | 是 | 确定是否将匿名使用和错误遥测发送给 Microsoft。 如果与 Microsoft 联系以帮助解决与 Snapshot Debugger 相关的问题，则可以使用此遥测。 它还用于监视使用模式。 |
| reconnectInterval | 00:15:00 | 重新连接到 Snapshot Debugger 终结点的频率。 允许的范围是一分钟到一天。 |
| shadowCopyFolder | null | 指定用于卷影复制二进制文件的文件夹。 如果未设置，则按以下环境变量指定的文件夹按顺序尝试： Fabric_Folder_App_Temp、LOCALAPPDATA、APPDATA、TEMP。 |
| shareUploaderProcess | 是 | 如果为 true，则只有一个 SnapshotUploader 实例将为共享 InstrumentationKey 的多个应用收集和上载快照。 如果设置为 false，则 SnapshotUploader 对于每个（ProcessName，InstrumentationKey）元组都是唯一的。 |
| snapshotInLowPriorityThread | 是 | 确定是否在低 IO 优先级线程中处理快照。 创建快照是一个快速操作，但为了将快照上载到 Snapshot Debugger 服务，必须首先将其作为小型转储写入磁盘。 这会在 SnapshotUploader 过程中发生。 如果将此值设置为 true，则使用低优先级 IO 来写入小型转储，而不会与应用程序争用资源。 如果将此值设置为 "false"，则会加速创建小型转储，同时降低应用程序的运行速度。 |
| snapshotsPerDayLimit | 30 | 一天中允许的最大快照数（24小时）。 此限制还强制用于 Application Insights 服务端。 上传速率限制为每个应用程序每天50（即每个检测密钥）。 此值有助于阻止创建在上载过程中最终会被拒绝的其他快照。 如果值为零，则完全删除限制，不建议这样做。 |
| snapshotsPerTenMinutesLimit | 1 | 10分钟内允许的最大快照数。 尽管此值没有上限，但由于它可能会影响应用程序的性能，因此请小心增加生产工作负荷。 创建快照的速度快，但创建快照的小型转储，并将其上载到 Snapshot Debugger 服务是一项更慢的操作，它将与应用程序争用资源（CPU 和 i/o）。 |
| tempFolder | null | 指定用于写入小型转储和上载者日志文件的文件夹。 如果未设置，则使用 *%TEMP%\Dumps* 。 |
| thresholdForSnapshotting | 1 | Application Insights 需要多长时间才能在请求快照之前查看异常。 |
| uploaderProxy | null | 替代快照上载程序进程中使用的代理服务器。 如果你的应用程序通过代理服务器连接到 internet，则可能需要使用此设置。 Snapshot Collector 在应用程序的进程中运行，并将使用相同的代理设置。 但是，快照上载程序作为单独的进程运行，你可能需要手动配置代理服务器。 如果此值为 null，则 Snapshot Collector 将尝试通过检查 WebRequest DefaultWebProxy 并将值传递到快照上载程序来自动检测代理的地址。 如果此值不为 null，则不会使用 "自动检测"，将在快照上载者中使用此处指定的代理服务器。 |

## <a name="cosmosdb"></a>CosmosDB

可在 [Cosmos DB 触发器和绑定](functions-bindings-cosmosdb-v2-output.md#host-json)中查找配置设置。

## <a name="durabletask"></a>durableTask

可在 [Durable Functions 的绑定](durable/durable-functions-bindings.md#host-json)中查找配置设置。

## <a name="eventhub"></a>eventHub

可在[事件中心触发器和绑定](functions-bindings-event-hubs-trigger.md#host-json)中查找配置设置。 

## <a name="extensions"></a>扩展

该属性返回一个对象，其中包含所有特定于绑定的设置，例如 [http](#http) 和 [eventHub](#eventhub)。

## <a name="extensionbundle"></a>extensionBundle 

使用扩展捆绑可将一组兼容的 Functions 绑定扩展添加到函数应用。 若要了解详细信息，请参阅[用于本地开发的扩展捆绑](functions-bindings-register.md#extension-bundles)。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

作业宿主运行的函数的列表。 空数组表示运行所有函数。 仅供在[本地运行](functions-run-local.md)时使用。 在 Azure 中的函数应用中，你应当改为按照[如何在 Azure Functions 中禁用函数](disable-function.md)中的步骤来禁用特定函数，而不是使用此设置。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指示所有函数的超时持续时间。 它采用 timespan 字符串格式。 在无服务器消耗计划中，有效范围为 1 秒至 10 分钟，默认值为 5 分钟。  

在高级计划中，有效范围为1秒至60分钟，默认值为30分钟。

在专用（应用服务）计划中，没有总体限制，默认值为 30 分钟。 值 `-1` 指示无限执行，但建议保留固定上限。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[主机运行状况监视器](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)的配置设置。

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|已启用|是|指定是否启用此功能。 | 
|healthCheckInterval|10 秒|定期后台运行状况检查之间的时间间隔。 | 
|healthCheckWindow|2 分钟|与 `healthCheckThreshold` 设置结合使用的滑动时间窗口。| 
|healthCheckThreshold|6|在启动主机回收之前，运行状况检查可以失败的最大次数。| 
|counterThreshold|0.80|性能计数器将被视为不正常的阈值。| 

## <a name="http"></a>http

可在 [http 触发器和绑定](functions-bindings-http-webhook-output.md#hostjson-settings)中查找配置设置。

## <a name="logging"></a>logging

控制函数应用（包括 Application Insights）的日志记录行为。

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|定义启用哪种级别的文件日志记录。  选项包括 `never`、`always` 和 `debugOnly`。 |
|logLevel|不适用|一个对象，它定义了用于筛选应用中的函数的日志类别。 版本2.x 和更高版本按照 ASP.NET Core 布局进行日志类别筛选。 此设置允许你筛选特定函数的日志记录。 有关详细信息，请参阅 ASP.NET Core 文档中的[日志筛选](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)。 |
|控制台|不适用| [控制台](#console)日志记录设置。 |
|applicationInsights|不适用| [applicationInsights](#applicationinsights) 设置。 |

## <a name="console"></a>控制台

此设置是[日志记录](#logging)的子项。 它在未处于调试模式时控制控制台日志记录。

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|isEnabled|false|启用或禁用控制台日志记录。| 

## <a name="manageddependency"></a>managedDependency

托管依赖项是一项功能，目前仅支持基于 PowerShell 的函数。 它使依赖项可以由服务自动管理。 `enabled` 属性设置为 `true` 时，`requirements.psd1` 文件会被处理。 发布任何次要版本时会更新依赖项。 有关详细信息，请参阅 PowerShell 文章中的[托管依赖项](functions-reference-powershell.md#dependency-management)。

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

可在[存储队列触发器和绑定](functions-bindings-storage-queue-output.md#host-json)中查找设置。  

## <a name="sendgrid"></a>SendGrid

可在 [SendGrid 触发器和绑定](functions-bindings-sendgrid.md#host-json)中查找配置设置。

## <a name="servicebus"></a>serviceBus

可在[服务总线触发器和绑定](functions-bindings-service-bus-output.md#host-json)中查找配置设置。

## <a name="singleton"></a>singleton

单一实例锁行为的配置设置。 有关详细信息，请参阅[有关单一实例支持的 GitHub 问题](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)。

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|占用函数级锁的时间段。 锁自动续订。| 
|listenerLockPeriod|00:01:00|占用侦听器锁的时间段。| 
|listenerLockRecoveryPollingInterval|00:01:00|在启动时无法获取侦听器锁的情况下，用于恢复侦听器锁的时间间隔。| 
|lockAcquisitionTimeout|00:01:00|运行时尝试获取锁的最长时间。| 
|lockAcquisitionPollingInterval|不适用|尝试获取锁的间隔时间。| 

## <a name="version"></a>版本

此值指示 host 的架构版本。 对于面向 v2 `"version": "2.0"`运行时或更高版本的函数应用，版本字符串是必需的。 V2 和 v3 之间没有 host json 架构更改。

## <a name="watchdirectories"></a>watchDirectories

应该监视其更改情况的一组[共享代码目录](functions-reference-csharp.md#watched-directories)。  确保当这些目录中的代码发生更改时，函数会拾取这些更改。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="override-hostjson-values"></a>重写 host json 值

在某些情况下，你可能希望在特定环境的 host json 文件中配置或修改特定设置，而无需更改 host json 文件本身。  可以重写特定的主机。 json 值正在创建与应用程序设置相同的值。 当运行时找到格式`AzureFunctionsJobHost__path__to__setting`的应用程序设置时，它将重写位于 json `path.to.setting`中的等效的 host json 设置。 表示为应用程序设置时，用于指示 JSON`.`层次结构的点（）将替换为双下划线（`__`）。 

例如，假设你想要在本地运行时禁用 Application insights 采样。 如果更改了本地 host json 文件以禁用 Application Insights，则在部署过程中可能会将此更改推送到生产应用。 执行此操作的更安全方法是改为`"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"`在`local.settings.json`文件中创建应用程序设置。 你可以在以下`local.settings.json`文件中看到此内容，这不会发布：

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何更新 host.json 文件](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [查看环境变量中的全局设置](functions-app-settings.md)
