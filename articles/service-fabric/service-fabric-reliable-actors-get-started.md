<properties
   pageTitle="Reliable Actors入门 | Microsoft Azure"
   description="本教程将指导你使用 Service Fabric Reliable Actors 完成创建、调试和部署 Canonical HelloWorld 服务的步骤。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="11/13/2015"
   wacn.date=""/>

# Reliable Actors：Canonical HelloWorld 演练方案
本文介绍了 Azure Service Fabric Reliable Actors 的基础知识，并指导你在 Visual Studio 中完成创建、调试和部署简单的 HelloWorld 应用程序。

## 安装和设置
在开始之前，确保你的计算机上已设置 Service Fabric 开发环境。
如果你需要设置此环境，请参阅[如何设置开发环境](/documentation/articles/service-fabric-get-started)上的详细说明。

## 基本概念
若要开始使用 Reliable Actors，你只需了解 4 个基本概念：

* **执行组件服务**。Reliable Actors 在可以在 Service Fabric 基础结构中部署的服务中进行打包。一个服务可以托管一个或多个执行组件。以下我们将详细讨论每个服务中一个执行组件和多个执行组件之间的利弊权衡。现在假设我们只需要实现一个执行组件。
* **执行组件接口**。执行组件接口用于定义执行组件的公共接口。在 Reliable Actor 模型术语中，执行组件接口用于定义执行组件可以理解并处理的消息类型。其他执行组件或客户端应用程序使用此执行组件接口将消息“发送”到（异步方式）此执行组件。Reliable Actors 可实现多个接口。正如我们将要看到的，HelloWorld 执行组件不仅可以实现 IHelloWorld 接口，还可以实现定义不同消息和/或功能的 ILogging 接口。
* **执行组件注册**。在 Reliable Actors 服务中，需要注册执行组件类型。这样，Service Fabric 会注意到新类型，并可以使用它来创建新的执行组件。
* **ActorProxy 类**。ActorProxy 类用于绑定到一个执行组件，并调用通过此执行组件接口公开的方法。ActorProxy 类提供两个重要功能：
	* 它可以解析名称。它能够在群集中找到执行组件（查找托管它的群集节点）。
	* 它可以处理故障。例如，在需要将执行组件重新定位到群集中另一个节点的故障之后，它可以重试方法调用和重新确定执行组件的位置。

## 在 Visual Studio 中创建新项目
安装了适用于 Visual Studio 的 Service Fabric 工具之后，你可以创建新的项目类型。新的项目类型位于“新建项目”对话框的“云”类别下面。


![适用于 Visual Studio 的 Service Fabric 工具 - 新建项目][1]

在下一个对话框中，可以选择你想要创建的项目类型。

![Service Fabric 项目模板][5]

对于 HelloWorld 项目，我们使用 Service Fabric Reliable Actors 服务。

创建此解决方案之后，可看到以下结构：

![Service Fabric 项目结构][2]

## Reliable Actors 基本构建基块

典型的 Reliable Actors 解决方案由 3 个项目组成：

* **应用程序项目 (HelloWorldApplication)**。这是将所有服务打包在一起以进行部署的项目。它包含用于管理应用程序的 **ApplicationManifest.xml** 和 PowerShell 脚本。

* **接口项目 (HelloWorld.Interfaces)**。这是包含执行组件的接口定义的项目。在 HelloWorld.Interfaces 项目中，你可以定义此解决方案中执行组件将要使用的接口。

```csharp

namespace MyActor.Interfaces
{
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;

    public interface IMyActor : IActor
    {
        Task<string> HelloWorld();
    }
}

```

* **服务项目 (HelloWorld)**。这是用于定义要托管执行组件的 Service Fabric 服务的项目。它包含一些在大多数情况下不需要编辑的样板代码 (ServiceHost.cs) 和执行组件的实现。执行组件的实现包括实现从基类型 (执行组件) 派生的类。它还实现 HelloWorld.Interfaces 项目中定义的接口。

```csharp

namespace MyActor
{
    using System;
    using System.Threading.Tasks;
    using Interfaces;
    using Microsoft.ServiceFabric.Actors;

    internal class MyActor : StatelessActor, IMyActor
    {
        public Task<string> HelloWorld()
        {
            throw new NotImplementedException();
        }
    }
}

```

Reliable Actors 服务项目包含用于创建 Service Fabric 服务的代码。在服务定义中，已注册一个或多个执行组件类型，因此它们可以用于实例化新的执行组件。

```csharp

namespace MyActor
{
    using System;
    using System.Fabric;
    using System.Threading;
    using Microsoft.ServiceFabric.Actors;

    internal static class Program
    {
        private static void Main()
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {
                    fabricRuntime.RegisterActor<MyActor>();

                    Thread.Sleep(Timeout.Infinite);  // Prevents this host process from terminating so services keeps running.
                }
            }
            catch (Exception e)
            {
                ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}

```

如果在 Visual Studio 中从新项目开始，并且只有一个执行组件定义，那么默认情况下，在 Visual Studio 生成的代码中包含此注册。如果在服务中定义其他执行组件，则需要使用以下操作添加执行组件注册：

```csharp

fabricRuntime.RegisterActor<MyActor>();


```

## 调试

适用于 Visual Studio 的 Service Fabric 工具支持在本地计算机上进行调试。你可以通过点击 F5 键启动调试会话。Visual Studio 会生成包（如果需要）。Visual Studio 还将在本地 Service Fabric 群集中部署应用程序，并附加调试器。体验类似于调试 ASP.NET 应用程序。

在部署过程中，你可以在“输出”窗口中查看进度。

![Service Fabric 调试输出窗口][3]


## 后续步骤

- [Service Fabric Reliable Actors 简介](/documentation/articles/service-fabric-reliable-actors-introduction)
- [执行组件 API 参考文档](https://msdn.microsoft.com/zh-cn/library/azure/dn971626.aspx)
- [代码示例](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=Mooncake_0307_2016-->