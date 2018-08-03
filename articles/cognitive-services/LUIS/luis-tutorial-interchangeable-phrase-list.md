---
title: 使用短语列表改进 LUIS 预测的教程 - Azure | Microsoft Docs
description: 在本教程中，向 LUIS 应用添加短语列表并查看得分方面的提升。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868967"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>教程：添加短语列表以改进预测
在本教程中，通过添加可互换的[短语列表功能](./luis-concept-feature.md)，提高意向得分的准确性并识别含义相同的词（同义词）的实体。

> [!div class="checklist"]
* 导入新应用  
* 使用已知陈述查询终结点 
* 使用未知陈述查询终结点
* 添加短语列表以提高未知陈述得分
* 验证使用短语列表时是否已找到实体

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="import-a-new-app"></a>导入新应用
1. 下载专为本教程设计的[示例 LUIS 应用][LuisSampleApp]。 在下一步骤中将要使用该值。 

2. 如[创建应用](Create-new-app.md#import-new-app)中所述，请将下载的文件作为新应用导入 [LUIS][LUIS] 网站。 应用名称是“我的短语列表教程”。 它具有意向、实体和陈述。 

3. [训练](luis-how-to-train.md)应用。 训练应用前，不能在 [LUIS][LUIS] 网站中对其[以交互方式进行测试](interactive-test.md#interactive-testing)。 

4. 在[发布](luis-how-to-publish-app.md)页上，选中“包含所有预测意向评分”复选框。 选中复选框后，将返回所有意向。 如果取消选中该复选框，则仅返回最高意向。 

5. [发布](luis-how-to-publish-app.md)应用。 发布应用后，可使用 HTTPS 终结点对其进行测试。 

## <a name="test-a-trained-utterance"></a>测试已训练的陈述
使用已发布的终结点来查询应用已知道的陈述。 由于 LUIS 已知道该陈述，因此得分高，并检测到实体。

1. 在[语言理解 (LUIS)][LUIS] 网站上，请在新应用的“发布”页上，选择“资源和密钥”部分中的终结点 URL。 

    ![发布终结点 URL](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. 通过浏览器，在 URL 末尾处 `q=` 之后添加以下查询。

    `I want a computer replacement`

    终结点会使用以下 JSON 进行响应：
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    意向得分为 0.973，实体检测得分为 0.846，得分都较高，这是因为对应用进行了此陈述的训练。 陈述位于 LUIS 应用中 GetHardware 的意向页上。 陈述的文本 `computer` 标记为“硬件”实体。 
    
    |状态|Word| 意向得分 | 实体得分 |
    |--|--|--|--|
    |训练结束| want | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>测试未训练的陈述
在浏览器中，使用相同的已发布终结点查询应用不知道的陈述：

`I require a computer replacement`

此陈述使用上一个陈述的同义词：

| 已训练的词 | 未训练的同义词 |
|--|--|
| want | require |

终结点响应为：

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| 状态 | Word | 意向得分 | 实体得分 |
|--|--|--|--|
| 训练结束| want | 0.973 | 0.846 |
| 未训练| require | 0.840 | - |

未训练的陈述意向得分低于已标记的陈述，因为 LUIS 知道该句子在语法上相同。 但 LUIS 不知道该陈述具有相同含义。 此外，如果没有短语列表，则找不到“硬件”实体。

必须训练 LUIS，告知此应用中“want”和“require”表示相同的意思，因为一个词可具有多种含义。 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>使用短语列表提高未训练陈述的得分 
1. 添加名为“want”和值为 `want` 的[短语列表](luis-how-to-add-features.md)功能，然后选择 Enter。

    > [!TIP]
    > 在每个词或短语之后，选择 Enter 键。 词或短语会添加到“短语列表值”框中，同时光标停留在“值”框中。 使用此功能可以快速输入多个值。

2. 若要查看 LUIS 建议的单词，请选择“建议”。 

    ![建议值](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. 添加所有词。 如果 `require` 不在建议列表中，请将其添加为所需的值。 

4. 由于这些单词是同义词，请保留“可交换”设置，然后选择“保存”。

    ![短语列表值](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. 在顶部导航栏中，选择“训练”以训练应用，但请勿发布应用。 现在已有两个模型。 可以比较两个模型中的值。

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>将短语列表模型和已发布模型进行比较
在此应用中，未使用同义词训练已发布模型。 只有当前已编辑的模型包含同义词的短语列表。 若要比较模型，请使用[交互式测试](interactive-test.md#interactive-testing)。 

1. 打开“测试”窗格，并输入以下陈述：

    `I require a computer replacement`

2. 若要打开检查面板中，请选择“检查”。 

    ![选择“检查”](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. 若要对已发布模型和新短语列表模型进行比较，请选择“与已发布的模型进行比较”。

    ![检查已发布模型与当前模型](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

添加短语列表后，可发现陈述准确性有所提高，并且可找到“硬件”实体。 

|状态 | 短语列表| 意向得分 | 实体得分 |
|--|--|--|--|
| 已发布 | - | 0.84 | - |
| 当前正在编辑 |✔| 0.92 | 标识的硬件实体 |

> [!TIP]
> * 通过[交互式测试](interactive-test.md#interactive-testing)，可以将发布的模型与发布后进行的任何训练方面的更改进行比较。 
> * 通过[终结点测试](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser)，可以查看精确的 LUIS 响应 JSON。 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>使用终结点测试获取实体得分
若要查看实体得分，请[发布模型](luis-how-to-publish-app.md)并查询终结点。 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

“硬件”实体在短语列表中显示得分为 0.595。 使用短语列表前，未检测到该实体。 

|状态 | 短语列表| 意向得分 | 实体得分 |
|--|--|--|--|
| 已发布 | - | 0.84 | - |
| 当前正在编辑 |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，在应用列表中选择应用名称右侧的省略号 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用终结点查询获取表述预测](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
