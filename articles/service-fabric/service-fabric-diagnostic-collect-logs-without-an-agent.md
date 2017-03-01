---
title: "直接从 Azure Service Fabric 服务进程收集日志 | Microsoft Azure"
description: "介绍 Service Fabric 应用程序可以将日志直接发送到 Azure Application Insights 或 Elasticsearch 等中心位置，而不依赖于 Azure 诊断代理。"
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: d7f7b157d8d6fb54259c8f23d5005509f4eb7872
ms.openlocfilehash: 22acb6afbfbfff753e71b5e821385798cc76ffdd


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>直接从 Azure Service Fabric 服务进程收集日志
## <a name="in-process-log-collection"></a>进程内日志收集
如果日志源和目标的集合很小，不经常更改，并且在源和目标之间存在简单映射，则对于 **Azure Service Fabric** 服务，使用 [Azure 诊断扩展](service-fabric-diagnostics-how-to-setup-wad.md)收集应用程序日志是一个不错的选择。 如果情况不是这样，一种替代方法是让服务将其日志直接发送到一个中心位置。 此过程被称为**进程内日志收集**，且具有多个潜在优势：

* *易于配置和部署*

    * 诊断数据收集配置只是服务配置的一部分。 将其与应用程序的其余部分始终保持“同步”很简单。
    * 可轻松基于每个应用程序或每个服务进行配置。
        * 基于代理的日志收集通常需要单独部署和配置诊断代理，这是额外的管理任务，也是潜在的错误来源。 通常对于每个虚拟机（节点），只允许一个代理实例，并在所有应用程序和该节点上运行的服务间共享该代理配置。 

* *灵活性*
   
    * 只要客户端库支持目标数据存储系统，应用程序就可以将数据发送至任何需要的地方。 可以根据需要添加新的目标。
    * 可以实现复杂的捕获、筛选和数据聚合规则。
    * 基于代理的日志收集通常受限于代理支持的数据接收器。 有些代理是可扩展的。

* *可访问内部应用程序数据和上下文*
   
    * 应用程序/服务进程内运行的诊断子系统可以轻松地随着上下文信息而扩展跟踪。
    * 借助基于代理的日志收集，必须通过 Windows 事件跟踪之类的某种进程间通信机制将数据发送至代理。 此机制可能造成额外限制。

可以结合这两种收集方法的优点。 事实上，它可能是许多应用程序的最佳解决方案。 基于代理的收集自然成为用于收集与整个群集和各个群集节点相关的日志的解决方案。 对于诊断服务启动问题和崩溃，这种方式比进程内日志收集更可靠。 此外，对于运行在 Service Fabric 群集内的许多服务，每个服务执行其自身进程内日志收集都会导致来自该群集的大量传出连接。 对于网络子系统和日志目标，大量传出连接的负担很重。 [**Azure 诊断**](../cloud-services/cloud-services-dotnet-diagnostics.md)等代理可以从多个服务收集数据，并通过几个连接发送所有数据，从而提高吞吐量。 

