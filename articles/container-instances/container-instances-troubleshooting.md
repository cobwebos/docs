---
title: "Azure 容器实例故障排除"
description: "了解如何排查 Azure 容器实例问题"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 1fd3b2c251860e883519744b11fcfc2b925cd2fa
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>排查 Azure 容器实例的部署问题

本文展示了如何排查向 Azure 容器实例部署容器时出现的问题。 还介绍了一些可能会遇到的常见问题。

## <a name="get-diagnostic-events"></a>获取诊断事件

若要查看容器内应用程序代码的日志，可使用 [az container logs][az-container-logs] 命令。 但如果未成功部署容器，则需要查看由 Azure 容器实例资源提供程序提供的诊断信息。 若要查看容器事件，请运行 [az container show][az-container-show] 命令：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

输出包括容器的核心属性，以及部署事件（此处截断显示）：

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>常见部署问题

一些常见问题导致了大部分部署错误。

## <a name="unable-to-pull-image"></a>无法请求映像

如果 Azure 容器实例最初无法请求印象，在最终失败前，它还会重试一段时间。 如果无法请求映像，[az container show][az-container-show] 的输出会显示如下事件：

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

要解决此问题，请删除容器并重试部署，请务必键入正确的映像名称。

## <a name="container-continually-exits-and-restarts"></a>容器不断退出并重启

如果容器运行直至完成并自动重启，可能需要将[重启策略](container-instances-restart-policy.md)设置为“失败时”或“从不”。 如果指定了“失败时”，但仍不断重启，则可能容器中执行的应用程序或脚本存在问题。

容器实例 API 包括 `restartCount` 属性。 若要检查容器的重启次数，可在 Azure CLI 2.0 中使用 [az container show][az-container-show] 命令。 在以下示例输出中（为简洁起见已将其截断），可以在输出末尾看到 `restartCount` 属性。

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 分发的大多数容器映像会设置一个 shell（如 bash）作为默认命令。 由于 Shell 本身不是长时间运行的服务，因此如果这些容器配置了“始终”重启策略，会立即退出并不断重启。

## <a name="container-takes-a-long-time-to-start"></a>容器启动时间过长

如果容器启动时间过长，但最终成功启动，请先查看容器映像大小。 Azure 容器实例按需请求容器映像，因此启动时间与映像大小直接相关。

可使用 Docker CLI 查看容器映像大小：

```bash
docker images
```

输出：

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

保持容器较小的关键是，确保最终映像不包含任何运行时不需要的内容。 执行此操作的一种方法是使用[多阶段生成][docker-multi-stage-builds]。 多阶段生成可轻松确保最终映像仅包含应用程序所需的项目，而不包含任何生成时需要的额外内容。

要减小容器启动时映像请求的影响，另一种方法是在希望使用 Azure 容器实例的相同区域中使用 Azure 容器注册表托管容器映像。 这会缩短容器映像需要经过的网络路径，显著缩短下载时间。

## <a name="resource-not-available-error"></a>资源不可用错误

由于 Azure 中的区域资源负载不同，尝试部署容器实例时可能会收到以下错误：

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

此错误指示由于尝试部署的区域中负载较重，无法在此时为容器分配指定的资源。 使用以下一个或多个缓解步骤来帮助解决此问题。

* 验证容器部署设置是否位于 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md#region-availability)中定义的参数内
* 为容器指定较低的 CPU 和内存设置
* 部署到其他 Azure 区域
* 稍后部署

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/

<!-- LINKS - Internal -->
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show