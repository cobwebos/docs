---
title: Azure 开发人员空间如何工作，是配置
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 03/04/2019
ms.topic: conceptual
description: 描述的过程，该 power Azure 开发人员空格和如何 azds.yaml 配置文件中配置
keywords: azds.yaml，Azure 开发人员空格、 开发空格、 Docker、 Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: 622a0780d74618fe694e5b9da0327490e0ec38dd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500553"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Azure 开发人员空间如何工作，是配置

开发 Kubernetes 应用程序可能具有挑战性。 您需要 Docker 和 Kubernetes 配置文件。 您需要找出如何进行本地测试应用程序和与其他从属服务进行交互。 您可能需要处理开发和测试多个服务在一次并使用一组开发人员。

Azure 开发人员空间可帮助你开发、 部署和调试 Kubernetes 应用程序在 Azure Kubernetes 服务 (AKS) 中的直接。 Azure 开发人员空间还允许团队共享开发空间。 跨团队共享开发空间允许各个团队成员，而无需复制或模拟依赖项或在群集中的其他应用程序开发中隔离。

Azure 开发人员空间创建并部署、 运行和调试 Kubernetes 应用程序在 AKS 中的为使用配置文件。 此配置文件位于应用程序的代码，可以添加到版本控制系统。

本文介绍了进程该 power Azure 开发空间以及如何在 Azure 开发人员空格配置文件中配置这些进程。 若要获取 Azure 开发人员空间快速运行并看到它在实践中，请完成快速入门教程之一：

