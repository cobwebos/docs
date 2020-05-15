---
title: 在 Azure 中的 Service Fabric 上创建 .NET 应用
description: 本教程介绍如何使用 ASP.NET Core 前端和 Reliable Services 有状态后端创建一个应用程序，然后将其部署到群集。
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc
ms.openlocfilehash: cbfae89ffa446ca3915129fd9add2701ac21d837
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75465471"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>教程：使用 ASP.NET Core Web API 前端服务和有状态后端服务创建并部署应用程序

本教程是一个系列中的第一部分。  其中介绍了如何使用 ASP.NET Core Web API 前端和有状态后端服务创建 Azure Service Fabric 应用程序以存储数据。 完成后，将生成一个投票应用程序，其中包含 ASP.NET Core Web 前端，用于将投票结果保存到群集的有状态后端服务中。 如果不想手动创建投票应用程序，可以[下载已完成应用程序的源代码](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)，跳到[大致了解投票示例应用程序](#walkthrough_anchor)。  如果需要，还可以观看本教程的[视频演练](https://channel9.msdn.com/Events/Connect/2017/E100)。

![连接到 Service Fabric 上的有状态后端服务的 AngularJS+ASP.NET API 前端](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建 ASP.NET Core Web API 服务作为有状态可靠服务
> * 创建 ASP.NET Core Web 应用程序服务作为无状态 Web 服务
> * 使用反向代理与有状态服务通信

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 构建 .NET Service Fabric 应用程序
> * [将应用程序部署到远程群集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [向 ASP.NET Core 前端服务添加 HTTPS 终结点](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [使用 Azure Pipelines 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>必备条件

在开始学习本教程之前：
* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [安装 Visual Studio 2019](https://www.visualstudio.com/) 版本 15.5 或更高版本，其中包含 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
* [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>创建 ASP.NET Web API 服务作为可靠服务

首先，使用 ASP.NET Core 创建投票应用程序的 Web 前端。 ASP.NET Core 是轻量跨平台的 Web 开发框架，可用于创建新式 Web UI 和 Web API。 若要全面了解 ASP.NET Core 如何与 Service Fabric 集成，强烈建议你通读 [Service Fabric Reliable Services 中的 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 一文。 现可按照本指南快速入门。 若要了解有关 ASP.NET Core 的详细信息，请参阅 [ASP.NET Core 文档](https://docs.microsoft.com/aspnet/core/)。

1. 以管理员身份启动 Visual Studio。 

2. 通过单击“文件”  ->  “新建”->  “项目”创建项目。

3. 在“新建项目”对话框中，选择“云”>“Service Fabric 应用程序”。  

4. 将应用程序命名为“Voting”，然后单击“创建”   。

   ![Visual Studio 中的新建项目对话框](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. 在“新建 Service Fabric 服务”页中，选择“无状态 ASP.NET Core”，将服务命名为“VotingWeb”，然后单击“创建”     。
   
   ![在新建服务对话框中选择 ASP.NET Web 服务](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 下一页将提供一组 ASP.NET Core 项目模板。 对于本教程，请选择“Web 应用程序(模型-视图-控制器)”，然后单击“创建”   。
   
   ![选择 ASP.NET 项目类型](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio 会创建应用程序和服务项目，并在解决方案资源管理器中显示它们。

   ![使用 ASP.NET Core Web API 服务创建应用程序之后的解决方案资源管理器]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>更新 site.js 文件
打开 **wwwroot/js/site.js**。  将其内容替换为“主页”视图所用的以下 JavaScript，然后保存更改。

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>更新 Index.cshtml 文件

打开特定于主文件夹控制器的视图 **Views/Home/Index.cshtml**。  将其内容替换为以下内容，然后保存所做更改。

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>更新 _Layout.cshtml 文件

打开 ASP.NET 应用的默认布局 **Views/Shared/_Layout.cshtml**。  将其内容替换为以下内容，然后保存所做更改。


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>更新 VotingWeb.cs 文件

打开 *VotingWeb.cs* 文件，该文件会使用 WebListener Web 服务器在无状态服务内创建 ASP.NET Core WebHost。

在文件顶部，添加 `using System.Net.Http;` 指令。

将 `CreateServiceInstanceListeners()` 函数替换为以下代码，然后保存所做更改。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

在 `CreateServiceInstanceListeners()` 的下面添加以下 `GetVotingDataServiceName` 方法，然后保存所做更改。 `GetVotingDataServiceName` 在投票时返回服务名称。

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>添加 VotesController.cs 文件

添加控制器用于定义投票操作。 右键单击“Controllers”文件夹，然后选择“添加”->“新建项”->“Visual C#”->“ASP.NET Core”->“类”。   将文件命名为“VotesController.cs”，然后单击“添加”   。  

将 *VotesController.cs* 文件内容替换为以下内容，然后保存所做更改。  稍后在执行[更新 VotesController.cs 文件](#updatevotecontroller_anchor)时将会修改此文件，以读取和写入来自后端服务的投票数据。  现在，控制器会将静态字符串数据返回到视图中。

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>配置侦听端口

创建 VotingWeb 前端服务后，Visual Studio 会随机选择服务侦听的端口。  VotingWeb 服务充当此应用程序的前端并接受外部流量，因此让我们将此服务绑定到已知的固定端口。  [服务清单](service-fabric-application-and-service-manifests.md)声明服务终结点。

在解决方案资源管理器中，打开“VotingWeb/PackageRoot/ServiceManifest.xml”  。  在“Resources”部分中查找“Endpoint”元素，并将“Port”值更改为 8080。     若要在本地部署和运行应用程序，应用程序侦听端口必须为打开状态且在你的计算机上可用。

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

此外，更新投票项目中的应用程序 URL 属性值，使 Web 浏览器在调试应用程序时打开到正确的端口。  在解决方案资源管理器中，选择“Voting”项目并将“应用程序 URL”属性更新为 8080    。

### <a name="deploy-and-run-the-voting-application-locally"></a>在本地部署并运行“Voting”应用程序
现在可以继续运行“Voting”应用程序进行调试。 在 Visual Studio 中，按 **F5** 在调试模式下将应用程序部署到本地 Service Fabric 群集。 如果此前未以管理员身份打开 Visual Studio，则应用程序会失败。 

> [!NOTE]
> 首次在本地运行和部署应用程序时，Visual Studio 会创建用于调试的本地 Service Fabric 群集。  创建群集可能需要一段时间。 群集创建状态显示在 Visual Studio 输出窗口中。

将“Voting”应用程序部署到本地 Service Fabric 群集后，Web 应用会在浏览器选项卡中自动打开，如下所示：

![ASP.NET Core 前端](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

若要停止调试应用程序，请返回到 Visual Studio 并按 Shift+F5  。

## <a name="add-a-stateful-back-end-service-to-your-application"></a>向应用程序添加有状态后端服务

在应用程序中运行 ASP.NET Web API 服务后，可以继续添加有状态可靠服务，以便在应用程序中存储一些数据。

Service Fabric 允许使用 Reliable Collections 直接在服务内以一致、可靠的方式存储数据。 Reliable Collections 是一组高度可用的可靠集合类，用过 C# 集合的用户都对它很熟悉。

在本教程中，创建一个服务，用于在 Reliable Collections 中存储计数器值。

1. 在解决方案资源管理器中，右键单击“Voting”应用程序项目中的“服务”，并选择“添加”->“新建 Service Fabric 服务...”   。
    
2. 在“新建 Service Fabric 服务”对话框中，选择“有状态 ASP.NET Core”，将服务命名为“VotingData”，然后按“创建”     。

    创建服务项目后，应用程序中会有两个服务。 随着继续生成应用程序，可采用相同的方式添加更多服务。 每个服务都可以单独进行版本控制和升级。

3. 下一页将提供一组 ASP.NET Core 项目模板。 本教程中，选择“API”  。

    Visual Studio 会创建 VotingData 服务项目，并在解决方案资源管理器中显示。

    ![解决方案资源管理器](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>添加 VoteDataController.cs 文件

在“VotingData”项目中，右键单击“Controllers”文件夹，然后选择“添加”->“新建项”->“类”    。 将文件命名为“VoteDataController.cs”，然后单击“添加”   。 将文件内容替换为以下内容，然后保存所做更改。

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>连接服务

下一步是连接这两个服务，使前端 Web 应用程序获取并设置来自后端服务的投票信息。

在如何与 Reliable Services 通信方面，Service Fabric 是十分灵活的。 在单个应用程序中，可能会有通过 TCP 访问的服务。 其他服务也许可以通过 HTTP REST API 访问，以及可通过 Web 套接字访问。 有关可用选项和相关权衡取舍的背景信息，请参阅[与服务通信](service-fabric-connect-and-communicate-with-services.md)。

本教程使用 [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md) 和 [Service Fabric 反向代理](service-fabric-reverseproxy.md)，以便 VotingWeb 前端 Web 服务能够与后端 VotingData 服务通信。 反向代理默认配置为使用端口 19081，应适用于本教程。 反向代理端口是在用于设置群集的 Azure 资源管理器模板中设置的。 若要确定使用哪个端口，请在 **Microsoft.ServiceFabric/clusters** 资源中搜索群集模板： 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
若要查找在本地开发群集中使用的反向代理端口，请查看本地 Service Fabric 群集清单中的 **HttpApplicationGatewayEndpoint** 元素：
1. 打开一个浏览器窗口，并导航到 http:\//localhost:19080 以打开 Service Fabric Explorer 工具。
2. 选择“群集”->“清单”。 
3. 记下 HttpApplicationGatewayEndpoint 元素端口。 默认情况下，此端口应是 19081。 如果不是 19081，则需要更改以下 VotesController.cs 代码的 GetProxyAddress 方法中的端口。

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>更新 VotesController.cs 文件

在“VotingWeb”项目中，打开 Controllers/VotesController.cs 文件   。  将 `VotesController` 类定义内容替换为以下内容，然后保存所做更改。 如果在前面的步骤中发现反向代理端口不是 19081，将GetProxyAddress 方法中使用的端口19081更改为发现的端口。

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>大致了解投票示例应用程序

投票应用程序由以下两个服务组成：

* Web 前端服务 (VotingWeb) - ASP.NET Core Web 前端服务，可提供网页服务，并公开用于与后端服务进行通信的 Web API。
* 后端服务 (VotingData) - ASP.NET Core Web 服务，可公开用于将投票结果存储在可靠字典中并保留在磁盘上的API。

![应用程序关系图](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

在应用程序中投票时，将会发生以下事件：

1. JavaScript 将投票请求作为 HTTP PUT 请求发送给 Web 前端服务中的 Web API。

2. Web 前端服务使用代理定位并将 HTTP PUT 请求转发给后端服务。

3. 后端服务接收传入请求，并将更新后的结果存储在可靠字典中（结果复制到群集内的多个节点，并保留在磁盘上）。 应用程序的所有数据都存储在群集中，因此无需使用数据库。

## <a name="debug-in-visual-studio"></a>在 Visual Studio 中进行调试

在 Visual Studio 中调试应用程序时，使用的是本地 Service Fabric 开发群集。 可以根据方案需要调整调试体验。 在此应用程序中，我们使用可靠字典将数据存储到后端服务中。 停止调试程序时，Visual Studio 会默认删除应用程序。 删除应用程序后，后端服务中的数据也会随之一起删除。 若要跨调试会话保留数据，可以在 Visual Studio 中更改“应用程序调试模式” （“Voting” 项目属性）。  

若要查看代码，请完成以下步骤：

1. 打开 **VotingWeb\VotesController.cs** 文件，并在此 Web API 的 **Put** 方法（第 72 行）中设置断点。

2. 打开 **VotingData\VoteDataController.cs** 文件，并在此 Web API 的 **Put** 方法（第 54 行）中设置断点。

3. 按 **F5** 以调试模式启动应用程序。

4. 返回到浏览器，再单击投票选项或添加新的投票选项。 触发 Web 前端 API 控制器中的第一个断点。
    

   1. 此时，浏览器中的 JavaScript 将请求发送到前端服务中的 Web API 控制器。

      ![添加投票前端服务](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. 首先，为后端服务构建 ReverseProxy 的 URL **(1)** 。
   3. 然后，向 ReverseProxy 发送 HTTP PUT 请求 **(2)** 。
   4. 最后，将后端服务的响应返回到客户端 **(3)** 。

5. 按 F5 以继续操作  。
   1. 此时，到达后端服务中的断点。

      ![添加投票后端服务](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. 方法 **(1)** 的第一行使用 `StateManager` 获取或添加 `counts` 可靠字典。
   3. 与可靠字典中的值进行的所有交互都需要使用事务，这个 using 语句 (2)  负责创建此事务。
   4. 在事务中更新投票选项的相关键值，并提交操作 **(3)** 。 提交方法返回后，便会更新字典中的数据，并将数据复制到群集中的其他节点。 数据现在安全地存储在群集中，并且后端服务可以故障转移到其他节点，同时数据仍可用。
6. 按 F5 以继续操作  。

若要停止调试会话，请按 Shift+F5  。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 创建 ASP.NET Core Web API 服务作为有状态可靠服务
> * 创建 ASP.NET Core Web 应用程序服务作为无状态 Web 服务
> * 使用反向代理与有状态服务通信

转到下一教程：
> [!div class="nextstepaction"]
> [将应用程序部署到 Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
