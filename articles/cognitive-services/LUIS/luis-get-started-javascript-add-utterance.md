---
title: 快速入门：了解如何使用 JavaScript 将话语添加到 LUIS 应用 - Azure 认知服务 | Microsoft Docs
description: 本快速入门介绍如何使用 JavaScript 调用 LUIS 应用。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: ffc19d12c1d3fbb24c514ac87f298d1a52d23eb8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43768816"
---
# <a name="quickstart-change-model-using-javascript"></a>快速入门：使用 JavaScript 更改模型

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>先决条件

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/)。

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>创建快速入门代码

创建 `add-utterances.html` 并添加以下代码：

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>运行代码

1. 在浏览器中打开该文件。

2. 添加 LUIS 创作 ID、LUIS 应用程序 ID。

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
完成此快速入门后，删除此快速入门中创建的所有文件。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [将 LUIS 与机器人集成](luis-csharp-tutorial-build-bot-framework-sample.md)