* [使用 CLI 和 Visual Studio Code 的 Java](quickstart-java.md)
* [.NET core 和 CLI 和 Visual Studio Code](quickstart-netcore.md)
* [.NET core 和 Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [使用 CLI 和 Visual Studio Code 的 Node.js](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Azure 开发人员空间的工作原理

Azure 开发人员空间的两个与交互的不同组件： 在控制器和客户端工具。

![Azure 开发人员空间组件](media/how-dev-spaces-works/components.svg)

控制器可以执行以下操作：

* 管理适用于开发人员空间创建和选择。
* 安装应用程序的 Helm 图表，并创建 Kubernetes 对象。
* 生成应用程序的容器映像。
* 将部署到 AKS 应用程序。
* 执行增量生成并重新启动你的源代码更改时。
* 管理日志和 HTTP 跟踪。
* 将转发 stdout 和 stderr 到客户端工具。
* 允许团队成员创建子开发空间派生自父开发空间。
* 配置的应用程序内空格和跨父和子空间的路由。

在控制器驻留在 AKS 之外。 它的客户端工具与 AKS 群集之间驱动的行为和通信。 使用 Azure CLI，当你准备将群集使用 Azure 开发人员空间时，已启用控制器。 一旦启用，你可以与它使用客户端工具进行交互。

客户端工具允许用户：
* 生成 Dockerfile、 Helm chart 和应用程序的 Azure 开发人员空格配置文件。
* 创建父级和子级开发空间。
* 告诉控制器以生成并启动应用程序。

在你的应用程序运行时，客户端还工具：
* 接收并显示 stdout 和 stderr 的在 AKS 中运行的应用程序。
* 使用[端口转发](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)以允许对使用 http 的应用程序的 web 访问：\//localhost。
* 将调试器附加到在 AKS 中运行的应用程序。
* 同步源的代码与你的开发空间时为增量生成，实现快速迭代检测到更改。

可以使用客户端从命令行工具的一部分`azds`命令。 此外可以使用客户端工具：

* 使用 visual Studio Code [Azure 开发人员空间扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)。
* 带有 visual Studio 2017[适用于 Kubernetes 的 Visual Studio 工具](https://aka.ms/get-vsk8stools)。

下面是用于设置和使用 Azure 开发人员空间的基本流程：
1. 为 Azure 开发人员空间准备你的 AKS 群集
1. 准备您的代码用于在 Azure 开发人员空间上运行
1. 适用于开发人员空间上运行代码
1. 适用于开发人员空间上调试代码
1. 共享开发空间

我们将介绍 Azure 开发人员空间中的每个工作原理的更多详细信息下面各节。

## <a name="prepare-your-aks-cluster"></a>准备在 AKS 群集

准备在 AKS 群集的过程包括：
* 验证你的 AKS 群集位于区域[支持的 Azure 开发人员空格](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams)。
* 验证运行 Kubernetes 1.10.3 或更高版本。
* 使用在群集上启用 Azure 开发人员空格 `az aks use-dev-spaces`

有关如何创建和配置 Azure 开发人员空间 AKS 群集的详细信息，请参阅入门指南：
* [开始使用 Java 的 Azure 开发人员空格](get-started-java.md)
* [开始在 Azure 开发人员使用.NET Core 和 Visual Studio 的空格](get-started-netcore-visualstudio.md)
* [开始在 Azure 开发人员使用.NET Core 的空格](get-started-netcore.md)
* [开始在 Azure 开发人员与 Node.js 配合使用的空间](get-started-nodejs.md)

在 AKS 群集上启用 Azure 开发人员空格时，它将为群集控制器安装。 控制器是单独的 Azure 资源，在群集外部并执行以下操作在群集中的资源：

* 创建或指定要用作开发空间的 Kubernetes 命名空间。
* 删除名为任何 Kubernetes 命名空间*azds*，如果它存在，并且还创建一个新。
* 部署 Kubernetes 初始值设定项对象。

![Azure 开发人员空间准备群集](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure 开发人员空格，必须至少一个适用于开发人员空间。 Azure 开发人员空间为开发人员空间使用 AKS 群集中的 Kubernetes 命名空间。 安装控制器时，它会提示您创建新的 Kubernetes 命名空间或选择现有的命名空间用作第一个适用于开发人员空间。 命名空间指定为开发人员空间时，会添加控制器*azds.io/space=true*到该命名空间，以将其标识为开发人员空格的标签。 默认情况下，准备你的群集后，创建或指定的初始开发空间处于选中状态。 选择一个空格后，它可供 Azure 开发人员空间用于创建新的工作负荷。

默认情况下，控制器创建名为开发人员空格*默认*通过升级现有*默认*Kubernetes 命名空间。 客户端工具可用于创建新的开发空间和删除现有的开发空间。 由于在 Kubernetes 中，限制*默认*不能删除适用于开发人员的空间。 在控制器还会删除任何现有 Kubernetes 命名的命名空间*azds*以避免与冲突`azds`由客户端工具的命令。

Kubernetes 初始值设定项对象用于在用于检测部署过程中将插入三个容器的 pod: devspaces 代理容器、 devspaces 代理初始化容器和 devspaces 生成容器。 **所有这三个这些容器在 AKS 群集上运行具有根访问权限。**

![Azure 开发人员空格 Kubernetes 初始值设定项](media/how-dev-spaces-works/kubernetes-initializer.svg)

Devspaces 代理容器是处理所有 TCP 流量传入和传出的应用程序容器的挎斗容器，可帮助执行路由。 如果要使用某些空格，devspaces 代理容器重新确定 HTTP 消息。 例如，它可以帮助将父和子空间中的应用程序之间的 HTTP 消息路由。 所有非 HTTP 流量将通过 devspaces 代理未修改的形式传递。 Devspaces 代理容器还记录所有入站和出站 HTTP 消息，并将其发送到客户端作为跟踪工具。 这些跟踪可查看由开发人员若要检查的应用程序的行为。

Devspaces 代理初始化容器是[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它将额外的路由规则基于空间层次结构添加到应用程序的容器。 它通过更新应用程序容器添加路由规则 */etc/resolv.conf*文件和 iptables 配置开始之前。 的最新更新 */etc/resolv.conf*允许父空间中的服务的 DNS 解析。 Iptables 配置更新确保为所有 TCP 流量和出应用程序的容器将但路由 devspaces 代理。 除了 Kubernetes 添加的规则会从 devspaces 代理初始化的所有更新。

Devspaces 生成容器是一个 init 容器，并且具有项目源代码和装载的 Docker 套接字。 项目源代码和对 Docker 允许直接由 pod 生成应用程序容器。

> [!NOTE]
> Azure 开发人员空间使用同一个节点生成应用程序的容器并运行它。 因此，Azure 开发人员空间不需要用于构建和运行你的应用程序的外部容器注册表。

在 AKS 群集中创建任何新 pod 侦听 Kubernetes 初始值设定项对象。 如果该 pod 部署到任何命名空间*azds.io/space=true*标签，将插入该 pod 使用更多的容器。 如果使用客户端工具运行应用程序的容器，只被注入 devspaces 生成容器。

一旦你已准备好在 AKS 群集，可以使用客户端工具来准备和运行你的代码在你开发的空间。

## <a name="prepare-your-code"></a>准备您的代码

若要在开发人员空间中运行你的应用程序，它需要容器化，并需要定义如何应部署到 Kubernetes。 若要容器化应用程序，需要一个 Dockerfile。 若要定义如何将你的应用程序部署到 Kubernetes，需要[Helm 图表](https://docs.helm.sh/)。 若要帮助您创建的 Dockerfile 和 Helm 图表为应用程序，客户端工具提供`prep`命令：

```cmd
azds prep --public
```

`prep`命令将看看在项目中的文件，然后尝试创建 Dockerfile 和 Helm 图表在 Kubernetes 中运行你的应用程序。 目前，`prep`命令将生成具有以下语言版本的 Dockerfile 和 Helm 图表：

* Java
* Node.js
* .NET Core

您*必须*运行`prep`命令从包含源代码的目录。 运行`prep`从正确的目录的命令允许客户端工具，以便标识语言，并创建相应的 Dockerfile，可用于容器化应用程序。 您还可以运行`prep`命令包含的目录*pom.xml* Java 项目的文件。

如果您运行`prep`不包含源的代码，客户端工具的目录中的命令将不会生成 Dockerfile。 它还将显示错误消息，指出：*由于不支持的语言，无法生成 Dockerfile*。 如果客户端工具无法识别项目类型，也会发生此错误。

在运行时`prep`命令时，您可以指定`--public`标志。 此标记指示要创建此服务的可访问 internet 的终结点的控制器。 如果未指定此标志，该服务才可从群集内访问或使用 localhost 隧道的客户端工具创建。 可以启用或禁用此行为： 运行后`prep`命令更新生成的 Helm 图表。

`prep`命令将不替换您项目中有任何现有 Dockerfile 或 Helm 图表。 如果已有的 Dockerfile 或 Helm 图表作为生成的文件使用相同的命名约定`prep`命令，`prep`命令将跳过生成这些文件。 否则为`prep`命令将生成其自己的 Dockerfile 或 Helm 图表沿端现有的文件。

`prep`命令还将生成`azds.yaml`根目录下的项目文件。 Azure 开发人员空间使用此文件来构建、 安装、 配置和运行应用程序。 此配置文件列出了 Dockerfile 和 Helm 图表的位置，并可基于这些项目的其他配置。

下面是使用创建的示例 azds.yaml 文件[.NET Core 示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

`azds.yaml`生成的文件`prep`命令应该能够正常工作的简单、 单个项目开发方案。 如果你的特定项目增加了复杂性，可能需要运行后更新此文件`prep`命令。 例如，你的项目可能需要进行一些调整到生成或启动基于您的开发或调试需要的进程。 此外可能具有多个应用程序在项目中，需要多个生成进程或不同的生成内容。

## <a name="run-your-code"></a>运行代码

若要在开发人员空间中运行你的代码，发出`up`所在的同一目录中的命令在`azds.yaml`文件：

```cmd
azds up
```

`up`命令将上传应用程序源文件和其他生成和运行到开发项目所需的项目。 从此处，在你开发的空间中的控制器：

1. 创建要将应用程序部署的 Kubernetes 对象。
1. 生成你的应用程序的容器。
1. 将部署到开发应用程序。
1. 如果配置，请创建具有可公开访问的应用程序终结点的 DNS 名称。
1. 使用*端口转发*来提供对应用程序终结点使用访问 http://locahost。
1. 将转发 stdout 和 stderr 到客户端工具。


### <a name="starting-a-service"></a>启动服务

当开发人员空间中启动服务时，客户端工具和控制器将适用于协调同步源代码文件、 容器和 Kubernetes 对象创建和运行应用程序。

在更精细的级别，下面是在运行时，会发生什么情况`azds up`:

1. 文件将从用户的计算机同步到仅适用于用户的 AKS 群集将 Azure 文件存储。 上载源代码、 Helm 图表和配置文件。 下一节中提供了在同步过程的更多详细信息。
1. 在控制器创建一个请求以启动新会话。 此请求包含多个属性，包括唯一 ID、 空间名称、 源代码、 路径和调试标志。
1. 控制器替换 *$(tag)* Helm 图表使用唯一的会话 ID 和安装 Helm 图表为您的服务中的占位符。 添加到 Helm 图表的唯一会话 id 的引用允许容器部署到 AKS 群集，此特定会话中进行关联回到会话请求和相关信息。
1. 安装过程中的 Helm 图表，Kubernetes 初始值设定项对象将更多的容器添加到应用程序的 pod 规范和访问你的项目的源代码。 添加 devspaces 代理和 devspaces 代理初始化容器来提供 HTTP 跟踪和空间路由。 添加 devspaces 生成容器，以提供用于构建应用程序的容器的 Docker 实例和项目源代码的访问权限的 pod。
1. 启动应用程序的 pod，devspaces 生成容器和 devspaces 代理初始化容器用于生成应用程序容器。 然后启动应用程序容器和 devspaces 代理容器。
1. 客户端功能的应用程序容器启动后，使用 Kubernetes*端口转发*功能通过提供对你的应用程序的 HTTP 访问 http://localhost。 此端口转发将在开发计算机连接到你的开发空间中的服务。
1. 当在 pod 中的所有容器都启动后时，服务正在运行。 此时，客户端功能开始流式传输的 HTTP 跟踪、 stdout 和 stderr。 开发人员的情况下，此信息显示的客户端的功能。

### <a name="updating-a-running-service"></a>更新正在运行的服务

在运行时服务，Azure 开发人员空间的功能来更新该服务，如果项目源文件的任何更改。 此外可以开发空格处理更新服务以不同的方式具体取决于更改的文件的类型。 有三种方法适用于开发人员空间可以更新正在运行的服务：

* 直接更新文件
* 重新生成并重新启动正在运行的应用程序的容器内的应用程序的进程
* 重建和重新部署应用程序的容器

![Azure 开发人员空格文件同步](media/how-dev-spaces-works/file-sync.svg)

无需重新启动的任何内容，可以直接在应用程序的容器中更新是静态资产，例如 html、 css 和 cshtml 文件，某些项目文件。 如果静态资产发生更改，新的文件是同步到开发人员空间，然后由正在运行的容器。

可以通过重新启动应用程序的进程正在运行的容器内应用对如源代码文件或应用程序配置文件更改。 在正在运行的容器使用后，这些文件进行同步，重新启动应用程序的进程*devhostagent*过程。 最初创建应用程序的容器时，控制器替换为应用程序的 startup 命令名为的不同过程*devhostagent*。 应用程序的实际进程然后作为子进程下运行*devhostagent*，并使用其输出通过管道传送*devhostagent*的输出。 *Devhostagent*过程也是开发人员空间的一部分，并且可以执行在正在运行的容器中的命令，代表开发人员的空格。 执行重启时*devhostagent*:

* 停止当前进程或与应用程序关联的进程
* 重新生成应用程序
* 重新启动该应用程序与关联的进程

方式*devhostagent*执行上述步骤中配置`azds.yaml`配置文件。 此配置是在后面的部分详细说明。

项目文件，例如 Dockerfile、 csproj 文件或 Helm 图表的任何部分更新需要重新生成并重新部署的应用程序的容器。 其中一个文件同步到开发人员空间时，控制器将运行[运行 helm upgrade](https://helm.sh/docs/helm/#helm-upgrade)重建和重新部署命令和应用程序的容器。

### <a name="file-synchronization"></a>文件同步

第一次在开发人员空间中，启动的应用程序上传应用程序的所有源代码文件。 在运行时应用程序并在更高版本的重新启动，上载已更改的文件。 使用两个文件来协调此过程： 客户端文件和一个控制器端文件。

客户端的文件存储在临时目录中和名为基于哈希在开发人员空间中运行的项目目录。 例如，在 Windows 上将具有类似的文件*Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog*为你的项目。 在 Linux 上，客户端的文件存储在 */tmp*目录。 可以通过运行在 macOS 上找到目录`echo $TMPDIR`命令。

此文件采用 JSON 格式且包含：

* 与开发人员空间同步每个项目文件条目
* 同步 ID
* 最后一次同步操作的时间戳

每个项目文件条目包含该文件，其时间戳的路径。

控制器端文件存储在 AKS 群集上。 它包含同步 ID 和上次同步的时间戳。

客户端和控制器端文件之间的同步时间戳不匹配时，会发生同步。 在同步时，客户端工具循环访问客户端的文件中的文件条目。 如果文件的时间戳晚于同步时间戳，该文件被同步到开发人员空间。 在同步完成后，同步时间戳会更新对客户端和控制器端文件。

如果客户端的文件不存在，同步的所有项目文件。 此行为，可通过删除客户端文件强制执行完全同步。

### <a name="how-routing-works"></a>路由的工作原理

适用于开发人员空间基于 AKS，并使用相同[的网络概念](../aks/concepts-network.md)。 Azure 开发人员空格也有集中式*ingressmanager*服务并将其自身入口控制器部署到 AKS 群集。 *Ingressmanager*服务的监视 AKS 群集使用适用于开发人员空间并增加 Azure 开发人员空格入口控制器与入口路由到应用程序 pod 的对象在群集中的内容。 每个 pod 中的 devspaces 代理容器添加`azds-route-as`HTTP 标头的 HTTP 流量传输到开发人员空间根据 URL。 例如，对 URL 的请求*http://azureuser.s.default.serviceA.fedcba09...azds.io*将获取 HTTP 标头与`azds-route-as: azureuser`。 将不会添加 devspaces 代理容器`azds-route-as`标头，如果已存在。

当 HTTP 请求从群集外部的服务请求将发送到入口控制器。 入口控制器请求将直接路由到相应的 pod 根据其入口对象和规则。 Pod 中的 devspaces 代理容器收到请求时，将添加`azds-route-as`标头基于 URL，并随后将请求路由到应用程序容器。

当对服务进行的 HTTP 请求时从群集内的另一个服务时，请求首先经历 devspaces 代理调用服务的容器。 Devspaces 代理容器会查看 HTTP 请求和检查`azds-route-as`标头。 基于标头，devspaces 代理容器将查找标头值与关联的服务的 IP 地址。 如果找到的 IP 地址，则 devspaces 代理容器重新确定对该 IP 地址的请求。 如果找不到 IP 地址，devspaces 代理容器会将请求路由到父应用程序容器。

例如，应用程序*serviceA*并*serviceB*已部署到名为父开发空间*默认*。 *serviceA*依赖*serviceB*和 HTTP 对其进行调用。 Azure 用户创建基于一个子开发空间*默认*空间称为*azureuser*。 Azure 用户还可部署他们自己的版本*serviceA*到其子空间。 当请求*http://azureuser.s.default.serviceA.fedcba09...azds.io*:

![路由的 azure 开发人员空格](media/how-dev-spaces-works/routing.svg)

1. 入口控制器会查找 pod URL，即与关联的 IP *serviceA.azureuser*。
1. 入口控制器查找 Azure 用户的开发空间中的 pod IP，并将路由到请求*serviceA.azureuser* pod。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 接收请求并将添加`azds-route-as: azureuser`作为 HTTP 标头。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 将路由到请求*serviceA*中的应用程序容器*serviceA.azureuser* pod。
1. *ServiceA*应用程序中*serviceA.azureuser* pod 会调用*serviceB*。 *ServiceA*应用程序还包含代码，以保留现有`azds-route-as`标头，这种情况下是`azds-route-as: azureuser`。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 接收请求，并查找的 IP *serviceB*值的基础`azds-route-as`标头。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 未找到的 IP *serviceB.azureuser*。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 查找 IP *serviceB*中的父区域中，即*serviceB.default*。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 找到的 IP *serviceB.default* ，并将路由到请求*serviceB.default* pod。
1. 中的 devspaces 代理容器*serviceB.default* pod 接收请求，并将路由到请求*serviceB*中的应用程序容器*serviceB.default*pod。
1. *ServiceB*应用程序中*serviceB.default* pod 将响应返回给*serviceA.azureuser* pod。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 接收响应，并将路由到响应*serviceA*中的应用程序容器*serviceA.azureuser* pod。
1. *ServiceA*应用程序接收的响应，然后返回其自己的响应。
1. 中的 devspaces 代理容器*serviceA.azureuser* pod 收到从响应*serviceA*应用程序容器，并将路由到群集外部的原始调用方的响应。

不是 HTTP 的其他所有 TCP 流量将通过入口控制器和 devspaces 代理容器未修改的形式都传递。

### <a name="how-running-your-code-is-configured"></a>如何配置运行你的代码

Azure 开发人员空间使用`azds.yaml`文件以安装和配置你的服务。 控制器使用`install`中的属性`azds.yaml`文件以安装 Helm 图表并创建 Kubernetes 对象：

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

默认情况下，`prep`命令将生成 Helm 图表。 它还设置*install.chart*到 Helm 图表的目录的属性。 如果你想要在其他位置使用 Helm 图表，则可以更新此属性，以使用该位置。

在安装 Helm 图表时，Azure 开发人员空间使您能够重写 Helm 图表中的值。 Helm 图表的默认值为`charts/APP_NAME/values.yaml`。

使用*install.values*属性，可以列出一个或多个文件定义要包含在 Helm 图表中被替换的值。 例如，如果您希望特别在运行你的应用程序开发人员空间中的主机名或数据库配置，您可以使用此重写功能。 您还可以添加 *？* 在任何文件名称，以便将其设置为可选的末尾。

*Install.set*属性可以配置一个或多个要在 Helm 图表中被替换的值。 配置中的任何值*install.set*将覆盖配置中列出的文件中配置的值*install.values*。 属性下的*install.set*依赖 Helm 图表中的值，可能会有所不同，具体取决于生成 Helm 图表。

在上述示例中， *install.set.replicaCount*属性会指示控制器在你开发的空间中运行应用程序的多少个实例。 具体取决于你的方案，可以增大此值，但它会影响将调试器附加到应用程序的 pod。 有关详细信息，请参阅[疑难解答文章](troubleshooting.md)。

生成 Helm 图表，在容器映像设置为 *{{。Values.image.repository}}:{{。Values.image.tag}}*。 `azds.yaml`文件定义*install.set.image.tag*属性设置为 *$(tag)* 默认情况下，它作为的值中使用 *{{。Values.image.tag}}*。 通过设置*install.set.image.tag*以这种方式的属性，它允许你的应用程序来运行 Azure 开发人员空格时以不同方式标记容器映像。 在此特定情况下，映像标记为 *<value from image.repository>: $(tag)*。 必须使用 *$(tag)* 变量的值作为*install.set.image.tag*为了使开发人员空间识别并找出在 AKS 群集中的容器。

在上述示例中，`azds.yaml`定义*install.set.ingress.hosts*。 *Install.set.ingress.hosts*属性定义的公共终结点的主机名格式。 此属性也使用 *$(spacePrefix)*， *$(rootSpacePrefix)*，并 *$(hostSuffix)*，这是由控制器提供的值。 

*$(SpacePrefix)* 是子开发空间，其形式的名称*SPACENAME.s*。 *$(RootSpacePrefix)* 是父空间的名称。 例如，如果*azureuser*的子空间*默认*，为值 *$(rootSpacePrefix)* 是*默认*值 *$(spacePrefix)* 是*azureuser.s*。 如果没有子空间的空间。 *$(spacePrefix)* 为空。 例如，如果*默认*空间具有没有父空间的值 *$(rootSpacePrefix)* 是*默认*的值和 *$(spacePrefix)* 为空。 *$(HostSuffix)* 是指向在 AKS 群集中运行的 Azure 开发人员空格入口控制器的 DNS 后缀。 此 DNS 后缀与通配符 DNS 条目，例如对应 *\*。RANDOM_VALUE.eus.azds.io*，Azure 开发人员空格控制器添加到你的 AKS 群集时创建。

在上面`azds.yaml`文件，也可以更新*install.set.ingress.hosts*若要更改你的应用程序的主机名。 例如，如果您希望简化应用程序的主机名 *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* 到 *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

若要生成你的应用程序的容器的控制器，，请使用下面的部分`azds.yaml`配置文件：

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

控制器使用 Dockerfile 生成并运行你的应用程序。

*Build.context*属性列出了 Dockerfile 所在的目录。 *Build.dockerfile*属性定义 Dockerfile 的用于构建应用程序的生产版本的名称。 *Configurations.develop.build.dockerfile*属性配置的 Dockerfile 的开发版本的应用程序的名称。

具有不同的开发和生产的 Dockerfile，可在开发期间启用某些内容并禁用这些项进行生产部署。 例如，可以启用调试或在开发和生产环境中的禁用过程的更详细日志记录。 如果你的 Dockerfile 以不同方式命名，或位于不同位置，还可以更新这些属性。

为了帮助您在开发期间快速循环访问，Azure 开发人员空间将从本地项目的更改同步和增量更新你的应用程序。 以下部分中`azds.yaml`配置文件用于配置同步和更新：

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

中列出的文件和目录的更改将同步*configurations.develop.container.sync*属性。 在运行时，最初同步这些目录`up`命令以及当检测到更改。 如果有其他或不同的目录所同步到你的开发空间，可以更改此属性。

*Configurations.develop.container.iterate.buildCommands*属性指定如何生成开发方案中的应用程序。 *Configurations.develop.container.command*属性提供有关在开发方案中运行应用程序的命令。 您可能想要更新这些属性是否存在更多生成或运行时标志或你想要在开发过程中使用的参数。

*Configurations.develop.container.iterate.processesToKill*列出终止停止应用程序的进程。 您可能想要更新此属性，如果你想要更改你的应用程序在开发过程的重新启动行为。 例如，如果您更新*configurations.develop.container.iterate.buildCommands*或*configurations.develop.container.command*属性以更改如何构建应用程序或启动，可能需要更改哪些进程已停止。

准备你的代码使用时`azds prep`命令时，您可以添加`--public`标志。 添加`--public`标志创建一个可公开访问的 URL，为应用程序。 如果省略此标志，该应用程序才可在群集或使用 localhost 隧道访问。 运行后`azds prep`命令，可以更改此设置修改*ingress.enabled*中的属性`charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>调试代码

对于 Java、.NET 和 Node.js 应用程序，您可以调试直接在您使用 Visual Studio Code 或 Visual Studio 2017 的开发人员共享空间中运行的应用程序。 Visual Studio Code 和 Visual Studio 2017 提供了工具连接到你的开发空间，启动应用程序，并附加调试器。 运行之后`azds prep`，可以在 Visual Studio Code 或 Visual Studio 2017 中打开你的项目。 Visual Studio Code 或 Visual Studio 2017 将生成其自己的配置文件来连接分开运行`azds prep`。 从 Visual Studio Code 或 Visual Studio 2017 中，您可以设置断点并启动应用程序以便对你适用于开发人员的空间。

![调试代码](media/get-started-node/debug-configuration-nodejs2.png)

当您启动使用 Visual Studio Code 或 Visual Studio 2017 调试应用程序时，它们处理启动并正在运行相同的方式连接到你的开发空间`azds up`。 Visual Studio Code 和 Visual Studio 2017 中的客户端工具还提供与特定的调试信息的附加参数。 参数包含的调试程序映像中调试器的映像中, 调试器的位置和应用程序的容器装载调试器文件夹中的目标位置的名称。 

由客户端工具自动确定调试程序映像。 它使用类似于在 Dockerfile 过程中使用的方法和 Helm 图表生成运行时`azds prep`。 在应用程序的映像中装载调试器后，运行使用`azds exec`。

## <a name="sharing-a-dev-space"></a>共享开发空间

在使用一个团队，你可以[跨整个团队共享开发空间](how-to/share-dev-spaces.md)创建派生的开发空间。 对开发人员空间的资源组参与者访问权限的任何人都可以使用适用于开发人员空间。

此外可以创建新的开发人员共享空间派生自另一个适用于开发人员空格。 当您创建派生的开发空间*azds.io/父空间 = 父空间名称*标签添加到派生的开发空间的命名空间。 此外，与派生的开发空间共享父开发空间中的所有应用程序。 如果部署到派生的开发的应用程序的更新的版本，它将仅存在于派生的开发空间和父开发空间不受任何影响。 可以具有最多三个级别的派生的开发空间或*祖父*空格。

派生的开发空间会将其自己的应用程序和从其父级中共享的应用程序之间的请求还以智能方式路由。 通过将请求路由到派生的开发空间中的应用程序尝试并回退到共享的应用程序从父开发空间路由的工作原理。 路由将回退到祖父空间中的共享应用程序如果应用程序不是父空间中。

例如：
* 适用于开发人员空间*默认*具有应用程序*serviceA*并*serviceB* 。
* 适用于开发人员空间*azureuser*派生自*默认*。
* 更新的版本*serviceA*部署到*azureuser*。

使用时*azureuser*，对所有请求*serviceA*将路由到中的更新版本*azureuser*。 对请求*serviceB*将首先尝试将路由到*azureuser*版本*serviceB*。 由于不存在，它将被路由到*默认*新版*serviceB*。 如果*azureuser*新版*serviceA*删除后，对所有请求*serviceA*将回退到使用*默认*的版本*serviceA*。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 开发人员空间，请参阅以下快速入门：

* [使用 CLI 和 Visual Studio Code 的 Java](quickstart-java.md)
* [.NET core 和 CLI 和 Visual Studio Code](quickstart-netcore.md)
* [.NET core 和 Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [使用 CLI 和 Visual Studio Code 的 Node.js](quickstart-nodejs.md)

若要开始使用团队开发，请参阅以下操作指南文章：

* [团队开发-使用 CLI 和 Visual Studio Code 的 Java](team-development-java.md)
* [-.NET Core CLI 和 Visual Studio Code 和进行团队开发](team-development-netcore.md)
* [团队开发的.NET Core 和 Visual Studio 2017](team-development-netcore-visualstudio.md)
* [团队开发-使用 CLI 和 Visual Studio Code 的 Node.js](team-development-nodejs.md)
