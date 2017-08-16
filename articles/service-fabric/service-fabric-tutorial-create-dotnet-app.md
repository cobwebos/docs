---
title: "创建适用于 Service Fabric 的 .NET 应用程序 | Microsoft Docs"
description: "了解如何使用 ASP.NET Core 前端和 Reliable Services 有状态后端创建一个应用程序，然后将其部署到群集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 71d910bc0e459528805521ba991e5291396a3b8d
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>使用 ASP.NET Core Web API 前端服务和有状态后端服务创建和部署应用程序
本教程是一个系列的第一部分，介绍如何使用 ASP.NET Core Web API 前端和有状态后端服务创建 Azure Service Fabric 应用程序，以便存储数据。 

![应用程序关系图](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 将 ASP.NET Core Web API 服务作为 Reliable Services 创建
> * 创建有状态 Reliable Services
> * 实现服务远程处理和使用服务代理

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 构建 .NET Service Fabric 应用程序
> * [将应用程序部署到远程群集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [使用 Visual Studio Team Services 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
- [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>将 ASP.NET Web API 服务作为 Reliable Services 创建
ASP.NET Core 是轻量跨平台的 Web 开发框架，可用于创建现代 Web UI 和 Web API。 若要全面了解 ASP.NET Core 如何与 Service Fabric 集成，强烈建议你通读 [Service Fabric Reliable Services 中的 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 一文。 现在可以按照本指南快速入门。 若要了解有关 ASP.NET Core 的详细信息，请参阅 [ASP.NET Core 文档](https://docs.microsoft.com/aspnet/core/)。

> [!NOTE]
> 本教程基于[用于 Visual Studio 2017 的 ASP.NET Core 工具](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc)。 用于 Visual Studio 2015 的 .NET Core 工具不再更新。

1. 以管理员身份启动 Visual Studio。

2. 通过单击“文件”->“新建”->“项目”创建项目

3. 在“新建项目”对话框中，选择“云”>“Service Fabric 应用程序”。

4. 将应用程序命名为“MyApplication”，然后按“确定”。

   ![Visual Studio 中的新建项目对话框](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. 在“新建 Service Fabric 服务”页中，选择“无状态 ASP.NET Core”，然后将服务命名为“MyWebAPIFrontEnd”。
   
   ![在新建服务对话框中选择 ASP.NET Web 服务](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 下一页将提供一组 ASP.NET Core 项目模板。 本教程中，选择“Web API”。 不过你也可以运用相同的思路来构建完整的 Web 应用程序。
   
   ![选择 ASP.NET 项目类型](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio 会创建应用程序和服务项目，并在解决方案资源管理器中显示它们。

   ![使用 ASP.NET Core Web API 服务创建应用程序之后的解决方案资源管理器]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>在本地部署和调试应用程序
现在可以开始调试应用程序，查看 ASP.NET Core Web API 模板提供的默认行为。

在 Visual Studio 中按 `F5`，部署用于调试的应用程序。 如果此前未以管理员身份打开 Visual Studio，则 `F5` 会失败。

> [!NOTE]
> 首次在本地运行和部署应用程序时，Visual Studio 会创建用于调试的本地群集，这可能需要一些时间。 群集创建状态显示在 Visual Studio 输出窗口中。

群集准备就绪时，将从系统托盘中的 Service Fabric 本地群集管理器应用程序收到通知。

1. 若要调试应用程序，请在 Visual Studio 中按 F5。
2. 部署完成后，Visual Studio 将启动浏览器并打开 ASP.NET Web API 服务的根路径。 ASP.NET Core Web API 模板不提供根目录的默认行为，因此会在浏览器中收到错误。
3. 将 `/api/values` 添加到浏览器中的 URL。 此请求将对 Web API 模板中的 ValuesController 调用 `Get` 方法。 它会返回模板提供的默认响应，即包含两个字符串的 JSON 数组：
   
![从 ASP.NET Core Web API 模板返回的默认值](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> 若要在调试应用程序时更改默认的 Visual Studio 2017 行为，可更改 Service Fabric 应用程序项目 MyApplication 的属性。
> 就本教程来说，将“应用程序调试模式”设置为“刷新应用程序”并将 '/api/values' 添加到“应用程序 URL”属性中可优化调试体验。
> 

若要停止调试应用程序，请返回到 Visual Studio 并按 Shift+F5。

### <a name="understanding-the-service-fabric-application-and-service"></a>了解 Service Fabric 应用程序和服务
Visual Studio 解决方案现在包含两个项目。
1. Service Fabric 应用程序项目 - MyApplication
    - 该项目不直接包含任何代码。 而是引用一组服务项目。 此外，它还包含其他类型的内容，用于指定应用程序的组成和部署方式。
2. 服务项目 - MyWebAPIFrontEnd
    - 该项目为 ASP.NET Core Web API 项目，包含服务的代码和配置。 查看 Controllers 文件夹中的 ValuesController.cs 代码文件，你会注意到，它是一个常规的 ASP.NET Core Web API 控制器。 在 Service Fabric 中将 ASP.NET Core Web API 作为 Reliable Services 运行时，不对作为控制器一部分编写的代码有任何具体要求。

有关 Service Fabric 中的应用程序模型的详细信息，请参阅[在 Service Fabric 中为应用程序建模](service-fabric-application-model.md)。

有关服务项目内容的详细信息，请参阅 [Reliable Services 入门](service-fabric-reliable-services-quick-start.md)。

## <a name="add-a-stateful-back-end-service-to-your-application"></a>向应用程序添加有状态后端服务
在应用程序中运行 ASP.NET Web API 服务以后，即可进一步添加有状态 Reliable Services，以便在应用程序中存储一些数据。

Service Fabric 允许使用 Reliable Collections 直接在服务内以一致、可靠的方式存储数据。 Reliable Collections 是一组简单的高度可用、可靠集合类，用过 C# 集合的用户都对它很熟悉。

在本教程中，你将创建一个服务，用于在 Reliable Collections 中存储计数器值。

1. 在解决方案资源管理器中，右键单击应用程序项目中的“**服务**”，并选择“**添加”>“新建 Service Fabric 服务**”。
   
    ![将一个新服务添加到现有应用程序](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. 在“新建 Service Fabric 服务”对话框中，选择“有状态服务”，将服务命名为“MyStatefulService”，然后按“确定”。

    ![Visual Studio 中的新建服务对话框](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    创建服务项目后，应用程序中会有两个服务。 随着继续生成应用程序，可采用相同的方式添加更多服务。 每个服务都可以单独进行版本控制和升级。

### <a name="deploy-and-debug-the-application-locally"></a>在本地部署和调试应用程序
将新服务添加到应用程序以后，即可调试整个应用程序，查看新服务的默认行为。

若要调试应用程序，请在 Visual Studio 中按 F5。

> [!NOTE]
> 如果选择将“刷新应用程序”用作“应用程序调试模式”，系统会提示你授予本地 Service Fabric 群集相应权限，方便其访问应用程序的生成输出文件夹。
> 

应用程序中的两个服务都是生成，在本地 Service Fabric 群集中部署和声明。 服务启动后，Visual Studio 仍会启动浏览器，但也会自动显示诊断事件查看器，方便你在其中查看来自服务的跟踪输出。
   
![诊断事件查看器](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

诊断事件查看器显示来自所有服务的跟踪消息，这些服务是要调试的 Visual Studio 解决方案的一部分。 暂停诊断事件查看器即可展开某个服务消息，以便检查其属性。 这样做可以查看群集中哪个服务发出了该消息、该服务实例当前正运行在哪个节点上，等等。

![诊断事件查看器](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

可以看到，服务消息来自已创建的有状态服务，因为 serviceTypeName 为 MyStatefulServiceType。 还可以看到它在发送消息，消息文本为“当前计数器...”。 该消息是由 MyStatefulService.cs 的 `RunAsync` 方法中这个突出显示的代码行发出的，如以下屏幕截图所示。

![服务消息代码](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

若要详细了解如何从服务和应用程序发出诊断信息，请参阅[对 Azure Service Fabric 进行监视和诊断](service-fabric-diagnostics-overview.md)。

若要停止调试应用程序，请返回到 Visual Studio 并按 Shift+F5。

### <a name="understanding-the-mystatefulservice-code"></a>了解 MyStatefulService 代码
若要了解其如何使用 Reliable Dictionaries 在群集中存储数据，让我们抽点时间看看 MyStatefulService 服务中的代码。

服务中有五行代码与创建、更新 Reliable Dictionaries 并从其中读取数据相关。

![Reliable Dictionaries 代码](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. 每次运行服务的 `RunAsync` 方法时（在服务启动时发生），这行代码就会获取名为 `myDictionary` 的 Reliable Dictionaries 或将其添加到服务。
2. 与 Reliable Dictionaries 中的值进行的所有交互都需要事务，在这行代码中输入的此 using 语句用于创建该事务。
3. 这行代码获取与在方法调用中指定的键（例如 `Counter`）关联的值。
4. 这行代码通过递增值的方式，更新与键 `Counter` 关联的值。
5. 此方法调用会提交事务，并在更新的值存储到群集中的节点仲裁后返回。

有关 Reliable Dictionaries 和 Reliable Collections 的更多详细信息，请参阅 [Azure Service Fabric 有状态服务中的 Reliable Collections 简介](service-fabric-reliable-services-reliable-collections.md)。

## <a name="connect-the-services"></a>连接服务
在下一步中，我们会连接这两个服务，使前端 Web API 返回后端服务 Reliable Dictionaries 的当前值。

在如何与 Reliable Services 通信方面，Service Fabric 提供十足的弹性。 在单个应用程序中，可能有能够通过 TCP 访问的服务。 其他服务也许可以通过 HTTP REST API 访问，并且仍可通过 Web 套接字访问。 有关可用选项和相关权衡取舍的背景信息，请参阅[与服务通信](service-fabric-connect-and-communicate-with-services.md)。

在本教程中，我们将使用[通过 Reliable Services 进行的服务远程处理](service-fabric-reliable-services-communication-remoting.md)。

在服务远程处理方法（模仿远程过程调用或 RPC）中，需定义一个接口，将其用作服务的公共约定。 然后使用该接口生成代理类，以便与服务交互。

### <a name="create-the-remoting-interface"></a>创建远程处理接口
首先创建该接口，将其用作两个服务之间的约定。 该接口必须由使用它的所有服务引用，因此我们在单独的类库项目中创建它。

1. 在解决方案资源管理器中，右键单击解决方案并选择“添加” > “新建项目”。
2. 在左侧导航窗格中选择“Visual C#”条目，然后选择“类库(.NET Framework)”模板。 确保 .NET Framework 版本已设置为 4.5.2 或更高。
   
    ![为有状态服务创建接口项目](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. 将类库命名为“MyStatefulService.Interface”，然后单击“确定”。

4. 在解决方案资源管理器中，右键单击该解决方案，然后选择“为解决方案管理 NuGet 包”。

5. 选择“浏览”，然后搜索“Microsoft.ServiceFabric.Services.Remoting”。 选择为解决方案中的三个服务项目安装它： 
   
    ![添加服务 NuGet 包](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. 在类库中，使用单个方法 `GetCountAsync` 创建接口，并从 `Microsoft.ServiceFabric.Services.Remoting.IService` 扩展接口。 远程接口必须派生自此接口，表明它是 Service Remoting 接口。 
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;  
    using System.Threading.Tasks;
    ...
    namespace MyStatefulService.Interface
    {           
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```
7. 在解决方案资源管理器中右键单击“MyStatefulService.Interface”项目，然后选择“属性”。 选择“生成”选项卡，然后在“平台目标”下拉列表中选择值“x64”。 

8. 保存所有更改。

### <a name="implement-the-interface-in-your-stateful-service"></a>在有状态服务中实现接口
现在我们已定义了接口，接下来需要在有状态服务中实现该接口。

1. 从“MyStatefulService”项目中添加对包含此接口的类库项目的引用。
   
    ![在有状态服务中添加对类库项目的引用](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![在有状态服务中添加对类库项目的引用](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. 打开 `MyStatefulService.cs` 文件，对其进行扩展，以便实现我们创建的 `ICounter` 接口。
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. 现在实现 `ICounter` 接口中定义的单个方法，即 `GetCountAsync`。
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary =
          await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

    - 此方法返回名为 `myDictionary` 的 Reliable Dictionaries 中 `Counter` 键的存储值。 

### <a name="expose-the-stateful-service-using-service-remoting"></a>使用服务远程处理公开有状态服务
`ICounter` 接口实现后，最后一步是打开服务远程处理终结点。 对于有状态服务，Service Fabric 提供了名为 `CreateServiceReplicaListeners` 的可重写方法。 通过此方法，可以根据想要为服务启用的通信类型指定一个或多个通信侦听器。

在本例中，我们将替换现有的 `CreateServiceReplicaListeners` 方法，并提供 `ServiceRemotingListener` 的实例，该实例通过 `ServiceProxy` 创建可从客户端调用的 RPC 终结点。  

更改 MyStatefulService.cs 文件中的 CreateServiceReplicaListeners 方法，然后将 using 语句添加到 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 命名空间。

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```

现在，有状态服务已准备好使用服务远程处理通过 RPC 接收来自其他服务的流量。

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>从前端服务调用有状态后端服务
后端服务公开接口以后，只需添加代码，以便通过 ASP.NET Web API 服务与其通信即可。 若要使用服务远程处理进行通信，可以通过值控制器使用服务代理。

1. 在解决方案资源管理器中展开“MyWebAPIFrontEnd”，右键单击“依赖项”，然后选择“添加引用”。  选择“MyStatefulService.Interface”，然后单击“确定”。
   
2. 在 Controllers 文件夹中，打开 `ValuesController.cs` 文件。 向文件添加以下 using 语句：

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. `Get` 方法目前只返回“value1”和“value2”的硬编码字符串数组，这符合前面在浏览器中看到的内容。 使用以下代码替换此实现：
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    此方法中的第一行代码使用 ICounter 接口创建有状态服务的 ServiceProxy 对象。 创建 ServiceProxy 时，需提供两项信息：分区 ID 和服务名称。
   
    可以将有状态服务分区，即根据某个键（例如客户 ID 或邮政编码）将数据分装到不同的存储桶中，以便进行伸缩。 在我们的简单应用程序中，有状态服务只有一个分区，所以键并不重要。 不管提供什么键，分区都是相同的。 若要深入了解分区服务，请参阅[如何为 Service Fabric Reliable Services 分区](service-fabric-concepts-partitioning.md)。
   
    服务名称是 fabric:/&lt;application_name&gt;/&lt;service_name&gt; 格式的 URI。
   
    使用这两项信息，Service Fabric 可唯一标识请求应发送到的计算机。 `ServiceProxy` 类还能顺利处理托管有状态服务分区的计算机发生故障的情况，另一台计算机必须进行升级才能取而代之。 这种抽象使得编写客户端代码来处理其他服务变得更为简单。
   
    拥有代理后，即可调用 `GetCountAsync` 方法并返回其结果。

4. 再次按 F5 运行修改后的应用程序。 像前面一样，Visual Studio 将自动启动浏览器并打开 Web 项目的根路径。 添加“api/values”路径，应会看到返回的当前计数器值。
   
    ![浏览器中显示的有状态计数器值](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    定期刷新浏览器，以查看计数器更新值。

若要停止调试应用程序，请返回到 Visual Studio 并按 Shift+F5。

## <a name="next-steps"></a>后续步骤
本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 将 ASP.NET Core Web API 服务作为 Reliable Services 创建
> * 创建有状态 Reliable Services
> * 实现服务远程处理和使用服务代理

转到下一教程：
> [!div class="nextstepaction"]
> [将应用程序部署到 Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
