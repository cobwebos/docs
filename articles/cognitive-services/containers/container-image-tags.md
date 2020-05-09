---
title: 认知服务容器映像标记
titleSuffix: Azure Cognitive Services
description: 所有认知服务容器图像标记的综合列表。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 850d7b9732ad547d502b042e7eaad5d3b471030e
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82925994"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure 认知服务容器图像标记

Azure 认知服务提供许多容器映像。 容器注册表和相应的存储库在容器映像之间有所不同。 每个容器映像名称提供多个标记。 容器映像标记是对容器映像进行版本控制的机制。 本文旨在用作列出所有认知服务容器映像及其可用标记的综合性参考。

> [!TIP]
> 使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)时，请密切关注容器注册表、存储库、容器映像名称和相应的标记的大小写，因为它们**区分大小写**。

## <a name="anomaly-detector"></a>异常检测器

可以在`containerpreview.azurecr.io`容器注册表中找到[异常探测器][ad-containers]容器映像。 它驻留在`microsoft`存储库中，并`cognitive-services-anomaly-detector`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>计算机视觉

可以[Computer Vision][cv-containers]在`containerpreview.azurecr.io`容器注册表中找到计算机视觉容器映像。 它驻留在`microsoft`存储库中，并`cognitive-services-read`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-read`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>人脸

可以[Face][fa-containers]在`containerpreview.azurecr.io`容器注册表中找到人脸容器映像。 它驻留在`microsoft`存储库中，并`cognitive-services-face`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-face`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
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

可以在`containerpreview.azurecr.io`容器注册表中找到[窗体识别器][fr-containers]容器映像。 它驻留在`microsoft`存储库中，并`cognitive-services-form-recognizer`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>语言理解 (LUIS)

可[LUIS][lu-containers]在`mcr.microsoft.com`容器注册表联合中找到 LUIS 容器映像。 它驻留在`azure-cognitive-services`存储库中，并`luis`命名为。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/luis`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
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

可以在`containerpreview.azurecr.io`容器注册表中找到[自定义语音到文本][sp-cstt]的容器映像。 它驻留在`microsoft`存储库中，并`cognitive-services-custom-speech-to-text`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`。

此容器映像提供以下标记：

| 图像标记            | 说明 |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>自定义文本到语音转换

可以在`containerpreview.azurecr.io`容器注册表中找到[自定义的文本到语音转换][sp-ctts]容器图像。 它驻留在`microsoft`存储库中，并`cognitive-services-custom-text-to-speech`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`。

此容器映像提供以下标记：

| 图像标记            | 说明 |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>语音转文本

可以在`containerpreview.azurecr.io`容器注册表中找到 "[语音到文本][sp-stt]" 容器图像。 它驻留在`microsoft`存储库中，并`cognitive-services-speech-to-text`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`。

此容器映像提供以下标记：

