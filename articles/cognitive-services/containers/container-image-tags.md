---
title: 认知服务容器映像标记
titleSuffix: Azure Cognitive Services
description: 所有认知服务容器图像标记的综合列表。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 2a24433389e738bf5d0ecb7ecac6bf369c8ba183
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369478"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure 认知服务容器图像标记

Azure 认知服务提供许多容器映像。 容器注册表和相应的存储库在容器映像之间有所不同。 每个容器映像名称提供多个标记。 容器映像标记是对容器映像进行版本控制的机制。 本文旨在用作列出所有认知服务容器映像及其可用标记的综合性参考。

> [!TIP]
> 使用时 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) ，请密切关注容器注册表、存储库、容器映像名称和相应的标记的大小写，因为它们 **区分大小写**。

## <a name="anomaly-detector"></a>异常检测器

可以在容器注册表联合中找到 [异常探测器][ad-containers] 容器映像 `mcr.microsoft.com` 。 它驻留在 `azure-cognitive-services` 存储库中，并命名为 `anomaly-detector` 。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` 。

此容器映像提供以下标记：

| 图像标记                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>计算机视觉

可以在容器注册表中找到 [计算机视觉][cv-containers] 读取 OCR 容器映像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-read` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-read` 。

此容器映像提供以下标记：

| 图像标记                    | 注意 |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | •进一步降低容器的内存使用率。 |
|                                          | •多箱安装程序需要外部缓存。 例如，设置 Redis 用于缓存。 |
|                                          | •当 Redis 缓存设置为0时，修复缺少问题的结果。  |
|                                          | •删除26MB 的请求正文大小限制。 容器现在可以接受 >26MB 文件。  |
|                                          | •向控制台日志记录添加时间戳和生成版本。  |
| `1.1.013050001-amd64-preview`            | * 添加了 ReadEngineConfig： ResultExpirationPeriod 容器初始化配置，用于指定系统何时应清除识别结果。 |
|                                          | 设置以小时为单位，默认值为48hr。   |
|                                          |   此设置可以减少结果存储的内存使用情况，尤其是在使用容器内存中存储时。  |
|                                          |    * 示例1。 ReadEngineConfig： ResultExpirationPeriod = 1，系统将在进程后清除识别结果1hr。   |
|                                          |    * 示例2。 ReadEngineConfig： ResultExpirationPeriod = 0，系统将在结果检索后清除识别结果。  |
|                                          | 修复了在将无效图像格式传入系统时出现500内部服务器错误。 它现在将返回400错误：   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>人脸

可以在容器注册表中找到人 [脸][fa-containers] 容器映像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-face` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-face` 。

此容器映像提供以下标记：

| 图像标记                    | 注意 |
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

可以在容器注册表中找到 [窗体识别器][fr-containers] 容器映像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-form-recognizer` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` 。

此容器映像提供以下标记：

| 图像标记                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>语言理解 (LUIS)

可在容器注册表联合中找到 [LUIS][lu-containers] 容器映像 `mcr.microsoft.com` 。 它驻留在 `azure-cognitive-services` 存储库中，并命名为 `luis` 。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/luis` 。

此容器映像提供以下标记：

| 图像标记                    | 注意 |
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

可以在容器注册表中找到 [自定义语音到文本][sp-cstt] 的容器映像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-custom-speech-to-text` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` 。

此容器映像提供以下标记：

| 图像标记            | 注意 |
|-----------------------|:------|
| `latest`              |       |
| `2.5.0-amd64`         |       |
| `2.4.0-amd64-preview` |       |
| `2.3.1-amd64-preview` |       | 
| `2.3.0-amd64-preview` |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>自定义文本到语音转换

可以在容器注册表中找到 [自定义的文本到语音转换][sp-ctts] 容器图像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-custom-text-to-speech` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` 。

此容器映像提供以下标记：

| 图像标记            | 注意 |
|-----------------------|:------|
| `latest`              |       |
| `1.7.0-amd64`         |       |
| `1.6.0-amd64-preview` |       |
| `1.6.0-amd64-preview` |       |
| `1.5.0-amd64-preview` |       |
| `1.4.0-amd64-preview` |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>语音转文本

可以在容器注册表中找到 " [语音到文本][sp-stt] " 容器图像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-speech-to-text` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` 。
*美国政府弗吉尼亚州*支持语音到文本的2.5.0 图像。 请使用 *美国政府弗吉尼亚* 帐单终结点和 api 密钥进行尝试。

此容器映像提供以下标记：

