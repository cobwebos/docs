---
title: 教程 3：正则表达式匹配的数据 - 提取格式良好的数据
titleSuffix: Azure Cognitive Services
description: 使用正则表达式实体从话语中提取带有一致格式的数据。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 06e212ef756fda9224b38b41c69c7c4eccfb9796
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159850"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>教程 3：提取格式良好的数据
在本教程中，我们将修改人力资源应用，以使用“正则表达式”实体从话语中提取带有一致格式的数据。

该实体的用途是提取话语中包含的重要数据。 此应用使用正则表达式实体的目的是从话语中提取带有格式的人力资源 (HR) 表单编号。 尽管话语的意向始终是使用机器学习确定的，但此特定实体类型不是机器学习的内容。 

**示例话语包括：**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
在以下情况下，正则表达式非常适合此类数据：

* 数据有良好的格式。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用现有的教程应用
> * 添加 FindForm 意向
> * 添加正则表达式实体 
> * 训练
> * 发布
> * 从终结点获取意向和实体

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用
继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1. 下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `regex`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。 

## <a name="findform-intent"></a>FindForm 意向

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 选择“创建新意向”。 

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

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>正则表达式实体 
要与表单编号匹配的正则表达式实体是 `hrf-[0-9]{6}`。 此正则表达式匹配文本字符 `hrf-`，但忽略大小写和区域性变体。 它完全匹配数字 0-9 中的 6 个数字。

HRF 代表 `human resources form`。

在话语添加到意向时，LUIS 会对话语执行词汇切分。 这些陈述的词汇切分会在连字符的前面和后面添加空格 (`Where is HRF - 123456?`)。在对陈述执行词汇切分之前，正则表达式将应用到原始格式的陈述。 由于正则表达式应用到原始格式，因此，它不需要处理单词边界。 

遵循以下步骤创建一个正则表达式实体，让 LUIS 知道 HRF 编号格式是什么：

1. 在左侧面板中选择“实体”。

2. 在“实体”页上选择“创建新实体”按钮。 

3. 在弹出对话框中，输入新实体名称 `HRF-number`，选择“正则表达式”作为实体类型，输入 `hrf-[0-9]{6}` 作为“正则表达式”值，然后选择“完成”。

    ![用于设置新实体属性的弹出对话框屏幕截图](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. 从左侧菜单中选择“意向”，然后选择“FindForm”意向查看话语中标记的正则表达式。 

    [![“标记包含现有实体和正则表达式模式的陈述”的屏幕截图](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    因为该实体不是机器学习的实体，因此，在创建标签后，该标签将立即应用到陈述并显示在 LUIS 网站中。

## <a name="train"></a>训练

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `When were HRF-123456 and hrf-234567 published in the last year?`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含两个表单编号（`HRF-123456` 和 `hrf-234567`）的 `FindForm` 意向。

    ```JSON
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


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤
本教程创建了新意向，添加了示例话语，然后创建了正则表达式实体以便从话语中提取格式良好的数据。 在训练并发布应用后，向终结点发出的查询识别了意向，并返回了提取的数据。

> [!div class="nextstepaction"]
> [了解列表实体](luis-quickstart-intent-and-list-entity.md)

