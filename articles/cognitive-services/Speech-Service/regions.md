---
title: 语音服务区域 | Microsoft Docs
description: 语音服务区域的参考。
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 11360d163fdba057d373d091d46903cde7789a8b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071413"
---
# <a name="regions-of-the-speech-service"></a>语音服务区域

语音服务可在不同区域使用。
创建订阅时，可根据需要选择一个可用的区域。

使用订阅时，必须考虑所选的区域。

## <a name="rest-api"></a>REST API

使用 REST API，选择适当的区域特定的终结点。
有关详细信息，请参阅 [REST API](rest-apis.md)。

## <a name="speech-sdk"></a>语音 SDK

在[语音 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中用作 [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) 的参数）。

下表列出了“语音识别”和“翻译”的可用区域：

区域| 语音 SDK 中区域参数的值
-|-
美国西部| `westus`
东亚| `eastasia`
北欧| `northeurope`

要了解哪些区域可通过语音 SDK 进行意向识别，请参阅[语言理解服务区域页](/azure/cognitive-services/luis/luis-reference-regions)。
对于列出的每个发布区域，相应的语音 SDK 区域参数肯定是将终结点域名的第一部分。
例如，使用 `westus` 指定美国西部发布区域。
