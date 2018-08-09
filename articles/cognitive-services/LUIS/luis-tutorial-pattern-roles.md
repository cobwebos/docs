---
title: 使用模式角色改进 LUIS 预测的教程 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 本教程介绍如何使用与上下文相关的实体的模式角色来改进 LUIS 预测。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 2fd473226dca2576be79b90bc05d66599f759713
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524138"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>教程：使用模式角色来改进应用

本教程介绍如何使用含与模式合并的角色的简单实体来改进意向和实体预测。  当使用模式时，意向需要较少的示例话语。

> [!div class="checklist"]
* 理解模式角色
* 使用含角色的简单实体 
* 使用含角色的简单实体为话语创建模式
* 如何验证模式预测改进

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>开始之前
如果还没有[模式](luis-tutorial-pattern.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json) GitHub 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `roles`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

## <a name="the-purpose-of-roles"></a>角色的用途
角色的用途是在话语中提取与上下文相关的实体。 在 `Move new employee Robert Williams from Sacramento and San Francisco` 话语中，原城市和目的地城市值彼此相关，并使用公共语言来表示每个位置。 

当使用模式时，必须在模式匹配话语之前，先在该模式中检测到任何实体。 

创建模式的第一步是选择模式的意向。 通过选择意向，如果模式匹配，则常常返回具有较高分数（通常为 99-100%）的正确意向。 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>比较分层实体与包含角色的简单实体

在[分层教程](luis-quickstart-intent-and-hier-entity.md)中，“MoveEmployee”意向检测到将现有员工从一栋建筑物或办公楼搬到另一栋的时机。 示例话语具有原位置和目的地，但未使用角色。 相反，原位置和目的地是分层实体的子级。 

在本教程中，人力资源应用会检测有关将新员工从一个城市调到另一个城市的话语。 这两种类型的话语很相似，但通过不同的 LUIS 功能来解决。

|教程|示例陈述|原位置和目的地|
|--|--|--|
|[分层（无角色）](luis-quickstart-intent-and-hier-entity.md)|将 Jill Jones 从“a-2349”调到“b-1298”|a-2349、b-1298|
|本教程（含角色）|将 Billy Patterson 从“Yuma”调到“Denver”。|Yuma、Denver|

无法在模式中使用分层实体，因为分层父级只能在父级中使用。 若要返回命名的原位置和目的地，必须使用模式。

### <a name="simple-entity-for-new-employee-name"></a>新员工名称的简单实体
新员工 Billy Patterson 的名称还不是“员工”列表实体的一部分。 首先需提取新员工名称，才能将名称发送到外部系统以创建公司凭据。 创建公司凭据后，会将员工凭据添加到“员工”列表实体。

“员工”列表已在[列表教程](luis-quickstart-intent-and-list-entity.md)中创建。

“NewEmployee”实体是不具有任何角色的简单实体。 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>适用于调职城市且含角色的简单实体
需要将新员工和家人从现有城市调到虚构公司所在的城市。 由于新员工可以来自于任何城市，因此需要发现此位置。 诸如列表实体等的集合列表可能不起作用，因为只会提取列表中的城市。

与原城市和目的地城市相关联的角色名称需要在所有实体中是唯一的。 确保这些角色唯一的一种简单办法是通过命名策略将其绑定到包含实体。 “NewEmployeeRelocation”实体是包含两个角色（“NewEmployeeReloOrigin”和“NewEmployeeReloDestination”）的简单实体。

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>简单实体需要足够的示例才能被检测到
由于示例话语 `Move new employee Robert Williams from Sacramento and San Francisco` 仅具有机器学习的实体，因此，向意向提供足够的示例话语至关重要，以便检测到这些实体。  

**尽管模式允许提供较少的示例话语，但如果未检测到实体，则该模式将不匹配。**

如果由于简单实体是城市等名称而难以检测到该实体，请考虑添加类似值的短语列表。 这通过为 LUIS 提供有关该类型的字或短语的其他信号，帮助检测城市名称。 短语列表只能通过帮助模式匹配所必需的实体检测来为模式提供帮助。 

## <a name="create-new-entities"></a>创建新实体
1. 选择顶部菜单中的“生成”。

2. 在左侧导航栏中选择“实体”。 

3. 选择“创建新实体”。

4. 在弹出窗口中，输入 `NewEmployee` 作为简单实体。

5. 选择“创建新实体”。

6. 在弹出窗口中，输入 `NewEmployeeRelocation` 作为简单实体。

7. 从实体列表中选择“NewEmployeeRelocation”。 

8. 输入第一个角色作为 `NewEmployeeReloOrigin` 并选择 Enter。

9. 输入第二个角色作为 `NewEmployeeReloDestination` 并选择 Enter。

## <a name="create-new-intent"></a>创建新意向
如果在开始前删除了预生成的 keyPhrase 实体，接着在执行完此部分中的步骤后又将其添加了回来，则在这些步骤中为实体添加标签可能会更加轻松。 

1. 从左侧导航栏中选择“意向”。

