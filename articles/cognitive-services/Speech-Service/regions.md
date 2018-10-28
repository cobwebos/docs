---
title: 语音服务区域
titlesuffix: Azure Cognitive Services
description: 语音服务区域的参考。
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: a5fce6f9547a96da3ce482ce388e5ba2093f2af4
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468120"
---
# <a name="regions-of-the-speech-service"></a>语音服务区域

语音服务可在不同区域使用。
创建订阅时，可根据需要选择一个可用的区域。

使用订阅时，必须考虑所选的区域。

## <a name="rest-api"></a>REST API

使用 REST API 选择特定于区域的正确终结点。
有关详细信息，请参阅 [REST API](rest-apis.md)。

## <a name="speech-sdk"></a>语音 SDK

在[语音服务 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中作为 `SpeechConfig.FromSubscription` 的参数）。

### <a name="regions-for-speech-recognition-and-translation"></a>语音识别和转换的适用区域

下表列出了“语音识别”和“翻译”的可用区域。

  区域 | 语音 SDK 参数 | 门户
 ------|-------|--------
 美国西部 | `westus` | https://westus.cris.ai
 美国西部 2 | `westus2` | https://westus2.cris.ai 
 美国东部 | `eastus` | https://eastus.cris.ai
 美国东部 2 | `eastus2` | https://eastus2.cris.ai
 东亚 | `eastasia` | https://eastasia.cris.ai
 东南亚 | `southeastasia` | https://southeastasia.cris.ai
 北欧 | `northeurope` | https://northeurope.cris.ai
 西欧 | `westeurope` | https://westeurope.cris.ai


### <a name="regions-for-intent-recognition"></a>意向识别适用的区域

若要了解哪些区域可通过语音 SDK 进行**意向识别**，请参阅[语言理解服务区域页](/azure/cognitive-services/luis/luis-reference-regions)。
对于列出的每个发布区域，相应的语音 SDK 区域参数肯定是将终结点域名的第一部分。
例如，使用 `westus` 指定美国西部发布区域。