本文介绍如何使用 [**EventFlow 开放源代码库**](https://github.com/Azure/diagnostics-eventflow)设置进程内日志收集。 其他库可用于同一用途，但 EventFlow 的优势在于它是专门为进程内日志收集所设计的，支持 Service Fabric 服务。 我们将 [**Azure Application Insights**](https://azure.microsoft.com/services/application-insights/) 用作日志目标。 也支持[**事件中心**](https://azure.microsoft.com/services/event-hubs/)或 [**Elasticsearch**](https://www.elastic.co/products/elasticsearch) 等其他目标。 只需在 EventFlow 配置文件中安装相应的 NuGet 包并配置目标即可。 有关 Application Insights 以外的日志目标的详细信息，请参阅 [EventFlow 文档](https://github.com/Azure/diagnostics-eventflow)。

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>将 EventFlow 库添加到 Service Fabric 服务项目
EventFlow 二进制文件都可用作一组 NuGet 包。 若要将 EventFlow 添加到 Service Fabric 服务项目中，右键单击解决方案资源管理器中的项目，然后选择“管理 NuGet 包”。 切换到“浏览”选项卡，搜索“`Diagnostics.EventFlow`”：

![Visual Studio NuGet 包管理器 UI 中的 EventFlow NuGet 包][1]

托管 EventFlow 的服务应包括相应的包，具体取决于应用程序日志的源和目标。 添加以下包： 

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` 
    * （从服务的 EventSource 类和标准 EventSource 捕获数据，例如 Microsoft-ServiceFabric-Services 和 Microsoft-ServiceFabric-Actors）
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` 
    * （我们会将日志发送到 Azure Application Insights 资源）  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * （允许初始化来自 Service Fabric 服务配置的 EventFlow 管道，报告任何将诊断数据以 Service Fabric 运行状况报告的形式进行发送的问题）

> [!NOTE]
> `Microsoft.Diagnostics.EventFlow.Input.EventSource` 包要求服务项目面向.NET Framework 4.6 或更高版本。 请确保在项目属性中设置相应的目标框架，然后再安装此包。 

安装所有的包后，下一步是在服务中配置和启用 EventFlow。

## <a name="configuring-and-enabling-log-collection"></a>配置和启用日志收集
EventFlow 管道（负责发送日志）根据配置文件中存储的规范创建。 `Microsoft.Diagnostics.EventFlow.ServiceFabric` 包在 `PackageRoot\Config` 解决方案文件夹下安装起始 EventFlow 配置文件。 文件名为 `eventFlowConfig.json`。 需要修改此配置文件，以从默认服务 `EventSource` 类捕获数据并将数据发送到 Application Insights 服务。

> [!NOTE]
> 我们假定你熟悉 **Azure Application Insights** 服务，并且具有计划用于监视 Service Fabric 服务的 Application Insights 资源。 如需详细信息，请参阅[创建 Application Insights 资源](../application-insights/app-insights-create-new-resource.md)。

在编辑器中打开 `eventFlowConfig.json` 文件，并更改其内容，如下所示。 请确保根据备注，替换 ServiceEventSource 名称和 Application Insights 检测密钥。 

```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

> [!NOTE]
> 服务的 ServiceEventSource 名称是应用于 ServiceEventSource 类的 `EventSourceAttribute` 的 Name 属性值。 该值属于服务代码，而且可以在 `ServiceEventSource.cs` 文件中指定。 例如，在以下代码片段中，ServiceEventSource 的名称是 MyCompany-Application1-Stateless1：
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

请注意，`eventFlowConfig.json` 文件属于服务配置包。 对此文件的更改可以包含在服务的完全升级或仅配置的升级中，具体取决于 Service Fabric 升级运行状况检查和自动回退（如果升级失败）。 有关详细信息，请参阅 [Service Fabric 应用程序升级](service-fabric-application-upgrade.md)。

最后一步是在服务的启动代码中（位于 `Program.cs` 文件）实例化 EventFlow 管道。 在以下示例中，与 EventFlow 相关的新增内容标有以 `****` 开头的备注：

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    
                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

作为 `ServiceFabricDiagnosticsPipelineFactory` 中 `CreatePipeline` 方法的参数传递的名称是表示 EventFlow 日志收集管道的运行状况实体的名称。 如果 EventFlow 发生错误，并通过 Service Fabric 运行状况子系统进行报告，则使用此名称。

## <a name="verification"></a>验证
在 Visual Studio 中启动服务，并观察调试输出窗口。 该服务启动后，应该可以开始看到你的服务正在发送“Application Insights 遥测”记录的证据。 打开 Web 浏览器并导航到 Application Insights 资源。 打开“搜索”选项卡（位于默认“概述”边栏选项卡的顶部）。 一小段延迟后，应该可以开始看到 Application Insights 门户中的跟踪：

![显示来自 Service Fabric 应用程序日志的 Application Insights 门户][2]

## <a name="next-steps"></a>后续步骤
* [深入了解如何诊断和监视 Service Fabric 服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [EventFlow 文档](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png


<!--HONumber=Jan17_HO3-->


