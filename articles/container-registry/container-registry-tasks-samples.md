---
title: ACR 任务示例
description: 用于生成、运行和修补容器映像的示例 Azure 容器注册表任务（ACR 任务）
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74456081"
---
# <a name="acr-tasks-samples"></a>ACR 任务示例

本文链接到几个 [Azure 容器注册表任务](container-registry-tasks-overview.md)（ACR 任务）方案的示例 `task.yaml` 文件和关联的 Dockerfile。 

有关其他示例，请参阅 [Azure 示例][task-examples]存储库。

## <a name="scenarios"></a>方案

* **生成映像** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **运行容器** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **生成和推送映像** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **生成和运行映像** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **生成和推送多个映像** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **并行生成和测试映像** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **生成映像并将其推送到多个注册表** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>后续步骤

详细了解 ACR 任务：

* [多步骤任务](container-registry-tasks-multi-step.md) - 基于 ACR 任务的工作流，用于在云中生成、测试和修补容器映像。
* [任务参考](container-registry-tasks-reference-yaml.md) - 任务步骤的类型、属性和用法。
* [命令存储库](https://github.com/AzureCR/cmd) - 作为 ACR 任务命令的容器的集合。


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
