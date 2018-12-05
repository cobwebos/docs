---
title: 教程 8：LUIS 中的关键短语提取
titleSuffix: Azure Cognitive Services
description: 使用 keyPhrase 预生成实体从话语中提取关键主题。 不需要使用预生成的实体标记任何话语。 自动检测实体。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: fafb6a6cb986b5bcd795c9bac0fadfce2827c667
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424806"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>教程 8：提取话语的关键短语
在本教程中，使用预生成的 keyPhrase 实体从话语中提取关键主题。 不需要使用预生成的实体标记任何话语。 自动检测实体。

以下陈述显示了关键短语的示例：

|陈述|keyPhrase 实体值|
|--|--|
|是否有具有较低扣除提供下一年的新医疗计划？是否有具有较低扣除提供下一年的新医疗计划？|"低扣除"<br>"新的医疗计划"<br>“年龄”|
|视觉治疗中介绍了高可扣除医疗计划？|"高可扣除医疗计划"<br>"视觉治疗"|

客户端应用程序可以使用这些值以及其他已提取的实体来决定对话中的下一步骤。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用现有的教程应用
> * 添加 keyPhrase 实体 
> * 定型
> * 发布
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用

继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `keyphrase`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="add-keyphrase-entity"></a>添加 keyPhrase 实体 
添加 keyPhrase 预生成实体，以从陈述中提取主题。

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 在左侧菜单中选择“实体”。

3. 选择“添加预生成的实体”。

4. 在弹出对话框中选择“keyPhrase”，然后选择“完成”。 

    [![“实体列表”弹出对话框的屏幕截图](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. 从左侧菜单中选择“意向”，然后选择 **Utilities.Confirm** 意向。 keyPhrase 实体在多个陈述中进行了标记。 

    [ ![Utilities.Confirm 意向的屏幕截图，其中在陈述中标记出了 keyPhrase](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `does form hrf-123456 cover the new dental benefits and medical plan`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    搜索窗体时，用户提供的信息比找到窗体所需的信息要多。 额外的信息将作为 **builtin.keyPhrase** 返回。 客户端应用程序可以将该额外的信息用于后续问题，例如“是否要跟人力资源代表谈谈新的牙科福利”，或者提供具有更多选项的菜单，这些选项包括“有关新的牙科福利或医疗计划的详细信息”。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程添加了预生成的 keyPhrase 实体，可以快速提供话语中的关键短语，而无需标记话语。 

> [!div class="nextstepaction"]
> [将情绪分析添加到应用](luis-quickstart-intent-and-sentiment-analysis.md)
