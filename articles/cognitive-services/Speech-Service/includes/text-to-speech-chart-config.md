---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细说明文本到语音 helm 图表配置选项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717244"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>文本到语音转换 (子图表: 图表/textToSpeech)

若要替代 "伞" 图表, 请在任何`textToSpeech.`参数上添加前缀以使其更具针对性。 例如, 它将重写相应的参数, 如`textToSpeech.numberOfConcurrentRequest`重`numberOfConcurrentRequest`写。

|参数|描述|默认|
| -- | -- | -- |
| `enabled` | **文本到语音**服务是否已启用。 | `false` |
| `numberOfConcurrentRequest` | **文本到语音**服务的并发请求数。 此图表基于该值自动计算 CPU 和内存资源。 | `2` |
| `optimizeForTurboMode`| 服务是否需要通过文本文件优化文本输入。 如果`true`为, 则此图表将为服务分配更多的 CPU 资源。 | `false` |
| `image.registry`| **文本到语音的**docker 图像注册表。 | `containerpreview.azurecr.io` |
| `image.repository` | **文本到语音**的 docker 图像存储库。 | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **文本到语音的**docker 图像标记。 | `latest` |
| `image.pullSecrets` | 用于拉取**文本到语音**的 docker 映像的图像密码。 | |
| `image.pullByHash`| 是否由哈希请求 docker 映像。 如果`true`为`image.hash` , 则为必需。 | `false` |
| `image.hash`| **文本到语音**的 docker 图像哈希。 仅在使用`image.pullByHash: true`时使用。  | |
| `image.args.eula`请求 | 指示已接受许可证。 唯一有效的值是`accept` | |
| `image.args.billing`请求 | Azure 门户的语音概述页上提供了 "计费终结点 URI" 值。 | |
| `image.args.apikey`请求 | 用于跟踪账单信息。 ||
| `service.type` | **文本到语音**服务的 Kubernetes 服务类型。 有关更多详细信息, 请参阅[Kubernetes 服务类型说明](https://kubernetes.io/docs/concepts/services-networking/service/), 并验证云提供程序支持。 | `LoadBalancer` |
| `service.port`|  **文本到语音**服务的端口。 | `80` |
| `service.autoScaler.enabled` | 是否启用[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。 如果`true`为, `text-to-speech-autoscaler`则将在 Kubernetes 群集中部署。 | `true` |
| `service.podDisruption.enabled` | 是否启用[Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果`true`为, `text-to-speech-poddisruptionbudget`则将在 Kubernetes 群集中部署。 | `true` |