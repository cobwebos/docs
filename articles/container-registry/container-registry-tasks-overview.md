---
title: 自动生成和修补与 Azure 容器注册表任务 （ACR 任务） 的容器映像
description: ACR 任务，提供安全、 自动化容器映像生成、 管理和修补在云中的 Azure 容器注册表中的功能的一套简介。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5089650996693b81e548bba8d89c0de29a8afd93
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147980"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>自动生成容器映像和维护与 ACR 任务

容器提供新的虚拟化级别，可将应用程序和开发人员依赖项与基础结构和操作要求隔离开来。 保留，但是，是需要解决如何管理和修补容器生命周期内此应用程序虚拟化。

## <a name="what-is-acr-tasks"></a>什么是 ACR 任务？

**ACR 任务**是 Azure 容器注册表中的一个功能套件。 它为 Linux、Windows 和 ARM 提供基于云的容器映像生成，并可以针对 Docker 容器自动执行 [OS 和框架修补](#automate-os-and-framework-patching)。 ACR 任务不仅可以使用按需容器映像生成将“内部循环”开发周期扩展到云，而且还能在提交源代码或更新容器的基础映像时实现自动生成。 使用基本映像更新触发器，可以自动执行 OS 和应用程序框架修补工作流，在遵守不可变容器原则的前提下维护安全的环境。

可通过四种方式使用 ACR 任务来生成和测试容器映像：

* [快速任务](#quick-task)：在 Azure 中按需生成并推送容器映像，无需安装本地 Docker 引擎。 请考虑云中的 `docker build`、`docker push`。 从本地源代码或 Git 存储库生成。
* [提交源代码时生成](#automatic-build-on-source-code-commit)：将代码提交到 Git 存储库时自动触发容器映像生成。
* [更新基础映像时生成](#automate-os-and-framework-patching)：更新映像的基础映像后触发容器映像生成。
* [多步骤任务](#multi-step-tasks):定义生成映像、将容器作为命令运行，并将映像推送到注册表的多步骤任务。 ACR 任务支持按需任务执行和并行映像生成、 测试和推送操作的这一功能。

## <a name="quick-task"></a>快速任务

内部循环开发周期是指编写代码、生成和测试应用程序，然后提交到源代码管理的迭代过程，它事实上是容器生命周期管理的起点。

在你提交第一行代码之前，ACR 任务的[快速任务](container-registry-tutorial-quick-task.md)功能可以通过将容器映像生成卸载到 Azure，来提供集成式开发体验。 使用快速生成可以在提交代码之前验证自动化生成定义和捕获潜在的问题。

Azure CLI 中的 [az acr build][az-acr-build] 命令使用我们熟悉的 `docker build` 格式提取*上下文*（要生成的文件集），将其发送到 ACR 任务，并在完成后，默认将生成的映像推送到其注册表。

有关的介绍，请参阅到快速入门[生成并运行容器映像](container-registry-quickstart-task-cli.md)Azure 容器注册表中。  

下表显示了 ACR 任务支持的上下文位置的几个示例：

| 上下文位置 | 描述 | 示例 |
| ---------------- | ----------- | ------- |
| 本地文件系统 | 本地文件系统上某个目录中的文件。 | `/home/user/projects/myapp` |
| GitHub 主分支 | GitHub 存储库主分支（或其他默认分支）中的文件。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存储库的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子文件夹 | GitHub 存储库中某个子文件夹内的文件。 示例显示了分支和子文件夹规范的组合。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| 远程 tarball | 远程 Web 服务器上某个压缩存档中的文件。 | `http://remoteserver/myapp.tar.gz` |

ACR 任务旨在用作容器生命周期基元。 例如，将 ACR 任务集成到 CI/CD 解决方案。 然后，CI/CD 解决方案可以结合[服务主体][az-login-service-principal]执行 [az login][az-login]，发出 [az acr build][az-acr-build] 命令来启动映像生成。

第一篇 ACR 任务教程[使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)中介绍了快速任务的用法。

## <a name="automatic-build-on-source-code-commit"></a>提交源代码时自动生成

将代码提交到 Git 存储库后，使用 ACR 任务自动触发容器映像生成。 使用生成任务（可使用 Azure CLI 命令 [az acr task][az-acr-task] 进行配置）可以指定 Git 存储库，并选择性地指定分支和 Docker 文件。 当团队将代码提交到存储库时，ACR 任务创建的 Webhook 会触发存储库中定义的容器映像的生成。

> [!IMPORTANT]
> 如果以前在预览期使用 `az acr build-task` 创建了任务，则需要使用 [az acr task][az-acr-task] 命令重新创建这些任务。

第二篇 ACR 任务教程[使用 Azure 容器注册表任务自动执行容器映像生成](container-registry-tutorial-build-task.md)中介绍了如何在提交源代码时触发生成。

## <a name="automate-os-and-framework-patching"></a>自动执行 OS 和框架修补

真正增强容器生成工作流的 ACR 任务的强大之处在于，它能够检测基础映像的更新。 当更新的基本映像推送到注册表，或在存储等公共库中 Docker Hub 中更新的基本映像时，ACR 任务可以自动生成基于它的任何应用程序映像。

在广义上，可将容器映像分类为基本映像和应用程序映像。   基本映像通常包括应用程序所基于的操作系统和应用程序框架，以及其他自定义项。 这些基础映像本身通常基于公共上游映像，例如：[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet] 或 [Node.js][base-node]。 多个应用程序映像可以共享一个通用基本映像。

当上游维护者更新 OS 或应用程序框架映像时（例如，使用关键 OS 安全修补），也必须更新基本映像以包含关键修复。 然后，还必须重新生成每个应用程序映像，以包含目前已包含在基础映像中的这些上游修复。

由于 ACR 任务在生成容器映像时会动态发现基础映像依赖项，因此，ACR 任务可以检测到应用程序映像的基础映像何时发生了更新。 预配置一个[生成任务](container-registry-tutorial-base-image-update.md#create-a-task)后，ACR 任务会**自动重新生成每个应用程序映像**。 通过这种自动检测和重新生成，ACR 任务能够节省在正常情况下手动跟踪和更新引用已更新基础映像的每个应用程序映像所需的时间和精力。

在第三篇 ACR 任务教程[更新基础映像后使用 Azure 容器注册表任务自动执行映像生成](container-registry-tutorial-base-image-update.md)中了解 OS 和框架修补。

> [!NOTE]
> 目前，基础映像更新触发器生成，仅在同时基和应用程序映像驻留在同一个 Azure 容器注册表中，或基驻留在公共 Docker 中心或 Microsoft 容器注册表存储库时。

## <a name="multi-step-tasks"></a>多步骤任务

多步骤任务提供了基于步骤的任务定义和用于生成、 测试和修补的容器映像在云中执行。 任务步骤定义各个容器映像构建和推送操作。 它们还可以定义一个或多个容器的执行，每个步骤都使用容器作为其执行环境。

例如，可以创建一个多步骤任务来自动完成以下操作：

1. 生成 Web 应用程序映像
1. 运行 Web 应用程序容器
1. 生成 Web 应用程序测试映像
1. 运行针对正在运行的应用程序容器执行测试的 Web 应用程序测试容器
1. 如果测试通过，则生成 Helm 图表存档包
1. 使用新的 Helm 图表存档包执行 `helm upgrade`

使用多步骤任务可将映像的生成、运行和测试操作拆分成组合性更强的步骤，并支持步骤间的依赖关系。 使用 ACR 任务中的多步骤任务，可以更精细地控制映像生成、测试，以及 OS 和框架修补工作流。

若要了解多步骤任务，请参阅[在 ACR 任务中运行多步骤生成、测试和修补任务](container-registry-tasks-multi-step.md)。

## <a name="view-task-logs"></a>查看任务日志

每个任务运行生成可以检查以确定是否已成功运行的任务步骤的日志输出。 如果您使用[az acr 生成](/cli/azure/acr#az-acr-build)，[运行 az acr](/cli/azure/acr#az-acr-run)，或[az acr 任务运行](/cli/azure/acr/task#az-acr-task-run)命令触发任务时，流式传输到控制台和也更高版本的存储任务运行的日志输出检索。 查看任务的日志在 Azure 门户中，运行或使用[az acr 任务日志](/cli/azure/acr/task#az-acr-task-logs)命令。

从 2019 年 7 月开始，数据和日志的注册表中的任务运行将会保留 30 天默认情况下并自动清除。 如果你想要运行的任务将数据存档，启用存档使用[az acr 任务更新运行](/cli/azure/acr/task#az-acr-task-update-run)命令。 下面的示例可帮助实现为运行任务归档*cf11*注册表中*myregistry*。

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>后续步骤

如果你已准备好自动执行操作系统和 framework 修补通过生成容器映像在云中，请查看由三部分[ACR 任务教程系列](container-registry-tutorial-quick-task.md)。

可以选择安装[适用于 Visual Studio Code 的 Docker 扩展](https://code.visualstudio.com/docs/azure/docker)以及适用于 Azure 容器注册表的 [Azure 帐户](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)扩展。 通过 Azure 容器注册表拉取和推送映像，或者运行 ACR 任务，这一切都可以在 Visual Studio Code 中进行。

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
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
