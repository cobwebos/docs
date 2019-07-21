---
title: 简单实体，短语列表
titleSuffix: Azure Cognitive Services
description: 在本教程中，请使用简单实体从话语中提取雇佣工作名称的机器学习数据。 若要提高提取的准确性，请添加一个短语列表，其中包含特定于简单实体的术语。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: a03a1224451411617f38c8cecafbef008aa08916
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276217"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>教程：通过简单实体和短语列表提取名称

在本教程中，请使用**简单**实体从话语中提取雇佣工作名称的机器学习数据。 若要提高提取的准确性，请添加一个短语列表，其中包含特定于简单实体的术语。

简单实体检测在单词或短语中包含的单个数据概念。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 导入示例应用
> * 添加简单实体 
> * 添加用于增强词汇信号的短语列表
> * 定型 
> * 发布 
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>简单实体

本教程会添加用于提取工作名称的新的简单实体。 在此 LUIS 应用中，简单实体的用途是让 LUIS 知道工作名称是什么，以及在陈述中的哪个位置可以找到该工作名称。 根据选择的词汇和话语的长度，工作名称在话语中的位置因话语而异。 LUIS 需要跨所有意向的工作名称的示例，而这些意向使用工作名称。  

出现以下情况时，简单实体适用于此类数据：

* 数据是单一概念。
* 数据没有严格的格式，例如正则表达式。
* 数据不常见，例如电话号码或数据的预生成实体。
* 数据并不与已知单词的列表完全匹配，例如列表实体。
* 数据不包含其他数据项，例如复合实体或上下文的角色。

假设某个聊天机器人包含以下话语：

|话语|可提取的工作名称|
|:--|:--|
|我想要申请新的会计工作。|会计|
|提交我的申请工程职位的简历。|工程|
|填写 123456 工作的申请|123456|

工作名称很难确定，因为它可以是名词、动词，也可以是包含多个词汇的短语。 例如：

|作业|
|--|
|工程师|
|软件工程师|
|高级软件工程师|
|工程团队主管 |
|空管|
|汽车驾驶员|
|救护车驾驶员|
|看管人|
|挤压机操作员|
|技工|

此 LUIS 应用包含多个意向的工作名称。 通过在所有意向的话语中标记这些词汇，LUIS 可以深入了解什么是工作名称，以及在话语的什么位置找到它。

在示例话语中标记实体以后，必须添加短语列表来加强简单实体的信号。 短语列表**不**用作完全匹配，不需要是预期的每个可能的值。 

## <a name="import-example-app"></a>导入示例应用

1.  从意向教程中下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”  部分的“版本”  选项卡上，克隆版本并将其命名为 `simple`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>在意向的示例话语中标记实体

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 在“意向”页上，选择“ApplyForJob”意向。   

