---
title: 认知服务容器映像标记
titleSuffix: Azure Cognitive Services
description: 所有认知服务容器映像标记的完整列表。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 786fde90e1bcf47e08857c26235a27862c5b3d75
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878759"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure 认知服务容器映像标记

Azure 认知服务提供了许多容器映像。 容器注册表和相应的存储库因容器映像而异。 每个容器映像名称都提供多个标记。 容器映像标记是版本控制容器映像的机制。 本文旨在用作列出所有认知服务容器映像及其可用标记的综合参考。

> [!TIP]
> 使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)时，要密切注意容器注册表、存储库、容器映像名称和相应标记的外壳，因为它们区分**大小写**。

## <a name="anomaly-detector"></a>异常检测器

[异常检测器][ad-containers]容器映像可以在容器注册表中找到`containerpreview.azurecr.io`。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-anomaly-detector`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>计算机视觉

[计算机视觉][cv-containers]容器映像可以在`containerpreview.azurecr.io`容器注册表中找到。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-read`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-read`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>人脸

[面][fa-containers]容器映像可以在`containerpreview.azurecr.io`容器注册表中找到。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-face`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-face`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>表单识别器

[窗体识别器][fr-containers]容器映像可以在容器注册表中找到`containerpreview.azurecr.io`。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-form-recognizer`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>语言理解 (LUIS)

[LUIS][lu-containers]容器映像可以在`mcr.microsoft.com`容器注册表子级中找到。 它驻留在存储库中`azure-cognitive-services`，并命名为`luis`。 完全限定的容器映像名称是`mcr.microsoft.com/azure-cognitive-services/luis`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>自定义语音转文本

可在`containerpreview.azurecr.io`容器注册表中找到[自定义语音到文本][sp-cstt]容器映像。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-custom-speech-to-text`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`。

此容器映像具有以下可用标记：

| 图像标签            | 说明 |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>自定义文本到语音

可在`containerpreview.azurecr.io`容器注册表中找到[自定义文本到语音转换][sp-ctts]容器映像。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-custom-text-to-speech`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`。

此容器映像具有以下可用标记：

| 图像标签            | 说明 |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>语音转文本

可以在`containerpreview.azurecr.io`容器注册表中找到[语音到文本][sp-stt]容器映像。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-speech-to-text`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`。

此容器映像具有以下可用标记：

