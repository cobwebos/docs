---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细信息的文本到语音转换 helm 图表配置选项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711490"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>文本到语音转换 (子图表： 图表/textToSpeech)

若要重写"涵盖性"图表，请添加前缀`textToSpeech.`任何参数，以使其更加明确。 例如，它将如替代的相应参数`textToSpeech.numberOfConcurrentRequest`重写`numberOfConcurrentRequest`。

|参数|描述|默认|
| -- | -- | -- |
| `enabled` | 是否**文本到语音转换**服务已启用。 | `false` |
| `numberOfConcurrentRequest` | 并发请求数**文本到语音转换**服务。 此图表自动计算基于此值的 CPU 和内存资源。 | `2` |
| `optimizeForTurboMode`| 是否需要优化的文本输入文本文件通过该服务。 如果`true`，此图表将分配到服务的更多 CPU 资源。 | `false` |
| `image.registry`| **文本到语音转换**docker 映像注册表。 | `containerpreview.azurecr.io` |
| `image.repository` | **文本到语音转换**docker 映像存储库。 | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **文本到语音转换**docker 映像标记。 | `latest` |
| `image.pullSecrets` | 提取映像机密**文本到语音转换**docker 映像。 | |
| `image.pullByHash`| 是否由哈希拉取 docker 映像。 如果`true`，`image.hash`是必需的。 | `false` |
| `image.hash`| **文本到语音转换**docker 映像哈希。 仅当使用`image.pullByHash: true`。  | |
| `image.args.eula` （必需） | 指示已接受许可证。 唯一有效的值是 `accept` | |
| `image.args.billing` （必需） | 计费终结点 URI 值是可在 Azure 门户的语音概述页上。 | |
| `image.args.apikey` （必需） | 用于跟踪账单信息。 ||
| `service.type` | Kubernetes 服务的类型**文本到语音转换**服务。 请参阅[Kubernetes 服务类型说明](https://kubernetes.io/docs/concepts/services-networking/service/)的更多详细信息，并验证云提供程序支持。 | `LoadBalancer` |
| `service.port`|  端口**文本到语音转换**服务。 | `80` |
| `service.autoScaler.enabled` | 是否[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)已启用。 如果`true`，则`text-to-speech-autoscaler`将 Kubernetes 群集中部署。 | `true` |
| `service.podDisruption.enabled` | 是否[Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)已启用。 如果`true`，则`text-to-speech-poddisruptionbudget`将 Kubernetes 群集中部署。 | `true` |