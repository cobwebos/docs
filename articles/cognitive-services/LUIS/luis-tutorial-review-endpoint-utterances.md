---
title: 介绍如何在语言理解 (LUIS) 中审核终结点表述的教程 - Azure | Microsoft Docs
description: 本教程介绍如何在 LUIS 的人力资源 (HR) 领域中审核终结点表述。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: diberry
ms.openlocfilehash: 1f1e3310e0d02983aaecc3f87ba9c116d65b751b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237207"
---
# <a name="tutorial-review-endpoint-utterances"></a>教程：审核终结点表述
本教程会验证或纠正通过 LUIS HTTP 终结点收到的表述，以便改进应用预测。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解如何审核终结点表述 
> * 使用适合人力资源 (HR) 领域的 LUIS 应用 
> * 查看终结点话语
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

本文需要一个免费的 [LUIS](luis-reference-regions.md#luis-website) 帐户，以便能够创作 LUIS 应用程序。

## <a name="before-you-begin"></a>开始之前
如果没有[情绪](luis-quickstart-intent-and-sentiment-analysis.md)教程中提供的人力资源应用，请从 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json) Github 存储库导入该应用。 如果将本教程作为新导入的应用使用，则还需训练和发布表述，然后通过[脚本](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js)或浏览器中的终结点将其添加到终结点。 要添加的表述如下：

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `review`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

如果你通过这一系列的教程获取了所有版本的应用，你可能会吃惊地发现，“审核终结点表述”列表不因版本而异。 要审核的表述只有一个池，不管你经常编辑哪个版本的表述，也不管你在终结点上发布哪个版本的应用。 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>审核终结点表述的目的
此审核过程是 LUIS 了解应用领域的另一种方式。 LUIS 已在审核列表中选择了表述。 此列表具有以下特点：

* 特定于应用。
* 旨在改进应用的预测准确性。 
* 应该定期进行审核。 

可以通过审核终结点表述来验证或纠正表述的已预测意向。 另外，请标记未预测的自定义实体。 

## <a name="review-endpoint-utterances"></a>查看终结点话语

