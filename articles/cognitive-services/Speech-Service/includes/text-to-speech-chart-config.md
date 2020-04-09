---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细介绍了文本转语音 Helm 图配置选项。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874326"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>文本转语音（子图：charts/textToSpeech）

若要替代“伞形”图，请在任何参数上添加前缀 `textToSpeech.`，以使其更具体。 例如，它将替代相应参数，例如，`textToSpeech.numberOfConcurrentRequest` 替代 `numberOfConcurrentRequest`。

|参数|描述|默认|
| -- | -- | -- |
| `enabled` | 是否启用**文本到语音转换**服务。 | `false` |
| `numberOfConcurrentRequest` | **文本转语音**服务的并发请求数。 此图表基于该值自动计算 CPU 和内存资源。 | `2` |
| `optimizeForTurboMode`| 服务是否需要针对通过文本文件输入的文本进行优化。 如果为 `true`，此图表将为服务分配更多的 CPU 资源。 | `false` |
| `image.registry`| **文本转语音** docker 映像注册表。 | `containerpreview.azurecr.io` |
| `image.repository` | **文本转语音** docker 映像存储库。 | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **文本转语音** docker 映像标记。 | `latest` |
| `image.pullSecrets` | 用于拉取**文本到语音** docker 映像的映像机密。 | |
| `image.pullByHash`| 是否通过哈希方法拉取该 docker 映像。 如果为 `true`，则需要 `image.hash`。 | `false` |
| `image.hash`| **文本转语音** docker 映像哈希。 仅当 `image.pullByHash: true` 时使用。  | |
| `image.args.eula`（必需） | 指示已接受许可证。 唯一有效的值是 `accept` | |
| `image.args.billing`（必需） | 可以在 Azure 门户的“语音概述”页上获取计费终结点 URI 值。 | |
| `image.args.apikey`（必需） | 用于跟踪账单信息。 ||
| `service.type` | **文本转语音**服务的 Kubernetes 服务类型。 请参阅 [Kubernetes 服务类型说明](https://kubernetes.io/docs/concepts/services-networking/service/)了解详细信息并验证云服务提供商支持。 | `LoadBalancer` |
| `service.port`|  **文本转语音**服务的端口。 | `80` |
| `service.annotations` | 服务元数据的**文本转语音**批注。 批注是键值对。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 是否启用了[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。 如果为 `true`，则 `text-to-speech-autoscaler` 将部署在 Kubernetes 群集中。 | `true` |
| `service.podDisruption.enabled` | 是否启用了 [Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果为 `true`，则 `text-to-speech-poddisruptionbudget` 将部署在 Kubernetes 群集中。 | `true` |