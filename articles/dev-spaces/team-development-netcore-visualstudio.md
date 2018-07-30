---
title: 使用 .NET Core 和 Visual Studio 进行适用于 Azure Dev Spaces 的团队开发 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: facf2de40c555ccd79fe73543a85f0b5cc150450
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247477"
---
# <a name="team-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 进行团队开发

本教程介绍如何使用多个 Dev Spaces 在不同的开发环境中同时工作，将不同的工作保存在同一群集的不同 Dev Spaces 中。

## <a name="call-another-container"></a>调用另一个容器
在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下载 *mywebapi* 的示例代码
为节省时间，让我们从 GitHub 存储库下载示例代码。 转到 https://github.com/Azure/dev-spaces，选择“克隆或下载”以下载 GitHub 存储库。 本部分的代码位于 `samples/dotnetcore/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 Visual Studio 窗口中打开项目 `mywebapi`。
1. 像前面针对 `webfrontend` 项目所做的一样，从启动设置下拉列表中选择“Azure Dev Spaces”。 这次不要创建新的 AKS 群集，而是选择已创建的群集。 如前所述，将“空间”保留默认值 `default`，并单击“确定”。 在“输出”窗口中可以看到，Visual Studio 开始在开发空间中“预热”此新服务，以便在开始调试时加速操作。
1. 按 F5，等待服务生成和部署。 如果 Visual Studio 状态栏变为橙色，则表示服务已准备就绪
1. 记下“输出”窗口中“Azre Dev Spaces for AKS”窗格内显示的终结点 URL。 该 URL 应类似于 http://localhost:\<portnumber\>。 容器看起来像是在本地运行，但实际上是在 Azure 的开发空间中运行。
2. `mywebapi` 准备就绪后，在浏览器中打开 localhost 地址，并在 URL 后面追加 `/api/values` 以针对 `ValuesController` 调用默认的 GET API。 
3. 如果成功执行所有步骤，应会看到 `mywebapi` 服务发出的响应，如下所示。

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>从 *webfrontend* 向 *mywebapi* 发出请求
现在，让我们在 `webfrontend` 中编写用于向 `mywebapi` 发出请求的代码。 切换到包含 `webfrontend` 项目的 Visual Studio 窗口。 在 `HomeController.cs` 文件中，将 About 方法的代码替换为以下代码：

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

上面的代码示例将传入请求中的 `azds-route-as` 标头转发给传出请求。 本文稍后将会介绍此类如何在团队场景中促成更有效率的开发体验。

### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在 `Controllers/ValuesController.cs` 文件中的 `Get(int id)` 方法内，设置一个用于处理 `api/values/{id}` GET 请求的断点。
1. 在上述代码粘贴到的 `webfrontend` 项目中，紧靠在向 `mywebapi/api/values` 发送 GET 请求的代码位置前面设置一个断点。
1. 在 `webfrontend` 项目中按 F5。 Visual Studio 会再次打开浏览器并定向到相应的 localhost 端口，同时会显示 Web 应用。
1. 单击页面顶部的“关于”链接，触发 `webfrontend` 项目中的断点。 
1. 按 F10 继续。 随即会触发 `mywebapi` 项目中的断点。
1. 按 F5 继续，然后返回到 `webfrontend` 项目中的代码。
1. 再次按 F5 完成请求，并返回到浏览器中的某个页面。 在 Web 应用中，“关于”页将会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。

干得不错！ 现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。

## <a name="learn-about-team-development"></a>了解团队开发

到目前为止，在运行应用程序代码的过程中，如同你是唯一的一位开发人员在操作该应用。 本部分介绍 Azure Dev Spaces 如何简化团队开发：
* 通过根据需要在共享的开发空间或不同的开发空间中工作，使一组开发人员能够在相同的环境中工作。
* 支持每个开发人员在隔离环境中迭代其代码，而不必担心打断其他人的工作。
* 在提交代码之前进行端到端的代码测试，而无需创建虚拟模型或模拟依赖关系。

### <a name="challenges-with-developing-microservices"></a>开发微服务时的挑战
示例应用程序暂时不很复杂。 但在实际开发中，随着服务的不断添加和开发团队的扩张，挑战很快就会出现。

想像一下，操作一个与其他数十个服务交互的服务，情况会是怎样。

