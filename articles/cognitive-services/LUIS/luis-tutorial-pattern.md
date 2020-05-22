---
title: 教程：模式 - LUIS
description: 在本教程中，使用模式来改进意向和实体预测，同时提供更少的示例话语。 该模式以模板言语示例形式提供，该示例包括用于标识实体和可忽略文本的语法。
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: c9bbd521d49d669e8ebd18b29bda9f2add8f7739
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592910"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>教程：添加常见的模式模板言语格式以改进预测

在本教程中，使用模式来改进意向和实体预测，这样可以提供更少的示例言语。 该模式是分配给意向的模板言语，包含用于标识实体和可忽略文本的语法。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建模式
> * 验证模式预测改进
> * 将文本标记为可忽略以及在模式中进行嵌套
> * 使用测试面板验证模式成功

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>意向和模式中的言语

LUIS 应用中存储了两种类型的言语：

* 意向中的示例言语
* 模式中的模板言语

将模板言语作为一种模式添加后，总体说来可以为意向提供更少的示例言语。

模式作为文本匹配和机器学习的组合来应用。  模式中的模板言语以及意向中的示例言语使 LUIS 能够更好地理解哪些言语符合意向。

## <a name="import-example-app-and-clone-to-new-version"></a>导入示例应用并克隆到新版

