---
title: ACR 任务概述
description: An introduction to ACR Tasks, a suite of features in Azure Container Registry that provides secure, automated container image build, management, and patching in the cloud.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: b4710591dfd78f0633d5071c78d80e300349f498
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456157"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automate container image builds and maintenance with ACR Tasks

容器提供新的虚拟化级别，可将应用程序和开发人员依赖项与基础结构和操作要求隔离开来。 What remains, however, is the need to address how this application virtualization is managed and patched over the container lifecycle.

## <a name="what-is-acr-tasks"></a>什么是 ACR 任务？

**ACR 任务**是 Azure 容器注册表中的一个功能套件。 It provides cloud-based container image building for [platforms](#image-platforms) including Linux, Windows, and ARM, and can automate [OS and framework patching](#automate-os-and-framework-patching) for your Docker containers. ACR Tasks not only extends your "inner-loop" development cycle to the cloud with on-demand container image builds, but also enables automated builds triggered by source code updates, updates to a container's base image, or timers. For example, with base image update triggers, you can automate your OS and application framework patching workflow, maintaining secure environments while adhering to the principles of immutable containers.

## <a name="task-scenarios"></a>Task scenarios

ACR Tasks supports several scenarios to build and maintain container images and other artifacts. See the following sections in this article for details.

* **[Quick task](#quick-task)** - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. 请考虑云中的 `docker build`、`docker push`。
* **Automatically triggered tasks** - Enable one or more *triggers* to build an image:
  * **[Trigger on source code update](#trigger-task-on-source-code-update)** 
  * **[Trigger on base image update](#automate-os-and-framework-patching)** 
  * **[Trigger on a schedule](#schedule-a-task)** 
* **[Multi-step task](#multi-step-tasks)** - Extend the single image build-and-push capability of ACR Tasks with multi-step, multi-container-based workflows. 

Each ACR Task has an associated [source code context](#context-locations) - the location of a set of source files used to build a container image or other artifact. Example contexts include a Git repository or a local filesystem.

Tasks can also take advantage of [run variables](container-registry-tasks-reference-yaml.md#run-variables), so you can reuse task definitions and standardize tags for images and artifacts.

## <a name="quick-task"></a>快速任务

内部循环开发周期是指编写代码、生成和测试应用程序，然后提交到源代码管理的迭代过程，它事实上是容器生命周期管理的起点。

在你提交第一行代码之前，ACR 任务的[快速任务](container-registry-tutorial-quick-task.md)功能可以通过将容器映像生成卸载到 Azure，来提供集成式开发体验。 使用快速生成可以在提交代码之前验证自动化生成定义和捕获潜在的问题。

Using the familiar `docker build` format, the [az acr build][az-acr-build] command in the Azure CLI takes a [context](#context-locations) (the set of files to build), sends it ACR Tasks and, by default, pushes the built image to its registry upon completion.

For an introduction, see the quickstart to [build and run a container image](container-registry-quickstart-task-cli.md) in Azure Container Registry.  

ACR 任务旨在用作容器生命周期基元。 例如，将 ACR 任务集成到 CI/CD 解决方案。 By executing [az login][az-login] with a [service principal][az-login-service-principal], your CI/CD solution could then issue [az acr build][az-acr-build] commands to kick off image builds.

第一篇 ACR 任务教程[使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)中介绍了快速任务的用法。

> [!TIP]
> If you want to build and push an image directly from source code, without a Dockerfile, Azure Container Registry provides the [az acr pack build][az-acr-pack-build] command (preview). This tool builds and pushes an image from application source code using [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Trigger task on source code update

Trigger a container image build or multi-step task when code is committed, or a pull request is made or updated, to a Git repository in GitHub or Azure DevOps. For example, configure a build task with the Azure CLI command [az acr task create][az-acr-task-create] by specifying a Git repository and optionally a branch and Dockerfile. When your team updates code in the repository, an ACR Tasks-created webhook triggers a build of the container image defined in the repo. 

ACR Tasks supports the following triggers when you set a Git repo as the task's context:

| 触发器 | Enabled by default |
| ------- | ------------------ |
| 提交 | 是 |
| Pull request | No |

To configure the trigger, you provide the task a personal access token (PAT) to set the webhook in the GitHub or Azure DevOps repo.

第二篇 ACR 任务教程[使用 Azure 容器注册表任务自动执行容器映像生成](container-registry-tutorial-build-task.md)中介绍了如何在提交源代码时触发生成。

## <a name="automate-os-and-framework-patching"></a>自动执行 OS 和框架修补

真正增强容器生成工作流的 ACR 任务的强大之处在于，它能够检测基础映像的更新。 When the updated base image is pushed to your registry, or a base image is updated in a public repo such as in Docker Hub, ACR Tasks can automatically build any application images based on it.

在广义上，可将容器映像分类为基本映像和应用程序映像。 基本映像通常包括应用程序所基于的操作系统和应用程序框架，以及其他自定义项。 These base images are themselves typically based on public upstream images, for example: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], or [Node.js][base-node]. 多个应用程序映像可以共享一个通用基本映像。

当上游维护者更新 OS 或应用程序框架映像时（例如，使用关键 OS 安全修补），也必须更新基本映像以包含关键修复。 然后，还必须重新生成每个应用程序映像，以包含目前已包含在基础映像中的这些上游修复。

由于 ACR 任务在生成容器映像时会动态发现基础映像依赖项，因此，ACR 任务可以检测到应用程序映像的基础映像何时发生了更新。 预配置一个[生成任务](container-registry-tutorial-base-image-update.md#create-a-task)后，ACR 任务会**自动重新生成每个应用程序映像**。 通过这种自动检测和重新生成，ACR 任务能够节省在正常情况下手动跟踪和更新引用已更新基础映像的每个应用程序映像所需的时间和精力。

For image builds from a Dockerfile, an ACR task tracks a base image update when the base image is in one of the following locations:

* 运行任务所在的同一 Azure 容器注册表
* 同一区域中的另一个 Azure 容器注册表 
* Docker Hub 中的公共存储库
* Microsoft 容器注册表中的公共存储库

> [!NOTE]
> * The base image update trigger is enabled by default in an ACR task. 
> * Currently, ACR Tasks only tracks base image updates for application (*runtime*) images. ACR Tasks doesn't track base image updates for intermediate (*buildtime*) images used in multi-stage Dockerfiles. 

Learn more about OS and framework patching in the third ACR Tasks tutorial, [Automate image builds on base image update with Azure Container Registry Tasks](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>计划任务

Optionally schedule a task by setting up one or more *timer triggers* when you create or update the task. Scheduling a task is useful for running container workloads on a defined schedule, or running maintenance operations or tests on images pushed regularly to your registry. For details, see [Run an ACR task on a defined schedule](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>多步骤任务

Multi-step tasks provide step-based task definition and execution for building, testing, and patching container images in the cloud. Task steps defined in a [YAML file](container-registry-tasks-reference-yaml.md) specify individual build and push operations for container images or other artifacts. 它们还可以定义一个或多个容器的执行，每个步骤都使用容器作为其执行环境。

例如，可以创建一个多步骤任务来自动完成以下操作：

1. 生成 Web 应用程序映像
1. 运行 Web 应用程序容器
1. 生成 Web 应用程序测试映像
1. Run the web application test container, which performs tests against the running application container
1. 如果测试通过，则生成 Helm 图表存档包
1. 使用新的 Helm 图表存档包执行 `helm upgrade`

使用多步骤任务可将映像的生成、运行和测试操作拆分成组合性更强的步骤，并支持步骤间的依赖关系。 使用 ACR 任务中的多步骤任务，可以更精细地控制映像生成、测试，以及 OS 和框架修补工作流。

若要了解多步骤任务，请参阅[在 ACR 任务中运行多步骤生成、测试和修补任务](container-registry-tasks-multi-step.md)。

## <a name="context-locations"></a>Context locations

下表显示了 ACR 任务支持的上下文位置的几个示例：

| 上下文位置 | 描述 | 示例 |
| ---------------- | ----------- | ------- |
| 本地文件系统 | 本地文件系统上某个目录中的文件。 | `/home/user/projects/myapp` |
| GitHub 主分支 | GitHub 存储库主分支（或其他默认分支）中的文件。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存储库的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子文件夹 | GitHub 存储库中某个子文件夹内的文件。 Example shows combination of a branch and subfolder specification. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps subfolder | Files within a subfolder in an Azure repo. Example shows combination of branch and subfolder specification. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| 远程 tarball | 远程 Web 服务器上某个压缩存档中的文件。 | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Image platforms

By default, ACR Tasks builds images for the Linux OS and the amd64 architecture. Specify the `--platform` tag to build Windows images or Linux images for other architectures. Specify the OS and optionally a supported architecture in OS/architecture format (for example, `--platform Linux/arm`). For ARM architectures, optionally specify a variant in OS/architecture/variant format (for example, `--platform Linux/arm64/v8`):

| OS | 体系结构|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>View task logs

Each task run generates log output that you can inspect to determine whether the task steps ran successfully. If you use the [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run), or [az acr task run](/cli/azure/acr/task#az-acr-task-run) command to trigger the task, log output for the task run is streamed to the console and also stored for later retrieval. When a task is automatically triggered, for example by a source code commit or a base image update, task logs are only stored. View the logs for a task run in the Azure portal, or use the [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) command.

By default, data and logs for task runs in a registry are retained for 30 days and then automatically purged. If you want to archive the data for a task run, enable archiving using the [az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) command. The following example enables archiving for the task run *cf11* in registry *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>后续步骤

When you're ready to automate container image builds and maintenance in the cloud, check out the [ACR Tasks tutorial series](container-registry-tutorial-quick-task.md).

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
