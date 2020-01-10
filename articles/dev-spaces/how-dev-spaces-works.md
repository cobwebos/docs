---
title: Azure Dev Spaces 的工作方式和配置方式
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: 描述 power Azure Dev Spaces 的进程，以及如何在 azds. yaml 配置文件中配置这些进程
keywords: azds，yaml，Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771132"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Azure Dev Spaces 的工作方式和配置方式

开发 Kubernetes 应用程序可能非常困难。 需要 Docker 和 Kubernetes 配置文件。 你需要确定如何在本地测试应用程序并与其他依赖服务进行交互。 你可能需要同时处理多个服务的开发和测试以及一组开发人员。

Azure Dev Spaces 可帮助你直接在 Azure Kubernetes 服务（AKS）中开发、部署和调试 Kubernetes 应用程序。 Azure Dev Spaces 还允许团队共享开发人员空间。 跨团队共享开发人员空间允许各个团队成员进行隔离开发，而无需复制或模拟群集中的依赖项或其他应用程序。

Azure Dev Spaces 创建并使用配置文件在 AKS 中部署、运行和调试 Kubernetes 应用程序。 此配置文件与您的应用程序的代码驻留在一起，可以添加到您的版本控制系统中。

本文介绍 power Azure Dev Spaces 的进程，以及如何在 Azure Dev Spaces 配置文件中配置这些进程。 若要快速运行 Azure Dev Spaces 并在实践中进行查看，请完成以下快速入门之一：