| 图像标记                  | 注意                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | 具有区域设置的容器映像 `en-US` 。 |
| `2.5.0-amd64-ar-ae`         | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.5.0-amd64-ar-eg`         | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.5.0-amd64-ar-kw`         | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.5.0-amd64-ar-qa`         | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.5.0-amd64-ar-sa`         | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.5.0-amd64-ca-es`         | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.5.0-amd64-da-dk`         | 具有区域设置的容器映像 `da-DK` 。 |
| `2.5.0-amd64-de-de`         | 具有区域设置的容器映像 `de-DE` 。 |
| `2.5.0-amd64-en-au`         | 具有区域设置的容器映像 `en-AU` 。 |
| `2.5.0-amd64-en-ca`         | 具有区域设置的容器映像 `en-CA` 。 |
| `2.5.0-amd64-en-gb`         | 具有区域设置的容器映像 `en-GB` 。 |
| `2.5.0-amd64-en-in`         | 具有区域设置的容器映像 `en-IN` 。 |
| `2.5.0-amd64-en-nz`         | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.5.0-amd64-en-us`         | 具有区域设置的容器映像 `en-US` 。 |
| `2.5.0-amd64-es-es`         | 具有区域设置的容器映像 `es-ES` 。 |
| `2.5.0-amd64-es-mx`         | 具有区域设置的容器映像 `es-MX` 。 |
| `2.5.0-amd64-fi-fi`         | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.5.0-amd64-fr-ca`         | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.5.0-amd64-fr-fr`         | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.5.0-amd64-gu-in`         | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.5.0-amd64-hi-in`         | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.5.0-amd64-it-it`         | 具有区域设置的容器映像 `it-IT` 。 |
| `2.5.0-amd64-ja-jp`         | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.5.0-amd64-ko-kr`         | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.5.0-amd64-mr-in`         | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.5.0-amd64-nb-no`         | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.5.0-amd64-nl-nl`         | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.5.0-amd64-pl-pl`         | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.5.0-amd64-pt-br`         | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.5.0-amd64-pt-pt`         | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.5.0-amd64-ru-ru`         | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.5.0-amd64-sv-se`         | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.5.0-amd64-ta-in`         | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.5.0-amd64-te-in`         | 具有区域设置的容器映像 `te-IN` 。 |
| `2.5.0-amd64-th-th`         | 具有区域设置的容器映像 `th-TH` 。 |
| `2.5.0-amd64-tr-tr`         | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.5.0-amd64-zh-cn`         | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.5.0-amd64-zh-hk`         | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.5.0-amd64-zh-tw`         | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.4.0-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.4.0-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.4.0-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.4.0-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.4.0-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.4.0-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.4.0-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.4.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.4.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.4.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.4.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.4.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.4.0-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.4.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.4.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.4.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.4.0-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.4.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.4.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.4.0-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.4.0-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.4.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.4.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.4.0-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.4.0-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.4.0-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.4.0-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.4.0-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.4.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.4.0-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.4.0-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.4.0-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.4.0-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.4.0-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.4.0-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.4.0-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.4.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.4.0-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.4.0-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.3.1-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.3.1-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.3.1-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.3.1-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.3.1-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.3.1-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.3.1-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.3.1-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.3.1-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.3.1-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.3.1-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.3.1-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.3.1-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.3.1-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.3.1-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.3.1-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.3.1-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.3.1-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.3.1-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.3.1-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.3.1-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.3.1-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.3.1-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.3.1-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.3.1-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.3.1-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.3.1-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.3.1-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.3.1-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.3.1-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.3.1-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.3.1-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.3.1-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.3.1-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.3.1-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.3.1-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.3.1-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.3.1-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.3.1-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.3.0-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.3.0-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.3.0-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.3.0-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.3.0-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.3.0-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.3.0-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.3.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.3.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.3.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.3.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.3.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.3.0-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.3.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.3.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.3.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.3.0-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.3.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.3.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.3.0-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.3.0-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.3.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.3.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.3.0-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.3.0-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.3.0-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.3.0-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.3.0-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.3.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.3.0-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.3.0-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.3.0-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.3.0-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.3.0-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.3.0-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.3.0-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.3.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.3.0-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.3.0-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.2.0-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.2.0-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.2.0-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.2.0-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.2.0-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.2.0-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.2.0-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.2.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.2.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.2.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.2.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.2.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.2.0-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.2.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.2.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.2.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.2.0-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.2.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.2.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.2.0-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.2.0-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.2.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.2.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.2.0-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.2.0-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.2.0-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.2.0-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.2.0-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.2.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.2.0-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.2.0-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.2.0-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.2.0-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.2.0-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.2.0-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.2.0-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.2.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.2.0-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.2.0-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.1.1-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.1.1-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.1.1-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.1.1-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.1.1-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.1.1-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.1.1-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.1.1-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.1.1-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.1.1-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.1.1-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.1.1-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.1.1-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.1.1-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.1.1-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.1.1-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.1.1-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.1.1-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.1.1-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.1.1-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.1.1-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.1.1-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.1.1-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.1.1-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.1.1-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.1.1-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.1.1-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.1.1-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.1.1-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.1.1-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.1.1-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.1.1-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.1.1-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.1.1-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.1.1-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.1.1-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.1.1-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.1.1-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.1.1-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.1.1-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.1.0-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.1.0-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.1.0-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.1.0-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.1.0-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.1.0-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.1.0-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.1.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.1.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.1.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.1.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.1.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.1.0-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.1.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.1.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.1.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.1.0-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.1.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.1.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.1.0-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.1.0-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.1.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.1.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.1.0-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.1.0-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.1.0-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.1.0-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.1.0-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.1.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.1.0-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.1.0-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.1.0-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.1.0-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.1.0-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.1.0-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.1.0-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.1.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.1.0-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.1.0-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.0.3-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.0.2-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.0.2-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.0.2-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.0.2-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.0.2-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.0.2-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.0.2-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.0.2-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.0.2-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.0.2-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.0.2-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.0.2-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.0.2-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.0.2-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.0.2-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.0.2-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.0.2-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.0.2-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.0.2-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.0.2-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.0.2-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.0.2-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.0.2-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.0.2-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.0.2-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.0.2-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.0.2-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.0.2-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.0.2-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.0.2-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.0.2-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.0.2-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.0.2-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.0.2-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.0.2-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.0.2-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.0.2-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.0.2-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.0.2-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.0.1-amd64-ar-ae-preview` | 具有区域设置的容器映像 `ar-AE` 。 |
| `2.0.1-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.0.1-amd64-ar-kw-preview` | 具有区域设置的容器映像 `ar-KW` 。 |
| `2.0.1-amd64-ar-qa-preview` | 具有区域设置的容器映像 `ar-QA` 。 |
| `2.0.1-amd64-ar-sa-preview` | 具有区域设置的容器映像 `ar-SA` 。 |
| `2.0.1-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.0.1-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.0.1-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.0.1-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.0.1-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.0.1-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.0.1-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.0.1-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.0.1-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.0.1-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.0.1-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.0.1-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.0.1-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.0.1-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.0.1-amd64-gu-in-preview` | 具有区域设置的容器映像 `gu-IN` 。 |
| `2.0.1-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.0.1-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.0.1-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.0.1-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.0.1-amd64-mr-in-preview` | 具有区域设置的容器映像 `mr-IN` 。 |
| `2.0.1-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.0.1-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.0.1-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.0.1-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.0.1-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.0.1-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.0.1-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.0.1-amd64-ta-in-preview` | 具有区域设置的容器映像 `ta-IN` 。 |
| `2.0.1-amd64-te-in-preview` | 具有区域设置的容器映像 `te-IN` 。 |
| `2.0.1-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.0.1-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.0.1-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.0.1-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.0.1-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `2.0.0-amd64-ar-eg-preview` | 具有区域设置的容器映像 `ar-EG` 。 |
| `2.0.0-amd64-ca-es-preview` | 具有区域设置的容器映像 `ca-ES` 。 |
| `2.0.0-amd64-da-dk-preview` | 具有区域设置的容器映像 `da-DK` 。 |
| `2.0.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `2.0.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `2.0.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `2.0.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `2.0.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `2.0.0-amd64-en-nz-preview` | 具有区域设置的容器映像 `en-NZ` 。 |
| `2.0.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `2.0.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `2.0.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `2.0.0-amd64-fi-fi-preview` | 具有区域设置的容器映像 `fi-FI` 。 |
| `2.0.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `2.0.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `2.0.0-amd64-hi-in-preview` | 具有区域设置的容器映像 `hi-IN` 。 |
| `2.0.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `2.0.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `2.0.0-amd64-ko-kr-preview` | 具有区域设置的容器映像 `ko-KR` 。 |
| `2.0.0-amd64-nb-no-preview` | 具有区域设置的容器映像 `nb-NO` 。 |
| `2.0.0-amd64-nl-nl-preview` | 具有区域设置的容器映像 `nl-NL` 。 |
| `2.0.0-amd64-pl-pl-preview` | 具有区域设置的容器映像 `pl-PL` 。 |
| `2.0.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `2.0.0-amd64-pt-pt-preview` | 具有区域设置的容器映像 `pt-PT` 。 |
| `2.0.0-amd64-ru-ru-preview` | 具有区域设置的容器映像 `ru-RU` 。 |
| `2.0.0-amd64-sv-se-preview` | 具有区域设置的容器映像 `sv-SE` 。 |
| `2.0.0-amd64-th-th-preview` | 具有区域设置的容器映像 `th-TH` 。 |
| `2.0.0-amd64-tr-tr-preview` | 具有区域设置的容器映像 `tr-TR` 。 |
| `2.0.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `2.0.0-amd64-zh-hk-preview` | 具有区域设置的容器映像 `zh-HK` 。 |
| `2.0.0-amd64-zh-tw-preview` | 具有区域设置的容器映像 `zh-TW` 。 |
| `1.2.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `1.2.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `1.2.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `1.2.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `1.2.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `1.2.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `1.2.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `1.2.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `1.2.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `1.2.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `1.2.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `1.2.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `1.2.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `1.2.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `1.1.3-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `1.1.3-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `1.1.3-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `1.1.3-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `1.1.3-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `1.1.3-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `1.1.3-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `1.1.3-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `1.1.3-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `1.1.3-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `1.1.3-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `1.1.3-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `1.1.3-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `1.1.3-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `1.1.2-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `1.1.2-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `1.1.2-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `1.1.2-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `1.1.2-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `1.1.2-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `1.1.2-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `1.1.2-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `1.1.2-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `1.1.2-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `1.1.2-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `1.1.2-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `1.1.2-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `1.1.2-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `1.1.1-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `1.1.1-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `1.1.1-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `1.1.1-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `1.1.1-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `1.1.1-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `1.1.1-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `1.1.1-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `1.1.1-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `1.1.1-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `1.1.1-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `1.1.1-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `1.1.1-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `1.1.1-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `1.1.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `1.1.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `1.1.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `1.1.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `1.1.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `1.1.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `1.1.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `1.1.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `1.1.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `1.1.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `1.1.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `1.1.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `1.1.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `1.1.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |
| `1.0.0-amd64-de-de-preview` | 具有区域设置的容器映像 `de-DE` 。 |
| `1.0.0-amd64-en-au-preview` | 具有区域设置的容器映像 `en-AU` 。 |
| `1.0.0-amd64-en-ca-preview` | 具有区域设置的容器映像 `en-CA` 。 |
| `1.0.0-amd64-en-gb-preview` | 具有区域设置的容器映像 `en-GB` 。 |
| `1.0.0-amd64-en-in-preview` | 具有区域设置的容器映像 `en-IN` 。 |
| `1.0.0-amd64-en-us-preview` | 具有区域设置的容器映像 `en-US` 。 |
| `1.0.0-amd64-es-es-preview` | 具有区域设置的容器映像 `es-ES` 。 |
| `1.0.0-amd64-es-mx-preview` | 具有区域设置的容器映像 `es-MX` 。 |
| `1.0.0-amd64-fr-ca-preview` | 具有区域设置的容器映像 `fr-CA` 。 |
| `1.0.0-amd64-fr-fr-preview` | 具有区域设置的容器映像 `fr-FR` 。 |
| `1.0.0-amd64-it-it-preview` | 具有区域设置的容器映像 `it-IT` 。 |
| `1.0.0-amd64-ja-jp-preview` | 具有区域设置的容器映像 `ja-JP` 。 |
| `1.0.0-amd64-pt-br-preview` | 具有区域设置的容器映像 `pt-BR` 。 |
| `1.0.0-amd64-zh-cn-preview` | 具有区域设置的容器映像 `zh-CN` 。 |

## <a name="text-to-speech"></a>文本转语音

可以在容器注册表中找到 [文本到语音的][sp-tts] 容器图像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-text-to-speech` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` 。

此容器映像提供以下标记：

