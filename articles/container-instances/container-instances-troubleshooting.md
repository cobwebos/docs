---
title: "Azure 容器实例故障排除"
description: "了解如何排查 Azure 容器实例问题"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/03/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: bff594d86c7c34ebff4d7dad5be4e54cdb604baf
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---

# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>排查 Azure 容器实例的部署问题

本文展示了如何排查向 Azure 容器实例部署容器时出现的问题。 还介绍了一些可能遇到的常见问题。

## <a name="getting-diagnostic-events"></a>获取诊断事件

要查看容器内应用程序代码的日志，可使用 [az container logs](/cli/azure/container#logs) 命令。 但如果未成功部署容器，则需要查看由 Azure 容器实例资源提供程序提供的诊断信息。 要查看容器事件，请运行以下命令：

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

输出包括容器的核心属性，以及部署事件：

```bash
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
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>常见部署问题

一些常见问题导致了大部分部署错误。

### <a name="unable-to-pull-image"></a>无法请求映像

如果 Azure 容器实例最初无法请求印象，在最终失败前，它还会重试一段时间。 如果无法请求映像，会显示如下事件：

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

要解决此问题，请删除容器并重试部署，请务必键入正确的映像名称。

### <a name="container-continually-exits-and-restarts"></a>容器不断退出并重启

目前，Azure 容器实例仅支持长时间运行的服务。 如果容器运行至完成并退出，它会自动重启并再次运行。 如果发生这种情况，会显示如下事件。 请注意，容器成功启动，随后快速重启。 容器实例 API 包含一个 `retryCount` 属性，该属性会显示特定容器的重启次数。

```bash
"events": [
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:55+00:00",
    "lastTimestamp": "2017-08-03T22:23:22+00:00",
    "message": "Pulling: pulling image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:23:23+00:00",
    "message": "Pulled: Successfully pulled image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Created: Created container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Started: Started container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Created: Created container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Started: Started container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 13,
    "firstTimestamp": "2017-08-03T22:21:59+00:00",
    "lastTimestamp": "2017-08-03T22:24:36+00:00",
    "message": "BackOff: Back-off restarting failed container",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:22:13+00:00",
    "lastTimestamp": "2017-08-03T22:22:13+00:00",
    "message": "Created: Created container with id 72e347e891290e238135e4a6b3078748ca25a1275dbbff30d8d214f026d89220",
    "type": "Normal"
  },
  ...
```

> [!NOTE]
> Linux 分发的大多数容器映像会设置一个 shell（如 bash）作为默认命令。 Shell 本身不是长时间运行的服务，因此这些容器会立即退出并不断重启。

### <a name="container-takes-a-long-time-to-start"></a>容器启动时间过长

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

保持容器较小的关键是，确保最终映像不包含任何运行时不需要的内容。 执行此操作的一种方法是使用[多阶段生成](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)。 多阶段生成可轻松确保最终映像仅包含应用程序所需的项目，而不包含任何生成时需要的额外内容。

要减小容器启动时映像请求的影响，另一种方法是在希望使用 Azure 容器实例的相同区域中使用 Azure 容器注册表托管容器映像。 这会缩短容器映像需要经过的网络路径，显著缩短下载时间。