| 图像标记                  | 说明                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | 具有`en-US`区域设置的容器映像。 |
| `2.2.0-amd64-ar-ae-preview` | 具有`ar-AE`区域设置的容器映像。 |
| `2.2.0-amd64-ar-eg-preview` | 具有`ar-EG`区域设置的容器映像。 |
| `2.2.0-amd64-ar-kw-preview` | 具有`ar-KW`区域设置的容器映像。 |
| `2.2.0-amd64-ar-qa-preview` | 具有`ar-QA`区域设置的容器映像。 |
| `2.2.0-amd64-ar-sa-preview` | 具有`ar-SA`区域设置的容器映像。 |
| `2.2.0-amd64-ca-es-preview` | 具有`ca-ES`区域设置的容器映像。 |
| `2.2.0-amd64-da-dk-preview` | 具有`da-DK`区域设置的容器映像。 |
| `2.2.0-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `2.2.0-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `2.2.0-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `2.2.0-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `2.2.0-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `2.2.0-amd64-en-nz-preview` | 具有`en-NZ`区域设置的容器映像。 |
| `2.2.0-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `2.2.0-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `2.2.0-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `2.2.0-amd64-fi-fi-preview` | 具有`fi-FI`区域设置的容器映像。 |
| `2.2.0-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `2.2.0-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `2.2.0-amd64-gu-in-preview` | 具有`gu-IN`区域设置的容器映像。 |
| `2.2.0-amd64-hi-in-preview` | 具有`hi-IN`区域设置的容器映像。 |
| `2.2.0-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `2.2.0-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `2.2.0-amd64-ko-kr-preview` | 具有`ko-KR`区域设置的容器映像。 |
| `2.2.0-amd64-mr-in-preview` | 具有`mr-IN`区域设置的容器映像。 |
| `2.2.0-amd64-nb-no-preview` | 具有`nb-NO`区域设置的容器映像。 |
| `2.2.0-amd64-nl-nl-preview` | 具有`nl-NL`区域设置的容器映像。 |
| `2.2.0-amd64-pl-pl-preview` | 具有`pl-PL`区域设置的容器映像。 |
| `2.2.0-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `2.2.0-amd64-pt-pt-preview` | 具有`pt-PT`区域设置的容器映像。 |
| `2.2.0-amd64-ru-ru-preview` | 具有`ru-RU`区域设置的容器映像。 |
| `2.2.0-amd64-sv-se-preview` | 具有`sv-SE`区域设置的容器映像。 |
| `2.2.0-amd64-ta-in-preview` | 具有`ta-IN`区域设置的容器映像。 |
| `2.2.0-amd64-te-in-preview` | 具有`te-IN`区域设置的容器映像。 |
| `2.2.0-amd64-th-th-preview` | 具有`th-TH`区域设置的容器映像。 |
| `2.2.0-amd64-tr-tr-preview` | 具有`tr-TR`区域设置的容器映像。 |
| `2.2.0-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `2.2.0-amd64-zh-hk-preview` | 具有`zh-HK`区域设置的容器映像。 |
| `2.2.0-amd64-zh-tw-preview` | 具有`zh-TW`区域设置的容器映像。 |
| `2.1.1-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `2.1.1-amd64-ar-ae-preview` | 具有`ar-AE`区域设置的容器映像。 |
| `2.1.1-amd64-ar-eg-preview` | 具有`ar-EG`区域设置的容器映像。 |
| `2.1.1-amd64-ar-kw-preview` | 具有`ar-KW`区域设置的容器映像。 |
| `2.1.1-amd64-ar-qa-preview` | 具有`ar-QA`区域设置的容器映像。 |
| `2.1.1-amd64-ar-sa-preview` | 具有`ar-SA`区域设置的容器映像。 |
| `2.1.1-amd64-ca-es-preview` | 具有`ca-ES`区域设置的容器映像。 |
| `2.1.1-amd64-da-dk-preview` | 具有`da-DK`区域设置的容器映像。 |
| `2.1.1-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `2.1.1-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `2.1.1-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `2.1.1-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `2.1.1-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `2.1.1-amd64-en-nz-preview` | 具有`en-NZ`区域设置的容器映像。 |
| `2.1.1-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `2.1.1-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `2.1.1-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `2.1.1-amd64-fi-fi-preview` | 具有`fi-FI`区域设置的容器映像。 |
| `2.1.1-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `2.1.1-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `2.1.1-amd64-gu-in-preview` | 具有`gu-IN`区域设置的容器映像。 |
| `2.1.1-amd64-hi-in-preview` | 具有`hi-IN`区域设置的容器映像。 |
| `2.1.1-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `2.1.1-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `2.1.1-amd64-ko-kr-preview` | 具有`ko-KR`区域设置的容器映像。 |
| `2.1.1-amd64-mr-in-preview` | 具有`mr-IN`区域设置的容器映像。 |
| `2.1.1-amd64-nb-no-preview` | 具有`nb-NO`区域设置的容器映像。 |
| `2.1.1-amd64-nl-nl-preview` | 具有`nl-NL`区域设置的容器映像。 |
| `2.1.1-amd64-pl-pl-preview` | 具有`pl-PL`区域设置的容器映像。 |
| `2.1.1-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `2.1.1-amd64-pt-pt-preview` | 具有`pt-PT`区域设置的容器映像。 |
| `2.1.1-amd64-ru-ru-preview` | 具有`ru-RU`区域设置的容器映像。 |
| `2.1.1-amd64-sv-se-preview` | 具有`sv-SE`区域设置的容器映像。 |
| `2.1.1-amd64-ta-in-preview` | 具有`ta-IN`区域设置的容器映像。 |
| `2.1.1-amd64-te-in-preview` | 具有`te-IN`区域设置的容器映像。 |
| `2.1.1-amd64-th-th-preview` | 具有`th-TH`区域设置的容器映像。 |
| `2.1.1-amd64-tr-tr-preview` | 具有`tr-TR`区域设置的容器映像。 |
| `2.1.1-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `2.1.1-amd64-zh-hk-preview` | 具有`zh-HK`区域设置的容器映像。 |
| `2.1.1-amd64-zh-tw-preview` | 具有`zh-TW`区域设置的容器映像。 |
| `2.1.0-amd64-ar-ae-preview` | 具有`ar-AE`区域设置的容器映像。 |
| `2.1.0-amd64-ar-eg-preview` | 具有`ar-EG`区域设置的容器映像。 |
| `2.1.0-amd64-ar-kw-preview` | 具有`ar-KW`区域设置的容器映像。 |
| `2.1.0-amd64-ar-qa-preview` | 具有`ar-QA`区域设置的容器映像。 |
| `2.1.0-amd64-ar-sa-preview` | 具有`ar-SA`区域设置的容器映像。 |
| `2.1.0-amd64-ca-es-preview` | 具有`ca-ES`区域设置的容器映像。 |
| `2.1.0-amd64-da-dk-preview` | 具有`da-DK`区域设置的容器映像。 |
| `2.1.0-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `2.1.0-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `2.1.0-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `2.1.0-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `2.1.0-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `2.1.0-amd64-en-nz-preview` | 具有`en-NZ`区域设置的容器映像。 |
| `2.1.0-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `2.1.0-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `2.1.0-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `2.1.0-amd64-fi-fi-preview` | 具有`fi-FI`区域设置的容器映像。 |
| `2.1.0-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `2.1.0-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `2.1.0-amd64-gu-in-preview` | 具有`gu-IN`区域设置的容器映像。 |
| `2.1.0-amd64-hi-in-preview` | 具有`hi-IN`区域设置的容器映像。 |
| `2.1.0-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `2.1.0-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `2.1.0-amd64-ko-kr-preview` | 具有`ko-KR`区域设置的容器映像。 |
| `2.1.0-amd64-mr-in-preview` | 具有`mr-IN`区域设置的容器映像。 |
| `2.1.0-amd64-nb-no-preview` | 具有`nb-NO`区域设置的容器映像。 |
| `2.1.0-amd64-nl-nl-preview` | 具有`nl-NL`区域设置的容器映像。 |
| `2.1.0-amd64-pl-pl-preview` | 具有`pl-PL`区域设置的容器映像。 |
| `2.1.0-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `2.1.0-amd64-pt-pt-preview` | 具有`pt-PT`区域设置的容器映像。 |
| `2.1.0-amd64-ru-ru-preview` | 具有`ru-RU`区域设置的容器映像。 |
| `2.1.0-amd64-sv-se-preview` | 具有`sv-SE`区域设置的容器映像。 |
| `2.1.0-amd64-ta-in-preview` | 具有`ta-IN`区域设置的容器映像。 |
| `2.1.0-amd64-te-in-preview` | 具有`te-IN`区域设置的容器映像。 |
| `2.1.0-amd64-th-th-preview` | 具有`th-TH`区域设置的容器映像。 |
| `2.1.0-amd64-tr-tr-preview` | 具有`tr-TR`区域设置的容器映像。 |
| `2.1.0-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `2.1.0-amd64-zh-hk-preview` | 具有`zh-HK`区域设置的容器映像。 |
| `2.1.0-amd64-zh-tw-preview` | 具有`zh-TW`区域设置的容器映像。 |
| `2.0.3-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `2.0.2-amd64-ar-ae-preview` | 具有`ar-AE`区域设置的容器映像。 |
| `2.0.2-amd64-ar-eg-preview` | 具有`ar-EG`区域设置的容器映像。 |
| `2.0.2-amd64-ar-kw-preview` | 具有`ar-KW`区域设置的容器映像。 |
| `2.0.2-amd64-ar-qa-preview` | 具有`ar-QA`区域设置的容器映像。 |
| `2.0.2-amd64-ar-sa-preview` | 具有`ar-SA`区域设置的容器映像。 |
| `2.0.2-amd64-ca-es-preview` | 具有`ca-ES`区域设置的容器映像。 |
| `2.0.2-amd64-da-dk-preview` | 具有`da-DK`区域设置的容器映像。 |
| `2.0.2-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `2.0.2-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `2.0.2-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `2.0.2-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `2.0.2-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `2.0.2-amd64-en-nz-preview` | 具有`en-NZ`区域设置的容器映像。 |
| `2.0.2-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `2.0.2-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `2.0.2-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `2.0.2-amd64-fi-fi-preview` | 具有`fi-FI`区域设置的容器映像。 |
| `2.0.2-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `2.0.2-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `2.0.2-amd64-gu-in-preview` | 具有`gu-IN`区域设置的容器映像。 |
| `2.0.2-amd64-hi-in-preview` | 具有`hi-IN`区域设置的容器映像。 |
| `2.0.2-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `2.0.2-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `2.0.2-amd64-ko-kr-preview` | 具有`ko-KR`区域设置的容器映像。 |
| `2.0.2-amd64-mr-in-preview` | 具有`mr-IN`区域设置的容器映像。 |
| `2.0.2-amd64-nb-no-preview` | 具有`nb-NO`区域设置的容器映像。 |
| `2.0.2-amd64-nl-nl-preview` | 具有`nl-NL`区域设置的容器映像。 |
| `2.0.2-amd64-pl-pl-preview` | 具有`pl-PL`区域设置的容器映像。 |
| `2.0.2-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `2.0.2-amd64-pt-pt-preview` | 具有`pt-PT`区域设置的容器映像。 |
| `2.0.2-amd64-ru-ru-preview` | 具有`ru-RU`区域设置的容器映像。 |
| `2.0.2-amd64-sv-se-preview` | 具有`sv-SE`区域设置的容器映像。 |
| `2.0.2-amd64-ta-in-preview` | 具有`ta-IN`区域设置的容器映像。 |
| `2.0.2-amd64-te-in-preview` | 具有`te-IN`区域设置的容器映像。 |
| `2.0.2-amd64-th-th-preview` | 具有`th-TH`区域设置的容器映像。 |
| `2.0.2-amd64-tr-tr-preview` | 具有`tr-TR`区域设置的容器映像。 |
| `2.0.2-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `2.0.2-amd64-zh-hk-preview` | 具有`zh-HK`区域设置的容器映像。 |
| `2.0.2-amd64-zh-tw-preview` | 具有`zh-TW`区域设置的容器映像。 |
| `2.0.1-amd64-ar-ae-preview` | 具有`ar-AE`区域设置的容器映像。 |
| `2.0.1-amd64-ar-eg-preview` | 具有`ar-EG`区域设置的容器映像。 |
| `2.0.1-amd64-ar-kw-preview` | 具有`ar-KW`区域设置的容器映像。 |
| `2.0.1-amd64-ar-qa-preview` | 具有`ar-QA`区域设置的容器映像。 |
| `2.0.1-amd64-ar-sa-preview` | 具有`ar-SA`区域设置的容器映像。 |
| `2.0.1-amd64-ca-es-preview` | 具有`ca-ES`区域设置的容器映像。 |
| `2.0.1-amd64-da-dk-preview` | 具有`da-DK`区域设置的容器映像。 |
| `2.0.1-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `2.0.1-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `2.0.1-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `2.0.1-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `2.0.1-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `2.0.1-amd64-en-nz-preview` | 具有`en-NZ`区域设置的容器映像。 |
| `2.0.1-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `2.0.1-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `2.0.1-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `2.0.1-amd64-fi-fi-preview` | 具有`fi-FI`区域设置的容器映像。 |
| `2.0.1-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `2.0.1-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `2.0.1-amd64-gu-in-preview` | 具有`gu-IN`区域设置的容器映像。 |
| `2.0.1-amd64-hi-in-preview` | 具有`hi-IN`区域设置的容器映像。 |
| `2.0.1-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `2.0.1-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `2.0.1-amd64-ko-kr-preview` | 具有`ko-KR`区域设置的容器映像。 |
| `2.0.1-amd64-mr-in-preview` | 具有`mr-IN`区域设置的容器映像。 |
| `2.0.1-amd64-nb-no-preview` | 具有`nb-NO`区域设置的容器映像。 |
| `2.0.1-amd64-nl-nl-preview` | 具有`nl-NL`区域设置的容器映像。 |
| `2.0.1-amd64-pl-pl-preview` | 具有`pl-PL`区域设置的容器映像。 |
| `2.0.1-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `2.0.1-amd64-pt-pt-preview` | 具有`pt-PT`区域设置的容器映像。 |
| `2.0.1-amd64-ru-ru-preview` | 具有`ru-RU`区域设置的容器映像。 |
| `2.0.1-amd64-sv-se-preview` | 具有`sv-SE`区域设置的容器映像。 |
| `2.0.1-amd64-ta-in-preview` | 具有`ta-IN`区域设置的容器映像。 |
| `2.0.1-amd64-te-in-preview` | 具有`te-IN`区域设置的容器映像。 |
| `2.0.1-amd64-th-th-preview` | 具有`th-TH`区域设置的容器映像。 |
| `2.0.1-amd64-tr-tr-preview` | 具有`tr-TR`区域设置的容器映像。 |
| `2.0.1-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `2.0.1-amd64-zh-hk-preview` | 具有`zh-HK`区域设置的容器映像。 |
| `2.0.1-amd64-zh-tw-preview` | 具有`zh-TW`区域设置的容器映像。 |
| `2.0.0-amd64-ar-eg-preview` | 具有`ar-EG`区域设置的容器映像。 |
| `2.0.0-amd64-ca-es-preview` | 具有`ca-ES`区域设置的容器映像。 |
| `2.0.0-amd64-da-dk-preview` | 具有`da-DK`区域设置的容器映像。 |
| `2.0.0-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `2.0.0-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `2.0.0-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `2.0.0-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `2.0.0-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `2.0.0-amd64-en-nz-preview` | 具有`en-NZ`区域设置的容器映像。 |
| `2.0.0-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `2.0.0-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `2.0.0-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `2.0.0-amd64-fi-fi-preview` | 具有`fi-FI`区域设置的容器映像。 |
| `2.0.0-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `2.0.0-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `2.0.0-amd64-hi-in-preview` | 具有`hi-IN`区域设置的容器映像。 |
| `2.0.0-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `2.0.0-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `2.0.0-amd64-ko-kr-preview` | 具有`ko-KR`区域设置的容器映像。 |
| `2.0.0-amd64-nb-no-preview` | 具有`nb-NO`区域设置的容器映像。 |
| `2.0.0-amd64-nl-nl-preview` | 具有`nl-NL`区域设置的容器映像。 |
| `2.0.0-amd64-pl-pl-preview` | 具有`pl-PL`区域设置的容器映像。 |
| `2.0.0-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `2.0.0-amd64-pt-pt-preview` | 具有`pt-PT`区域设置的容器映像。 |
| `2.0.0-amd64-ru-ru-preview` | 具有`ru-RU`区域设置的容器映像。 |
| `2.0.0-amd64-sv-se-preview` | 具有`sv-SE`区域设置的容器映像。 |
| `2.0.0-amd64-th-th-preview` | 具有`th-TH`区域设置的容器映像。 |
| `2.0.0-amd64-tr-tr-preview` | 具有`tr-TR`区域设置的容器映像。 |
| `2.0.0-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `2.0.0-amd64-zh-hk-preview` | 具有`zh-HK`区域设置的容器映像。 |
| `2.0.0-amd64-zh-tw-preview` | 具有`zh-TW`区域设置的容器映像。 |
| `1.2.0-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `1.2.0-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `1.2.0-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `1.2.0-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `1.2.0-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `1.2.0-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `1.2.0-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `1.2.0-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `1.2.0-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `1.2.0-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `1.2.0-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `1.2.0-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `1.2.0-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `1.2.0-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `1.1.3-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `1.1.3-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `1.1.3-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `1.1.3-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `1.1.3-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `1.1.3-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `1.1.3-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `1.1.3-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `1.1.3-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `1.1.3-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `1.1.3-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `1.1.3-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `1.1.3-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `1.1.3-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `1.1.2-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `1.1.2-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `1.1.2-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `1.1.2-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `1.1.2-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `1.1.2-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `1.1.2-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `1.1.2-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `1.1.2-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `1.1.2-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `1.1.2-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `1.1.2-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `1.1.2-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `1.1.2-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `1.1.1-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `1.1.1-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `1.1.1-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `1.1.1-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `1.1.1-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `1.1.1-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `1.1.1-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `1.1.1-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `1.1.1-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `1.1.1-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `1.1.1-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `1.1.1-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `1.1.1-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `1.1.1-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `1.1.0-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `1.1.0-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `1.1.0-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `1.1.0-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `1.1.0-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `1.1.0-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `1.1.0-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `1.1.0-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `1.1.0-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `1.1.0-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `1.1.0-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `1.1.0-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `1.1.0-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `1.1.0-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |
| `1.0.0-amd64-de-de-preview` | 具有`de-DE`区域设置的容器映像。 |
| `1.0.0-amd64-en-au-preview` | 具有`en-AU`区域设置的容器映像。 |
| `1.0.0-amd64-en-ca-preview` | 具有`en-CA`区域设置的容器映像。 |
| `1.0.0-amd64-en-gb-preview` | 具有`en-GB`区域设置的容器映像。 |
| `1.0.0-amd64-en-in-preview` | 具有`en-IN`区域设置的容器映像。 |
| `1.0.0-amd64-en-us-preview` | 具有`en-US`区域设置的容器映像。 |
| `1.0.0-amd64-es-es-preview` | 具有`es-ES`区域设置的容器映像。 |
| `1.0.0-amd64-es-mx-preview` | 具有`es-MX`区域设置的容器映像。 |
| `1.0.0-amd64-fr-ca-preview` | 具有`fr-CA`区域设置的容器映像。 |
| `1.0.0-amd64-fr-fr-preview` | 具有`fr-FR`区域设置的容器映像。 |
| `1.0.0-amd64-it-it-preview` | 具有`it-IT`区域设置的容器映像。 |
| `1.0.0-amd64-ja-jp-preview` | 具有`ja-JP`区域设置的容器映像。 |
| `1.0.0-amd64-pt-br-preview` | 具有`pt-BR`区域设置的容器映像。 |
| `1.0.0-amd64-zh-cn-preview` | 具有`zh-CN`区域设置的容器映像。 |

