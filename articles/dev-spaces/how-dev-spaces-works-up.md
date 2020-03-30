---
title: 使用 Azure 开发空间运行代码的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 开发人员空间在 Azure 库伯内斯服务上运行代码的过程
keywords: azds.yaml， Azure 开发空间， 开发空间， Docker， 库伯奈斯， Azure， AKS， Azure 库伯奈斯服务， 容器
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241356"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>使用 Azure 开发空间运行代码的工作原理

Azure 开发人员空间为您提供了多种方法来快速迭代和调试 Kubernetes 应用程序，并与您的团队协作处理 Azure Kubernetes 服务 （AKS） 群集。 一旦[项目准备在开发空间中运行][how-it-works-prep]，您可以使用开发人员空间在 AKS 群集中生成和运行项目。

本文介绍了在具有开发空间的 AKS 中运行代码的情况。

## <a name="run-your-code"></a>运行代码

要在开发空间中运行代码，`up`请发出与`azds.yaml`文件相同的目录中的命令：

```cmd
azds up
```

该`up`命令将生成和运行项目所需的应用程序源文件和其他项目上载到开发空间。 从那里，开发空间中的控制器：

1. 创建库伯奈斯对象以部署应用程序。
1. 为应用程序生成容器。
1. 将应用程序部署到开发空间。
1. 如果配置了应用程序终结点，则为应用程序终结点创建可公开访问的 DNS 名称。
1. 使用*端口转发*提供对应用程序终结点的访问。 http://localhost
1. 将稳贞和稳稳地转发到客户端工具。


## <a name="starting-a-service"></a>启动服务

在开发空间中启动服务时，客户端工具和控制器协调工作，以同步源文件、创建容器和 Kubernetes 对象以及运行应用程序。

在更精细的级别上，下面是运行`azds up`时发生的情况：

1. [文件][sync-section]从用户的计算机同步到用户 AKS 群集独有的 Azure 文件存储。 将上载源代码、Helm 图表和配置文件。
1. 控制器创建启动新会话的请求。 此请求包含多个属性，包括唯一 ID、空格名称、源代码路径和调试标志。
1. 控制器将 Helm 图表中的 *$（标记）* 占位符替换为唯一的会话 ID，并为您的服务安装 Helm 图表。 向 Helm 图表添加对唯一会话 ID 的引用允许将部署到此特定会话的 AKS 群集的容器绑定回会话请求和相关信息。
1. 在安装 Helm 图表期间，Kubernetes Webhook 准入服务器向应用程序的窗格中添加了其他容器，以便进行检测并访问项目的源代码。 添加开发空间代理和开发空间代理-init 容器以提供 HTTP 跟踪和空间路由。 添加 devspace-build 容器是为了向 pod 提供对 Docker 实例和项目源代码的访问，以便生成应用程序的容器。
1. 启动应用程序的 pod 时，使用开发空间生成容器和开发空间代理 -init 容器来生成应用程序容器。 然后启动应用程序容器和开发空间代理容器。
1. 应用程序容器启动后，客户端功能使用 Kubernetes*端口转发*功能通过http://localhost提供对应用程序的 HTTP 访问。 此端口转发将开发计算机连接到开发空间中的服务。
1. 当窗格中的所有容器都启动时，服务正在运行。 此时，客户端功能开始流式传输 HTTP 跟踪、抖痕和稳稳。 此信息由开发人员的客户端功能显示。

## <a name="updating-a-running-service"></a>更新正在运行的服务

在服务运行时，如果任何项目源文件出现更改，Azure 开发人员空间可以更新该服务。 Dev Spaces 还会根据更改的文件类型以不同的方式处理更新服务。 开发空间可通过三种方式更新正在运行的服务：

* 直接更新文件
* 在正在运行的应用程序的容器内重建和重新启动应用程序的进程
* 重建和重新部署应用程序的容器

![Azure 开发空间文件同步](media/how-dev-spaces-works/file-sync.svg)

某些静态资源的项目文件（如 html、css 和 cshtml 文件）可以直接在应用程序的容器中更新，而无需重新启动任何内容。 如果静态资产发生更改，则新文件将同步到开发空间，然后由正在运行的容器使用。

可以通过在正在运行的容器中重新启动应用程序的进程来应用对文件（如源代码或应用程序配置文件）的更改。 同步这些文件后，将使用*devhostagent*进程在正在运行的容器中重新启动应用程序的进程。 最初创建应用程序的容器时，控制器将应用程序的启动命令替换为称为*devhostagent*的不同进程。 然后，应用程序的实际进程在*devhostagent*下作为子进程运行，并且其输出使用*devhostagent*的输出进行管道输出。 *devhostagent*进程也是开发空间的一部分，可以代表开发人员空间在正在运行的容器中执行命令。 执行重新启动时 *，devhostagent*：

