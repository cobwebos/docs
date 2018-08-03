---
title: 使用模式改进 LUIS 预测的教程 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 本教程介绍如何使用意向模式来改进 LUIS 意向和实体预测。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238448"
---
# <a name="tutorial-improve-app-with-patterns"></a>教程：使用模式来改进应用

本教程介绍如何使用模式来改进意向和实体预测。  

> [!div class="checklist"]
* 如何识别模式是否对应用有帮助
* 如何创建模式 
* 如何验证模式预测改进

本文需要一个免费的 [LUIS](luis-reference-regions.md) 帐户，以便创作 LUIS 应用程序。

## <a name="before-you-begin"></a>开始之前
如果尚未获得 [批处理测试](luis-tutorial-batch-testing.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) GitHub 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `patterns`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>模式通过较少实例对 LUIS 进行常见陈述的训练
由于人力资源域的性质，可通过几种常见方式询问组织中的员工关系。 例如：

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

在未提供许多陈述示例的情况下，这些陈述过于相近而无法确定每个陈述的上下文唯一性。 通过为意向添加模式，LUIS 可了解意向的常见陈述模式，而无需提供许多陈述示例。 

此意向的示例模板陈述包括：

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

该模式通过模板话语示例提供，该示例包括用于标识实体和可忽略文本的语法。 模式是正则表达式匹配和机器学习的组合。  模板话语示例和意向话语使 LUIS 能够更好地理解哪些话语符合意向。

为了使模式与话语匹配，话语内的实体必须首先匹配模板话语中的实体。 但是，模板不会帮助预测实体，仅预测意向。 

尽管模式允许提供较少的示例话语，但如果未检测到实体，则该模式将不匹配。

请记住，员工是在[列表实体教程](luis-quickstart-intent-and-list-entity.md)中创建的。

## <a name="create-new-intents-and-their-utterances"></a>创建新意向及其话语
添加两个新的意向：`OrgChart-Manager` 和 `OrgChart-Reports`。 LUIS 向客户端应用返回预测后，意向名称可以用作客户端应用中的函数名称，并且“员工”实体可以用作该函数的参数。

```
OrgChart-Manager(employee){
    ///
}
```

1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

2. 在“意向”页上，选择“创建新意向”。 

3. 在弹出对话框中输入 `OrgChart-Manager`，然后选择“完成”。

    ![创建新消息弹出窗口](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. 将示例陈述添加到意向。

    |示例陈述|
    |--|
    |John W. Smith 是谁的下属？|
    |John W. Smith 向谁报告？|
    |John W. Smith 的经理是谁？|
    |Jill Jones 直接向谁报告？|
    |Jill Jones 主管是谁？|

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "LUIS 的屏幕截图，将新的话语添加到意向")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    如果 keyPhrase 实体在意向的话语中而不是员工实体中标记，请不要担心。 两者都已在“测试”窗格和终结点中正确预测。 

5. 在左侧导航栏中选择“意向”。

6. 选择“创建新意向”。 

7. 在弹出对话框中输入 `OrgChart-Reports`，然后选择“完成”。

8. 将示例陈述添加到意向。

    |示例陈述|
    |--|
    |John W. Smith 的下属是谁？|
    |谁向 John W. Smith 报告？|
    |John W. Smith 管理谁？|
    |谁直接向 Jill Jones 报告？|
    |Jill Jones 监督谁？|

## <a name="caution-about-example-utterance-quantity"></a>有关示例话语数量的警告
这些意向中的示例话语数量不足以正确定型 LUIS。 在实际应用中，每个意向应至少有 15 个话语，具有不同的单词选择和话语长度。 这几个话语是专门为突出显示模式而选择的。 

## <a name="train-the-luis-app"></a>训练 LUIS 应用
新意向和话语需要定型。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![训练按钮的图像](./media/luis-tutorial-pattern/hr-train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![成功通知栏的图像](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

2. 选择“生产”槽和“发布”按钮。

    [![“发布”页的屏幕截图，其中已突出显示“发布到生产槽”按钮](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的话语查询终结点
1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    [![“发布”页的屏幕截图，其中已突出显示终结点 URL](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


2. 将光标定位到地址中 URL 的末尾，并输入 `Who is the boss of Jill Jones?`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

此查询是否成功？ 在此定型周期中，它确实成功了。 两个顶级意向的分数很接近。 因为 LUIS 定型每次都不完全相同，有一些变化，所以这两个分数可能会在下一个训练周期中反转。 结果导致可能会返回的错误意向。 

使用模式使正确意向的分数在百分比上显着提高，并且距离下一个最高分数更远。 

## <a name="add-the-template-utterances"></a>添加模板陈述

1. 选择顶部菜单中的“生成”。

2. 在左侧导航栏中，在“提高应用性能”下，从左侧导航栏中选择“模式”。

3. 选择“OrgChart-Manager”意向，然后一次输入一个以下模板话语，在输入每个模板话语后选择 Enter：

    |模板话语|
    |:--|
    |{Employee} 是谁的下属[?]|
    |{Employee} 向谁报告[?]|
    |{Employee} 的经理是谁[?]|
    |{Employee} 直接向谁报告[?]|
    |{Employee} 的主管是谁[?]|
    |{Employee} 的老板是谁[?]|

    `{Employee}` 语法可标记模板陈述中的实体位置以及它是哪个实体。 
    
    具有角色的实体使用包含角色名称的语法，并在[单独的角色教程](luis-tutorial-pattern-roles.md)中介绍。 

    可选的语法 `[]` 标记可选的单词或标点。 LUIS 匹配话语，忽略括号内的可选文本。

    如果键入模板话语，LUIS 会帮助你在输入左大括号 `{` 时填充实体。

    [![为意向输入模板话语的屏幕截图](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



4. 选择“OrgChart-Reports”意向，然后一次输入一个以下模板话语，在输入每个模板话语后选择 Enter：

    |模板话语|
    |:--|
    |{Employee} 的下属是谁[?]|
    |谁向 {Employee} 报告[?]|
    |{Employee} 管理谁[?]|
    |谁直接向 {Employee} 报告[?]|
    |{Employee} 监督谁[?]|
    |{Employee} 是谁的老板[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>使用模式时查询终结点

1. 再次定型并发布应用。

2. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

3. 将光标定位到地址中 URL 的末尾，并输入 `Who is the boss of Jill Jones?` 作为话语。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

意向预测现在明显更高。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，在应用列表中选择应用名称右侧的省略号 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何结合使用角色和模式](luis-tutorial-pattern-roles.md)