请使用以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true)。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 将 JSON 导入到 [LUIS 门户](https://www.luis.ai)的新应用中。 在“我的应用”页上，选择“+ 新建聊天应用”，然后选择“作为 JSON 导入”。   选择在上一步骤中下载的文件。

1. 在“管理”部分的“版本”选项卡上选择活动版本，然后选择“克隆”。   将克隆的版本命名为 `patterns`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="create-new-intents-and-their-utterances"></a>创建新意向及其话语

这两个意向根据言语文本查找经理或经理的直接下属。 难点在于，这两个意向的含义不同，而大部分单词是相同的， 只是单词顺序不同。 为了能够正确预测意向，必须提供更多示例。

1. 从导航栏中选择“生成”。

1. 在“意向”页上，选择“+ 创建”以创建新意向。 

1. 在弹出对话框中输入 `OrgChart-Manager`，然后选择“完成”。

    ![创建新消息弹出窗口](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. 将示例陈述添加到意向。 这些言语不完全相像，但确实有一种可以提取的模式。

    |示例陈述|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    如果 keyPhrase 实体在意向的话语中而不是员工实体中标记，请不要担心。 两者都已在“测试”窗格和终结点中正确预测。

1. 在左侧导航栏中选择“意向”。

1. 选择“+ 创建”以创建新意向。 在弹出对话框中输入 `OrgChart-Reports`，然后选择“完成”。

1. 将示例陈述添加到意向。

    |示例陈述|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>有关示例话语数量的警告

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>在测试或发布前训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>发布应用，从终结点进行查询

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 转到地址栏中 URL 的末尾，将 YOUR_QUERY_HERE 替换为 `Who is the boss of Jill Jones?`。

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

两个顶级意向的分数很接近，而最高的意向没有高得很显著（超出 60%），并没有超出下一个意向的分数足够多。

由于 LUIS 训练每次都不完全相同（有一定的变化），因此这两个排名最靠前的评分可能会在下一个训练周期中反转。 结果导致可能会返回的错误意向。

使用模式使正确意向的分数在百分比上显着提高，并且距离下一个最高分数更远。

使此第二个浏览器窗口保持打开。 本教程稍后将使用该窗口。

## <a name="template-utterances"></a>模板话语
考虑到人力资源主题领域的性质，可通过几种常见方式询问组织中的员工关系。 例如：

|陈述|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

在未提供许多陈述示例的情况下，这些陈述过于相近而无法确定每个陈述的上下文唯一性。 通过为意向添加模式，LUIS 可了解意向的常见陈述模式，而无需提供许多陈述示例。

此意向的模板陈述示例包括：

|模板陈述示例|语法含义|
|--|--|
|`Who does {Employee} report to[?]`|可交换 `{Employee}`<br>忽略 `[?]`|
|`Who reports to {Employee}[?]`|可交换 `{Employee}`<br>忽略 `[?]`|

`{Employee}` 语法可标记模板陈述中的实体位置以及它是哪个实体。 可选语法 `[?]` 标记可选的单词或[标点](luis-reference-application-settings.md#punctuation-normalization)。 LUIS 匹配话语，忽略括号内的可选文本。

虽然语法类似于正则表达式，但它不是正则表达式。 仅支持大括号 `{}` 和方括号 `[]` 语法。 它们最多可以嵌套两层。

为了使模式与言语匹配，言语中的实体必须首先匹配模板言语中的实体。 这意味着，在带实体的模式成功之前，实体在预测度高的示例言语中必须有足够的示例。 但是，模板不会帮助预测实体，仅预测意向。

**尽管模式允许提供较少的示例话语，但如果未检测到实体，则该模式将不匹配。**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>添加 OrgChart-Manager 意向的模式

1. 选择顶部菜单中的“生成”。

1. 在左侧导航栏中，在“提高应用性能”下，从左侧导航栏中选择“模式” 。

1. 选择 **OrgChart-Manager** 意向，然后输入以下模板话语：

    |模板话语|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

    这些模板言语使用大括号表示法包含了 Employee 实体。

1. 在“模式”页上选择“OrgChart-Reports”意向，然后输入以下模板言语：

    |模板话语|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>使用模式时查询终结点

将模式添加到应用后，在预测运行时终结点上训练、发布和查询应用。

1. 选择“训练”。 训练完成后，请依次选择“发布”、“生产”槽、“完成”。  

1. 发布完成后，请将浏览器标签页切换回终结点 URL 标签页。

1. 转到地址栏中 URL 的末尾，将 YOUR_QUERY_HERE 替换为 `Who is the boss of Jill Jones?`

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

现在，意向预测的置信度已明显提高，次高意向的分数显著降低。 训练时，这两种意向不会反转。

### <a name="working-with-optional-text-and-prebuilt-entities"></a>使用可选文本和预构建的实体

本教程中前面的模式模板话语包含几个可选文本示例，例如字母 s 的所有格 `'s` 的使用以及问号 `?` 的使用。 假设需要在言语文本中允许当前和未来日期。

示例话语如下：

|Intent|包含可选文本和预构建的实体的示例话语|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

这些示例每个都使用 LUIS 需要正确预测的动词时态 `was`、`is`、`will be` 以及日期 `March 3`、`now` 和 `in a month`。 注意，除了 `in` 和 `on` 之外，表中最后两个示例使用了几乎相同的文本。

允许此可选信息的示例模板言语：

|Intent|包含可选文本和预构建的实体的示例话语|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


可选语法方括号 `[]` 的使用使此可选文本易于添加到模板话语中，并且可以嵌套到另一个级别 `[[]]` 并包括实体或文本。


**问：为何所有 `w` 字母（每句模板话语中的第一个字母）都是小写？它们不应当任选使用大写或小写吗？** 由客户端应用程序提交到查询终结点的话语将转换为小写。 模板话语可以为大写或小写，终结点话语也可以任意使用大写或小写。 比较始终在转换为小写后进行。

**问：如果 March 3 被同时预测为数字 `3` 和日期 `March 3`，预构建的数字为何不是模板话语的一部分？** 模板话语从上下文来看使用的是日期，无论是逐字表示为 `March 3` 还是抽象为 `in a month`. 日期可以包含数字，但数字不一定会被视为日期。 请始终使用能够最好地表示你要在预测 JSON 结果中返回的类型的实体。

**问：如果使用了措辞不当的话语（例如 `Who will {Employee}['s] manager be on March 3?`），将会怎样？** 像这种在语法上不同的动词时态（其中 `will` 和 `be` 是独立的）需要作为一句新的模板话语。 现有模板话语将不会匹配它。 虽然话语的意向未更改，但是话语中的单词位置已更改。 此更改会影响 LUIS 中的预测。 可以使用 [group 和 or 运算符](#use-the-or-operator-and-groups)来组合谓语时态，以合并这些言语。

**请记住：将首先查找实体，然后再匹配模式。**

### <a name="add-new-pattern-template-utterances"></a>添加新的模式模板话语

1. 仍然停留在“生成”的“模式”部分中，添加几个新的模式模板话语。 从“意向”下拉菜单中选择“OrgChart-Manager”，然后输入下面的每条模板话语：

    |Intent|包含可选文本和预构建的实体的示例话语|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. 选择导航栏的“训练”来训练该应用。

3. 训练完成后，选择面板顶部的“测试”以打开测试面板。

4. 输入多条测试话语来验证模式是否匹配以及意向得分是否很高。

    输入第一条话语后，选择结果下的“检查”，以便可以看到所有预测结果。 每个言语应该包含 **OrgChart Manager** 意向，并且应提取 Employee 和 datetimeV2 实体的值。

    |话语|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

所有这些话语都在内部找到了实体，因此它们将匹配相同的模式，并且具有很高的预测得分。 你添加了几个模式，这些模式将匹配多个言语变体。 无需在意向中添加任何示例言语，即可让模板言语在模式中工作。

这样使用模式提供了：
* 更高的预测评分
* 意向中的相同示例言语
* 模式中一些构造良好的模板言语

### <a name="use-the-or-operator-and-groups"></a>使用 OR 运算符和 group

前面的几个模板言语非常接近。 使用 **group** `()` 和 **OR** `|` 语法减少模板言语。

使用 group `()` 和 OR `|` 语法可将以下 2 个模式合并成单个模式。

|Intent|包含可选文本和预构建的实体的示例话语|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

新的模板言语为：

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]` 列中的一个值匹配。

此代码围绕所需的谓语时态使用 **group**，并在两者之间结合 **or** 管道使用可选的 `in` 和 `on`。

1. 在“模式”页上，选择“OrgChart-Manager”筛选器。  通过搜索 `manager` 来缩小列表范围。

1. 保留模板言语的一个版本（以便在下一步骤中编辑），并删除其他变体。

1. 将模板言语更改为：

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. 选择导航栏的“训练”来训练该应用。

3. 训练完成后，选择面板顶部的“测试”以打开测试面板。

    使用“测试”窗格测试言语的版本：

    |在“测试”窗格中输入的言语|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

通过使用更多模式语法，可以减少必须在应用中维护的模板言语的数量，同时仍保持高的预测评分。

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>使用言语开头和结尾定位符

模式语法提供包含脱字号 `^` 的开头和结尾言语定位符语法。 开头和结尾言语定位符可以结合使用，以针对非常具体的、可能是文本的言语；或者，可以单独使用以针对意向。

## <a name="using-patternany-entity"></a>使用 Pattern.any 实体

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>通过 Pattern.any 添加示例言语

1. 从顶部导航栏中选择“生成”，然后从左侧导航栏中选择“意向”。

1. 从“意向”列表中选择“FindForm”。

1. 添加一些示例言语。 应预测为 Pattern.any 的文本以粗体文本显示。 根据言语中两边的其他单词很难确定表单名称。 Pattern.any 可以标记实体的边界，这样有助于找到表单名称。

    |示例陈述|窗体名称|
    |--|--|
    |“实验室着火时该怎么办”的表单在哪里，在我阅读该表单后需要谁签名？|实验室着火时该怎么办
    |“公司新员工请求调职”在服务器上的哪里？|公司新员工请求调职|
    |“主要校园的健康和保健请求”的作者是谁并且最新版本是什么？|主要校园的健康和保健请求|
    |我正在查找名为“包括物理资产的 Office 移动请求”的表单。 |包括物理资产的 Office 移动请求|

    如果没有 Pattern.any 实体，则 LUIS 很难理解表单标题的结束位置，因为表单名称有很多种变体。

### <a name="create-a-patternany-entity"></a>创建 Pattern.any 实体
Pattern.any 实体提取不同长度的实体。 它仅适用于模式，因为模式会使用语法标记实体的开头和结尾。

1. 在左侧导航栏中选择“意向”。

1. 选择“+ 创建”，输入名称 `FormName`，然后选择“Pattern.any”作为类型。 选择“创建”。

### <a name="add-a-pattern-that-uses-the-patternany"></a>添加使用 Pattern.any 的模式

1. 从左侧导航栏中选择“模式”。

1. 选择“FindForm”意向。

1. 输入以下使用新实体的模板话语：

    |模板话语|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. 将应用定型。

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>测试自由格式的数据提取的新模式
1. 从顶栏中选择“测试”，打开测试面板。

1. 输入以下话语：

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. 在结果下选择“检查”，查看实体和意向的测试结果。

    首先找到 `FormName` 实体，然后找到模式，并确定意向。 如果测试结果中未检测到实体，因而未找到模式，则需要在意向（而不是模式）上添加更多示例陈述。

1. 选择顶部导航栏上的“测试”按钮，关闭测试面板。

### <a name="using-an-explicit-list"></a>使用显式列表

如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](reference-pattern-syntax.md#explicit-lists)来更正此问题。

## <a name="what-did-this-tutorial-accomplish"></a>本教程的目标是什么？

本教程添加了模式，以帮助 LUIS 通过明显更高的分数来预测意向，而无需添加更多的示例言语。 对实体和可忽略的文本进行标记使得 LUIS 可以将模式应用于更广泛的各种话语。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤


> [!div class="nextstepaction"]
> [了解如何结合使用角色和模式](luis-tutorial-pattern.md)
