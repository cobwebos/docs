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
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 497582138504250b3c4a77dab440d29ad928a7d8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>使用 ASP.NET Core Web API 前端服务和有状态后端服务创建并部署应用程序
本教程是一个系列中的第一部分。  其中介绍了如何使用 ASP.NET Core Web API 前端和有状态后端服务创建 Azure Service Fabric 应用程序以存储数据。 完成后，将生成一个投票应用程序，其中包含 ASP.NET Core Web 前端，用于将投票结果保存到群集的有状态后端服务中。 如果不想手动创建投票应用程序，可以[下载已完成应用程序的源代码](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)，跳到[大致了解投票示例应用程序](#walkthrough_anchor)。

![应用程序关系图](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 将 ASP.NET Core Web API 服务作为有状态可靠服务创建
> * 将 ASP.NET Core Web 应用程序服务作为无状态 Web 服务创建
> * 使用反向代理与有状态服务通信

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 构建 .NET Service Fabric 应用程序
> * [将应用程序部署到远程群集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [使用 Visual Studio Team Services 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
- [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>将 ASP.NET Web API 服务作为 Reliable Services 创建
首先，使用 ASP.NET Core 创建投票应用程序的 Web 前端。 ASP.NET Core 是轻量跨平台的 Web 开发框架，可用于创建现代 Web UI 和 Web API。 若要全面了解 ASP.NET Core 如何与 Service Fabric 集成，强烈建议你通读 [Service Fabric Reliable Services 中的 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 一文。 现可按照本指南快速入门。 若要了解有关 ASP.NET Core 的详细信息，请参阅 [ASP.NET Core 文档](https://docs.microsoft.com/aspnet/core/)。

> [!NOTE]
> 本教程基于[用于 Visual Studio 2017 的 ASP.NET Core 工具](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc)。 用于 Visual Studio 2015 的 .NET Core 工具不再更新。

1. 以管理员身份启动 Visual Studio。

2. 通过单击“文件”->“新建”->“项目”创建项目

3. 在“新建项目”对话框中，选择“云”>“Service Fabric 应用程序”。

4. 将应用程序命名为“Voting”，然后按“确定”。

   ![Visual Studio 中的新建项目对话框](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. 在“新建 Service Fabric 服务”页中，选择“无状态 ASP.NET Core”，然后将服务命名为“VotingWeb”。
   
   ![在新建服务对话框中选择 ASP.NET Web 服务](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 下一页将提供一组 ASP.NET Core 项目模板。 本教程中，选择“Web 应用程序(MVC)”。 
   
   ![选择 ASP.NET 项目类型](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio 会创建应用程序和服务项目，并在解决方案资源管理器中显示它们。

   ![使用 ASP.NET Core Web API 服务创建应用程序之后的解决方案资源管理器]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>将 AngularJS 添加到 VotingWeb 服务
使用内置的 [Bower 支持](/aspnet/core/client-side/bower)将 [AngularJS](http://angularjs.org/) 添加到服务。 打开 bower.json，添加 angular 和 angular-bootstrap 的条目，然后保存所做更改。

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
保存 bower.json 文件后，Angular 安装在项目的 wwwroot/lib 文件夹中。 此外，它还会在 Dependencies/Bower 文件夹内列出。

### <a name="update-the-sitejs-file"></a>更新 site.js 文件
打开 wwwroot/js/site.js 文件。  将其内容替换为“主文件夹”视图所用的 JavaScript：

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
打开特定于主文件夹控制器的视图 Views/Home/Index.cshtml 文件。  将其内容替换为以下内容，然后保存所做更改。

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
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

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

### <a name="update-the-layoutcshtml-file"></a>更新 _Layout.cshtml 文件
打开 ASP.NET 应用的默认布局 Views/Shared/_Layout.cshtml 文件。  将其内容替换为以下内容，然后保存所做更改。

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

</head>
<body>
    <div class="container body-content">
        @RenderBody()
    </div>

    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/lib/angular/angular.js"></script>
    <script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
    <script src="~/js/site.js"></script>

    @RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>更新 VotingWeb.cs 文件
打开 VotingWeb.cs 文件，该文件会使用 WebListener Web 服务器在无状态服务内创建 ASP.NET Core WebHost。  

在文件顶部，添加 `using System.Net.Http;` 指令。  

将 `CreateServiceInstanceListeners()` 函数替换为以下内容，然后保存所做更改。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>添加 VotesController.cs 文件
添加定义投票操作的控制器。 右键单击“控制器”文件夹，然后选择“添加”->“新建项目”->“类”。  将文件命名为“VotesController.cs”，然后单击“添加”。  

将文件内容替换为以下内容，然后保存所做更改。  之后，在[更新 VotesController.cs 文件](#updatevotecontroller_anchor)中，将修改此文件，以读取和写入来自后端服务的投票数据。  现在，控制器会将静态字符串数据返回到视图中。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
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
创建 VotingWeb 前端服务后，Visual Studio 会随机选择服务侦听的端口。  VotingWeb 服务充当此应用程序的前端并接受外部流量，因此让我们将此服务绑定到已知的固定端口。 在解决方案资源管理器中，打开“VotingWeb/PackageRoot/ServiceManifest.xml”。  在“资源”部分中查找“终结点”资源，并将“端口”值更改为 80 或其他端口。 若要在本地部署和运行应用程序，应用程序侦听端口必须为打开状态且在你的计算机上可用。

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

此外，更新投票项目中的应用程序 URL 属性值，使 Web 浏览器在你使用“F5”进行调试时打开到正确的端口。  在解决方案资源管理器中，选择“投票”项目并更新“应用程序 URL”属性。

![应用程序 URL](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

### <a name="deploy-and-run-the-application-locally"></a>在本地部署并运行应用程序
现可继续运行应用程序。 在 Visual Studio 中按 `F5`，部署用于调试的应用程序。 如果此前未以管理员身份打开 Visual Studio，则 `F5` 会失败。

> [!NOTE]
> 首次在本地运行和部署应用程序时，Visual Studio 会创建用于调试的本地群集。  创建群集可能需要一段时间。 群集创建状态显示在 Visual Studio 输出窗口中。

此时，Web 应用应如下所示：

![ASP.NET Core 前端](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

若要停止调试应用程序，请返回到 Visual Studio 并按 Shift+F5。

## <a name="add-a-stateful-back-end-service-to-your-application"></a>向应用程序添加有状态后端服务
在应用程序中运行 ASP.NET Web API 服务以后，即可进一步添加有状态 Reliable Services，以便在应用程序中存储一些数据。

Service Fabric 允许使用 Reliable Collections 直接在服务内以一致、可靠的方式存储数据。 Reliable Collections 是一组高度可用的可靠集合类，用过 C# 集合的用户都对它很熟悉。

在本教程中，创建一个服务，用于在 Reliable Collections 中存储计数器值。

1. 在解决方案资源管理器中，右键单击应用程序项目中的“**服务**”，并选择“**添加”>“新建 Service Fabric 服务**”。
   
    ![将一个新服务添加到现有应用程序](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. 在“新建 Service Fabric 服务”对话框中，选择“有状态 ASP.NET Core”，将服务命名为“VotingData”，然后按“确定”。

    ![Visual Studio 中的新建服务对话框](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    创建服务项目后，应用程序中会有两个服务。 随着继续生成应用程序，可采用相同的方式添加更多服务。 每个服务都可以单独进行版本控制和升级。

3. 下一页将提供一组 ASP.NET Core 项目模板。 本教程中，选择“Web API”。

    ![选择 ASP.NET 项目类型](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio 会创建一个服务项目，并在解决方案资源管理器中显示它们。

    ![解决方案资源管理器](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>添加 VoteDataController.cs 文件

在“VotingData”项目中，右键单击“控制器”文件夹，然后选择“添加”->“新建项目”->“类”。 将文件命名为“VoteDataController.cs”，然后单击“添加”。 将文件内容替换为以下内容，然后保存所做更改。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
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
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

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
下一步会连接这两个服务，使前端 Web 应用程序获取并设置来自后端服务的投票信息。

在如何与 Reliable Services 通信方面，Service Fabric 提供十足的弹性。 在单个应用程序中，可能有能够通过 TCP 访问的服务。 其他服务也许可以通过 HTTP REST API 访问，并且仍可通过 Web 套接字访问。 有关可用选项和相关权衡取舍的背景信息，请参阅[与服务通信](service-fabric-connect-and-communicate-with-services.md)。

在本教程中，我们将使用 [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md)。

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>更新 VotesController.cs 文件
在“VotingWeb”项目中，打开 Controllers/VotesController.cs 文件。  将 `VotesController` 类定义内容替换为以下内容，然后保存所做更改。

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>大致了解投票示例应用程序
投票应用程序由以下两个服务组成：
- Web 前端服务 (VotingWeb) - ASP.NET Core Web 前端服务，可提供网页服务，并公开用于与后端服务进行通信的 Web API。
- 后端服务 (VotingData) - ASP.NET Core Web 服务，可公开用于将投票结果存储到磁盘上保留的可靠字典中的 API。

![应用程序关系图](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

在应用程序中投票时，将会发生以下事件：
1. JavaScript 将投票请求作为 HTTP PUT 请求发送给 Web 前端服务中的 Web API。

2. Web 前端服务使用代理定位并将 HTTP PUT 请求转发给后端服务。

3. 后端服务接收传入请求，并将更新后的结果存储在可靠字典中（结果复制到群集内的多个节点，并保留在磁盘上）。 应用程序的所有数据都存储在群集中，因此无需使用数据库。

## <a name="debug-in-visual-studio"></a>在 Visual Studio 中进行调试
在 Visual Studio 中调试应用程序时，使用的是本地 Service Fabric 开发群集。 可以根据需要针对自己的方案调整调试体验。 在此应用程序中，我们使用可靠字典将数据存储到后端服务中。 停止调试程序时，Visual Studio 会默认删除应用程序。 删除应用程序后，后端服务中的数据也会随之一起删除。 若要跨调试会话保留数据，可以将“应用程序调试模式”作为 Visual Studio 中“投票”项目的属性进行更改。

若要查看代码，请完成以下步骤：
1. 打开 VotesController.cs 文件，并在 Web API 的 Put 方法（第 47 行）中设置断点。可以在 Visual Studio 的解决方案资源管理器中搜索此文件。

2. 打开 VoteDataController.cs 文件，并在此 Web API 的 Put 方法（第 50 行）中设置断点。

3. 返回到浏览器，再单击投票选项或添加新的投票选项。 点击 Web 前端 API 控制器中的第一个断点。
    
    1. 此时，浏览器中的 JavaScript 将请求发送到前端服务中的 Web API 控制器。
    
    ![添加投票前端服务](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. 首先，为后端服务构建 ReverseProxy (1) 的 URL。
    3. 然后我们将向 ReverseProxy (2) 发送 HTTP PUT 请求。
    4. 最后，将后端服务的响应返回到客户端 (3)。

4. 按 F5 以继续操作
    1. 此时，到达后端服务中的断点。
    
    ![添加投票后端服务](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. 在方法的第一行（图中标识为 1）中，我们将使用 `StateManager` 获取或添加 `counts` 可靠字典。
    3. 与可靠字典中的值进行的所有交互都需要使用事务，这个 using 语句（图中标识为2）负责创建此事务。
    4. 接下来，我们在事务中更新投票选项的相关键值，并提交操作（图中标识为3）。 提交方法返回后，便会更新字典中的数据，并将数据复制到群集中的其他节点。 数据现在安全地存储在群集中，并且后端服务可以故障转移到其他节点，同时数据仍可用。
5. 按 F5 以继续操作

若要停止调试会话，请按 Shift+F5。


## <a name="next-steps"></a>后续步骤
本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 将 ASP.NET Core Web API 服务作为有状态可靠服务创建
> * 将 ASP.NET Core Web 应用程序服务作为无状态 Web 服务创建
> * 使用反向代理与有状态服务通信

转到下一教程：
> [!div class="nextstepaction"]
> [将应用程序部署到 Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
