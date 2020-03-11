---
title: 容器存储库和映像
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 两个表，表示所有认知服务产品的容器注册表、存储库和映像名称。
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082474"
---
### <a name="container-repositories-and-images"></a>容器存储库和映像

下表列出了 Azure 认知服务提供的可用容器映像。 有关所有可用容器映像名称及其可用标记的完整列表，请参阅[认知服务容器映像标记](../container-image-tags.md)。 目前没有任何认知服务容器公开发布（GA）。 在此期间，在做出进一步公告之前，容器作为*公共无选通*或*公共封闭预览版*提供。

 - *公共无选通*：容器公开提供，无需使用门控机制。
 - *公共封闭预览版*：容器公开提供，但首先需要正式请求才能访问容器注册表。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "无选通" （容器注册表： `mcr.microsoft.com`）

Microsoft 容器注册表（MCR） syndicates 了用于认知服务的所有公开发布的 "无选通" 容器。 还可以直接从[Docker 中心](https://hub.docker.com/_/microsoft-azure-cognitive-services)获取容器。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 语言检测 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 情绪分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>公共 "封闭" 预览版（容器注册表： `containerpreview.azurecr.io`）

容器预览注册表承载认知服务的所有公开发布的 "封闭" 容器。 这些容器需要正式请求，才能通过其容器注册表进行访问。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [异常探测器](../../anomaly-detector/anomaly-detector-container-howto.md) | 异常检测器 | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [计算机视觉](../../Computer-vision/computer-vision-how-to-install-containers.md) | 读取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [人脸](../../face/face-how-to-install-containers.md) | 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [窗体识别器](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | 表单识别器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=stt) | 语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=cstt) | 自定义语音到文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=tts) | 文本转语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=ctts) | 自定义文本到语音转换 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
