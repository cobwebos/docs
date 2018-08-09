---
title: Azure 容器实例故障排除
description: 了解如何排查 Azure 容器实例问题
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6f57bc41cddc997a69f92ba4e8ca66faaeb29738
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424596"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>排查 Azure 容器实例中的常见问题

本文展示了如何排查管理容器或向 Azure 容器实例部署容器时出现的常见问题。

## <a name="naming-conventions"></a>命名约定

定义容器规格时，某些参数需要遵循命名限制。 下表包含容器组属性的特定要求。 有关 Azure 命名约定的详细信息，请参阅 Azure 体系结构中心中的[命名约定][azure-name-restrictions]。

| 范围 | Length | 大小写 | 有效的字符 | 建议的模式 | 示例 |
| --- | --- | --- | --- | --- | --- | --- |
| 容器组名称 | 1-64 |不区分大小写 |第一个或最后一个字符不能为字母数字和连字符 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 容器名称 | 1-64 |不区分大小写 |第一个或最后一个字符不能为字母数字和连字符 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 容器端口 | 介于 1 和 65535 之间 |Integer |一个介于 1 和 65535 之间的整数 |`<port-number>` |`443` |
| DNS 名称标签 | 5-63 |不区分大小写 |第一个或最后一个字符不能为字母数字和连字符 |`<name>` |`frontend-site1` |
| 环境变量 | 1-63 |不区分大小写 |第一个或最后一个字符不能为字母数字和下划线 (_) |`<name>` |`MY_VARIABLE` |
| 卷名 | 5-63 |不区分大小写 |第一个或最后一个字符不能为小写字母、数字和连字符。 不能包含两个连续的连字符。 |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>不受支持的映像的操作系统版本

如果指定了 Azure 容器实例不支持的映像，则将返回 `OsVersionNotSupported` 错误。 该错误类似于以下内容，其中 `{0}` 是你尝试部署的映像的名称：

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

在部署基于半年频道 (SAC) 版本的 Windows 映像时，通常会遇到此错误。 例如，Windows 版本 1709 和 1803 是 SAC 版本，在部署时会生成此错误。

Azure 容器实例支持仅基于长期服务频道 (LTSC) 版本的 Windows 映像。 若要缓解此问题，部署 Windows 容器时，请始终部署基于 LTSC 的映像。

有关 Windows 的 LTSC 和 SAC 版本的详细信息，请参阅 [Windows Server 半年频道概述][windows-sac-overview]。

## <a name="unable-to-pull-image"></a>无法请求映像

如果 Azure 容器实例最初无法请求映像，则会重试一段时间。 如果映像请求操作继续失败，ACI 最终会使部署失败，可能会显示 `Failed to pull image` 错误。

若要解决此问题，请删除容器实例，然后重试部署。 请确保映像存在于注册表中，并且你已正确键入映像名称。

如果无法请求映像，[az container show][az-container-show] 的输出会显示如下事件：

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

## <a name="container-continually-exits-and-restarts"></a>容器不断退出并重启

如果容器运行直至完成并自动重启，可能需要将[重启策略](container-instances-restart-policy.md)设置为“失败时”或“从不”。 如果指定了“失败时”，但仍不断重启，则可能容器中执行的应用程序或脚本存在问题。

容器实例 API 包括 `restartCount` 属性。 若要检查容器的重启次数，可在 Azure CLI 中使用 [az container show][az-container-show] 命令。 在以下示例输出中（为简洁起见已将其截断），可以在输出末尾看到 `restartCount` 属性。

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

影响 Azure 容器实例中的容器启动时间的两个主要因素是：

* [映像大小](#image-size)
* [映像位置](#image-location)

Windows 映像具有[其他注意事项](#cached-windows-images)。

### <a name="image-size"></a>映像大小

如果容器启动时间过长，但最终成功启动，请先查看容器映像大小。 由于 Azure 容器实例按需请求容器映像，因此显示的启动时间与映像大小直接相关。

可在 Docker CLI 中使用 `docker images` 命令查看容器映像大小：

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

保持容器较小的关键是，确保最终映像不包含任何运行时不需要的内容。 执行此操作的一种方法是使用[多阶段生成][docker-multi-stage-builds]。 多阶段生成可轻松确保最终映像仅包含应用程序所需的项目，而不包含任何生成时需要的额外内容。

### <a name="image-location"></a>映像位置

若要减小映像请求对容器启动时间的影响，另一种方法是在希望部署容器实例的同一区域的 [Azure 容器注册表](/azure/container-registry/)中托管容器映像。 这会缩短容器映像需要经过的网络路径，显著缩短下载时间。

### <a name="cached-windows-images"></a>缓存的 Windows 映像

对于基于某些 Windows 映像的映像，Azure 容器实例使用一种缓存机制来帮助加快容器启动时间。

若要确保最快的 Windows 容器启动时间，请使用以下**两个映像**的**三个最新**版本之一作为基础映像：

* [Windows Server 2016][docker-hub-windows-core]（仅限 LTS）
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows 容器慢速网络准备情况

Windows 容器在初始创建后可能最多 5 秒内没有入站或出站连接。 初始设置后，容器网络应适当恢复。

## <a name="resource-not-available-error"></a>资源不可用错误

由于 Azure 中的区域资源负载不同，尝试部署容器实例时可能会收到以下错误：

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

此错误指示由于尝试部署的区域中负载较重，无法在此时为容器分配指定的资源。 使用以下一个或多个缓解步骤来帮助解决此问题。

* 验证容器部署设置是否位于 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md#region-availability)中定义的参数内
* 为容器指定较低的 CPU 和内存设置
* 部署到其他 Azure 区域
* 稍后部署

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>无法连接到基础 Docker API 或运行特权容器

Azure 容器实例不公开对托管容器组的底层基础结构的直接访问。 这包括访问运行在容器主机上的 Docker API 和运行特权容器。 如果需要 Docker 交互，请查看 [REST 参考文档](https://aka.ms/aci/rest)以了解 ACI API 支持的内容。 如果缺少某些内容，请在 [ACI 反馈论坛](https://aka.ms/aci/feedback)上提交请求。

## <a name="next-steps"></a>后续步骤
了解如何[检索容器日志和事件](container-instances-get-logs.md)来帮助调试你的容器。

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
