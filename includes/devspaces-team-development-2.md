---
title: include 文件
description: include 文件
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 7dddf7e48913aea9d84e0f8b66c2c9d29449ee6f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825635"
---
### <a name="run-the-service"></a>运行该服务

1. 点击 F5（或在终端窗口键入 `azds up`）运行该服务。 该服务将自动在你新选择的空间 _dev/scott_ 中运行。 
1. 可以再次运行 `azds list-up` 来确认服务在其自己的空间中运行。 你会注意到 *mywebapi* 的实例现在正在 _dev/scott_ 空间中运行（在 _dev_ 中运行的版本仍在运行，但未列出）。

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. 运行 `azds list-uris`，并注意 *webfrontend* 的接入点 URL。

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. 将 URL 与 scott.s 前缀一起使用以导航到应用程序。 请注意，此更新的 URL 仍可解析。 此 URL 对于 _dev/scott_ 空间是唯一的。 此特殊 URL 表示发送到“Scott URL”的请求会尝试先路由到 _dev/scott_ 空间中的服务，但如果失败，就会回退到 _dev_ 空间中的服务。

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

使用 Azure Dev Spaces 的这一内置功能，可以在共享空间内测试代码，无需每个开发人员都在自己的空间内重新创建完整的服务堆栈。 如本指南的上一步骤中所示，此路由需要应用代码转发传播标头。

### <a name="test-code-in-a-space"></a>在空间中测试代码
若要结合 *webfrontend* 测试 *mywebapi* 的新版本，请在浏览器中打开 *webfrontend* 的公共接入点 URL 并转到“关于”页。 你应看到显示新消息。

现在，删除该 URL 的“scott.s” 部分，并刷新浏览器。 应该会看到旧行为（*mywebapi* 版本在 _dev_ 中运行）。

创建始终包含最新更改的 _dev_ 空间后，假设应用程序设计为利用 DevSpace 的基于空间的路由（如本教程部分中所述），则更容易发现，Dev Spaces 在较大应用程序的上下文中可为新功能的测试提供很大的帮助。 无需将所有服务部署到专用空间，可以创建派生自 _dev_ 的专用空间，并仅“启动”实际使用的服务。 Dev Spaces 路由基础结构将在专用空间中利用它所能找到的最多服务来处理剩余的工作，同时默认回到 _dev_ 空间中运行的最新版本。 更有利的是，多个开发人员可以同时在其自己的空间中积极开发不同的服务，而不会相互干扰。

### <a name="well-done"></a>干得不错！
你已完成入门指南！ 你已了解如何：

> [!div class="checklist"]
> * 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
> * 在容器中以迭代方式开发代码。
> * 独立开发两个独立的服务，并使用 Kubernetes 的 DNS 服务发现来调用另一个服务。
> * 在团队环境中高效地开发和测试代码。
> * 使用 Dev Spaces 建立功能基线可在较大微服务应用程序的上下文中轻松测试隔离的更改

现在，已探索了 Azure Dev Spaces，请[与团队成员共享你的开发空间](../articles/dev-spaces/how-to/share-dev-spaces.md)，并帮助他们了解一起协作是多么容易。

## <a name="clean-up"></a>清理
若要完全删除群集中的某个 Azure Dev Spaces 实例，包括所有设备空间以及其中正在运行的服务，请使用 `az aks remove-dev-spaces` 命令。 请记住，此操作是不可逆的。 可以再次在群集上添加 Azure Dev Spaces 支持，但这就像重新开始一样。 旧服务和空间不会还原。

下面的示例列出你的活动订阅中的 Azure Dev Spaces 控制器，然后删除与资源组“myaks-rg”中的 AKS 群集“myaks”关联的 Azure Dev Spaces 控制器。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```