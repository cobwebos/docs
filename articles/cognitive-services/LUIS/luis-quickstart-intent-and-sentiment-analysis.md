---
title: 教程：创建返回情绪分析的 LUIS 应用 - Azure | Microsoft Docs
description: 本教程介绍如何将情绪分析添加到 LUIS 应用，以便在陈述中分析积极、消极和中性的情感。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: baa449bb9e78a5c6437b0a9528e5d1f10dfa519f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520446"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>教程：9。  添加情绪分析
在本教程中，我们将创建一个应用，用于演示如何从陈述中提取积极、消极和中性的情绪。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解情绪分析
> * 在人力资源 (HR) 域中使用 LUIS 应用 
> * 添加情绪分析
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>开始之前
如果还没有 [keyPhrase 实体](luis-quickstart-intent-and-key-phrase.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json) Github 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `sentiment`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

## <a name="sentiment-analysis"></a>情绪分析
情绪分析是确定用户陈述是积极、消极还是中性的功能。 

以下陈述显示了情绪的示例：

|情绪|得分|陈述|
|:--|:--|:--|
|积极|0.91 |John W. Smith 在巴黎的演讲做得很好。|
|积极|0.84 |jill-jones@mycompany.com 对 Parker 的推销工作做得很好。|

情绪分析是应用于每个陈述的应用设置。 你不必在陈述中找到表示情感的词语并为它们加注标签，因为情绪分析应用于整个陈述。 

## <a name="add-employeefeedback-intent"></a>添加 EmployeeFeedback 意向 
添加一个新意向来从公司的成员捕获员工反馈。 

1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

    [![LUIS 应用的屏幕截图，其中已突出显示右上方的导航栏](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. 选择“创建新意向”。

    [![LUIS 应用的屏幕截图，其中已突出显示右上方的导航栏](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. 将新意向命名为 `EmployeeFeedback`。

    ![以 EmployeeFeedback 为名称创建新意向的对话框](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 添加几项陈述来指明员工某项工作做得很好或者某个方面需要改进：

    请记住，在此人力资源应用中，员工是在列表实体 `Employee` 中通过姓名、电子邮件、电话分机号码、移动电话号码及其美国联邦社会安全号码定义的。 

    |陈述|
    |--|
    |425-555-1212 做了很好的工作来欢迎一位同事从产假归来。|
    |234-56-7891 在一位同事悲伤时做了很好的安慰工作。|
    |jill-jones@mycompany.com 没有文书工作所需的所有发票。|
    |john.w.smith@mycompany.com 在一个月后上交了没有签名的必需表单|
    |x23456 没有赶上重要的市场营销场外会议。|
    |x12345 错过了六月份的评审会议。|
    |Jill Jones 在哈佛进行了令人震撼的推销|
    |John W. Smith 在斯坦福的演讲做得很好|

    [ ![在 EmployeeFeedback 意向中包含了示例陈述的 LUIS 应用的屏幕截图](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>将应用配置为包含情绪分析
在“发布”页上配置情绪分析。 

1. 在右上方的导航栏中选择“发布”。

    ![“意向”页的屏幕截图，其中已展开“发布”按钮 ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. 选择“启用情绪分析”。 

## <a name="publish-app-to-endpoint"></a>将应用发布到终结点

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-an-utterance"></a>使用陈述查询终结点

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Jill Jones work with the media team on the public portal was amazing`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含所提取的情绪分析的 `EmployeeFeedback` 意向。

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

sentimentAnalysis 是积极的，得分为 0.86。 

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用已启用情绪分析，识别了自然语言查询意向，并返回了包含总体情绪（评分形式）的提取数据。 

现在，聊天机器人已获得足够的信息，可以确定对话中的下一步。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和陈述中的情绪数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [在 HR 应用中审核终结点表述](luis-tutorial-review-endpoint-utterances.md) 

