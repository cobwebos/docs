---
title: 运行多个依赖服务：.NET Core 和 Visual Studio Code
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: 453cf1fdf2d37817d12ec32f2cba00b3671b1d11
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325685"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>运行多个依赖服务：通过 Azure Dev Spaces 使用 .NET Core 和 Visual Studio Code 运行

在本教程中，你将了解如何使用 Azure Dev Spaces 开发多服务应用程序，并了解 Dev Spaces 提供的一些附加优势。

## <a name="call-a-service-running-in-a-separate-container"></a>调用独立容器中运行的服务

在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![多个容器](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下载 *mywebapi* 的示例代码
为节省时间，让我们从 GitHub 存储库下载示例代码。 转到 https://github.com/Azure/dev-spaces ，选择“克隆或下载”以下载 GitHub 存储库。  本部分的代码位于 `samples/dotnetcore/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 VS Code 窗口中打开文件夹 `mywebapi`。 
1. 打开**命令面板**（使用“视图”|“命令面板”  菜单），并使用“自动完成”来键入并选择此命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 此命令不应与 `azds prep` 命令混淆，后者配置部署的项目。
1. 按 F5，等待服务生成和部署。 当应用程序启动时，你就会知道它已准备就绪。  按 Ctrl+C 将其关闭。 调试控制台中将显示消息。
1. 终结点 URL 将类似于 `http://localhost:<portnumber>`。 **提示：VS Code 状态栏将变为橙色，并显示一个可点击的 URL。** 容器看起来像是在本地运行，但实际上是在 Azure 的开发空间中运行。 出现 localhost 地址的原因是，`mywebapi` 尚未定义任何公共终结点，只能从 Kubernetes 实例内部访问。 为方便操作以及便于与本地计算机中的专用服务交互，Azure Dev Spaces 将与 Azure 中运行的容器建立临时的 SSH 隧道。
1. 当 `mywebapi` 准备就绪时，请在浏览器中打开 localhost 地址。 将 `/api/values` 追加到 URL 的后面，以针对 `ValuesController` 调用默认的 GET API。
1. 如果成功执行所有步骤，应会看到 `mywebapi` 服务发出的响应。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>从 *webfrontend* 向 *mywebapi* 发出请求
现在，让我们在 `webfrontend` 中编写用于向 `mywebapi` 发出请求的代码。
1. 切换到 `webfrontend` 的 VS Code 窗口。
1. 替换 `HomeController.cs` 中 About 方法的代码： 

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

上面的代码示例将传入请求中的 `azds-route-as` 标头转发给传出请求。 本文稍后将会介绍此函数如何帮助团队实现[协作性开发](team-development-netcore.md)。

### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在 `Get(int id)` 方法中设置一个用于处理 `api/values/{id}` GET 请求的断点。 这大约是 [Controllers/ValuesController.cs  文件中的第 23 行](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23)。
1. 在 `webfrontend` 项目中，紧靠在向 `mywebapi/api/values` 发送 GET 请求的代码位置前面设置一个断点。 这大约是你在上一部分中修改的 [Controllers/HomeController.cs  文件](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs)中的第 32 行。
1. 在 `webfrontend` 项目中按 F5。
1. 调用 Web 应用，并对两个服务中的代码进行单步调试。
1. 在 Web 应用中，“关于”页将会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。

### <a name="well-done"></a>干得不错！
现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的团队开发](team-development-netcore.md)
