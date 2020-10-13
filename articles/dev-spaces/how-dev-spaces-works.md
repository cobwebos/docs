---
title: Azure Dev Spaces 工作原理
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: 描述 power Azure Dev Spaces 的进程
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963697"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces 工作原理

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

开发 Kubernetes 应用程序可能非常困难。 需要 Docker 和 Kubernetes 配置文件。 你需要确定如何在本地测试应用程序并与其他依赖服务进行交互。 你可能需要同时处理多个服务和一组开发人员的开发和测试。

Azure Dev Spaces 提供多种方法来快速循环访问和调试 Kubernetes 应用程序并与团队进行协作。 本文介绍 Azure Dev Spaces 可以执行的操作及其工作原理。

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>快速循环访问和调试 Kubernetes 应用程序

Azure Dev Spaces 降低了在 AKS 群集的上下文中开发、测试和迭代 Kubernetes 应用程序的工作量。 这种减少的工作量使开发人员可以专注于应用程序的业务逻辑，而不是将其服务配置为在 Kubernetes 中运行。

### <a name="bridge-to-kubernetes"></a>Bridge to Kubernetes

通过桥接到 Kubernetes，可以将开发计算机连接到 Kubernetes 群集，这样就可以在开发计算机上运行和调试代码，就像它在群集上运行一样。 桥接 Kubernetes 通过在群集上运行一个可充当远程代理来重定向开发计算机和群集之间的流量的 pod，来重定向已连接的群集之间的流量。 此流量重定向允许开发计算机上的代码和群集中运行的服务进行通信，就像它们位于同一群集中一样。 有关将开发计算机连接到 Kubernetes 群集的详细信息，请参阅 [桥 To Kubernetes 的工作原理][how-it-works-bridge-to-kubernetes]。

### <a name="run-your-code-in-aks"></a>在 AKS 中运行你的代码

除了在开发计算机与 AKS 群集之间重定向流量外 Azure Dev Spaces，你还可以配置并快速在 AKS 中运行代码。 通过 Visual Studio、Visual Studio Code 或 Azure Dev Spaces CLI，Azure 开发人员空间会将代码上传到群集，然后生成并运行它。 Azure Dev spaces 还可以智能地同步代码更改，并重新启动服务，以反映所需的更改。 运行代码时，生成日志和 HTTP 跟踪会流式传输回客户端，以便你可以监视进度并诊断任何问题。 你还可以使用 Azure Dev Spaces 将 Visual Visual Studio Code Studio 中的调试器附加到 Java、Node.js 和 .NET Core 服务中。 有关详细信息，请参阅 [准备 Azure Dev Spaces 项目的工作][how-it-works-prep]原理、 [使用 Azure Dev Spaces 运行代码的工作][how-it-works-up]原理以及 [使用 Azure Dev Spaces 远程调试代码的][how-it-works-remote-debugging]方式。

## <a name="team-development"></a>团队开发

Azure Dev Spaces 有助于团队在同一 AKS 群集上高效地工作应用程序，而无需中断。

### <a name="intelligent-routing-between-dev-spaces"></a>开发共享空间之间的智能路由

使用 Azure Dev Spaces，团队可以共享运行云本机应用程序的单个 AKS 群集，并创建独立的开发人员空间，团队无需干扰其他开发人员空间即可进行开发、测试和调试。 应用程序的基线版本在根开发人员空间中运行。 然后，团队成员会根据用于开发、测试和调试应用程序更改的根空间创建独立的子开发人员空间。 通过开发人员空间中的路由功能，子开发人员空间可以在子开发人员空间和父开发人员空间中运行的服务之间路由请求。 使用此路由，开发人员可在从父空间重用依赖服务时运行其自己的服务版本。 每个子空间都有其自己的唯一 URL，供其他人进行共享和访问以进行协作。 有关路由在 Azure Dev Spaces 中的工作原理的详细信息，请参阅 [路由如何与 Azure Dev Spaces 配合工作][how-it-works-routing]。

### <a name="live-testing-an-open-pull-request"></a>实时测试开放式拉取请求

你还可以将 GitHub 操作与 Azure Dev Spaces 一起使用，以便在合并之前在群集中直接测试对你的应用程序所做的更改。 Azure Dev Spaces 可以将应用程序的审阅版本自动部署到群集，使作者和其他团队成员可以查看整个应用程序上下文中的更改。 使用 Azure Dev Spaces 的路由功能，还会将此查看版本的应用程序部署到群集中，而不会影响其他 Dev 空间。 所有这些功能都允许您满怀信心地批准和合并拉取请求。 若要查看 GitHub 操作和 Azure Dev Spaces 的示例，请参阅 [Azure Kubernetes 服务 & Github 操作][pr-flow]。

## <a name="next-steps"></a>后续步骤

若要开始将本地开发计算机连接到 AKS 群集，请参阅 [将开发计算机连接到 AKS 群集][connect]。


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development