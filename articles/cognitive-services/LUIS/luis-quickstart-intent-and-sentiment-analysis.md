---
title: 情绪分析
titleSuffix: Azure Cognitive Services
description: 在本教程中，我们将创建一个应用，用于演示如何从话语中获取积极、消极和中性的情绪。 情绪是从整个话语确定的。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: b9465e7965c5f1c62134cf309ef0efe914e14f8d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215820"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>教程：获取话语的情绪

在本教程中，我们将创建一个应用，用于演示如何从话语中确定积极、消极和中性的情绪。 情绪是从整个话语确定的。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 创建新应用
> * 将情绪分析添加为发布设置
> * 训练应用
> * 发布应用
> * 从终结点获取获取话语的情绪

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>情绪分析是一项发布设置

以下陈述显示了情绪的示例：

|情绪|得分|陈述|
|:--|:--|:--|
|积极|0.91 |John W. Smith 在巴黎的演讲做得很好。|
|积极|0.84 |西雅图工程师对 Parker 的推销工作做得很好。|

情绪分析是一种适用于所有话语的发布设置。 不需要在陈述中查找指示情绪的单词并对其进行标记。 

因为它是发布设置，所以不会在意向或实体页上看到它。 可以在[交互式测试](luis-interactive-test.md#view-sentiment-results)窗格中或在终结点 URL 处进行测试时看到它。 


## <a name="create-a-new-app"></a>创建新应用

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>添加 PersonName 预生成实体 


1. 在左侧导航菜单中选择“实体”。

1. 选择“添加预生成的实体”按钮。

1. 从预生成的实体列表中选择以下实体，然后选择“完成”：

    * **[PersonName](luis-reference-prebuilt-person.md)** 

    ![在“预生成的实体”对话框中选择的数字的屏幕截图](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>创建用于确定员工反馈的意向

添加一个新意向来从公司的成员捕获员工反馈。 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 选择“创建新意向”。

3. 将新意向命名为 `EmployeeFeedback`。

    ![以 EmployeeFeedback 为名称创建新意向的对话框](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 添加几项陈述来指明员工某项工作做得很好或者某个方面需要改进：

    |陈述|
    |--|
    |John Smith 做了很好的工作来欢迎一位同事从产假归来。|
    |Jill Jones 在一位同事悲伤时做了很好的安慰工作。|
    |Bob Barnes 没有文书工作所需的所有发票。|
    |Todd Thomas 在一个月后上交了没有签名的必需表单|
    |Katherine Kelly 没有赶上重要的市场营销场外会议。|
    |Denise Dillard 错过了六月份的评审会议。|
    |Mark Mathews 在哈佛进行了令人震撼的推销|
    |Walter Williams 在斯坦福的演讲做得很好|

    [ ![在 EmployeeFeedback 意向中包含了示例陈述的 LUIS 应用的屏幕截图](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>将话语示例添加到 None 意向 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>训练应用，以便可以测试对意向所做的更改 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>将应用配置为包含情绪分析

1. 在右上角的导航栏中选择“管理”，然后从左侧菜单中选择“发布设置”。

1. 选择“情绪分析”以启用此设置。 

    ![开启“情绪分析”作为发布设置](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>发布应用，以便可以从终结点查询已训练的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>从终结点获取话语的情绪

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 将光标定位到地址中 URL 的末尾，并输入 `Jill Jones work with the media team on the public portal was amazing`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含所提取的情绪分析的 `EmployeeFeedback` 意向。
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
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

    sentimentAnalysis 是积极的，评分为 86%。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相关信息

* 情绪分析由认知服务[文本分析](../Text-Analytics/index.yml)提供。 此功能仅限于文本分析[支持的语言](luis-language-support.md##languages-supported)。
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)


## <a name="next-steps"></a>后续步骤
本教程将情绪分析添加为发布设置，以从整个话语中提取情绪值。

> [!div class="nextstepaction"] 
> [在 HR 应用中审核终结点表述](luis-tutorial-review-endpoint-utterances.md) 

