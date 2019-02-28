---
title: 预测意向
titleSuffix: Azure Cognitive Services
description: 在本教程中，创建可预测用户意向的自定义应用。 此应用是最简单类型的 LUIS 应用，因为它不从话语文本（例如电子邮件地址或日期）提取各种数据元素。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 067829a1d9425ede1320242e364eca7c30bb7053
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593907"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>教程：生成 LUIS 应用，以确定用户意向

在本教程中，请创建一个自定义人力资源 (HR) 应用，以便根据话语（文本）预测用户的意图。 

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建新应用 
> * 创建意向
> * 添加示例话语
> * 训练应用
> * 发布应用
> * 从终结点获取意向


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>将用户意向用作意向

此应用的目的是确定聊天性的自然语言文本的意图： 

`Are there any new positions in the Seattle office?`

这些意图可以归类为**意向**。 

此应用具有多个意向。 

|意向|目的|
|--|--|
|ApplyForJob|确定用户是否正在申请工作。|
|GetJobInformation|确定用户是否正在寻找有关常规工作或特定工作的信息。|
|无|确定用户是否正在询问应用不应回答的问题。 此意向如果作为应用创建的一部分提供，则无法删除。 |

## <a name="create-a-new-app"></a>创建新应用

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>创建工作信息的意向

1. 选择“创建新意向”。 输入新意向名称 `GetJobInformation`。 当某位用户想要获取公司空缺职位的信息时，就会预测此意向。 

    ![语言理解 (LUIS) 新建意向对话框的屏幕截图](media/luis-quickstart-intents-only/create-intent.png "Screenshot of Language Understanding (LUIS) New intent dialog")

1. 选择“完成”。

2. 将多个示例话语添加到你预期用户会请求的此意向：

    | 示例陈述|
    |--|
    |今天是否发布了任何新作业？|
    |在西雅图办事处是否有任何新职位？|
    |是否为工程师开放了远程辅助角色或远程办公工作？|
    |是否有数据库方面的工作？|
    |我在寻找在坦帕办事处联合办公的职位。|
    |在旧金山办事处是否有实习机会？|
    |是否有任何在大学里的兼职工作？|
    |寻找会计方面的新职位|
    |寻找纽约市的双语人士工作。|
    |寻找会计方面的新职位。|
    |是否为新作业？|
    |显示过去 2 天内添加的所有工程师工作。|
    |今日工作招聘呢？|
    |在伦敦办事处开设了哪些会计职位？|
    |为高级工程师提供了哪些职位？|
    |作业列表位于何处|

    [![为 MyStore 意向输入新话语的屏幕截图](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Screenshot of entering new utterances for MyStore intent")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    可通过提供示例话语来训练 LUIS，让其了解应该根据哪些类型的话语预测出此意向。 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>将话语示例添加到 None 意向 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>在测试或发布前训练应用

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>发布应用，从终结点进行查询

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>从终结点获取意向预测

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 将光标定位到地址栏中 URL 的末尾，并输入 `I'm looking for a job with Natural Language Processing`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此话语不同于任何示例话语。 它非常适合用于测试，测试结果应返回 `GetJobInformation` 意向（评分最高的意向）。 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    `verbose=true` 查询字符串参数意味着在应用的查询结果中包含“所有意向”。 实体数组为空，因为此应用当前没有任何实体。 

    JSON 结果中标识了评分最高的意向作为 **`topScoringIntent`** 属性。 所有评分介于 1 和 0 之间，评分越接近 1 越好。 

## <a name="create-intent-for-job-applications"></a>创建工作应用程序的意向

返回到 LUIS 门户并创建一个新的意向，以便确定用户话语是否与申请工作有关。

1. 在右上侧菜单中选择“生成”，返回到应用生成界面。

1. 从左侧菜单中选择“意向”以进入意向列表。

1. 选择“创建新意向”，并输入名称 `ApplyForJob`。 

    ![用于创建新意向的 LUIS 对话框](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. 将多个陈述添加到用户预期会请求的此意向，例如：

    | 示例陈述|
    |--|
    |Fill out application for Job 123456|
    |Here is my c.v. for position 654234|
    |这是兼职接待员职位的简历。|
    |我正在申请处理文书工作的艺术办公工作。|
    |我正在申请在圣地亚哥研发学院进行暑期实习|
    |我正在请求将我的简历投递到自助餐厅的临时职位。|
    |我正在向俄亥俄州哥伦布的新汽车团队提交我的个人简历|
    |I want to apply for the new accounting job|
    |此处是 456789 会计实习文书工作|
    |Job 567890 and my paperwork|
    |附加了塔尔萨会计实习的论文。|
    |在假期送货的文书工作|
    |请将发送我的简历，以申请西雅图的新会计工作|
    |Submit resume for engineering position|
    |这是我申请 坦帕 234123 邮局。|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>再次训练

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>再次发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>再次获取意向预测

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 在新浏览器窗口中，在 URL 的末尾输入 `Can I submit my resume for job 235986`。 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    结果包含新意向 **ApplyForJob** 和现有意向。 

## <a name="client-application-next-steps"></a>客户端应用程序后续步骤

LUIS 在返回 JSON 响应后，就已经完成了此请求。 LUIS 不提供用户话语的应答，只会识别以自然语言请求的信息类型。 会话跟进由客户端应用程序（如 Azure 机器人）提供。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相关信息

* [实体类型](luis-concept-entity-types.md)
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)
* [Azure 机器人](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>后续步骤

本教程创建了人力资源 (HR) 应用、创建了 2 个意向、向每个意向添加了示例话语、向 None 意向添加了示例话语，并在终结点上进行了训练、发布和测试操作。 这些是生成 LUIS 模型的基本步骤。 

继续使用此应用，[添加简单的实体和短语列表](luis-quickstart-primary-and-secondary-data.md)。

> [!div class="nextstepaction"]
> [将预生成的意向和实体添加到此应用](luis-tutorial-prebuilt-intents-entities.md)
