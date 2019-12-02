---
title: 使用 .NET Core 和 Visual Studio 进行团队开发
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 895d2edbb268eab9944909ecda7193ce945bbf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325538"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>使用 .NET Core 和 Visual Studio 通过 Azure Dev Spaces 进行团队开发

本教程介绍开发人员团队如何同时使用 Dev Spaces 在同一个 Kubernetes 群集中进行协作。

## <a name="learn-about-team-development"></a>了解团队开发

到目前为止，在运行应用程序代码的过程中，如同你是唯一的一位开发人员在操作该应用。 本部分介绍 Azure Dev Spaces 如何简化团队开发：
* 通过根据需要在共享的开发空间或不同的开发空间中工作，使一组开发人员能够在相同的环境中工作。
* 支持每个开发人员在隔离环境中迭代其代码，而不必担心打断其他人的工作。
* 在提交代码之前进行端到端的代码测试，而无需创建虚拟模型或模拟依赖关系。

### <a name="challenges-with-developing-microservices"></a>开发微服务时的挑战
示例应用程序暂时并不复杂。 但在实际开发中，随着服务的不断添加和开发团队的扩张，挑战很快就会出现。

* 开发计算机可能没有足够的资源来同时运行所需的每个服务。
* 某些服务可能需要允许公开访问。 例如，某个服务需要通过一个终结点来响应 Webhook。
* 若要运行一部分服务，必须知道所有服务之间的整个依赖关系层次结构。 确定这种层次结构可能很困难，尤其是服务数量增加时。
* 某些开发人员倾向于模拟许多服务依赖项。 这种方法可能有助于解决问题，但管理这些虚拟模型很快又会影响到开发成本。 此外，这种方法会导致开发环境看上去与生产环境不同，并且会引入一些不易察觉的 bug。
* 执行任何形式的集成测试随之也会变得很困难。 集成测试只能在提交后以切实可行的方式进行，这意味着，在开发周期的后期才能发现问题。

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>在共享的开发空间中工作
借助 Azure Dev Spaces，可以在 Azure 中设置共享的开发空间。  每个开发人员只需专注于他（她）所负责的应用程序部分，并可以在已包含其方案所依赖的其他所有服务和云资源的开发空间中，以迭代方式开发预提交代码。  依赖项始终保持最新，开发人员就像是在生产环境中工作一样。

### <a name="work-in-your-own-space"></a>在自己的空间中工作
在开发服务的代码过程中，准备好签入代码之前，代码通常不会处于良好状态。 仍需以迭代方式对其进行调整和测试，并在解决方案中体验该代码。 Azure Dev Spaces 提供“空间”的概念，使你能够在隔离环境中工作，而不必担心打断其他团队成员的工作。 

## <a name="use-dev-spaces-for-team-development"></a>使用 Dev Spaces 进行团队开发
让我们使用 *webfrontend* -> *mywebapi* 示例应用程序，通过一个具体的示例来演示这些思路。 情景假设：开发人员 Scott 需要对 *mywebapi* 服务（且只有该服务）进行更改。  在 Scott 进行更新的过程中不需要更改 *webfrontend*。

如果不使用 Dev Spaces，Scott 可以通过多种方法来开发和测试其更新，但这些方法都不理想： 
* 在本地运行所有组件，这需要一台装有 Docker 的强大开发计算机，并可能需要使用 MiniKube。
* 在 Kubernetes 群集上的隔离命名空间中运行所有组件。 由于 *webfrontend* 不会更改，因此使用隔离的命名空间会浪费群集资源。
* 仅运行 *mywebapi*，并在测试时发出手动 REST 调用。 此类测试不会测试完整的端到端流。
* 将侧重于开发的代码添加到 *webfrontend*，使开发人员能够向 *mywebapi* 的不同实例发送请求。 添加此代码会使 *webfrontend* 服务变得复杂。

### <a name="set-up-your-baseline"></a>设置基线
首先需要部署服务的基线。 此部署表示“上次已知正常”，使你能够轻松比较本地代码与已签入版本的行为。 然后，基于此基线创建子空间，以便可以在较大应用程序的上下文中测试对 *mywebapi* 所做的更改。

