---
title: Azure Dev Spaces 工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述为 Azure 开发空间供电的过程
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234960"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces 工作原理

开发 Kubernetes 应用程序可能具有挑战性。 您需要 Docker 和库伯奈斯配置文件。 您需要了解如何在本地测试应用程序并与其他从属服务进行交互。 您可能需要与开发人员团队一起同时处理开发和测试多个服务。

Azure 开发人员空间为您提供了多种方法来快速迭代和调试 Kubernetes 应用程序并与您的团队协作。 本文介绍了 Azure 开发空间可以做什么以及其工作原理。

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>快速迭代和调试库伯奈斯应用程序

Azure 开发人员空间减少了在 AKS 群集上下文中开发、测试和迭代 Kubernetes 应用程序的工作。 这种工作量的减少使开发人员能够专注于其应用程序的业务逻辑，而不是将其服务配置为在 Kubernetes 中运行。

### <a name="connect-your-development-machine-to-aks"></a>将开发计算机连接到 AKS

使用 Azure 开发人员空间，可以将开发计算机连接到 AKS 群集，从而可以在开发计算机上运行和调试代码，就像它在群集上运行一样。 Azure 开发人员空间通过在群集上运行充当远程代理的 Pod 来重定向连接的 AKS 群集之间的流量，从而在开发计算机和群集之间重定向流量。 此流量重定向允许开发计算机上的代码和在 AKS 群集中运行的服务进行通信，就像它们位于同一 AKS 群集中一样。 有关将开发计算机连接到 AKS 的详细信息，请参阅[将开发计算机连接到 AKS 群集的工作原理][how-it-works-connect]。

### <a name="run-your-code-in-aks"></a>在 AKS 中运行代码

除了在开发计算机和 AKS 群集之间重定向流量外，借助 Azure 开发人员空间，您还可以直接在 AKS 中配置和快速运行代码。 借助可视化工作室、可视化工作室代码或 Azure 开发空间 CLI，Azure 开发人员空间将上载代码到群集，然后生成并运行它。 Azure 开发人员空间还可以智能地同步代码更改并重新启动服务，以便根据需要反映更改。 运行代码时，生成日志和 HTTP 跟踪将流式传输回客户端，以便您可以监视进度并诊断任何问题。 您还可以使用 Azure 开发人员空间，将可视化工作室和可视化工作室代码中的调试器附加到 Java、Node.js 和 .NET 核心服务。 有关详细信息，请参阅为[Azure 开发人员空间准备项目的工作原理][how-it-works-prep]、使用[Azure 开发人员空间运行代码的工作原理][how-it-works-up]，以及使用[Azure 开发人员空间远程调试代码的工作原理][how-it-works-remote-debugging]。

## <a name="team-development"></a>团队开发

Azure 开发人员空间可帮助团队在同一 AKS 群集上高效地处理其应用程序，而不会造成中断。

### <a name="intelligent-routing-between-dev-spaces"></a>开发空间之间的智能路由

使用 Azure 开发人员空间，团队可以共享运行云原生应用程序的单个 AKS 群集，并创建独立的开发空间，团队可以在其中开发、测试和调试，而不会干扰其他开发空间。 应用程序的基准版本在根开发空间中运行。 然后，团队成员根据应用程序的根空间创建独立的子开发空间，以便进行开发、测试和调试更改。 通过 Dev Spaces 中的路由功能，子开发空间可以在子开发空间和父开发空间中运行的服务之间路由请求。 此路由允许开发人员在重用父空间中的从属服务时运行自己的服务版本。 每个子空间都有其自己独特的 URL，其他人可以共享和访问该 URL 进行协作。 有关路由在 Azure 开发空间中的工作方式的详细信息，请参阅[路由如何与 Azure 开发空间一起工作][how-it-works-routing]。

### <a name="live-testing-an-open-pull-request"></a>实时测试打开拉取请求

还可以将 GitHub 操作与 Azure 开发空间一起使用，在合并之前直接在群集中的拉取请求中测试对应用程序的更改。 Azure 开发人员空间可以自动将应用程序的审阅版本部署到群集，从而允许作者和其他团队成员查看整个应用程序上下文中的更改。 使用 Azure 开发人员空间的路由功能，应用程序的此审阅版本也会部署到群集，而不会影响其他开发空间。 所有这些功能都允许您自信地批准和合并拉取请求。 要查看 GitHub 操作和 Azure 开发空间的示例，请参阅[GitHub 操作& Azure 库伯奈斯服务][pr-flow]。

## <a name="next-steps"></a>后续步骤

要开始将本地开发计算机连接到 AKS 群集，请参阅[将开发计算机连接到 AKS 群集][connect]。

要开始使用 Azure 开发人员空间进行团队开发，请参阅[Azure 开发人员空间中的团队开发][quickstart-team]快速入门。

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development