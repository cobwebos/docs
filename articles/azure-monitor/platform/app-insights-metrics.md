---
title: 基于 Azure 应用程序 Insights 日志的指标 |Microsoft Docs
description: 本文列出了支持的聚合和维度 Azure 应用程序见解指标。 基于日志的指标的详细信息包括底层 Kusto 查询语句。
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664909"
---
# <a name="application-insights-log-based-metrics"></a>基于日志的指标 Application Insights

Application Insights 基于日志的指标，可让你分析受监视应用的运行状况，创建功能强大的仪表板，并配置警报。 有两种指标：

* 场景后面[基于日志的指标](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)将从存储的事件转换为[Kusto 查询](https://docs.microsoft.com/azure/kusto/query/)。
* [标准指标](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)存储为预先聚合的时间序列。

由于*标准指标*是在收集过程中预聚合的，因此它们在查询时具有更好的性能。 这使它们成为仪表板建设和实时警报的更好选择。 *基于日志的指标*具有更多的维度，这使它们成为数据分析和即席诊断的高级选项。 使用[命名空间选择器](metrics-getting-started.md#create-your-first-metric-chart)在[指标资源管理器](metrics-getting-started.md)中切换基于日志的指标和标准指标。

## <a name="interpret-and-use-queries-from-this-article"></a>解释并使用本文中的查询

本文列出了支持的聚合和维度的指标。 基于日志的指标的详细信息包括底层 Kusto 查询语句。 为方便起见，每个查询使用时间粒度、图表类型和有时拆分维度的默认值，这简化了在 Log Analytics 无需修改的情况下使用查询。

在[指标资源管理器](metrics-getting-started.md)中绘制相同的指标时，没有默认值-根据图表设置动态调整查询：

- 所选**时间范围**将转换为其他*where timestamp ...* 子句，以仅从所选时间范围选取事件。 例如，显示最近24小时内的数据的图表，该查询包括 *| where timestamp > 之前（24 h）* 。

- 所选**时间粒度**会置于最终*汇总 .。。by bin （timestamp，[时间粒度]）* 子句。

- 所有选定的**筛选器**维度都转换为其他*where*子句。

- 所选**拆分图表**维度转换为额外的汇总属性。 例如，如果按*位置*拆分图表，并使用5分钟的时间粒度进行绘制 *，则汇总子句汇总* *.。。按 bin （时间戳、5 m）、位置*。

> [!NOTE]
> 如果你不熟悉 Kusto 查询语言，则首先将 Kusto 语句复制并粘贴到 Log Analytics 查询窗格，而不做任何修改。 单击 "**运行**" 以查看基本图表。 当你开始了解查询语言的语法时，你可以开始进行少量的修改并查看更改的影响。 浏览你自己的数据是开始实现[Log Analytics](../../azure-monitor/log-query/get-started-portal.md)和[Azure Monitor](../../azure-monitor/overview.md)的全部功能的好办法。

## <a name="availability-metrics"></a>可用性指标

可用性类别中的指标使你可以查看 web 应用程序的运行状况，如世界各地所述。 [配置可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)，开始使用此类别中的任何指标。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性（availabilityResults/availabilityPercentage）
*可用性*指标显示未检测到任何问题的 web 测试运行的百分比。 可能的最小值为0，表示所有 web 测试运行均已失败。 100的值表示所有 web 测试都通过了验证条件。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|百分比|平均值|运行位置、测试名称|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>可用性测试持续时间（availabilityResults/duration）

"*可用性测试持续时间*" 指标显示运行 web 测试所花的时间。 对于[多步骤 web 测试](../../azure-monitor/app/availability-multistep.md)，指标反映所有步骤的总执行时间。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|毫秒|平均值、最小值、最大值|运行位置、测试名称、测试结果

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>可用性测试（availabilityResults/count）

"*可用性测试*" 指标反映 Azure Monitor 运行的 web 测试的计数。

|度量单位|支持的聚合|支持的维度|
|---|---|---|---|---|---|
|Count|Count|运行位置、测试名称、测试结果|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>浏览器指标

浏览器指标由真实最终用户浏览器 Application Insights 的 JavaScript SDK 收集。 它们可让用户更深入地了解你的 web 应用程序的体验。 通常不会对浏览器度量值进行采样，这意味着它们与服务器端度量值相比，它们的使用速率是更高的精度，而这可能会通过采样进行扭曲。

> [!NOTE]
> 若要收集浏览器指标，必须通过[Application Insights JAVASCRIPT SDK](../../azure-monitor/app/javascript.md)来检测你的应用程序。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>浏览器页面加载时间（browserTimings/totalDuration）

|度量单位|支持的聚合|预聚合维度|
|---|---|---|
|毫秒|平均值、最小值、最大值|无|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>客户端处理时间（browserTiming/processingDuration）

|度量单位|支持的聚合|预聚合维度|
|---|---|---|
|毫秒|平均值、最小值、最大值|无|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>页面加载网络连接时间（browserTimings/networkDuration）

|度量单位|支持的聚合|预聚合维度|
|---|---|---|
|毫秒|平均值、最小值、最大值|无|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>接收响应时间（browserTimings/receiveDuration）

|度量单位|支持的聚合|预聚合维度|
|---|---|---|
|毫秒|平均值、最小值、最大值|无|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Send request time （browserTimings/sendDuration）

|度量单位|支持的聚合|预聚合维度|
|---|---|---|
|毫秒|平均值、最小值、最大值|无|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>故障指标

**故障**中的指标显示处理请求、依赖项调用和引发的异常的问题。

### <a name="browser-exceptions-exceptionsbrowser"></a>浏览器异常（异常/浏览器）

此指标反映在浏览器中运行的应用程序代码引发的异常的数量。 度量值仅包括使用 ```trackException()``` Application Insights API 调用跟踪的异常。

|度量单位|支持的聚合|预聚合维度|说明|
|---|---|---|---|
|Count|Count|无|基于日志的版本使用**Sum**聚合|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>依赖项调用失败（依赖关系/失败）

失败的依赖项调用数。

|度量单位|支持的聚合|预聚合维度|说明|
|---|---|---|---|
|Count|Count|无|基于日志的版本使用**Sum**聚合|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>异常（异常/计数）

每次将异常记录到 Application Insights 时，都会调用 SDK 的[trackException （）方法](../../azure-monitor/app/api-custom-events-metrics.md#trackexception)。 异常指标显示记录的异常的数量。

|度量单位|支持的聚合|预聚合维度|说明|
|---|---|---|---|
|Count|Count|云角色名称、云角色实例、设备类型|基于日志的版本使用**Sum**聚合|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>失败的请求数（请求/失败）

标记为*失败*的跟踪服务器请求的计数。 默认情况下，Application Insights SDK 会自动将返回 HTTP 响应代码5xx 或4xx 的每个服务器请求标记为失败的请求。 可以通过在[自定义遥测初始值设定](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)项中修改请求遥测项的*success*属性来自定义此逻辑。

|度量单位|支持的聚合|预聚合维度|说明|
|---|---|---|---|
|Count|Count|云角色实例，云角色名称，实际或综合流量，请求性能，响应代码|基于日志的版本使用**Sum**聚合|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>服务器异常（异常/服务器）

此指标显示服务器异常的数目。

|度量单位|支持的聚合|预聚合维度|说明|
|---|---|---|---|
|Count|Count|云角色名称、云角色实例|基于日志的版本使用**Sum**聚合|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>性能计数器

使用**性能计数器**类别中的度量值来访问[Application Insights 收集的系统性能计数器](../../azure-monitor/app/performance-counters.md)。

### <a name="available-memory-performancecountersavailablememory"></a>可用内存（performanceCounters/availableMemory）

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>异常率（performanceCounters/exceptionRate）

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 请求执行时间（performanceCounters/requestExecutionTime）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 请求速率（performanceCounters/requestsPerSecond）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>应用程序队列中的 HTTP 请求（performanceCounters/requestsInQueue）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>进程 CPU （performanceCounters/processCpuPercentage）

指标显示托管应用的进程所消耗的总处理器容量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|百分比|平均值、最小值、最大值|云角色实例

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>进程 IO 速率（performanceCounters/processIOBytesPerSecond）

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|每秒字节数|平均值、最小值、最大值|云角色实例

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>处理专用字节数（performanceCounters/processPrivateBytes）

监视的进程为其数据分配的非共享内存量。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|字节|平均值、最小值、最大值|云角色实例

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>处理器时间（performanceCounters/processorCpuPercentage）

监视的服务器实例上运行的*所有*进程的 CPU 使用率。

|度量单位|支持的聚合|支持的维度|
|---|---|---|
|百分比|平均值、最小值、最大值|云角色实例

>[!NOTE]
> 处理器时间指标不适用于 Azure 应用服务中托管的应用程序。 使用[进程 CPU](#process-cpu-performancecountersprocesscpupercentage)指标跟踪应用服务中托管的 web 应用程序的 CPU 使用率。

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>服务器指标

### <a name="dependency-calls-dependenciescount"></a>依赖项调用（依赖项/计数）

此指标与依赖项调用的数目有关。

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>依赖项持续时间（依赖项/持续时间）

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

### <a name="server-requests-requestscount"></a>服务器请求数（请求数/计数）

此指标反映了你的 web 应用程序收到的传入服务器请求数。

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>服务器响应时间（请求/持续时间）

此指标反映了服务器处理传入请求所需的时间。

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

### <a name="page-view-load-time-pageviewsduration"></a>页面视图加载时间（pageViews/duration）

此指标是指加载 PageView 事件所花的时间量。

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

### <a name="page-views-pageviewscount"></a>页面视图（pageViews/count）

通过 TrackPageView （） Application Insights API 记录的 PageView 事件的计数。

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>会话（会话数/计数）

此指标是指不同会话 Id 的计数。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>跟踪（跟踪/计数）

通过 TrackTrace （） Application Insights API 调用记录的跟踪语句的计数。

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>用户（用户/计数）

访问应用程序的不同用户的数量。 使用遥测采样和筛选可能会显著影响此指标的准确性。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>经过身份验证的用户（用户/身份验证）

在应用程序中进行身份验证的不同用户的数量。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
