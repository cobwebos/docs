<properties
   pageTitle="为你的 Service Fabric 应用程序设置 Application Insights"
   description="在 Application Insights 中接收你应用程序的 Service Fabric 事件。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/20/2015"
   wacn.date=""/>

# 为你的 Service Fabric 应用程序设置 Application Insights
 本文将指导你为你的 Service Fabric 应用程序启用 Application Insights。

## 先决条件

本文假设你已在 Visual Studio 中创建了 Service Fabric 应用程序。若要查明方式，[请单击此处](/documentation/articles/service-fabric-reliable-services-quick-start)。

## 安装 NuGet 包
作为 Service Fabric 的一部分发布，SDK 是我们 NuGet 程序包 Microsoft.ServiceFabric.Telemetry.ApplicationInsights 的预发布版本。
此包将 Service Fabric EventSource 事件与 Application Insights 联系在一起，提供自动检测 Service Fabric 应用的功能。此包将用新事件继续更新，应用程序将自动发出这些新事件。

你可以通过以下步骤安装包：

1. 打开 Service Fabric 应用的 NuGet 程序包管理器。可以通过在 Visual Studio 中右键单击你的项目并选择“管理 NuGet 包...”来执行此操作。
2. 你将需要选择“Microsoft Azure Service Fabric”作为你的包源以列出 Service Fabric SDK 中包含的包。    
![VS2015 NuGet 程序包管理器](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. 选择左侧的 Microsoft.ServiceFabric.Telemetry.ApplicationInsights 包。
4. 单击“安装”开始安装过程。
5. 查看并接受 EULA。

## 启用 Service Fabric 事件
为了在 Application Insights 中自动接收 Service Fabric 事件，你将需要启用我们的侦听器。可以通过将以下代码行插入你的应用来实现此操作。

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### StatefulActor\\Program.cs 的示例：

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

你可以在[此处](/documentation/articles/service-fabric-reliable-actors-diagnostics)了解有关 Reliable Actors 运行时发出的事件，在[此处](/documentation/articles/service-fabric-reliable-services-diagnostics)了解有关 Reliable Services 运行时发出的事件。

注意，为了获得 Reliable Actors 运行时方法调用，必须使用 EventLevel.Verbose（如上面的示例所示）。

## 设置 Application Insights
检测密钥的作用是将你的 Service Fabric 应用与你的 Application Insights 资源联系在一起。你可以通过遵循 [Application Insights 指南](/documentation/articles/app-insights-create-new-resource#create-an-application-insights-resource)了解如何获取你的检测密钥。
创建资源时，为应用程序类型选择“其他”。

![为 AI 应用类型选择“其他”](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

获取检测密钥之后，可以将其插入如下所示的 ApplicationInsights.config 文件：

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## 查看数据
你可以[自定义 App Insights 边栏选项卡](/documentation/articles/app-insights-metrics-explorer)以满足你的需求。
大多数 Service Fabric 事件都将显示为“自定义事件”，但 Fabric Actor 方法的调用和服务 RunAsync() 的调用将显示为请求。  
将这些事件建模为请求可让你在生成图表时使用“请求名称”维度和“请求持续时间”度量值。
将继续添加新的图表、度量值和事件，以供将来使用。

## 后续步骤
了解有关使用 Application Insights 来检测 Service Fabric 应用的详细信息。

- [Application Insights 入门](/documentation/articles/app-insights-get-started)
- [了解如何创建自己的自定义事件和度量值](/documentation/articles/app-insights-custom-events-metrics-api)
 

<!---HONumber=74-->