2. 选择“创建新意向”。 

3. 在弹出对话框中，输入 `NewEmployeeRelocationProcess` 作为意向名称。

4. 输入以下示例话语，为新实体添加标签。 实体和角色值均以粗体显示。 如果发现为文本添加标签很容易，请记得切换到“令牌视图”。 

    在意向中添加标签时，不要指定实体的角色。 在稍后创建模式时才执行此操作。 

    |话语|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |将 **Bob Jones** 从 **Seattle** 调到 **Los Colinas**|Bob Jones|Seattle、Los Colinas|
    |将 **Dave C. Cooper** 从 **Redmond** 调到 **New York City**|Dave C. Cooper|Redmond、New York City|
    |将 **Jim Paul Smith** 从 **Toronto** 调到 **West Vancouver**|Jim Paul Smith|Toronto、West Vancouver|
    |将 **J. Benson** 从 **Boston** 调到 **Staines-upon-Thames**|J. Benson|Boston、Staines-upon-Thames|
    |将 **Travis "Trav" Hinton** 从 **Castelo Branco** 调到 **Orlando**|Travis "Trav" Hinton|Castelo Branco、Orlando|
    |将 **Trevor Nottington III** 从 **Aranda de Duero** 调到 **Boise**|Trevor Nottington III|Aranda de Duero、Boise|
    |将 **Dr.Greg Williams** 从 **Orlando** 调到 **Ellicott City**|Dr.Greg Williams|Orlando、Ellicott City|
    |将 **Robert "Bobby" Gregson** 从 **Kansas City** 调到 **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City、San Juan Capistrano|
    |将 **Patti Owens** 从 **Bellevue** 调到 **Rockford**|Patti Owens|Bellevue、Rockford|
    |将 **Janet Bartlet** 从 **Tuscan** 调到 **Santa Fe**|Janet Bartlet|Tuscan、Santa Fe|

    员工名称具有各种前缀、字数统计、语法和后缀。 这对于 LUIS 理解新员工名称的变体至关重要。 城市名称还具有各种字数统计和语法。 此多样性对于指导 LUIS 这些实体可能在用户的话语中出现的方式至关重要。 
    
    如果任一实体具有相同的字数统计并且没有任何其他变体，则指导 LUIS 此实体仅具有该字数统计并且没有任何其他变体。 LUIS 可能无法准确预测更广泛的变体集合，因为并未向它显示任何变化。 

    如果删除了 keyPhrase 实体，请立即将其添加回应用。

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-without-pattern"></a>查询不含模式的终结点

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. 将光标定位到地址中 URL 的末尾，并输入 `Move Wayne Berry from Miami to Mount Vernon`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```JSON
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

意向预测得分大约只有 50%。 如果客户端应用程序需要较大的数字，则需要修改此项目。 此外，也未预测到任何实体。

模式有助于预测得分，但是，必须在模式匹配话语之前正确预测实体。 

## <a name="add-a-pattern-that-uses-roles"></a>添加使用角色的模式
1. 选择顶部导航栏中的“生成”。

2. 选择左侧导航栏中的“模式”。

3. 从“选择意向”下拉列表中选择“NewEmployeeRelocationProcess”。 

4. 输入以下模式：`move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    如果定型、发布和查询终结点，则可能会因看到找不到实体而感到失望，因此，该模式不会进行匹配，因而预测也得不到改进。 这是没有足够的含带标签的实体的示例话语的结果。 要修复此问题，请添加短语列表，而不是添加更多示例。

## <a name="create-a-phrase-list-for-cities"></a>创建适用于城市的短语列表
城市就像人名一样很棘手，因为它们可以是字和标点符号的任意组合。 但区域和全球的城市都是已知的，因此，LUIS 需要城市的短语列表才能开始学习。 

1. 从左侧菜单的“提高应用性能”部分中，选择“短语列表”。 

2. 命名 `Cities` 列表，并为该列表添加以下 `values`：

    |短语列表的值|
    |--|
    |西雅图|
    |San Diego|
    |New York City|
    |洛杉矶|
    |Portland|
    |Philadephia|
    |迈阿密|
    |达拉斯|

    请勿添加世界上的每个城市，也不要添加区域中的每个城市。 LUIS 需要能够从列表中推断出城市的概况。 

    确保“这些值可互换”处于选中状态。 此设置意味着列表上的字被视为同义词。 这正是它们应在模式中的处理方式。

    请记住，[最后一次](luis-quickstart-primary-and-secondary-data.md)创建短语列表的教程系列，也有助于促进简单实体的实体检测。  

3. 定型并发布应用。

## <a name="query-endpoint-for-pattern"></a>适用于模式的查询终结点
1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

2. 将光标定位到地址中 URL 的末尾，并输入 `Move wayne berry from miami to mount vernon`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```JSON
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

意向得分现在会高出许多，并且角色名称为实体响应的一部分。

## <a name="clean-up-resources"></a>清理资源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 LUIS 应用的最佳做法](luis-concept-best-practices.md)