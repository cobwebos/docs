---
title: Azure 容器注册表任务示例
description: 用于生成、运行和修补容器映像的示例 Azure 容器注册表任务（ACR 任务）
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152848"
---
# <a name="acr-tasks-samples"></a>ACR 任务示例

本文详细介绍了几个[Azure 容器注册表任务](container-registry-tasks-overview.md)（ACR 任务）方案 `task.yaml` 文件和关联的 dockerfile。 

有关其他示例，请参阅[Azure 示例][task-examples]存储库。

## <a name="scenarios"></a>方案

* **生成映像** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

*  - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)中**运行容器**

* **生成并推送映像** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **构建并运行** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)的映像

* **生成并推送多个映像** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **并行生成和测试图像** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **将映像构建并推送到多个注册表** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>后续步骤

了解有关 ACR 任务的详细信息：

* [多步骤任务](container-registry-tasks-multi-step.md)-基于 ACR 任务的工作流，用于在云中构建、测试和修补容器映像。
* [任务参考](container-registry-tasks-reference-yaml.md) - 任务步骤的类型、属性和用法。
* [Cmd](https://github.com/AzureCR/cmd)存储库-用于 ACR 任务的容器集合。


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
