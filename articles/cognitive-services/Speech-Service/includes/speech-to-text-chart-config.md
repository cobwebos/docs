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
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876034"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>语音到文本（子图表：图表/speechToText）

若要替代“伞形”图，请在任何参数上添加前缀 `speechToText.`，以使其更具体。 例如，它将替代相应参数，例如，`speechToText.numberOfConcurrentRequest` 替代 `numberOfConcurrentRequest`。

|参数|描述|默认|
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

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>情绪分析（子图表：图表/speechToText）

从2.2.0 容器的 v 和 Helm 图表的 v 为0.2.0 开始，使用文本分析 API 将以下参数用于情绪分析。

|参数|说明|值|默认|
| --- | --- | --- | --- |
|`textanalytics.enabled`| **文本分析**服务是否已启用| true/false| `false`|
|`textanalytics.image.registry`| **文本分析**docker 映像注册表| 有效的 docker 映像注册表| |
|`textanalytics.image.repository`| **文本分析**docker 映像存储库| 有效的 docker 映像存储库| |
|`textanalytics.image.tag`| **文本分析**docker 图像标记| 有效的 docker 映像标记| |
|`textanalytics.image.pullSecrets`| 用于请求**文本分析**docker 映像的图像机密| 有效的机密名称| |
|`textanalytics.image.pullByHash`| 指定是否通过哈希拉取 docker 映像。  如果`yes`还`image.hash`需要，则为。 如果`no`为，则将其设置为 "false"。 默认值为 `false`。| true/false| `false`|
|`textanalytics.image.hash`| **文本分析**docker 映像哈希。 仅将它用于`image.pullByHash:true`。| 有效的 docker 映像哈希 | |
|`textanalytics.image.args.eula`| **文本分析**容器所需的参数之一，指示已接受许可证。 此选项的值必须为： `accept`。| `accept`如果要使用容器 | |
|`textanalytics.image.args.billing`| **文本分析**容器所需的参数之一，它指定计费终结点 URI。 可以在 Azure 门户的“语音概述”页上获取计费终结点 URI 值。|有效的计费终结点 URI||
|`textanalytics.image.args.apikey`| **文本分析**容器所需的参数之一，用于跟踪计费信息。| 有效的 apikey||
|`textanalytics.cpuRequest`| 请求的**文本分析**容器 CPU| int| `3000m`|
|`textanalytics.cpuLimit`| **文本分析**容器的有限 CPU| | `8000m`|
|`textanalytics.memoryRequest`| 请求**文本分析**容器的内存| | `3Gi`|
|`textanalytics.memoryLimit`| **文本分析**容器的有限内存| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| 情绪分析 URI 后缀，整个 URI 的格式为 "`<service>`http://：`<port>`/`<sentimentURISuffix>`"。 | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Kubernetes 中的**文本分析**服务的类型。 请参阅[Kubernetes 服务类型](https://kubernetes.io/docs/concepts/services-networking/service/) | 有效的 Kubernetes 服务类型 | `LoadBalancer` |
|`textanalytics.service.port`| **文本分析**服务的端口| int| `50085`|
|`textanalytics.service.annotations`| 用户可以添加到**文本分析**服务元数据中的批注。 例如：<br/> **批注**<br/>`   `**some/annotation1： value1**<br/>`  `**some/annotation2： value2** | 批注，每行一个批注| |
|`textanalytics.serivce.autoScaler.enabled`| 是否启用[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。 如果启用， `text-analytics-autoscaler`将部署在 Kubernetes 群集中 | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| 是否启用[Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果启用， `text-analytics-poddisruptionbudget`将部署在 Kubernetes 群集中| true/false| `true`|
