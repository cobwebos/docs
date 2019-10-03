---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细介绍语音到文本 helm 图表配置选项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971322"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>语音到文本 (子图表: 图表/speechToText)

若要替代 "伞" 图表, 请在任何`speechToText.`参数上添加前缀以使其更具针对性。 例如, 它将重写相应的参数 (例如, `speechToText.numberOfConcurrentRequest`重`numberOfConcurrentRequest`写)。

|参数|描述|默认|
| -- | -- | -- |
| `enabled` | **语音到文本**服务是否已启用。 | `false` |
| `numberOfConcurrentRequest` | **语音到文本**服务的并发请求数。 此图表基于该值自动计算 CPU 和内存资源。 | `2` |
| `optimizeForAudioFile`| 服务是否需要通过音频文件优化音频输入。 如果`true`为, 则此图表将为服务分配更多的 CPU 资源。 | `false` |
| `image.registry`| **语音到文本**的 docker 图像注册表。 | `containerpreview.azurecr.io` |
| `image.repository` | **语音到文本**的 docker 映像存储库。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **语音到文本**的 docker 图像标记。 | `latest` |
| `image.pullSecrets` | 用于拉取**语音到文本**docker 映像的图像密码。 | |
| `image.pullByHash`| 是否由哈希请求 docker 映像。 如果`true`为`image.hash` , 则为必需。 | `false` |
| `image.hash`| **语音到文本**docker 图像哈希。 仅在使用`image.pullByHash: true`时使用。  | |
| `image.args.eula`请求 | 指示已接受许可证。 唯一有效的值是`accept` | |
| `image.args.billing`请求 | Azure 门户的语音概述页上提供了 "计费终结点 URI" 值。 | |
| `image.args.apikey`请求 | 用于跟踪账单信息。 ||
| `service.type` | **语音到文本**服务的 Kubernetes 服务类型。 有关更多详细信息, 请参阅[Kubernetes 服务类型说明](https://kubernetes.io/docs/concepts/services-networking/service/), 并验证云提供程序支持。 | `LoadBalancer` |
| `service.port`|  **语音到文本**服务的端口。 | `80` |
| `service.annotations` | 服务元数据的**语音到文本**注释。 批注是键值对。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 是否启用[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。 如果`true`为, `speech-to-text-autoscaler`则将在 Kubernetes 群集中部署。 | `true` |
| `service.podDisruption.enabled` | 是否启用[Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果`true`为, `speech-to-text-poddisruptionbudget`则将在 Kubernetes 群集中部署。 | `true` |