1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

    [ ![LUIS 应用的屏幕截图，其中已突出显示右上方导航栏中的“生成”](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. 从左侧导航栏中选择“审核终结点表述”。 列表会筛选出 **ApplyForJob** 意向。 

    [ ![左侧导航栏中“审核终结点表述”按钮的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. 切换“实体”视图，查看标记的实体。 
    
    [ ![“审核终结点表述”的屏幕截图，突出显示了“实体”视图切换](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |话语|正确的意向|缺失的实体|
    |:--|:--|:--|
    |我要找一份使用自然语言处理的工作|GetJobInfo|工作 -“自然语言处理”|

    此表述的意向不正确，其分数低于 50%。 **ApplyForJob** 意向有 21 个表述，而 **GetJobInformation** 意向则有 7 个表述。 除了应正确匹配终结点表述，还应向 **GetJobInformation** 意向添加更多的表述。 这是一项练习，需要你自行完成。 每个意向（**None** 意向除外）的示例表述数目应该基本相同。 **None** 意向应包含应用中总表述数的 10%。 

    处于“令牌视图”位置时，可以将鼠标悬停在表述中的任意蓝色文本上，以便查看预测的实体名称。 

3. 对于表述 `I'm looking for a job with Natual Language Processing`，请在“匹配的意向”列中选择正确的意向 **GetJobInformation**。 

    [ ![“审核终结点表述”的屏幕截图，将表述与意向进行了匹配](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. 在同一表述中，`Natural Language Processing` 的实体为 keyPhrase。 这本应是“工作”实体。 选择 `Natural Language Processing`，然后从列表选择“工作”实体。

    [ ![“审核终结点表述”的屏幕截图，已在表述中对实体进行了标记](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. 在同一行的“添加到匹配的意向”列中选择画圈的复选标记。 

    [ ![在意向中确定表述匹配项的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    此操作将表述从“审核终结点表述”移到 **GetJobInformation** 意向。 此终结点表述现在是该意向的一个示例表述。 

6. 审核此意向中的剩余表述，标记不正确的表述并纠正“匹配的意向”。

7. 如果所有表述都正确，请选择每一行的复选框，然后选择“添加所选项”，以便正确匹配这些表述。 

    [ ![确定剩余表述与匹配的意向的关系的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. 此列表应该再也不会有这些表述。 如果出现更多的表述，请继续完成列表中的项目，纠正意向并标记缺失的实体，直至此列表为空。 选择“筛选器”列表中的下一意向，然后继续纠正表述并对实体进行标记。 请记住，每个意向的最后一步是选择表述行中的“添加到匹配的意向”，或者勾选每个意向的框，然后选择表上面的“添加所选项”。 

    这是一个很小的应用。 审核过程只需数分钟。

## <a name="add-new-job-name-to-phrase-list"></a>向短语列表添加新的工作名称
不断向短语列表添加新发现的工作名称。 

1. 从左侧导航栏中选择“短语列表”。

2. 选择“工作”短语列表。

3. 将 `Natural Language Processing` 作为值添加，然后选择“保存”。 

## <a name="train-the-luis-app"></a>训练 LUIS 应用
在训练之前，LUIS 并不知道这些更改。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中 LUIS 应用的更新模型，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

2. 如果已导入此应用，则需选择“情绪分析”。 

3. 选择“生产”槽和“发布”按钮。

4. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-an-utterance"></a>使用陈述查询终结点
尝试一个与纠正的表述相近的表述。 

1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

2. 将光标定位到地址中 URL 的末尾，并输入 `Are there any natural language processing jobs in my department right now?`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

```JSON
{
  "query": "are there any natural language processing jobs in my department right now?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.9247605
  },
  "intents": [
    {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    {
      "intent": "ApplyForJob",
      "score": 0.129989788
    },
    {
      "intent": "FindForm",
      "score": 0.006438211
    },
    {
      "intent": "EmployeeFeedback",
      "score": 0.00408575451
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00194211153
    },
    {
      "intent": "None",
      "score": 0.00166400627
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00118593348
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0007885918
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0006373631
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0005980781
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.719905E-05
    }
  ],
  "entities": [
    {
      "entity": "right now",
      "type": "builtin.datetimeV2.datetime",
      "startIndex": 64,
      "endIndex": 72,
      "resolution": {
        "values": [
          {
            "timex": "PRESENT_REF",
            "type": "datetime",
            "value": "2018-07-05 15:23:18"
          }
        ]
      }
    },
    {
      "entity": "natural language processing",
      "type": "Job",
      "startIndex": 14,
      "endIndex": 40,
      "score": 0.9869922
    },
    {
      "entity": "natural language processing jobs",
      "type": "builtin.keyPhrase",
      "startIndex": 14,
      "endIndex": 45
    },
    {
      "entity": "department",
      "type": "builtin.keyPhrase",
      "startIndex": 53,
      "endIndex": 62
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8251864
  }
}
}
```

通过一个高分预测到了正确意向，检测到的“工作”实体为 `natural language processing`。 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>是否可以通过添加更多的表述来代替审核？ 
你可能会想，为何不添加更多的示例表述呢？ 审核终结点表述的目的是什么？ 在实际的 LUIS 应用中，终结点表述来自各个用户，其遣词造句方式与你并不相同。 如果同样的词汇和句式多次使用，则原始预测的百分比会更高。 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>为何顶级意向会包含在表述列表中？ 
某些终结点表述在审核列表中的百分比会很高。 仍需审核并验证这些表述。 这些顶级意向之所以在列表中，是因为次高分数意向的分数与顶级意向的分数很接近。 

## <a name="what-has-this-tutorial-accomplished"></a>本教程实现了哪些目的？
通过审核终结点提供的表述，应用预测准确性得到了提高。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 在左上侧菜单中选择“我的应用”。 在应用列表中选择应用名称右侧的省略号 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何使用模式](luis-tutorial-pattern.md)
