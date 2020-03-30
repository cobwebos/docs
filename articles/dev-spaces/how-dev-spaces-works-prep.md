---
title: 为 Azure 开发空间准备项目的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 开发人员空间准备项目的工作原理
keywords: azds.yaml， Azure 开发空间， 开发空间， Docker， 库伯奈斯， Azure， AKS， Azure 库伯奈斯服务， 容器
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241629"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>为 Azure 开发空间准备项目的工作原理

Azure 开发人员空间为您提供了多种方法来快速迭代和调试 Kubernetes 应用程序，并与您的团队协作处理 Azure Kubernetes 服务 （AKS） 群集。 开发空间可以为项目生成 Dockerfile 和 Helm 图表。 开发人员空间还创建和使用配置文件，用于在 AKS 中部署、运行和调试 Kubernetes 应用程序。 所有这些文件都驻留在应用程序的代码中，可以添加到版本控制系统中。

本文介绍了为在具有开发空间的 AKS 中运行的项目准备发生的情况。

## <a name="prepare-your-code"></a>准备代码

为了在开发空间中运行应用程序，它需要将其容器化，并且您需要定义应该如何将其部署到 Kubernetes。 要对应用程序进行容器化，您需要 Dockerfile。 要定义应用程序如何部署到库伯内斯，您需要一个[Helm 图表](https://docs.helm.sh/)。 为了协助为应用程序创建 Dockerfile 和 Helm 图表，客户端工具提供以下`prep`命令：

```cmd
azds prep --enable-ingress
```

该`prep`命令将查看项目中的文件，并尝试创建 Dockerfile 和 Helm 图表，用于在 Kubernetes 中运行应用程序。 目前，该`prep`命令将生成具有以下语言的 Dockerfile 和 Helm 图表：

* Java
* Node.js
* .NET Core

*您必须*从包含源代码`prep`的目录中运行该命令。 从正确的`prep`目录运行命令允许客户端工具标识语言并创建适当的 Dockerfile 来容器化应用程序。 还可以从包含 Java`prep`项目的*pom.xml*文件的目录运行该命令。

如果从不包含源代码`prep`的目录运行命令，则客户端工具将不会生成 Dockerfile。 它还将显示一个错误，指出：*由于不支持的语言，无法生成 Dockerfile。* 如果客户端工具无法识别项目类型，也会发生此错误。

运行该`prep`命令时，可以选择指定标志`--enable-ingress`。 此标志告诉控制器为此服务创建可 Internet 访问的终结点。 如果不指定此标志，则服务只能从群集内或使用客户端工具创建的本地主机隧道进行访问。 您可以通过更新生成的 Helm 图表来`prep`启用或禁用此行为。

该`prep`命令不会替换项目中现有的 Dockerfile 或 Helm 图表。 如果现有的 Dockerfile 或 Helm 图表使用与`prep`命令生成的文件相同的命名约定，则`prep`该命令将跳过生成这些文件。 否则，该`prep`命令将生成其自己的 Dockerfile 或 Helm 图表，沿现有文件的一侧。

> [!IMPORTANT]
> Azure 开发人员空间使用 Dockerfile 和 Helm 图表来生成和运行代码，但如果您想要更改项目的生成和运行方式，则可以修改这些文件。

该`prep`命令还将在项目的根`azds.yaml`目录生成一个文件。 Azure 开发空间使用此文件来生成、安装、配置和运行应用程序。 此配置文件列出了 Dockerfile 和 Helm 图表的位置，并在这些项目的基础上提供了其他配置。

下面是使用[.NET Core 示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)创建的 azds.yaml 文件示例：

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

该`azds.yaml``prep`命令生成的文件适用于简单的单个项目开发方案。 如果特定项目的复杂性增加，则可能需要在运行`prep`该命令后更新此文件。 例如，您的项目可能需要根据您的开发或调试需求对生成或启动过程进行一些更改。 项目中可能还有多个应用程序，这些应用程序需要多个生成过程或不同的生成内容。

## <a name="next-steps"></a>后续步骤

要了解有关在开发空间中运行代码的更多详细信息，请参阅[使用 Azure 开发人员空间运行代码的工作原理][how-it-works-up]。

要开始使用 Azure 开发空间为 Azure 开发空间准备项目，请参阅以下快速入门：

* [使用可视化工作室代码和 Java 快速迭代和调试][quickstart-java]
* [使用可视化工作室代码和 .NET 快速迭代和调试][quickstart-netcore]
* [使用可视化工作室代码和 Node.js 快速迭代和调试][quickstart-node]
* [使用 Visual Studio 和 .NET 核心快速迭代和调试][quickstart-vs]
* [使用 CLI 在库伯奈斯开发应用程序][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md