---
title: 基础映像更新 - 任务
description: 了解应用程序容器映像的基础映像，并了解基础映像更新如何触发 Azure 容器注册表任务。
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 35933c4cdbbf2762f7a54bd945f8a8ffa55b9f21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85918506"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>关于 ACR 任务的基础映像更新

本文介绍了有关应用程序基础映像的更新的背景信息，并介绍了这些更新如何触发 Azure 容器注册表任务。

## <a name="what-are-base-images"></a>什么是基础映像？

定义了大部分容器映像的 Dockerfile 会指定映像所基于的父级映像，通常将父级映像称为它的基础映像  。 基础映像通常包含操作系统，例如 [Alpine Linux][base-alpine] 或 [Windows Nano Server][base-windows]，其余的容器层应用于这些操作系统上。 这些映像可能还包括应用程序框架，例如 [Node.js][base-node] 或 [.NET Core][base-dotnet]。 这些基础映像本身通常基于公共上游映像。 多个应用程序映像可以共享一个通用基本映像。

基础映像通常通过映像维护程序更新，以将 OS 或框架的新功能或改进添加进该映像中。 安全补丁是更新基础映像的另一常见原因。 当这些上游更新发生时，你还必须更新基础映像，使之包括关键修复。 然后，还必须重新生成每个应用程序映像，以包含目前已包含在基础映像中的这些上游修复。

在某些情况下（例如当有专用开发团队时），基础映像可能不止指定 OS 或框架。 例如，基础映像可以是需要跟踪的共享服务组件映像。 团队成员可能需要跟踪此基础映像以进行测试，或者在开发应用程序映像时需要定期更新该映像。

## <a name="track-base-image-updates"></a>跟踪基础映像更新

更新容器的基础映像时，ACR 任务能够自动生成映像。

在生成容器映像时，ACR 任务会动态发现基础映像依赖项。 因此，它可以检测应用程序映像的基础映像何时更新。 使用一个预配置的生成任务，ACR 任务可以自动重新生成引用基础映像的每个应用程序映像。 通过这种自动检测和重新生成，ACR 任务能够节省在正常情况下手动跟踪和更新引用已更新基础映像的每个应用程序映像所需的时间和精力。

## <a name="base-image-locations"></a>基础映像位置

对于从 Dockerfile 生成的映像，ACR 任务将在以下位置检测对基础映像的依赖关系：

* 运行任务所在的同一 Azure 容器注册表
* 同一或不同区域中的另一个专用 Azure 容器注册表 
* Docker Hub 中的公共存储库 
* Microsoft 容器注册表中的公共存储库

如果 `FROM` 语句中指定的基础映像驻留在上述某个位置，则 ACR 任务会添加一个挂钩，以确保它的基础映像更新时会重新生成该映像。

## <a name="base-image-notifications"></a>基础映像通知

更新基础映像和触发依赖任务之间间隔的时间取决于基础映像位置：

* **Docker Hub 或 MCR 的公共存储库中的基础映像** - 对于公共存储库中的基础映像，ACR 任务会按 10 到 60 分钟的随机时间间隔检查映像更新。 依赖任务会相应地运行。
* **Azure 容器注册表中的基础映像** - 对于 Azure 容器注册表中的基础映像，ACR 任务会在更新其基础映像时立即触发运行。 基础映像可以位于运行任务的 ACR 中，也可以位于任何区域的另一 ACR 中。

## <a name="additional-considerations"></a>其他注意事项

* **应用程序映像的基础映像** - 目前，ACR 任务仅跟踪应用程序（*运行时*）映像的基础映像更新。 它不跟踪多阶段 Dockerfile 中使用的中间 (buildtime) 映像的基础映像更新。  

* **默认启用** - 使用 [az acr task create][az-acr-task-create] 命令创建某个 ACR 任务时，会默认启用该任务，以便在更新基础映像时触发。 即，`base-image-trigger-enabled` 属性设置为 True。 若要在任务中禁用此行为，请将该属性更新为 False。 例如，运行以下 [az acr task update][az-acr-task-update] 命令：

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **触发依赖项跟踪** - 若要启用某个 ACR 任务来确定并跟踪容器映像的依赖项（包括其基础映像），必须先将该任务触发**至少一次**。 例如，使用 [az acr task run][az-acr-task-run] 命令手动触发该任务。

* **基础映像的稳定标记** - 若要在更新基础映像时触发任务，基础映像必须有一个稳定的标记，例如 `node:9-alpine`。 在将 OS 和框架修补到最新稳定版本时会更新的基础映像往往带有此标记。 如果使用新的版本标记更新基础映像，则不会触发任务。 有关映像标记的详细信息，请参阅[最佳做法指南](container-registry-image-tag-version.md)。 

* **其他任务触发器** - 在由基础映像更新触发的任务中，你还可以启用基于[源代码提交](container-registry-tutorial-build-task.md)或[计划](container-registry-tasks-scheduled.md)的触发器。 基础映像更新还可以触发[多步骤任务](container-registry-tasks-multi-step.md)。

## <a name="next-steps"></a>后续步骤

参阅以下教程，了解在更新基础映像后自动执行应用程序映像生成的方案：

* [在同一注册表中更新基础映像时自动执行容器映像生成](container-registry-tutorial-base-image-update.md)

* [在不同注册表中更新基础映像时自动执行容器映像生成](container-registry-tutorial-base-image-update.md)


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
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
