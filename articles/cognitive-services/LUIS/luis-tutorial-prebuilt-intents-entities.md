---
title: 预生成意向和实体
titleSuffix: Azure Cognitive Services
description: 在本教程中，我们将预生成的意向和实体添加到某个应用，以便快速了解意向预测和数据提取。 不需要使用预生成的实体标记任何话语。 自动检测实体。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 804224898f90aa9af587d6d5b4b80c6afcfa586d
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754979"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>教程：确定常见意向和实体

在本教程中，我们将预生成的意向和实体添加到人力资源教程应用，以便快速了解意向预测和数据提取。 不需要使用预生成的实体标记任何话语，因为会自动检测实体。

预生成的模型（域、意向和实体）可帮助你快速生成模型。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建新应用
> * 添加预生成意向 
> * 添加预生成的实体 
> * 定型 
> * 发布 
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>创建新应用

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>添加预生成的意向以帮助获取常见用户意向

LUIS 提供几个预生成意向，以便提供常见用户意向。  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 选择“添加预生成域意向”。 

1. 搜索 `Utilities`。 

    [![“预生成意向”对话框的屏幕截图，其中的搜索框中包含“实用工具”](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. 选择以下意向，然后选择“完成”： 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop

    这些意向有助于确定用户在聊天中所处的位置及其需求。 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>添加预生成的实体以帮助提取常见数据类型

LUIS 为常见数据提取提供多个预生成的实体。 

1. 在左侧导航菜单中选择“实体”。

1. 选择“添加预生成的实体”按钮。

1. 从预生成的实体列表中选择以下实体，然后选择“完成”：

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![在“预生成的实体”对话框中选择的数字的屏幕截图](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

    这些实体可帮助你在客户端应用程序中添加名称和位置识别。

## <a name="add-example-utterances-to-the-none-intent"></a>将话语示例添加到 None 意向 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>训练应用，以便可以测试对意向所做的更改 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>发布应用，以便可以从终结点查询已训练的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>从终结点获取意向和实体预测结果

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 将光标定位到浏览器地址栏中 URL 的末尾，并输入 `I want to cancel my trip to Seattle to see Bob Smith`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    结果中预测了置信度为 80% 的 Utilities.Cancel 意向，并提取了城市和人名数据。 


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相关信息

详细了解预生成的模型：

* [预生成的域](luis-reference-prebuilt-domains.md)：这是一些常见的域，可以减少 LUIS 应用创作的总体工作量
* 预生成的意向：这是常见域的各个意向。 可以单独添加意向，而无需添加整个域。
* [预生成的实体](luis-prebuilt-entities.md)：这是可用于大多数 LUIS 应用的常见数据类型。

详细了解如何使用 LUIS 应用：

* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)

## <a name="next-steps"></a>后续步骤

通过添加预生成的意向和实体，客户端应用程序可以确定常见的用户意向并提取常见的数据类型。  

> [!div class="nextstepaction"]
> [向应用添加正则表达式实体](luis-quickstart-intents-regex-entity.md)

