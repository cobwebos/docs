---
title: 使用 Azure Container Registry Build (ACR Build) 自动执行 OS 和框架修补
description: 介绍 ACR Build。ACR Build 是 Azure 容器注册表中的功能套件，它在云中提供安全、自动化的容器映像生成和修补。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 05/01/2018
ms.author: marsma
ms.openlocfilehash: 3ef91270bceb5865bdbdf9c436e4519595a3dc09
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057768"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>使用 ACR Build 自动执行 OS 和框架修补

容器提供新的虚拟化级别，可将应用程序和开发人员依赖项与基础结构和操作要求隔离开来。 但是，另外还需要解决这种应用程序虚拟化的修补方式。

**ACR Build** 是 Azure 容器注册表中的功能套件，它不仅提供本机容器映像生成功能，而且还能自动为 Docker 容器执行 [OS 和框架修补](#automate-os-and-framework-patching)。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>什么是 ACR Build？

Azure Container Registry Build 是 Azure 原生的容器映像生成服务。 借助 ACR Build，可以使用按需容器映像版本在云中实现内部循环开发，并在提交源代码和更新基本映像时实现自动生成。

将代码提交到 Git 存储库或更新容器的基本映像时，会自动触发容器映像生成。 使用基本映像更新触发器，可以自动执行 OS 和应用程序框架修补工作流，在遵守不可变容器原则的前提下维护安全的环境。

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>快速生成：内部循环扩展到云

在开发人员提交第一行代码之前，生命周期管理就已开始。 使用 ACR Build 的[快速生成](container-registry-tutorial-quick-build.md)功能可以实现集成式本地内部循环开发体验，并将生成任务卸载到 Azure。 使用“快速生成”可以在提交代码验证自动化生成定义。

Azure CLI 中的 [az acr build][az-acr-build] 命令使用我们熟悉的 `docker build` 格式，它会提取本地上下文，将其发送到 ACR Build 服务，并在完成后，默认将生成的映像推送到其注册表。 ACR Build 遵循异地复制的注册表，可让分散在各地的开发团队利用最靠近的复制注册表。 在预览期，ACR Build 只能在美国东部和西欧使用。

ACR Build 旨在用作容器生命周期基元。 例如，将 ACR Build 集成到 CI/CD 解决方案。 然后，CI/CD 解决方案可以结合[服务主体][az-login-service-principal]执行 [az login][az-login]，发出 [az acr build][az-acr-build] 命令来启动映像生成。

第一篇 ACR Build 教程[使用 Azure Container Registry Build 在云中生成容器映像](container-registry-tutorial-quick-build.md)中介绍了“快速生成”的用法。

## <a name="automatic-build-on-source-code-commit"></a>提交源代码时自动生成

将代码提交到 Git 存储库后，使用 ACR Build 自动触发容器映像生成。 使用生成任务（可使用 Azure CLI 命令 [az acr build-task][az-acr-build-task] 进行配置）可以指定 Git 存储库，并选择性地指定分支和 Docker 文件。 当团队将代码提交到存储库时，ACR Build 创建的 Webhook 会触发存储库中定义的容器映像的生成。

第二篇 ACR Build 教程[使用 Azure Container Registry Build 自动执行容器映像生成](container-registry-tutorial-build-task.md)中介绍了如何在提交源代码时触发生成。

## <a name="automate-os-and-framework-patching"></a>自动执行 OS 和框架修补

真正增强容器生成管道的 ACR Build 的强大之处在于，它能够检测基本映像的更新。 将更新的基本映像将推送到注册表时，ACR Build 可以基于该映像自动生成任何应用程序映像。

在广义上，可将容器映像分类为基本映像和应用程序映像。 基本映像通常包括应用程序所基于的操作系统和应用程序框架，以及其他自定义项。 这些基本映像本身通常基于公共上游映像，例如 [Alpine Linux][base-alpine] 或 [Node.js][base-node]。 多个应用程序映像可以共享一个通用基本映像。

当上游维护者更新 OS 或应用程序框架映像时（例如，使用关键 OS 安全修补），也必须更新基本映像以包含关键修复。 然后，还必须重新生成每个应用程序映像，以包含目前已包含在基础映像中的这些上游修复。

由于 ACR Build 在生成容器映像时会动态发现基本映像依赖项，因此，ACR Build 可以检测到应用程序映像的基本映像何时发生了更新。 预配置一个[生成任务](container-registry-tutorial-base-image-update.md#create-build-task)后，ACR Build 会**自动重新生成每个应用程序映像**。 通过这种自动检测和重新生成，ACR Build 能够节省在正常情况下手动跟踪和更新引用已更新基本映像的每个应用程序映像所需的时间和精力。

在第三篇 ACR Build 教程[更新基本映像后使用 Azure Container Registry Build 自动执行映像生成](container-registry-tutorial-base-image-update.md)中了解 OS 和框架修补。

> [!NOTE]
> 在初始预览版中，仅当基本映像和应用程序映像驻留在同一 Azure 容器注册表中时，基本映像更新才会触发生成。

## <a name="next-steps"></a>后续步骤

如果已准备好通过在云中生成容器映像来自动执行 OS 和框架修补，请查看由三篇文章构成的 ACR Build 系列教程。

> [!div class="nextstepaction"]
> [使用 Azure Container Registry Build 在云中生成容器映像](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