| 图像标签                  | 说明                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | 具有区域设置的`en-US`容器映像。 |
| `2.1.1-amd64-ar-ae-preview` | 具有区域设置的`ar-AE`容器映像。 |
| `2.1.1-amd64-ar-eg-preview` | 具有区域设置的`ar-EG`容器映像。 |
| `2.1.1-amd64-ar-kw-preview` | 具有区域设置的`ar-KW`容器映像。 |
| `2.1.1-amd64-ar-qa-preview` | 具有区域设置的`ar-QA`容器映像。 |
| `2.1.1-amd64-ar-sa-preview` | 具有区域设置的`ar-SA`容器映像。 |
| `2.1.1-amd64-ca-es-preview` | 具有区域设置的`ca-ES`容器映像。 |
| `2.1.1-amd64-da-dk-preview` | 具有区域设置的`da-DK`容器映像。 |
| `2.1.1-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `2.1.1-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `2.1.1-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `2.1.1-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `2.1.1-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `2.1.1-amd64-en-nz-preview` | 具有区域设置的`en-NZ`容器映像。 |
| `2.1.1-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `2.1.1-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `2.1.1-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `2.1.1-amd64-fi-fi-preview` | 具有区域设置的`fi-FI`容器映像。 |
| `2.1.1-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `2.1.1-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `2.1.1-amd64-gu-in-preview` | 具有区域设置的`gu-IN`容器映像。 |
| `2.1.1-amd64-hi-in-preview` | 具有区域设置的`hi-IN`容器映像。 |
| `2.1.1-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `2.1.1-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `2.1.1-amd64-ko-kr-preview` | 具有区域设置的`ko-KR`容器映像。 |
| `2.1.1-amd64-mr-in-preview` | 具有区域设置的`mr-IN`容器映像。 |
| `2.1.1-amd64-nb-no-preview` | 具有区域设置的`nb-NO`容器映像。 |
| `2.1.1-amd64-nl-nl-preview` | 具有区域设置的`nl-NL`容器映像。 |
| `2.1.1-amd64-pl-pl-preview` | 具有区域设置的`pl-PL`容器映像。 |
| `2.1.1-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `2.1.1-amd64-pt-pt-preview` | 具有区域设置的`pt-PT`容器映像。 |
| `2.1.1-amd64-ru-ru-preview` | 具有区域设置的`ru-RU`容器映像。 |
| `2.1.1-amd64-sv-se-preview` | 具有区域设置的`sv-SE`容器映像。 |
| `2.1.1-amd64-ta-in-preview` | 具有区域设置的`ta-IN`容器映像。 |
| `2.1.1-amd64-te-in-preview` | 具有区域设置的`te-IN`容器映像。 |
| `2.1.1-amd64-th-th-preview` | 具有区域设置的`th-TH`容器映像。 |
| `2.1.1-amd64-tr-tr-preview` | 具有区域设置的`tr-TR`容器映像。 |
| `2.1.1-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `2.1.1-amd64-zh-hk-preview` | 具有区域设置的`zh-HK`容器映像。 |
| `2.1.1-amd64-zh-tw-preview` | 具有区域设置的`zh-TW`容器映像。 |
| `2.1.0-amd64-ar-ae-preview` | 具有区域设置的`ar-AE`容器映像。 |
| `2.1.0-amd64-ar-eg-preview` | 具有区域设置的`ar-EG`容器映像。 |
| `2.1.0-amd64-ar-kw-preview` | 具有区域设置的`ar-KW`容器映像。 |
| `2.1.0-amd64-ar-qa-preview` | 具有区域设置的`ar-QA`容器映像。 |
| `2.1.0-amd64-ar-sa-preview` | 具有区域设置的`ar-SA`容器映像。 |
| `2.1.0-amd64-ca-es-preview` | 具有区域设置的`ca-ES`容器映像。 |
| `2.1.0-amd64-da-dk-preview` | 具有区域设置的`da-DK`容器映像。 |
| `2.1.0-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `2.1.0-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `2.1.0-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `2.1.0-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `2.1.0-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `2.1.0-amd64-en-nz-preview` | 具有区域设置的`en-NZ`容器映像。 |
| `2.1.0-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `2.1.0-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `2.1.0-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `2.1.0-amd64-fi-fi-preview` | 具有区域设置的`fi-FI`容器映像。 |
| `2.1.0-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `2.1.0-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `2.1.0-amd64-gu-in-preview` | 具有区域设置的`gu-IN`容器映像。 |
| `2.1.0-amd64-hi-in-preview` | 具有区域设置的`hi-IN`容器映像。 |
| `2.1.0-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `2.1.0-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `2.1.0-amd64-ko-kr-preview` | 具有区域设置的`ko-KR`容器映像。 |
| `2.1.0-amd64-mr-in-preview` | 具有区域设置的`mr-IN`容器映像。 |
| `2.1.0-amd64-nb-no-preview` | 具有区域设置的`nb-NO`容器映像。 |
| `2.1.0-amd64-nl-nl-preview` | 具有区域设置的`nl-NL`容器映像。 |
| `2.1.0-amd64-pl-pl-preview` | 具有区域设置的`pl-PL`容器映像。 |
| `2.1.0-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `2.1.0-amd64-pt-pt-preview` | 具有区域设置的`pt-PT`容器映像。 |
| `2.1.0-amd64-ru-ru-preview` | 具有区域设置的`ru-RU`容器映像。 |
| `2.1.0-amd64-sv-se-preview` | 具有区域设置的`sv-SE`容器映像。 |
| `2.1.0-amd64-ta-in-preview` | 具有区域设置的`ta-IN`容器映像。 |
| `2.1.0-amd64-te-in-preview` | 具有区域设置的`te-IN`容器映像。 |
| `2.1.0-amd64-th-th-preview` | 具有区域设置的`th-TH`容器映像。 |
| `2.1.0-amd64-tr-tr-preview` | 具有区域设置的`tr-TR`容器映像。 |
| `2.1.0-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `2.1.0-amd64-zh-hk-preview` | 具有区域设置的`zh-HK`容器映像。 |
| `2.1.0-amd64-zh-tw-preview` | 具有区域设置的`zh-TW`容器映像。 |
| `2.0.3-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `2.0.2-amd64-ar-ae-preview` | 具有区域设置的`ar-AE`容器映像。 |
| `2.0.2-amd64-ar-eg-preview` | 具有区域设置的`ar-EG`容器映像。 |
| `2.0.2-amd64-ar-kw-preview` | 具有区域设置的`ar-KW`容器映像。 |
| `2.0.2-amd64-ar-qa-preview` | 具有区域设置的`ar-QA`容器映像。 |
| `2.0.2-amd64-ar-sa-preview` | 具有区域设置的`ar-SA`容器映像。 |
| `2.0.2-amd64-ca-es-preview` | 具有区域设置的`ca-ES`容器映像。 |
| `2.0.2-amd64-da-dk-preview` | 具有区域设置的`da-DK`容器映像。 |
| `2.0.2-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `2.0.2-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `2.0.2-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `2.0.2-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `2.0.2-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `2.0.2-amd64-en-nz-preview` | 具有区域设置的`en-NZ`容器映像。 |
| `2.0.2-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `2.0.2-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `2.0.2-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `2.0.2-amd64-fi-fi-preview` | 具有区域设置的`fi-FI`容器映像。 |
| `2.0.2-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `2.0.2-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `2.0.2-amd64-gu-in-preview` | 具有区域设置的`gu-IN`容器映像。 |
| `2.0.2-amd64-hi-in-preview` | 具有区域设置的`hi-IN`容器映像。 |
| `2.0.2-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `2.0.2-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `2.0.2-amd64-ko-kr-preview` | 具有区域设置的`ko-KR`容器映像。 |
| `2.0.2-amd64-mr-in-preview` | 具有区域设置的`mr-IN`容器映像。 |
| `2.0.2-amd64-nb-no-preview` | 具有区域设置的`nb-NO`容器映像。 |
| `2.0.2-amd64-nl-nl-preview` | 具有区域设置的`nl-NL`容器映像。 |
| `2.0.2-amd64-pl-pl-preview` | 具有区域设置的`pl-PL`容器映像。 |
| `2.0.2-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `2.0.2-amd64-pt-pt-preview` | 具有区域设置的`pt-PT`容器映像。 |
| `2.0.2-amd64-ru-ru-preview` | 具有区域设置的`ru-RU`容器映像。 |
| `2.0.2-amd64-sv-se-preview` | 具有区域设置的`sv-SE`容器映像。 |
| `2.0.2-amd64-ta-in-preview` | 具有区域设置的`ta-IN`容器映像。 |
| `2.0.2-amd64-te-in-preview` | 具有区域设置的`te-IN`容器映像。 |
| `2.0.2-amd64-th-th-preview` | 具有区域设置的`th-TH`容器映像。 |
| `2.0.2-amd64-tr-tr-preview` | 具有区域设置的`tr-TR`容器映像。 |
| `2.0.2-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `2.0.2-amd64-zh-hk-preview` | 具有区域设置的`zh-HK`容器映像。 |
| `2.0.2-amd64-zh-tw-preview` | 具有区域设置的`zh-TW`容器映像。 |
| `2.0.1-amd64-ar-ae-preview` | 具有区域设置的`ar-AE`容器映像。 |
| `2.0.1-amd64-ar-eg-preview` | 具有区域设置的`ar-EG`容器映像。 |
| `2.0.1-amd64-ar-kw-preview` | 具有区域设置的`ar-KW`容器映像。 |
| `2.0.1-amd64-ar-qa-preview` | 具有区域设置的`ar-QA`容器映像。 |
| `2.0.1-amd64-ar-sa-preview` | 具有区域设置的`ar-SA`容器映像。 |
| `2.0.1-amd64-ca-es-preview` | 具有区域设置的`ca-ES`容器映像。 |
| `2.0.1-amd64-da-dk-preview` | 具有区域设置的`da-DK`容器映像。 |
| `2.0.1-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `2.0.1-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `2.0.1-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `2.0.1-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `2.0.1-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `2.0.1-amd64-en-nz-preview` | 具有区域设置的`en-NZ`容器映像。 |
| `2.0.1-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `2.0.1-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `2.0.1-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `2.0.1-amd64-fi-fi-preview` | 具有区域设置的`fi-FI`容器映像。 |
| `2.0.1-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `2.0.1-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `2.0.1-amd64-gu-in-preview` | 具有区域设置的`gu-IN`容器映像。 |
| `2.0.1-amd64-hi-in-preview` | 具有区域设置的`hi-IN`容器映像。 |
| `2.0.1-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `2.0.1-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `2.0.1-amd64-ko-kr-preview` | 具有区域设置的`ko-KR`容器映像。 |
| `2.0.1-amd64-mr-in-preview` | 具有区域设置的`mr-IN`容器映像。 |
| `2.0.1-amd64-nb-no-preview` | 具有区域设置的`nb-NO`容器映像。 |
| `2.0.1-amd64-nl-nl-preview` | 具有区域设置的`nl-NL`容器映像。 |
| `2.0.1-amd64-pl-pl-preview` | 具有区域设置的`pl-PL`容器映像。 |
| `2.0.1-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `2.0.1-amd64-pt-pt-preview` | 具有区域设置的`pt-PT`容器映像。 |
| `2.0.1-amd64-ru-ru-preview` | 具有区域设置的`ru-RU`容器映像。 |
| `2.0.1-amd64-sv-se-preview` | 具有区域设置的`sv-SE`容器映像。 |
| `2.0.1-amd64-ta-in-preview` | 具有区域设置的`ta-IN`容器映像。 |
| `2.0.1-amd64-te-in-preview` | 具有区域设置的`te-IN`容器映像。 |
| `2.0.1-amd64-th-th-preview` | 具有区域设置的`th-TH`容器映像。 |
| `2.0.1-amd64-tr-tr-preview` | 具有区域设置的`tr-TR`容器映像。 |
| `2.0.1-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `2.0.1-amd64-zh-hk-preview` | 具有区域设置的`zh-HK`容器映像。 |
| `2.0.1-amd64-zh-tw-preview` | 具有区域设置的`zh-TW`容器映像。 |
| `2.0.0-amd64-ar-eg-preview` | 具有区域设置的`ar-EG`容器映像。 |
| `2.0.0-amd64-ca-es-preview` | 具有区域设置的`ca-ES`容器映像。 |
| `2.0.0-amd64-da-dk-preview` | 具有区域设置的`da-DK`容器映像。 |
| `2.0.0-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `2.0.0-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `2.0.0-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `2.0.0-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `2.0.0-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `2.0.0-amd64-en-nz-preview` | 具有区域设置的`en-NZ`容器映像。 |
| `2.0.0-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `2.0.0-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `2.0.0-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `2.0.0-amd64-fi-fi-preview` | 具有区域设置的`fi-FI`容器映像。 |
| `2.0.0-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `2.0.0-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `2.0.0-amd64-hi-in-preview` | 具有区域设置的`hi-IN`容器映像。 |
| `2.0.0-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `2.0.0-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `2.0.0-amd64-ko-kr-preview` | 具有区域设置的`ko-KR`容器映像。 |
| `2.0.0-amd64-nb-no-preview` | 具有区域设置的`nb-NO`容器映像。 |
| `2.0.0-amd64-nl-nl-preview` | 具有区域设置的`nl-NL`容器映像。 |
| `2.0.0-amd64-pl-pl-preview` | 具有区域设置的`pl-PL`容器映像。 |
| `2.0.0-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `2.0.0-amd64-pt-pt-preview` | 具有区域设置的`pt-PT`容器映像。 |
| `2.0.0-amd64-ru-ru-preview` | 具有区域设置的`ru-RU`容器映像。 |
| `2.0.0-amd64-sv-se-preview` | 具有区域设置的`sv-SE`容器映像。 |
| `2.0.0-amd64-th-th-preview` | 具有区域设置的`th-TH`容器映像。 |
| `2.0.0-amd64-tr-tr-preview` | 具有区域设置的`tr-TR`容器映像。 |
| `2.0.0-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `2.0.0-amd64-zh-hk-preview` | 具有区域设置的`zh-HK`容器映像。 |
| `2.0.0-amd64-zh-tw-preview` | 具有区域设置的`zh-TW`容器映像。 |
| `1.2.0-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `1.2.0-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `1.2.0-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `1.2.0-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `1.2.0-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `1.2.0-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `1.2.0-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `1.2.0-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `1.2.0-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `1.2.0-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `1.2.0-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `1.2.0-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `1.2.0-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `1.2.0-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `1.1.3-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `1.1.3-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `1.1.3-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `1.1.3-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `1.1.3-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `1.1.3-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `1.1.3-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `1.1.3-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `1.1.3-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `1.1.3-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `1.1.3-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `1.1.3-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `1.1.3-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `1.1.3-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `1.1.2-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `1.1.2-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `1.1.2-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `1.1.2-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `1.1.2-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `1.1.2-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `1.1.2-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `1.1.2-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `1.1.2-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `1.1.2-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `1.1.2-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `1.1.2-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `1.1.2-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `1.1.2-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `1.1.1-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `1.1.1-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `1.1.1-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `1.1.1-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `1.1.1-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `1.1.1-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `1.1.1-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `1.1.1-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `1.1.1-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `1.1.1-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `1.1.1-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `1.1.1-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `1.1.1-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `1.1.1-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `1.1.0-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `1.1.0-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `1.1.0-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `1.1.0-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `1.1.0-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `1.1.0-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `1.1.0-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `1.1.0-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `1.1.0-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `1.1.0-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `1.1.0-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `1.1.0-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `1.1.0-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `1.1.0-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |
| `1.0.0-amd64-de-de-preview` | 具有区域设置的`de-DE`容器映像。 |
| `1.0.0-amd64-en-au-preview` | 具有区域设置的`en-AU`容器映像。 |
| `1.0.0-amd64-en-ca-preview` | 具有区域设置的`en-CA`容器映像。 |
| `1.0.0-amd64-en-gb-preview` | 具有区域设置的`en-GB`容器映像。 |
| `1.0.0-amd64-en-in-preview` | 具有区域设置的`en-IN`容器映像。 |
| `1.0.0-amd64-en-us-preview` | 具有区域设置的`en-US`容器映像。 |
| `1.0.0-amd64-es-es-preview` | 具有区域设置的`es-ES`容器映像。 |
| `1.0.0-amd64-es-mx-preview` | 具有区域设置的`es-MX`容器映像。 |
| `1.0.0-amd64-fr-ca-preview` | 具有区域设置的`fr-CA`容器映像。 |
| `1.0.0-amd64-fr-fr-preview` | 具有区域设置的`fr-FR`容器映像。 |
| `1.0.0-amd64-it-it-preview` | 具有区域设置的`it-IT`容器映像。 |
| `1.0.0-amd64-ja-jp-preview` | 具有区域设置的`ja-JP`容器映像。 |
| `1.0.0-amd64-pt-br-preview` | 具有区域设置的`pt-BR`容器映像。 |
| `1.0.0-amd64-zh-cn-preview` | 具有区域设置的`zh-CN`容器映像。 |

## <a name="text-to-speech"></a>文本转语音

[文本到语音转换][sp-tts]容器映像可以在`containerpreview.azurecr.io`容器注册表中找到。 它驻留在存储库中`microsoft`，并命名为`cognitive-services-text-to-speech`。 完全限定的容器映像名称是`containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`。

此容器映像具有以下可用标记：

| 图像标签                                  | 说明                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 带有区域设置和`en-US``en-US-JessaRUS`语音的容器映像。        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | 带有区域设置和`ar-EG``ar-EG-Hoda`语音的容器映像。            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | 带有区域设置和`ar-SA``ar-SA-Naayf`语音的容器映像。           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | 带有区域设置和`bg-BG``bg-BG-Ivan`语音的容器映像。            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | 带有区域设置和`ca-ES``ca-ES-HerenaRUS`语音的容器映像。       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | 带有区域设置和`cs-CZ``cs-CZ-Jakub`语音的容器映像。           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | 带有区域设置和`da-DK``da-DK-HelleRUS`语音的容器映像。        |
| `1.3.0-amd64-de-at-michael-preview`         | 带有区域设置和`de-AT``de-AT-Michael`语音的容器映像。         |
| `1.3.0-amd64-de-ch-karsten-preview`         | 带有区域设置和`de-CH``de-CH-Karsten`语音的容器映像。         |
| `1.3.0-amd64-de-de-hedda-preview`           | 带有区域设置和`de-DE``de-DE-Hedda`语音的容器映像。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 带有区域设置和`de-DE``de-DE-Hedda`语音的容器映像。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 带有区域设置和`de-DE``de-DE-HeddaRUS`语音的容器映像。        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | 带有区域设置和`de-DE``de-DE-Stefan-Apollo`语音的容器映像。   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | 带有区域设置和`el-GR``el-GR-Stefanos`语音的容器映像。        |
| `1.3.0-amd64-en-au-catherine-preview`       | 带有区域设置和`en-AU``en-AU-Catherine`语音的容器映像。       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | 带有区域设置和`en-AU``en-AU-HayleyRUS`语音的容器映像。       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | 带有区域设置和`en-CA``en-CA-HeatherRUS`语音的容器映像。      |
| `1.3.0-amd64-en-ca-linda-preview`           | 带有区域设置和`en-CA``en-CA-Linda`语音的容器映像。           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | 带有区域设置和`en-GB``en-GB-George-Apollo`语音的容器映像。   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | 带有区域设置和`en-GB``en-GB-HazelRUS`语音的容器映像。        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | 带有区域设置和`en-GB``en-GB-Susan-Apollo`语音的容器映像。    |
| `1.3.0-amd64-en-ie-sean-preview`            | 带有区域设置和`en-IE``en-IE-Sean`语音的容器映像。            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | 带有区域设置和`en-IN``en-IN-Heera-Apollo`语音的容器映像。    |
| `1.3.0-amd64-en-in-priyarus-preview`        | 带有区域设置和`en-IN``en-IN-PriyaRUS`语音的容器映像。        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | 带有区域设置和`en-IN``en-IN-Ravi-Apollo`语音的容器映像。     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | 带有区域设置和`en-US``en-US-BenjaminRUS`语音的容器映像。     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | 带有区域设置和`en-US``en-US-Guy24kRUS`语音的容器映像。       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | 带有区域设置和`en-US``en-US-Jessa24kRUS`语音的容器映像。     |
| `1.3.0-amd64-en-us-jessarus-preview`        | 带有区域设置和`en-US``en-US-JessaRUS`语音的容器映像。        |
| `1.3.0-amd64-en-us-zirarus-preview`         | 带有区域设置和`en-US``en-US-ZiraRUS`语音的容器映像。         |
| `1.3.0-amd64-es-es-helenarus-preview`       | 带有区域设置和`es-ES``es-ES-HelenaRUS`语音的容器映像。       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | 带有区域设置和`es-ES``es-ES-Laura-Apollo`语音的容器映像。    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | 带有区域设置和`es-ES``es-ES-Pablo-Apollo`语音的容器映像。    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | 带有区域设置和`es-MX``es-MX-HildaRUS`语音的容器映像。        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | 带有区域设置和`es-MX``es-MX-Raul-Apollo`语音的容器映像。     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | 带有区域设置和`fi-FI``fi-FI-HeidiRUS`语音的容器映像。        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | 带有区域设置和`fr-CA``fr-CA-Caroline`语音的容器映像。        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | 带有区域设置和`fr-CA``fr-CA-HarmonieRUS`语音的容器映像。     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | 带有区域设置和`fr-CH``fr-CH-Guillaume`语音的容器映像。       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | 带有区域设置和`fr-FR``fr-FR-HortenseRUS`语音的容器映像。     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | 带有区域设置和`fr-FR``fr-FR-Julie-Apollo`语音的容器映像。    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | 带有区域设置和`fr-FR``fr-FR-Paul-Apollo`语音的容器映像。     |
| `1.3.0-amd64-he-il-asaf-preview`            | 带有区域设置和`he-IL``he-IL-Asaf`语音的容器映像。            |
| `1.3.0-amd64-hi-in-hemant-preview`          | 带有区域设置和`hi-IN``hi-IN-Hemant`语音的容器映像。          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 带有区域设置和`hi-IN``hi-IN-Kalpana-Apollo`语音的容器映像。  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 带有区域设置和`hi-IN``hi-IN-Kalpana`语音的容器映像。         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 带有区域设置和`hi-IN``hi-IN-Kalpana`语音的容器映像。         |
| `1.3.0-amd64-hr-hr-matej-preview`           | 带有区域设置和`hr-HR``hr-HR-Matej`语音的容器映像。           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | 带有区域设置和`hu-HU``hu-HU-Szabolcs`语音的容器映像。        |
| `1.3.0-amd64-id-id-andika-preview`          | 带有区域设置和`id-ID``id-ID-Andika`语音的容器映像。          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | 带有区域设置和`it-IT``it-IT-Cosimo-Apollo`语音的容器映像。   |
| `1.3.0-amd64-it-it-luciarus-preview`        | 带有区域设置和`it-IT``it-IT-LuciaRUS`语音的容器映像。        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | 带有区域设置和`ja-JP``ja-JP-Ayumi-Apollo`语音的容器映像。    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | 带有区域设置和`ja-JP``ja-JP-HarukaRUS`语音的容器映像。       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | 带有区域设置和`ja-JP``ja-JP-Ichiro-Apollo`语音的容器映像。   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | 带有区域设置和`ko-KR``ko-KR-HeamiRUS`语音的容器映像。        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | 带有区域设置和`ms-MY``ms-MY-Rizwan`语音的容器映像。          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | 带有区域设置和`nb-NO``nb-NO-HuldaRUS`语音的容器映像。        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | 带有区域设置和`nl-NL``nl-NL-HannaRUS`语音的容器映像。        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | 带有区域设置和`pl-PL``pl-PL-PaulinaRUS`语音的容器映像。      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | 带有区域设置和`pt-BR``pt-BR-Daniel-Apollo`语音的容器映像。   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | 带有区域设置和`pt-BR``pt-BR-HeloisaRUS`语音的容器映像。      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | 带有区域设置和`pt-PT``pt-PT-HeliaRUS`语音的容器映像。        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | 带有区域设置和`ro-RO``ro-RO-Andrei`语音的容器映像。          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | 带有区域设置和`ru-RU``ru-RU-EkaterinaRUS`语音的容器映像。    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | 带有区域设置和`ru-RU``ru-RU-Irina-Apollo`语音的容器映像。    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | 带有区域设置和`ru-RU``ru-RU-Pavel-Apollo`语音的容器映像。    |
| `1.3.0-amd64-sk-sk-filip-preview`           | 带有区域设置和`sk-SK``sk-SK-Filip`语音的容器映像。           |
| `1.3.0-amd64-sl-si-lado-preview`            | 带有区域设置和`sl-SI``sl-SI-Lado`语音的容器映像。            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | 带有区域设置和`sv-SE``sv-SE-HedvigRUS`语音的容器映像。       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | 带有区域设置和`ta-IN``ta-IN-Valluvar`语音的容器映像。        |
| `1.3.0-amd64-te-in-chitra-preview`          | 带有区域设置和`te-IN``te-IN-Chitra`语音的容器映像。          |
| `1.3.0-amd64-th-th-pattara-preview`         | 带有区域设置和`th-TH``th-TH-Pattara`语音的容器映像。         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | 带有区域设置和`tr-TR``tr-TR-SedaRUS`语音的容器映像。         |
| `1.3.0-amd64-vi-vn-an-preview`              | 带有区域设置和`vi-VN``vi-VN-An`语音的容器映像。              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | 带有区域设置和`zh-CN``zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 带有区域设置和`zh-CN``zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | 带有区域设置和`zh-CN``zh-CN-Yaoyao-Apollo`语音的容器映像。   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | 带有区域设置和`zh-HK``zh-HK-Danny-Apollo`语音的容器映像。    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | 带有区域设置和`zh-HK``zh-HK-Tracy-Apollo`语音的容器映像。    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | 带有区域设置和`zh-HK``zh-HK-TracyRUS`语音的容器映像。        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | 带有区域设置和`zh-TW``zh-TW-HanHanRUS`语音的容器映像。       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | 带有区域设置和`zh-TW``zh-TW-Yating-Apollo`语音的容器映像。   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | 带有区域设置和`zh-TW``zh-TW-Zhiwei-Apollo`语音的容器映像。   |
| `1.2.0-amd64-de-de-heddarus-preview`        | 带有区域设置和`de-DE``de-DE-Hedda`语音的容器映像。           |
| `1.2.0-amd64-de-de-heddarus-preview`        | 带有区域设置和`de-DE``de-DE-HeddaRUS`语音的容器映像。        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | 带有区域设置和`de-DE``de-DE-Stefan-Apollo`语音的容器映像。   |
| `1.2.0-amd64-en-au-catherine-preview`       | 带有区域设置和`en-AU``en-AU-Catherine`语音的容器映像。       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | 带有区域设置和`en-AU``en-AU-HayleyRUS`语音的容器映像。       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | 带有区域设置和`en-GB``en-GB-George-Apollo`语音的容器映像。   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | 带有区域设置和`en-GB``en-GB-HazelRUS`语音的容器映像。        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | 带有区域设置和`en-GB``en-GB-Susan-Apollo`语音的容器映像。    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | 带有区域设置和`en-IN``en-IN-Heera-Apollo`语音的容器映像。    |
| `1.2.0-amd64-en-in-priyarus-preview`        | 带有区域设置和`en-IN``en-IN-PriyaRUS`语音的容器映像。        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | 带有区域设置和`en-IN``en-IN-Ravi-Apollo`语音的容器映像。     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | 带有区域设置和`en-US``en-US-BenjaminRUS`语音的容器映像。     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | 带有区域设置和`en-US``en-US-Guy24kRUS`语音的容器映像。       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | 带有区域设置和`en-US``en-US-Jessa24kRUS`语音的容器映像。     |
| `1.2.0-amd64-en-us-jessarus-preview`        | 带有区域设置和`en-US``en-US-JessaRUS`语音的容器映像。        |
| `1.2.0-amd64-en-us-zirarus-preview`         | 带有区域设置和`en-US``en-US-ZiraRUS`语音的容器映像。         |
| `1.2.0-amd64-es-es-helenarus-preview`       | 带有区域设置和`es-ES``es-ES-HelenaRUS`语音的容器映像。       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | 带有区域设置和`es-ES``es-ES-Laura-Apollo`语音的容器映像。    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | 带有区域设置和`es-ES``es-ES-Pablo-Apollo`语音的容器映像。    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | 带有区域设置和`es-MX``es-MX-HildaRUS`语音的容器映像。        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | 带有区域设置和`es-MX``es-MX-Raul-Apollo`语音的容器映像。     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | 带有区域设置和`fr-CA``fr-CA-Caroline`语音的容器映像。        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | 带有区域设置和`fr-CA``fr-CA-HarmonieRUS`语音的容器映像。     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | 带有区域设置和`fr-FR``fr-FR-HortenseRUS`语音的容器映像。     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | 带有区域设置和`fr-FR``fr-FR-Julie-Apollo`语音的容器映像。    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | 带有区域设置和`fr-FR``fr-FR-Paul-Apollo`语音的容器映像。     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | 带有区域设置和`it-IT``it-IT-Cosimo-Apollo`语音的容器映像。   |
| `1.2.0-amd64-it-it-luciarus-preview`        | 带有区域设置和`it-IT``it-IT-LuciaRUS`语音的容器映像。        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | 带有区域设置和`ja-JP``ja-JP-Ayumi-Apollo`语音的容器映像。    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | 带有区域设置和`ja-JP``ja-JP-HarukaRUS`语音的容器映像。       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | 带有区域设置和`ja-JP``ja-JP-Ichiro-Apollo`语音的容器映像。   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | 带有区域设置和`ko-KR``ko-KR-HeamiRUS`语音的容器映像。        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | 带有区域设置和`pt-BR``pt-BR-Daniel-Apollo`语音的容器映像。   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | 带有区域设置和`pt-BR``pt-BR-HeloisaRUS`语音的容器映像。      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | 带有区域设置和`zh-CN``zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 带有区域设置和`zh-CN``zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | 带有区域设置和`zh-CN``zh-CN-Yaoyao-Apollo`语音的容器映像。   |
| `1.1.0-amd64-de-de-hedda-preview`           | 带有区域设置和`de-DE``de-DE-Hedda`语音的容器映像。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 带有区域设置和`de-DE``de-DE-Hedda`语音的容器映像。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 带有区域设置和`de-DE``de-DE-HeddaRUS`语音的容器映像。        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | 带有区域设置和`de-DE``de-DE-Stefan-Apollo`语音的容器映像。   |
| `1.1.0-amd64-en-au-catherine-preview`       | 带有区域设置和`en-AU``en-AU-Catherine`语音的容器映像。       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | 带有区域设置和`en-AU``en-AU-HayleyRUS`语音的容器映像。       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | 带有区域设置和`en-GB``en-GB-George-Apollo`语音的容器映像。   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | 带有区域设置和`en-GB``en-GB-HazelRUS`语音的容器映像。        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | 带有区域设置和`en-GB``en-GB-Susan-Apollo`语音的容器映像。    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | 带有区域设置和`en-IN``en-IN-Heera-Apollo`语音的容器映像。    |
| `1.1.0-amd64-en-in-priyarus-preview`        | 带有区域设置和`en-IN``en-IN-PriyaRUS`语音的容器映像。        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | 带有区域设置和`en-IN``en-IN-Ravi-Apollo`语音的容器映像。     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | 带有区域设置和`en-US``en-US-BenjaminRUS`语音的容器映像。     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | 带有区域设置和`en-US``en-US-Guy24kRUS`语音的容器映像。       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | 带有区域设置和`en-US``en-US-Jessa24kRUS`语音的容器映像。     |
| `1.1.0-amd64-en-us-jessarus-preview`        | 带有区域设置和`en-US``en-US-JessaRUS`语音的容器映像。        |
| `1.1.0-amd64-en-us-zirarus-preview`         | 带有区域设置和`en-US``en-US-ZiraRUS`语音的容器映像。         |
| `1.1.0-amd64-es-es-helenarus-preview`       | 带有区域设置和`es-ES``es-ES-HelenaRUS`语音的容器映像。       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | 带有区域设置和`es-ES``es-ES-Laura-Apollo`语音的容器映像。    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | 带有区域设置和`es-ES``es-ES-Pablo-Apollo`语音的容器映像。    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | 带有区域设置和`es-MX``es-MX-HildaRUS`语音的容器映像。        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | 带有区域设置和`es-MX``es-MX-Raul-Apollo`语音的容器映像。     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | 带有区域设置和`fr-CA``fr-CA-Caroline`语音的容器映像。        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | 带有区域设置和`fr-CA``fr-CA-HarmonieRUS`语音的容器映像。     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | 带有区域设置和`fr-FR``fr-FR-HortenseRUS`语音的容器映像。     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | 带有区域设置和`fr-FR``fr-FR-Julie-Apollo`语音的容器映像。    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | 带有区域设置和`fr-FR``fr-FR-Paul-Apollo`语音的容器映像。     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | 带有区域设置和`it-IT``it-IT-Cosimo-Apollo`语音的容器映像。   |
| `1.1.0-amd64-it-it-luciarus-preview`        | 带有区域设置和`it-IT``it-IT-LuciaRUS`语音的容器映像。        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | 带有区域设置和`ja-JP``ja-JP-Ayumi-Apollo`语音的容器映像。    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | 带有区域设置和`ja-JP``ja-JP-HarukaRUS`语音的容器映像。       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | 带有区域设置和`ja-JP``ja-JP-Ichiro-Apollo`语音的容器映像。   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | 带有区域设置和`ko-KR``ko-KR-HeamiRUS`语音的容器映像。        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | 带有区域设置和`pt-BR``pt-BR-Daniel-Apollo`语音的容器映像。   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | 带有区域设置和`pt-BR``pt-BR-HeloisaRUS`语音的容器映像。      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | 带有区域设置和`zh-CN``zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 带有区域设置和`zh-CN``zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | 带有区域设置和`zh-CN``zh-CN-Yaoyao-Apollo`语音的容器映像。   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | 带有区域设置和`en-US``en-US-BenjaminRUS`语音的容器映像。     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | 带有区域设置和`en-US``en-US-Guy24kRUS`语音的容器映像。       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | 带有区域设置和`en-US``en-US-Jessa24kRUS`语音的容器映像。     |
| `1.0.0-amd64-en-us-jessarus-preview`        | 带有区域设置和`en-US``en-US-JessaRUS`语音的容器映像。        |
| `1.0.0-amd64-en-us-zirarus-preview`         | 带有区域设置和`en-US``en-US-ZiraRUS`语音的容器映像。         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | 带有区域设置和`zh-CN``zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 带有区域设置和`zh-CN``zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | 带有区域设置和`zh-CN``zh-CN-Yaoyao-Apollo`语音的容器映像。   |

## <a name="key-phrase-extraction"></a>关键短语提取

[关键短语提取][ta-kp]容器映像可以在`mcr.microsoft.com`容器注册表组中找到。 它驻留在存储库中`azure-cognitive-services`，并命名为`keyphrase`。 完全限定的容器映像名称是`mcr.microsoft.com/azure-cognitive-services/keyphrase`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>语言检测

[语言检测][ta-la]容器映像可以在`mcr.microsoft.com`容器注册表子级中找到。 它驻留在存储库中`azure-cognitive-services`，并命名为`language`。 完全限定的容器映像名称是`mcr.microsoft.com/azure-cognitive-services/language`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>情绪分析

[情绪分析][ta-se]容器映像可以在`mcr.microsoft.com`容器注册表联合体上找到。 它驻留在存储库中`azure-cognitive-services`，并命名为`sentiment`。 完全限定的容器映像名称是`mcr.microsoft.com/azure-cognitive-services/sentiment`。

此容器映像具有以下可用标记：

| 图像标签                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