## <a name="text-to-speech"></a>文本转语音

可以在`containerpreview.azurecr.io`容器注册表中找到[文本到语音的][sp-tts]容器图像。 它驻留在`microsoft`存储库中，并`cognitive-services-text-to-speech`命名为。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`。

此容器映像提供以下标记：

| 图像标记                                  | 说明                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有区域设置和`en-US` `en-US-JessaRUS`语音的容器映像。        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | 具有区域设置和`ar-EG` `ar-EG-Hoda`语音的容器映像。            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | 具有区域设置和`ar-SA` `ar-SA-Naayf`语音的容器映像。           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | 具有区域设置和`bg-BG` `bg-BG-Ivan`语音的容器映像。            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | 具有区域设置和`ca-ES` `ca-ES-HerenaRUS`语音的容器映像。       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | 具有区域设置和`cs-CZ` `cs-CZ-Jakub`语音的容器映像。           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | 具有区域设置和`da-DK` `da-DK-HelleRUS`语音的容器映像。        |
| `1.3.0-amd64-de-at-michael-preview`         | 具有区域设置和`de-AT` `de-AT-Michael`语音的容器映像。         |
| `1.3.0-amd64-de-ch-karsten-preview`         | 具有区域设置和`de-CH` `de-CH-Karsten`语音的容器映像。         |
| `1.3.0-amd64-de-de-hedda-preview`           | 具有区域设置和`de-DE` `de-DE-Hedda`语音的容器映像。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 具有区域设置和`de-DE` `de-DE-Hedda`语音的容器映像。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 具有区域设置和`de-DE` `de-DE-HeddaRUS`语音的容器映像。        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | 具有区域设置和`de-DE` `de-DE-Stefan-Apollo`语音的容器映像。   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | 具有区域设置和`el-GR` `el-GR-Stefanos`语音的容器映像。        |
| `1.3.0-amd64-en-au-catherine-preview`       | 具有区域设置和`en-AU` `en-AU-Catherine`语音的容器映像。       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | 具有区域设置和`en-AU` `en-AU-HayleyRUS`语音的容器映像。       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | 具有区域设置和`en-CA` `en-CA-HeatherRUS`语音的容器映像。      |
| `1.3.0-amd64-en-ca-linda-preview`           | 具有区域设置和`en-CA` `en-CA-Linda`语音的容器映像。           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | 具有区域设置和`en-GB` `en-GB-George-Apollo`语音的容器映像。   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | 具有区域设置和`en-GB` `en-GB-HazelRUS`语音的容器映像。        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | 具有区域设置和`en-GB` `en-GB-Susan-Apollo`语音的容器映像。    |
| `1.3.0-amd64-en-ie-sean-preview`            | 具有区域设置和`en-IE` `en-IE-Sean`语音的容器映像。            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | 具有区域设置和`en-IN` `en-IN-Heera-Apollo`语音的容器映像。    |
| `1.3.0-amd64-en-in-priyarus-preview`        | 具有区域设置和`en-IN` `en-IN-PriyaRUS`语音的容器映像。        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | 具有区域设置和`en-IN` `en-IN-Ravi-Apollo`语音的容器映像。     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | 具有区域设置和`en-US` `en-US-BenjaminRUS`语音的容器映像。     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | 具有区域设置和`en-US` `en-US-Guy24kRUS`语音的容器映像。       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | 具有区域设置和`en-US` `en-US-Jessa24kRUS`语音的容器映像。     |
| `1.3.0-amd64-en-us-jessarus-preview`        | 具有区域设置和`en-US` `en-US-JessaRUS`语音的容器映像。        |
| `1.3.0-amd64-en-us-zirarus-preview`         | 具有区域设置和`en-US` `en-US-ZiraRUS`语音的容器映像。         |
| `1.3.0-amd64-es-es-helenarus-preview`       | 具有区域设置和`es-ES` `es-ES-HelenaRUS`语音的容器映像。       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | 具有区域设置和`es-ES` `es-ES-Laura-Apollo`语音的容器映像。    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | 具有区域设置和`es-ES` `es-ES-Pablo-Apollo`语音的容器映像。    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | 具有区域设置和`es-MX` `es-MX-HildaRUS`语音的容器映像。        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | 具有区域设置和`es-MX` `es-MX-Raul-Apollo`语音的容器映像。     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | 具有区域设置和`fi-FI` `fi-FI-HeidiRUS`语音的容器映像。        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | 具有区域设置和`fr-CA` `fr-CA-Caroline`语音的容器映像。        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | 具有区域设置和`fr-CA` `fr-CA-HarmonieRUS`语音的容器映像。     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | 具有区域设置和`fr-CH` `fr-CH-Guillaume`语音的容器映像。       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | 具有区域设置和`fr-FR` `fr-FR-HortenseRUS`语音的容器映像。     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | 具有区域设置和`fr-FR` `fr-FR-Julie-Apollo`语音的容器映像。    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | 具有区域设置和`fr-FR` `fr-FR-Paul-Apollo`语音的容器映像。     |
| `1.3.0-amd64-he-il-asaf-preview`            | 具有区域设置和`he-IL` `he-IL-Asaf`语音的容器映像。            |
| `1.3.0-amd64-hi-in-hemant-preview`          | 具有区域设置和`hi-IN` `hi-IN-Hemant`语音的容器映像。          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 具有区域设置和`hi-IN` `hi-IN-Kalpana-Apollo`语音的容器映像。  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 具有区域设置和`hi-IN` `hi-IN-Kalpana`语音的容器映像。         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 具有区域设置和`hi-IN` `hi-IN-Kalpana`语音的容器映像。         |
| `1.3.0-amd64-hr-hr-matej-preview`           | 具有区域设置和`hr-HR` `hr-HR-Matej`语音的容器映像。           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | 具有区域设置和`hu-HU` `hu-HU-Szabolcs`语音的容器映像。        |
| `1.3.0-amd64-id-id-andika-preview`          | 具有区域设置和`id-ID` `id-ID-Andika`语音的容器映像。          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | 具有区域设置和`it-IT` `it-IT-Cosimo-Apollo`语音的容器映像。   |
| `1.3.0-amd64-it-it-luciarus-preview`        | 具有区域设置和`it-IT` `it-IT-LuciaRUS`语音的容器映像。        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有区域设置和`ja-JP` `ja-JP-Ayumi-Apollo`语音的容器映像。    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | 具有区域设置和`ja-JP` `ja-JP-HarukaRUS`语音的容器映像。       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有区域设置和`ja-JP` `ja-JP-Ichiro-Apollo`语音的容器映像。   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | 具有区域设置和`ko-KR` `ko-KR-HeamiRUS`语音的容器映像。        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | 具有区域设置和`ms-MY` `ms-MY-Rizwan`语音的容器映像。          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | 具有区域设置和`nb-NO` `nb-NO-HuldaRUS`语音的容器映像。        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | 具有区域设置和`nl-NL` `nl-NL-HannaRUS`语音的容器映像。        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | 具有区域设置和`pl-PL` `pl-PL-PaulinaRUS`语音的容器映像。      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | 具有区域设置和`pt-BR` `pt-BR-Daniel-Apollo`语音的容器映像。   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | 具有区域设置和`pt-BR` `pt-BR-HeloisaRUS`语音的容器映像。      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | 具有区域设置和`pt-PT` `pt-PT-HeliaRUS`语音的容器映像。        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | 具有区域设置和`ro-RO` `ro-RO-Andrei`语音的容器映像。          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | 具有区域设置和`ru-RU` `ru-RU-EkaterinaRUS`语音的容器映像。    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | 具有区域设置和`ru-RU` `ru-RU-Irina-Apollo`语音的容器映像。    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | 具有区域设置和`ru-RU` `ru-RU-Pavel-Apollo`语音的容器映像。    |
| `1.3.0-amd64-sk-sk-filip-preview`           | 具有区域设置和`sk-SK` `sk-SK-Filip`语音的容器映像。           |
| `1.3.0-amd64-sl-si-lado-preview`            | 具有区域设置和`sl-SI` `sl-SI-Lado`语音的容器映像。            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | 具有区域设置和`sv-SE` `sv-SE-HedvigRUS`语音的容器映像。       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | 具有区域设置和`ta-IN` `ta-IN-Valluvar`语音的容器映像。        |
| `1.3.0-amd64-te-in-chitra-preview`          | 具有区域设置和`te-IN` `te-IN-Chitra`语音的容器映像。          |
| `1.3.0-amd64-th-th-pattara-preview`         | 具有区域设置和`th-TH` `th-TH-Pattara`语音的容器映像。         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | 具有区域设置和`tr-TR` `tr-TR-SedaRUS`语音的容器映像。         |
| `1.3.0-amd64-vi-vn-an-preview`              | 具有区域设置和`vi-VN` `vi-VN-An`语音的容器映像。              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | 具有区域设置和`zh-CN` `zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 具有区域设置和`zh-CN` `zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有区域设置和`zh-CN` `zh-CN-Yaoyao-Apollo`语音的容器映像。   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | 具有区域设置和`zh-HK` `zh-HK-Danny-Apollo`语音的容器映像。    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | 具有区域设置和`zh-HK` `zh-HK-Tracy-Apollo`语音的容器映像。    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | 具有区域设置和`zh-HK` `zh-HK-TracyRUS`语音的容器映像。        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | 具有区域设置和`zh-TW` `zh-TW-HanHanRUS`语音的容器映像。       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | 具有区域设置和`zh-TW` `zh-TW-Yating-Apollo`语音的容器映像。   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有区域设置和`zh-TW` `zh-TW-Zhiwei-Apollo`语音的容器映像。   |
| `1.2.0-amd64-de-de-heddarus-preview`        | 具有区域设置和`de-DE` `de-DE-Hedda`语音的容器映像。           |
| `1.2.0-amd64-de-de-heddarus-preview`        | 具有区域设置和`de-DE` `de-DE-HeddaRUS`语音的容器映像。        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | 具有区域设置和`de-DE` `de-DE-Stefan-Apollo`语音的容器映像。   |
| `1.2.0-amd64-en-au-catherine-preview`       | 具有区域设置和`en-AU` `en-AU-Catherine`语音的容器映像。       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | 具有区域设置和`en-AU` `en-AU-HayleyRUS`语音的容器映像。       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | 具有区域设置和`en-GB` `en-GB-George-Apollo`语音的容器映像。   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | 具有区域设置和`en-GB` `en-GB-HazelRUS`语音的容器映像。        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | 具有区域设置和`en-GB` `en-GB-Susan-Apollo`语音的容器映像。    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | 具有区域设置和`en-IN` `en-IN-Heera-Apollo`语音的容器映像。    |
| `1.2.0-amd64-en-in-priyarus-preview`        | 具有区域设置和`en-IN` `en-IN-PriyaRUS`语音的容器映像。        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | 具有区域设置和`en-IN` `en-IN-Ravi-Apollo`语音的容器映像。     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | 具有区域设置和`en-US` `en-US-BenjaminRUS`语音的容器映像。     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | 具有区域设置和`en-US` `en-US-Guy24kRUS`语音的容器映像。       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | 具有区域设置和`en-US` `en-US-Jessa24kRUS`语音的容器映像。     |
| `1.2.0-amd64-en-us-jessarus-preview`        | 具有区域设置和`en-US` `en-US-JessaRUS`语音的容器映像。        |
| `1.2.0-amd64-en-us-zirarus-preview`         | 具有区域设置和`en-US` `en-US-ZiraRUS`语音的容器映像。         |
| `1.2.0-amd64-es-es-helenarus-preview`       | 具有区域设置和`es-ES` `es-ES-HelenaRUS`语音的容器映像。       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | 具有区域设置和`es-ES` `es-ES-Laura-Apollo`语音的容器映像。    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | 具有区域设置和`es-ES` `es-ES-Pablo-Apollo`语音的容器映像。    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | 具有区域设置和`es-MX` `es-MX-HildaRUS`语音的容器映像。        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | 具有区域设置和`es-MX` `es-MX-Raul-Apollo`语音的容器映像。     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | 具有区域设置和`fr-CA` `fr-CA-Caroline`语音的容器映像。        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | 具有区域设置和`fr-CA` `fr-CA-HarmonieRUS`语音的容器映像。     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | 具有区域设置和`fr-FR` `fr-FR-HortenseRUS`语音的容器映像。     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | 具有区域设置和`fr-FR` `fr-FR-Julie-Apollo`语音的容器映像。    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | 具有区域设置和`fr-FR` `fr-FR-Paul-Apollo`语音的容器映像。     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | 具有区域设置和`it-IT` `it-IT-Cosimo-Apollo`语音的容器映像。   |
| `1.2.0-amd64-it-it-luciarus-preview`        | 具有区域设置和`it-IT` `it-IT-LuciaRUS`语音的容器映像。        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有区域设置和`ja-JP` `ja-JP-Ayumi-Apollo`语音的容器映像。    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | 具有区域设置和`ja-JP` `ja-JP-HarukaRUS`语音的容器映像。       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有区域设置和`ja-JP` `ja-JP-Ichiro-Apollo`语音的容器映像。   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | 具有区域设置和`ko-KR` `ko-KR-HeamiRUS`语音的容器映像。        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | 具有区域设置和`pt-BR` `pt-BR-Daniel-Apollo`语音的容器映像。   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | 具有区域设置和`pt-BR` `pt-BR-HeloisaRUS`语音的容器映像。      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | 具有区域设置和`zh-CN` `zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 具有区域设置和`zh-CN` `zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有区域设置和`zh-CN` `zh-CN-Yaoyao-Apollo`语音的容器映像。   |
| `1.1.0-amd64-de-de-hedda-preview`           | 具有区域设置和`de-DE` `de-DE-Hedda`语音的容器映像。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 具有区域设置和`de-DE` `de-DE-Hedda`语音的容器映像。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 具有区域设置和`de-DE` `de-DE-HeddaRUS`语音的容器映像。        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | 具有区域设置和`de-DE` `de-DE-Stefan-Apollo`语音的容器映像。   |
| `1.1.0-amd64-en-au-catherine-preview`       | 具有区域设置和`en-AU` `en-AU-Catherine`语音的容器映像。       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | 具有区域设置和`en-AU` `en-AU-HayleyRUS`语音的容器映像。       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | 具有区域设置和`en-GB` `en-GB-George-Apollo`语音的容器映像。   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | 具有区域设置和`en-GB` `en-GB-HazelRUS`语音的容器映像。        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | 具有区域设置和`en-GB` `en-GB-Susan-Apollo`语音的容器映像。    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | 具有区域设置和`en-IN` `en-IN-Heera-Apollo`语音的容器映像。    |
| `1.1.0-amd64-en-in-priyarus-preview`        | 具有区域设置和`en-IN` `en-IN-PriyaRUS`语音的容器映像。        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | 具有区域设置和`en-IN` `en-IN-Ravi-Apollo`语音的容器映像。     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | 具有区域设置和`en-US` `en-US-BenjaminRUS`语音的容器映像。     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | 具有区域设置和`en-US` `en-US-Guy24kRUS`语音的容器映像。       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | 具有区域设置和`en-US` `en-US-Jessa24kRUS`语音的容器映像。     |
| `1.1.0-amd64-en-us-jessarus-preview`        | 具有区域设置和`en-US` `en-US-JessaRUS`语音的容器映像。        |
| `1.1.0-amd64-en-us-zirarus-preview`         | 具有区域设置和`en-US` `en-US-ZiraRUS`语音的容器映像。         |
| `1.1.0-amd64-es-es-helenarus-preview`       | 具有区域设置和`es-ES` `es-ES-HelenaRUS`语音的容器映像。       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | 具有区域设置和`es-ES` `es-ES-Laura-Apollo`语音的容器映像。    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | 具有区域设置和`es-ES` `es-ES-Pablo-Apollo`语音的容器映像。    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | 具有区域设置和`es-MX` `es-MX-HildaRUS`语音的容器映像。        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | 具有区域设置和`es-MX` `es-MX-Raul-Apollo`语音的容器映像。     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | 具有区域设置和`fr-CA` `fr-CA-Caroline`语音的容器映像。        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | 具有区域设置和`fr-CA` `fr-CA-HarmonieRUS`语音的容器映像。     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | 具有区域设置和`fr-FR` `fr-FR-HortenseRUS`语音的容器映像。     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | 具有区域设置和`fr-FR` `fr-FR-Julie-Apollo`语音的容器映像。    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | 具有区域设置和`fr-FR` `fr-FR-Paul-Apollo`语音的容器映像。     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | 具有区域设置和`it-IT` `it-IT-Cosimo-Apollo`语音的容器映像。   |
| `1.1.0-amd64-it-it-luciarus-preview`        | 具有区域设置和`it-IT` `it-IT-LuciaRUS`语音的容器映像。        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有区域设置和`ja-JP` `ja-JP-Ayumi-Apollo`语音的容器映像。    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | 具有区域设置和`ja-JP` `ja-JP-HarukaRUS`语音的容器映像。       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有区域设置和`ja-JP` `ja-JP-Ichiro-Apollo`语音的容器映像。   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | 具有区域设置和`ko-KR` `ko-KR-HeamiRUS`语音的容器映像。        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | 具有区域设置和`pt-BR` `pt-BR-Daniel-Apollo`语音的容器映像。   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | 具有区域设置和`pt-BR` `pt-BR-HeloisaRUS`语音的容器映像。      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | 具有区域设置和`zh-CN` `zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 具有区域设置和`zh-CN` `zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有区域设置和`zh-CN` `zh-CN-Yaoyao-Apollo`语音的容器映像。   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | 具有区域设置和`en-US` `en-US-BenjaminRUS`语音的容器映像。     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | 具有区域设置和`en-US` `en-US-Guy24kRUS`语音的容器映像。       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | 具有区域设置和`en-US` `en-US-Jessa24kRUS`语音的容器映像。     |
| `1.0.0-amd64-en-us-jessarus-preview`        | 具有区域设置和`en-US` `en-US-JessaRUS`语音的容器映像。        |
| `1.0.0-amd64-en-us-zirarus-preview`         | 具有区域设置和`en-US` `en-US-ZiraRUS`语音的容器映像。         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | 具有区域设置和`zh-CN` `zh-CN-HuihuiRUS`语音的容器映像。       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 具有区域设置和`zh-CN` `zh-CN-Kangkang-Apollo`语音的容器映像。 |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有区域设置和`zh-CN` `zh-CN-Yaoyao-Apollo`语音的容器映像。   |

## <a name="key-phrase-extraction"></a>关键短语提取

可以[Key Phrase Extraction][ta-kp]在容器注册表 " `mcr.microsoft.com`联合" 中找到关键短语提取容器映像。 它驻留在`azure-cognitive-services`存储库中，并`keyphrase`命名为。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/keyphrase`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>语言检测

可以[Language Detection][ta-la]在容器注册表 " `mcr.microsoft.com`联合" 中找到语言检测容器映像。 它驻留在`azure-cognitive-services`存储库中，并`language`命名为。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/language`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>情绪分析

可以[Sentiment Analysis][ta-se]在容器注册表 " `mcr.microsoft.com`联合" 中找到情绪分析容器映像。 它驻留在`azure-cognitive-services`存储库中，并`sentiment`命名为。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/sentiment`。

此容器映像提供以下标记：

| 图像标记                    | 说明 |
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
