---
title: 使用 VS Code 通过 Azure Dev Spaces 进行团队开发 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: 8a7200723b39c40fe9596bbab0acaf752acbbaf8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451387"
---
# <a name="team-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 进行团队开发

本教程介绍如何使用多个 Dev Spaces 在不同的开发环境中同时工作，将不同的工作保存在同一群集的不同 Dev Spaces 中。

## <a name="call-a-service-running-in-a-separate-container"></a>调用独立容器中运行的服务

在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>打开 *mywebapi* 的示例代码
名为 `samples` 的文件夹下应已包含本指南中 `mywebapi` 的示例代码（如果没有，请转到 https://github.com/Azure/dev-spaces，并选择“克隆或下载”以下载 GitHub 存储库。）本部分的代码位于 `samples/nodejs/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 VS Code 窗口中打开文件夹 `mywebapi`。
1. 打开**命令面板**（使用“视图”|“命令面板”菜单），并使用“自动完成”来键入并选择此命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 此命令不应与 `azds prep` 命令混淆，后者配置部署的项目。
1. 按 F5，等待服务生成和部署。 当 VS Code 调试栏出现时，表示服务已准备就绪。
1. 记下终结点 URL（类似于 http://localhost:\<portnumber\>）。 **提示：VS Code 状态栏会显示一个可点击的 URL。** 容器看起来是在本地运行，但实际上是在 Azure 上的开发环境中运行。 出现 localhost 地址的原因是，`mywebapi` 尚未定义任何公共终结点，只能从 Kubernetes 实例内部访问。 为方便操作以及便于与本地计算机中的专用服务交互，Azure Dev Spaces 将与 Azure 中运行的容器建立临时的 SSH 隧道。
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
 4. *删除*位于 `server.js` 末尾的 `server.close()` 行

上面的代码示例将传入请求中的 `azds-route-as` 标头转发给传出请求。 本文稍后将会介绍此函数如何帮助团队实现协作性开发。

### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在默认的 GET `/` 处理程序中设置断点。
1. 在 `webfrontend` 项目中，紧靠在向 `http://mywebapi` 发送 GET 请求的代码位置前面设置一个断点。
1. 在 `webfrontend` 项目中按 F5。
1. 打开 Web 应用，并对两个服务中的代码进行单步调试。 Web 应用应会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。

干得不错！ 现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。

## <a name="learn-about-team-development"></a>了解团队开发

[!INCLUDE [](../../includes/team-development-1.md)]

现在，让我们看看该项目的运行情况：
1. 转到 `mywebapi` 的 VS Code 窗口，并对默认的 GET `/` 处理程序进行代码编辑，例如：

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>干得不错！
你已完成入门指南！ 你已了解如何：

> [!div class="checklist"]
> * 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
> * 在容器中以迭代方式开发代码。
> * 独立开发两个独立的服务，并使用 Kubernetes 的 DNS 服务发现来调用另一个服务。
> * 在团队环境中高效地开发和测试代码。

现在，已探索了 Azure Dev Spaces，请[与团队成员共享你的开发空间](how-to/share-dev-spaces.md)，并帮助他们了解一起协作是多么容易。

## <a name="clean-up"></a>清理
若要完全删除群集中的某个 Azure Dev Spaces 实例，包括所有设备空间以及其中正在运行的服务，请使用 `az aks remove-dev-spaces` 命令。 请记住，此操作是不可逆的。 可以再次在群集上添加 Azure Dev Spaces 支持，但这就像重新开始一样。 旧服务和空间不会还原。

下面的示例列出你的活动订阅中的 Azure Dev Spaces 控制器，然后删除与资源组“myaks-rg”中的 AKS 群集“myaks”关联的 Azure Dev Spaces 控制器。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```




