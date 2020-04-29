---
title: 安装语音容器
titleSuffix: Azure Cognitive Services
description: 详细介绍语音到文本 helm 图表配置选项。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422041"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>语音到文本（子图表：图表/speechToText）

若要替代“伞形”图，请在任何参数上添加前缀 `speechToText.`，以使其更具体。 例如，它将替代相应参数，例如，`speechToText.numberOfConcurrentRequest` 替代 `numberOfConcurrentRequest`。

|参数|说明|默认|
| -- | -- | -- |
| `enabled` | **语音到文本**服务是否已启用。 | `false` |
| `numberOfConcurrentRequest` | **语音到文本**服务的并发请求数。 此图表基于该值自动计算 CPU 和内存资源。 | `2` |
| `optimizeForAudioFile`| 服务是否需要通过音频文件优化音频输入。 如果为 `true`，此图表将为服务分配更多的 CPU 资源。 | `false` |
| `image.registry`| **语音到文本**的 docker 图像注册表。 | `containerpreview.azurecr.io` |
| `image.repository` | **语音到文本**的 docker 映像存储库。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **语音到文本**的 docker 图像标记。 | `latest` |
| `image.pullSecrets` | 用于拉取**语音到文本**docker 映像的图像密码。 | |
| `image.pullByHash`| 是否通过哈希方法拉取该 docker 映像。 如果为 `true`，则需要 `image.hash`。 | `false` |
| `image.hash`| **语音到文本**docker 图像哈希。 仅当 `image.pullByHash: true` 时使用。  | |
| `image.args.eula`（必需） | 指示已接受许可证。 唯一有效的值是 `accept` | |
| `image.args.billing`（必需） | 可以在 Azure 门户的“语音概述”页上获取计费终结点 URI 值。 | |
| `image.args.apikey`（必需） | 用于跟踪账单信息。 ||
| `service.type` | **语音到文本**服务的 Kubernetes 服务类型。 请参阅 [Kubernetes 服务类型说明](https://kubernetes.io/docs/concepts/services-networking/service/)了解详细信息并验证云服务提供商支持。 | `LoadBalancer` |
| `service.port`|  **语音到文本**服务的端口。 | `80` |
| `service.annotations` | 服务元数据的**语音到文本**注释。 批注是键值对。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 是否启用了[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。 如果为 `true`，则 `speech-to-text-autoscaler` 将部署在 Kubernetes 群集中。 | `true` |
| `service.podDisruption.enabled` | 是否启用了 [Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果为 `true`，则 `speech-to-text-poddisruptionbudget` 将部署在 Kubernetes 群集中。 | `true` |