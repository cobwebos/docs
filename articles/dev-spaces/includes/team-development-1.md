---
title: include 文件
description: include 文件
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 404e238e51b7ac8b799f413965560a8d42ccc5df
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371110"
---
到目前为止，在运行应用程序代码的过程中，如同你是唯一的一位开发人员在操作该应用。 本部分介绍 Azure Dev Spaces 如何简化团队开发：
* 使开发人员团队能够在同一个开发环境中工作。
* 支持每个开发人员在隔离环境中迭代其代码，而不必担心打断其他人的工作。
* 在提交代码之前进行端到端的代码测试，而无需创建虚拟模型或模拟依赖关系。

## <a name="challenges-with-developing-microservices"></a>开发微服务时的挑战
示例应用程序暂时不很复杂。 但在实际开发中，随着服务的不断添加和开发团队的扩张，挑战很快就会出现。

想像一下，操作一个与其他数十个服务交互的服务，情况会是怎样。

- 开发期间在本地运行所有组件可能不切实际。 开发计算机可能没有足够的资源用于运行整个应用。 或者，应用中的终结点可能需要允许公开访问（例如，你的应用需要响应来自 SaaS 应用的 Webhook）。

- 可以尝试只运行所要依赖的服务，但这意味着，需要知道依赖关系的完整闭环（例如，依赖项的依赖项）。 或者，有时无法轻松知道如何生成和运行依赖项，因为以前未开展过相关的工作。
- 某些开发人员倾向于模拟许多服务依赖项。 有时此方法可帮助解决问题，但管理这些虚拟模型很快又要投入相关的开发工作。 此外，此方法会导致开发环境看上去与生产环境很不相同，并且会引入微妙的 bug。
- 执行任何形式的端到端测试随之也会变得很困难。 集成测试只能在提交后以切实可行的方式进行，这意味着，在开发周期的后期才能发现问题。

![](../media/common/microservices-challenges.png)


## <a name="work-in-a-shared-development-environment"></a>在共享的开发环境中工作
借助 Azure Dev Spaces，可以在 Azure 中设置共享的开发环境。 每个开发人员只需专注于他（她）所负责的应用程序部分，并可以在已包含其方案所依赖的其他所有服务和云资源的环境中，以迭代方式开发预提交代码。 依赖项始终保持最新，开发人员就像是在生产环境中工作一样。

## <a name="work-in-your-own-space"></a>在自己的空间中工作
在开发服务的代码过程中，准备好签入代码之前，代码通常不会处于良好状态。 仍需以迭代方式对其进行调整和测试，并在解决方案中体验该代码。 Azure Dev Spaces 提供“空间”的概念，使你能够在隔离环境中工作，而不必担心打断其他团队成员的工作。

> [!Note]
> 在继续之前，请关闭这两个服务的所有 VS Code 窗口，然后在每个服务的根文件夹中运行 `azds up -d`。 （这是预览版的限制。）

让我们仔细看看服务当前在哪个位置运行。 运行 `azds list` 命令，将会看到如下所示的输出：

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     http://webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

“空间”列显示这两个服务在名为 `default` 的空间中运行。 打开公共 URL 并导航到 Web 应用的任何人将调用前面编写的代码路径，该代码路径从前到后运行这两个服务。 现在，假设你要继续开发 `mywebapi`。 如何能够做出代码更改并对其测试，同时又不干扰其他使用开发环境的开发人员？ 为此，请设置自己的空间。

## <a name="create-a-space"></a>创建空间
若要在除 `default` 以外的空间中运行自己的 `mywebapi` 版本，可使用以下命令创建自己的空间：

``` 
azds space create --name scott
```

在上述示例中，我已使用自己的名字来为新空间命名，使我的同事能够识别我所在的空间。但是，你可以指定任何名称，并灵活规定其含义，例如“sprint4”或“demo”。

运行 `azds space list` 命令查看开发环境中所有空间的列表。 当前选定空间的旁边显示了星号 (*)。 在本例中，创建名为“scott”的空间后已自动选中它。 随时可以使用 `azds space select` 命令选择另一个空间。
