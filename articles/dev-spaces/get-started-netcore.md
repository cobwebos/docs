---
title: 使用 .NET Core 和 VS Code 在云中创建 Kubernetes 开发环境 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: a57118feb85a010e38d73b758ebfb84d1cc463fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361244"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>通过 .NET Core 开始使用 Azure Dev Spaces

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

现在可以在 Azure 中创建基于 Kubernetes 的开发环境了。

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>安装 Azure CLI
Azure Dev Spaces 需要进行最基本的本地计算机设置。 开发环境的大部分配置存储在云中，可以与其他用户共享。 首先，请下载并运行 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

> [!IMPORTANT]
> 如果已安装 Azure CLI，请确保使用 2.0.32 或更高版本。

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

可以一边等待群集的创建，一边开始开发代码。

## <a name="create-an-aspnet-core-web-app"></a>创建一个 ASP.NET Core Web 应用
如果已安装 [.NET Core](https://www.microsoft.com/net)，则可快速地在名为 `webfrontend` 的文件夹中创建 ASP.NET Core Web 应用。
    
```cmd
dotnet new mvc --name webfrontend
```

或者**从 GitHub 下载示例代码**，方法是：导航到 https://github.com/Azure/dev-spaces，然后选择“克隆或下载”，将 GitHub 存储库下载到本地环境。 本指南的代码位于 `samples/dotnetcore/getting-started/webfrontend` 中。

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>更新内容文件
Azure Dev Spaces 不仅仅是用来让代码在 Kubernetes 中运行，它还可以用来快速地以迭代方式查看所做的代码更改在云的 Kubernetes 环境中的效果。

1. 找到 `./Views/Home/Index.cshtml` 文件，对 HTML 进行编辑。 例如，将第 70 行的 `<h2>Application uses</h2>` 更改为类似 `<h2>Hello k8s in Azure!</h2>` 的内容
1. 保存文件。 稍后会在终端窗口中看到一条消息，指出正在运行的容器中的文件已更新。
1. 转到浏览器并刷新页面。 此时会看到网页显示更新的 HTML。

发生了什么情况？ 对内容文件（例如 HTML 和 CSS）所做的编辑不需要在 .NET Core Web 应用中进行重新编译，因此活动的 `azds up` 命令会自动将任何修改的内容文件同步到 Azure 中正在运行的容器，方便你立即查看所做的内容编辑。

## <a name="update-a-code-file"></a>更新代码文件
更新代码文件需要的工作多一些，因为 .NET Core 应用需重新构建并生成更新的应用程序二进制文件。

1. 在终端窗口中按 `Ctrl+C`（用于停止 `azds up`）。
1. 打开名为 `Controllers/HomeController.cs` 的代码文件，编辑“关于”页面将显示的消息：`ViewData["Message"] = "Your application description page.";`
1. 保存文件。
1. 在终端窗口中运行 `azds up`。 

此命令重新生成容器映像并重新部署 Helm 图表。 若要查看代码更改在运行的应用程序中的效果，请转到 Web 应用中的“关于”菜单。


不过，还有一种更快的开发代码的方法，该方法在下一部分介绍。 

## <a name="debug-a-container-in-kubernetes"></a>在 Kubernetes 中调试容器

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>选择 AZDS 调试配置
1. 若要打开“调试”视图，请单击 VS Code 侧**活动栏**中的“调试”图标。
1. 选择“.NET Core 启动(AZDS)”作为活动的调试配置。

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> 如果在命令面板中看不到任何 Azure Dev Spaces 命令，请确保已安装 Azure Dev Spaces 的 VS Code 扩展。 确保在 VS Code 中打开的工作区是包含 azds.yaml 的文件夹。


### <a name="debug-the-container-in-kubernetes"></a>在 Kubernetes 中调试容器
按 **F5** 在 Kubernetes 中调试代码。

与使用 `up` 命令类似，代码会同步到开发环境，而容器则会在生成后部署到 Kubernetes。 这次调试程序自然会附加到远程容器。

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

在服务器端的代码文件中设置一个断点，例如，在 `Controllers/HomeController.cs` 源文件的 `Index()` 函数中设置断点。 刷新浏览器页面即可命中断点。

可以不受限制地访问调试信息（例如调用堆栈、局部变量、异常信息等），就像在本地执行代码一样。

### <a name="edit-code-and-refresh"></a>编辑代码并刷新
在调试程序处于活动状态的情况下进行代码编辑， 例如修改 `Controllers/HomeController.cs` 中“关于”页面的消息。 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

保存文件，然后在“调试操作”窗格中单击“刷新”按钮。 

![](media/get-started-netcore/debug-action-refresh.png)

Azure Dev Spaces 不会在每次进行代码编辑时都重新生成和重新部署新的容器映像（这通常需要很长时间），而是在现有的容器中以增量方式重新编译代码，加快编辑/调试循环速度。

刷新浏览器中的 Web 应用，然后转到“关于”页面。 此时会看到自定义消息显示在 UI 中。

**现在，我们已掌握了一种快速迭代代码并直接在 Kubernetes 中进行调试的方法！** 接下来，我们了解如何创建和调用另一个容器。

## <a name="call-a-service-running-in-a-separate-container"></a>调用独立容器中运行的服务

在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![多个容器](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下载 *mywebapi* 的示例代码
为节省时间，让我们从 GitHub 存储库下载示例代码。 转到 https://github.com/Azure/dev-spaces，选择“克隆或下载”以下载 GitHub 存储库。 本部分的代码位于 `samples/dotnetcore/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 VS Code 窗口中打开文件夹 `mywebapi`。
1. 按 F5，等待服务生成和部署。 当 VS Code 调试栏出现时，表示服务已准备就绪。
1. 记下终结点 URL（类似于 http://localhost:\<portnumber\>）。 **提示：VS Code 状态栏会显示一个可点击的 URL。** 容器看起来是在本地运行，但实际上是在 Azure 上的开发环境中运行。 出现 localhost 地址的原因是，`mywebapi` 尚未定义任何公共终结点，只能从 Kubernetes 实例内部访问。 为方便操作以及便于与本地计算机中的专用服务交互，Azure Dev Spaces 将与 Azure 中运行的容器建立临时的 SSH 隧道。
1. 当 `mywebapi` 准备就绪时，请在浏览器中打开 localhost 地址。 将 `/api/values` 追加到 URL 的后面，以针对 `ValuesController` 调用默认的 GET API。 
1. 如果成功执行所有步骤，应会看到 `mywebapi` 服务发出的响应。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>从 *webfrontend* 向 *mywebapi* 发出请求
现在，让我们在 `webfrontend` 中编写用于向 `mywebapi` 发出请求的代码。
1. 切换到 `webfrontend` 的 VS Code 窗口。
1. 替换 About 方法的代码：

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
        // headers in the incoming request to any outgoing requests
        using (var client = new HeaderPropagatingHttpClient(this.Request))
        {
            // Call *mywebapi*, and display its response in the page
            var response = await client.GetAsync("http://mywebapi/api/values/1");
            ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
        }

        return View();
    }
    ```

注意如何运用 Kubernetes 的 DNS 服务发现，将该服务引用为 `http://mywebapi`。 **开发环境中的代码运行方式与在生产环境中相同**。

上述代码示例还使用了 `HeaderPropagatingHttpClient` 类。 运行 `azds prep` 时，此帮助器类已添加到代码文件夹。 `HeaderPropagatingHttpClient` 派生自已知的 `HttpClient` 类，它会添加相应的功能，用于将特定标头从现有的 ASP.NET HttpRequest 对象传播到传出的 HttpRequestMessage 对象。 本文稍后将会介绍如何使用此派生类在团队场景中促成更有效率的开发体验。


### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在 `Get(int id)` 方法中设置一个用于处理 `api/values/{id}` GET 请求的断点。
1. 在 `webfrontend` 项目中，紧靠在向 `mywebapi/api/values` 发送 GET 请求的代码位置前面设置一个断点。
1. 在 `webfrontend` 项目中按 F5。
1. 调用 Web 应用，并对两个服务中的代码进行单步调试。
1. 在 Web 应用中，“关于”页将会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。


干得不错！ 现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。

## <a name="learn-about-team-development"></a>了解团队开发

[!INCLUDE[](includes/team-development-1.md)]

让我们看看该项目的运行情况。 转到 `mywebapi` 的 VS Code 窗口，并对 `string Get(int id)` 方法进行代码编辑，例如：

    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
