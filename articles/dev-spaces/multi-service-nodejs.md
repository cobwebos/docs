---
title: 使用 Node.js 和 VS Code 运行多个依赖的服务
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 8d17ece1426c2aedda2ef98cb465dad532d4dba6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57902699"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 进行多服务开发

在本教程中，你将了解如何使用 Azure Dev Spaces 开发多服务应用程序，并了解 Dev Spaces 提供的一些附加优势。

## <a name="call-a-service-running-in-a-separate-container"></a>调用独立容器中运行的服务

在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>打开 *mywebapi* 的示例代码
名为 `samples` 的文件夹下应已包含本指南中 `mywebapi` 的示例代码（如果没有，请转到 https://github.com/Azure/dev-spaces，并选择“克隆或下载”以下载 GitHub 存储库。）本部分的代码位于 `samples/nodejs/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 VS Code 窗口中打开文件夹 `mywebapi`。
1. 打开**命令面板**（使用“视图”|“命令面板”菜单），并使用“自动完成”来键入并选择此命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 此命令不应与 `azds prep` 命令混淆，后者配置部署的项目。
1. 按 F5，等待服务生成和部署。 当调试控制台中出现“正在侦听端口 80”消息时，你就会知道它已准备就绪。
1. 记下终结点 URL（类似于 `http://localhost:<portnumber>`）。 **提示：VS Code 状态栏会显示一个可点击的 URL。** 容器看起来是在本地运行，但实际上是在 Azure 上的开发环境中运行。 出现 localhost 地址的原因是，`mywebapi` 尚未定义任何公共终结点，只能从 Kubernetes 实例内部访问。 为方便操作以及便于与本地计算机中的专用服务交互，Azure Dev Spaces 将与 Azure 中运行的容器建立临时的 SSH 隧道。
1. 当 `mywebapi` 准备就绪时，请在浏览器中打开 localhost 地址。 应会看到 `mywebapi` 服务发来的响应（“Hello from mywebapi”）。


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>从 *webfrontend* 向 *mywebapi* 发出请求
现在，让我们在 `webfrontend` 中编写用于向 `mywebapi` 发出请求的代码。
1. 切换到 `webfrontend` 的 VS Code 窗口。
1. 在 `server.js` 的顶部添加以下代码行：
    ```javascript
    var request = require('request');
    ```

3. 替换 `/api` GET 处理程序的代码。 处理请求时，该代码又会调用 `mywebapi`，然后返回来自这两个服务的结果。

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *删除*位于 `server.js` 末尾的 `server.close()` 行

上面的代码示例将传入请求中的 `azds-route-as` 标头转发给传出请求。 本文稍后将会介绍此函数如何帮助团队实现协作性开发。

### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在默认的 GET `/` 处理程序中设置断点。
1. 在 `webfrontend` 项目中，紧靠在向 `http://mywebapi` 发送 GET 请求的代码位置前面设置一个断点。
1. 在 `webfrontend` 项目中按 F5。
1. 打开 Web 应用，并对两个服务中的代码进行单步调试。 Web 应用应会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。

### <a name="automatic-tracing-for-http-messages"></a>自动跟踪 HTTP 消息
你可能已注意到，虽然 *webfrontend* 未包含任何特殊代码来输出它对 *mywebapi* 的 HTTP 调用，但是你可以在输出窗口中看到 HTTP 跟踪消息：
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --hyh-> webfrontend:
   GET /api?_=1544485357627 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --1b1-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-1b1-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-hyh-- webfrontend:
   HTTP/1.1 200 OK
   Hello from webfrontend and Hello from mywebapi
```
这是可以从 Dev Spaces 获得的“免费”权益之一。 我们插入了一些组件，用于在 HTTP 请求通过系统时对其进行跟踪，以便你在开发期间更轻松地跟踪复杂的多服务调用。

### <a name="well-done"></a>干得不错！
现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的团队开发](team-development-nodejs.md)