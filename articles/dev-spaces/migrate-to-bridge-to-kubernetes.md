---
title: 迁移到 Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/12/2020
ms.topic: conceptual
description: 描述从 Azure Dev Spaces 到 Kubernetes 的迁移过程
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，桥到 Kubernetes
ms.openlocfilehash: 209776be80f2814dc8e4d347c0eea273017f70ad
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019930"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>迁移到 Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces 将于2023年10月31日停用。 开发人员应转向使用客户端开发人员工具，通过 Kubernetes。
>
> Azure Dev Spaces 的目的就是在 Kubernetes 上开发开发人员。 在 Azure Dev Spaces 的方法中，重大折衷会给开发人员带来额外的负担，以了解 Docker 和 Kubernetes 的配置以及 Kubernetes 的部署概念。 随着时间的推移，这种方法也显而易见了，Azure Dev Spaces 的方法不能有效地降低 Kubernetes 上的内部循环开发速度。 桥接 Kubernetes 可以有效地降低内部循环开发的速度，并避免开发人员产生不必要的负担。
>
> 核心任务保持不变：生成最佳开发人员体验，以便在更大的应用程序的上下文中开发、测试和调试微服务代码。

桥接 Kubernetes 为使用 Azure Dev Spaces 的许多开发方案提供更轻量的替代方法。 桥接 Kubernetes 是在[Visual Studio][vs]   和 [Visual Studio Code][vsc]中使用扩展的仅限客户端体验。  

桥接 Kubernetes 通过允许已建立的 Kubernetes 应用程序包括在本地开发工作站上运行的服务，帮助你的开发体验。 与开发人员共享空间不同，桥接于 Kubernetes 可减少内部循环复杂性，因为需要创建 Docker 和 Kubernetes 配置，使开发人员能够专注于其微服务代码的业务逻辑。

桥接 Kubernetes 可帮助您循环访问开发计算机上运行的代码，同时使用依赖项和 Kubernetes 环境中的现有配置。 与此相反，Azure Dev Spaces 将微服务部署到 Kubernetes 环境中，然后才能远程调试服务并循环访问代码。

本文提供 Azure Dev Spaces 与 Kubernetes 之间的比较，以及从 Azure Dev Spaces 迁移到 Kubernetes 的说明。

## <a name="development-approaches"></a>开发方法

