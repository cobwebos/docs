---
title: 查看任务运行日志-任务
description: 如何查看和管理 ACR 任务生成的运行日志。
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246963"
---
# <a name="view-and-manage-task-run-logs"></a>查看和管理任务运行日志

[Azure 容器注册表任务](container-registry-tasks-overview.md)中运行的每个任务都会生成日志输出，你可以通过检查这些输出来确定任务步骤是否成功运行。 

本文介绍如何查看和管理任务运行日志。

## <a name="view-streamed-logs"></a>查看流式处理日志

手动触发任务时，日志输出会直接流式传输到控制台。 例如，当你使用[az acr build](/cli/azure/acr#az-acr-build)、 [az acr run](/cli/azure/acr#az-acr-run)或[az acr task run](/cli/azure/acr/task#az-acr-task-run)命令手动触发任务时，你会看到向控制台流式传输的日志输出。 

以下示例[az acr run](/cli/azure/acr#az-acr-run)命令手动触发了一个任务，该任务运行从同一注册表中提取的容器：

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

流日志：

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>查看存储的日志 

Azure 容器注册表存储所有任务的运行日志。 可以在 Azure 门户中查看存储的运行日志。 或者，使用[az acr task logs](/cli/azure/acr/task#az-acr-task-logs)命令查看选定的日志。 默认情况下，日志保留30天。

如果自动触发任务（例如，通过源代码更新），则访问存储的日志是查看运行日志的*唯一*方法。 自动任务触发器包括源代码提交或拉取请求、基本映像更新和计时器触发器。

若要在门户中查看运行日志：

1. 导航到容器注册表。
1. 在 "**服务**" 中，选择 "**任务** > **运行**"。
1. 选择**运行 Id**以查看运行状态和运行日志。 日志包含与流式处理日志相同的信息（如果生成了日志）。

![查看任务运行登录门户](./media/container-registry-tasks-logs/portal-task-run-logs.png)

若要使用 Azure CLI 查看日志，请运行[az acr task logs](/cli/azure/acr/task#az-acr-task-logs) ，并指定运行 ID、任务名称或生成任务创建的特定映像。 如果指定任务名称，该命令将显示上次创建的运行的日志。

下面的示例输出运行 ID 为*cf4*的日志：

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>备用日志存储

你可能希望将任务运行日志存储在本地文件系统上，或使用备用存档解决方案，如 Azure 存储。

例如，创建本地*tasklogs*目录，并将[az acr task 日志](/cli/azure/acr/task#az-acr-task-logs)的输出重定向到本地文件：

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

你还可以将本地日志文件保存到 Azure 存储。 例如，使用[Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md)、 [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md)或其他方法将文件上传到存储帐户。


## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 容器注册表任务](container-registry-tasks-overview.md)的详细信息

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
