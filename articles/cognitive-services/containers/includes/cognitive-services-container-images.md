---
title: 容器存储库和映像
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 表示所有认知服务产品的容器注册表、存储库和映像名称的两个表。
ms.service: cognitive-services
ms.topic: include
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: ae11fe785237a02652af1f16aaba1f1bac5ecd62
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961908"
---
### <a name="container-repositories-and-images"></a>容器存储库和映像

下表是 Azure 认知服务提供的可用容器映像的综合列表。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>公共“非封闭”（容器注册表：`mcr.microsoft.com`）

Microsoft 容器注册表托管了认知服务的所有公共可用的“非封闭”容器。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 语言检测 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 情绪分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>公共“门控”预览版（容器注册表：`containerpreview.azurecr.io`）

容器预览注册表托管认知服务的所有公共可用“门控”容器。 这些容器需要正式的访问请求才能使用它们。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [异常检测器](../../anomaly-detector/anomaly-detector-container-howto.md) | 异常检测器 | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [计算机视觉](../../Computer-vision/computer-vision-how-to-install-containers.md) | 读取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [人脸](../../face/face-how-to-install-containers.md) | 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [表单识别器](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | 表单识别器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=stt) | 语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=cstt) | 自定义语音到文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=tts) | 文本转语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [语音服务 API](../../speech-service/speech-container-howto.md?tab=ctts) | 自定义文本到语音转换 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