| 图像标记                                  | 注意                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有 `en-US` 区域设置和语音的容器映像 `en-US-AriaRUS` 。         |
| `1.7.0-amd64-ar-eg-hoda`                    | 具有 `ar-EG` 区域设置和语音的容器映像 `ar-EG-Hoda` 。            |
| `1.7.0-amd64-ar-sa-naayf`                   | 具有 `ar-SA` 区域设置和语音的容器映像 `ar-SA-Naayf` 。           |
| `1.7.0-amd64-bg-bg-ivan`                    | 具有 `bg-BG` 区域设置和语音的容器映像 `bg-BG-Ivan` 。            |
| `1.7.0-amd64-ca-es-herenarus`               | 具有 `ca-ES` 区域设置和语音的容器映像 `ca-ES-HerenaRUS` 。       |
| `1.7.0-amd64-cs-cz-jakub`                   | 具有 `cs-CZ` 区域设置和语音的容器映像 `cs-CZ-Jakub` 。           |
| `1.7.0-amd64-da-dk-hellerus`                | 具有 `da-DK` 区域设置和语音的容器映像 `da-DK-HelleRUS` 。        |
| `1.7.0-amd64-de-at-michael`                 | 具有 `de-AT` 区域设置和语音的容器映像 `de-AT-Michael` 。         |
| `1.7.0-amd64-de-ch-karsten`                 | 具有 `de-CH` 区域设置和语音的容器映像 `de-CH-Karsten` 。         |
| `1.7.0-amd64-de-de-hedda`                   | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.7.0-amd64-de-de-heddarus`                | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.7.0-amd64-de-de-stefan-apollo`           | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Stefan-Apollo` 。   |
| `1.7.0-amd64-el-gr-stefanos`                | 具有 `el-GR` 区域设置和语音的容器映像 `el-GR-Stefanos` 。        |
| `1.7.0-amd64-en-au-catherine`               | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-Catherine` 。       |
| `1.7.0-amd64-en-au-hayleyrus`               | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-HayleyRUS` 。       |
| `1.7.0-amd64-en-ca-heatherrus`              | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-HeatherRUS` 。      |
| `1.7.0-amd64-en-ca-linda`                   | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-Linda` 。           |
| `1.7.0-amd64-en-gb-george-apollo`           | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-George-Apollo` 。   |
| `1.7.0-amd64-en-gb-hazelrus`                | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-HazelRUS` 。        |
| `1.7.0-amd64-en-gb-susan-apollo`            | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-Susan-Apollo` 。    |
| `1.7.0-amd64-en-ie-sean`                    | 具有 `en-IE` 区域设置和语音的容器映像 `en-IE-Sean` 。            |
| `1.7.0-amd64-en-in-heera-apollo`            | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Heera-Apollo` 。    |
| `1.7.0-amd64-en-in-priyarus`                | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-PriyaRUS` 。        |
| `1.7.0-amd64-en-in-ravi-apollo`             | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Ravi-Apollo` 。     |
| `1.7.0-amd64-en-us-benjaminrus`             | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.7.0-amd64-en-us-guy24krus`               | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.7.0-amd64-en-us-aria24krus`              | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Aria24kRUS` 。      |
| `1.7.0-amd64-en-us-ariarus`                 | 具有 `en-US` 区域设置和语音的容器映像 `en-US-AriaRUS` 。         |
| `1.7.0-amd64-en-us-zirarus`                 | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.7.0-amd64-es-es-helenarus`               | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-HelenaRUS` 。       |
| `1.7.0-amd64-es-es-laura-apollo`            | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Laura-Apollo` 。    |
| `1.7.0-amd64-es-es-pablo-apollo`            | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Pablo-Apollo` 。    |
| `1.7.0-amd64-es-mx-hildarus`                | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-HildaRUS` 。        |
| `1.7.0-amd64-es-mx-raul-apollo`             | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-Raul-Apollo` 。     |
| `1.7.0-amd64-fi-fi-heidirus`                | 具有 `fi-FI` 区域设置和语音的容器映像 `fi-FI-HeidiRUS` 。        |
| `1.7.0-amd64-fr-ca-caroline`                | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-Caroline` 。        |
| `1.7.0-amd64-fr-ca-harmonierus`             | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-HarmonieRUS` 。     |
| `1.7.0-amd64-fr-ch-guillaume`               | 具有 `fr-CH` 区域设置和语音的容器映像 `fr-CH-Guillaume` 。       |
| `1.7.0-amd64-fr-fr-hortenserus`             | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-HortenseRUS` 。     |
| `1.7.0-amd64-fr-fr-julie-apollo`            | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Julie-Apollo` 。    |
| `1.7.0-amd64-fr-fr-paul-apollo`             | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Paul-Apollo` 。     |
| `1.7.0-amd64-he-il-asaf`                    | 具有 `he-IL` 区域设置和语音的容器映像 `he-IL-Asaf` 。            |
| `1.7.0-amd64-hi-in-hemant`                  | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Hemant` 。          |
| `1.7.0-amd64-hi-in-kalpana-apollo`          | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana-Apollo` 。  |
| `1.7.0-amd64-hi-in-kalpana`                 | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana` 。         |
| `1.7.0-amd64-hr-hr-matej`                   | 具有 `hr-HR` 区域设置和语音的容器映像 `hr-HR-Matej` 。           |
| `1.7.0-amd64-hu-hu-szabolcs`                | 具有 `hu-HU` 区域设置和语音的容器映像 `hu-HU-Szabolcs` 。        |
| `1.7.0-amd64-id-id-andika`                  | 具有 `id-ID` 区域设置和语音的容器映像 `id-ID-Andika` 。          |
| `1.7.0-amd64-it-it-cosimo-apollo`           | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-Cosimo-Apollo` 。   |
| `1.7.0-amd64-it-it-luciarus`                | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-LuciaRUS` 。        |
| `1.7.0-amd64-ja-jp-ayumi-apollo`            | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ayumi-Apollo` 。    |
| `1.7.0-amd64-ja-jp-harukarus`               | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-HarukaRUS` 。       |
| `1.7.0-amd64-ja-jp-ichiro-apollo`           | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ichiro-Apollo` 。   |
| `1.7.0-amd64-ko-kr-heamirus`                | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-HeamiRUS` 。        |
| `1.7.0-amd64-ms-my-rizwan`                  | 具有 `ms-MY` 区域设置和语音的容器映像 `ms-MY-Rizwan` 。          |
| `1.7.0-amd64-nb-no-huldarus`                | 具有 `nb-NO` 区域设置和语音的容器映像 `nb-NO-HuldaRUS` 。        |
| `1.7.0-amd64-nl-nl-hannarus`                | 具有 `nl-NL` 区域设置和语音的容器映像 `nl-NL-HannaRUS` 。        |
| `1.7.0-amd64-pl-pl-paulinarus`              | 具有 `pl-PL` 区域设置和语音的容器映像 `pl-PL-PaulinaRUS` 。      |
| `1.7.0-amd64-pt-br-daniel-apollo`           | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-Daniel-Apollo` 。   |
| `1.7.0-amd64-pt-br-heloisarus`              | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-HeloisaRUS` 。      |
| `1.7.0-amd64-pt-pt-heliarus`                | 具有 `pt-PT` 区域设置和语音的容器映像 `pt-PT-HeliaRUS` 。        |
| `1.7.0-amd64-ro-ro-andrei`                  | 具有 `ro-RO` 区域设置和语音的容器映像 `ro-RO-Andrei` 。          |
| `1.7.0-amd64-ru-ru-ekaterinarus`            | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-EkaterinaRUS` 。    |
| `1.7.0-amd64-ru-ru-irina-apollo`            | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Irina-Apollo` 。    |
| `1.7.0-amd64-ru-ru-pavel-apollo`            | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Pavel-Apollo` 。    |
| `1.7.0-amd64-sk-sk-filip`                   | 具有 `sk-SK` 区域设置和语音的容器映像 `sk-SK-Filip` 。           |
| `1.7.0-amd64-sl-si-lado`                    | 具有 `sl-SI` 区域设置和语音的容器映像 `sl-SI-Lado` 。            |
| `1.7.0-amd64-sv-se-hedvigrus`               | 具有 `sv-SE` 区域设置和语音的容器映像 `sv-SE-HedvigRUS` 。       |
| `1.7.0-amd64-ta-in-valluvar`                | 具有 `ta-IN` 区域设置和语音的容器映像 `ta-IN-Valluvar` 。        |
| `1.7.0-amd64-te-in-chitra`                  | 具有 `te-IN` 区域设置和语音的容器映像 `te-IN-Chitra` 。          |
| `1.7.0-amd64-th-th-pattara`                 | 具有 `th-TH` 区域设置和语音的容器映像 `th-TH-Pattara` 。         |
| `1.7.0-amd64-tr-tr-sedarus`                 | 具有 `tr-TR` 区域设置和语音的容器映像 `tr-TR-SedaRUS` 。         |
| `1.7.0-amd64-vi-vn-an`                      | 具有 `vi-VN` 区域设置和语音的容器映像 `vi-VN-An` 。              |
| `1.7.0-amd64-zh-cn-huihuirus`               | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.7.0-amd64-zh-cn-kangkang-apollo`         | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.7.0-amd64-zh-cn-yaoyao-apollo`           | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |
| `1.7.0-amd64-zh-hk-danny-apollo`            | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Danny-Apollo` 。    |
| `1.7.0-amd64-zh-hk-tracy-apollo`            | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Tracy-Apollo` 。    |
| `1.7.0-amd64-zh-hk-tracyrus`                | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-TracyRUS` 。        |
| `1.7.0-amd64-zh-tw-hanhanrus`               | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-HanHanRUS` 。       |
| `1.7.0-amd64-zh-tw-yating-apollo`           | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Yating-Apollo` 。   |
| `1.7.0-amd64-zh-tw-zhiwei-apollo`           | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Zhiwei-Apollo` 。   |
| `1.6.0-amd64-ar-eg-hoda-preview`            | 具有 `ar-EG` 区域设置和语音的容器映像 `ar-EG-Hoda` 。            |
| `1.6.0-amd64-ar-sa-naayf-preview`           | 具有 `ar-SA` 区域设置和语音的容器映像 `ar-SA-Naayf` 。           |
| `1.6.0-amd64-bg-bg-ivan-preview`            | 具有 `bg-BG` 区域设置和语音的容器映像 `bg-BG-Ivan` 。            |
| `1.6.0-amd64-ca-es-herenarus-preview`       | 具有 `ca-ES` 区域设置和语音的容器映像 `ca-ES-HerenaRUS` 。       |
| `1.6.0-amd64-cs-cz-jakub-preview`           | 具有 `cs-CZ` 区域设置和语音的容器映像 `cs-CZ-Jakub` 。           |
| `1.6.0-amd64-da-dk-hellerus-preview`        | 具有 `da-DK` 区域设置和语音的容器映像 `da-DK-HelleRUS` 。        |
| `1.6.0-amd64-de-at-michael-preview`         | 具有 `de-AT` 区域设置和语音的容器映像 `de-AT-Michael` 。         |
| `1.6.0-amd64-de-ch-karsten-preview`         | 具有 `de-CH` 区域设置和语音的容器映像 `de-CH-Karsten` 。         |
| `1.6.0-amd64-de-de-hedda-preview`           | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.6.0-amd64-de-de-heddarus-preview`        | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.6.0-amd64-de-de-stefan-apollo-preview`   | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Stefan-Apollo` 。   |
| `1.6.0-amd64-el-gr-stefanos-preview`        | 具有 `el-GR` 区域设置和语音的容器映像 `el-GR-Stefanos` 。        |
| `1.6.0-amd64-en-au-catherine-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-Catherine` 。       |
| `1.6.0-amd64-en-au-hayleyrus-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-HayleyRUS` 。       |
| `1.6.0-amd64-en-ca-heatherrus-preview`      | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-HeatherRUS` 。      |
| `1.6.0-amd64-en-ca-linda-preview`           | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-Linda` 。           |
| `1.6.0-amd64-en-gb-george-apollo-preview`   | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-George-Apollo` 。   |
| `1.6.0-amd64-en-gb-hazelrus-preview`        | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-HazelRUS` 。        |
| `1.6.0-amd64-en-gb-susan-apollo-preview`    | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-Susan-Apollo` 。    |
| `1.6.0-amd64-en-ie-sean-preview`            | 具有 `en-IE` 区域设置和语音的容器映像 `en-IE-Sean` 。            |
| `1.6.0-amd64-en-in-heera-apollo-preview`    | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Heera-Apollo` 。    |
| `1.6.0-amd64-en-in-priyarus-preview`        | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-PriyaRUS` 。        |
| `1.6.0-amd64-en-in-ravi-apollo-preview`     | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Ravi-Apollo` 。     |
| `1.6.0-amd64-en-us-benjaminrus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.6.0-amd64-en-us-guy24krus-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.6.0-amd64-en-us-aria24krus-preview`      | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Aria24kRUS` 。      |
| `1.6.0-amd64-en-us-ariarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-AriaRUS` 。         |
| `1.6.0-amd64-en-us-zirarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.6.0-amd64-es-es-helenarus-preview`       | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-HelenaRUS` 。       |
| `1.6.0-amd64-es-es-laura-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Laura-Apollo` 。    |
| `1.6.0-amd64-es-es-pablo-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Pablo-Apollo` 。    |
| `1.6.0-amd64-es-mx-hildarus-preview`        | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-HildaRUS` 。        |
| `1.6.0-amd64-es-mx-raul-apollo-preview`     | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-Raul-Apollo` 。     |
| `1.6.0-amd64-fi-fi-heidirus-preview`        | 具有 `fi-FI` 区域设置和语音的容器映像 `fi-FI-HeidiRUS` 。        |
| `1.6.0-amd64-fr-ca-caroline-preview`        | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-Caroline` 。        |
| `1.6.0-amd64-fr-ca-harmonierus-preview`     | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-HarmonieRUS` 。     |
| `1.6.0-amd64-fr-ch-guillaume-preview`       | 具有 `fr-CH` 区域设置和语音的容器映像 `fr-CH-Guillaume` 。       |
| `1.6.0-amd64-fr-fr-hortenserus-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-HortenseRUS` 。     |
| `1.6.0-amd64-fr-fr-julie-apollo-preview`    | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Julie-Apollo` 。    |
| `1.6.0-amd64-fr-fr-paul-apollo-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Paul-Apollo` 。     |
| `1.6.0-amd64-he-il-asaf-preview`            | 具有 `he-IL` 区域设置和语音的容器映像 `he-IL-Asaf` 。            |
| `1.6.0-amd64-hi-in-hemant-preview`          | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Hemant` 。          |
| `1.6.0-amd64-hi-in-kalpana-apollo-preview`  | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana-Apollo` 。  |
| `1.6.0-amd64-hi-in-kalpana-preview`         | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana` 。         |
| `1.6.0-amd64-hr-hr-matej-preview`           | 具有 `hr-HR` 区域设置和语音的容器映像 `hr-HR-Matej` 。           |
| `1.6.0-amd64-hu-hu-szabolcs-preview`        | 具有 `hu-HU` 区域设置和语音的容器映像 `hu-HU-Szabolcs` 。        |
| `1.6.0-amd64-id-id-andika-preview`          | 具有 `id-ID` 区域设置和语音的容器映像 `id-ID-Andika` 。          |
| `1.6.0-amd64-it-it-cosimo-apollo-preview`   | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-Cosimo-Apollo` 。   |
| `1.6.0-amd64-it-it-luciarus-preview`        | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-LuciaRUS` 。        |
| `1.6.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ayumi-Apollo` 。    |
| `1.6.0-amd64-ja-jp-harukarus-preview`       | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-HarukaRUS` 。       |
| `1.6.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ichiro-Apollo` 。   |
| `1.6.0-amd64-ko-kr-heamirus-preview`        | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-HeamiRUS` 。        |
| `1.6.0-amd64-ms-my-rizwan-preview`          | 具有 `ms-MY` 区域设置和语音的容器映像 `ms-MY-Rizwan` 。          |
| `1.6.0-amd64-nb-no-huldarus-preview`        | 具有 `nb-NO` 区域设置和语音的容器映像 `nb-NO-HuldaRUS` 。        |
| `1.6.0-amd64-nl-nl-hannarus-preview`        | 具有 `nl-NL` 区域设置和语音的容器映像 `nl-NL-HannaRUS` 。        |
| `1.6.0-amd64-pl-pl-paulinarus-preview`      | 具有 `pl-PL` 区域设置和语音的容器映像 `pl-PL-PaulinaRUS` 。      |
| `1.6.0-amd64-pt-br-daniel-apollo-preview`   | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-Daniel-Apollo` 。   |
| `1.6.0-amd64-pt-br-heloisarus-preview`      | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-HeloisaRUS` 。      |
| `1.6.0-amd64-pt-pt-heliarus-preview`        | 具有 `pt-PT` 区域设置和语音的容器映像 `pt-PT-HeliaRUS` 。        |
| `1.6.0-amd64-ro-ro-andrei-preview`          | 具有 `ro-RO` 区域设置和语音的容器映像 `ro-RO-Andrei` 。          |
| `1.6.0-amd64-ru-ru-ekaterinarus-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-EkaterinaRUS` 。    |
| `1.6.0-amd64-ru-ru-irina-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Irina-Apollo` 。    |
| `1.6.0-amd64-ru-ru-pavel-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Pavel-Apollo` 。    |
| `1.6.0-amd64-sk-sk-filip-preview`           | 具有 `sk-SK` 区域设置和语音的容器映像 `sk-SK-Filip` 。           |
| `1.6.0-amd64-sl-si-lado-preview`            | 具有 `sl-SI` 区域设置和语音的容器映像 `sl-SI-Lado` 。            |
| `1.6.0-amd64-sv-se-hedvigrus-preview`       | 具有 `sv-SE` 区域设置和语音的容器映像 `sv-SE-HedvigRUS` 。       |
| `1.6.0-amd64-ta-in-valluvar-preview`        | 具有 `ta-IN` 区域设置和语音的容器映像 `ta-IN-Valluvar` 。        |
| `1.6.0-amd64-te-in-chitra-preview`          | 具有 `te-IN` 区域设置和语音的容器映像 `te-IN-Chitra` 。          |
| `1.6.0-amd64-th-th-pattara-preview`         | 具有 `th-TH` 区域设置和语音的容器映像 `th-TH-Pattara` 。         |
| `1.6.0-amd64-tr-tr-sedarus-preview`         | 具有 `tr-TR` 区域设置和语音的容器映像 `tr-TR-SedaRUS` 。         |
| `1.6.0-amd64-vi-vn-an-preview`              | 具有 `vi-VN` 区域设置和语音的容器映像 `vi-VN-An` 。              |
| `1.6.0-amd64-zh-cn-huihuirus-preview`       | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.6.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.6.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |
| `1.6.0-amd64-zh-hk-danny-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Danny-Apollo` 。    |
| `1.6.0-amd64-zh-hk-tracy-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Tracy-Apollo` 。    |
| `1.6.0-amd64-zh-hk-tracyrus-preview`        | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-TracyRUS` 。        |
| `1.6.0-amd64-zh-tw-hanhanrus-preview`       | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-HanHanRUS` 。       |
| `1.6.0-amd64-zh-tw-yating-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Yating-Apollo` 。   |
| `1.6.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Zhiwei-Apollo` 。   |
| `1.5.0-amd64-ar-eg-hoda-preview`            | 具有 `ar-EG` 区域设置和语音的容器映像 `ar-EG-Hoda` 。            |
| `1.5.0-amd64-ar-sa-naayf-preview`           | 具有 `ar-SA` 区域设置和语音的容器映像 `ar-SA-Naayf` 。           |
| `1.5.0-amd64-bg-bg-ivan-preview`            | 具有 `bg-BG` 区域设置和语音的容器映像 `bg-BG-Ivan` 。            |
| `1.5.0-amd64-ca-es-herenarus-preview`       | 具有 `ca-ES` 区域设置和语音的容器映像 `ca-ES-HerenaRUS` 。       |
| `1.5.0-amd64-cs-cz-jakub-preview`           | 具有 `cs-CZ` 区域设置和语音的容器映像 `cs-CZ-Jakub` 。           |
| `1.5.0-amd64-da-dk-hellerus-preview`        | 具有 `da-DK` 区域设置和语音的容器映像 `da-DK-HelleRUS` 。        |
| `1.5.0-amd64-de-at-michael-preview`         | 具有 `de-AT` 区域设置和语音的容器映像 `de-AT-Michael` 。         |
| `1.5.0-amd64-de-ch-karsten-preview`         | 具有 `de-CH` 区域设置和语音的容器映像 `de-CH-Karsten` 。         |
| `1.5.0-amd64-de-de-hedda-preview`           | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.5.0-amd64-de-de-heddarus-preview`        | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.5.0-amd64-de-de-stefan-apollo-preview`   | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Stefan-Apollo` 。   |
| `1.5.0-amd64-el-gr-stefanos-preview`        | 具有 `el-GR` 区域设置和语音的容器映像 `el-GR-Stefanos` 。        |
| `1.5.0-amd64-en-au-catherine-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-Catherine` 。       |
| `1.5.0-amd64-en-au-hayleyrus-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-HayleyRUS` 。       |
| `1.5.0-amd64-en-ca-heatherrus-preview`      | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-HeatherRUS` 。      |
| `1.5.0-amd64-en-ca-linda-preview`           | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-Linda` 。           |
| `1.5.0-amd64-en-gb-george-apollo-preview`   | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-George-Apollo` 。   |
| `1.5.0-amd64-en-gb-hazelrus-preview`        | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-HazelRUS` 。        |
| `1.5.0-amd64-en-gb-susan-apollo-preview`    | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-Susan-Apollo` 。    |
| `1.5.0-amd64-en-ie-sean-preview`            | 具有 `en-IE` 区域设置和语音的容器映像 `en-IE-Sean` 。            |
| `1.5.0-amd64-en-in-heera-apollo-preview`    | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Heera-Apollo` 。    |
| `1.5.0-amd64-en-in-priyarus-preview`        | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-PriyaRUS` 。        |
| `1.5.0-amd64-en-in-ravi-apollo-preview`     | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Ravi-Apollo` 。     |
| `1.5.0-amd64-en-us-benjaminrus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.5.0-amd64-en-us-guy24krus-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.5.0-amd64-en-us-aria24krus-preview`      | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Aria24kRUS` 。     |
| `1.5.0-amd64-en-us-ariarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-AriaRUS` 。        |
| `1.5.0-amd64-en-us-zirarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.5.0-amd64-es-es-helenarus-preview`       | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-HelenaRUS` 。       |
| `1.5.0-amd64-es-es-laura-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Laura-Apollo` 。    |
| `1.5.0-amd64-es-es-pablo-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Pablo-Apollo` 。    |
| `1.5.0-amd64-es-mx-hildarus-preview`        | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-HildaRUS` 。        |
| `1.5.0-amd64-es-mx-raul-apollo-preview`     | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-Raul-Apollo` 。     |
| `1.5.0-amd64-fi-fi-heidirus-preview`        | 具有 `fi-FI` 区域设置和语音的容器映像 `fi-FI-HeidiRUS` 。        |
| `1.5.0-amd64-fr-ca-caroline-preview`        | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-Caroline` 。        |
| `1.5.0-amd64-fr-ca-harmonierus-preview`     | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-HarmonieRUS` 。     |
| `1.5.0-amd64-fr-ch-guillaume-preview`       | 具有 `fr-CH` 区域设置和语音的容器映像 `fr-CH-Guillaume` 。       |
| `1.5.0-amd64-fr-fr-hortenserus-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-HortenseRUS` 。     |
| `1.5.0-amd64-fr-fr-julie-apollo-preview`    | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Julie-Apollo` 。    |
| `1.5.0-amd64-fr-fr-paul-apollo-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Paul-Apollo` 。     |
| `1.5.0-amd64-he-il-asaf-preview`            | 具有 `he-IL` 区域设置和语音的容器映像 `he-IL-Asaf` 。            |
| `1.5.0-amd64-hi-in-hemant-preview`          | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Hemant` 。          |
| `1.5.0-amd64-hi-in-kalpana-apollo-preview`  | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana-Apollo` 。  |
| `1.5.0-amd64-hi-in-kalpana-preview`         | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana` 。         |
| `1.5.0-amd64-hr-hr-matej-preview`           | 具有 `hr-HR` 区域设置和语音的容器映像 `hr-HR-Matej` 。           |
| `1.5.0-amd64-hu-hu-szabolcs-preview`        | 具有 `hu-HU` 区域设置和语音的容器映像 `hu-HU-Szabolcs` 。        |
| `1.5.0-amd64-id-id-andika-preview`          | 具有 `id-ID` 区域设置和语音的容器映像 `id-ID-Andika` 。          |
| `1.5.0-amd64-it-it-cosimo-apollo-preview`   | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-Cosimo-Apollo` 。   |
| `1.5.0-amd64-it-it-luciarus-preview`        | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-LuciaRUS` 。        |
| `1.5.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ayumi-Apollo` 。    |
| `1.5.0-amd64-ja-jp-harukarus-preview`       | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-HarukaRUS` 。       |
| `1.5.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ichiro-Apollo` 。   |
| `1.5.0-amd64-ko-kr-heamirus-preview`        | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-HeamiRUS` 。        |
| `1.5.0-amd64-ms-my-rizwan-preview`          | 具有 `ms-MY` 区域设置和语音的容器映像 `ms-MY-Rizwan` 。          |
| `1.5.0-amd64-nb-no-huldarus-preview`        | 具有 `nb-NO` 区域设置和语音的容器映像 `nb-NO-HuldaRUS` 。        |
| `1.5.0-amd64-nl-nl-hannarus-preview`        | 具有 `nl-NL` 区域设置和语音的容器映像 `nl-NL-HannaRUS` 。        |
| `1.5.0-amd64-pl-pl-paulinarus-preview`      | 具有 `pl-PL` 区域设置和语音的容器映像 `pl-PL-PaulinaRUS` 。      |
| `1.5.0-amd64-pt-br-daniel-apollo-preview`   | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-Daniel-Apollo` 。   |
| `1.5.0-amd64-pt-br-heloisarus-preview`      | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-HeloisaRUS` 。      |
| `1.5.0-amd64-pt-pt-heliarus-preview`        | 具有 `pt-PT` 区域设置和语音的容器映像 `pt-PT-HeliaRUS` 。        |
| `1.5.0-amd64-ro-ro-andrei-preview`          | 具有 `ro-RO` 区域设置和语音的容器映像 `ro-RO-Andrei` 。          |
| `1.5.0-amd64-ru-ru-ekaterinarus-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-EkaterinaRUS` 。    |
| `1.5.0-amd64-ru-ru-irina-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Irina-Apollo` 。    |
| `1.5.0-amd64-ru-ru-pavel-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Pavel-Apollo` 。    |
| `1.5.0-amd64-sk-sk-filip-preview`           | 具有 `sk-SK` 区域设置和语音的容器映像 `sk-SK-Filip` 。           |
| `1.5.0-amd64-sl-si-lado-preview`            | 具有 `sl-SI` 区域设置和语音的容器映像 `sl-SI-Lado` 。            |
| `1.5.0-amd64-sv-se-hedvigrus-preview`       | 具有 `sv-SE` 区域设置和语音的容器映像 `sv-SE-HedvigRUS` 。       |
| `1.5.0-amd64-ta-in-valluvar-preview`        | 具有 `ta-IN` 区域设置和语音的容器映像 `ta-IN-Valluvar` 。        |
| `1.5.0-amd64-te-in-chitra-preview`          | 具有 `te-IN` 区域设置和语音的容器映像 `te-IN-Chitra` 。          |
| `1.5.0-amd64-th-th-pattara-preview`         | 具有 `th-TH` 区域设置和语音的容器映像 `th-TH-Pattara` 。         |
| `1.5.0-amd64-tr-tr-sedarus-preview`         | 具有 `tr-TR` 区域设置和语音的容器映像 `tr-TR-SedaRUS` 。         |
| `1.5.0-amd64-vi-vn-an-preview`              | 具有 `vi-VN` 区域设置和语音的容器映像 `vi-VN-An` 。              |
| `1.5.0-amd64-zh-cn-huihuirus-preview`       | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.5.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.5.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |
| `1.5.0-amd64-zh-hk-danny-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Danny-Apollo` 。    |
| `1.5.0-amd64-zh-hk-tracy-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Tracy-Apollo` 。    |
| `1.5.0-amd64-zh-hk-tracyrus-preview`        | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-TracyRUS` 。        |
| `1.5.0-amd64-zh-tw-hanhanrus-preview`       | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-HanHanRUS` 。       |
| `1.5.0-amd64-zh-tw-yating-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Yating-Apollo` 。   |
| `1.5.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Zhiwei-Apollo` 。   |
| `1.4.0-amd64-ar-eg-hoda-preview`            | 具有 `ar-EG` 区域设置和语音的容器映像 `ar-EG-Hoda` 。            |
| `1.4.0-amd64-ar-sa-naayf-preview`           | 具有 `ar-SA` 区域设置和语音的容器映像 `ar-SA-Naayf` 。           |
| `1.4.0-amd64-bg-bg-ivan-preview`            | 具有 `bg-BG` 区域设置和语音的容器映像 `bg-BG-Ivan` 。            |
| `1.4.0-amd64-ca-es-herenarus-preview`       | 具有 `ca-ES` 区域设置和语音的容器映像 `ca-ES-HerenaRUS` 。       |
| `1.4.0-amd64-cs-cz-jakub-preview`           | 具有 `cs-CZ` 区域设置和语音的容器映像 `cs-CZ-Jakub` 。           |
| `1.4.0-amd64-da-dk-hellerus-preview`        | 具有 `da-DK` 区域设置和语音的容器映像 `da-DK-HelleRUS` 。        |
| `1.4.0-amd64-de-at-michael-preview`         | 具有 `de-AT` 区域设置和语音的容器映像 `de-AT-Michael` 。         |
| `1.4.0-amd64-de-ch-karsten-preview`         | 具有 `de-CH` 区域设置和语音的容器映像 `de-CH-Karsten` 。         |
| `1.4.0-amd64-de-de-hedda-preview`           | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.4.0-amd64-de-de-heddarus-preview`        | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.4.0-amd64-de-de-stefan-apollo-preview`   | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Stefan-Apollo` 。   |
| `1.4.0-amd64-el-gr-stefanos-preview`        | 具有 `el-GR` 区域设置和语音的容器映像 `el-GR-Stefanos` 。        |
| `1.4.0-amd64-en-au-catherine-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-Catherine` 。       |
| `1.4.0-amd64-en-au-hayleyrus-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-HayleyRUS` 。       |
| `1.4.0-amd64-en-ca-heatherrus-preview`      | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-HeatherRUS` 。      |
| `1.4.0-amd64-en-ca-linda-preview`           | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-Linda` 。           |
| `1.4.0-amd64-en-gb-george-apollo-preview`   | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-George-Apollo` 。   |
| `1.4.0-amd64-en-gb-hazelrus-preview`        | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-HazelRUS` 。        |
| `1.4.0-amd64-en-gb-susan-apollo-preview`    | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-Susan-Apollo` 。    |
| `1.4.0-amd64-en-ie-sean-preview`            | 具有 `en-IE` 区域设置和语音的容器映像 `en-IE-Sean` 。            |
| `1.4.0-amd64-en-in-heera-apollo-preview`    | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Heera-Apollo` 。    |
| `1.4.0-amd64-en-in-priyarus-preview`        | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-PriyaRUS` 。        |
| `1.4.0-amd64-en-in-ravi-apollo-preview`     | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Ravi-Apollo` 。     |
| `1.4.0-amd64-en-us-benjaminrus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.4.0-amd64-en-us-guy24krus-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.4.0-amd64-en-us-aria24krus-preview`      | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Aria24kRUS` 。     |
| `1.4.0-amd64-en-us-ariarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-AriaRUS` 。        |
| `1.4.0-amd64-en-us-zirarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.4.0-amd64-es-es-helenarus-preview`       | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-HelenaRUS` 。       |
| `1.4.0-amd64-es-es-laura-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Laura-Apollo` 。    |
| `1.4.0-amd64-es-es-pablo-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Pablo-Apollo` 。    |
| `1.4.0-amd64-es-mx-hildarus-preview`        | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-HildaRUS` 。        |
| `1.4.0-amd64-es-mx-raul-apollo-preview`     | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-Raul-Apollo` 。     |
| `1.4.0-amd64-fi-fi-heidirus-preview`        | 具有 `fi-FI` 区域设置和语音的容器映像 `fi-FI-HeidiRUS` 。        |
| `1.4.0-amd64-fr-ca-caroline-preview`        | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-Caroline` 。        |
| `1.4.0-amd64-fr-ca-harmonierus-preview`     | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-HarmonieRUS` 。     |
| `1.4.0-amd64-fr-ch-guillaume-preview`       | 具有 `fr-CH` 区域设置和语音的容器映像 `fr-CH-Guillaume` 。       |
| `1.4.0-amd64-fr-fr-hortenserus-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-HortenseRUS` 。     |
| `1.4.0-amd64-fr-fr-julie-apollo-preview`    | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Julie-Apollo` 。    |
| `1.4.0-amd64-fr-fr-paul-apollo-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Paul-Apollo` 。     |
| `1.4.0-amd64-he-il-asaf-preview`            | 具有 `he-IL` 区域设置和语音的容器映像 `he-IL-Asaf` 。            |
| `1.4.0-amd64-hi-in-hemant-preview`          | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Hemant` 。          |
| `1.4.0-amd64-hi-in-kalpana-apollo-preview`  | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana-Apollo` 。  |
| `1.4.0-amd64-hi-in-kalpana-preview`         | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana` 。         |
| `1.4.0-amd64-hr-hr-matej-preview`           | 具有 `hr-HR` 区域设置和语音的容器映像 `hr-HR-Matej` 。           |
| `1.4.0-amd64-hu-hu-szabolcs-preview`        | 具有 `hu-HU` 区域设置和语音的容器映像 `hu-HU-Szabolcs` 。        |
| `1.4.0-amd64-id-id-andika-preview`          | 具有 `id-ID` 区域设置和语音的容器映像 `id-ID-Andika` 。          |
| `1.4.0-amd64-it-it-cosimo-apollo-preview`   | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-Cosimo-Apollo` 。   |
| `1.4.0-amd64-it-it-luciarus-preview`        | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-LuciaRUS` 。        |
| `1.4.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ayumi-Apollo` 。    |
| `1.4.0-amd64-ja-jp-harukarus-preview`       | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-HarukaRUS` 。       |
| `1.4.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ichiro-Apollo` 。   |
| `1.4.0-amd64-ko-kr-heamirus-preview`        | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-HeamiRUS` 。        |
| `1.4.0-amd64-ms-my-rizwan-preview`          | 具有 `ms-MY` 区域设置和语音的容器映像 `ms-MY-Rizwan` 。          |
| `1.4.0-amd64-nb-no-huldarus-preview`        | 具有 `nb-NO` 区域设置和语音的容器映像 `nb-NO-HuldaRUS` 。        |
| `1.4.0-amd64-nl-nl-hannarus-preview`        | 具有 `nl-NL` 区域设置和语音的容器映像 `nl-NL-HannaRUS` 。        |
| `1.4.0-amd64-pl-pl-paulinarus-preview`      | 具有 `pl-PL` 区域设置和语音的容器映像 `pl-PL-PaulinaRUS` 。      |
| `1.4.0-amd64-pt-br-daniel-apollo-preview`   | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-Daniel-Apollo` 。   |
| `1.4.0-amd64-pt-br-heloisarus-preview`      | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-HeloisaRUS` 。      |
| `1.4.0-amd64-pt-pt-heliarus-preview`        | 具有 `pt-PT` 区域设置和语音的容器映像 `pt-PT-HeliaRUS` 。        |
| `1.4.0-amd64-ro-ro-andrei-preview`          | 具有 `ro-RO` 区域设置和语音的容器映像 `ro-RO-Andrei` 。          |
| `1.4.0-amd64-ru-ru-ekaterinarus-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-EkaterinaRUS` 。    |
| `1.4.0-amd64-ru-ru-irina-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Irina-Apollo` 。    |
| `1.4.0-amd64-ru-ru-pavel-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Pavel-Apollo` 。    |
| `1.4.0-amd64-sk-sk-filip-preview`           | 具有 `sk-SK` 区域设置和语音的容器映像 `sk-SK-Filip` 。           |
| `1.4.0-amd64-sl-si-lado-preview`            | 具有 `sl-SI` 区域设置和语音的容器映像 `sl-SI-Lado` 。            |
| `1.4.0-amd64-sv-se-hedvigrus-preview`       | 具有 `sv-SE` 区域设置和语音的容器映像 `sv-SE-HedvigRUS` 。       |
| `1.4.0-amd64-ta-in-valluvar-preview`        | 具有 `ta-IN` 区域设置和语音的容器映像 `ta-IN-Valluvar` 。        |
| `1.4.0-amd64-te-in-chitra-preview`          | 具有 `te-IN` 区域设置和语音的容器映像 `te-IN-Chitra` 。          |
| `1.4.0-amd64-th-th-pattara-preview`         | 具有 `th-TH` 区域设置和语音的容器映像 `th-TH-Pattara` 。         |
| `1.4.0-amd64-tr-tr-sedarus-preview`         | 具有 `tr-TR` 区域设置和语音的容器映像 `tr-TR-SedaRUS` 。         |
| `1.4.0-amd64-vi-vn-an-preview`              | 具有 `vi-VN` 区域设置和语音的容器映像 `vi-VN-An` 。              |
| `1.4.0-amd64-zh-cn-huihuirus-preview`       | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.4.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.4.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |
| `1.4.0-amd64-zh-hk-danny-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Danny-Apollo` 。    |
| `1.4.0-amd64-zh-hk-tracy-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Tracy-Apollo` 。    |
| `1.4.0-amd64-zh-hk-tracyrus-preview`        | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-TracyRUS` 。        |
| `1.4.0-amd64-zh-tw-hanhanrus-preview`       | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-HanHanRUS` 。       |
| `1.4.0-amd64-zh-tw-yating-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Yating-Apollo` 。   |
| `1.4.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Zhiwei-Apollo` 。   |
| `1.3.0-amd64-ar-eg-hoda-preview`            | 具有 `ar-EG` 区域设置和语音的容器映像 `ar-EG-Hoda` 。            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | 具有 `ar-SA` 区域设置和语音的容器映像 `ar-SA-Naayf` 。           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | 具有 `bg-BG` 区域设置和语音的容器映像 `bg-BG-Ivan` 。            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | 具有 `ca-ES` 区域设置和语音的容器映像 `ca-ES-HerenaRUS` 。       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | 具有 `cs-CZ` 区域设置和语音的容器映像 `cs-CZ-Jakub` 。           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | 具有 `da-DK` 区域设置和语音的容器映像 `da-DK-HelleRUS` 。        |
| `1.3.0-amd64-de-at-michael-preview`         | 具有 `de-AT` 区域设置和语音的容器映像 `de-AT-Michael` 。         |
| `1.3.0-amd64-de-ch-karsten-preview`         | 具有 `de-CH` 区域设置和语音的容器映像 `de-CH-Karsten` 。         |
| `1.3.0-amd64-de-de-hedda-preview`           | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-HeddaRUS` 。        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Stefan-Apollo` 。   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | 具有 `el-GR` 区域设置和语音的容器映像 `el-GR-Stefanos` 。        |
| `1.3.0-amd64-en-au-catherine-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-Catherine` 。       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-HayleyRUS` 。       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-HeatherRUS` 。      |
| `1.3.0-amd64-en-ca-linda-preview`           | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-Linda` 。           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-George-Apollo` 。   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-HazelRUS` 。        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-Susan-Apollo` 。    |
| `1.3.0-amd64-en-ie-sean-preview`            | 具有 `en-IE` 区域设置和语音的容器映像 `en-IE-Sean` 。            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Heera-Apollo` 。    |
| `1.3.0-amd64-en-in-priyarus-preview`        | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-PriyaRUS` 。        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Ravi-Apollo` 。     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Jessa24kRUS` 。     |
| `1.3.0-amd64-en-us-jessarus-preview`        | 具有 `en-US` 区域设置和语音的容器映像 `en-US-JessaRUS` 。        |
| `1.3.0-amd64-en-us-zirarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.3.0-amd64-es-es-helenarus-preview`       | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-HelenaRUS` 。       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Laura-Apollo` 。    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Pablo-Apollo` 。    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-HildaRUS` 。        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-Raul-Apollo` 。     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | 具有 `fi-FI` 区域设置和语音的容器映像 `fi-FI-HeidiRUS` 。        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-Caroline` 。        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-HarmonieRUS` 。     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | 具有 `fr-CH` 区域设置和语音的容器映像 `fr-CH-Guillaume` 。       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-HortenseRUS` 。     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Julie-Apollo` 。    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Paul-Apollo` 。     |
| `1.3.0-amd64-he-il-asaf-preview`            | 具有 `he-IL` 区域设置和语音的容器映像 `he-IL-Asaf` 。            |
| `1.3.0-amd64-hi-in-hemant-preview`          | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Hemant` 。          |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana` 。         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 具有 `hi-IN` 区域设置和语音的容器映像 `hi-IN-Kalpana` 。         |
| `1.3.0-amd64-hr-hr-matej-preview`           | 具有 `hr-HR` 区域设置和语音的容器映像 `hr-HR-Matej` 。           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | 具有 `hu-HU` 区域设置和语音的容器映像 `hu-HU-Szabolcs` 。        |
| `1.3.0-amd64-id-id-andika-preview`          | 具有 `id-ID` 区域设置和语音的容器映像 `id-ID-Andika` 。          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-Cosimo-Apollo` 。   |
| `1.3.0-amd64-it-it-luciarus-preview`        | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-LuciaRUS` 。        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ayumi-Apollo` 。    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-HarukaRUS` 。       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ichiro-Apollo` 。   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-HeamiRUS` 。        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | 具有 `ms-MY` 区域设置和语音的容器映像 `ms-MY-Rizwan` 。          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | 具有 `nb-NO` 区域设置和语音的容器映像 `nb-NO-HuldaRUS` 。        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | 具有 `nl-NL` 区域设置和语音的容器映像 `nl-NL-HannaRUS` 。        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | 具有 `pl-PL` 区域设置和语音的容器映像 `pl-PL-PaulinaRUS` 。      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-Daniel-Apollo` 。   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-HeloisaRUS` 。      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | 具有 `pt-PT` 区域设置和语音的容器映像 `pt-PT-HeliaRUS` 。        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | 具有 `ro-RO` 区域设置和语音的容器映像 `ro-RO-Andrei` 。          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-EkaterinaRUS` 。    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Irina-Apollo` 。    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | 具有 `ru-RU` 区域设置和语音的容器映像 `ru-RU-Pavel-Apollo` 。    |
| `1.3.0-amd64-sk-sk-filip-preview`           | 具有 `sk-SK` 区域设置和语音的容器映像 `sk-SK-Filip` 。           |
| `1.3.0-amd64-sl-si-lado-preview`            | 具有 `sl-SI` 区域设置和语音的容器映像 `sl-SI-Lado` 。            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | 具有 `sv-SE` 区域设置和语音的容器映像 `sv-SE-HedvigRUS` 。       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | 具有 `ta-IN` 区域设置和语音的容器映像 `ta-IN-Valluvar` 。        |
| `1.3.0-amd64-te-in-chitra-preview`          | 具有 `te-IN` 区域设置和语音的容器映像 `te-IN-Chitra` 。          |
| `1.3.0-amd64-th-th-pattara-preview`         | 具有 `th-TH` 区域设置和语音的容器映像 `th-TH-Pattara` 。         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | 具有 `tr-TR` 区域设置和语音的容器映像 `tr-TR-SedaRUS` 。         |
| `1.3.0-amd64-vi-vn-an-preview`              | 具有 `vi-VN` 区域设置和语音的容器映像 `vi-VN-An` 。              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Danny-Apollo` 。    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-Tracy-Apollo` 。    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | 具有 `zh-HK` 区域设置和语音的容器映像 `zh-HK-TracyRUS` 。        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-HanHanRUS` 。       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Yating-Apollo` 。   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | 具有 `zh-TW` 区域设置和语音的容器映像 `zh-TW-Zhiwei-Apollo` 。   |
| `1.2.0-amd64-de-de-hedda-preview`           | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.2.0-amd64-de-de-heddarus-preview`        | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-HeddaRUS` 。        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Stefan-Apollo` 。   |
| `1.2.0-amd64-en-au-catherine-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-Catherine` 。       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-HayleyRUS` 。       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-George-Apollo` 。   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-HazelRUS` 。        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-Susan-Apollo` 。    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Heera-Apollo` 。    |
| `1.2.0-amd64-en-in-priyarus-preview`        | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-PriyaRUS` 。        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Ravi-Apollo` 。     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Jessa24kRUS` 。     |
| `1.2.0-amd64-en-us-jessarus-preview`        | 具有 `en-US` 区域设置和语音的容器映像 `en-US-JessaRUS` 。        |
| `1.2.0-amd64-en-us-zirarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.2.0-amd64-es-es-helenarus-preview`       | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-HelenaRUS` 。       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Laura-Apollo` 。    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Pablo-Apollo` 。    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-HildaRUS` 。        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-Raul-Apollo` 。     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-Caroline` 。        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-HarmonieRUS` 。     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-HortenseRUS` 。     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Julie-Apollo` 。    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Paul-Apollo` 。     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-Cosimo-Apollo` 。   |
| `1.2.0-amd64-it-it-luciarus-preview`        | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-LuciaRUS` 。        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ayumi-Apollo` 。    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-HarukaRUS` 。       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ichiro-Apollo` 。   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-HeamiRUS` 。        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-Daniel-Apollo` 。   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-HeloisaRUS` 。      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |
| `1.1.0-amd64-de-de-hedda-preview`           | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Hedda` 。           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-HeddaRUS` 。        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-Stefan-Apollo` 。   |
| `1.1.0-amd64-en-au-catherine-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-Catherine` 。       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-HayleyRUS` 。       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-George-Apollo` 。   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-HazelRUS` 。        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-Susan-Apollo` 。    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Heera-Apollo` 。    |
| `1.1.0-amd64-en-in-priyarus-preview`        | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-PriyaRUS` 。        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | 具有 `en-IN` 区域设置和语音的容器映像 `en-IN-Ravi-Apollo` 。     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Jessa24kRUS` 。     |
| `1.1.0-amd64-en-us-jessarus-preview`        | 具有 `en-US` 区域设置和语音的容器映像 `en-US-JessaRUS` 。        |
| `1.1.0-amd64-en-us-zirarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.1.0-amd64-es-es-helenarus-preview`       | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-HelenaRUS` 。       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Laura-Apollo` 。    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-Pablo-Apollo` 。    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-HildaRUS` 。        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-Raul-Apollo` 。     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-Caroline` 。        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-HarmonieRUS` 。     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-HortenseRUS` 。     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Julie-Apollo` 。    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-Paul-Apollo` 。     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-Cosimo-Apollo` 。   |
| `1.1.0-amd64-it-it-luciarus-preview`        | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-LuciaRUS` 。        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ayumi-Apollo` 。    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-HarukaRUS` 。       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-Ichiro-Apollo` 。   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-HeamiRUS` 。        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-Daniel-Apollo` 。   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-HeloisaRUS` 。      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-BenjaminRUS` 。     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Guy24kRUS` 。       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | 具有 `en-US` 区域设置和语音的容器映像 `en-US-Jessa24kRUS` 。     |
| `1.0.0-amd64-en-us-jessarus-preview`        | 具有 `en-US` 区域设置和语音的容器映像 `en-US-JessaRUS` 。        |
| `1.0.0-amd64-en-us-zirarus-preview`         | 具有 `en-US` 区域设置和语音的容器映像 `en-US-ZiraRUS` 。         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-HuihuiRUS` 。       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Kangkang-Apollo` 。 |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-Yaoyao-Apollo` 。   |

