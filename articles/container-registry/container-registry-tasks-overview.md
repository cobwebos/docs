---
title: 使用 Azure 容器注册表任务（ACR 任务）自动构建和修补容器映像
description: 介绍 ACR 任务。ACR 任务是 Azure 容器注册表中的功能套件，它在云中提供安全、自动化的容器映像的生成、管理和修补。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/05/2019
ms.author: danlep
ms.openlocfilehash: c62987031a73aa4840c1d036689a3c52fb4dc4a0
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914667"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>使用 ACR 任务自动执行容器映像的生成和维护

容器提供新的虚拟化级别，可将应用程序和开发人员依赖项与基础结构和操作要求隔离开来。 但是，另外还需要解决在容器生命周期中这种应用程序虚拟化的管理和修补方式。

## <a name="what-is-acr-tasks"></a>什么是 ACR 任务？

**ACR 任务**是 Azure 容器注册表中的一个功能套件。 它为[平台](#image-platforms)（包括 Linux、WINDOWS 和 ARM）提供基于云的容器映像，并可自动为 Docker 容器自动[修补操作系统和框架](#automate-os-and-framework-patching)。 ACR 任务不仅会将 "内部循环" 开发周期扩展到使用按需容器映像生成的云，还可以启用源代码更新触发的自动生成、容器的基本映像更新或计时器更新。 例如，对于基本映像更新触发器，可以在遵守不可变容器原则的同时，自动执行操作系统和应用程序框架修补工作流，维护安全环境。

## <a name="task-scenarios"></a>任务方案

ACR 任务支持多种方案来构建和维护容器映像和其他项目。 有关详细信息，请参阅本文中的以下部分。

* **[快速任务](#quick-task)** -生成一个容器映像并将其推送到 Azure 中的容器注册表，无需安装本地 Docker 引擎。 请考虑云中的 `docker build`、`docker push`。
* **自动触发的任务**-启用一个或多个*触发器*以生成映像：
  * **[源代码更新时触发](#trigger-task-on-source-code-update)** 
  * **[基本映像更新上的触发器](#automate-os-and-framework-patching)** 
  * **[按计划触发](#schedule-a-task)** 
* **[多步骤任务](#multi-step-tasks)** -通过多步骤、多容器的工作流扩展 ACR 任务的单一映像生成和推送功能。 

每个 ACR 任务都有一个关联的[源代码上下文](#context-locations)-一组用于构建容器映像或其他项目的源文件的位置。 示例上下文包括 Git 存储库或本地文件系统。

任务还可以利用[运行变量](container-registry-tasks-reference-yaml.md#run-variables)，因此你可以重复使用任务定义并标准化图像和项目的标记。

## <a name="quick-task"></a>快速任务

内部循环开发周期是指编写代码、生成和测试应用程序，然后提交到源代码管理的迭代过程，它事实上是容器生命周期管理的起点。

在你提交第一行代码之前，ACR 任务的[快速任务](container-registry-tutorial-quick-task.md)功能可以通过将容器映像生成卸载到 Azure，来提供集成式开发体验。 使用快速生成可以在提交代码之前验证自动化生成定义和捕获潜在的问题。

Azure CLI 中的 [az acr build][az-acr-build] 命令使用我们熟悉的 `docker build` 格式提取[上下文](#context-locations)（要生成的文件集），将其发送到 ACR 任务，并在完成后，默认将生成的映像推送到其注册表。

如需简介，请参阅在 Azure 容器注册表中[生成和运行容器映像](container-registry-quickstart-task-cli.md)的快速入门。  

ACR 任务旨在用作容器生命周期基元。 例如，将 ACR 任务集成到 CI/CD 解决方案。 然后，CI/CD 解决方案可以结合[服务主体][az-login-service-principal]执行 [az login][az-login]，发出 [az acr build][az-acr-build] 命令来启动映像生成。

第一篇 ACR 任务教程[使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)中介绍了快速任务的用法。

> [!TIP]
> 如果你想要直接从源代码生成并推送映像，而没有 Dockerfile，Azure 容器注册表将提供[az acr pack 生成][az-acr-pack-build]命令（预览版）。 此工具使用[Cloud Native Buildpacks](https://buildpacks.io/)从应用程序源代码生成并推送映像。

## <a name="trigger-task-on-source-code-update"></a>源代码更新上的触发器任务

向 GitHub 或 Azure DevOps 中的 Git 存储库提交代码或获取或更新拉取请求时，触发容器映像生成或多步骤任务。 例如，通过指定 Git 存储库以及（可选）分支和 Dockerfile，使用 Azure CLI 命令[az acr task create][az-acr-task-create]配置生成任务。 当你的团队更新存储库中的代码时，ACR 任务创建的 webhook 将触发存储库中定义的容器映像的生成。 

将 Git 存储库设置为任务的上下文时，ACR 任务支持以下触发器：

| 触发器 | 默认情况下启用 |
| ------- | ------------------ |
| 提交 | 是 |
| 拉取请求 | 否 |

若要配置触发器，请为任务提供个人访问令牌（PAT），以便在 GitHub 或 Azure DevOps 存储库中设置 webhook。

第二篇 ACR 任务教程[使用 Azure 容器注册表任务自动执行容器映像生成](container-registry-tutorial-build-task.md)中介绍了如何在提交源代码时触发生成。

## <a name="automate-os-and-framework-patching"></a>自动执行 OS 和框架修补

真正增强容器生成工作流的 ACR 任务的强大之处在于，它能够检测基础映像的更新。 将更新的基础映像将推送到注册表时，或者在公共存储库（例如 Docker Hub）中更新基础映像时，ACR 任务可以基于该映像自动生成任何应用程序映像。

在广义上，可将容器映像分类为基本映像和应用程序映像。 基本映像通常包括应用程序所基于的操作系统和应用程序框架，以及其他自定义项。 这些基础映像本身通常基于公共上游映像，例如：[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet] 或 [Node.js][base-node]。 多个应用程序映像可以共享一个通用基本映像。

当上游维护者更新 OS 或应用程序框架映像时（例如，使用关键 OS 安全修补），也必须更新基本映像以包含关键修复。 然后，还必须重新生成每个应用程序映像，以包含目前已包含在基础映像中的这些上游修复。

由于 ACR 任务在生成容器映像时会动态发现基础映像依赖项，因此，ACR 任务可以检测到应用程序映像的基础映像何时发生了更新。 预配置一个[生成任务](container-registry-tutorial-base-image-update.md#create-a-task)后，ACR 任务会**自动重新生成每个应用程序映像**。 通过这种自动检测和重新生成，ACR 任务能够节省在正常情况下手动跟踪和更新引用已更新基础映像的每个应用程序映像所需的时间和精力。

对于从 Dockerfile 生成的映像，ACR 任务会在基本映像位于以下位置之一时跟踪基本映像更新：

* 运行任务所在的同一 Azure 容器注册表
* 同一区域中的另一个 Azure 容器注册表 
* Docker Hub 中的公共存储库
* Microsoft 容器注册表中的公共存储库

> [!NOTE]
> * 默认情况下，在 ACR 任务中启用基本映像更新触发器。 
> * 目前，ACR 任务只跟踪应用程序（*运行时*）映像的基本图像更新。 ACR 任务不跟踪多阶段 Dockerfile 中使用的中间（*buildtime*）映像的基本映像更新。 

在第三个 ACR 任务教程中了解有关 OS 和 framework 修补的详细信息，请参阅[通过 Azure 容器注册表任务对基础映像更新进行自动构建映像](container-registry-tutorial-base-image-update.md)。

## <a name="schedule-a-task"></a>计划任务

还可以选择在创建或更新任务时设置一个或多个*计时器触发器*来计划任务。 计划任务适用于按定义的计划运行容器工作负荷，或对定期推送到注册表中的映像运行维护操作或测试。 有关详细信息，请参阅[按定义的计划运行 ACR 任务](container-registry-tasks-scheduled.md)。

## <a name="multi-step-tasks"></a>多步骤任务

多步骤任务提供用于在云中构建、测试和修补容器映像的基于步骤的任务定义和执行。 [YAML 文件](container-registry-tasks-reference-yaml.md)中定义的任务步骤指定容器映像或其他项目的单独生成和推送操作。 它们还可以定义一个或多个容器的执行，每个步骤都使用容器作为其执行环境。

例如，可以创建一个多步骤任务来自动完成以下操作：

1. 生成 Web 应用程序映像
1. 运行 Web 应用程序容器
1. 生成 Web 应用程序测试映像
1. 运行 web 应用程序测试容器，该容器对正在运行的应用程序容器执行测试
1. 如果测试通过，则生成 Helm 图表存档包
1. 使用新的 Helm 图表存档包执行 `helm upgrade`

使用多步骤任务可将映像的生成、运行和测试操作拆分成组合性更强的步骤，并支持步骤间的依赖关系。 使用 ACR 任务中的多步骤任务，可以更精细地控制映像生成、测试，以及 OS 和框架修补工作流。

若要了解多步骤任务，请参阅[在 ACR 任务中运行多步骤生成、测试和修补任务](container-registry-tasks-multi-step.md)。

## <a name="context-locations"></a>上下文位置

下表显示了 ACR 任务支持的上下文位置的几个示例：

| 上下文位置 | 描述 | 示例 |
| ---------------- | ----------- | ------- |
| 本地文件系统 | 本地文件系统上某个目录中的文件。 | `/home/user/projects/myapp` |
| GitHub 主分支 | GitHub 存储库主分支（或其他默认分支）中的文件。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存储库的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子文件夹 | GitHub 存储库中某个子文件夹内的文件。 示例显示了分支和子文件夹规范的组合。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| 远程 tarball | 远程 Web 服务器上某个压缩存档中的文件。 | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>映像平台

默认情况下，ACR 任务为 Linux OS 和 amd64 体系结构生成映像。 `--platform`指定标记以便为其他体系结构生成 Windows 映像或 Linux 映像。 指定操作系统，并根据需要指定支持的体系结构格式（例如`--platform Linux/arm`）。 对于 ARM 体系结构，可以选择指定 OS/体系结构/变体格式的变体`--platform Linux/arm64/v8`（例如）：

| OS | 体系结构|
| --- | ------- | 
| Linux | amd64<br/>单臂<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>查看任务日志

每个任务运行都会生成日志输出，检查该输出即可确定任务步骤是否已成功运行。 如果使用 [az acr build](/cli/azure/acr#az-acr-build)、[az acr run](/cli/azure/acr#az-acr-run) 或 [az acr task run](/cli/azure/acr/task#az-acr-task-run) 命令来触发任务，则任务运行的日志输出会流式传输到控制台，并且还会进行存储，供以后检索。 当自动触发任务（例如，通过源代码提交或基本映像更新）时，仅存储任务日志。 在 Azure 门户中查看某个任务运行的日志，或者使用 [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) 命令。

默认情况下，在注册表中运行的任务的数据和日志将保留30天，然后自动清除。 若要将任务运行的数据存档，请使用 [az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) 命令启用存档功能。 以下示例在注册表 *myregistry* 中为任务运行 *cf11* 启用存档功能。

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>后续步骤

如果已准备好在云中自动生成和维护容器映像，请查看[ACR 任务教程系列](container-registry-tutorial-quick-task.md)。

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
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
