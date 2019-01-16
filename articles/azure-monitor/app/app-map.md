---
title: Azure Application Insights 中的应用程序映射 | Microsoft 文档
description: 使用应用程序映射监视复杂的应用程序拓扑
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 238b75681ec525187ea27f60ac8b21b05b13954d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063108"
---
# <a name="application-map-triage-distributed-applications"></a>应用程序映射：会审分布式应用程序

应用程序映射可帮助你发现的性能瓶颈或热点失败的所有组件的分布式应用程序。 在地图上的每个节点表示应用程序组件或其依赖项;并且有运行状况 KPI 和警报状态。 可从任何组件单击以获得更详细的诊断，如 Application Insights 事件。 如果应用使用了 Azure 服务，还可以单击获得 Azure 诊断，如 SQL 数据库顾问建议。

## <a name="what-is-a-component"></a>组件是什么？

组件是分布式/微服务应用程序的可独立部署的部件。 开发者或操作团队具有代码级可见性，或有权访问由这些应用程序组件生成的遥测。 

* 组件与“观察到的”外部依赖项（如 SQL 和 EventHub 等）不同，团队/组织可能无权访问（代码或遥测）。
* 组件在任意数目的服务器/角色/容器实例上运行。
* 组件可以是单独的 Application Insights 检测密钥（即使订阅并不相同），或是向单个 Application Insights 检测密钥报告的不同角色。 预览地图体验显示了组件而不考虑如何设置它们。

## <a name="composite-application-map"></a>复合应用程序映射

你可以跨多个级别的相关应用程序组件中查看完整的应用程序拓扑。 组件可以是不同的 Application Insights 资源或不同的角色在单个资源。 应用映射通过跟踪已安装 Application Insights SDK 的服务器之间进行的任何 HTTP 依赖项调用来查找服务器节点。 

这种体验开头渐进式发现的组件。 当首次加载应用程序映射时，将触发一组查询来发现与此组件相关的组件。 在左上角的按钮将使用更新你的应用程序中的组件数量发现它们。 

单击"更新映射组件"，发现在该点之前的所有组件刷新映射。 这可能要花费一段时间来进行加载，具体取决于应用程序的复杂性。

如果所有组件都是单个的 Application Insights 资源中的角色，则不需要此发现步骤。 这样的应用程序的初始负载将具有所有组件。

![应用程序映射屏幕截图](media/app-map/001.png)

使用此体验的主要目标之一是能够实现可视化效果具有数百个组件的复杂拓扑。

单击以查看相关的见解，转到的性能和失败的该组件的会审体验的任何组件。

![浮出控件](media/app-map/application-map-001.png)

### <a name="investigate-failures"></a>调查故障

选择“调查故障”以启动故障窗格。

![“调查故障”按钮的屏幕截图](media/app-map/investigate-failures.png)

![故障体验的屏幕截图](media/app-map/failures.png)

### <a name="investigate-performance"></a>调查性能

若要排查性能问题，请选择“调查性能”。

![“调查性能”按钮的屏幕截图](media/app-map/investigate-performance.png)

![性能体验的屏幕截图](media/app-map/performance.png)

### <a name="go-to-details"></a>转到详细信息

选择“转到详细信息”可探索端到端事务体验，从而可以查看深入到调用堆栈级别的信息。

![“转到详细信息”按钮的屏幕截图](media/app-map/go-to-details.png)

![端到端事务详细信息的屏幕截图](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>在 Analytics 中查看

若要进一步查询和调查应用程序数据，请单击“在 Analytics 中查看”。

![“在 Analytics 中查看”按钮的屏幕截图](media/app-map/view-in-analytics.png)

![分析体验的屏幕截图](media/app-map/analytics.png)

### <a name="alerts"></a>警报

若要查看活动警报和导致警报触发的基础规则，请选择“警报”。

![“警报”按钮的屏幕截图](media/app-map/alerts.png)

![分析体验的屏幕截图](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>设置 cloud_RoleName

应用程序映射使用 `cloud_RoleName` 属性来标识映射上的组件。 Application Insights SDK 会自动将 `cloud_RoleName` 属性添加到组件发出的遥测数据。 例如，SDK 会将网站名称或服务角色名称添加到 `cloud_RoleName` 属性。 但是，在某些情况下，你可能希望替代默认值。 若要替代 cloud_RoleName 并更改要在应用程序映射上显示的内容，请如下所示进行操作：

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "RoleName";
            }
        }
    }
}
```

**加载初始值设定项**

在 ApplicationInsights.config 中：

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

另一种方法是在代码中实例化初始值设定项，例如在 Global.aspx.cs 中：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js 的替代方法

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

如果你通过 Application Insights Spring Boot 入门版使用 Spring Boot，则唯一需要执行的更改是在 application.properties 文件中为应用程序设置自定义名称。

`spring.application.name=<name-of-app>`

Spring Boot 入门版会自动将 cloudRoleName 分配给你为 spring.application.name 属性输入的值。

有关 Java 关联以及如何为非 SpringBoot 应用程序配置 cloudRoleName 的详细信息，请查看介绍了关联的[此部分](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name)内容。

### <a name="clientbrowser-side-javascript"></a>客户端/浏览器端 JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

有关如何使用遥测数据初始化表达式替代 cloud_RoleName 属性的详细信息，请参阅[添加属性：ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer)。

## <a name="troubleshooting"></a>故障排除

如果无法让应用程序映射按预期方式工作，请尝试以下步骤：

1. 请确保你使用的是官方支持的 SDK。 不受支持的/社区 SDK 可能不支持关联。

    有关受支持的 SDK 的列表，请参考此[文章](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

2. 将所有组件都升级到最新 SDK 版本。

3. 如果将 Azure Functions 与 C# 一起使用，请升级到 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)。

4. 确认 [cloud_RoleName](app-map.md#Set-cloud-RoleName) 已正确配置。

5. 如果缺少某个依赖项，请确保它在[自动收集的依赖项](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)列表中。 如果不在其中，也可以使用某个[跟踪依赖项调用](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)手动跟踪它。

## <a name="portal-feedback"></a>门户反馈
若要提供反馈，请使用反馈选项。

![MapLink-1 图](./media/app-map/14-updated.png)

## <a name="next-steps"></a>后续步骤

* [了解关联](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
