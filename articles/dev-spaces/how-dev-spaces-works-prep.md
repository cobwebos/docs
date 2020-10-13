---
title: 准备 Azure Dev Spaces 项目的方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 介绍如何使用 Azure Dev Spaces 准备项目
keywords: azds，yaml，Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968151"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>准备 Azure Dev Spaces 项目的方式

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 Dev 空间可以为项目生成 Dockerfile 和 Helm 图表。 开发人员空间还创建并使用配置文件在 AKS 中部署、运行和调试 Kubernetes 应用程序。 所有这些文件都驻留在应用程序的代码中，可以添加到版本控制系统中。

本文介绍了准备项目以在包含 Dev 空间的 AKS 中运行的情况。

## <a name="prepare-your-code"></a>准备你的代码

若要在开发环境中运行应用程序，需要将其放在容器中，并需要定义应该如何将其部署到 Kubernetes。 若要容器化你的应用程序，你需要一个 Dockerfile。 若要定义将应用程序部署到 Kubernetes 的方式，需要一个 [Helm 图](https://docs.helm.sh/)。 为帮助创建应用程序的 Dockerfile 和 Helm 图表，客户端工具提供 `prep` 以下命令：

```cmd
azds prep --enable-ingress
```

此 `prep` 命令将查看项目中的文件，并尝试创建 Dockerfile 和 Helm 图表以在 Kubernetes 中运行你的应用程序。 目前，该 `prep` 命令将生成具有以下语言的 Dockerfile 和 Helm 图表：

* Java
* Node.js
* .NET Core

*必须* `prep` 从包含源代码的目录运行该命令。 `prep`从正确的目录中运行命令，可让客户端工具识别语言，并创建相应的 Dockerfile 来容器化应用程序。 还可以 `prep` 从包含 Java 项目 *pom.xml* 文件的目录运行该命令。

如果在 `prep` 不包含源代码的目录中运行命令，则客户端工具不会生成 Dockerfile。 它还会显示一条错误消息，指出： *由于语言不受支持，无法生成 Dockerfile*。 如果客户端工具无法识别项目类型，也会出现此错误。

运行 `prep` 命令时，可以选择指定 `--enable-ingress` 标志。 此标志通知控制器为此服务创建可通过 internet 访问的终结点。 如果未指定此标志，则只能从群集内部或使用客户端工具创建的 localhost 隧道访问该服务。 可以在运行 `prep` 命令后通过更新生成的 Helm 图表来启用或禁用此行为。

此 `prep` 命令不会替换项目中的任何现有 dockerfile 或 Helm 图表。 如果现有的 Dockerfile 或 Helm 图表使用的命名约定与命令生成的文件相同 `prep` ，则该 `prep` 命令将跳过生成这些文件。 否则，该 `prep` 命令将生成自己的 Dockerfile 或 Helm 图表，并将其作为现有文件的边缘。

> [!IMPORTANT]
> Azure Dev Spaces 使用项目的 Dockerfile 和 Helm 图表生成并运行你的代码，但如果你想要更改生成和运行项目的方式，则可以修改这些文件。

`prep`命令还将 `azds.yaml` 在项目的根目录中生成一个文件。 Azure Dev Spaces 使用此文件生成、安装、配置和运行您的应用程序。 此配置文件列出 Dockerfile 和 Helm 图的位置，还在这些项目之上提供其他配置。

下面是使用 [.Net Core 示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)创建的 azds 文件示例：

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

`azds.yaml`该命令生成的文件 `prep` 旨在适用于简单的单一项目开发方案。 如果特定的项目增加了复杂性，则在运行该命令后，可能需要更新此文件 `prep` 。 例如，您的项目可能需要根据您的开发或调试需求对您的生成或启动过程进行一些更改。 你的项目中还可能有多个应用程序，这需要多个生成过程或不同的生成内容。

## <a name="next-steps"></a>后续步骤

若要详细了解如何在开发环境中运行代码，请参阅 [使用 Azure Dev Spaces 运行代码的工作原理][how-it-works-up]。

[how-it-works-up]: how-dev-spaces-works-up.md