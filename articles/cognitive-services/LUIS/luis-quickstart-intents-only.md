---
title: 使用两个意向创建一个简单的应用 - Azure | Microsoft Docs
description: 本教程介绍如何使用两个意向但不使用任何实体来创建一个简单的 LUIS 应用，以识别用户陈述。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: de295a93d395cee4c4dfbea4f2e7f7338036feb8
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494367"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>教程：1. 使用自定义域生成应用
在本教程中，我们将创建一个应用，用于演示如何使用**意向**，根据用户提交到应用的陈述（文本），来确定该用户的_意向_。 完成本教程后，会在云中运行一个 LUIS 终结点。

此应用是最简单类型的 LUIS 应用，因为它不会从陈述中提取数据。 它只确定用户的陈述意向。

<!-- green checkmark -->
> [!div class="checklist"]
> * 创建适用于人力资源 (HR) 领域的新应用 
> * 添加 GetJobInformation 意向
> * 将示例陈述添加到 GetJobInformation 意向 
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应
> * 添加 ApplyForJob 意向
> * 将示例陈述添加到 ApplyForJob 意向 
> * 再次训练、发布并查询终结点 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="purpose-of-the-app"></a>应用的用途
此应用具有多个意向。 第一个意向 **`GetJobInformation`** 识别用户何时需要公司内部提供的作业的信息。 第二个意向 **`None`** 识别其他每种类型的陈述。 本快速入门稍后会添加第三个意向 `ApplyForJob`。 

## <a name="create-a-new-app"></a>创建新应用
1. 登录到 [LUIS](luis-reference-regions.md#luis-website) 网站。 确保登录到需要发布 LUIS 终结点的[区域](luis-reference-regions.md#publishing-regions)。

2. 在 [LUIS](luis-reference-regions.md#luis-website) 网站上，选择“创建新应用”。  

    [![](media/luis-quickstart-intents-only/app-list.png "“我的应用”页的屏幕截图")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. 在弹出的对话框中，输入名称 `HumanResources`。 此应用涵盖了有关公司人力资源部门的问题。 此类部门处理招聘相关的问题，例如公司的空缺职位。

    ![LUIS 新应用](./media/luis-quickstart-intents-only/create-app.png)

4. 过程完成后，应用会显示具有 **None** 意向的“意向”页。 

## <a name="create-getjobinformation-intention"></a>创建 GetJobInformation 意向
1. 选择“创建新意向”。 输入新意向名称 `GetJobInformation`。 每当某个用户想要获取有关贵公司的空缺职位的信息时，就会预测此意向。

    ![](media/luis-quickstart-intents-only/create-intent.png "“新建意向”对话框的屏幕截图")

    创建意向会创建想要识别的信息类别。 为类别命名可让使用 LUIS 查询结果的其他任何应用程序使用该类别名称来查找相应的答案。 LUIS 不会回答这些问题，而只会识别以自然语言请求的信息类型。 

2. 将 7 个陈述添加到用户预期会请求此意向，例如：

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

3. LUIS 应用当前没有 **None** 意向的陈述。 它需要应用不会回答的陈述。 请不要将此项留空。 在左侧面板中选择“意向”。 

4. 选择“None”意向。 添加用户可能会输入的、但与应用无关的 3 个陈述。 如果应用与作业发布内容有关，则下面是一些合理的 **None** 陈述：

    | 示例陈述|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|

    在 LUIS 调用方应用程序（例如聊天机器人）中，如果 LUIS 返回陈述的 **None** 意向，机器人可以询问用户是否要结束对话。 如果用户不想要结束对话，聊天机器人还能提供有关继续对话的更多指示。 

## <a name="train-and-publish-the-app"></a>训练并发布应用

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>将应用发布到终结点

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="query-endpoint-for-getjobinformation-intent"></a>查询 GetJobInformation 意向的终结点

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `I'm looking for a job with Natual Language Processing`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于步骤 4 中的任何示例陈述，因此，它非常适合用于测试，测试结果应返回 `GetJobInformation` 意向（评分最高的意向）。 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
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

## <a name="create-applyforjob-intention"></a>创建 ApplyForJob 意向
返回到 LUIS 网站的浏览器选项卡，并创建要应用到作业的新意向。

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

    再次[训练并发布](#train-and-publish-the-app)。 

## <a name="query-endpoint-for-applyforjob-intent"></a>查询 ApplyForJob 意向的终结点

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 在新浏览器窗口中，在 URL 的末尾输入 `Can I submit my resume for job 235986`。 

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用只包含几个意向，它识别了一个意向相同、但措辞不同的自然语言查询。 

JSON 结果中标识了评分最高的意向。 所有评分介于 1 和 0 之间，评分越接近 1 越好。 `GetJobInformation` 和 `None` 意向的评分非常接近于零。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果，并查找用于回答问题的信息（未存储在 LUIS 中）或结束对话。 这是机器人或调用方应用程序的编程选项。 LUIS 不执行此类工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将预生成的意向和实体添加到此应用](luis-tutorial-prebuilt-intents-entities.md)
