---
title: 使用 Azure 容器注册表任务（ACR 任务）自动执行 OS 和框架修补
description: 介绍 ACR 任务。ACR 任务是 Azure 容器注册表中的功能套件，它在云中提供安全、自动化的容器映像生成和修补。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: cd2b14dc29f865a162cb1ced605e740a96f7a46a
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329967"
---
# <a name="automate-os-and-framework-patching-with-acr-tasks"></a>使用 ACR 任务自动执行 OS 和框架修补

容器提供新的虚拟化级别，可将应用程序和开发人员依赖项与基础结构和操作要求隔离开来。 但是，另外还需要解决这种应用程序虚拟化的修补方式。

## <a name="what-is-acr-tasks"></a>什么是 ACR 任务？

**ACR 任务**是 Azure 容器注册表中的一个功能套件。 它为 Linux、Windows 和 ARM 提供基于云的容器映像生成，并可以针对 Docker 容器自动执行 [OS 和框架修补](#automate-os-and-framework-patching)。 ACR 任务不仅可以使用按需容器映像生成将“内部循环”开发周期扩展到云，而且还能在提交源代码或更新容器的基础映像时实现自动生成。 使用基本映像更新触发器，可以自动执行 OS 和应用程序框架修补工作流，在遵守不可变容器原则的前提下维护安全的环境。

可通过四种方式使用 ACR 任务来生成和测试容器映像：

* [快速任务](#quick-task)：在 Azure 中按需生成并推送容器映像，无需安装本地 Docker 引擎。 请考虑云中的 `docker build`、`docker push`。 从本地源代码或 Git 存储库生成。
* [提交源代码时生成](#automatic-build-on-source-code-commit)：将代码提交到 Git 存储库时自动触发容器映像生成。
* [更新基础映像时生成](#automate-os-and-framework-patching)：更新映像的基础映像后触发容器映像生成。
* [多步骤任务](#multi-step-tasks-preview)（预览版）：定义生成映像、将容器作为命令运行，并将映像推送到注册表的多步骤任务。 此 ACR 任务预览版功能支持按需任务执行，以及并行映像生成、测试和推送操作。

## <a name="quick-task"></a>快速任务

内部循环开发周期是指编写代码、生成和测试应用程序，然后提交到源代码管理的迭代过程，它事实上是容器生命周期管理的起点。

在你提交第一行代码之前，ACR 任务的[快速任务](container-registry-tutorial-quick-task.md)功能可以通过将容器映像生成卸载到 Azure，来提供集成式开发体验。 使用快速生成可以在提交代码之前验证自动化生成定义和捕获潜在的问题。

Azure CLI 中的 [az acr build][az-acr-build] 命令使用我们熟悉的 `docker build` 格式提取*上下文*（要生成的文件集），将其发送到 ACR 任务，并在完成后，默认将生成的映像推送到其注册表。

下表显示了 ACR 任务支持的上下文位置的几个示例：

| 上下文位置 | Description | 示例 |
| ---------------- | ----------- | ------- |
| 本地文件系统 | 本地文件系统上某个目录中的文件。 | `/home/user/projects/myapp` |
| GitHub 主分支 | GitHub 存储库主分支（或其他默认分支）中的文件。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存储库的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub PR | GitHub 存储库中的拉取请求。 | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| GitHub 子文件夹 | GitHub 存储库中某个子文件夹内的文件。 示例显示了 PR 和子文件夹规范的组合。 | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| 远程 tarball | 远程 Web 服务器上某个压缩存档中的文件。 | `http://remoteserver/myapp.tar.gz` |

ACR 任务旨在用作容器生命周期基元。 例如，将 ACR 任务集成到 CI/CD 解决方案。 然后，CI/CD 解决方案可以结合[服务主体][az-login-service-principal]执行 [az login][az-login]，发出 [az acr build][az-acr-build] 命令来启动映像生成。

第一篇 ACR 任务教程[使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)中介绍了快速任务的用法。

## <a name="automatic-build-on-source-code-commit"></a>提交源代码时自动生成

将代码提交到 Git 存储库后，使用 ACR 任务自动触发容器映像生成。 使用生成任务（可使用 Azure CLI 命令 [az acr task][az-acr-task] 进行配置）可以指定 Git 存储库，并选择性地指定分支和 Docker 文件。 当团队将代码提交到存储库时，ACR 任务创建的 Webhook 会触发存储库中定义的容器映像的生成。

> [!IMPORTANT]
> 如果以前在预览期使用 `az acr build-task` 创建了任务，则需要使用 [az acr task][az-acr-task] 命令重新创建这些任务。

第二篇 ACR 任务教程[使用 Azure 容器注册表任务自动执行容器映像生成](container-registry-tutorial-build-task.md)中介绍了如何在提交源代码时触发生成。

## <a name="automate-os-and-framework-patching"></a>自动执行 OS 和框架修补

真正增强容器生成工作流的 ACR 任务的强大之处在于，它能够检测基础映像的更新。 将更新的基础映像将推送到注册表时，ACR 任务可以基于该映像自动生成任何应用程序映像。

在广义上，可将容器映像分类为基本映像和应用程序映像。 基本映像通常包括应用程序所基于的操作系统和应用程序框架，以及其他自定义项。 这些基础映像本身通常基于公共上游映像，例如：[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet] 或 [Node.js][base-node]。 多个应用程序映像可以共享一个通用基本映像。

当上游维护者更新 OS 或应用程序框架映像时（例如，使用关键 OS 安全修补），也必须更新基本映像以包含关键修复。 然后，还必须重新生成每个应用程序映像，以包含目前已包含在基础映像中的这些上游修复。

由于 ACR 任务在生成容器映像时会动态发现基础映像依赖项，因此，ACR 任务可以检测到应用程序映像的基础映像何时发生了更新。 预配置一个[生成任务](container-registry-tutorial-base-image-update.md#create-a-task)后，ACR 任务会**自动重新生成每个应用程序映像**。 通过这种自动检测和重新生成，ACR 任务能够节省在正常情况下手动跟踪和更新引用已更新基础映像的每个应用程序映像所需的时间和精力。

在第三篇 ACR 任务教程[更新基础映像后使用 Azure 容器注册表任务自动执行映像生成](container-registry-tutorial-base-image-update.md)中了解 OS 和框架修补。

> [!NOTE]
> 仅当基础映像和应用程序映像驻留在同一 Azure 容器注册表中，或者基础映像驻留在公共 Docker 中心存储库中时，基础映像更新才会触发生成。

## <a name="multi-step-tasks-preview"></a>多步骤任务（预览版）

多步骤任务（ACR 任务的一项预览版功能）提供用于在云中构建、测试和修补容器映像的基于步骤的任务定义和执行。 任务步骤定义各个容器映像构建和推送操作。 它们还可以定义一个或多个容器的执行，每个步骤都使用容器作为其执行环境。

例如，可以创建一个多步骤任务来自动完成以下操作：

1. 生成 Web 应用程序映像
1. 运行 Web 应用程序容器
1. 生成 Web 应用程序测试映像
1. 运行针对正在运行的应用程序容器执行测试的 Web 应用程序测试容器
1. 如果测试通过，则生成 Helm 图表存档包
1. 使用新的 Helm 图表存档包执行 `helm upgrade`

使用多步骤任务可将映像的生成、运行和测试操作拆分成组合性更强的步骤，并支持步骤间的依赖关系。 使用 ACR 任务中的多步骤任务，可以更精细地控制映像生成、测试，以及 OS 和框架修补工作流。

若要了解多步骤任务，请参阅[在 ACR 任务中运行多步骤生成、测试和修补任务](container-registry-tasks-multi-step.md)。

> [!IMPORTANT]
> ACR 任务的多步骤任务功能目前以预览版提供。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改

## <a name="next-steps"></a>后续步骤

如果已准备好通过在云中生成容器映像来自动执行 OS 和框架修补，请查看由三篇文章构成的 ACR 任务系列教程。

> [!div class="nextstepaction"]
> [使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png