* [带有 CLI 和 Visual Studio Code 的 Java](quickstart-java.md)
* [带有 CLI 和 Visual Studio Code 的 .NET Core](quickstart-netcore.md)
* [带有 Visual Studio 的 .NET Core](quickstart-netcore-visualstudio.md)
* [带有 CLI 和 Visual Studio Code 的 node.js](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces 工作原理

Azure Dev Spaces 具有两个不同的组件：控制器和客户端工具。

![Azure Dev Spaces 组件](media/how-dev-spaces-works/components.svg)

控制器执行以下操作：

* 管理开发空间创建和选择。
* 安装应用程序的 Helm 图表并创建 Kubernetes 对象。
* 生成应用程序的容器映像。
* 将应用程序部署到 AKS。
* 当源代码发生变化时，会执行增量生成和重启。
* 管理日志和 HTTP 跟踪。
* 将 stdout 和 stderr 转发到客户端工具。
* 允许团队成员创建派生自父开发人员空间的子 dev 空间。
* 为空间内的应用程序以及父和子空间配置路由。

控制器位于 AKS 外部。 它驱动客户端工具和 AKS 群集之间的行为和通信。 在准备群集以使用 Azure Dev Spaces 时，将使用 Azure CLI 启用控制器。 启用后，可以使用客户端工具与它交互。

客户端工具使用户能够：
* 生成应用程序的 Dockerfile、Helm 图和 Azure Dev Spaces 配置文件。
* 创建父和子 dev 空间。
* 告诉控制器生成并启动应用程序。

当应用程序运行时，客户端工具也会：
* 从 AKS 中运行的应用程序接收并显示 stdout 和 stderr。
* 使用[端口转发](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)允许使用 http 访问应用程序（\//localhost.）
* 在 AKS 中将调试器附加到正在运行的应用程序。
* 当检测到增量生成发生更改时，将源代码同步到您的开发空间，从而实现快速迭代。

您可以使用命令行中的客户端工具作为 `azds` 命令的一部分。 你还可以将客户端工具用于：

* 使用[Azure Dev Spaces 扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)Visual Studio Code。
* 带有[Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools)的 Visual Studio。

下面是设置和使用 Azure Dev Spaces 的基本流程：
1. 为 Azure Dev Spaces 准备 AKS 群集
1. 准备要在 Azure Dev Spaces 上运行的代码
1. 在开发人员空间上运行代码
1. 在开发人员空间上调试代码
1. 共享开发人员空间

我们将在以下各节中详细介绍 Azure Dev Spaces 的工作方式。

## <a name="prepare-your-aks-cluster"></a>准备 AKS 群集

准备 AKS 群集涉及：
* 验证 AKS 群集位于[Azure Dev Spaces 支持][supported-regions]的区域中。
* 正在验证是否正在运行 Kubernetes 1.10.3 或更高版本。
* 使用 `az aks use-dev-spaces` 启用群集上的 Azure Dev Spaces

有关如何为 Azure Dev Spaces 创建和配置 AKS 群集的详细信息，请参阅入门指南之一：
* [通过 Java 开始 Azure Dev Spaces](get-started-java.md)
* [通过 .NET Core 和 Visual Studio Azure Dev Spaces 入门](get-started-netcore-visualstudio.md)
* [通过 .NET Core 开始 Azure Dev Spaces](get-started-netcore.md)
* [通过 node.js 开始 Azure Dev Spaces](get-started-nodejs.md)

在 AKS 群集上启用 Azure Dev Spaces 时，它将为群集安装控制器。 控制器是群集外部的独立 Azure 资源，并对群集中的资源执行以下操作：

* 创建或指定要用作开发人员空间的 Kubernetes 命名空间。
* 删除名为*azds*的任何 Kubernetes 命名空间（如果存在），并创建一个新命名空间。
* 部署 Kubernetes webhook 配置。
* 部署 webhook 许可服务器。
    

它还使用您的 AKS 群集用来对其他 Azure Dev Spaces 组件进行服务调用的相同服务主体。

![Azure Dev Spaces 准备群集](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure Dev Spaces，必须至少有一个 Dev 空间。 Azure Dev Spaces 将 AKS 群集中的 Kubernetes 命名空间用于开发共享空间。 当控制器正在安装时，它会提示您创建一个新的 Kubernetes 命名空间或选择现有的命名空间，以用作您的第一个开发空间。 将命名空间指定为开发人员空间时，控制器会将*azds.io/space=true*标签添加到该命名空间，以将其标识为 dev 空间。 默认情况下，你创建或指定的初始 dev 空间在准备好群集后处于选中状态。 选择空间后，Azure Dev Spaces 使用它来创建新的工作负荷。

默认情况下，控制器通过升级现有的*默认*Kubernetes 命名空间来创建一个名为*default*的 dev 空间。 可以使用客户端工具创建新的 dev 空间，并删除现有的 dev 空间。 由于 Kubernetes 中的限制，不能删除*默认*的 dev 空间。 控制器还会删除名为*azds*的任何现有 Kubernetes 命名空间，以避免与客户端工具使用的 `azds` 命令发生冲突。

Kubernetes webhook 许可服务器用于在部署期间插入包含三个容器的 pod，用于检测： devspaces 容器、devspaces-代理初始化容器和 devspaces 构建容器。 **所有这三个容器均使用 AKS 群集上的根访问权限运行。** 它们还使用 AKS 群集用来对其他 Azure Dev Spaces 组件进行服务调用的相同服务主体。

![Azure Dev Spaces Kubernetes webhook 许可服务器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces 容器是一种挎斗容器，用于处理进出应用程序容器的所有 TCP 流量，并帮助执行路由。 如果使用了某些空格，devspaces 容器将重置 HTTP 消息。 例如，它可以帮助在父和子空间的应用程序之间路由 HTTP 消息。 所有非 HTTP 流量均未修改 devspaces。 Devspaces 容器还会记录所有入站和出站 HTTP 消息，并将它们作为跟踪发送到客户端工具。 开发人员可以查看这些跟踪来检查应用程序的行为。

Devspaces 容器是一个[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它根据空间层次结构将其他路由规则添加到应用程序的容器。 在启动应用程序容器的 */etc/resolv.conf*文件和 iptables 配置之前，它会添加路由规则。 */Etc/resolv.conf*的更新允许对父空间中的服务进行 DNS 解析。 Iptables 配置更新确保进出应用程序容器的所有 TCP 流量通过 devspaces 代理路由。 除了 Kubernetes 添加的规则外，devspaces 中的所有更新都会发生。

Devspaces 容器为 init 容器，并装载了项目源代码和 Docker 套接字。 项目源代码和对 Docker 的访问权限允许直接通过 pod 生成应用程序容器。

> [!NOTE]
> Azure Dev Spaces 使用同一节点来生成应用程序的容器并运行它。 因此，Azure Dev Spaces 不需要使用外部容器注册表来构建和运行应用程序。

Kubernetes webhook 许可服务器侦听在 AKS 群集中创建的任何新的 pod。 如果该 pod 部署到带有*azds.io/space=true*标签的任何命名空间，则会将该 pod 与附加容器一起注入。 仅当应用程序的容器使用客户端工具运行时，才注入 devspaces 容器。

准备好 AKS 群集后，可以使用客户端工具在开发人员空间中准备并运行代码。

## <a name="prepare-your-code"></a>准备你的代码

若要在开发环境中运行应用程序，需要将其放在容器中，并需要定义应该如何将其部署到 Kubernetes。 若要容器化你的应用程序，你需要一个 Dockerfile。 若要定义将应用程序部署到 Kubernetes 的方式，需要一个[Helm 图](https://docs.helm.sh/)。 为帮助创建应用程序的 Dockerfile 和 Helm 图表，客户端工具提供 `prep` 命令：

```cmd
azds prep --public
```

`prep` 命令将查看项目中的文件，并尝试创建用于在 Kubernetes 中运行应用程序的 Dockerfile 和 Helm 图表。 目前，`prep` 命令将生成具有以下语言的 Dockerfile 和 Helm 图表：

* Java
* Node.js
* .NET Core

*必须*从包含源代码的目录中运行 `prep` 命令。 从正确的目录中运行 `prep` 命令，可让客户端工具识别语言，并创建适当的 Dockerfile 容器化应用程序。 还可以从包含用于 Java 项目的*pom .xml*文件的目录运行 `prep` 命令。

如果在不包含源代码的目录中运行 `prep` 命令，则客户端工具不会生成 Dockerfile。 它还会显示一条错误消息，指出：*由于语言不受支持，无法生成 Dockerfile*。 如果客户端工具无法识别项目类型，也会出现此错误。

运行 `prep` 命令时，可以选择指定 `--public` 标志。 此标志通知控制器为此服务创建可通过 internet 访问的终结点。 如果未指定此标志，则只能从群集内部或使用客户端工具创建的 localhost 隧道访问该服务。 可以在运行 `prep` 命令后，通过更新生成的 Helm 图表来启用或禁用此行为。

`prep` 命令将不会替换项目中任何现有的 Dockerfile 或 Helm 图表。 如果现有的 Dockerfile 或 Helm 图表与 `prep` 命令生成的文件采用相同的命名约定，则 `prep` 命令将跳过生成这些文件。 否则，`prep` 命令将生成自己的 Dockerfile 或 Helm 图表，并将其作为现有文件的边缘。

`prep` 命令还会在项目的根目录中生成 `azds.yaml` 文件。 Azure Dev Spaces 使用此文件生成、安装、配置和运行您的应用程序。 此配置文件列出 Dockerfile 和 Helm 图的位置，还在这些项目之上提供其他配置。

下面是使用[.Net Core 示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)创建的 azds 文件示例：

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`prep` 命令生成的 `azds.yaml` 文件应该适用于简单的单一项目开发方案。 如果特定项目的复杂性增加，可能需要在运行 `prep` 命令后更新此文件。 例如，您的项目可能需要根据您的开发或调试需求对您的生成或启动过程进行一些调整。 你的项目中还可能有多个应用程序，这需要多个生成过程或不同的生成内容。

## <a name="run-your-code"></a>运行代码

若要在 dev 空间中运行代码，请在 `azds.yaml` 文件所在的目录中发出 `up` 命令：

```cmd
azds up
```

`up` 命令将生成和运行项目所需的应用程序源文件和其他项目上传到 dev 空间。 其中，控制器位于开发环境中：

1. 创建用于部署应用程序的 Kubernetes 对象。
1. 为应用程序生成容器。
1. 将应用程序部署到 dev 空间。
1. 为应用程序终结点创建一个可公开访问的 DNS 名称（如果已配置）。
1. 使用*端口转发*，通过 http://localhost 提供对应用程序终结点的访问。
1. 将 stdout 和 stderr 转发到客户端工具。


### <a name="starting-a-service"></a>启动服务

当你在开发人员空间中启动服务时，客户端工具和控制器将与同步你的源文件、创建你的容器和 Kubernetes 对象并运行你的应用程序协同工作。

在更精细的级别上，运行 `azds up`时，会发生以下情况：

1. 文件从用户计算机同步到用户的 AKS 群集独有的 Azure 文件存储。 将上传源代码、Helm 图和配置文件。 下一节提供了有关同步过程的更多详细信息。
1. 控制器创建用于启动新会话的请求。 此请求包含多个属性，包括唯一 ID、空间名称、源代码路径和调试标志。
1. 控制器将 Helm 图表中的 *$ （标记）* 占位符替换为唯一的会话 ID，并为服务安装 Helm 图表。 将对唯一会话 ID 的引用添加到 Helm 图表后，可将部署到 AKS 群集中的容器的容器绑定回会话请求和关联的信息。
1. 在安装 Helm 图表的过程中，Kubernetes webhook 许可服务器会向应用程序的 pod 添加其他容器，以便检测和访问项目的源代码。 添加 devspaces 和 devspaces init 容器以提供 HTTP 跟踪和空间路由。 添加了 devspaces 容器，以便为 pod 提供对 Docker 实例的访问和用于生成应用程序容器的项目源代码。
1. 启动应用程序的 pod 后，将使用 devspaces 容器和 devspaces 初始化容器来生成应用程序容器。 然后，将启动应用程序容器和 devspaces 容器。
1. 应用程序容器启动后，客户端功能将使用 Kubernetes*端口转发*功能，通过 http://localhost 提供对应用程序的 HTTP 访问。 此端口转发将开发计算机连接到您的开发环境中的服务。
1. 如果已启动 pod 中的所有容器，则服务正在运行。 此时，客户端功能开始流式传输 HTTP 跟踪、stdout 和 stderr。 此信息由开发人员的客户端功能显示。

### <a name="updating-a-running-service"></a>更新正在运行的服务

当服务正在运行时，如果有项目源文件发生更改，Azure Dev Spaces 可以更新该服务。 开发人员空间还根据更改的文件类型处理更新服务的方式。 开发人员可通过三种方式来更新正在运行的服务：

* 直接更新文件
* 重新生成并重新启动应用程序在运行的应用程序容器内的进程
* 重新生成和重新部署应用程序的容器

![Azure Dev Spaces 文件同步](media/how-dev-spaces-works/file-sync.svg)

某些作为静态资产的项目文件（如 html、css 和 cshtml 文件）可以直接在应用程序的容器中更新，而无需重新启动任何内容。 如果静态资产发生更改，则新文件将同步到 dev 空间，然后由正在运行的容器使用。

可以通过在正在运行的容器中重新启动应用程序的进程来应用对文件（如源代码或应用程序配置文件）的更改。 同步这些文件后，将使用*devhostagent*进程在运行中的容器内重新启动应用程序的进程。 最初创建应用程序的容器时，控制器会将该应用程序的启动命令替换为一个名为*devhostagent*的不同进程。 然后，应用程序的实际进程在*devhostagent*下以子进程的形式运行，并使用*devhostagent*的输出管道输出。 *Devhostagent*进程也是开发空间的一部分，可以代表 dev 空间在正在运行的容器中执行命令。 执行重新启动时， *devhostagent*：

* 停止当前进程或与应用程序关联的进程
* 重新生成应用程序
* 重新启动与应用程序关联的进程

在 `azds.yaml` 配置文件中配置*devhostagent*执行前面步骤的方式。 稍后部分详细介绍了此配置。

对项目文件（如 Dockerfile、.csproj 文件或 Helm 图表的任何部分）的更新需要重新生成和重新部署应用程序的容器。 当其中一个文件同步到开发人员空间时，控制器将运行[helm upgrade](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure)命令，然后重新生成并重新部署应用程序的容器。

### <a name="file-synchronization"></a>文件同步

第一次在开发环境中启动应用程序时，将上传应用程序的所有源文件。 当应用程序正在运行，并且在以后的重新启动时，只会上载已更改的文件。 使用两个文件来协调此过程：一个客户端文件和一个控制器端文件。

客户端文件存储在临时目录中，并基于在开发人员空间中运行的项目目录的哈希值进行命名。 例如，在 Windows 上，你的项目将有一个文件，如*Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* 。 在 Linux 上，客户端文件存储在 */tmp*目录中。 可以通过运行 `echo $TMPDIR` 命令来查找 macOS 上的目录。

此文件采用 JSON 格式，其中包含：

* 与开发人员空间同步的每个项目文件的条目
* 同步 ID
* 上次同步操作的时间戳

每个项目文件条目都包含该文件的路径及其时间戳。

控制器端文件存储在 AKS 群集上。 它包含同步 ID 和上次同步的时间戳。

如果客户端和控制器端文件之间的同步时间戳不匹配，则会发生同步。 在同步过程中，客户端工具将循环访问客户端文件中的文件项。 如果该文件的时间戳在同步时间戳之后，则该文件将同步到 dev 空间。 完成同步后，会在客户端和控制器端文件上更新同步时间戳。

如果未提供客户端文件，则将同步所有项目文件。 此行为使你可以通过删除客户端文件来强制执行完全同步。

### <a name="how-routing-works"></a>路由的工作原理

Dev 空间构建在 AKS 之上，并使用相同的[网络概念](../aks/concepts-network.md)。 Azure Dev Spaces 还具有一个集中式*ingressmanager*服务，并将其自己的入口控制器部署到 AKS 群集。 *Ingressmanager*服务会监视包含 dev 空间的 AKS 群集，并在群集中扩充 Azure Dev Spaces 入口控制器，其中包含用于路由到应用程序箱的入口对象。 每个 pod 中的 devspaces 容器将 HTTP 流量 `azds-route-as` 的 HTTP 标头添加到基于该 URL 的 dev 空间。 例如，对 URL 的请求 *http://azureuser.s.default.serviceA.fedcba09...azds.io* 会获得 `azds-route-as: azureuser` 的 HTTP 标头。 Devspaces 容器将不会添加 `azds-route-as` 标头（如果已存在）。

当从群集外部向服务发出 HTTP 请求时，请求将发送到入口控制器。 入口控制器根据请求的入口对象和规则将请求直接路由到相应的 pod。 Pod 中的 devspaces 容器接收请求，根据 URL 添加 `azds-route-as` 标头，然后将请求路由到应用程序容器。

当从群集中的其他服务向服务发出 HTTP 请求时，请求将首先通过调用服务的 devspaces 容器。 Devspaces 容器查看 HTTP 请求并检查 `azds-route-as` 标头。 根据标头，devspaces 容器将查找与标头值相关联的服务的 IP 地址。 如果找到了 IP 地址，devspaces 容器会将请求重排到该 IP 地址。 如果找不到 IP 地址，devspaces 容器会将请求路由到父应用程序容器。

例如，应用程序*serviceA*和*serviceB*部署到名为*default*的父开发人员空间。 *serviceA*依赖于*serviceB*并向其发出 HTTP 调用。 Azure 用户基于名为*azureuser*的*默认*空间创建子开发人员空间。 Azure 用户还会将其自己的*serviceA*版本部署到其子空间。 当请求进行 *http://azureuser.s.default.serviceA.fedcba09...azds.io* 时：

![Azure Dev Spaces 路由](media/how-dev-spaces-works/routing.svg)

1. 入口控制器查找与 URL 关联的 pod 的 IP，即*serviceA. azureuser*。
1. 入口控制器在 Azure 用户的开发区域中查找 pod 的 IP，并将请求路由到*serviceA. azureuser* pod。
1. *Azureuser* pod 中的 devspaces 容器接收请求，并将 `azds-route-as: azureuser` 添加为 HTTP 标头。
1. *Azureuser* pod 中的 devspaces 容器将请求路由到*serviceA* pod 中的*serviceA*应用程序容器。
1. *Azureuser* pod 中的*serviceA*应用程序调用*serviceB*。 *ServiceA*应用程序还包含用于保留现有 `azds-route-as` 标头的代码，在本例中为 `azds-route-as: azureuser`。
1. *Azureuser* pod 中的 devspaces 容器接收请求，并根据 `azds-route-as` 标头的值查找*serviceB*的 IP。
1. *Azureuser* pod 中的 devspaces 容器找不到*serviceB*的 IP。
1. *Azureuser* pod 中的 devspaces 容器在父空间中查找*serviceB*的 IP，即 " *serviceB*"。
1. *Azureuser* pod 中的 devspaces 容器查找*serviceB*的 IP，并将该请求路由到*serviceB。*
1. *ServiceB* pod 中的 devspaces 容器接收请求，并将请求路由到*serviceB* pod 中的*serviceB*应用程序容器。
1. *ServiceB* pod 中的*serviceB*应用程序将响应返回到*serviceA。*
1. *Azureuser* pod 中的 devspaces 容器接收响应，并将响应路由到*serviceA* pod 中的*serviceA*应用程序容器。
1. *ServiceA*应用程序接收响应，然后返回其自己的响应。
1. *Azureuser* pod 中的 devspaces 容器接收来自*serviceA*应用程序容器的响应，并将响应路由到群集外部的原始调用方。

非 HTTP 的所有其他 TCP 流量均通过入口控制器和 devspaces-proxy 容器未修改。

### <a name="how-running-your-code-is-configured"></a>如何配置运行代码

Azure Dev Spaces 使用 `azds.yaml` 文件来安装和配置服务。 控制器使用 `azds.yaml` 文件中的 `install` 属性安装 Helm 图表并创建 Kubernetes 对象：

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

默认情况下，`prep` 命令将生成 Helm 图表。 它*还将 "Helm" 属性设置*为 "" 图表的目录。 如果要在其他位置使用 Helm 图，则可以更新此属性以使用该位置。

安装 Helm 图表时，Azure Dev Spaces 提供了一种替代 Helm 图中的值的方法。 Helm 图的默认值为 `charts/APP_NAME/values.yaml`。

使用*install. values*属性，可以列出一个或多个文件，用于定义要在 Helm 图表中替换的值。 例如，如果在开发环境中运行应用程序时需要专门使用主机名或数据库配置，则可以使用此替代功能。 你还可以添加 *？* 在任何文件名的末尾，将其设置为可选。

*Install*属性允许您配置一个或多个要在 Helm 图表中替换的值。 在*install*中配置的任何值都将替代*install*中列出的文件中配置的值。 "*安装*" 下的属性取决于 Helm 图表中的值，可能因生成的 Helm 图表而有所不同。

在上面的示例中， *replicaCount*属性告诉控制器应用程序的多少实例要在你的开发环境中运行。 根据你的方案，你可以增大此值，但会影响将调试器附加到应用程序的 pod。 有关详细信息，请参阅[故障排除一文](troubleshooting.md)。

在生成的 Helm 图表中，容器映像设置为 *{{。值. repository}}： {{。值. tag}}* 。 默认情况下，`azds.yaml` 文件*将 "install. tag* " 属性定义为 *$ （标记）* *。值. tag}}* 。 通过以这种方式设置*该属性，* 它可以在运行 Azure Dev Spaces 时以不同的方式标记应用程序的容器映像。 在这种特定情况下，图像被标记为*图像\<值 >： $ （标记）* 。 您必须使用 *$ （标记）* 变量作为 Dev 空间的值，*以便在 AKS 群集中识别*并找到容器。

在上面的示例中，`azds.yaml` 定义了 "*安装*"。 " *Install* " 属性定义了公共终结点的主机名格式。 此属性还使用 *$ （spacePrefix）* 、 *$ （rootSpacePrefix）* 和 *$ （hostSuffix）* ，这是控制器提供的值。 

*$ （SpacePrefix）* 是子 dev 空间的名称，其格式为*SPACENAME*。 *$ （RootSpacePrefix）* 是父空间的名称。 例如，如果*azureuser*是*默认*的子空间， *$ （rootSpacePrefix）* 的值是*默认*值， *$ （spacePrefix）* 的值为*azureuser*。 如果空间不是子空间， *$ （spacePrefix）* 为空白。 例如，如果*默认*空间没有父空间，则 " *$ （rootSpacePrefix）* " 的值为 "*默认*值"，而 " *$ （spacePrefix）* " 的值为空。 *$ （HostSuffix）* 是指向在 AKS 群集中运行的 Azure Dev Spaces 入口控制器的 DNS 后缀。 此 DNS 后缀对应于通配符 DNS 条目，例如 *\*。* 在将 Azure Dev Spaces 控制器添加到 AKS 群集时创建的 eus. azds。 RANDOM_VALUE

在上述 `azds.yaml` 文件中，你还可以更新 "*安装*"，以更改应用程序的主机名。 例如，如果你想要将应用程序的主机名从 *$ （spacePrefix） $ （rootSpacePrefix） webfrontend $ （hostSuffix）* 简化为 $ （spacePrefix） *$ （rootSpacePrefix） Web $ （hostSuffix*）。

若要为应用程序生成容器，控制器使用 `azds.yaml` 配置文件的以下部分：

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

控制器使用 Dockerfile 生成并运行应用程序。

Dockerfile*属性列出*了存在的目录。 *Dockerfile*属性定义用于生成应用程序的生产版本的 dockerfile 的名称。 *Dockerfile*属性为应用程序的开发版本配置 dockerfile 的名称，。

使用不同的 Dockerfile 进行开发和生产，可以在开发过程中启用某些功能，并为生产部署禁用这些项。 例如，你可以在开发过程中启用调试或更详细日志记录，并在生产环境中禁用。 如果 Dockerfile 的命名方式不同或位于其他位置，也可以更新这些属性。

为了帮助你在开发过程中快速迭代，Azure Dev Spaces 将从本地项目同步更改，并以增量方式更新应用程序。 `azds.yaml` 配置文件中的以下部分用于配置同步和更新：

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

将同步更改的文件和目录列在 "*配置.* " 属性中。 当你运行 `up` 命令时以及检测到更改时，这些目录最初将进行同步。 如果要将其他或不同的目录同步到开发人员空间，则可以更改此属性。

*BuildCommands*属性指定了如何在开发方案中生成应用程序。 "*配置.* " 属性提供在开发方案中运行应用程序的命令。 如果要在开发期间使用其他生成或运行时标志或参数，则可能需要更新这些属性中的任何一个。

*ProcessesToKill*列出了终止应用程序时要终止的进程。 如果希望在开发过程中更改应用程序的重新启动行为，则可能需要更新此属性。 例如，如果你更新了*buildCommands 或*属性来更改应用程序的生成方式，你可能需要更改停止的进程，以更改应用程序的生成或启动*方式。*

使用 `azds prep` 命令准备你的代码时，你可以选择添加 `--public` 标志。 添加 `--public` 标志可为应用程序创建可公开访问的 URL。 如果省略此标志，则只能在群集内或使用 localhost 隧道访问应用程序。 运行 `azds prep` 命令后，可以更改此设置以修改 `charts/APPNAME/values.yaml`中的 "*已启用*" 属性：

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>调试代码

对于 Java、.NET 和 node.js 应用程序，可以使用 Visual Studio Code 或 Visual Studio 调试直接在开发环境中运行的应用程序。 Visual Studio Code 和 Visual Studio 提供了用于连接到开发人员空间、启动应用程序并附加调试器的工具。 运行 `azds prep`后，你可以在 Visual Studio Code 或 Visual Studio 中打开你的项目。 Visual Studio Code 或 Visual Studio 将生成自己的配置文件用于连接，而与运行 `azds prep`不同。 在 Visual Studio Code 或 Visual Studio 中，你可以设置断点并启动应用程序到你的开发环境。

![调试代码](media/get-started-node/debug-configuration-nodejs2.png)

使用 Visual Studio Code 或 Visual Studio 启动应用程序以进行调试时，它们会按照与运行 `azds up`相同的方式处理启动和连接到开发环境。 Visual Studio Code 和 Visual Studio 中的客户端工具还提供了一个附加参数，其中包含用于调试的特定信息。 参数包含调试器映像的名称、中调试器的位置、调试器的映像中的位置，以及用于装载调试器文件夹的应用程序容器中的目标位置。

调试器映像由客户端工具自动确定。 它使用的方法类似于在 Dockerfile 期间使用的方法，在运行 `azds prep`时生成的 Helm 图表。 在应用程序的映像中装入调试器后，将使用 `azds exec`运行该调试器。

## <a name="sharing-a-dev-space"></a>共享开发人员空间

与团队合作时，可以在[整个团队中共享一个开发人员空间](how-to/share-dev-spaces.md)，并创建派生的开发人员空间。 具有参与者访问该开发人员空间资源组的任何人都可以使用 dev 空间。

你还可以创建派生自另一个开发人员空间的新开发人员空间。 创建派生的开发人员空间时， *azds.io/parent-space=PARENT-SPACE-NAME*标签将添加到派生开发人员空间的命名空间中。 此外，父 dev 空间中的所有应用程序都与派生的开发人员空间共享。 如果将应用程序的更新版本部署到派生的开发人员空间，则该应用程序将仅存在于派生的开发人员空间中，并且父开发人员空间不受影响。 最多可以有三个级别的派生开发人员空间或*祖父*空间。

派生开发人员空间还将智能地在其自己的应用程序和从其父对象共享的应用程序之间路由请求。 路由的工作方式是：尝试将请求路由到派生的开发人员空间中的应用程序，并从父 dev 空间回退到共享应用程序。 如果应用程序不在父空间中，则路由将回退到祖父空间中的共享应用程序。

例如：
* Dev space*默认*具有应用程序*serviceA*和*serviceB* 。
* 开发人员空间*azureuser*派生自*默认值*。
* 将*serviceA*的更新版本部署到*azureuser*。

当使用*azureuser*时，对*serviceA*的所有请求都将路由到*azureuser*中的更新版本。 对*serviceB*的请求将首先尝试路由到*serviceB*的*azureuser*版本。 由于它不存在，它将路由到*serviceB*的*默认*版本。 如果删除*azureuser*版本的*serviceA* ，则对*serviceA*的所有请求都将回退到使用*默认*版本的*serviceA*。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Dev Spaces，请参阅以下快速入门：

* [带有 CLI 和 Visual Studio Code 的 Java](quickstart-java.md)
* [带有 CLI 和 Visual Studio Code 的 .NET Core](quickstart-netcore.md)
* [带有 Visual Studio 的 .NET Core](quickstart-netcore-visualstudio.md)
* [带有 CLI 和 Visual Studio Code 的 node.js](quickstart-nodejs.md)

若要开始团队开发，请参阅以下操作指南文章：

* [团队开发-带有 CLI 和 Visual Studio Code 的 Java](team-development-java.md)
* [团队开发-带有 CLI 和 Visual Studio Code 的 .NET Core](team-development-netcore.md)
* [团队开发-带有 Visual Studio 的 .NET Core](team-development-netcore-visualstudio.md)
* [团队开发-node.js 与 CLI 和 Visual Studio Code](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
