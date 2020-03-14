---
title: 训练应用-LUIS
titleSuffix: Azure Cognitive Services
description: 训练是向语言理解 (LUIS) 应用版本进行教学以提高其自然语言理解能力的过程。 对模型进行更新（例如添加、编辑、标记或删除实体、意向或陈述）后，请对 LUIS 应用进行训练。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219859"
---
# <a name="train-your-active-version-of-the-luis-app"></a>训练 LUIS 应用的有效版本 

训练是向语言理解 (LUIS) 应用进行教学以提高其自然语言理解能力的过程。 对模型进行更新（例如添加、编辑、标记或删除实体、意向或陈述）后，请对 LUIS 应用进行训练。 

对应用进行训练和[测试](luis-concept-test.md)是一个迭代过程。 训练 LUIS 应用后，采用示例陈述来对应用进行测试，查看是否能准确地识别意向和实体。 如果未能准确识别，请对 LUIS 应用进行更新和训练，然后再次测试。 

训练将应用于 LUIS 门户中的活动版本。 

## <a name="how-to-train-interactively"></a>如何以交互方式训练

若要在 [LUIS 门户中](https://www.luis.ai)启动迭代过程，首先需要将 LUIS 应用训练至少一次。 在训练之前，请确保每个意向具有至少一个陈述。

1. 在“我的应用”页面上选择应用名称以访问应用。 

1. 在应用中，在顶部的面板中选择“训练”。 

1. 训练完成后，浏览器顶部会出现一个通知。

## <a name="training-date-and-time"></a>训练日期和时间

训练日期和时间是 GMT + 2。 

## <a name="train-with-all-data"></a>使用所有数据进行训练

训练使用一小部分负采样。 如果要使用所有数据而不是小型否定采样，请使用[API](#version-settings-api-use-of-usealltrainingdata)。

### <a name="version-settings-api-use-of-usealltrainingdata"></a>版本设置 API 使用 UseAllTrainingData

使用[版本设置 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) ，将 `UseAllTrainingData` 设置为 true 以关闭此功能。 

## <a name="unnecessary-training"></a>不必要的训练

不需要在每次更改后都进行训练。 只有在向模型应用一组更改后并且要执行的下一步骤是测试或发布时，才应当进行训练。 如果不需要测试或发布，则无需训练。 

## <a name="training-with-the-rest-apis"></a>使用 REST API 进行训练

在 LUIS 门户中进行训练是单个步骤，只需按下“训练”按钮。 使用 REST API 进行训练是一个两步过程。 第一个步骤是通过 HTTP POST [请求训练](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)。 然后通过 HTTP Get 请求[训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46)。 

若要了解训练是何时完成的，必须对状态进行轮询，直至所有模型都成功训练。 

## <a name="next-steps"></a>后续步骤

* [交互式测试](luis-interactive-test.md)
* [批处理测试](luis-how-to-batch-test.md)