* 停止与应用程序关联的当前进程或进程
* 重建应用程序
* 重新启动与应用程序关联的进程或进程

在[`azds.yaml`中配置][azds-yaml-section]*了 devhostagent*执行上述步骤的方式。

对项目文件（如 Dockerfile、csproj 文件）或 Helm 图表的任何部分的更新都需要重新生成和重新部署应用程序的容器。 当其中一个文件同步到开发空间时，控制器将运行[掌舵升级][helm-upgrade]命令，并重新生成和重新部署应用程序的容器。

## <a name="file-synchronization"></a>文件同步

第一次在开发空间中启动应用程序时，将上载应用程序的所有源文件。 当应用程序正在运行并在以后重新启动时，仅上载已更改的文件。 两个文件用于协调此过程：客户端文件和控制器端文件。

客户端文件存储在临时目录中，并根据在开发人员空间中运行的项目目录的哈希值命名。 例如，在 Windows 上，您将有一个文件，如*用户\USERNAME_AppData_Local_Temp_1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog。* 您的项目。 在 Linux 上，客户端文件存储在 */tmp*目录中。 您可以通过运行`echo $TMPDIR`命令在 macOS 上找到目录。

此文件采用 JSON 格式，包含：

* 与开发空间同步的每个项目文件的条目
* 同步 ID
* 上次同步操作的时间戳

每个项目文件条目都包含文件的路径及其时间戳。

控制器端文件存储在 AKS 群集上。 它包含同步 ID 和上次同步的时间戳。

当同步时间戳在客户端和控制器端文件之间不匹配时，将发生同步。 在同步期间，客户端工具会遍遍客户端文件中的文件条目。 如果文件的时间戳位于同步时间戳之后，则该文件将同步到开发空间。 同步完成后，在客户端和控制器端文件上更新同步时间戳。

如果客户端文件不存在，则所有项目文件都同步。 此行为允许您通过删除客户端文件来强制完全同步。

## <a name="how-running-your-code-is-configured"></a>如何配置代码的运行方式

Azure 开发空间使用`azds.yaml`该文件来安装和配置服务。 控制器使用`install``azds.yaml`文件中的属性来安装 Helm 图表并创建 Kubernetes 对象：

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

默认情况下，该`prep`命令将生成 Helm 图表。 它还将*安装.chart*属性设置为 Helm 图表的目录。 如果要在不同位置使用 Helm 图表，可以更新此属性以使用该位置。

安装 Helm 图表时，Azure 开发人员空间提供了一种覆盖 Helm 图表中的值的方法。 Helm 图表的默认值在`charts/APP_NAME/values.yaml`中。

使用*install.value*属性，可以列出一个或多个文件，这些文件定义要在 Helm 图表中替换的值。 例如，如果需要主机名或数据库配置，特别是在开发空间中运行应用程序时，则可以使用此覆盖功能。 您还可以添加 *？* 在任何文件名的末尾将其设置为可选。

*安装.set*属性允许您配置要在 Helm 图表中替换的一个或多个值。 在*install.set*中配置的任何值都将覆盖*在 install.值*中列出的文件中配置的值。 *install.set*下的属性取决于 Helm 图表中的值，并且可能因生成的 Helm 图表而异。

在上面的示例中 *，install.set.replicaCount*属性告诉控制器在开发空间中运行应用程序有多少实例。 根据您的方案，可以增加此值，但它会影响将调试器附加到应用程序的 pod。 有关详细信息，请参阅[故障排除文章][troubleshooting]。

在生成的 Helm 图表中，容器图像设置为 *{ } 。值.image.repository_：]。值.image.tag]*。 默认情况下`azds.yaml`，该文件将*安装.set.image.tag*属性定义为 *$（标记），* 它用作 *{ 的值。值.image.tag]*。 通过以这种方式设置*install.set.image.tag*属性，它允许在运行 Azure 开发人员空间时以不同方式标记应用程序的容器映像。 在此特定情况下，图像被标记为*\<图像中的值>。* 您必须使用 *$（标记）* 变量作为*安装.set.image.tag*的值，以便开发人员空间识别和定位 AKS 群集中的容器。

在上面的示例中，`azds.yaml`定义*安装.set.ingress.hosts*。 *安装.set.ingress.hosts*属性为公共终结点定义主机名格式。 此属性还使用 *$（空格前缀）$（**根空间前缀）* 和 *$（主机Suffix），* 它们是控制器提供的值。

