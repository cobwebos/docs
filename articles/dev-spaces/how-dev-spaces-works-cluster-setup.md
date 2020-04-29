---
title: 为 Azure Dev Spaces 设置群集的方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述如何为 Azure Dev Spaces 设置 Azure Kubernetes 服务群集
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241720"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>为 Azure Dev Spaces 设置群集的方式

Azure Dev Spaces 提供多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务（AKS）群集上与团队进行协作。 一种方法是在 AKS 群集上启用 Azure Dev Spaces，以便可以[直接在群集上运行服务][how-it-works-up]，并使用[其他网络和路由功能][how-it-works-routing]。 本文介绍准备群集并启用 Azure Dev Spaces 会发生的情况。

## <a name="prepare-your-aks-cluster"></a>准备 AKS 群集

若要为 AKS 群集准备开发人员空间，请验证 AKS 群集是否在[Azure Dev Spaces 支持][supported-regions]的区域中，并运行 Kubernetes 1.10.3 或更高版本。 要从 Azure 门户启用群集上的 Azure Dev Spaces，请导航到群集，单击 " *Dev 空间*"，将 "*使用 dev 空间*" 更改为 *"是"*，然后单击 "*保存*"。 还可以通过运行`az aks use-dev-spaces`从 Azure CLI 启用 Azure Dev Spaces。

有关为 Dev 空间设置 AKS 群集的示例，请参阅[团队开发快速入门][quickstart-team]。

在 AKS 群集上启用 Azure Dev Spaces 时，它将为群集安装控制器。 控制器位于 AKS 群集外。 它驱动客户端工具和 AKS 群集之间的行为和通信。 启用后，可以使用客户端工具与控制器交互。

控制器执行以下操作：

* 管理开发空间创建和选择。
* 安装应用程序的 Helm 图表并创建 Kubernetes 对象。
* 生成应用程序的容器映像。
* 将应用程序部署到 AKS。
* 当源代码发生变化时，会执行增量生成和重启。
* 管理日志和 HTTP 跟踪。
* 将 stdout 和 stderr 转发到客户端工具。
* 为空间内的应用程序以及父和子空间配置路由。

控制器是群集外部的独立 Azure 资源，并对群集中的资源执行以下操作：

* 创建或指定要用作开发人员空间的 Kubernetes 命名空间。
* 删除名为*azds*的任何 Kubernetes 命名空间（如果存在），并创建一个新命名空间。
* 部署 Kubernetes webhook 配置。
* 部署 webhook 许可服务器。

它使用 AKS 群集用来对其他 Azure Dev Spaces 组件进行服务调用的服务主体。

![Azure Dev Spaces 准备群集](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure Dev Spaces，必须至少有一个 Dev 空间。 Azure Dev Spaces 将 AKS 群集中的 Kubernetes 命名空间用于开发共享空间。 当控制器正在安装时，它会提示您创建一个新的 Kubernetes 命名空间或选择现有的命名空间，以用作您的第一个开发空间。 默认情况下，控制器提供将现有*默认*Kubernetes 命名空间升级到第一个开发空间。

将命名空间指定为开发人员空间时，控制器会将*azds.io/space=true*标签添加到该命名空间，以将其标识为 dev 空间。 默认情况下，你创建或指定的初始 dev 空间在准备好群集后处于选中状态。 选择空间后，Azure Dev Spaces 使用它来创建新的工作负荷。

可以使用客户端工具创建新的 dev 空间，并删除现有的 dev 空间。 由于 Kubernetes 中的限制，不能删除*默认*的 dev 空间。 控制器还会删除名为*azds*的任何现有 Kubernetes 命名空间，以`azds`避免与客户端工具使用的命令冲突。

Kubernetes webhook 许可服务器用于在部署期间插入包含三个容器的 pod，用于检测： devspaces 容器、devspaces-代理初始化容器和 devspaces 构建容器。 **所有这三个容器均使用 AKS 群集上的根访问权限运行。** 它们还使用 AKS 群集用来对其他 Azure Dev Spaces 组件进行服务调用的相同服务主体。

![Azure Dev Spaces Kubernetes webhook 许可服务器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces 容器是一种挎斗容器，用于处理进出应用程序容器的所有 TCP 流量，并帮助执行路由。 如果使用了某些空格，devspaces 容器将重置 HTTP 消息。 例如，它可以帮助在父和子空间的应用程序之间路由 HTTP 消息。 所有非 HTTP 流量均未修改 devspaces。 Devspaces 容器还会记录所有入站和出站 HTTP 消息，并将它们作为跟踪发送到客户端工具。 开发人员可以查看这些跟踪来检查应用程序的行为。

Devspaces 容器是一个[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它根据空间层次结构将其他路由规则添加到应用程序的容器。 在启动应用程序容器的 */etc/resolv.conf*文件和 iptables 配置之前，它会添加路由规则。 */Etc/resolv.conf*的更新允许对父空间中的服务进行 DNS 解析。 Iptables 配置更新确保进出应用程序容器的所有 TCP 流量通过 devspaces 代理路由。 除了 Kubernetes 添加的规则外，devspaces 中的所有更新都会发生。

Devspaces 容器为 init 容器，并装载了项目源代码和 Docker 套接字。 项目源代码和对 Docker 的访问权限允许直接通过 pod 生成应用程序容器。

> [!NOTE]
> Azure Dev Spaces 使用同一节点来生成应用程序的容器并运行它。 因此，Azure Dev Spaces 不需要使用外部容器注册表来构建和运行应用程序。

Kubernetes webhook 许可服务器侦听在 AKS 群集中创建的任何新的 pod。 如果该 pod 部署到带有*azds.io/space=true*标签的任何命名空间，则会将该 pod 与附加容器一起注入。 仅当应用程序的容器使用客户端工具运行时，才注入 devspaces 容器。

准备好 AKS 群集后，可以使用客户端工具在开发人员空间中准备并运行代码。

## <a name="client-side-tooling"></a>客户端工具

客户端工具使用户能够：
* 生成应用程序的 Dockerfile、Helm 图和 Azure Dev Spaces 配置文件。
* 创建父和子 dev 空间。
* 告诉控制器生成并启动应用程序。

当应用程序运行时，客户端工具也会：
* 从 AKS 中运行的应用程序接收并显示 stdout 和 stderr。
* 使用[端口转发](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)允许使用 http 访问应用程序 web：\//localhost。
* 在 AKS 中将调试器附加到正在运行的应用程序。
* 当检测到增量生成发生更改时，将源代码同步到您的开发空间，从而实现快速迭代。
* 允许你将开发人员计算机直接连接到 AKS 群集。

您可以在命令行中使用客户端工具作为`azds`命令的一部分。 你还可以将客户端工具用于：

* 使用[Azure Dev Spaces 扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)Visual Studio Code。
* 带有[Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools)的 Visual Studio。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用客户端工具在开发环境中准备和运行代码，请参阅[准备 Azure Dev Spaces 项目的工作原理][how-it-works-prep]。

若要开始使用 Azure Dev Spaces 进行团队开发，请参阅[Azure Dev Spaces 快速入门中的团队开发][quickstart-team]。

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md