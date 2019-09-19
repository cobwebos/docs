---
title: 容器存储库和映像
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 两个表，表示所有认知服务产品的容器注册表、存储库和映像名称。
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 181f4acd86dfacb15592ded6f2df3287e3dc13bf
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130152"
---
### <a name="container-repositories-and-images"></a>容器存储库和映像

下表列出了 Azure 认知服务提供的可用容器映像。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "无选通" （容器注册表： `mcr.microsoft.com`）

Microsoft 容器注册表承载认知服务的所有公开发布的 "无选通" 容器。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 语言检测 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [文本分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 情绪分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>公共 "封闭" 预览版（容器注册表： `containerpreview.azurecr.io`）

容器预览注册表承载认知服务的所有公开发布的 "封闭" 容器。 这些容器需要一个正式的访问请求才能使用它们。

| 服务 | 容器 | 容器注册表/存储库/映像名称 |
|--|--|--|
| [异常检测器](../../anomaly-detector/anomaly-detector-container-howto.md) | 异常检测器 | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [计算机视觉](../../Computer-vision/computer-vision-how-to-install-containers.md) | 识别文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [计算机视觉](../../Computer-vision/computer-vision-how-to-install-containers.md) | 读取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [人脸](../../face/face-how-to-install-containers.md) | 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [表单识别器](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | 表单识别器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [语音服务 API](../../speech-service/speech-container-howto.md) | 语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [语音服务 API](../../speech-service/speech-container-howto.md) | 文本转语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
