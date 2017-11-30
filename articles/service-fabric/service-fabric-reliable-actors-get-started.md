---
title: "用 C# 创建第一个基于角色的 Azure 微服务 | Microsoft Docs"
description: "本教程会向你演示使用 Service Fabric Reliable Actors 创建、调试和部署简单的基于执行组件的服务的步骤。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: vturecek
ms.openlocfilehash: eb076c30eda63c37a8b555d40d5903cbbf0d426a
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors 入门
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-actors-get-started.md)
> * [Linux 上的 Java](service-fabric-reliable-actors-get-started-java.md)

本文演示在 Visual Studio 中创建和调试简单的 Reliable Actor 应用程序。 有关 Reliable Actors 的详细信息，请参阅 [Service Fabric Reliable Actors 简介](service-fabric-reliable-actors-introduction.md)。

## <a name="prerequisites"></a>先决条件

在开始之前，确保计算机上已设置 Service Fabric 开发环境（包括 Visual Studio）。 有关详细信息，请参阅[如何设置开发环境](service-fabric-get-started.md)。

## <a name="create-a-new-project-in-visual-studio"></a>在 Visual Studio 中创建新项目

以管理员身份启动 Visual Studio 2015 或以上版本，然后新建一个“Service Fabric 应用程序”项目：

![适用于 Visual Studio 的 Service Fabric 工具 - 新建项目][1]

在下一对话框中，选择“执行组件服务”并输入服务的名称。

![Service Fabric 项目模板][5]

创建的项目显示以下结构：

![Service Fabric 项目结构][2]

## <a name="examine-the-solution"></a>检查解决方案

该解决方案包含三个项目：

* 应用程序项目 (MyApplication)。 此项目将所有服务打包在一起以进行部署。 它包含用于管理应用程序的 *ApplicationManifest.xml* 和 PowerShell 脚本。

* 接口项目 (HelloWorld.Interfaces)。 此项目包含执行组件的接口定义。 可以在任何项目中使用任何名称来定义执行组件接口。  接口将定义由执行组件实现和调用执行组件的客户端这两者所共享的执行组件协定。  由于客户端项目可能依赖于接口，因此通常在独立于执行组件实现的程序集中定义它。

* 执行组件服务项目 (HelloWorld)。 此项目定义要托管执行组件的 Service Fabric 服务。 它包含执行组件的实现 HellowWorld.cs。 执行组件实现是派生自基类型 `Actor` 的一个类，用于实现 MyActor.Interfaces 项目中定义的接口。 执行组件类还必须实现一个接受 `ActorService` 实例和 `ActorId` 并将其传递给基本 `Actor` 类的构造函数。
    
    此项目还包含 Program.cs，它使用 `ActorRuntime.RegisterActorAsync<T>()` 向 Service Fabric 运行时注册执行组件类。 `HelloWorld` 类已注册。 还必须使用 `Main()` 方法来注册添加到项目中的任何其他执行组件实现。

## <a name="customize-the-helloworld-actor"></a>自定义 HelloWorld 执行组件

项目模板定义 `IHelloWorld` 接口中的某些方法，并在 `HelloWorld` 执行组件实现中实现这些方法。  替换这些方法，以便执行组件服务返回简单的“Hello World”字符串。

在 HelloWorld.Interfaces 项目中的 IHelloWorld.cs 文件上，替换接口定义，如下所示：

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

在 HelloWorld 项目中的 HelloWorld.cs 上，替换整个类定义，如下所示：

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

按 Ctrl-Shift-B 以生成项目并确保编译所有内容。

## <a name="add-a-client"></a>添加客户端

创建一个简单的控制台应用程序，以调用执行组件服务。

1. 右键单击“解决方案资源管理器”中的解决方案，并单击“添加” > “新建项目...”。

2. 在“.NET Core”项目类型下，选择“控制台应用(.NET Core)”。  将项目命名为 ActorClient。
    
    ![“添加新项目”对话框][6]    
    
    > [!NOTE]
    > 控制台应用程序不是通常在 Service Fabric 中用作客户端的那种应用，但它非常便于使用 Service Fabric 仿真器进行调试和测试。

3. 控制台应用程序必须为 64 位的应用程序，以保持与接口项目和其他依赖项的兼容性。  在“解决方案资源管理器”中，右键单击“ActorClient”项目，并单击“属性”。  在“生成”选项卡上，将“平台目标”设置为“x64”。
    
    ![生成属性][8]

4. 客户端项目需要 Reliable Actors NuGet 包。  单击“工具” > “NuGet 包管理器” > “包管理器控制台”。  在“包管理器控制台”中，输入以下命令：
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    NuGet 包及其所有依赖项都安装在 ActorClient 项目中。

5. 客户端项目还需要对接口项目的引用。  在 ActorClient 项目中，右键单击“依赖项”，然后单击“添加引用...”。选择“项目”>“解决方案”（如果尚未选择），然后勾选“HelloWorld.Interfaces”旁边的复选框。  单击 **“确定”**。
    
    ![“添加引用”对话框][7]

6. 在 ActorClient 项目中，将 Program.cs 的整个内容替换为以下代码：
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>运行和调试

按 F5 在 Service Fabric 开发群集中本地生成、部署和运行应用程序。  在部署过程中，可以在“**输出**”窗口中查看进度。

![Service Fabric 调试输出窗口][3]

当输出包含文本“应用程序已准备就绪”时，可使用 ActorClient 应用程序来测试服务。  在“解决方案资源管理器”中，右键单击“ActorClient”项目，然后单击“调试” > “启动新实例”。  命令行应用程序应显示执行组件服务中的输出。

![应用程序输出][9]

> [!TIP]
> Service Fabric 执行组件运行时发出一些[与执行组件方法相关的事件和性能计数器](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)。 它们可用于进行诊断和性能监视。

## <a name="next-steps"></a>后续步骤
了解有关 [Reliable Actors 如何使用 Service Fabric 平台](service-fabric-reliable-actors-platform.md)的详细信息。


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png