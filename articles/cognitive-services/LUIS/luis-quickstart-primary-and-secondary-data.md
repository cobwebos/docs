---
title: 教程：创建 LUIS 应用以提取数据 - Azure | Microsoft Docs
description: 本教程介绍如何使用意向和简单实体创建一个简单的 LUIS 应用，以提取机器学习的数据。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: e6ab9d1db0144ffa68fe9dc3381ba31d57aa0cae
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130884"
---
# <a name="tutorial-6-add-simple-entity-and-phrase-list"></a>教程：6. 添加简单实体和短语列表
在本教程中，我们将使用**简单**实体创建一个应用，用于演示如何从陈述中提取机器学习的数据。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解简单实体 
> * 创建适用于人力资源 (HR) 领域的新 LUIS 应用 
> * 添加用于从应用提取工作的简单实体
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应
> * 添加用于增强工作词汇信号的短语列表
> * 训练、发布应用并反复查询终结点

本文需要一个免费的 [LUIS](luis-reference-regions.md#luis-website) 帐户，以便创作 LUIS 应用程序。

## <a name="before-you-begin"></a>开始之前
如果尚未获得[分层实体](luis-quickstart-intent-and-hier-entity.md)教程中所述的人力资源应用，请将 JSON [导入](create-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) Github 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `simple`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。  

## <a name="purpose-of-the-app"></a>应用的用途
此应用演示如何从陈述中提取数据。 假设某个聊天机器人包含以下陈述：

|陈述|可提取的工作名称|
|:--|:--|
|我想要申请新的会计工作。|会计|
|请提交我的申请工程职位的简历。|工程|
|填写 123456 工作的申请|123456|

本教程会添加用于提取工作名称的新实体。 

## <a name="purpose-of-the-simple-entity"></a>简单实体的用途
在此 LUIS 应用中，简单实体的用途是让 LUIS 知道工作名称是什么，以及在陈述中的哪个位置可以找到该工作名称。 根据选择的词汇和陈述的长度，工作在陈述中的位置因陈述而异。 LUIS 需要跨所有意向获取任一陈述的工作示例。  

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

此 LUIS 应用包含多个意向的工作名称。 通过在所有意向的陈述中标记这些词汇，LUIS 可以深入了解什么是工作，以及在陈述的什么位置找到它。

## <a name="create-job-simple-entity"></a>创建工作简单实体

1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

    [ ![LUIS 应用的屏幕截图，其中已突出显示右上方导航栏中的“生成”](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. 在“意向”页上，选择“ApplyForJob”意向。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "LUIS 的屏幕截图，其中已突出显示“ApplyForJob”意向")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. 在陈述`I want to apply for the new accounting job`中选择`accounting`，在弹出菜单的顶部字段中输入`Job`，然后在弹出菜单中选择“创建新实体”。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "LUIS 的屏幕截图，其中已突出显示包含创建实体步骤的“ApplyForJob”意向")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. 在弹出窗口中验证实体名称和类型，然后选择“完成”。

    ![创建名称为“工作”、类型为“简单”的简单实体弹出模式对话框](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. 在陈述 `Submit resume for engineering position` 中，将 `engineering` 一词标记为“工作”实体。 选择 `engineering` 一词，然后从弹出菜单中选择“工作”。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "LUIS 的屏幕截图，其中已突出标记工作实体")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    所有陈述都已标记，但五个陈述还不足以教会 LUIS 什么是工作相关词汇和短语。 使用数值的工作不需更多示例，因为使用的是正则表达式实体。 使用词汇或短语的工作至少还需要 15 个示例。 

6. 添加更多的陈述并将工作词汇或短语标记为“工作”实体。 就某项就业服务来说，工作类型通常是跨就业的。 如果所需工作与特定行业相关，则工作词汇应反映这一点。 

    |陈述|工作实体|
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

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>针对 GetJobInformation 意向标记示例陈述中的实体
1. 在左侧菜单中选择“意向”。

2. 从意向列表中选择“GetJobInformation”。 

3. 标记示例陈述中的工作：

    |陈述|工作实体|
    |:--|:--|
    |是否有数据库方面的工作？|数据库|
    |寻找会计方面的新职位|会计|
    |为高级工程师提供了哪些职位？|高级工程师|

    还有其他示例陈述，但这些陈述不包含工作词汇。

## <a name="train-the-luis-app"></a>训练 LUIS 应用
LUIS 在训练之前，并不知道意向和实体（模型）发生的变化。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![选择训练按钮](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功通知](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

2. 选择“生产”槽和“发布”按钮。

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "“发布”页的屏幕截图，其中已突出显示“发布到生产槽”按钮")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的陈述查询终结点
在“发布”页的底部，选择“终结点”链接。 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "“发布”页的屏幕截图，其中已突出显示终结点")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 将光标定位到地址中 URL 的末尾，并输入 `Here is my c.v. for the programmer job`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回 `ApplyForJob` 陈述。

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

## <a name="to-boost-signal-add-jobs-phrase-list"></a>若要增强信号，请添加工作短语列表
打开 LUIS-Samples Github 存储库中的 [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv)。 此列表的工作词汇和短语超过一千个。 查看列表中是否有对你有意义的工作词汇。 如果列表中没有你的词汇或短语，请添加你自己的词汇或短语。

1. 在 LUIS 应用的“生成”部分，选择“提高应用性能”菜单下的“短语列表”。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "突出显示的“短语列表”左侧导航按钮的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. 选择“创建新的短语列表”。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "突出显示的“创建新的短语列表”按钮的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. 将新的短语列表命名为`Jobs`，然后将列表从 jobs-phrase-list.csv 复制到“值”文本框中。 按 Enter。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "“创建新的短语列表”弹出对话框的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    如果需要将更多词汇添加到短语列表中，请查看“相关值”，然后添加相关值。 

4. 选择“保存”，激活短语列表。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "“创建新的短语列表”弹出对话框的屏幕截图，在短语列表值框中已填充了词汇")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. 再次[训练](#train-the-luis-app)并[发布](#publish-the-app-to-get-the-endpoint-URL)应用，以便使用短语列表。

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

## <a name="phrase-lists"></a>短语列表
添加短语列表增强了列表中词汇的信号，但**不可**将其用作完全匹配。 短语列表中有多个工作的第一个字为 `lead`，而且也有名为 `welder` 的工作，但是没有名为 `lead welder` 的工作。 此工作短语列表可能不完整。 在定期[查看终结点陈述](label-suggested-utterances.md)和查找其他工作词汇时，请将其添加到短语列表， 然后重新训练并重新发布。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用包含一个简单实体和一个由词汇组成的短语列表，识别了自然语言查询意向并返回了作业数据。 

现在，聊天机器人已获得足够的信息，可以确定申请工作的主要操作、该操作的参数以及所推荐的具体工作。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 通话应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以便使用第三方 API 向人力资源代表发送工作信息。 如果机器人或调用方应用程序有其他编程选项，LUIS 不会执行相关的工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 在左上侧菜单中选择“我的应用”。 在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加预生成的 keyphrase 实体](luis-quickstart-intent-and-key-phrase.md)