1. 在陈述`I want to apply for the new accounting job`中选择`accounting`，在弹出菜单的顶部字段中输入`Job`，然后在弹出菜单中选择“创建新实体”。  

    [![带有“ApplyForJob”意向的 LUIS 的屏幕截图，其中突出显示了创建实体的步骤](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Screenshot of LUIS with 'ApplyForJob' intent with create entity steps highlighted")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. 在弹出窗口中验证实体名称和类型，然后选择“完成”。 

    ![创建名称为“工作”、类型为“简单”的简单实体弹出模式对话框](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. 在剩余的话语中，选择词汇或短语，然后从弹出菜单中选择“工作”，将与工作有关的词汇标记为“工作”实体   。 

    [![LUIS 标签的屏幕截图，突出显示了工作实体](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Screenshot of LUIS labeling job entity highlighted")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>添加更多的示例话语并标记实体

简单的实体需要很多示例，从而提高预测的置信度。 
 
1. 添加更多的陈述并将工作词汇或短语标记为“工作”实体。  

    |话语|工作实体|
    |:--|:--|
    |我要申请研发方面的计划经理职务|计划经理|
    |这是我的帮厨申请。|帮厨|
    |我申请野营顾问的简历已附加。|野营顾问|
    |这是我申请行政 助理的简历。|行政助理|
    |我想要申请营销方面的管理工作。|管理、营销|
    |这是我申请新会计职位的简历。|会计|
    |我的吧员申请已包括在内。|吧员|
    |我提交的是屋顶工和屋架工申请。|屋顶工、屋架工|
    |这是我申请 公交车司机的简历|公交车司机|
    |我是一位注册护士。 这是我的简历。|注册护士|
    |我要提交的是申请我在报纸上看到的教学职位的书面材料。|教学|
    |这是我申请 果蔬存货管理员的简历。|存货管理员|
    |申请铺砖工作。|磁贴|
    |附加的是申请景观建筑师的简历。|景观建筑师|
    |我申请生物学教授的简历已包括在内。|生物学教授|
    |我想要申请摄影方面的职位。|摄影|

## <a name="mark-job-entity-in-other-intents"></a>在其他意向中标记工作实体

1. 在左侧菜单中选择“意向”。 

1. 从意向列表中选择“GetJobInformation”。  

1. 标记示例话语中的工作

    如果一个意向中的示例话语比另一个意向中多，则该意向更可能成为置信度最高的预测意向。 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>训练应用，以便可以测试对意向所做的更改 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>发布应用，以便可以从终结点查询已训练的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>从终结点获取意向和实体预测结果 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Here is my c.v. for the engineering job`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回 `ApplyForJob` 陈述。

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS 发现了正确的意向 **ApplyForJob** 并提取了正确的实体 **Job**，其值为 `engineering`。


## <a name="names-are-tricky"></a>名称很微妙
LUIS 应用通过高置信度来查找正确的意向，然后提取工作名称，但名称是很微妙的。 试试这个陈述：`This is the lead welder paperwork`。  

在下面的 JSON 中，LUIS 使用正确的意向 `ApplyForJob` 进行了响应，但没有提取工作名称 `lead welder`。 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

由于名称可以是任何词汇，因此如果有一个包含词汇的短语列表来增强信号，则 LUIS 在预测实体时会更准确。

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>为了增强与工作相关的词汇的信号，请添加工作相关词汇的短语列表

打开 Azure-Samples GitHub 存储库中的 [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv)。 此列表有超过 1,000 个工作词汇和短语。 查看列表中是否有对你有意义的工作词汇。 如果列表中没有你的词汇或短语，请添加你自己的词汇或短语。

1. 在 LUIS 应用的“生成”部分，选择“提高应用性能”菜单下的“短语列表”。   

1. 选择“创建新的短语列表”。  

1. 将新的短语列表命名为`JobNames`，然后将列表从 jobs-phrase-list.csv 复制到“值”文本框中。 

    [![“新建短语列表”弹出对话框的屏幕截图](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Screenshot of create new phrase list dialog pop-up")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    如果需要将更多词汇添加到短语列表中，请选择“建议”  ，查看新的“相关值”  ，然后添加任何相关值。 

    确保选中“这些值可互换”  ，因为这些值应全都被视为作业的同义词。 了解有关可互换和不可互换[短语列表概念](luis-concept-feature.md#how-to-use-phrase-lists)的更多信息。

1. 选择“完成”，以激活短语列表。 

    [![“新建短语列表”弹出对话框以及短语列表值框中字词的屏幕截图](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Screenshot of create new phrase list dialog pop-up with words in phrase list values box")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. 再次训练并发布应用，以便使用短语列表。

1. 使用同一陈述（`This is the lead welder paperwork.`）在终结点反复查询

    JSON 响应包含提取的实体：

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相关信息

* [无实体教程的意向](luis-quickstart-intents-only.md)
* [简单实体](luis-concept-entity-types.md)概念信息
* [短语列表](luis-concept-feature.md)概念信息
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)


## <a name="next-steps"></a>后续步骤

在本教程中，人力资源应用使用一个机器学习的简单实体来查找话语中的工作名称。 由于工作名称可能包含各种单词或短语，因此应用需要一个短语列表来增强工作名称单词的信号。 

> [!div class="nextstepaction"]
> [添加预生成的 keyphrase 实体](luis-quickstart-intent-and-key-phrase.md)
