---
title: "Azure Application Insights 数据模型 | Microsoft Docs"
description: "介绍在 JSON 中通过连续导出导出的、用作筛选器的属性。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 3084bb344976bc542e78a55a1c27c4dedc111af0
ms.contentlocale: zh-cn
ms.lasthandoff: 04/12/2017


---
# <a name="application-insights-export-data-model"></a>Application Insights 导出数据模型
此表列出了从 [Application Insights](app-insights-overview.md) SDK 发送到门户的遥测属性。
[连续导出](app-insights-export-telemetry.md)的数据输出中会显示这些属性。
这些属性还显示在[指标资源管理器](app-insights-metrics-explorer.md)和[诊断搜索](app-insights-diagnostic-search.md)的属性筛选器中。

需要注意的要点：

* 这些表中的 `[0]` 表示必须在其中插入索引的路径中的一个点，但它不一定总为 0。
* 持续时间的单位为微秒，因此 10000000 == 1 秒。
* 日期和时间采用 UTC，以 ISO 格式 `yyyy-MM-DDThh:mm:ss.sssZ` 表示


## <a name="example"></a>示例
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>上下文
所有类型的遥测随附上下文部分。 并非所有字段都连同每个数据点传输。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| context.custom.dimensions [0] |object [ ] |自定义属性参数设置的键-值字符串对。 键的最大长度为 100，值的最大长度为 1024。 如果唯一值超过 100 个，属性可搜索，但不可用于分段。 每个 ikey 最多有 200 个键。 |
| context.custom.metrics [0] |object [ ] |自定义测量参数和 TrackMetrics 设置的键-值对。 键的最大长度为 100，值可以是数字。 |
| context.data.eventTime |字符串 |UTC |
| context.data.isSynthetic |布尔值 |请求似乎来自 Bot 或 Web 测试。 |
| context.data.samplingRate |数字 |SDK 生成的、发送到门户的遥测百分比。 范围为 0.0-100.0。 |
| context.device |对象 |客户端设备 |
| context.device.browser |字符串 |IE、Chrome... |
| context.device.browserVersion |字符串 |Chrome 48.0... |
| context.device.deviceModel |字符串 | |
| context.device.deviceName |字符串 | |
| context.device.id |字符串 | |
| context.device.locale |字符串 |en-GB、de-DE... |
| context.device.network |字符串 | |
| context.device.oemName |字符串 | |
| context.device.osVersion |字符串 |主机 OS |
| context.device.roleInstance |字符串 |服务器主机的 ID |
| context.device.roleName |字符串 | |
| context.device.type |字符串 |电脑、浏览器... |
| context.location |对象 |派生自 clientip。 |
| context.location.city |字符串 |派生自 clientip（如果已知） |
| context.location.clientip |字符串 |最后一个八边形匿名化为 0。 |
| context.location.continent |字符串 | |
| context.location.country |字符串 | |
| context.location.province |字符串 |州或省 |
| context.operation.id |字符串 |具有相同操作 ID 的项在门户中显示为相关项。 通常为请求 ID。 |
| context.operation.name |字符串 |URL 或请求名称 |
| context.operation.parentId |字符串 |允许嵌套的相关项。 |
| context.session.id |字符串 |一组来自相同源的操作的 ID。 如果在 30 分钟期限内没有操作，则表示会话结束。 |
| context.session.isFirst |布尔值 | |
| context.user.accountAcquisitionDate |字符串 | |
| context.user.anonAcquisitionDate |字符串 | |
| context.user.anonId |字符串 | |
| context.user.authAcquisitionDate |字符串 |[经过身份验证的用户](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |布尔值 | |
| internal.data.documentVersion |字符串 | |
| internal.data.id |字符串 | |

## <a name="events"></a>事件
[TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 生成的自定义事件。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| event [0] count |integer |100/([采样](app-insights-sampling.md)率)。 例如 4 =&gt; 25%。 |
| event [0] name |字符串 |事件名称。  最大长度为 250。 |
| event [0] url |字符串 | |
| event [0] urlData.base |字符串 | |
| event [0] urlData.host |字符串 | |

## <a name="exceptions"></a>异常
报告服务器和浏览器中发生的[异常](app-insights-asp-net-exceptions.md)。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| basicException [0] assembly |字符串 | |
| basicException [0] count |integer |100/([采样](app-insights-sampling.md)率)。 例如 4 =&gt; 25%。 |
| basicException [0] exceptionGroup |字符串 | |
| basicException [0] exceptionType |字符串 | |
| basicException [0] failedUserCodeMethod |字符串 | |
| basicException [0] failedUserCodeAssembly |字符串 | |
| basicException [0] handledAt |字符串 | |
| basicException [0] hasFullStack |布尔值 | |
| basicException [0] id |字符串 | |
| basicException [0] method |字符串 | |
| basicException [0] message |字符串 |异常消息。 最大长度为 10k。 |
| basicException [0] outerExceptionMessage |字符串 | |
| basicException [0] outerExceptionThrownAtAssembly |字符串 | |
| basicException [0] outerExceptionThrownAtMethod |字符串 | |
| basicException [0] outerExceptionType |字符串 | |
| basicException [0] outerId |字符串 | |
| basicException [0] parsedStack [0] assembly |字符串 | |
| basicException [0] parsedStack [0] fileName |字符串 | |
| basicException [0] parsedStack [0] level |integer | |
| basicException [0] parsedStack [0] line |integer | |
| basicException [0] parsedStack [0] method |字符串 | |
| basicException [0] stack |字符串 |最大长度为 10k |
| basicException [0] typeName |字符串 | |

## <a name="trace-messages"></a>跟踪消息
由 [TrackTrace](app-insights-api-custom-events-metrics.md#tracktrace) 和[日志记录适配器](app-insights-asp-net-trace-logs.md)发送。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| message [0] loggerName |字符串 | |
| message [0] parameters |字符串 | |
| message [0] raw |字符串 |日志消息，最大长度为 10k。 |
| message [0] severityLevel |字符串 | |

## <a name="remote-dependency"></a>远程依赖项
由 TrackDependency 发送。 用于报告服务器中[依赖项调用](app-insights-asp-net-dependencies.md)以及浏览器中 AJAX 调用的性能和用法。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| remoteDependency [0] async |布尔值 | |
| remoteDependency [0] baseName |字符串 | |
| remoteDependency [0] commandName |字符串 |例如“home/index” |
| remoteDependency [0] count |integer |100/([采样](app-insights-sampling.md)率)。 例如 4 =&gt; 25%。 |
| remoteDependency [0] dependencyTypeName |字符串 |HTTP、SQL... |
| remoteDependency [0] durationMetric.value |数字 |从依赖项调用到完成响应花费的时间 |
| remoteDependency [0] id |字符串 | |
| remoteDependency [0] name |字符串 |URL。 最大长度为 250。 |
| remoteDependency [0] resultCode |字符串 |源 HTTP 依赖项 |
| remoteDependency [0] success |布尔值 | |
| remoteDependency [0] type |字符串 |Http、Sql... |
| remoteDependency [0] url |字符串 |最大长度为 2000 |
| remoteDependency [0] urlData.base |字符串 |最大长度为 2000 |
| remoteDependency [0] urlData.hashTag |字符串 | |
| remoteDependency [0] urlData.host |字符串 |最大长度为 200 |

## <a name="requests"></a>请求
由 [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest) 发送。 标准模块使用此属性报告在服务器上测量的服务器响应时间。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| request [0] count |integer |100/([采样](app-insights-sampling.md)率)。 例如：4 =&gt; 25%。 |
| request [0] durationMetric.value |数字 |从请求到响应花费的时间。 1e7 == 1s |
| request [0] id |字符串 |操作 ID |
| request [0] name |字符串 |GET/POST + URL 基。  最大长度为 250 |
| request [0] responseCode |integer |发送到客户端的 HTTP 响应 |
| request [0] success |布尔值 |默认值 == (responseCode &lt; 400) |
| request [0] url |字符串 |不包括主机 |
| request [0] urlData.base |字符串 | |
| request [0] urlData.hashTag |字符串 | |
| request [0] urlData.host |字符串 | |

## <a name="page-view-performance"></a>页面视图性能
由浏览器发送。 测量处理页面花费的时间，从用户发起请求算起，到显示完成（不包括异步 AJAX 调用）为止。

上下文值显示客户端 OS 和浏览器版本。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |integer |从接收 HTML 完成到显示页面花费的时间。 |
| clientPerformance [0] name |字符串 | |
| clientPerformance [0] networkConnection.value |integer |建立网络连接花费的时间。 |
| clientPerformance [0] receiveRequest.value |integer |从发送请求结束到在回复中接收 HTML 所花费的时间。 |
| clientPerformance [0] sendRequest.value |integer |发送 HTTP 请求花费的时间。 |
| clientPerformance [0] total.value |integer |从开始发送请求到显示页面花费的时间。 |
| clientPerformance [0] url |字符串 |此请求的 URL |
| clientPerformance [0] urlData.base |字符串 | |
| clientPerformance [0] urlData.hashTag |字符串 | |
| clientPerformance [0] urlData.host |字符串 | |
| clientPerformance [0] urlData.protocol |字符串 | |

## <a name="page-views"></a>页面视图
由 trackPageView() 或 [stopTrackPage](app-insights-api-custom-events-metrics.md#page-views) 发送

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| view [0] count |integer |100/([采样](app-insights-sampling.md)率)。 例如 4 =&gt; 25%。 |
| view [0] durationMetric.value |integer |在 trackPageView() 中设置的，或者由 startTrackPage() - stopTrackPage() 设置的可选值。 与 clientPerformance 值不同。 |
| view [0] name |字符串 |页面标题。  最大长度为 250 |
| view [0] url |字符串 | |
| view [0] urlData.base |字符串 | |
| view [0] urlData.hashTag |字符串 | |
| view [0] urlData.host |字符串 | |

## <a name="availability"></a>可用性
报告[可用性 Web 测试](app-insights-monitor-web-app-availability.md)。

| 路径 | 类型 | 说明 |
| --- | --- | --- |
| availability [0] availabilityMetric.name |字符串 |availability |
| availability [0] availabilityMetric.value |数字 |1.0 或 0.0 |
| availability [0] count |integer |100/([采样](app-insights-sampling.md)率)。 例如 4 =&gt; 25%。 |
| availability [0] dataSizeMetric.name |字符串 | |
| availability [0] dataSizeMetric.value |integer | |
| availability [0] durationMetric.name |字符串 | |
| availability [0] durationMetric.value |数字 |测试持续时间。 1e7==1s |
| availability [0] message |字符串 |故障诊断 |
| availability [0] result |字符串 |通过/失败 |
| availability [0] runLocation |字符串 |http 请求的地域源 |
| availability [0] testName |字符串 | |
| availability [0] testRunId |字符串 | |
| availability [0] testTimestamp |字符串 | |

## <a name="metrics"></a>度量值
由 TrackMetric() 生成。

指标值出现在 context.custom.metrics[0] 中

例如：

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>关于指标值
指标报告和其他位置中的指标值是使用标准对象结构报告的。 例如：

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

目前（将来可能会变化）在所有通过标准 SDK 模块报告的值中，只有 `count==1` 以及 `name` 和 `value` 字段有用。 这些元素存在差异的唯一情况是你编写自己的 TrackMetric 调用，并在其中设置其他参数。

其他字段的用途是便于在 SDK 中聚合指标，减少门户接收的流量。 例如，在发送每份指标报告之前，可求多个连续读数的平均值。 然后计算最小值、最大值、标准偏差和聚合值（总和或平均值），并将计数设置为报告呈现的读数数目。

上表中省略了一些极少用到的字段：count、min、max、stdDev 和 sampledValue。

如果需要减少遥测量，可以改用[采样](app-insights-sampling.md)，而不要使用预先聚合的指标。

### <a name="durations"></a>持续时间
除非另有规定，否则持续时间以十分之一微秒表示，因此 10000000.0 表示 1 秒。

## <a name="see-also"></a>另请参阅
* [Application Insights](app-insights-overview.md)
* [连续导出](app-insights-export-telemetry.md)
* [代码示例](app-insights-export-telemetry.md#code-samples)

