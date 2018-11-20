---
title: 教程 2：预生成的意向和实体 - 使用预生成的常用话语 - 在 LUIS 中提取常用数据
titleSuffix: Azure Cognitive Services
description: 将预生成的意向和实体添加到人力资源教程应用，以便快速了解意向预测和数据提取。 不需要使用预生成的实体标记任何话语。 自动检测实体。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d73284ecf40b2dfcd4954c1ddee82bf92a8931b9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281789"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>教程 2：确定常见意向和实体
在本教程中，修改“人力资源”应用。 将预生成的意向和实体添加到人力资源教程应用，以便快速了解意向预测和数据提取。 不需要使用预生成的实体标记任何话语，因为会自动检测实体。

常见主题域和数据类型的预生成模型可帮助你快速生成模型，并提供模型外观的示例。 

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 使用现有的教程应用
> * 添加预生成意向 
> * 添加预生成的实体 
> * 定型 
> * 发布 
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用
继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `prebuilts`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。 

## <a name="add-prebuilt-intents"></a>添加预生成意向
LUIS 提供几个预生成意向，以便提供常见用户意向。  

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 选择“添加预生成的意向”。 

3. 搜索 `Utilities`。 

    [![“预生成意向”对话框的屏幕截图，其中的搜索框中包含“实用工具”](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 选择以下意向，然后选择“完成”： 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>添加预生成的实体
LUIS 为常见数据提取提供多个预生成的实体。 

1. 在左侧导航菜单中选择“实体”。

2. 选择“管理预生成的实体”按钮。

3. 从预生成的实体的列表中选择“数字”和“datetimeV2”，然后选择“完成”。

    ![在“预生成的实体”对话框中选择的数字的屏幕截图](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到浏览器地址栏中 URL 的末尾，并输入 `I want to cancel on March 3`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 

    ```JSON
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    结果预测了 Utilities.Cancel 意向并提取了 3 月 3 日的日期和数字 3。 

    “3 月 3 日”有两个值，因为表述没有指出“3 月 3 日”是过去的还是未来的。 将由客户端应用程序负责决定是做一个假设，还是要求澄清（如果需要）。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

通过添加预生成的意向和实体，客户端应用程序可以确定常见的用户意向并提取常见的数据类型。 

> [!div class="nextstepaction"]
> [向应用添加正则表达式实体](luis-quickstart-intents-regex-entity.md)

