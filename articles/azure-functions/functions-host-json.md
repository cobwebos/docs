---
title: Azure Functions 2.x 的 host.json 参考
description: 使用 v2 运行时的 Azure Functions host.json 文件的参考文档。
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3d98be2dcc351aa88b9e126c883865079e407c2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473364"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 及更高版本的 host.json 参考 

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-host-json-v1.md)
> * [版本 2*](functions-host-json.md)

*host.json* 元数据文件包含对函数应用的所有函数产生影响的全局配置选项。 本文列出了从 Azure Functions 运行时 2.x 版开始可用的设置。  

> [!NOTE]
> 本文适用于 Azure Functions 2.x 及更高版本。  有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

其他功能应用配置选项在[应用设置](functions-app-settings.md)（针对已部署的应用）或[本地.settings.json](functions-run-local.md#local-settings-file)文件（用于本地开发）中管理。

与绑定相关的 host.json 中的配置将同样应用于函数应用中的每个函数。 

## <a name="sample-hostjson-file"></a>示例 host.json 文件

版本 2.x_ 的以下示例*host.json*文件指定了所有可能的选项（不包括任何仅供内部使用的选项）。

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

控制应用程序见解的选项，包括[采样选项](./functions-monitoring.md#configure-sampling)。

有关完整的 JSON 结构，请参阅前面的[示例 host.json 文件](#sample-hostjson-file)。

> [!NOTE]
> 日志采样可能会导致一些执行不会显示在 Application Insights 监视器边栏选项卡中。 为了避免日志采样，请添加到`samplingExcludedTypes: "Request"``applicationInsights`值。

| properties | 默认 | 描述 |
| --------- | --------- | --------- | 
| 采样设置 | 不适用 | 请参阅[应用程序见解.采样设置](#applicationinsightssamplingsettings)。 |
| 启用实时测量 | true | 启用实时指标集合。 |
| 启用依赖跟踪 | true | 启用依赖项跟踪。 |
| 启用性能计数器集合 | true | 启用库杜性能计数器集合。 |
| 实时参数初始化延迟 | 00:00:15 | 仅供内部使用。 |
| http 自动收集选项 | 不适用 | 请参阅[应用程序见解.http自动收集选项](#applicationinsightshttpautocollectionoptions)。 |
| 快照配置 | 不适用 | 请参阅[应用程序见解.快照配置](#applicationinsightssnapshotconfiguration)。 |

### <a name="applicationinsightssamplingsettings"></a>应用程序见解.采样设置

|properties | 默认 | 描述 |
| --------- | --------- | --------- | 
| isEnabled | true | 启用或禁用采样。 | 
| maxTelemetryItemsPerSecond | 20 | 每台服务器主机上每秒记录的遥测项的目标数量。 如果应用在许多主机上运行，请减小此值以保持在总体流量目标速率内。 | 
| 评估间隔 | 01:00:00 | 会重新评估当前遥测速率的间隔。 评估以移动平均线形式进行执行。 可能想要缩短此间隔（如果遥测很容易就激增）。 |
| 初始采样百分比| 1.0 | 在采样过程开始时应用的初始采样百分比可动态更改百分比。 不要在调试时减小值。 |
| 采样百分比增加超时 | 00:00:01 | 当采样百分比值发生更改时，此属性确定不久之后允许应用程序见解再次提高采样百分比以捕获更多数据。 |
| 采样百分比减少超时 | 00:00:01 | 当采样百分比值发生更改时，此属性确定不久之后允许应用程序见解再次降低采样百分比以捕获更少的数据。 |
| 最小采样百分比 | 0.1 | 由于采样百分比不同，此属性确定允许的最小采样百分比。 |
| 最大采样百分比 | 0.1 | 由于采样百分比不同，此属性确定允许的最大采样百分比。 |
| 移动平均比率 | 1.0 | 在移动平均线的计算中，权重分配给最新的值。 使用等于或小于 1 的值。 较小的值会使算法不易受突然的更改影响。 |
| 排除类型 | null | 不希望采样类型的分号分隔列表。 已识别的类型包括：Dependency、Event、Exception、PageView、Request、Trace。 传输指定类型的所有实例;对未指定的类型进行采样。 |
| 包括类型 | null | 要采样的类型的分号分隔列表;空列表表示所有类型的。 此处列出的覆盖`excludedTypes`类型中列出的类型。 已识别的类型包括：Dependency、Event、Exception、PageView、Request、Trace。 传输指定类型的所有实例;对未指定的类型进行采样。 |

### <a name="applicationinsightshttpautocollectionoptions"></a>应用程序见解.http自动收集选项

|properties | 默认 | 描述 |
| --------- | --------- | --------- | 
| 启用Httptrigger扩展信息集合 | true | 启用或禁用 HTTP 触发器的扩展 HTTP 请求信息：传入请求关联标头、多检测密钥支持、HTTP 方法、路径和响应。 |
| 启用W3C分布式跟踪 | true | 启用或禁用对 W3C 分布式跟踪协议的支持（并启用旧相关架构）。 默认情况下启用（如果`enableHttpTriggerExtendedInfoCollection`为 true）。 如果`enableHttpTriggerExtendedInfoCollection`为 false，则此标志仅适用于传出请求，不适用于传入请求。 |
| 启用响应标头注入 | true | 启用或禁用将多组件相关标头注入响应。 启用注入允许应用程序见解构造应用程序映射，以何时使用多个检测密钥。 默认情况下启用（如果`enableHttpTriggerExtendedInfoCollection`为 true）。 如果`enableHttpTriggerExtendedInfoCollection`为 false，则此设置不适用。 |

### <a name="applicationinsightssnapshotconfiguration"></a>应用程序见解.快照配置

有关快照的详细信息，请参阅[在 .NET 应用中调试异常的快照](/azure/azure-monitor/app/snapshot-debugger)，以及[启用应用程序见解快照调试器或查看快照的疑难解答问题](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)。

|properties | 默认 | 描述 |
| --------- | --------- | --------- | 
| 代理端点 | null | 用于连接到应用程序见解快照调试器服务的终结点。 如果为 null，则使用默认终结点。 |
| 捕获快照内存权重 | 0.5 | 检查是否有足够的内存拍摄快照时，当前进程内存大小给出的权重。 预期值大于 0 正确的分数（0 <捕获快照内存权重< 1）。 |
| 失败的请求限制 | 3 | 禁用遥测处理器之前请求快照的失败请求数的限制。|
| 句柄 未跟踪的异常 | true | 启用或禁用跟踪应用程序见解遥测未跟踪的异常。 |
| isEnabled | true | 启用或禁用快照集合 | 
| 是启用的开发者模式 | false | 在开发人员模式下启用或禁用快照集合。 |
| 正在启用时分析 | true | 启用或禁用快照创建，即使应用程序见解探查器正在收集详细的分析会话。 |
| 是启用的例外捕捉点 | false | 启用或禁用异常筛选。 |
| 是低优先级快照加载器 | true | 确定是否以低于正常优先级运行快照加载进程。 |
| 最大收集平面大小 | 50 | 我们可以在 1 到 9999 之间随时跟踪的最大问题数。 |
| 所需的最大快照 | 3 | 为单个问题收集的快照的最大数量，范围为 1 到 999。 在应用程序中，问题可能被视为单个引发语句。 一旦为问题收集的快照数达到此值，在重置问题计数器（请参阅`problemCounterResetInterval`）并再次达到`thresholdForSnapshotting`限制之前，将不再为该问题收集快照。 |
| 问题计数器重置间隔 | 24:00:00 | 在一分钟到七天之间重置问题计数器的频率。 达到此间隔时，所有问题计数将重置为零。 已达到执行快照阈值但尚未生成 中的`maximumSnapshotsRequired`快照数的现有问题保持活动状态。 |
| 提供匿名遥测 | true | 确定是否向 Microsoft 发送匿名使用情况和错误遥测。 如果您联系 Microsoft 以帮助解决快照调试器的问题，则可能会使用此遥测数据。 它还用于监视使用模式。 |
| 重新连接间隔 | 00:15:00 | 我们重新连接到快照调试器终结点的频率。 允许的范围为一分钟到一天。 |
| 影子复制文件夹 | null | 指定用于卷影复制二进制文件的文件夹。 如果未设置，则按顺序尝试以下环境变量指定的文件夹：Fabric_Folder_App_Temp、本地应用数据、APPDATA、TEMP。 |
| 共享加载程序 | true | 如果为 true，则只有一个快照 Uploader 实例将收集和上载共享检测密钥的多个应用的快照。 如果设置为 false，则快照 Uploader 对于每个（进程名称、检测键）元组将是唯一的。 |
| 快照在低优先级线程 | true | 确定是否处理低 IO 优先级线程中的快照。 创建快照是一种快速操作，但是，为了将快照上载到快照调试器服务，必须首先将其写入磁盘作为小型转储。 这发生在快照加载器过程中。 将此值设置为 true 使用低优先级 IO 编写小型转储，这不会与您的应用程序竞争资源。 将此值设置为 false 会加快小型转储的创建速度，但代价是减慢应用程序的速度。 |
| 快照PerDay限制 | 30 | 一天（24 小时）内允许的最大快照数。 此限制也在应用程序见解服务端强制执行。 每个应用程序的上载速率限制为每天 50 个（即每个检测密钥）。 此值有助于防止创建其他快照，这些快照最终将在上载期间被拒绝。 值为零将完全删除限制，不建议这样做。 |
| 快照Perten分钟限制 | 1 | 10 分钟内允许的最大快照数。 尽管此值没有上限，但请谨慎增加此值，因为它可能会影响应用程序的性能。 创建快照速度很快，但创建快照的小型转储并将其上载到快照调试器服务是一个速度慢得多的操作，它将与应用程序争夺资源（CPU 和 I/O）。 |
| 临时文件夹 | null | 指定用于编写小型转储和上载器日志文件的文件夹。 如果未设置，则使用 *%TEMP%\转储*。 |
| 用于快照的阈值 | 1 | 应用程序见解在请求快照之前需要查看异常的次数。 |
| 上传器代理 | null | 覆盖快照上载程序进程中使用的代理服务器。 如果您的应用程序通过代理服务器连接到互联网，则可能需要使用此设置。 快照收集器在应用程序的进程中运行，并将使用相同的代理设置。 但是，快照上传程序作为单独的进程运行，您可能需要手动配置代理服务器。 如果此值为空，则快照收集器将尝试通过检查 System.Net.WebRequest.DefaultWebProxy 并将该值传递给快照上传程序来自动检测代理的地址。 如果此值不是空，则不使用自动检测，此处指定的代理服务器将在快照上传器中使用。 |

## <a name="cosmosdb"></a>CosmosDB

可在 [Cosmos DB 触发器和绑定](functions-bindings-cosmosdb-v2-output.md#host-json)中查找配置设置。

## <a name="durabletask"></a>durableTask

可在 [Durable Functions 的绑定](durable/durable-functions-bindings.md#host-json)中查找配置设置。

## <a name="eventhub"></a>eventHub

可在[事件中心触发器和绑定](functions-bindings-event-hubs-output.md#host-json)中查找配置设置。 

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

在高级计划中，有效范围为 1 秒到 60 分钟，默认值为 30 分钟。

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

|properties  |默认 | 描述 |
|---------|---------|---------| 
|已启用|true|指定是否启用此功能。 | 
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
    "fileLoggingMode": "debugOnly"
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

|properties  |默认 | 描述 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|定义启用哪种级别的文件日志记录。  选项包括 `never`、`always` 和 `debugOnly`。 |
|logLevel|不适用|一个对象，它定义了用于筛选应用中的函数的日志类别。 版本 2.x 和更高版本遵循日志类别筛选ASP.NET核心布局。 此设置允许您筛选特定函数的日志记录。 有关详细信息，请参阅 ASP.NET Core 文档中的[日志筛选](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)。 |
|console|不适用| [控制台](#console)日志记录设置。 |
|applicationInsights|不适用| [applicationInsights](#applicationinsights) 设置。 |

## <a name="console"></a>console

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

|properties  |默认 | 描述 |
|---------|---------|---------| 
|isEnabled|false|启用或禁用控制台日志记录。| 

## <a name="manageddependency"></a>managedDependency

托管依赖项是一项功能，目前仅支持基于 PowerShell 的函数。 它使依赖项可以由服务自动管理。 `enabled` 属性设置为 `true` 时，`requirements.psd1` 文件会被处理。 发布任何次要版本时会更新依赖项。 有关详细信息，请参阅 PowerShell 一文中的[托管依赖项](functions-reference-powershell.md#dependency-management)。

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

|properties  |默认 | 描述 |
|---------|---------|---------| 
|lockPeriod|00:00:15|占用函数级锁的时间段。 锁自动续订。| 
|listenerLockPeriod|00:01:00|占用侦听器锁的时间段。| 
|listenerLockRecoveryPollingInterval|00:01:00|在启动时无法获取侦听器锁的情况下，用于恢复侦听器锁的时间间隔。| 
|lockAcquisitionTimeout|00:01:00|运行时尝试获取锁的最长时间。| 
|lockAcquisitionPollingInterval|不适用|尝试获取锁的间隔时间。| 

## <a name="version"></a>版本

此值指示 host.json 的架构版本。 面向 v2 运行时或更高版本的函数应用需要版本字符串`"version": "2.0"`。 v2 和 v3 之间没有 host.json 架构更改。

## <a name="watchdirectories"></a>watchDirectories

应该监视其更改情况的一组[共享代码目录](functions-reference-csharp.md#watched-directories)。  确保当这些目录中的代码发生更改时，函数会拾取这些更改。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何更新 host.json 文件](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [查看环境变量中的全局设置](functions-app-settings.md)