## <a name="neural-text-to-speech"></a>神经文本到语音转换

可以在容器注册表中找到 [神经文本到语音] [ntts] 容器映像 `containerpreview.azurecr.io` 。 它驻留在 `microsoft` 存储库中，并命名为 `cognitive-services-neural-text-to-speech` 。 完全限定的容器映像名称为， `containerpreview.azurecr.io/microsoft/cognitive-services-neural-text-to-speech` 。

此容器映像提供以下标记：

| 图像标记                                  | 注意                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 具有 `en-US` 区域设置和语音的容器映像 `en-US-AriaNeural` 。      |
| `1.2.0-amd64-de-de-katjaneural-preview`     | 具有 `de-DE` 区域设置和语音的容器映像 `de-DE-KatjaNeural` 。     |
| `1.2.0-amd64-en-au-natashaneural-preview`   | 具有 `en-AU` 区域设置和语音的容器映像 `en-AU-NatashaNeural` 。   |
| `1.2.0-amd64-en-ca-claraneural-preview`     | 具有 `en-CA` 区域设置和语音的容器映像 `en-CA-ClaraNeural` 。     |
| `1.2.0-amd64-en-gb-libbyneural-preview`     | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-LibbyNeural` 。     |
| `1.2.0-amd64-en-gb-mianeural-preview`       | 具有 `en-GB` 区域设置和语音的容器映像 `en-GB-MiaNeural` 。       |
| `1.2.0-amd64-en-us-arianeural-preview`      | 具有 `en-US` 区域设置和语音的容器映像 `en-US-AriaNeural` 。      |
| `1.2.0-amd64-en-us-guyneural-preview`       | 具有 `en-US` 区域设置和语音的容器映像 `en-US-GuyNeural` 。       |
| `1.2.0-amd64-es-es-elviraneural-preview`    | 具有 `es-ES` 区域设置和语音的容器映像 `es-ES-ElviraNeural` 。    |
| `1.2.0-amd64-es-mx-dalianeural-preview`     | 具有 `es-MX` 区域设置和语音的容器映像 `es-MX-DaliaNeural` 。     |
| `1.2.0-amd64-fr-ca-sylvieneural-preview`    | 具有 `fr-CA` 区域设置和语音的容器映像 `fr-CA-SylvieNeural` 。    |
| `1.2.0-amd64-fr-fr-deniseneural-preview`    | 具有 `fr-FR` 区域设置和语音的容器映像 `fr-FR-DeniseNeural` 。    |
| `1.2.0-amd64-it-it-elsaneural-preview`      | 具有 `it-IT` 区域设置和语音的容器映像 `it-IT-ElsaNeural` 。      |
| `1.2.0-amd64-ja-jp-nanamineural-preview`    | 具有 `ja-JP` 区域设置和语音的容器映像 `ja-JP-NanamiNeural` 。    |
| `1.2.0-amd64-ko-kr-sunhineural-preview`     | 具有 `ko-KR` 区域设置和语音的容器映像 `ko-KR-SunHiNeural` 。     |
| `1.2.0-amd64-pt-br-franciscaneural-preview` | 具有 `pt-BR` 区域设置和语音的容器映像 `pt-BR-FranciscaNeural` 。 |
| `1.2.0-amd64-zh-cn-xiaoxiaoneural-preview`  | 具有 `zh-CN` 区域设置和语音的容器映像 `zh-CN-XiaoxiaoNeural` 。  |

## <a name="key-phrase-extraction"></a>关键短语提取

可以在容器注册表 "联合" 中找到 [关键短语提取][ta-kp] 容器映像 `mcr.microsoft.com` 。 它驻留在 `azure-cognitive-services` 存储库中，并命名为 `keyphrase` 。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/keyphrase` 。

