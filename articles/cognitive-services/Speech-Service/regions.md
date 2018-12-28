---
title: 区域 - 语音服务
titlesuffix: Azure Cognitive Services
description: 语音服务区域的参考。
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d41213d72d40555d8dc5aeab76040fc556dae774
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091639"
---
# <a name="speech-service-supported-regions"></a>语音服务支持的区域

通过语音服务，应用程序可将音频转换为文本、执行语音翻译以及将文本转换为语音。 多个区域中均提供该服务，这些区域为语音 SDK 和 REST API 使用唯一终结点。

请务必使用与订阅的区域匹配的终结点。

## <a name="speech-sdk"></a>语音 SDK

在[语音服务 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中作为 `SpeechConfig.FromSubscription` 的参数）。

### <a name="speech-recognition-and-translation"></a>语音识别和翻译

以下区域提供语音 SDK，用于“语音识别”和“翻译”：

  区域 | 语音 SDK 参数 | 语音自定义门户
 ------|-------|--------
 美国西部 | `westus` | https://westus.cris.ai
 美国西部 2 | `westus2` | https://westus2.cris.ai
 美国东部 | `eastus` | https://eastus.cris.ai
 美国东部 2 | `eastus2` | https://eastus2.cris.ai
 东亚 | `eastasia` | https://eastasia.cris.ai
 东南亚 | `southeastasia` | https://southeastasia.cris.ai
 北欧 | `northeurope` | https://northeurope.cris.ai
 西欧 | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>意向识别

语音 SDK 的“意向识别”与 LIUS 共享区域支持。 有关可用区域的完整列表，请参阅[发布区域和终结点 - LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)

若要了解哪些区域可通过语音 SDK 进行**意向识别**，请参阅[语言理解服务区域页](/azure/cognitive-services/luis/luis-reference-regions)。

对于列出的每个发布区域，请使用提供的“API 区域名称”。 例如，对美国西部使用 `westus`。

## <a name="rest-apis"></a>REST API

语音服务还为语音转文本和文本转语音请求公开 REST 终结点。

### <a name="speech-to-text"></a>语音转文本

有关语音转文本的参考文档，请参阅 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>文本转语音

有关文本转语音的参考文档，请参阅 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
