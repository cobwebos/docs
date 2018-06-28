---
title: 教程：了解如何使用 JavaScript 将陈述添加到 LUIS 应用 | Microsoft Docs
description: 本教程介绍如何使用 JavaScript 调用 LUIS 应用。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265453"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>教程：使用 JavaScript 将陈述添加到应用
在本教程中，我们将编写一个程序，以使用 JavaScript 中的创作 API 将陈述添加到意向。

<!-- green checkmark -->
> [!div class="checklist"]
> * 创建 Visual Studio 控制台项目 
> * 添加方法以调用 LUIS API 来添加陈述和训练应用
> * 添加包含 BookFlight 意向的示例陈述的 JSON 文件
> * 运行控制台并查看陈述的训练状态

有关详细信息，请参阅[将示例陈述添加到意向](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)、[训练](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)和[训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API 的技术文档。

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="prerequisites"></a>先决条件
* LUIS [**创作密钥**](luis-concept-keys.md#authoring-key)。 
* 现有的 LUIS **应用程序 ID** 和**版本 ID**。 
* VSCode 中名为 `add-utterances.html` 的新文件项目。

> [!NOTE] 
> [**LUIS-Samples** Github 存储库](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html)中提供了完整的 `add-utterances.html` 文件。


## <a name="write-the-code"></a>编写代码
创建 `add-utterances.html` 并添加以下代码：

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>在浏览器中查看
1. 在浏览器中打开该文件。

2. 添加 LUIS 创作 ID、LUIS 应用程序 ID，并且更改版本（如果不是 `0.1`）

3. 修改要添加到应用程序的**陈述数组**。 该数组存储在 utteranceJSON 变量中。 根据自己在域和陈述方面的需要，更改这些值。 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. 选择 `Upload utterance` 按钮。 LUIS 结果显示在按钮下面。

5. 选择 `Train model` 按钮，使用这些新陈述训练应用程序。

6. 选择 `Train Status` 按钮查看训练状态。 

![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>清理资源
完成本教程后，如果不再需要 Visual Studio 和控制台应用程序，请将其删除。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [将 LUIS 与机器人集成](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website