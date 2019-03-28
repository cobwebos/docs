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
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439511"
---
# <a name="team-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 进行团队开发

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

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>在共享的开发空间中工作
借助 Azure Dev Spaces，可以在 Azure 中设置共享的开发空间。 每个开发人员只需专注于他（她）所负责的应用程序部分，并可以在已包含其方案所依赖的其他所有服务和云资源的开发空间中，以迭代方式开发预提交代码。 依赖项始终保持最新，开发人员就像是在生产环境中工作一样。

### <a name="work-in-your-own-space"></a>在自己的空间中工作
在开发服务的代码过程中，准备好签入代码之前，代码通常不会处于良好状态。 仍需以迭代方式对其进行调整和测试，并在解决方案中体验该代码。 Azure Dev Spaces 提供“空间”的概念，使你能够在隔离环境中工作，而不必担心打断其他团队成员的工作。

## <a name="use-dev-spaces-for-team-development"></a>使用 Dev Spaces 进行团队开发
让我们使用 *webfrontend* -> *mywebapi* 示例应用程序，通过一个具体的示例来演示这些思路。 情景假设：开发人员 Scott 需要对 *mywebapi* 服务（且只有该服务）进行更改。 在 Scott 进行更新的过程中不需要更改 *webfrontend*。

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
> 查看[有关使用 Azure DevOps 设置 CI/CD 的指南](../articles/dev-spaces/how-to/setup-cicd.md)，以创建类似于下图的工作流。
>
> ![示例 CI/CD 示意图](../articles/dev-spaces/media/common/ci-cd-complex.png)

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

出现提示时，选择“dev”作为“父 dev 空间”。 这意味着，新空间 _dev/scott_ 将派生自空间 _dev_。 很快，我们将会看到这将如何帮助我们进行测试。

沿用简介中提出的假设，我们对新空间使用了名称 _scott_，使同事知道谁在该空间中操作。 但是，也可以根据空间的用途灵活命名，例如 _sprint4_ 或 _demo_。 在任何情况下，对于此应用程序的所有开发人员而言，_dev_ 都充当基线：

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

运行 `azds space list` 命令查看开发环境中所有空间的列表。 “选定”列指示当前已选择的空间 (true/false)。 在本例中，创建名为 _dev/scott_ 的空间后已自动选中它。 随时可以使用 `azds space select` 命令选择另一个空间。

让我们看看该项目的运行情况。