---
title: 如何使用 Azure Dev Spaces 运行代码
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 介绍如何在 Azure Kubernetes Service 上运行代码，并提供 Azure Dev Spaces
keywords: azds，yaml，Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: a189b26264f3e22a69acb3e3c0410203f9822c31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981289"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>如何使用 Azure Dev Spaces 运行代码

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 [准备好在开发环境中运行项目][how-it-works-prep]后，可以使用 dev 空间在 AKS 群集中生成并运行项目。

本文介绍如何在 AKS 中运行包含 Dev 空间的代码。

## <a name="run-your-code"></a>运行代码

若要在开发人员空间中运行代码，请 `up` 在文件所在的目录中发出命令 `azds.yaml` ：

```cmd
azds up
```

`up`命令将生成并运行项目所需的应用程序源文件和其他项目上传到 dev 空间。 其中，控制器位于开发环境中：

1. 创建用于部署应用程序的 Kubernetes 对象。
1. 为应用程序生成容器。
1. 将应用程序部署到 dev 空间。
1. 为应用程序终结点创建一个可公开访问的 DNS 名称（如果已配置）。
1. 使用 *端口转发* ，通过使用提供对应用程序终结点的访问 http://localhost 。
1. 将 stdout 和 stderr 转发到客户端工具。


## <a name="starting-a-service"></a>启动服务

当你在开发人员空间中启动服务时，客户端工具和控制器将与同步你的源文件、创建你的容器和 Kubernetes 对象并运行你的应用程序协同工作。

在更精细的级别上，运行时，会发生以下情况 `azds up` ：

1. [文件][sync-section] 从用户计算机同步到用户的 AKS 群集独有的 Azure 文件存储。 将上传源代码、Helm 图和配置文件。
1. 控制器创建用于启动新会话的请求。 此请求包含多个属性，包括唯一 ID、空间名称、源代码路径和调试标志。
1. 控制器会将 Helm 图表中的 *$ (标记) * 占位符替换为唯一的会话 ID，并安装服务的 Helm 图表。 将对唯一会话 ID 的引用添加到 Helm 图表后，可将部署到 AKS 群集中的容器的容器绑定回会话请求和关联的信息。
1. 在安装 Helm 图表的过程中，Kubernetes webhook 许可服务器会向应用程序的 pod 添加其他容器，以便检测和访问项目的源代码。 添加 devspaces 和 devspaces init 容器以提供 HTTP 跟踪和空间路由。 添加了 devspaces 容器，以便为 pod 提供对 Docker 实例的访问和用于生成应用程序容器的项目源代码。
1. 启动应用程序的 pod 后，将使用 devspaces 容器和 devspaces 初始化容器来生成应用程序容器。 然后，将启动应用程序容器和 devspaces 容器。
1. 在应用程序容器启动后，客户端功能将使用 Kubernetes *端口转发* 功能来提供对你的应用程序的 HTTP 访问 http://localhost 。 此端口转发将开发计算机连接到您的开发环境中的服务。
1. 如果已启动 pod 中的所有容器，则服务正在运行。 此时，客户端功能开始流式传输 HTTP 跟踪、stdout 和 stderr。 此信息由开发人员的客户端功能显示。

## <a name="updating-a-running-service"></a>更新正在运行的服务

当服务正在运行时，如果有项目源文件发生更改，Azure Dev Spaces 可以更新该服务。 开发人员空间还根据更改的文件类型处理更新服务的方式。 开发人员可通过三种方式来更新正在运行的服务：

* 直接更新文件
* 重新生成并重新启动应用程序在运行的应用程序容器内的进程
* 重新生成和重新部署应用程序的容器

![Azure Dev Spaces 文件同步](media/how-dev-spaces-works/file-sync.svg)

某些作为静态资产的项目文件（如 html、css 和 cshtml 文件）可以直接在应用程序的容器中更新，而无需重新启动任何内容。 如果静态资产发生更改，则新文件将同步到 dev 空间，然后由正在运行的容器使用。

