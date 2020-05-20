---
title: 运行多个依赖服务：.NET Core 和 Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: 本教程演示如何使用 Azure Dev Spaces 和 Visual Studio 在 Azure Kubernetes 服务上调试多服务 .NET Core 应用程序
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: 7f95c21c2cf5b7adcdb34d7bbe2b1f8314c20333
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438403"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>运行多个依赖服务：通过 Azure Dev Spaces 使用 .NET Core 和 Visual Studio 运行

在本教程中，你将了解如何使用 Azure Dev Spaces 开发多服务应用程序，并了解 Dev Spaces 提供的一些附加优势。

## <a name="call-another-container"></a>调用另一个容器
在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下载 *mywebapi* 的示例代码
为节省时间，让我们从 GitHub 存储库下载示例代码。 转到 https://github.com/Azure/dev-spaces ，选择“克隆或下载”以下载 GitHub 存储库。  本部分的代码位于 `samples/dotnetcore/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 Visual Studio 窗口中打开项目 `mywebapi`。 
1. 像前面针对 `webfrontend` 项目所做的一样，从启动设置下拉列表中选择“Azure Dev Spaces”。 这次不要创建新的 AKS 群集，而是选择已创建的群集。 如前所述，将“空间”保留默认值 `default`，并单击“确定”。  在“输出”窗口中可以看到，Visual Studio 开始在开发空间中“预热”此新服务，以便在开始调试时加速操作。
1. 按 F5，等待服务生成和部署。 如果 Visual Studio 状态栏变为橙色，则表示服务已准备就绪
1. 记下“输出”窗口中“Azre Dev Spaces for AKS”窗格内显示的终结点 URL。   该 URL 应类似于 `http://localhost:<portnumber>`。 容器看起来像是在本地运行，但实际上是在 Azure 的开发空间中运行。
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

上面的代码示例将传入请求中的 `azds-route-as` 标头转发给传出请求。 本文稍后将会介绍此类如何在[团队场景](team-development-netcore-visualstudio.md)中促成更有效率的开发体验。

### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在 `Controllers/ValuesController.cs` 文件中的 `Get(int id)` 方法内，设置一个用于处理 `api/values/{id}` GET 请求的断点。
1. 在上述代码粘贴到的 `webfrontend` 项目中，紧靠在向 `mywebapi/api/values` 发送 GET 请求的代码位置前面设置一个断点。
1. 在 `webfrontend` 项目中按 F5。 Visual Studio 会再次打开浏览器并定向到相应的 localhost 端口，同时会显示 Web 应用。
1. 单击页面顶部的“关于”链接，触发 `webfrontend` 项目中的断点。 
1. 按 F10 继续。 随即会触发 `mywebapi` 项目中的断点。
1. 按 F5 继续，然后返回到 `webfrontend` 项目中的代码。
1. 再次按 F5 完成请求，并返回到浏览器中的某个页面。 在 Web 应用中，“关于”页将会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。

### <a name="well-done"></a>干得不错！
现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的团队开发](team-development-netcore-visualstudio.md)
