---
title: 模式
titleSuffix: Azure Cognitive Services
description: 使用模式来改进意向和实体预测，同时提供更少的示例话语。 该模式通过模板话语示例提供，该示例包括用于标识实体和可忽略文本的语法。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: e559d4a3366c45bd054cbf3a235805e048de3493
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276049"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>教程：添加常见的模式模板话语格式

在本教程中，使用模式来改进意向和实体预测，同时提供更少的示例话语。 该模式通过模板话语示例提供，该示例包括用于标识实体和可忽略文本的语法。 模式是表达式匹配和机器学习的组合。  模板话语示例和意向话语使 LUIS 能够更好地理解哪些话语符合意向。 

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 导入示例应用 
> * 创建意向
> * 定型
> * 发布
> * 从终结点获取意向和实体
> * 创建模式
> * 验证模式预测改进
> * 将文本标记为可忽略以及在模式中进行嵌套
> * 使用测试面板验证模式成功

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>导入示例应用

继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”  部分的“版本”  选项卡上，克隆版本并将其命名为 `patterns`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="create-new-intents-and-their-utterances"></a>创建新意向及其话语

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

    [![LUIS 向意向中添加新话语屏幕截图](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "LUIS 向意向中添加新话语屏幕截图")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    如果 keyPhrase 实体在意向的话语中而不是员工实体中标记，请不要担心。 两者都已在“测试”窗格和终结点中正确预测。 

5. 在左侧导航栏中选择“意向”。 

6. 选择“创建新意向”  。 

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

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Who is the boss of Jill Jones?`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```json
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

使此第二个浏览器窗口保持打开。 本教程稍后将使用该窗口。 

## <a name="template-utterances"></a>模板话语
由于人力资源域的性质，可通过几种常见方式询问组织中的员工关系。 例如：

|陈述|
|--|
|Jill Jones 向谁报告？|
|谁向 Jill Jones 报告？|

在未提供许多陈述示例的情况下，这些陈述过于相近而无法确定每个陈述的上下文唯一性。 通过为意向添加模式，LUIS 可了解意向的常见陈述模式，而无需提供许多陈述示例。 

此意向的模板陈述示例包括：

|模板陈述示例|语法含义|
|--|--|
|{Employee} 向谁报告[?]|可互换的 {Employee}，是否忽略[?]}|
|谁向 {Employee} 报告[?]|可互换的 {Employee}，是否忽略[?]}|

`{Employee}` 语法可标记模板陈述中的实体位置以及它是哪个实体。 可选的语法 `[?]` 标记可选的单词或标点。 LUIS 匹配话语，忽略括号内的可选文本。

虽然语法类似于正则表达式，它不是正则表达式。 仅支持大括号 `{}` 和方括号 `[]` 语法。 它们最多可以嵌套两层。

为了使模式与话语匹配，话语内的实体必须首先匹配模板话语中的实体。 但是，模板不会帮助预测实体，仅预测意向。 

**尽管模式允许提供较少的示例话语，但如果未检测到实体，则该模式将不匹配。**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>添加 OrgChart-Manager 意向的模式

1. 选择顶部菜单中的“生成”  。

2. 在左侧导航栏中，在“提高应用性能”下，从左侧导航栏中选择“模式”   。