可以通过在正在运行的容器中重新启动应用程序的进程来应用对文件（如源代码或应用程序配置文件）的更改。 同步这些文件后，将使用 *devhostagent* 进程在运行中的容器内重新启动应用程序的进程。 最初创建应用程序的容器时，控制器会将该应用程序的启动命令替换为一个名为 *devhostagent*的不同进程。 然后，应用程序的实际进程在 *devhostagent*下以子进程的形式运行，并使用 *devhostagent*的输出管道输出。 *Devhostagent*进程也是开发空间的一部分，可以代表 dev 空间在正在运行的容器中执行命令。 执行重新启动时， *devhostagent*：

* 停止当前进程或与应用程序关联的进程
* 重新生成应用程序
* 重新启动与应用程序关联的进程

[在中 `azds.yaml` 配置][azds-yaml-section] *devhostagent*执行前面步骤的方式。

对项目文件（如 Dockerfile、.csproj 文件或 Helm 图表的任何部分）的更新需要重新生成和重新部署应用程序的容器。 当其中一个文件同步到开发人员空间时，控制器将运行 [helm upgrade][helm-upgrade] 命令，然后重新生成并重新部署应用程序的容器。

## <a name="file-synchronization"></a>文件同步

第一次在开发环境中启动应用程序时，将上传应用程序的所有源文件。 当应用程序正在运行，并且在以后的重新启动时，只会上载已更改的文件。 使用两个文件来协调此过程：一个客户端文件和一个控制器端文件。

客户端文件存储在临时目录中，并基于在开发人员空间中运行的项目目录的哈希值进行命名。 例如，在 Windows 上，你的项目将有一个文件，如 *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* 。 在 Linux 上，客户端文件存储在 */tmp* 目录中。 可以通过运行命令来查找 macOS 上的目录 `echo $TMPDIR` 。

此文件采用 JSON 格式，其中包含：

* 与开发人员空间同步的每个项目文件的条目
* 同步 ID
* 上次同步操作的时间戳

每个项目文件条目都包含该文件的路径及其时间戳。

控制器端文件存储在 AKS 群集上。 它包含同步 ID 和上次同步的时间戳。

如果客户端和控制器端文件之间的同步时间戳不匹配，则会发生同步。 在同步过程中，客户端工具将循环访问客户端文件中的文件项。 如果该文件的时间戳在同步时间戳之后，则该文件将同步到 dev 空间。 完成同步后，会在客户端和控制器端文件上更新同步时间戳。

如果未提供客户端文件，则将同步所有项目文件。 此行为使你可以通过删除客户端文件来强制执行完全同步。

## <a name="how-running-your-code-is-configured"></a>如何配置运行代码

Azure Dev Spaces 使用此 `azds.yaml` 文件来安装和配置服务。 控制器使用 `install` 文件中的属性 `azds.yaml` 来安装 Helm 图表并创建 Kubernetes 对象：

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

默认情况下，该 `prep` 命令将生成 Helm 图表。 它 *还将 "Helm" 属性设置* 为 "" 图表的目录。 如果要在其他位置使用 Helm 图，则可以更新此属性以使用该位置。

安装 Helm 图表时，Azure Dev Spaces 提供了一种替代 Helm 图中的值的方法。 Helm 图的默认值为 `charts/APP_NAME/values.yaml` 。

使用 *install. values* 属性，可以列出一个或多个文件，用于定义要在 Helm 图表中替换的值。 例如，如果在开发环境中运行应用程序时需要专门使用主机名或数据库配置，则可以使用此替代功能。 你还可以添加 *？* 在任何文件名的末尾，将其设置为可选。

*Install*属性允许您配置一个或多个要在 Helm 图表中替换的值。 在 *install* 中配置的任何值都将替代 *install*中列出的文件中配置的值。 " *安装* " 下的属性取决于 Helm 图表中的值，可能因生成的 Helm 图表而有所不同。

在上面的示例中， *replicaCount* 属性告诉控制器应用程序的多少实例要在你的开发环境中运行。 根据你的方案，你可以增大此值，但会影响将调试器附加到应用程序的 pod。 有关详细信息，请参阅 [故障排除一文][troubleshooting]。

在生成的 Helm 图表中，容器映像设置为 *{{。值. repository}}： {{。值. tag}}*。 `azds.yaml`默认情况下*install.set.image.tag* ，该文件将 " (标记" 属性定义为 *$ 标记) * 。 *值. tag}}*。 通过以这种方式设置 *该属性，* 它可以在运行 Azure Dev Spaces 时以不同的方式标记应用程序的容器映像。 在此特定情况下，图像被标记为* \<value from image.repository> ： $ (标记) *。 必须使用 *$ (标记) * 变量作为 "AKS" 的值   *，以使* Dev 空间能够识别和定位群集中的容器。

