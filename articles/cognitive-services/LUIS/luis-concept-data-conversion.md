---
title: 数据转换
titleSuffix: Language Understanding - Azure Cognitive Services
description: 了解如何在语言理解 (LUIS) 得出预测之前更改话语
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: c7495ba3f2dc453463038fd37147b4574536ca92
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215327"
---
# <a name="convert-data-format-of-utterances"></a>转换话语的数据格式
LUIS 在预测之前使用认知服务语音服务将话语从口头话语转换为文本话语。 

## <a name="speech-to-intent-conversion-concepts"></a>“语音转意向”转换概念
借助 LUIS 的“语音转文本”功能，可将口述话语发送到终结点并接收 LUIS 预测响应。 该过程是[语音](https://docs.microsoft.com/azure/cognitive-services/Speech)服务与 LUIS 的集成。 通过[教程](../speech-service/how-to-recognize-intents-from-speech-csharp.md)了解有关语音转意向的更多信息。

### <a name="key-requirements"></a>关键要求
无需为此集成创建必应语音 API 密钥。 可在此集成中使用 Azure 门户中创建的语言理解密钥。 请勿使用 LUIS 初学者密钥。

### <a name="pricing-tier"></a>定价层
此集成使用与通常的语言理解定价层不同的[定价](luis-boundaries.md#key-limits)模型。 

### <a name="quota-usage"></a>配额使用情况
相关信息请参阅[密钥限制](luis-boundaries.md#key-limits)。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用“语音转文本”功能](luis-tutorial-speech-to-intent.md)

