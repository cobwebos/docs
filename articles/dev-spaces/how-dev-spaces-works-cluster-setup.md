---
title: 如何为 Azure Dev Spaces 设置群集
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述如何为 Azure Dev Spaces 设置 Azure Kubernetes 服务群集
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972673"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>如何为 Azure Dev Spaces 设置群集

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 一种方法是在 AKS 群集上启用 Azure Dev Spaces，以便可以[直接在群集上运行服务][how-it-works-up]并使用[其他网络和路由功能][how-it-works-routing]。 本文介绍了准备群集并启用 Azure Dev Spaces 时将发生的情况。

## <a name="prepare-your-aks-cluster"></a>准备 AKS 群集

若要为 Dev Spaces 准备 AKS 群集，请验证 AKS 群集位于 [Azure Dev Spaces 支持的][supported-regions]区域中，并且你正在运行 Kubernetes 1.10.3 或更高版本。 可以通过运行从 Azure CLI 启用 Azure Dev Spaces `az aks use-dev-spaces` 。

在 AKS 群集上启用 Azure Dev Spaces 时，它将为群集安装控制器。 控制器位于 AKS 群集外部， 并且驱动客户端工具与 AKS 群集之间的行为和通信。 启用控制器后，你便可以使用客户端工具与控制器进行交互。

控制器执行以下操作：

* 管理开发空间的创建和选择。
* 安装应用程序的 Helm 图表并创建 Kubernetes 对象。
* 生成应用程序的容器映像。
* 将应用程序部署到 AKS。
* 进行增量生成，并在源代码发生更改时重启。
* 管理日志和 HTTP 跟踪。
* 将 stdout 和 stderr 转发到客户端工具。
* 为一个空间内以及父空间和子空间之间的应用程序配置路由。

控制器是群集外部的独立 Azure 资源，对群集中的资源执行以下操作：

* 创建或指定要用作开发空间的 Kubernetes 命名空间。
* 删除任何名为“azds”的 Kubernetes 命名空间（如果存在），并创建一个新命名空间。
* 部署 Kubernetes Webhook 配置。
* 部署 Webhook 许可服务器。

它使用 AKS 群集用于对其他 Azure Dev Spaces 组件进行服务调用的相同服务主体。

![Azure Dev Spaces 准备群集](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure Dev Spaces，必须至少有一个开发空间。 Azure Dev Spaces 为开发空间使用 AKS 群集中的 Kubernetes 命名空间。 安装控制器时，它将提示你创建一个新的 Kubernetes 命名空间或选择一个现有命名空间用作第一个开发空间。 默认情况下，控制器提出将现有的默认 Kubernetes 命名空间升级为第一个开发空间。

将某个命名空间指定为开发空间时，控制器会将“azds.io/space=true”标签添加到该命名空间以将其标识为开发空间。 准备群集后，系统会默认选中创建或指定的初始开发空间。 选择空间后，Azure Dev Spaces 将使用该空间来创建新的工作负载。

可以使用客户端工具创建新的开发空间并删除现有的开发空间。 由于 Kubernetes 中的限制，无法删除默认开发空间。 控制器还会删除名为“azds”的任何现有 Kubernetes 命名空间，以避免与客户端工具使用的 `azds` 命令冲突。

Kubernetes Webhook 许可服务器用于在部署期间为 Pod 注入三个容器以进行检测：devspaces-proxy 容器、devspaces-proxy-init 容器和 devspaces-build 容器。 所有这三个容器都在 AKS 群集上通过根访问权限运行。 它们也使用 AKS 群集用于对其他 Azure Dev Spaces 组件进行服务调用的相同服务主体。

![Azure Dev Spaces Kubernetes Webhook 许可服务器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

devspaces-proxy 容器是一种 sidecar 容器，它处理进出应用程序容器的所有 TCP 流量，并帮助执行路由。 如果正在使用某些空间，则 devspaces-proxy 容器将重新路由 HTTP 消息。 例如，它可以帮助在父空间和子空间中的应用程序之间路由 HTTP 消息。 所有非 HTTP 流量都会通过 devspaces-proxy，而不会被修改。 devspaces-proxy 容器还会记录所有入站和出站 HTTP 消息，并将它们作为跟踪发送到客户端工具。 然后，开发者可以查看这些跟踪以检查应用程序的行为。

devspaces-proxy-init 容器是一种 [init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它根据空间层次结构向应用程序容器添加其他路由规则， 方法是在启动之前更新应用程序容器的 /etc/resolv.conf 文件和 iptables 配置。 对 /etc/resolv.conf 的更新允许对父空间中的服务进行 DNS 解析。 iptables 配置更新可确保所有进出应用程序容器的 TCP 流量都通过 devspaces-proxy 进行路由。 除了 Kubernetes 添加的规则之外，还会发生 devspaces-proxy-init 的所有更新。

devspaces-build 容器是一种 init 容器，它装载了项目源代码和 Docker 套接字。 项目源代码和对 Docker 的访问允许 Pod 直接生成应用程序容器。

> [!NOTE]
> Azure Dev Spaces 使用同一个节点来生成应用程序容器并运行该容器。 因此，Azure Dev Spaces 不需要外部容器注册表来生成和运行应用程序。

Kubernetes Webhook 许可服务器侦听在 AKS 群集中创建的任何新 Pod。 如果该 Pod 部署到具有 azds.io/space=true 标签的任何命名空间，它将向该 Pod 注入其他容器。 只有在使用客户端工具运行应用程序容器时，才会注入 devspaces-build 容器。

准备好 AKS 群集后，便可以使用客户端工具在开发空间中准备和运行代码。

## <a name="client-side-tooling"></a>客户端工具

客户端工具允许用户：
* 为应用程序生成 Dockerfile、Helm 图表和 Azure Dev Spaces 配置文件。
* 创建父级和子级开发空间。
* 告知控制器生成并启动应用程序。

当应用程序正在运行时，客户端工具还会：
* 从 AKS 中运行的应用程序接收 stdout 和 stderr，并显示它们。
* 使用 [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) 允许使用 http:\//localhost 对应用程序进行 web 访问。
* 在 AKS 中将调试程序附加到正在运行的应用程序。
* 在检测到增量生成发生更改时，将源代码同步到开发空间，从而允许快速迭代。
* 允许将开发者计算机直接连接到 AKS 群集。

可以使用命令行的客户端工具作为 `azds` 命令的一部分。 还可以将客户端工具用于：

* 使用 [Azure Dev Spaces 扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)的 Visual Studio Code。
* 包含“Azure 开发”工作负载的 Visual Studio。

## <a name="next-steps"></a>后续步骤

若要了解有关使用客户端工具在开发空间中准备和运行代码的更多信息，请参阅[如何为 Azure Dev Spaces 准备项目][how-it-works-prep]。


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service