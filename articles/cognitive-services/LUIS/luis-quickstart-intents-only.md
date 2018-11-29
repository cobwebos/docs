---
title: 教程 1：在自定义 LUIS 应用中查找意图
titleSuffix: Azure Cognitive Services
description: 创建可预测用户意图的自定义应用。 此应用是最简单类型的 LUIS 应用，因为它不从话语文本（例如电子邮件地址或日期）提取各种数据元素。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 30c9f572d77caacbeecf5f15d74fd8517e9fa883
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426853"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>教程 1：生成可确定用户意图的自定义应用

在本教程中，请创建一个自定义人力资源 (HR) 应用，以便根据话语（文本）预测用户的意图。 完成本教程后，会在云中运行一个 LUIS 终结点。

此应用的目的是确定聊天性的自然语言文本的意图。 这些意图可以归类为**意向**。 此应用具有多个意向。 第一个意向 **`GetJobInformation`** 识别用户何时需要公司内部提供的作业的信息。 第二个意向为 **`None`**，用于用户不在本应用领域（范围）内的任何话语。 稍后会添加第三个意向：**`ApplyForJob`**，适用于任何与申请工作有关的话语。 这第三个意向不同于 `GetJobInformation`，因为当某人申请工作时，工作信息应该已经公开。 但是，确定意向可能很困难，因为二者都是关于工作的，具体取决于单词的选择。

LUIS 在返回 JSON 响应后，就已经完成了此请求。 LUIS 不提供用户话语的应答，只会识别以自然语言请求的信息类型。 

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建新应用 
> * 创建意向
> * 添加示例话语
> * 训练应用
> * 发布应用
> * 从终结点获取意向

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>创建新应用

1. 使用 URL [https://www.luis.ai](https://www.luis.ai) 登录到 LUIS 门户。 

2. 选择“创建新应用”。  

    [![](media/luis-quickstart-intents-only/app-list.png "语言理解 (LUIS) 的“我的应用”页的屏幕截图")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. 在弹出的对话框中输入名称 `HumanResources`，保留默认的区域性“英语”。 将说明保留为空。

    ![LUIS 新应用](./media/luis-quickstart-intents-only/create-app.png)

    接下来，应用会显示具有 **None** 意向的“意向”页。

## <a name="getjobinformation-intent"></a>GetJobInformation 意向

1. 选择“创建新意向”。 输入新意向名称 `GetJobInformation`。 每当某个用户想要获取公司空缺职位的信息时，就会预测此意向。

    ![](media/luis-quickstart-intents-only/create-intent.png "语言理解 (LUIS) 的“新建意向”对话框的屏幕截图")

2. 可以通过提供示例话语来训练 LUIS，让其了解应该根据哪些类型的话语预测出此意向。 将多个示例话语添加到你预期用户会请求的此意向，例如：

    | 示例陈述|
    |--|
    |今天是否发布了任何新作业？|
    |为高级工程师提供了哪些职位？|
    |是否有数据库方面的工作？|
    |寻找会计方面的新职位|
    |作业列表位于何处|
    |是否为新作业？|
    |在西雅图办事处是否有任何新职位？|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "为 MyStore 意向输入新陈述的屏幕截图")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>None 意向 
客户端应用程序需要了解某个话语是否超出应用程序的主题领域。 如果 LUIS 返回某个话语的 **None** 意向，客户端应用程序可以询问用户是否要结束聊天。 如果用户不想要结束聊天，客户端应用程序还能提供有关继续聊天的更多指示。 

这些超出主题领域的示例话语归到 **None** 意向中。 请不要将此意向留空。 

1. 在左侧面板中选择“意向”。

2. 选择“None”意向。 添加用户可能会输入但与人力资源应用无关的 3 个话语。 如果应用与职位发布有关，则下面是一些合理的 **None** 话语：

    | 示例陈述|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|


## <a name="train"></a>定型 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>获取意向

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址栏中 URL 的末尾，并输入 `I'm looking for a job with Natural Language Processing`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此话语不同于任何示例话语。 它非常适合用于测试，测试结果应返回 `GetJobInformation` 意向（评分最高的意向）。 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

    结果包含应用中的**所有意向**，目前为 2 个意向。 实体数组为空，因为此应用当前没有任何实体。 

    JSON 结果中标识了评分最高的意向作为 **`topScoringIntent`** 属性。 所有评分介于 1 和 0 之间，评分越接近 1 越好。 

## <a name="applyforjob-intent"></a>ApplyForJob 意向
返回到 LUIS 网站并创建一个新的意向，以便确定用户话语是否与申请工作有关。

1. 在右上侧菜单中选择“生成”，返回到应用生成界面。

2. 在左侧菜单中选择“意向”。

3. 选择“创建新意向”，并输入名称 `ApplyForJob`。 

    ![用于创建新意向的 LUIS 对话框](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. 将多个陈述添加到用户预期会请求的此意向，例如：

    | 示例陈述|
    |--|
    |I want to apply for the new accounting job|
    |Fill out application for Job 123456|
    |Submit resume for engineering position|
    |Here is my c.v. for position 654234|
    |Job 567890 and my paperwork|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "为 ApplyForJob 意向输入新陈述的屏幕截图")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    带标签的意向框在红色框中，因为 LUIS 目前并不确定该意向是否正确。 训练应用可让 LUIS 知道这些陈述位于正确的意向中。 

## <a name="train-again"></a>再次训练

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>再次发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>再次获取意向

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 在新浏览器窗口中，在 URL 的末尾输入 `Can I submit my resume for job 235986`。 

    ```JSON
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

    结果包含新意向 **ApplyForJob** 和现有意向。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程创建了人力资源 (HR) 应用、创建了 2 个意向、向每个意向添加了示例话语、向 None 意向添加了示例话语，并在终结点上进行了训练、发布和测试操作。 这些是生成 LUIS 模型的基本步骤。 

> [!div class="nextstepaction"]
> [将预生成的意向和实体添加到此应用](luis-tutorial-prebuilt-intents-entities.md)
