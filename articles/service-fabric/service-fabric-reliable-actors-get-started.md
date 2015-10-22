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
   ms.date="08/05/2015"
   wacn.date=""/>

# Reliable Actors：Canonical HelloWorld 演练方案
本文介绍了 Service Fabric Reliable Actors 的基础知识并指导你在 Visual Studio 中完成创建、调试和部署简单的 HelloWorld 应用程序。

## 安装和设置
在开始之前，确保你的计算机上具有 Service Fabric 开发环境设置。可在[此处](/documentation/articles/service-fabric-get-started)找到有关如何设置开发环境的详细说明。

## 基本概念
若要开始使用 Reliable Actors，你只需了解 4 个基本概念：

* **Actor Service**。Reliable Actors 在可以在 Service Fabric 基础结构中部署的服务中进行打包。一个服务可以托管一个或多个 Actor。我们稍后将进入有关每个服务中一个和多个 Actor 的权衡的更多详细信息。现在让我们假设我们只需要实现一个 Actor。
* **Actor 接口**。Actor 接口用于定义 Actor 的公共接口。在 Actor 模型术语中，它定义 Actor 能够了解过程的消息类型。Actor 接口由其他 Actor 或客户端应用程序用于 将消息“发送”到（异步方式）Actor。Reliable Actors 可以实现多个接口，如我们将要看到的，HelloWorld Actor 不仅可以实现 IHelloWorld 接口，还可以实现定义不同消息/功能的 ILogging 接口。
* **Actor 注册**。在 Actor Service 中，需要注册 Actor 类型，以便 Service Fabric 能够感知新类型并可以用它来创建新的 Actor。
* **ActorProxy 类**。ActorProxy 类用于绑定到 Actor 并调用通过其接口公开的方法。ActorProxy 类提供两个重要功能：
	* 名称解析：能够在群集（在其中找到托管它的群集节点）中找到 Actor。
	* 处理故障：例如，需要将 Actor 重新定位到群集中另一个节点的故障之后，它可以重试方法调用和重新确定 Actor 位置。

## 在 Visual Studio 中创建新项目
安装适用于 Visual Studio 的 Service Fabric 工具后，你可以创建新的项目类型。新的项目类型位于“新建项目”对话框的“云”类别下


![适用于 VS 的 Service Fabric 工具 - 新项目][1]

在下一个对话框中，可以选择你想要创建的项目类型。

![Service Fabric 项目模板][5]

对于 HelloWorld 项目，我们使用 Service Fabric Actor Service。

创建解决方案后，应看到以下结构：

![Service Fabric 项目结构][2]

## Reliable Actors 基本构建基块

典型的 Reliable Actors 解决方案由 3 个项目组成：

* 应用程序项目 (HelloWorldApplication)。这是将所有服务打包在一起以进行部署的项目。它包含用于管理应用程序的 ApplicationManifest.xml 和 PowerShell 脚本。

* 接口项目 (HelloWorld.Interfaces)。这是包含 Actor 的接口定义的项目。在接口项目中，你可以定义 Actor 用于解决方案中的接口。

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* 服务项目 (HelloWorld)。这是用于定义要托管 Actor 的 Service Fabric 服务的项目。它包含一些在大多数情况下不需要编辑的样板文件代码 (ServiceHost.cs) 和 Actor 的实现。Actor 的实现包含实现派生自基类型 (Actor) 并且实现接口项目中定义的接口的类。

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

Actor Service 项目包含用于创建 Service Fabric 服务的代码，在服务定义中，会注册 Actor 类型，以便它们可以用于实例化新的 Actor。

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

如果在 Visual Studio 中从新项目开始，并且只有一个 Actor 定义，在默认情况下，注册包含在 Visual Studio 生成的代码中。如果在服务中定义其他 Actor，则需要使用以下操作添加 Actor 注册：

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## 调试

适用于 Visual Studio 的 Service Fabric 工具支持在本地计算机上进行调试。你可以通过点击 F5 启动调试会话。Visual Studio 在本地 Service Fabric 群集中生成（如有必要）、打包和部署应用程序，并附加调试器。体验类似于调试 ASP.NET 应用程序。在部署过程中，你可以在输出窗口中查看进度

![Service Fabric 调试输出窗口][3]


## 后续步骤

[Service Fabric Reliable Actors 简介](/documentation/articles/service-fabric-reliable-actors-introduction) [Actors API 参考文档](https://msdn.microsoft.com/library/azure/dn971626.aspx) [示例代码](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=74-->