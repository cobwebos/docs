---
title: 教程 7：LUIS 中使用短语列表的简单实体
titleSuffix: Azure Cognitive Services
description: 从话语中提取机器学习的数据
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 4647e663f4a2734451835f90d2687bb6aaf91712
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277794"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>教程 7：通过简单实体和短语列表提取名称

在本教程中，请使用**简单**实体从话语中提取雇佣工作名称的机器学习数据。 若要提高提取的准确性，请添加一个短语列表，其中包含特定于简单实体的术语。

本教程会添加用于提取工作名称的新的简单实体。 在此 LUIS 应用中，简单实体的用途是让 LUIS 知道工作名称是什么，以及在陈述中的哪个位置可以找到该工作名称。 根据选择的词汇和话语的长度，工作名称在话语中的位置因话语而异。 LUIS 需要跨所有意向的工作名称的示例，而这些意向使用工作名称。  

出现以下情况时，简单实体适用于此类数据：

* 数据是单一概念。
* 数据没有严格的格式，例如正则表达式。
* 数据不常见，例如电话号码或数据的预生成实体。
* 数据并不与已知单词的列表完全匹配，例如列表实体。
* 数据不包含其他数据项，例如复合实体或层次结构实体。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用现有的教程应用
> * 添加用于从应用提取工作的简单实体
> * 添加用于增强工作词汇信号的短语列表
> * 定型 
> * 发布 
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用

继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `simple`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="simple-entity"></a>简单实体
简单实体检测在单词或短语中包含的单个数据概念。

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

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 在“意向”页上，选择“ApplyForJob”意向。 

3. 在陈述`I want to apply for the new accounting job`中选择`accounting`，在弹出菜单的顶部字段中输入`Job`，然后在弹出菜单中选择“创建新实体”。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "LUIS 的屏幕截图，其中已突出显示包含创建实体步骤的“ApplyForJob”意向")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. 在弹出窗口中验证实体名称和类型，然后选择“完成”。

    ![创建名称为“工作”、类型为“简单”的简单实体弹出模式对话框](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. 在陈述 `Submit resume for engineering position` 中，将 `engineering` 一词标记为“工作”实体。 选择 `engineering` 一词，然后从弹出菜单中选择“工作”。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "LUIS 的屏幕截图，其中已突出标记工作实体")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    所有陈述都已标记，但五个陈述还不足以教会 LUIS 什么是工作相关词汇和短语。 使用数值的工作不需更多示例，因为使用的是正则表达式实体。 使用词汇或短语的工作至少还需要 15 个示例。 

6. 添加更多的陈述并将工作词汇或短语标记为“工作”实体。 就某项就业服务来说，工作类型通常是跨就业的。 如果所需工作与特定行业相关，则工作词汇应反映这一点。 

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
    |我想要申请摄影方面的职位。|摄影|git 

## <a name="label-entity-in-example-utterances"></a>在示例话语中标记实体

对实体进行标记可以向 LUIS 显示实体在示例话语中位于何处。

1. 在左侧菜单中选择“意向”。

2. 从意向列表中选择“GetJobInformation”。 

3. 标记示例陈述中的工作：

    |话语|工作实体|
    |:--|:--|
    |是否有数据库方面的工作？|数据库|
    |寻找会计方面的新职位|会计|
    |为高级工程师提供了哪些职位？|高级工程师|

    还有其他示例陈述，但这些陈述不包含工作词汇。

## <a name="train"></a>定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Here is my c.v. for the programmer job`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回 `ApplyForJob` 陈述。

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    LUIS 发现了正确的意向 **ApplyForJob** 并提取了正确的实体 **Job**，其值为 `programmer`。


## <a name="names-are-tricky"></a>名称很微妙
LUIS 应用通过高置信度来查找正确的意向，然后提取工作名称，但名称是很微妙的。 试试这个陈述：`This is the lead welder paperwork`。  

在下面的 JSON 中，LUIS 使用正确的意向 `ApplyForJob` 进行了响应，但没有提取工作名称 `lead welder`。 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

由于名称可以是任何词汇，因此如果有一个包含词汇的短语列表来增强信号，则 LUIS 在预测实体时会更准确。

## <a name="to-boost-signal-add-phrase-list"></a>若要增强信号，请添加短语列表

打开 LUIS-Samples Github 存储库中的 [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv)。 此列表的工作词汇和短语超过一千个。 查看列表中是否有对你有意义的工作词汇。 如果列表中没有你的词汇或短语，请添加你自己的词汇或短语。

1. 在 LUIS 应用的“生成”部分，选择“提高应用性能”菜单下的“短语列表”。

2. 选择“创建新的短语列表”。 

3. 将新的短语列表命名为`Job`，然后将列表从 jobs-phrase-list.csv 复制到“值”文本框中。 按 Enter。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "“创建新的短语列表”弹出对话框的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    如果需要将更多词汇添加到短语列表中，请查看“相关值”，然后添加相关值。 

4. 选择“保存”，激活短语列表。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "“创建新的短语列表”弹出对话框的屏幕截图，在短语列表值框中已填充了词汇")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. 再次[训练](#train)并[发布](#publish)应用，以便使用短语列表。

6. 使用同一陈述（`This is the lead welder paperwork.`）在终结点反复查询

    JSON 响应包含提取的实体：

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，人力资源应用使用一个机器学习的简单实体来查找话语中的工作名称。 由于工作名称可能包含各种单词或短语，因此应用需要一个短语列表来增强工作名称单词的信号。 

> [!div class="nextstepaction"]
> [添加预生成的 keyphrase 实体](luis-quickstart-intent-and-key-phrase.md)