![开发方法](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces 帮助 Kubernetes 开发人员使用直接在其 AKS 群集中运行的代码，从而避免了对不类似于已部署环境的本地环境的需求。 这种方法可以改进开发的某些方面，但也引入了学习和维护其他概念（如 Docker、Kubernetes 和 Helm），然后才能开始使用 Azure Dev Spaces。

桥接 Kubernetes 使开发人员能够直接在其开发计算机上工作，同时与群集的其余部分交互。 此方法在共享其群集提供的依赖关系和环境的同时，直接利用在开发计算机上运行代码的熟悉和速度。 此方法还利用了在 Kubernetes 中运行的保真度和缩放。

## <a name="feature-comparison"></a>功能对比

Azure Dev Spaces 和桥接 Kubernetes 具有类似的功能，它们在多个区域中也有所不同：

| 要求  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes 服务 | 15个 Azure 区域 | 任何 AKS 服务区域    |
| **安全性** |
| 群集上所需的安全访问  | AKS 群集参与者  | Kubernetes RBAC-部署更新   |
| 开发计算机上所需的安全访问  | 空值  | 本地管理/sudo   |
| **可用性** |
| 独立于 Kubernetes 和 Docker 项目  | 否  | 是   |
| 自动回滚更改，后期调试  | 否  | 是   |
| **环境** |
| 适用于 Visual Studio 2019  | 是  | 是   |
| 适用于 Visual Studio Code  | 是  | 是   |
| 适用于 CLI  | 是  | 否   |
| **操作系统兼容性** |
| 适用于 Windows 10  | 是  | 是  |
| 适用于 Linux  | 是  | 是  |
| 适用于 macOS  | 是  | 是  |
| **功能** |
| 开发人员隔离或团队开发  | 是  | 是  |
| 有选择地覆盖环境变量  | 否  | 是  |
| 创建 Dockerfile 和 Helm 图表  | 是  | 否  |
| 将代码永久性部署到 Kubernetes  | 是  | 否  |
| Kubernetes pod 中的远程调试  | 是  | 否  |
| 本地调试，连接到 Kubernetes  | 否  | 是  |
| 在同一工作站上同时调试多个服务  | 是  | 是  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes 内部循环开发

Azure Dev Spaces 和桥接到 Kubernetes 之间的最大差异在于代码的运行位置。 Azure Dev Spaces 有助于开发和调试微服务代码，但需要在群集中运行该代码。 桥接 Kubernetes 使你可以直接在开发计算机上开发和调试微服务代码，同时仍处于 Kubernetes 中运行的更大的应用程序的上下文中。 桥接 Kubernetes 扩展 Kubernetes 群集的外围，并允许本地进程从 Kubernetes 继承配置。

![内部循环开发](media/migrate-to-btk/btk-graphic-non-isolated.gif)

使用 Bridge 连接到 Kubernetes 时，会建立开发计算机与群集之间的网络连接。在此连接的生存期内，会将代理添加到群集中，以替代将请求重定向到开发计算机的 Kubernetes 部署。 断开连接后，应用程序部署将恢复为使用群集上运行的部署的原始版本。 这种方法与 Azure Dev Spaces 的工作方式不同，其中的代码与群集同步，然后运行。 Azure Dev Spaces 也不会回滚您的代码。

桥接 Kubernetes 可以灵活地使用在 Kubernetes 中运行的应用程序，而无需考虑它们的部署方法。 如果你使用 CI/CD 构建并运行应用程序，无论你使用的是已建立的工具还是自定义脚本，你仍可以使用 Bridge Kubernetes 来开发和调试代码。

> [!TIP]
> 利用 [Microsoft Kubernetes extension][kubernetes-extension] ，你可以通过 Intellisense 快速开发 Kubernetes 清单并帮助基架 Helm 图表。  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>从 Azure Dev Spaces 过渡到 Kubernetes

1. 如果使用的是 Visual Studio，请将 Visual Studio IDE 更新到版本16.7 或更高版本，并从 [Visual Studio Marketplace][vs-marketplace]将桥安装到 Kubernetes 扩展。 如果使用 Visual Studio Code，请将 Bridge 安装 [到 Kubernetes 扩展][vsc-marketplace]。
1. 使用 Azure 门户或 [AZURE DEV SPACES CLI][azds-delete]禁用 Azure Dev Spaces 控制器。
1. 使用 [Azure Cloud Shell](https://shell.azure.com)。 或者，在安装了 bash 的 Mac、Linux 或 Windows 上，打开 bash shell 提示。 请确保命令行环境中提供以下工具： Azure CLI、docker、kubectl、卷曲、tar 和 gunzip。
1. 创建容器注册表，或使用现有容器。 可以使用 [Azure 容器注册表](../container-registry/index.yml) 或使用 [Docker Hub](https://hub.docker.com/)在 azure 中创建容器注册表。
1. 运行迁移脚本，将 Azure Dev Spaces 资产转换为 Kubernetes 资产。 此脚本会构建一个与 Kubernetes 兼容的新映像，将其上传到指定的注册表，然后使用 [Helm](https://helm.sh) 更新包含该映像的群集。 必须提供资源组、AKS 群集的名称和容器注册表。 还有其他命令行选项，如下所示：

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   该脚本支持以下标志：

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. 将 *azds* 中的任何自定义项（如环境变量设置）手动迁移到项目的 *docker-compose.override.yml* 文件中。
1.  (可选) `azds.yaml` 从项目中删除该文件。
1. 重新部署应用程序。
1. 在已部署的应用程序上将桥配置为 Kubernetes。 若要详细了解如何在 Visual Studio 中使用 Bridge Kubernetes，请参阅 [在 Visual studio 中使用 bridge 来 Kubernetes][use-btk-vs]。 有关 VS Code，请参阅 [在 VS Code 中使用 Bridge Kubernetes][use-btk-vsc]。
1. 使用新创建的桥启动调试，以 Kubernetes 调试/启动配置文件。
1. 你可以根据需要再次运行该脚本以重新部署到群集。

## <a name="team-development-in-a-shared-cluster"></a>共享群集中的团队开发

你还可以将特定于开发人员的路由用于 Kubernetes。 Azure Dev Spaces 团队开发方案使用多个 Kubernetes 命名空间，使用父和子命名空间的概念将服务与应用程序的其余部分隔离开来。 桥接到 Kubernetes 提供了相同的功能，但具有改进的性能特征，并在相同的应用程序命名空间内。

桥接到 Kubernetes 和 Azure Dev Spaces 要求在整个应用程序中存在并传播 HTTP 标头。 如果已将应用程序配置为处理 Azure Dev Spaces 的标头传播，则需要更新该标头。 若要从 Azure Dev Spaces 过渡到 Kubernetes，请将 *azds* 中配置的标头更新为 " *Kubernetes*"。

## <a name="evaluate-bridge-to-kubernetes"></a>评估桥与 Kubernetes

如果要在群集中禁用 Azure Dev Spaces 之前尝试使用 Bridge 进行 Kubernetes，最简单的方法是使用新群集。 如果在同一群集上尝试同时使用 Azure Dev Spaces 并桥接到 Kubernetes，则在两者上使用路由功能时将遇到问题。

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>使用 Visual Studio 将 Bridge 评估到 Kubernetes

1. 将 Visual Studio IDE 更新到版本16.7 或更高版本，并从 [Visual Studio Marketplace][vs-marketplace]将桥安装到 Kubernetes 扩展。
1. 创建新的 AKS 群集并部署你的应用程序。 你还可以使用 [示例应用程序][btk-sample-app]。
1. 在已部署的应用程序上将桥配置为 Kubernetes。 若要详细了解如何在 Visual Studio 中使用 Bridge Kubernetes，请参阅 [将 bridge 与 Kubernetes 配合使用][use-btk-vs]。
1. 在 Visual Studio 中使用新创建的桥 Kubernetes 调试配置文件开始调试。

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>使用 Visual Studio Code 将 Bridge 评估 Kubernetes

1. 将 [桥安装到 Kubernetes 扩展][vsc-marketplace]。
1. 创建新的 AKS 群集并部署你的应用程序。 你还可以使用 [示例应用程序][btk-sample-app]。
1. 在已部署的应用程序上将桥配置为 Kubernetes。 有关在 Visual Studio Code 中使用 Bridge Kubernetes 的详细信息，请参阅 [将 bridge 与 Kubernetes 配合使用][use-btk-vsc]。
1. 在 Visual Studio 中使用新创建的桥 Kubernetes 启动配置文件开始调试。

## <a name="next-steps"></a>后续步骤

详细了解 Bridge 到 Kubernetes 的工作原理。

> [!div class="nextstepaction"]
> [Bridge to Kubernetes 的工作原理][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/