3. 选择 **OrgChart-Manager** 意向，然后输入以下模板话语：

    |模板话语|
    |:--|
    |{Employee} 是谁的下属[?]|
    |{Employee} 向谁报告[?]|
    |{Employee} 的经理是谁[?]|
    |{Employee} 直接向谁报告[?]|
    |{Employee} 的主管是谁[?]|
    |{Employee} 的老板是谁[?]|

    具有角色的实体使用包含角色名称的语法，并在[单独的角色教程](luis-tutorial-pattern-roles.md)中介绍。 

    如果键入模板话语，LUIS 会帮助你在输入左大括号 `{` 时填充实体。

    [![为意向输入模板话语的屏幕截图](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. 在“模式”页上选择“OrgChart-Reports”意向，然后输入以下模板言语： 

    |模板话语|
    |:--|
    |{Employee} 的下属是谁[?]|
    |谁向 {Employee} 报告[?]|
    |{Employee} 管理谁[?]|
    |谁直接向 {Employee} 报告[?]|
    |{Employee} 监督谁[?]|
    |{Employee} 是谁的老板[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>使用模式时查询终结点

将模式添加到应用后，在预测运行时终结点上训练、发布和查询应用。

1. 再次定型并发布应用。

1. 将浏览器选项卡切换回终结点 URL 选项卡。

1. 将光标定位到地址中 URL 的末尾，并输入 `Who is the boss of Jill Jones?` 作为话语。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```json
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

现在，意向预测的置信度已明显提高。

## <a name="working-with-optional-text-and-prebuilt-entities"></a>使用可选文本和预构建的实体

本教程中前面的模式模板话语包含几个可选文本示例，例如字母 s 的所有格 `'s` 的使用以及问号 `?` 的使用。 假设需要在言语文本中允许当前和未来日期。

示例话语如下：

|意向|包含可选文本和预构建的实体的示例话语|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

这些示例每个都使用 LUIS 需要正确预测的动词时态 `was`、`is`、`will be` 以及日期 `March 3`、`now` 和 `in a month`。 注意，除了 `in` 和 `on` 之外，最后两个示例使用了几乎相同的文本。

允许此可选信息的示例模板言语： 

|意向|包含可选文本和预构建的实体的示例话语|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


可选语法方括号 `[]` 的使用使此可选文本易于添加到模板话语中，并且可以嵌套到另一个级别 `[[]]` 并包括实体或文本。


**问：为何所有 `w` 字母（每句模板话语中的第一个字母）都是小写？它们不应当任选使用大写或小写吗？** 由客户端应用程序提交到查询终结点的话语将转换为小写。 模板话语可以为大写或小写，终结点话语也可以任意使用大写或小写。 比较始终在转换为小写后进行。

**问：如果 March 3 被同时预测为数字 `3` 和日期 `March 3`，预构建的数字为何不是模板话语的一部分？** 模板话语从上下文来看使用的是日期，无论是逐字表示为 `March 3` 还是抽象为 `in a month`. 日期可以包含数字，但数字不一定会被视为日期。 请始终使用能够最好地表示你要在预测 JSON 结果中返回的类型的实体。  

**问：如果使用了措辞不当的话语（例如 `Who will {Employee}['s] manager be on March 3?`），将会怎样？** 像这种在语法上不同的动词时态（其中 `will` 和 `be` 是独立的）需要作为一句新的模板话语。 现有模板话语将不会匹配它。 虽然话语的意向未更改，但是话语中的单词位置已更改。 此更改会影响 LUIS 中的预测。 可以使用 [group 和 or 运算符](#use-the-or-operator-and-groups)来组合谓语时态，以合并这些言语。 

**请记住：将首先查找实体，然后再匹配模式。**

## <a name="edit-the-existing-pattern-template-utterance"></a>编辑现有模式模板话语

1. 在 LUIS 网站上，在顶部的菜单中选择“生成”  ，然后在左侧菜单中选择“模式”。  

1. 搜索现有的模板言语 `Who is {Employee}['s] manager[?]`，选择右侧的省略号 (***...***)，然后在弹出菜单中选择“编辑”。  

1. 将模板话语更改为：`who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>添加新的模式模板话语

1. 仍然停留在“生成”  的“模式”  部分中，添加几个新的模式模板话语。 从“意向”下拉菜单中选择“OrgChart-Manager”  ，然后输入下面的每条模板话语：

    |意向|包含可选文本和预构建的实体的示例话语|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. 将应用定型。

3. 选择面板顶部的“测试”  以打开测试面板。 

4. 输入多条测试话语来验证模式是否匹配以及意向得分是否很高。 

    输入第一条话语后，选择结果下的“检查”  ，以便可以看到所有预测结果。 每个言语应该包含 **OrgChart Manager** 意向，并且应提取 Employee 和 datetimeV2 实体的值。

    |话语|
    |--|
    |Who will be Jill Jones manager|
    |who will be jill jones's manager|
    |Who will be Jill Jones's manager?|
    |who will be Jill jones manager on March 3|
    |Who will be Jill Jones manager next Month|
    |Who will be Jill Jones manager in a month?|

所有这些话语都在内部找到了实体，因此它们将匹配相同的模式，并且具有很高的预测得分。

## <a name="use-the-or-operator-and-groups"></a>使用 OR 运算符和 group

前面的几个模板言语非常接近。 使用 **group** `()` 和 **OR** `|` 语法可以减少模板言语。 

使用 group `()` 和 OR `|` 语法可将以下 2 个模式合并成单个模式。

|意向|包含可选文本和预构建的实体的示例话语|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

新的模板言语为： 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`。 

此代码围绕所需的谓语时态使用 **group**，并在两者之间结合 **or** 管道使用可选的 `in` 和 `on`。 

1. 在“模式”页上，选择“OrgChart-Manager”筛选器。   通过搜索 `manager` 来缩小列表范围。 

    ![在 OrgChart-Manager 意向模式中搜索“manager”一词](./media/luis-tutorial-pattern/search-patterns.png)

1. 保留模板言语的一个版本（以便在下一步骤中编辑），并删除其他变体。 

1. 将模板言语更改为： 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`。

1. 将应用定型。

1. 使用“测试”窗格测试言语的版本：

    |在“测试”窗格中输入的言语|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>使用言语开头和结尾定位符

模式语法提供包含脱字号 `^` 的开头和结尾言语定位符语法。 开头和结尾言语定位符可以结合使用，以针对非常具体的、可能是文本的言语；或者，可以单独使用以针对意向。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程为在没有许多示例话语的情况下难以高准确率预测的话语添加了两个意向。 为它们添加模式使得 LUIS 更好地预测了意向，并且分数显著提高。 对实体和可忽略的文本进行标记使得 LUIS 可以将模式应用于更广泛的各种话语。

> [!div class="nextstepaction"]
> [了解如何结合使用角色和模式](luis-tutorial-pattern-roles.md)
