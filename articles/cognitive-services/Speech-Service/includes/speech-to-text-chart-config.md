---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细介绍了语音到文本 helm 图表配置选项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717229"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>语音到文本 (子图表： 图表/speechToText)

若要重写"涵盖性"图表，请添加前缀`speechToText.`任何参数，以使其更加明确。 例如，它将如替代的相应参数`speechToText.numberOfConcurrentRequest`重写`numberOfConcurrentRequest`。

|参数|描述|默认|
| -- | -- | -- |
| `enabled` | 是否**语音到文本**服务已启用。 | `false` |
| `numberOfConcurrentRequest` | 并发请求数**语音到文本**服务。 此图表自动计算基于此值的 CPU 和内存资源。 | `2` |
| `optimizeForAudioFile`| 是否需要优化通过音频文件的音频输入该服务。 如果`true`，此图表将分配到服务的更多 CPU 资源。 | `false` |
| `image.registry`| **语音到文本**docker 映像注册表。 | `containerpreview.azurecr.io` |
| `image.repository` | **语音到文本**docker 映像存储库。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **语音到文本**docker 映像标记。 | `latest` |
| `image.pullSecrets` | 提取映像机密**语音到文本**docker 映像。 | |
| `image.pullByHash`| 是否由哈希拉取 docker 映像。 如果`true`，`image.hash`是必需的。 | `false` |
| `image.hash`| **语音到文本**docker 映像哈希。 仅当使用`image.pullByHash: true`。  | |
| `image.args.eula` （必需） | 指示已接受许可证。 唯一有效的值是 `accept` | |
| `image.args.billing` （必需） | 计费终结点 URI 值是可在 Azure 门户的语音概述页上。 | |
| `image.args.apikey` （必需） | 用于跟踪账单信息。 ||
| `service.type` | Kubernetes 服务的类型**语音到文本**服务。 请参阅[Kubernetes 服务类型说明](https://kubernetes.io/docs/concepts/services-networking/service/)的更多详细信息，并验证云提供程序支持。 | `LoadBalancer` |
| `service.port`|  端口**语音到文本**服务。 | `80` |
| `service.autoScaler.enabled` | 是否[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)已启用。 如果`true`，则`speech-to-text-autoscaler`将 Kubernetes 群集中部署。 | `true` |
| `service.podDisruption.enabled` | 是否[Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)已启用。 如果`true`，则`speech-to-text-poddisruptionbudget`将 Kubernetes 群集中部署。 | `true` |