---
title: 容器存储库和映像
services: cognitive-services
author: aahill
manager: nitinme
description: 表示所有认知服务产品的容器注册表、存储库和映像名称的两个表。
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: d514460f3cd80b5b85604251743abbbbcd1adc8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907009"
---
### <a name="container-repositories-and-images"></a>容器存储库和映像

下表列出了 Azure 认知服务提供的可用容器映像。 有关所有可用容器映像名称及其可用标记的完整列表，请参阅 [认知服务容器映像标记](../container-image-tags.md)。 

#### <a name="generally-available"></a>正式发布 

Microsoft 容器注册表 (MCR) syndicates 了所有可用于认知服务的通用容器。 还可以直接从 [Docker 中心](https://hub.docker.com/_/microsoft-azure-cognitive-services)获取容器。

**LUIS**

| 容器 | 容器注册表/存储库/映像名称 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

有关详细信息，请参阅 [如何运行和安装 LUIS 容器](../../LUIS/luis-container-howto.md) 。

**文本分析**

| 容器 | 容器注册表/存储库/映像名称 |
|--|--|
| 情绪分析 v3 (英语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| 情绪分析 v3 (西班牙语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| 情绪分析 v3 (法语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| 情绪分析 v3 (意大利语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| 情绪分析 v3 (德语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| 情绪分析 v3 (简体中文)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| 情绪分析 v3 (中文-繁体)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| 情绪分析 v3 (日语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| 情绪分析 v3 (葡萄牙语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| 情绪分析 v3 (荷兰语)  | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

有关详细信息，请参阅 [如何运行和安装文本分析容器](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) 。

**异常检测器** 

| 容器 | 容器注册表/存储库/映像名称 |
|--|--|
| 异常检测器 | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

有关详细信息，请参阅 [如何运行和安装异常探测器容器](../../anomaly-detector/anomaly-detector-container-howto.md) 。

**语音服务**

> [!NOTE]
> 若要使用语音容器，你将需要完成 [联机请求窗体](https://aka.ms/csgate)。

| 容器 | 容器注册表/存储库/映像名称 |
|--|--|
| [语音到文本](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [自定义语音转文本](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [文本到语音转换](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"无选通" 预览版 

以下预览容器是公开提供的。 Microsoft 容器注册表 (MCR) syndicates 用于认知服务的所有公开的无选通容器。 还可以直接从 [Docker 中心](https://hub.docker.com/_/microsoft-azure-cognitive-services)获取容器。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 语言检测 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>"封闭" 预览版

以前，封闭预览容器承载于 `containerpreview.azurecr.io` 存储库中。 从9月22日2020开始，这些容器 (除了运行状况) 文本分析在 Microsoft 容器注册表 (MCR) 上承载，并不需要使用 docker login 命令下载它们。 若要使用容器，你将需要：

1. 使用 Azure 订阅 ID 和用户方案填写 [请求窗体](https://aka.ms/csgate) 。 
2. 批准后，从 MCR 下载容器。 
3. 使用适当 Azure 资源中的密钥和终结点在运行时对容器进行身份验证。 

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [计算机视觉](../../Computer-vision/computer-vision-how-to-install-containers.md) | Read v3.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| [计算机视觉](../../Computer-vision/computer-vision-how-to-install-containers.md) | 读取3.1 版 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | 空间分析 | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=ctts) | 自定义文本到语音转换 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=lid) | 语言检测 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=ntts) | 神经文本到语音转换 | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [运行状况文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | 运行状况文本分析 | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