在上面的示例中， `azds.yaml` 定义了 " *安装*"。 " *Install* " 属性定义了公共终结点的主机名格式。 此属性还使用 *$ (spacePrefix) *， *$ (rootSpacePrefix) *， *$ (hostSuffix) *，这是控制器提供的值。

*$ (spacePrefix) *是子 dev 空间的名称，采用*SPACENAME*的形式。 *$ (rootSpacePrefix) *为父空间的名称。 例如，如果 *azureuser* 是 *默认*的子空间，则 *$ (rootSpacePrefix) * 的值是 *默认* 值， *$ (spacePrefix) * 的值为 *azureuser*。 如果空间不是子空间， *$ (spacePrefix) * 为空白。 例如，如果 *默认* 空间没有父空间， *$ (rootSpacePrefix) * 的值是 *默认* 值， *$ (spacePrefix) * 的值为空。 *$ (hostSuffix) *是指向在 AKS 群集中运行的 Azure Dev Spaces 入口控制器的 DNS 后缀。 此 DNS 后缀对应于通配符 DNS 条目，例如* \* 。* 在将 Azure Dev Spaces 控制器添加到 AKS 群集时创建的 eus. azds。 RANDOM_VALUE

在上述 `azds.yaml` 文件中，你还可以更新 " *安装* "，以更改应用程序的主机名。 例如，如果你想要将应用程序的主机名从 *$ (spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix) * 到 *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix) *来简化应用程序的主机名。

若要为应用程序生成容器，控制器将使用配置文件的以下部分 `azds.yaml` ：

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

Dockerfile *属性列出* 了存在的目录。 *build.dockerfile*属性定义用于生成应用程序的生产版本的 Dockerfile 的名称。 *configurations.develop.build.dockerfile*属性为应用程序的开发版本配置 Dockerfile 的名称。

使用不同的 Dockerfile 进行开发和生产，可以在开发过程中启用某些功能，并为生产部署禁用这些项。 例如，你可以在开发过程中启用调试或更详细日志记录，并在生产环境中禁用。 如果 Dockerfile 的命名方式不同或位于其他位置，也可以更新这些属性。

为了帮助你在开发过程中快速迭代，Azure Dev Spaces 将从本地项目同步更改，并以增量方式更新应用程序。 配置文件中的以下部分 `azds.yaml` 用于配置同步和更新：

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

将同步更改的文件和目录列在 " *配置.* " 属性中。 当你运行命令时，以及 `up` 在检测到更改时，这些目录将进行同步。 如果要将其他或不同的目录同步到开发人员空间，则可以更改此属性。

*BuildCommands*属性指定了如何在开发方案中生成应用程序。 " *配置.* " 属性提供在开发方案中运行应用程序的命令。 如果要在开发期间使用其他生成或运行时标志或参数，则可能需要更新这些属性中的任何一个。

*ProcessesToKill*列出了终止应用程序时要终止的进程。 如果希望在开发过程中更改应用程序的重新启动行为，则可能需要更新此属性。 例如，如果你更新了*buildCommands 或*属性来更改应用程序的生成方式，你可能需要更改停止的进程，以更改应用程序的生成或启动*方式。*

使用命令准备你的代码时 `azds prep` ，你可以选择添加 `--enable-ingress` 标志。 添加 `--enable-ingress` 标志将为应用程序创建可公开访问的 URL。 如果省略此标志，则只能在群集内或使用 localhost 隧道访问应用程序。 运行该命令后 `azds prep` ，可以更改此设置以修改中的 " *已启用* " 属性 `charts/APPNAME/values.yaml` ：

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>后续步骤

若要了解有关网络的详细信息以及如何在 Azure Dev Spaces 中路由请求，请参阅 [如何使用 Azure Dev Spaces 进行路由][how-it-works-routing]。

若要详细了解如何使用 Kubernetes 快速循环访问和开发，请参阅 [如何使用 Bridge 进行 Kubernetes][how-it-works-bridge-to-kubernetes] ，以及 [如何使用 Azure Dev Spaces 的远程调试代码][how-it-works-remote-debugging]。


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md