此容器映像提供以下标记：

| 图像标记                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>语言检测

可以在容器注册表 "联合" 中找到 [语言检测][ta-la] 容器映像 `mcr.microsoft.com` 。 它驻留在 `azure-cognitive-services` 存储库中，并命名为 `language` 。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/language` 。

此容器映像提供以下标记：

| 图像标记                    | 注意 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>情绪分析

可以在容器注册表 "联合" 中找到 [情绪分析][ta-se] 容器映像 `mcr.microsoft.com` 。 它驻留在 `azure-cognitive-services` 存储库中，并命名为 `sentiment` 。 完全限定的容器映像名称为， `mcr.microsoft.com/azure-cognitive-services/sentiment` 。

此容器映像提供以下标记：

| 图像标记 | 注意                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 情绪分析 v3 (英语)                |
| `3.0-es`   | 情绪分析 v3 (西班牙语)                |
| `3.0-fr`   | 情绪分析 v3 (法语)                 |
| `3.0-it`   | 情绪分析 v3 (意大利语)                |
| `3.0-de`   | 情绪分析 v3 (德语)                 |
| `3.0-zh`   | 情绪分析 v3 (简体中文)   |
| `3.0-zht`  | 情绪分析 v3 (中文-繁体)  |
| `3.0-ja`   | 情绪分析 v3 (日语)               |
| `3.0-pt`   | 情绪分析 v3 (葡萄牙语)             |
| `3.0-nl`   | 情绪分析 v3 (荷兰语)                  |
| `1.1.009301-amd64-preview`    | 情绪分析 v2      |
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
