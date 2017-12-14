---
title: "在 Azure Service Fabric 中针对 ASP.NET Core 服务进行的监视和诊断 | Microsoft Docs"
description: "了解如何设置 Azure Service Fabric ASP.NET Core 应用程序的监视和诊断。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: ce854a3dc41dec69c3f8de245a03d55a2354335f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>在 Service Fabric 上监视和诊断 ASP.NET Core 应用程序
本教程是一个系列中的第四部分， 介绍了使用 Application Insights 针对 Service Fabric 群集上运行的 ASP.NET Core 应用程序设置监视和诊断的步骤。 我们会从本教程第一部分（即[构建 .NET Service Fabric 应用程序](service-fabric-tutorial-create-dotnet-app.md)）开发的应用程序中收集遥测数据。 

此教程系列的第四部分介绍如何：
> [!div class="checklist"]
> * 为应用程序配置 Application Insights
> * 收集响应遥测数据，跟踪服务之间基于 HTTP 的通信
> * 使用 Application Insights 中的应用映射功能
> * 使用 Application Insights API 添加自定义事件

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [构建 .NET Service Fabric 应用程序](service-fabric-tutorial-create-dotnet-app.md)
> * [将应用程序部署到远程群集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [使用 Visual Studio Team Services 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * 设置应用程序的监视和诊断

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
- [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序
如果未生成[本教程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中的投票示例应用程序，还可以下载它。 在命令窗口或终端中运行以下命令，将示例应用存储库克隆到本地计算机。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>设置 Application Insights 资源
Application Insights 是 Azure 的应用程序性能管理平台，也是 Service Fabric 建议用于应用程序监视和诊断的平台。 若要创建 Application Insights 资源，请导航到 [Azure 门户](https://portal.azure.com)。 单击左侧导航菜单中的“新建”，打开 Azure Marketplace。 单击“监视 + 管理”，然后单击“Application Insights”。

![创建新的 AI 资源](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

此时需填写要创建的资源的必要属性信息。 输入适当的“名称”、“资源组”和“订阅”。 设置“位置”，以便在将来部署 Service Fabric 群集。 在本教程中，需将应用部署到本地群集，因此与“位置”字段无关。 “应用程序类型”应保留为“ASP.NET Web 应用程序”。 

![AI 资源属性](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

填充所需信息以后，请单击“创建”对资源进行预配 - 大约需一分钟。 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>将 Application Insights 添加到应用程序的服务

使用提升的权限启动 Visual Studio 2017。 为此，可右键单击“开始”菜单中的 Visual Studio 图标，然后选择“以管理员身份运行”。 单击“文件” > “打开” > “项目/解决方案”，导航到 Voting 应用程序（在本教程第一部分创建，也可以进行 Git 克隆）。 打开 *Voting.sln*，如果系统提示你还原应用程序的 NuGet 包，请单击“是”。

执行以下步骤，为 VotingWeb 和 VotingData 服务配置 Application Insights：
1. 右键单击服务的名称，然后单击“配置 Application Insights...”。

    ![配置 AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. 单击“免费开始”。
3. 登录到帐户（也可用于设置 Azure 订阅），选择在其中创建了 Application Insights 资源的订阅。 在“资源”下拉列表的“现有的 Application Insights 资源”下找到该资源。 单击“注册”将 Application Insights 添加到服务。

    ![注册 AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. 在弹出的对话框完成操作后，单击“完成”。
    
请确保针对应用程序中的这两项服务执行上述步骤，完成应用程序的 Application Insights 配置。 将相同的 Application Insights 资源用于这两项服务是为了查看服务之间的传入和传出请求和通信。

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>将 Microsoft.ApplicationInsights.ServiceFabric.Native NuGet 添加到服务

Application Insights 有两个特定于 Service Fabric 的 NuGet，可以根据方案使用。 一个用于 Service Fabric 的本机服务，另一个用于容器和来宾可执行文件。 在这种情况下，我们将使用 Microsoft.ApplicationInsights.ServiceFabric.Native NuGet，以便充分利用对其自带的服务上下文的理解。 若要详细了解 Application Insights SDK 和特定于 Service Fabric 的 NuGet，请参阅 [Microsoft Application Insights for Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md)（适用于 Service Fabric 的 Microsoft Application Insights）。 

下面是设置 NuGet 的步骤：
1. 右键单击解决方案资源管理器顶部的“解决方案 'Voting'”，然后单击“为解决方案管理 NuGet 包...”。
2. 单击“NuGet - 解决方案”窗口顶部浏览菜单中的“浏览”，然后勾选搜索栏旁边的“包括预发行版”框。
3. 搜索 `Microsoft.ApplicationInsights.ServiceFabric.Native`，然后单击相应的 NuGet 包。
4. 在右侧单击应用程序中两项服务旁边的复选框“VotingWeb”和“VotingData”，然后单击“安装”。
    ![AI 注册完成](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. 在弹出的“审阅更改”对话框中单击“确定”，接受“接受许可证”中的条款。 这样即可将 NuGet 添加到服务。
6. 现在需在两个服务中设置遥测初始值设定项。 为此，请打开“VotingWeb.cs”和“VotingData.cs”。 对这两个文件执行下述两项步骤：
    1. 在每个 \<ServiceName>.cs 顶部添加下面这两个 using 语句：
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. 在 CreateServiceInstanceListeners() 或 CreateServiceReplicaListeners() 的嵌套式 return 语句中，在 ConfigureServices > services 下两个已声明的单一实例服务之间添加：`.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`。
    此时会向遥测添加服务上下文，方便用户更好地理解 Application Insights 中遥测的源代码。 VotingWeb.cs 中的嵌套式 return 语句应如下所示：
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    同样，VotingData.cs 中的内容应如下所示：

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

此时可以部署应用程序了。 单击顶部的“开始”（或 F5），Visual Studio 就会生成应用程序并将其打包，设置本地群集，然后向其部署应用程序。 

应用程序部署完以后，请访问 [localhost:8080](localhost:8080)，其中应该可以看到 Voting 单页应用程序示例。 投票选择各种不同的项目，以便创建一些示例数据和遥测 - 我投票选择甜点！

![AI 示例投票](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

添加完投票选项后，也可以随意删除部分投票选项。

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>在 Application Insights 中查看遥测和应用映射 

转到 Azure 门户中的 Application Insights 资源，在资源的左侧导航栏的“配置”下单击“预览版”。 在可用预览版的列表中，将“多角色应用程序映射”设置为“启用”。

![AI 启用 AppMap](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

单击“概览”，回到资源的登陆页。 然后单击顶部的“搜索”，查看传入的跟踪。 跟踪显示在 Application Insights 中需要数分钟。 如果看不到任何内容，请等待一会儿，然后单击顶部的“刷新”按钮。
![AI 查看跟踪](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

在“搜索”窗口中向下滚动就会看到 Application Insights 自带的所有传入遥测。 在 Voting 应用程序中每执行一个操作，就会有一个来自 VotingWeb 的传出 PUT 请求（PUT 投票/Put [名称]）、一个来自 VotingData 的传入 PUT 请求（PUT VoteData/Put [名称]），后跟一对用于刷新所显示数据的 GET 请求。 此外还会在 localhost 上有一个针对 HTTP 的依赖项跟踪，因为这些请求是 HTTP 请求。 下面是一个示例，显示了添加一个投票后的情况：![AI 示例请求跟踪](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

单击其中一个跟踪即可查看其详细信息。 Application Insights 提供了该请求的有用信息，其中包括“响应时间”和“请求 URL”。 另外，由于添加了特定于 Service Fabric 的 NuGet，因此还会在底部的“自定义数据”部分提供 Service Fabric 群集上下文中的应用程序数据。 这其中包括服务上下文，因此可以查看请求源的“PartitionID”和“ReplicaId”。在对应用程序中的错误进行诊断时，这样能够更好地查找问题。

![AI 跟踪详细信息](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

另外，由于已启用应用映射，因此在“概览”页上单击“应用映射”图标时，会显示已连接的两个服务。

![AI 跟踪详细信息](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

可以通过应用映射更好地了解应用程序拓扑，尤其是在开始添加多个不同的一起运行的服务时。 也可通过它来获取有关请求成功率的基本数据，对失败的请求进行诊断，了解问题之所在。 若要详细了解如何使用应用映射，请参阅 [Application Insights 中的应用程序映射](../application-insights/app-insights-app-map.md)。

## <a name="add-custom-instrumentation-to-your-application"></a>将自定义检测添加到应用程序

虽然 Application Insights 提供了许多现成的遥测，但你可能需要添加更多的自定义检测。 这可能取决于业务需求，或者在应用程序出错时对诊断进行改进的需求。 Application Insights 有一个用于引入自定义事件和指标的 API，详见[此文](../application-insights/app-insights-api-custom-events-metrics.md)。

让我们向 VoteDataController.cs（位于“VotingData” > “控制器”下）添加一些自定义事件，以便跟踪在基础 votesDictionary 中添加和删除投票的时间。 
1. 在其他 using 语句末尾添加 `using Microsoft.ApplicationInsights;`。
2. 在创建 IReliableStateManager 时，在类的开头声明新的 TelemetryClient：`private TelemetryClient telemetry = new TelemetryClient();`。
3. 在 Put() 函数中添加一个事件，对已添加的投票进行确认。 在事务完成后将 `telemetry.TrackEvent($"Added a vote for {name}");` 直接添加到 return OkResult 语句前面。
4. 在 Delete() 中有一个基于特定条件（即 votesDictionary 包含给定投票选项的投票）的“if/else”。 
    1. 在 if 语句中的 await tx.CommitAsync() 后添加一个确认投票已删除的事件：`telemetry.TrackEvent($"Deleted votes for {name}");`
    2. 在 else 语句中的 return 语句之前添加一个表明没有进行删除的事件：`telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

下面是一个示例，显示在添加事件后 Put() 和 Delete() 函数的情况：

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

进行这些更改以后，请启动应用程序，以便生成和部署最新版本。 应用程序部署完以后，请访问 [localhost:8080](localhost:8080)，添加和删除一些投票选项。 然后回到 Application Insights 资源，查看最新运行的跟踪（与前面一样，跟踪可能需要 1-2 分钟才会显示在 Application Insights 中）。 不管是添加的还是删除的投票，此时都会看到一个“自定义事件”，以及所有响应遥测。 

![自定义事件](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：
> [!div class="checklist"]
> * 为应用程序配置 Application Insights
> * 收集响应遥测数据，跟踪服务之间基于 HTTP 的通信
> * 使用 Application Insights 中的应用映射功能
> * 使用 Application Insights API 添加自定义事件

设置完 ASP.NET 应用程序的监视和诊断以后，请尝试以下操作：
- [在 Service Fabric 中浏览监视和诊断](service-fabric-diagnostics-overview.md)
- [使用 Application Insights 进行 Service Fabric 事件分析](service-fabric-diagnostics-event-analysis-appinsights.md)
- 若要详细了解 Application Insights，请参阅 [Application Insights Documentation](https://docs.microsoft.com/azure/application-insights/)（Application Insights 文档）
