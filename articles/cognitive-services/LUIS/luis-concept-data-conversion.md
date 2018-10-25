---
title: LUIS 中的数据转换概念 - 语言理解
titleSuffix: Azure Cognitive Services
description: 了解如何在语言理解 (LUIS) 得出预测之前更改话语
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9324f7b4f7bed844f16d17b8960878892be4b165
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638386"
---
# <a name="data-conversion-concepts-in-luis"></a>LUIS 中的数据转换概念
LUIS 在预测之前使用认知服务语音服务将话语从口头话语转换为文本话语。 

## <a name="speech-to-intent-conversion-concepts"></a>“语音转意向”转换概念
借助 LUIS 的“语音转文本”功能，可将口述话语发送到终结点并接收 LUIS 预测响应。 该过程是[语音](https://docs.microsoft.com/azure/cognitive-services/Speech)服务与 LUIS 的集成。 

### <a name="key-requirements"></a>关键要求
无需为此集成创建必应语音 API 密钥。 可在此集成中使用 Azure 门户中创建的语言理解密钥。 请勿使用 LUIS 初学者密钥，它不适用于此集成。

### <a name="new-endpoint"></a>新建终结点 
此集成会创建新的终结点和[定价](luis-boundaries.md#key-limits)模型。 该终结点可通过[语音 SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) 接收口述话语和文本话语，因此可将其用作单个终结点。 

### <a name="quota-usage"></a>配额使用情况
相关信息请参阅[密钥限制](luis-boundaries.md#key-limits)。 

### <a name="data-retention"></a>数据保留
无论发送到终结点的数据是语音还是文本，都借助语音 SDK 仅出于改善语音模型的目的使用此数据。 此数据仅用于你的模型，目的是增强语音和 LUIS 的常规功能。 删除 LUIS 应用时一并删除所保留的数据。

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用“语音转文本”功能](luis-tutorial-speech-to-intent.md)

