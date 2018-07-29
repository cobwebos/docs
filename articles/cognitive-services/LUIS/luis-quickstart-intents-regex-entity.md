---
title: 教程：创建 LUIS 应用以获取正则表达式匹配的数据 - Azure | Microsoft Docs
description: 本教程介绍如何使用意向和正则表达式实体创建一个简单的 LUIS 应用，以提取数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 99f796bf26df755ca938c3023057e2e9de1706a1
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238329"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>教程：3. 添加正则表达式实体
在本教程中，我们将创建一个应用，用于演示如何使用**正则表达式**实体从陈述中提取带有一致格式的数据。


<!-- green checkmark -->
> [!div class="checklist"]
> * 了解正则表达式实体 
> * 使用具有 FindForm 意向的适用于人力资源 (HR) 领域的 LUIS 应用
> * 添加正则表达式实体以从陈述中提取表单编号
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

本文需要一个免费的 [LUIS](luis-reference-regions.md#luis-website) 帐户，以便创作 LUIS 应用程序。

## <a name="before-you-begin"></a>开始之前
如果尚未获得[预生成实体](luis-tutorial-prebuilt-intents-entities.md)教程中所述的人力资源应用，请将 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json) Github 存储库中的 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `regex`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 


## <a name="purpose-of-the-regular-expression-entity"></a>正则表达式实体的用途
实体的用途是获取陈述中包含的重要数据。 应用使用正则表达式实体从陈述中提取带有格式的人力资源 (HR) 表单编号。 此数据不是机器学习的数据。 

简单的陈述示例包括：

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

陈述的缩写或俚语版本包括：

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
要与表单编号匹配的正则表达式实体是 `hrf-[0-9]{6}`。 此正则表达式匹配文本字符 `hrf -`，但忽略大小写和区域性变体。 它完全匹配数字 0-9 中的 6 个数字。

HRF 代表“人力资源表单”。

### <a name="tokenization-with-hyphens"></a>包含连字符的词汇切分
将陈述添加到意向后，LUIS 会对陈述执行词汇切分。 这些陈述的词汇切分会在连字符的前面和后面添加空格 (`Where is HRF - 123456?`)。在对陈述执行词汇切分之前，正则表达式将应用到原始格式的陈述。 由于正则表达式应用到原始格式，因此，它不需要处理单词边界。 


## <a name="add-findform-intent"></a>添加 FindForm 意向

1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

    [![LUIS 应用的屏幕截图，其中已突出显示右上方的导航栏](./media/luis-quickstart-intents-regex-entity/first-image.png)](./media/luis-quickstart-intents-regex-entity/first-image.png#lightbox)

2. 选择“创建新意向”。 

    [![“意向”页的屏幕截图，其中已突出显示“创建新意向”按钮](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png)](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png#lightbox)

3. 在弹出对话框中输入 `FindForm`，然后选择“完成”。 

    ![“创建新意向”对话框的屏幕截图，其中的搜索框中包含“实用工具”](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. 将示例陈述添加到意向。

    |示例陈述|
    |--|
    |What is the URL for hrf-123456?|
    |Where is hrf-345678?|
    |When was hrf-456098 updated?|
    |Did John Smith update hrf-234639 last week?|
    |How many version of hrf-345123 are there?|
    |Who needs to authorize form hrf-123456?|
    |How many people need to sign off on hrf-345678?|
    |hrf-234123 date?|
    |author of hrf-546234?|
    |title of hrf-456234?|

    [![“意向”页的屏幕截图，其中已突出显示新陈述](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    应用程序包含从前一教程添加的预生成数字实体，因此，已标记每个表单编号。 此信息可能对客户端应用程序而言已足够，但不会使用数字类型来标记编号。 使用适当的名称创建新实体可以在从 LUIS 返回实体后，让客户端应用程序适当地处理该实体。

## <a name="create-a-hrf-number-regular-expression-entity"></a>创建 HRF 编号正则表达式实体 
遵循以下步骤创建一个正则表达式实体，让 LUIS 知道 HRF 编号格式是什么：

1. 在左侧面板中选择“实体”。

2. 在“实体”页上选择“创建新实体”按钮。 

    [![“实体”页的屏幕截图，其中已突出显示“创建新实体”按钮](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png)](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png#lightbox)

3. 在弹出对话框中，输入新实体名称 `HRF-number`，选择“正则表达式”作为实体类型，输入 `hrf-[0-9]{6}` 作为正则表达式，然后选择“完成”。

    ![用于设置新实体属性的弹出对话框屏幕截图](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. 选择“意向”，然后选择“FindForm”意向查看陈述中标记的正则表达式。 

    [![“标记包含现有实体和正则表达式模式的陈述”的屏幕截图](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    因为该实体不是机器学习的实体，因此，在创建标签后，该标签将立即应用到陈述并显示在 LUIS 网站中。

## <a name="train-the-luis-app"></a>训练 LUIS 应用
正则表达式实体不需要训练，但新意向和陈述需要训练。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![训练按钮的图像](./media/luis-quickstart-intents-regex-entity/train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![成功通知栏的图像](./media/luis-quickstart-intents-regex-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

    ![FindKnowledgeBase 的屏幕截图，其中已突出显示顶部导航栏中的“发布”按钮](./media/luis-quickstart-intents-regex-entity/publish-button.png)

2. 选择“生产”槽和“发布”按钮。

    ![“发布”页的屏幕截图，其中已突出显示“发布到生产槽”按钮](./media/luis-quickstart-intents-regex-entity/publish-to-production.png)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的话语查询终结点
1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    ![“发布”页的屏幕截图，其中已突出显示终结点 URL](./media/luis-quickstart-intents-regex-entity/publish-select-endpoint.png)

2. 将光标定位到地址中 URL 的末尾，并输入 `When were HRF-123456 and hrf-234567 published in the last year?`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含两个表单编号（`HRF-123456` 和 `hrf-234567`）的 `FindForm` 意向。

    ```
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    陈述中的编号返回两次，一次作为新实体 `hrf-number` 返回，一次作为预生成实体 `number` 返回。 一个陈述可以包含多个实体，以及多个相同类型的实体，如此示例中所示。 LUIS 将使用正则表达式实体提取命名数据，从编程角度讲，这对于接收 JSON 响应的客户端应用程序而言会更有帮助。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用识别了意向，并返回了提取的数据。 

现在，聊天机器人已获得足够的信息，可以确定确定主要操作 `FindForm`，以及搜索中的表单编号。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和表单编号，并可以搜索第三方 API。 LUIS 不执行此类工作。 LUIS 只会确定用户的意向，并提取有关该意向的数据。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 在左上侧菜单中选择“我的应用”。 在应用列表中选择应用名称右侧的省略号 (***...***)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解列表实体](luis-quickstart-intent-and-list-entity.md)

