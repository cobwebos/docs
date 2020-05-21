---
title: 容器存储库和映像
services: cognitive-services
author: aahill
manager: nitinme
description: 表示所有认知服务产品的容器注册表、存储库和映像名称的两个表。
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 6d47829d32ed0b7ded545c73748cda1019d94bde
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83720878"
---
### <a name="container-repositories-and-images"></a>容器存储库和映像

下表列出了 Azure 认知服务提供的可用容器映像。 有关所有可用容器映像名称及其可用标记的完整列表，请参阅[认知服务容器映像标记](../container-image-tags.md)。 

#### <a name="generally-available"></a>正式发布 

Microsoft 容器注册表（MCR） syndicates 了所有可用于认知服务的通用容器。 还可以直接从[Docker 中心](https://hub.docker.com/_/microsoft-azure-cognitive-services)获取容器。

#### <a name="luis"></a>[LUIS](#tab/luis)

| LUIS 容器 | 容器注册表/存储库/映像名称 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

有关详细信息，请参阅[如何运行和安装 LUIS 容器](../../LUIS/luis-container-howto.md)。

#### <a name="text-analytics"></a>[文本分析](#tab/text-analytics)

| 文本分析容器 | 容器注册表/存储库/映像名称 |
|--|--|
| 情绪分析 v3 （英语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| 情绪分析 v3 （西班牙语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| 情绪分析 v3 （法语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| 情绪分析 v3 （意大利语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| 情绪分析 v3 （德语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| 情绪分析 v3 （简体中文） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| 情绪分析 v3 （中文-繁体） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| 情绪分析 v3 （日语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| 情绪分析 v3 （葡萄牙语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| 情绪分析 v3 （荷兰语） | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

有关详细信息，请参阅[如何运行和安装文本分析容器](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md)。

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>公用 "无选通" 预览版（容器注册表： `mcr.microsoft.com` ）

以下预览容器是公开提供的。 Microsoft 容器注册表（MCR） syndicates 了所有公开发布的无选通容器用于认知服务。 还可以直接从[Docker 中心](https://hub.docker.com/_/microsoft-azure-cognitive-services)获取容器。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 语言检测 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [异常探测器](../../anomaly-detector/anomaly-detector-container-howto.md) | 异常检测器 | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>公共 "封闭" 预览版（容器注册表： `containerpreview.azurecr.io` ）

以下封闭预览版容器在容器预览注册表上承载，并要求应用程序进行访问。 有关详细信息，请参阅以下容器文章。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [计算机视觉](../../Computer-vision/computer-vision-how-to-install-containers.md) | 读取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [人脸](../../face/face-how-to-install-containers.md) | 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [窗体识别器](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | 表单识别器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=stt) | 语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=cstt) | 自定义语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=tts) | 文本转语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=ctts) | 自定义文本到语音转换 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
