---
title: 数据转换 - LUIS
titleSuffix: Azure Cognitive Services
description: 了解如何在语言理解 (LUIS) 得出预测之前更改话语
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b2455df87c8eae1a48cb6c8b1381dad85d304bf4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82099234"
---
# <a name="convert-data-format-of-utterances"></a>转换话语的数据格式
LUIS 在预测之前提供用户话语的以下转换

* 语音转文本（使用[认知服务语音](../Speech-Service/overview.md)服务）。

## <a name="speech-to-text"></a>语音转文本

语音转文本作为与 LUIS 的集成提供。

### <a name="intent-conversion-concepts"></a>意向转换概念
借助 LUIS 的“语音转文本”功能，可将口述话语发送到终结点并接收 LUIS 预测响应。 该过程是[语音](https://docs.microsoft.com/azure/cognitive-services/Speech)服务与 LUIS 的集成。 通过[教程](../speech-service/how-to-recognize-intents-from-speech-csharp.md)了解有关语音转意向的更多信息。

### <a name="key-requirements"></a>关键要求
无需为此集成创建必应语音 API 密钥****。 可在此集成中使用 Azure 门户中创建的语言理解密钥****。 请勿使用 LUIS 初学者密钥。

### <a name="pricing-tier"></a>定价层
此集成使用与通常的语言理解定价层不同的[定价](luis-limits.md#key-limits)模型。

### <a name="quota-usage"></a>配额使用情况
相关信息请参阅[密钥限制](luis-limits.md#key-limits)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [提取数据](luis-concept-data-extraction.md)

