---
title: 使用 Java 和 Visual Studio Code 进行团队开发
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: 本教程介绍如何使用 Azure Dev Spaces 和 Visual Studio Code 在 Azure Kubernetes 服务中对 Java 应用程序进行团队开发
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 352671b2fe31095b0ffcaffb49195071a456a892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "78245011"
---
# <a name="team-development-using-java-and-visual-studio-code-with-azure-dev-spaces"></a>通过 Azure Dev Spaces 使用 Java 和 Visual Studio Code 进行团队开发

本教程介绍开发人员团队如何同时使用 Dev Spaces 在同一个 Kubernetes 群集中进行协作。

## <a name="learn-about-team-development"></a>了解团队开发
到目前为止，在运行应用程序代码的过程中，如同你是唯一的一位开发人员在操作该应用。 本部分介绍 Azure Dev Spaces 如何简化团队开发：
* 通过根据需要在共享的开发空间或不同的开发空间中工作，使一组开发人员能够在相同的环境中工作。
* 支持每个开发人员在隔离环境中迭代其代码，而不必担心打断其他人的工作。
* 在提交代码之前进行端到端的代码测试，而无需创建虚拟模型或模拟依赖关系。

### <a name="challenges-with-developing-microservices"></a>开发微服务时的挑战
示例应用程序暂时不很复杂。 但在实际开发中，随着服务的不断添加和开发团队的扩张，挑战很快就会出现。 开发期间在本地运行所有组件可能不切实际。

* 开发计算机可能没有足够的资源来同时运行所需的每个服务。
* 某些服务可能需要允许公开访问。 例如，某个服务需要通过一个终结点来响应 Webhook。
* 若要运行一部分服务，必须知道所有服务之间的整个依赖关系层次结构。 确定这种层次结构可能很困难，尤其是服务数量增加时。
* 某些开发人员倾向于模拟许多服务依赖项。 这种方法可能有助于解决问题，但管理这些虚拟模型很快又会影响到开发成本。 此外，这种方法会导致开发环境看上去与生产环境有很大的不同，并且会催生一些不易察觉的 bug。
* 执行任何形式的集成测试随之也会变得很困难。 集成测试只能在提交后以切实可行的方式进行，这意味着，在开发周期的后期才能发现问题。

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>在共享的开发空间中工作
借助 Azure Dev Spaces，可以在 Azure 中设置共享的开发空间。  每个开发人员只需专注于他（她）所负责的应用程序部分，并可以在已包含其方案所依赖的其他所有服务和云资源的开发空间中，以迭代方式开发预提交代码。  依赖项始终保持最新，开发人员就像是在生产环境中工作一样。

### <a name="work-in-your-own-space"></a>在自己的空间中工作
在开发服务的代码过程中，准备好签入代码之前，代码通常不会处于良好状态。 仍需以迭代方式对其进行调整和测试，并在解决方案中体验该代码。 Azure Dev Spaces 提供“空间”的概念，使你能够在隔离环境中工作，而不必担心打断其他团队成员的工作。 

## <a name="use-dev-spaces-for-team-development"></a>使用 Dev Spaces 进行团队开发
让我们使用 *webfrontend* -> *mywebapi* 示例应用程序，通过一个具体的示例来演示这些思路。 情景假设：开发人员 Scott 需要对 *mywebapi* 服务（且只有该服务）进行更改。  在 Scott 进行更新的过程中不需要更改 *webfrontend*。

如果不使用 Dev Spaces，Scott 可以通过多种方法来开发和测试其更新，但这些方法都不理想： 
* 在本地运行所有组件。 这需要一台装有 Docker 的强大开发计算机，并可能需要使用 MiniKube。
* 在 Kubernetes 群集上的隔离命名空间中运行所有组件。 由于 *webfrontend* 不会更改，因此这种方法会浪费群集资源。
* 仅运行 *mywebapi*，并在测试时发出手动 REST 调用。 这不会测试完整的端到端流。
* 将侧重于开发的代码添加到 *webfrontend*，使开发人员能够向 *mywebapi* 的不同实例发送请求。 此代码会使 *webfrontend* 服务变得复杂。

### <a name="set-up-your-baseline"></a>设置基线
首先需要部署服务的基线。 此部署表示“上次已知正常”，使你能够轻松比较本地代码与已签入版本的行为。 然后，基于此基线创建子空间，以便可以在较大应用程序的上下文中测试对 *mywebapi* 所做的更改。

