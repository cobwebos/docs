---
title: Azure Application Insights 中的应用程序映射 | Microsoft 文档
description: 使用应用程序映射监视复杂的应用程序拓扑
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 305de556cc6e50af21cb38aa1b8073b420efbf43
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887756"
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

这种体验开头渐进式发现的组件。 首次加载应用程序映射时，会触发一组查询来发现与此组件相关的组件。 在左上角的按钮将使用更新你的应用程序中的组件数量发现它们。 

单击"更新映射组件"，发现在该点之前的所有组件刷新映射。 这可能要花费一段时间来进行加载，具体取决于应用程序的复杂性。

如果所有组件都是单个的 Application Insights 资源中的角色，则不需要此发现步骤。 这样的应用程序的初始负载将具有所有组件。

![应用程序映射屏幕截图](media/app-map/app-map-001.png)

使用此体验的主要目标之一是能够实现可视化效果具有数百个组件的复杂拓扑。

单击以查看相关的见解，转到的性能和失败的该组件的会审体验的任何组件。

![浮出控件](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>调查故障

选择“调查故障”以启动故障窗格。

![“调查故障”按钮的屏幕截图](media/app-map/investigate-failures.png)

![故障体验的屏幕截图](media/app-map/failures.png)

### <a name="investigate-performance"></a>调查性能

若要排查性能问题，请选择“调查性能”。

![“调查性能”按钮的屏幕截图](media/app-map/investigate-performance.png)

![性能体验的屏幕截图](media/app-map/performance.png)

### <a name="go-to-details"></a>转到详细信息

选择“转到详细信息”可探索端到端事务体验，从而可以深入到调用堆栈级别的信息。

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

## <a name="set-cloud-role-name"></a>设置云角色名称

应用程序映射使用**云角色名称**属性来标识映射上的组件。 Application Insights SDK 会自动将云角色名称属性添加到组件发出的遥测数据。 例如，SDK 会将网站名称或服务角色名称添加到云角色名称属性。 但是，在某些情况下，你可能希望替代默认值。 若要替代云角色名称并更改要在应用程序映射上显示的内容，请如下所示进行操作：

### <a name="netnet-core"></a>.NET/.NET Core

**按如下所示编写自定义 TelemetryInitializer。**

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
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET apps：将初始值设定项加载到活动 TelemetryConfiguration**

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

ASP.NET Web 应用程序的另一种方法是在代码中（例如在 Global.aspx.cs 中）实例化初始值设定项：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> 使用 `ApplicationInsights.config` 或使用 `TelemetryConfiguration.Active` 添加初始值设定项对于 ASP.NET Core 应用程序无效。 

**ASP.NET Core 应用：将初始值设定项加载到 TelemetryConfiguration**

对于 [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 应用程序，添加新的 `TelemetryInitializer` 是通过将其添加到依赖项注入容器来完成的，如下所示。 这是在 `ConfigureServices` 类的 `Startup.cs` 方法中完成的。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
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

从 Application Insights Java SDK 2.5.0 开始，可以通过将 `<RoleName>` 添加到 `ApplicationInsights.xml` 文件来指定云角色名称，例如

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

如果你通过 Application Insights Spring Boot 入门版使用 Spring Boot，则唯一需要执行的更改是在 application.properties 文件中为应用程序设置自定义名称。

`spring.application.name=<name-of-app>`

Spring Boot 入门版会自动将云角色名称分配给你为 spring.application.name 属性输入的值。

### <a name="clientbrowser-side-javascript"></a>客户端/浏览器端 JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>了解应用程序映射上下文中的云角色名称

以下包含多个云角色名称的应用程序映射可帮助你加深对**云角色名称**的理解：

![应用程序映射屏幕截图](media/app-map/cloud-rolename.png)

在上面的应用程序映射中，绿框中的每个名称是云角色名称值，它们反映了此特定分布式应用程序的不同方面。 因此，此应用的角色由 `Authentication`、`acmefrontend`、`Inventory Management` 和 `Payment Processing Worker Role` 组成。 

对于此应用，其中的每个云角色名称也代表具有自身检测密钥的唯一 Application Insights 资源。 由于此应用程序的所有者有权访问这四个不同 Application Insights 资源中的每一个，因此，应用程序映射可将基础关系的映射拼接到一起。

[官方定义](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)：

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

或者，**云角色实例**可帮助实现以下方案：尽管**云角色名称**会告知问题出现在 Web 前端中的某个位置，但你可能在多个负载均衡的服务器中运行 Web 前端，因此，能够通过 Kusto 查询下钻到更深的层并了解问题是影响所有 Web 前端服务器/实例还是只影响其中的一个服务器/实例可能极其重要。

另一个方案是，应用在容器化环境中运行，仅仅了解单个服务器可能无法获得足够的信息来找出给定的问题，因此需要替代云角色实例的值。

若要详细了解如何用遥测初始化表达式替代 "云角色名称" 属性，请参阅[添加属性： ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)。

## <a name="troubleshooting"></a>故障排除

如果无法让应用程序映射按预期方式工作，请尝试以下步骤：

### <a name="general"></a>常规

1. 请确保你使用的是官方支持的 SDK。 不受支持的/社区 SDK 可能不支持关联。

    有关受支持的 SDK 的列表，请参考此[文章](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

2. 将所有组件都升级到最新 SDK 版本。

3. 如果将 Azure Functions 与 C# 一起使用，请升级到 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)。

4. 确认[云角色名称](#set-cloud-role-name)已正确配置。

5. 如果缺少某个依赖项，请确保它在[自动收集的依赖项](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)列表中。 如果不在其中，也可以使用某个[跟踪依赖项调用](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)手动跟踪它。

### <a name="too-many-nodes-on-the-map"></a>映射中存在过多的节点

应用程序映射为请求遥测中的每个唯一云角色名称构造一个应用程序节点，并为依赖项遥测中的类型、目标和云角色名称的每个唯一组合构造一个依赖项节点。 如果遥测中包含 10,000 个以上的节点，则应用程序映射无法提取所有的节点和链接，因此映射是不完整的。 如果发生这种情况，查看映射时会显示一条警告消息。

此外，应用程序映射最多仅支持一次性呈现的 1000 个独立未分组节点。 应用程序映射将具有相同类型和调用方的依赖项分组到一起，因此可降低了视觉复杂性。但是，如果遥测包含过多的唯一云角色名称或过多的依赖项类型，则这种分组并不足够，并且映射无法呈现。

若要解决此问题，需要更改检测，以正确设置云角色名称、依赖项类型和依赖项目标字段。

* 依赖项目标应代表依赖项的逻辑名称。 在许多情况下，它相当于依赖项的服务器或资源名称。 例如，对于 HTTP 依赖项，其目标将设置为主机名。 依赖项目标不应包含会在不同的请求中发生变化的唯一 ID 或参数。

* 依赖项类型应代表依赖项的逻辑类型。 例如，HTTP、 SQL 或 Azure Blob 就是典型的依赖项类型。 它不应包含唯一 ID。

* [上面的部分](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)介绍了云角色名称的用途。

## <a name="portal-feedback"></a>门户反馈

若要提供反馈，请使用反馈选项。

![MapLink-1 图](./media/app-map/14-updated.png)

## <a name="next-steps"></a>后续步骤

* 若要详细了解相关工作原理 Application Insights 参阅[遥测相关文章](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)。
* [端到端事务诊断体验](transaction-diagnostics.md)将所有 Application Insights 监视的组件中的服务器端遥测关联到一个视图中。
* 有关 ASP.NET Core 和 ASP.NET 中的高级关联方案，请参阅[跟踪自定义操作](custom-operations-tracking.md)一文。