*$（空格前缀）* 是子开发空间的名称，它采用*SPACENAME.s*的形式。 *$（根空间前缀）* 是父空格的名称。 例如，如果*Azureuser*是*默认*的子空间，则 *$（rootSpace前缀）* 的值*为默认值*，*并且值 $（空格前缀）* 为*azureuser.s*。 如果空格不是子空格，*则 $（空格前缀）* 为空。 例如，如果*默认*空间没有父空间，则 *$（根空间前缀）* 的值为*默认值*，并且值 *$（空格前缀）为*空。 *$（主机Suffix）* 是指向在 AKS 群集中运行的 Azure 开发空间入口控制器的 DNS 后缀。 此 DNS 后缀对应于通配符 DNS 条目，*\*例如 。RANDOM_VALUE.eus.azds.io*）是在将 Azure 开发人员空间控制器添加到 AKS 群集时创建的。

在上面`azds.yaml`的文件中，您还可以更新*安装.set.ingress.hosts*来更改应用程序的主机名。 例如，如果要将应用程序的主机名从 *$（空格前缀）$（根空间前缀）Webfrontend$（主机Suffix）* 简化为 *$（空格前缀）$（rootSpace前缀）web$（主机素缀）。*

要为应用程序构建容器，控制器使用配置文件的`azds.yaml`以下部分：

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

控制器使用 Dockerfile 生成和运行应用程序。

*build.context*属性列出了 Dockerfile 存在的目录。 *build.dockerfile*属性定义用于生成应用程序的生产版本的 Dockerfile 的名称。 *配置.开发.build.dockerfile*属性为应用程序的开发版本配置 Dockerfile 的名称。

具有不同的 Dockerfile 进行开发和生产允许您在开发期间启用某些内容，并禁用这些项以进行生产部署。 例如，您可以在开发期间启用调试或更详细的日志记录，并在生产环境中禁用。 如果 Dockerfile 的命名方式不同或位于其他位置，也可以更新这些属性。

为了帮助您在开发过程中快速迭代，Azure 开发人员空间将同步本地项目的更改并逐步更新应用程序。 配置文件中的`azds.yaml`以下部分用于配置同步和更新：

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

将同步更改的文件和目录列在*配置.d.d.s.container.sync*属性中。 在运行命令时以及检测到更改时，`up`最初会同步这些目录。 如果希望将其他目录或不同目录同步到开发空间，则可以更改此属性。

*配置.dd.容器.itit.buildCommands*属性指定如何在开发方案中生成应用程序。 *配置.dd.容器.命令*属性提供在开发方案中运行应用程序的命令。 如果要在开发期间使用其他生成或运行时标志或参数，则可能需要更新这些属性之一。

*配置.dd.容器.iterate.进程ToKill*列出了要终止的进程以停止应用程序。 如果要在开发期间更改应用程序的重新启动行为，则可能需要更新此属性。 例如，如果您更新了*配置.d.d.s.容器.itite.buildCommand*或*配置.d.is.container.命令*属性来更改应用程序的生成或启动方式，则可能需要更改停止的进程。

使用`azds prep`命令准备代码时，可以选择添加`--enable-ingress`标志。 添加该`--enable-ingress`标志可为应用程序创建可公开访问的 URL。 如果省略此标志，则应用程序只能在群集中或使用本地主机隧道访问。 运行命令`azds prep`后，可以在 中更改更改*ininin.启用*属性的`charts/APPNAME/values.yaml`此设置。

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>后续步骤

要了解有关网络以及如何在 Azure 开发人员空间中路由有关，请参阅[路由如何与 Azure 开发空间一起工作][how-it-works-routing]。

要了解有关使用 Azure 开发人员空间快速迭代和开发有关详细信息，请参阅[如何将开发计算机连接到开发空间工作，][how-it-works-connect]以及使用[Azure 开发人员空间远程调试代码的工作原理][how-it-works-remote-debugging]。

要开始使用 Azure 开发空间来运行项目，请参阅以下快速入门：

* [使用可视化工作室代码和 Java 快速迭代和调试][quickstart-java]
* [使用可视化工作室代码和 .NET 快速迭代和调试][quickstart-netcore]
* [使用可视化工作室代码和 Node.js 快速迭代和调试][quickstart-node]
* [使用 Visual Studio 和 .NET 核心快速迭代和调试][quickstart-vs]
* [使用 CLI 在库伯奈斯开发应用程序][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md