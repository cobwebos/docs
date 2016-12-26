---
title: "在 Java Web 应用中筛选 Azure Application Insights 遥测 |Microsoft Docs"
description: "筛选出无需监视的事件，减少遥测流量。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 9ade7b48b16d79c23355a8dbd46e9367abe4abd6
ms.openlocfilehash: f2ddd6b02ac0dcf35c2519589f2f439c02c886c9


---
# <a name="filter-telemetry-in-your-java-web-app"></a>在 Java Web 应用中筛选遥测

可通过筛选器选择 [Java Web 应用发送到 Application Insights](app-insights-java-get-started.md) 的遥测。 可使用现成的筛选器，也可编写自己的自定义筛选器。

现成的筛选器包括：

* 跟踪严重性级别
* 特定的 URL、关键字或响应代码
* 快速响应，即快速向应用响应的遥测发出请求
* 特定事件名称

> [!NOTE] 
> 筛选器会使应用的指标产生偏差。 例如，为了诊断缓慢响应时间，你可能会决定设置一个排除快速响应时间的筛选器。 但必须注意，Application Insights 报告的平均响应时间会慢于实际速度，且请求数会小于实际数目。
> 如果这是一个问题，请改用[采样](app-insights-sampling.md)。

## <a name="setting-filters"></a>设置筛选器

在 ApplicationInsights.xml 中，添加 `TelemetryProcessors` 部分，如此例所示：


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources, 
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[检查整套内置处理器](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor)。

## <a name="built-in-filters"></a>内置筛选器

### <a name="metric-telemetry-filter"></a>指标遥测筛选器

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` - 用逗号分隔的自定义指标名称列表。


### <a name="page-view-telemetry-filter"></a>页面视图遥测筛选器

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` - 持续时间，表示加载该页所花的时间。 如果设置了此筛选器，那么页面加载时间快于此时间时就不会报告。 
* `NotNeededNames` - 用逗号分隔的页名称列表。
* `NotNeededUrls` - 用逗号分隔的 URL 分段列表。 例如，`"home"` 可筛选出 URL 中包含“home”的所有页面。


### <a name="request-telemetry-filter"></a>请求遥测筛选器


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>综合源筛选器

筛选出 SyntheticSource 属性中具有值的所有遥测。 其中包括来自动程序、蜘蛛程序和可用性测试的请求。

筛选出针对所有综合请求的遥测：


```XML

           <Processor type="SyntheticSourceFilter" />
```

筛选出针对特定综合源的遥测：


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` - 用逗号分隔的综合源名称列表。

### <a name="telemetry-event-filter"></a>遥测事件筛选器

筛选自定义事件（使用 [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) 记录）。


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` - 用逗号分隔的事件名称列表。


### <a name="trace-telemetry-filter"></a>跟踪遥测筛选器

筛选日志跟踪（使用 [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) 或[记录框架收集器](app-insights-java-trace-logs.md)记录）。

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` 有效值是：
 *  OFF             - 筛选出所有跟踪
 *  TRACE           -不筛选。 等效于 Trace 级别
 *  INFO            - 筛选出 TRACE 级别
 *  WARN            - 筛选出 TRACE 和 INFO
 *  ERROR           - 筛选出 WARN、INFO、TRACE
 *  CRITICAL        - 筛选出除 CRITICAL 外的所有值


```


## Custom filters

### 1. Code your filter

In your code, create a class that implements `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful) 
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2.在配置文件中调用筛选器

在 ApplicationInsights.xml 中：

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>故障排除

我的筛选器不能正常工作。

* 请检查提供的参数值是否有效。 例如，持续时间应为整数。 无效值将导致筛选器被忽略。 如果自定义筛选器从构造函数或 set 方法中引发异常，它将被忽略。

## <a name="next-steps"></a>后续步骤

* [采样](app-insights-sampling.md) - 请考虑将采样作为替代方法，该方法不会使指标出现偏差。




<!--HONumber=Nov16_HO5-->


