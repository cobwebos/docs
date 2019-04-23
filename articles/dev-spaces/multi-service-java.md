---
title: 使用 Java 和 VS Code 运行多个依赖的服务
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
manager: yuvalm
ms.openlocfilehash: 9f371be384214447c9d98f6223dd830f7c240326
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357037"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 进行多服务开发

在本教程中，你将了解如何使用 Azure Dev Spaces 开发多服务应用程序，并了解 Dev Spaces 提供的一些附加优势。

## <a name="call-a-service-running-in-a-separate-container"></a>调用独立容器中运行的服务

在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![多个容器](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下载 *mywebapi* 的示例代码
为节省时间，让我们从 GitHub 存储库下载示例代码。 转到 https://github.com/Azure/dev-spaces，选择“克隆或下载”以下载 GitHub 存储库。 本部分的代码位于 `samples/java/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 VS Code 窗口中打开文件夹 `mywebapi`。
1. 打开**命令面板**（使用“视图”|“命令面板”菜单），并使用“自动完成”来键入并选择此命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。
1. 按 F5，等待服务生成和部署。 当调试控制台中出现类似下面的消息时，你就会知道它已准备就绪：

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. 终结点 URL 将类似于 `http://localhost:<portnumber>`。 **提示：VS Code 状态栏会显示一个可点击的 URL。** 容器看起来像是在本地运行，但实际上是在 Azure 的开发空间中运行。 出现 localhost 地址的原因是，`mywebapi` 尚未定义任何公共终结点，只能从 Kubernetes 实例内部访问。 为方便操作以及便于与本地计算机中的专用服务交互，Azure Dev Spaces 将与 Azure 中运行的容器建立临时的 SSH 隧道。
1. 当 `mywebapi` 准备就绪时，请在浏览器中打开 localhost 地址。
1. 如果成功执行所有步骤，应会看到 `mywebapi` 服务发出的响应。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>从 *webfrontend* 向 *mywebapi* 发出请求
现在，让我们在 `webfrontend` 中编写用于向 `mywebapi` 发出请求的代码。
1. 切换到 `webfrontend` 的 VS Code 窗口。
1. 在 `package` 语句下*添加*以下 `import` 语句：

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *替换* greeting 方法的代码：

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

上面的代码示例将传入请求中的 `azds-route-as` 标头转发给传出请求。 本文稍后将会介绍此函数如何帮助团队实现协作性开发。

### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在 `webapi` 项目的 `index()` 方法中设置一个断点。
1. 在 `webfrontend` 项目中，紧靠在向 `mywebapi` 发送 GET 请求的代码位置前面设置一个断点，位于以 `try` 开头的行上。
1. 在 `webfrontend` 项目中按 F5（如果当前正在运行，则重新启动调试）。
1. 调用 Web 应用，并对两个服务中的代码进行单步调试。
1. 在 Web 应用中，“关于”页将会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。

### <a name="automatic-tracing-for-http-messages"></a>自动跟踪 HTTP 消息
你可能已注意到，虽然 *webfrontend* 未包含任何特殊代码来输出它对 *mywebapi* 的 HTTP 调用，但是你可以在输出窗口中看到 HTTP 跟踪消息：
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --ytv-> webfrontend:8080:
   GET /greeting?_=1544503627515 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --ve4-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-ve4-- mywebapi:
   HTTP/1.1 200
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-ytv-- webfrontend:8080:
   HTTP/1.1 200
   Hello from webfrontend and
   Hello from mywebapi
```
这是可以从 Dev Spaces 获得的“免费”权益之一。 我们插入了一些组件，用于在 HTTP 请求通过系统时对其进行跟踪，以便你在开发期间更轻松地跟踪复杂的多服务调用。

### <a name="well-done"></a>干得不错！
现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的团队开发](team-development-java.md)
