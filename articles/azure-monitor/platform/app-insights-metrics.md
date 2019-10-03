---
title: 基于 Azure Application Insights 日志的指标 | Microsoft Docs
description: 本文列出了 Azure Application Insights 指标以及支持的聚合与维度。 基于日志的指标的详细信息包括底层 Kusto 查询语句。
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 903fd2309949036b62fb4975596fb645c021d06d
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535033"
---
# <a name="application-insights-log-based-metrics"></a>基于 Application Insights 日志的指标

使用基于 Application Insights 日志的指标可以分析受监视应用的运行状况、创建功能强大的仪表板，以及配置警报。 有两种类型的指标：

* [基于日志的指标](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)在幕后转换为存储的事件中的 [Kusto 查询](https://docs.microsoft.com/azure/kusto/query/)。
* [标准指标](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)存储为预先聚合的时序。

由于标准指标在收集期间会预先聚合，因此它们在查询时具有更好的性能。 因此它们更适合在仪表板和实时警报中显示。 基于日志的指标具有更多的维度，因此，它们特别适合用于数据分析和即席诊断。 在[指标资源管理器](metrics-getting-started.md)中使用[命名空间选择器](metrics-getting-started.md#create-your-first-metric-chart)可以在基于日志的指标与标准指标之间切换。

## <a name="interpret-and-use-queries-from-this-article"></a>本文中的查询的解释和用法

本文列出了指标以及支持的聚合与维度。 基于日志的指标的详细信息包括底层 Kusto 查询语句。 为方便起见，每个查询对时间粒度、图表类型使用默认值，有时还会拆分维度，这简化了查询在 Log Analytics 中的使用，而无需进行任何修改。

在[指标资源管理器](metrics-getting-started.md)中绘制同一指标的图表时，不会使用默认值 - 查询会根据图表设置动态调整：

- 所选的“时间范围”将转换为额外的 *where timestamp ...* 子句，以便仅选取所选时间范围内的事件。 例如，对于显示最近 24 小时数据的图表，查询将包含 *| where timestamp > ago(24 h)* 。

- 所选**时间粒度**会置于最终*汇总 ...by bin (timestamp, [时间粒度])* 子句。

- 任何所选“筛选器”维度将转换为额外的 *where* 子句。

- 所选的“拆分图表”维度将转换为额外的 summarize 属性。 例如, 如果按*位置*拆分图表, 并使用5分钟的时间粒度进行绘制, 则*汇总子句汇总* *...按 bin (时间戳、5 m)、位置*。

> [!NOTE]
> 如果你不熟悉 Kusto 查询语言，请先复制 Kusto 语句并将其粘贴到 Log Analytics 查询窗格，而无需进行任何修改。 单击“运行”查看基本图表。 对查询语言的语法有一定的了解后，可以开始进行少量的修改，并查看更改造成的影响。 探索自己的数据是开始实现 [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) 和 [Azure Monitor](../../azure-monitor/overview.md) 的全部功能的好办法。

## <a name="availability-metrics"></a>可用性指标

参考“可用性”类别中的指标可以了解在世界各地预测到的 Web 应用程序运行状况。 [配置可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)，以开始使用此类别中的任何指标。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性 (availabilityResults/availabilityPercentage)
“可用性”指标显示未检测到任何问题的 Web 测试运行的百分比。 可能的最小值为 0，表示所有 Web 测试运行均失败。 值 100 表示所有 Web 测试运行都符合验证条件。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|百分比|Average|运行位置、测试名称|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>可用性测试持续时间 (availabilityResults/duration)

“可用性测试持续时间”指标显示运行 Web 测试所花费的时间。 对于[多步骤 Web 测试](../../azure-monitor/app/availability-multistep.md)，该指标反映所有步骤的总执行时间。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|毫秒|Average、Min、Max|运行位置、测试名称、测试结果

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>可用性测试 (availabilityResults/count)

“可用性测试”指标反映 Azure Monitor 执行的 Web 测试运行计数。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|Count|Count|运行位置、测试名称、测试结果|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>浏览器指标

浏览器指标由 Application Insights JavaScript SDK 从实际的最终用户浏览器收集。 它们提供 Web 应用用户体验的深入见解。 通常不会对浏览器指标采样，这意味着，在用量数字方面，与服务器端指标相比，它们提供的精度更高，而后者的采样可能会导致结果有偏差。

> [!NOTE]
> 若要收集浏览器指标, 必须通过[Application Insights JAVASCRIPT SDK](../../azure-monitor/app/javascript.md)来检测你的应用程序。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>浏览器页面加载时间 (browserTimings/totalDuration)

|度量单位|支持的聚合|预先聚合的维度|
|---|---|---|
|毫秒|Average、Min、Max|无|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>客户端处理时间 (browserTiming/processingDuration)

|度量单位|支持的聚合|预先聚合的维度|
|---|---|---|
|毫秒|Average、Min、Max|None|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>页面加载网络连接时间 (browserTimings/networkDuration)

|度量单位|支持的聚合|预先聚合的维度|
|---|---|---|
|毫秒|Average、Min、Max|None|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>接收响应时间 (browserTimings/receiveDuration)

|度量单位|支持的聚合|预先聚合的维度|
|---|---|---|
|毫秒|Average、Min、Max|None|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>发送请求时间 (browserTimings/sendDuration)

|度量单位|支持的聚合|预先聚合的维度|
|---|---|---|
|毫秒|Average、Min、Max|None|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>失败指标

“失败”中的指标显示处理请求、依赖项调用的问题，以及引发的异常。

### <a name="browser-exceptions-exceptionsbrowser"></a>浏览器异常数 (exceptions/browser)

此指标反映浏览器中运行的应用程序代码引发的异常数。 该指标仅包含使用 ```trackException()``` Application Insights API 调用跟踪的异常。

|度量单位|支持的聚合|预先聚合的维度|说明|
|---|---|---|---|
|Count|Count|无|基于日志的版本使用 **Sum** 聚合|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>依赖项调用失败数 (dependencies/failed)

失败的依赖项调用数。

|度量单位|支持的聚合|预先聚合的维度|说明|
|---|---|---|---|
|Count|Count|None|基于日志的版本使用 **Sum** 聚合|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>异常数 (exceptions/count)

每当你将异常记录到 Application Insights 时，都会调用 SDK 的 [trackException() 方法](../../azure-monitor/app/api-custom-events-metrics.md#trackexception)。 “异常数”指标显示记录的异常数。

|度量单位|支持的聚合|预先聚合的维度|说明|
|---|---|---|---|
|Count|Count|云角色名称、云角色实例、设备类型|基于日志的版本使用 **Sum** 聚合|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>失败的请求数 (请求/失败)

标记为失败的受跟踪服务器请求计数。 默认情况下，Application Insights SDK 会自动将返回 HTTP 响应代码 5xx 或 4xx 的每个服务器请求标记为失败的请求。 可以通过在[自定义遥测初始化表达式](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer)中修改请求遥测项的 *success* 属性来自定义此逻辑。

|度量单位|支持的聚合|预先聚合的维度|说明|
|---|---|---|---|
|Count|Count|云角色实例、云角色名称、实际或综合流量、请求性能、响应代码|基于日志的版本使用 **Sum** 聚合|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>服务器异常数 (exceptions/server)

此指标显示服务器异常数。

|度量单位|支持的聚合|预先聚合的维度|说明|
|---|---|---|---|
|Count|Count|云角色名称、云角色实例|基于日志的版本使用 **Sum** 聚合|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>性能计数器

使用“性能计数器”类别中的指标可以访问 [Application Insights 收集的系统性能计数器](../../azure-monitor/app/performance-counters.md)。

### <a name="available-memory-performancecountersavailablememory"></a>可用内存 (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>异常率 (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 请求执行时间 (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 请求速率 (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>应用程序队列中的 HTTP 请求数 (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>进程 CPU (performanceCounters/processCpuPercentage)

该指标显示托管受监视应用的进程消耗的处理器总容量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|百分比|Average、Min、Max|云角色实例

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>进程 IO 速率 (performanceCounters/processIOBytesPerSecond)

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|每秒字节数|Average、Min、Max|云角色实例

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>进程专用字节数 (performanceCounters/processPrivateBytes)

受监视进程为其数据分配的非共享内存量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|字节|Average、Min、Max|云角色实例

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>处理器时间 (performanceCounters/processorCpuPercentage)

受监视服务器实例上运行的所有进程的 CPU 消耗量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|百分比|Average、Min、Max|云角色实例

>[!NOTE]
> 处理器时间指标不适用于 Azure 应用服务中托管的应用程序。 使用 [进程 CPU](#process-cpu-performancecountersprocesscpupercentage) 指标可以跟踪应用服务中托管的 Web 应用程序的 CPU 利用率。

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>服务器指标

### <a name="dependency-calls-dependenciescount"></a>依赖项调用数 (dependencies/count)

此指标与依赖项调用数相关。

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>依赖项持续时间 (dependencies/duration)

此指标是指依赖项调用的持续时间。

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>服务器请求数 (requests/count)

此指标反映 Web 应用程序收到的传入服务器请求数。

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>服务器响应时间 (requests/duration)

此指标反映服务器处理传入请求所花费的时间。

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>使用指标

### <a name="page-view-load-time-pageviewsduration"></a>页面查看次数加载时间 (pageViews/duration)

此指标是指加载 PageView 事件所花费的时间。

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>页面查看次数 (pageViews/count)

使用 TrackPageView () Application Insights API 记录的 PageView 事件计数。

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>会话数 (sessions/count)

此指标是指不同会话 ID 的计数。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>跟踪数 (traces/count)

使用 TrackTrace () Application Insights API 调用记录的跟踪语句计数。

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>用户数 (users/count)

访问应用程序的不同用户的数目。 使用遥测采样和筛选可能会显著影响此指标的准确性。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>经过身份验证的用户数 (users/authenticated)

在应用程序中进行身份验证的不同用户的数目。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