1. 克隆 [Dev Spaces 示例应用程序](https://github.com/Azure/dev-spaces)：`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. 签出远程分支 *azds_updates*：`git checkout -b azds_updates origin/azds_updates`
1. 选择 _dev_ 空间：`azds space select --name dev`。 当系统提示选择父 dev 空间时，请选择“\<无\>”。
1. 导航到 _mywebapi_ 目录并执行 `azds up -d`
1. 导航到 _webfrontend_ 目录并执行 `azds up -d`
1. 执行 `azds list-uris` 以查看 _webfrontend_ 的公共终结点

> [!TIP]
> 使用上述步骤可手动设置基线，但我们建议团队使用 CI/CD 让基线与提交的代码一起保持最新状态。
>
> 查看[有关使用 Azure DevOps 设置 CI/CD 的指南](how-to/setup-cicd.md)，以创建类似于下图的工作流。
>
> ![示例 CI/CD 图](media/common/ci-cd-complex.png)

此时基线应会运行。 运行 `azds list-up --all` 命令，将会看到如下所示的输出：

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

DevSpace 列显示这两个服务都在名为 _dev_ 的空间中运行。 打开公共 URL 并导航到 Web 应用的任何人都会调用通过这两个服务运行的已签入代码路径。 现在，假设你要继续开发 _mywebapi_。 如何能够做出代码更改并对其测试，同时又不干扰其他使用开发环境的开发人员？ 为此，请设置自己的空间。

### <a name="create-a-dev-space"></a>创建开发空间
若要在除 _dev_ 以外的空间中运行自己的 _mywebapi_ 版本，可使用以下命令创建自己的空间：

```cmd
azds space select --name scott
```

出现提示时，选择“dev”作为“父 dev 空间”。   这意味着，新空间 _dev/scott_ 将派生自空间 _dev_。 很快，我们将会看到这将如何帮助我们进行测试。

沿用简介中提出的假设，我们对新空间使用了名称 _scott_，使同事知道谁在该空间中操作。 但是，也可以根据空间的用途灵活命名，例如 _sprint4_ 或 _demo_。 在任何情况下，对于此应用程序的所有开发人员而言，_dev_ 都充当基线：

![](media/common/ci-cd-space-setup.png)

运行 `azds space list` 命令查看开发环境中所有空间的列表。 “选定”列指示当前已选择的空间 (true/false)。  在本例中，创建名为 _dev/scott_ 的空间后已自动选中它。 随时可以使用 `azds space select` 命令选择另一个空间。

让我们看看该项目的运行情况。

### <a name="make-a-code-change"></a>进行代码更改
转到 `mywebapi` 的 VS Code 窗口，并在 `src/main/java/com/ms/sample/mywebapi/Application.java` 中对 `String index()` 方法进行代码编辑，例如：

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

### <a name="run-the-service"></a>运行该服务

若要运行此服务，请按 F5（或在终端窗口键入 `azds up`）。 该服务将自动在你新选择的空间 _dev/scott_ 中运行。 运行 `azds list-up` 来确认服务在其自己的空间中运行：

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

注意 *mywebapi* 的实例现在正在 _dev/scott_ 空间中运行。 在 _dev_ 中运行的版本仍在运行，但未列出。

通过运行 `azds list-uris` 列出当前空间的 URL。

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

请注意，*webfrontend* 的公共访问点 URL 带有 *scott.s* 前缀。 此 URL 对于 _dev/scott_ 空间是唯一的。 此 URL 前缀告知入口控制器将请求路由到服务的 _dev/scott_ 版本。 当 Dev Spaces 处理包含此 URL 的请求时，入口控制器首先尝试将请求路由到 _dev/scott_ 空间中的 *webfrontend* 服务。 如果该操作失败，则会将请求作为回退路由到 _dev_ 空间中的 *webfrontend* 服务。 另请注意，可以使用一个 localhost URL 通过 Kubernetes 的端口转发  功能经 localhost 访问服务。 若要详细了解 Azure Dev Spaces 中的 URL 和路由，请参阅 [Azure Dev Spaces 的工作原理及其配置方式](how-dev-spaces-works.md)。

![空间路由](media/common/Space-Routing.png)

使用 Azure Dev Spaces 的这一内置功能，可以在共享空间内测试代码，无需每个开发人员都在自己的空间内重新创建完整的服务堆栈。 如本指南的上一步骤中所示，此路由需要应用代码转发传播标头。

### <a name="test-code-in-a-space"></a>在空间中测试代码
若要结合 *webfrontend* 测试 *mywebapi* 的新版本，请在浏览器中打开 *webfrontend* 的公共接入点 URL 并转到“关于”页。 你应看到显示新消息。

现在，删除该 URL 的“scott.s” 部分，并刷新浏览器。 应该会看到旧行为（*mywebapi* 版本在 _dev_ 中运行）。

创建始终包含最新更改的 _dev_ 空间后，假设应用程序设计为利用 DevSpace 的基于空间的路由（如本教程部分中所述），则更容易发现，Dev Spaces 在较大应用程序的上下文中可为新功能的测试提供很大的帮助。 无需将所有服务部署到专用空间，可以创建派生自 _dev_ 的专用空间，并仅“启动”实际使用的服务。 Dev Spaces 路由基础结构将在专用空间中利用它所能找到的最多服务来处理剩余的工作，同时默认回到 _dev_ 空间中运行的最新版本。 更有利的是，多个开发人员可以同时在其自己的空间中积极开发不同的服务，而不会相互干扰。 

### <a name="well-done"></a>干得不错！
你已完成入门指南！ 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
> * 在容器中以迭代方式开发代码。
> * 独立开发两个独立的服务，并使用 Kubernetes 的 DNS 服务发现来调用另一个服务。
> * 在团队环境中高效地开发和测试代码。
> * 使用 Dev Spaces 建立功能基线可在较大微服务应用程序的上下文中轻松测试隔离的更改

现在已探索了Azure Dev Spaces，请[与团队成员共享开发空间](how-to/share-dev-spaces.md)并开始协作。

## <a name="clean-up"></a>清除
若要完全删除群集中的某个 Azure Dev Spaces 实例，包括所有设备空间以及其中正在运行的服务，请使用 `az aks remove-dev-spaces` 命令。 请记住，此操作是不可逆的。 可以再次在群集上添加 Azure Dev Spaces 支持，但这就像重新开始一样。 旧服务和空间不会还原。

下面的示例列出你的活动订阅中的 Azure Dev Spaces 控制器，然后删除与资源组“myaks-rg”中的 AKS 群集“myaks”关联的 Azure Dev Spaces 控制器。

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
