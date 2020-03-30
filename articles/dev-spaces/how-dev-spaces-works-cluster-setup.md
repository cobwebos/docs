---
title: 为 Azure 开发空间设置群集的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述为 Azure 开发人员空间设置 Azure 库伯奈斯服务群集的工作原理
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241720"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>为 Azure 开发空间设置群集的工作原理

Azure 开发人员空间为您提供了多种方法来快速迭代和调试 Kubernetes 应用程序，并与您的团队协作处理 Azure Kubernetes 服务 （AKS） 群集。 一种方法是在 AKS 群集上启用 Azure 开发空间，以便您可以[直接在群集上运行服务][how-it-works-up]，并使用[其他网络和路由功能][how-it-works-routing]。 本文介绍准备群集和启用 Azure 开发空间时会发生什么情况。

## <a name="prepare-your-aks-cluster"></a>准备 AKS 群集

要为开发空间准备 AKS 群集，请验证 AKS 群集位于[Azure 开发空间支持][supported-regions]的区域中，并且运行 Kubernetes 1.10.3 或更高版本。 要从 Azure 门户在群集上启用 Azure 开发空间，请导航到群集，请单击 *"开发空间*"，将 *"使用开发空间*更改为*是*"，然后单击"*保存*"。 还可以通过运行`az aks use-dev-spaces`从 Azure CLI 启用 Azure 开发空间。

有关为开发人员空间设置 AKS 群集的示例，请参阅[团队开发快速入门][quickstart-team]。

在 AKS 群集上启用 Azure 开发空间后，它将安装群集的控制器。 控制器位于 AKS 群集之外。 它驱动客户端工具与 AKS 群集之间的行为和通信。 启用后，您可以使用客户端工具与控制器进行交互。

控制器执行以下操作：

* 管理开发空间的创建和选择。
* 安装应用程序的 Helm 图表并创建库伯内斯对象。
* 生成应用程序的容器映像。
* 将应用程序部署到 AKS。
* 当源代码更改时，执行增量生成和重新启动。
* 管理日志和 HTTP 跟踪。
* 将稳贞和稳稳地转发到客户端工具。
* 为空间内的应用程序以及跨父空间和子空间配置路由。

控制器是群集外部的单独 Azure 资源，对群集中的资源执行以下操作：

* 创建或指定 Kubernetes 命名空间以用作开发空间。
* 删除任何名为*azds*的库伯内斯命名空间（如果存在）并创建新的命名空间。
* 部署库伯奈斯 Webhook 配置。
* 部署 Webhook 准入服务器。

它使用与 AKS 群集用于对其他 Azure 开发人员空间组件进行服务调用相同的服务主体。

![Azure 开发空间准备群集](media/how-dev-spaces-works/prepare-cluster.svg)

为了使用 Azure 开发空间，必须至少有一个开发空间。 Azure 开发空间使用 AKS 群集中的库伯内斯命名空间来开发空间。 安装控制器时，它会提示您创建新的 Kubernetes 命名空间或选择现有命名空间作为第一个开发空间使用。 默认情况下，控制器提供将现有*默认*Kubernetes 命名空间升级到第一个开发空间。

当命名空间被指定为开发空间时，控制器会向该命名空间添加*azds.io/space=true*标签，以将其标识为开发空间。 默认情况下，在准备群集后，将选择您创建或指定的初始开发空间。 选择空间后，Azure 开发人员空间会将其用于创建新工作负荷。

您可以使用客户端工具创建新的开发空间并删除现有的开发空间。 由于 Kubernets 中的限制，无法删除*默认*开发空间。 控制器还会删除任何名为*azds*的现有 Kubernetes 命名空间，以避免`azds`与客户端工具使用的命令发生冲突。

Kubernetes Webhook 准入服务器用于在部署检测期间向 Pod 注入三个容器：开发空间代理容器、开发空间代理-init 容器和开发空间生成容器。 **所有这些容器都使用 AKS 群集上的根访问运行。** 它们还使用 AKS 群集用于对其他 Azure 开发人员空间组件进行服务调用的相同服务主体。

![Azure 开发空间库伯内斯网访问服务器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

开发空间代理容器是一个侧车容器，用于处理进出应用程序容器的所有 TCP 流量，并帮助执行路由。 如果使用某些空格，则开发空间代理容器将重新路由 HTTP 消息。 例如，它可以帮助在父空间和子空间中的应用程序之间路由 HTTP 消息。 所有非 HTTP 流量都通过未修改的开发人员空间代理。 开发空间代理容器还记录所有入站和出站 HTTP 消息，并将它们作为跟踪发送到客户端工具。 然后，开发人员可以查看这些跟踪以检查应用程序的行为。

开发空间代理-init 容器是一个[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它根据空间层次结构向应用程序的容器添加其他路由规则。 它通过在应用程序容器的 */etc/resolv.conf*文件和 iptables 配置启动之前更新路由规则来添加路由规则。 对 */etc/resolv.conf*的更新允许在父空间中解析服务。 iptables 配置更新可确保通过开发空间代理路由进出应用程序容器的所有 TCP 流量。 除了 Kubernetes 添加的规则之外，来自开发空间-代理-它的所有更新都会发生。

开发空间生成容器是 init 容器，并装载了项目源代码和 Docker 套接字。 项目源代码和对 Docker 的访问允许由 pod 直接生成应用程序容器。

> [!NOTE]
> Azure 开发空间使用相同的节点来构建应用程序的容器并运行它。 因此，Azure 开发人员空间不需要外部容器注册表来构建和运行应用程序。

Kubernetes Webhook 进入服务器侦听在 AKS 群集中创建的任何新 pod。 如果该窗格部署到带有*azds.io/space=true*标签的任何命名空间，它将该窗格注入其他容器。 仅当使用客户端工具运行应用程序的容器时，才会注入开发空间生成容器。

准备好 AKS 群集后，可以使用客户端工具在开发空间中准备和运行代码。

## <a name="client-side-tooling"></a>客户端工具

客户端工具允许用户：
* 为应用程序生成 Dockerfile、Helm 图表和 Azure 开发人员空间配置文件。
* 创建父和子开发空间。
* 告诉控制器生成和启动应用程序。

应用程序运行时，客户端工具也：
* 接收并显示在 AKS 中运行的应用程序的稳贞和稳稳。
* 使用[端口转发](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)允许使用 http：\//localhost 对应用程序进行 Web 访问。
* 将调试器附加到 AKS 中正在运行的应用程序。
* 当检测到增量生成更改时，将源代码同步到开发空间，从而允许快速迭代。
* 允许您将开发人员计算机直接连接到 AKS 群集。

可以使用命令行中的客户端工具作为命令的一`azds`部分。 您还可以使用客户端工具：

* 使用[Azure 开发空间扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)的可视化工作室代码 。
* 视觉工作室与[视觉工作室工具为库伯内特](https://aka.ms/get-vsk8stools)斯。

## <a name="next-steps"></a>后续步骤

要了解有关使用客户端工具在开发空间中准备和运行代码的详细信息，请参阅[为 Azure 开发人员空间准备项目的工作原理][how-it-works-prep]。

要开始使用 Azure 开发人员空间进行团队开发，请参阅[Azure 开发人员空间中的团队开发][quickstart-team]快速入门。

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md