1. 克隆 [Dev Spaces 示例应用程序](https://github.com/Azure/dev-spaces)：`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. 签出远程分支 *azds_updates*：`git checkout -b azds_updates origin/azds_updates`
1. 关闭这两个服务的任何 F5/调试会话，但在 Visual Studio 窗口保持项目打开。
1. 切换到包含 _mywebapi_ 项目的 Visual Studio 窗口。
1. 在“解决方案资源管理器”中右键单击该项目，并选择“属性”。  
1. 选择左侧的“调试”选项卡，显示 Azure Dev Spaces 设置。 
1. 选择“更改”，以创建调试服务（使用 F5 或 Ctrl+F5）时使用的空间。 
1. 在“空间”下拉列表中，选择“\<创建新空间...\>”。 
1. 确保父空间设置为“\<无\>”，然后输入空间名称 **dev**。  单击“确定”。
1. 按 Ctrl+F5，在不附加调试程序的情况下运行 _mywebapi_。
1. 切换到包含 _webfrontend_ 项目的 Visual Studio 窗口，同样按 Ctrl+F5 运行该项目。

> [!Note]
> 最初按 Ctrl+F5 显示网页后，有时需要刷新浏览器。

> [!TIP]
> 使用上述步骤可手动设置基线，但我们建议团队使用 CI/CD 让基线与提交的代码一起保持最新状态。
>
> 查看[有关使用 Azure DevOps 设置 CI/CD 的指南](how-to/setup-cicd.md)，以创建类似于下图的工作流。
>
> ![示例 CI/CD 示意图](media/common/ci-cd-complex.png)

打开公共 URL 并导航到 Web 应用的任何用户将调用编写的代码路径，该代码路径会使用默认的 _dev_ 空间从前到后运行这两个服务。 现在，假设你要继续开发 *mywebapi* - 如何能够完成开发，同时又不干扰使用开发空间的其他开发人员？ 为此，请设置自己的空间。

### <a name="create-a-new-dev-space"></a>创建新的开发空间
在 Visual Studio 中可以创建附加的空间，以便在对服务执行 F5 或 Ctrl+F5 调试时使用。 可将空间命名为任何所需的名称，并且可以灵活定义其含义（例如 _sprint4_ 或 _demo_）。

执行以下操作创建新空间：
1. 切换到包含 *mywebapi* 项目的 Visual Studio 窗口。
2. 在“解决方案资源管理器”中右键单击该项目，并选择“属性”。  
3. 选择左侧的“调试”选项卡，显示 Azure Dev Spaces 设置。 
4. 在此处，可以更改或创建在执行 F5 或 Ctrl+F5 调试时使用的群集和/或空间。 *确保已选择前面创建的 Azure 开发人员空间*。
5. 在“空间”下拉列表中，选择“\<创建新空间...\>”。 

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. 在“添加空间”对话框中，将父空间设置为 **dev**，并为新空间输入一个名称。  可以使用自己的名字来为新空间命名（例如“scott”），使同事能够识别你所在的空间。 单击“确定”。 

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. 现在应会看到，项目属性页中选中了你的 AKS 群集和新空间。

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>更新 *mywebapi* 的代码

1. 在 *mywebapi* 项目中，对 `Controllers/ValuesController.cs` 文件中的 `string Get(int id)` 方法进行代码更改，如下所示：
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. 在此更新的代码块中设置断点（前面可能已设置了一个断点）。
3. 按 F5 启动 _mywebapi_ 服务，这会使用选定的空间启动群集中的服务。 在本例中，选定的空间为 _scott_。

以下示意图可帮助你了解不同空间的工作原理。 紫色路径显示了通过 _dev_ 空间传送的请求；未在 URL 的前面附加空间时，会使用此默认路径。 粉色路径显示了通过 _dev/scott_ 空间传送的请求。

![](media/common/Space-Routing.png)

使用 Azure Dev Spaces 的内置功能可在共享环境中对代码进行端到端测试，而无需每个开发人员在其空间中重新创建完整的服务堆栈。 如本指南的上一步骤中所示，此路由需要在应用代码中转发传播标头。

### <a name="test-code-running-in-the-_devscott_-space"></a>测试 _dev/scott_ 空间中运行的代码
若要结合 *webfrontend* 测试 *mywebapi* 的新版本，请在浏览器中打开 *webfrontend* 的公共接入点 URL (例如 http://dev.webfrontend.123456abcdef.eus.azds.io) 并转到“关于”页。 应会看到原始消息“Hello from webfrontend and Hello from mywebapi”。

现在，请将“scott.s.” 部分添加到 URL，这样它就可以读取类似于 http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io 的内容，并刷新浏览器。 随后应会命中 *mywebapi* 项目中设置的断点。 按 F5 继续，浏览器中应会显示新消息“Hello from webfrontend and mywebapi now says something new”。 这是因为，_dev/scott_ 中已更新的代码的路径正在 *mywebapi* 空间中运行。

创建始终包含最新更改的 _dev_ 空间后，假设应用程序设计为利用 DevSpace 的基于空间的路由（如本教程部分中所述），则更容易发现，Dev Spaces 在较大应用程序的上下文中可为新功能的测试提供很大的帮助。 无需将所有服务部署到专用空间，可以创建派生自 _dev_ 的专用空间，并仅“启动”实际使用的服务。  Dev Spaces 路由基础结构将在专用空间中利用它所能找到的最多服务来处理剩余的工作，同时默认回到 _dev_ 空间中运行的最新版本。 更有利的是，多个开发人员可以同时在其自己的空间中积极开发不同的服务，而不会相互干扰。 

### <a name="well-done"></a>干得不错！
你已完成入门指南！ 你已了解如何：

> [!div class="checklist"]
> * 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
> * 在容器中以迭代方式开发代码。
> * 独立开发两个独立的服务，并使用 Kubernetes 的 DNS 服务发现来调用另一个服务。
> * 在团队环境中高效地开发和测试代码。
> * 使用 Dev Spaces 建立功能基线可在较大微服务应用程序的上下文中轻松测试隔离的更改

现在，已探索了 Azure Dev Spaces，请[与团队成员共享你的开发空间](how-to/share-dev-spaces.md)，并帮助他们了解一起协作是多么容易。

## <a name="clean-up"></a>清理
若要完全删除群集中的某个 Azure Dev Spaces 实例，包括所有设备空间以及其中正在运行的服务，请使用 `az aks remove-dev-spaces` 命令。 请记住，此操作是不可逆的。 可以再次在群集上添加 Azure Dev Spaces 支持，但这就像重新开始一样。 旧服务和空间不会还原。

下面的示例列出你的活动订阅中的 Azure Dev Spaces 控制器，然后删除与资源组“myaks-rg”中的 AKS 群集“myaks”关联的 Azure Dev Spaces 控制器。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