- 开发期间在本地运行所有组件可能不切实际。 开发计算机可能没有足够的资源用于运行整个应用。 或者，应用中的终结点可能需要允许公开访问（例如，你的应用需要响应来自 SaaS 应用的 Webhook）。
- 可以尝试只运行所要依赖的服务，但这意味着，需要知道依赖关系的完整闭环（例如，依赖项的依赖项）。 或者，有时无法轻松知道如何生成和运行依赖项，因为以前未开展过相关的工作。
- 某些开发人员倾向于模拟许多服务依赖项。 有时这有助于解决问题，但管理这些虚拟模型很快又要投入相关的开发工作。 此外，这会导致开发环境看上去与生产环境不同，并且会引入微小的 bug。
- 执行任何形式的端到端测试随之也会变得很困难。 集成测试只能在提交后以切实可行的方式进行，这意味着，在开发周期的后期才能发现问题。

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>在共享的开发空间中工作
借助 Azure Dev Spaces，可以在 Azure 中设置共享的开发空间。 每个开发人员只需专注于他（她）所负责的应用程序部分，并可以在已包含其方案所依赖的其他所有服务和云资源的开发空间中，以迭代方式开发预提交代码。 依赖项始终保持最新，开发人员就像是在生产环境中工作一样。

### <a name="work-in-your-own-space"></a>在自己的空间中工作
在开发服务的代码过程中，准备好签入代码之前，代码通常不会处于良好状态。 仍需以迭代方式对其进行调整和测试，并在解决方案中体验该代码。 Azure Dev Spaces 提供“空间”的概念，使你能够在隔离环境中工作，而不必担心打断其他团队成员的工作。

执行以下操作，以确保 `webfrontend` 和 `mywebapi` 服务**都在`default`开发空间中**运行。
1. 关闭这两个服务的任何 F5/调试会话，但在 Visual Studio 窗口保持项目打开。
2. 切换到包含 `mywebapi` 项目的 Visual Studio 窗口，按 Ctrl+F5，在不附加调试程序的情况下运行服务
3. 切换到包含 `webfrontend` 项目的 Visual Studio 窗口，同样按 Ctrl+F5 运行该项目。

> [!Note]
> 最初按 Ctrl+F5 显示网页后，有时需要刷新浏览器。

打开公共 URL 并导航到 Web 应用的任何用户将调用编写的代码路径，该代码路径会使用默认的 `default` 空间从前到后运行这两个服务。 现在，假设你要继续开发 `mywebapi` - 如何能够完成开发，同时又不干扰使用开发空间的其他开发人员？ 为此，请设置自己的空间。

### <a name="create-a-new-dev-space"></a>创建新的开发空间
在 Visual Studio 中可以创建附加的空间，以便在对服务执行 F5 或 Ctrl+F5 调试时使用。 可将空间命名为任何所需的名称，并且可以灵活定义其含义（例如 `sprint4` 或 `demo`）。

执行以下操作创建新空间：
1. 切换到包含 `mywebapi` 项目的 Visual Studio 窗口。
2. 在“解决方案资源管理器”中右键单击该项目，并选择“属性”。
3. 选择左侧的“调试”选项卡，显示 Azure Dev Spaces 设置。
4. 在此处，可以更改或创建在执行 F5 或 Ctrl+F5 调试时使用的群集和/或空间。 *确保已选择前面创建的 Azure 开发人员空间*。
5. 在“空间”下拉列表中，选择“<创建新空间...>”。

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. 在“添加空间”对话框中，将父空间设置为“默认”，并为新空间输入一个名称。 可以使用自己的名字来为新空间命名（例如“scott”），使同事能够识别你所在的空间。 单击“确定”。

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. 现在应会看到，项目属性页中选中了你的 AKS 群集和新空间。

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>更新 *mywebapi* 的代码

1. 在 `mywebapi` 项目中，对 `Controllers/ValuesController.cs` 文件中的 `string Get(int id)` 方法进行代码更改，如下所示：
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. 在此更新的代码块中设置断点（前面可能已设置了一个断点）。
3. 按 F5 启动 `mywebapi` 服务。 这将使用选定的空间（在本例中为 `scott`）在群集中启动该服务。

以下示意图可帮助你了解不同空间的工作原理。 紫色路径显示了通过 `default` 空间传送的请求；未在 URL 的前面附加空间时，会使用此默认路径。 粉色路径显示了通过 `default/scott` 空间传送的请求。

![](media/common/Space-Routing.png)

使用 Azure Dev Spaces 的内置功能可在共享环境中对代码进行端到端测试，而无需每个开发人员在其空间中重新创建完整的服务堆栈。 如本指南的上一步骤中所示，此路由需要在应用代码中转发传播标头。

### <a name="test-code-running-in-the-defaultscott-space"></a>测试 `default/scott` 空间中运行的代码
若要结合 `webfrontend` 测试 `mywebapi` 的新版本，请在浏览器中打开 `webfrontend` 的公共接入点 URL（例如，输入 http://webfrontend.123456abcdef.eastus.aksapp.io) 并转到“关于”页）。 应会看到原始消息“Hello from webfrontend and Hello from mywebapi”。

现在，请将“scott.s.” 部分添加到 URL，使其类似于 http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io；然后刷新浏览器。 随后应会命中 `mywebapi` 项目中设置的断点。 按 F5 继续，浏览器中应会显示新消息“Hello from webfrontend and mywebapi now says something new”。 这是因为，`default/scott` 中已更新的代码的路径正在 `mywebapi` 空间中运行。

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
