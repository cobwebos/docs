---
title: 模式角色
titleSuffix: Azure Cognitive Services
description: 模式从格式正确的模板言语中提取数据。 模板话语使用简单的实体和角色提取相关的数据，例如源位置和目标位置。
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: d6a2c9d92d79bed3f0e9a9976a64f6e11debba88
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523268"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>教程：使用角色提取与上下文相关的模式

在本教程中，使用模式从格式正确的模板话语中提取数据。 模板言语使用[简单的实体](luis-concept-entity-types.md#simple-entity)和[角色](luis-concept-roles.md)提取相关数据，例如源位置和目标位置。  当使用模式时，意向需要较少的示例话语。


**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 导入示例应用
> * 创建新实体
> * 创建新意向
> * 定型
> * 发布
> * 从终结点获取意向和实体
> * 使用角色创建模式
> * 创建城市的短语列表
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>在模式中使用角色

角色的用途是在言语中提取与上下文相关的实体。 在 `Move new employee Robert Williams from Sacramento and San Francisco` 话语中，原城市和目的地城市值彼此相关，并使用公共语言来表示每个位置。 


新员工 Billy Patterson 的名称还不是“员工”列表实体的一部分。 首先需提取新员工名称，才能将名称发送到外部系统以创建公司凭据。 创建公司凭据后，会将员工凭据添加到“员工”列表实体。

需要将新员工和家人从现有城市调到虚构公司所在的城市。 由于新员工可以来自于任何城市，因此需要发现此位置。 诸如列表实体等的集合列表可能不起作用，因为只会提取列表中的城市。

与原城市和目的地城市相关联的角色名称需要在所有实体中是唯一的。 确保这些角色唯一的一种简单办法是通过命名策略将其绑定到包含实体。 NewEmployeeRelocation 实体是具有以下两个角色的简单实体：NewEmployeeReloOrigin 和 NewEmployeeReloDestination。 Relo 是 relocation 的缩写。

由于示例话语 `Move new employee Robert Williams from Sacramento and San Francisco` 仅具有机器学习的实体，因此，向意向提供足够的示例话语至关重要，以便检测到这些实体。  

**尽管模式允许提供较少的示例话语，但如果未检测到实体，则该模式将不匹配。**

如果由于简单实体是城市等名称而难以检测到该实体，请考虑添加类似值的短语列表。 这通过为 LUIS 提供有关该类型的字或短语的其他信号，帮助检测城市名称。 短语列表只能通过帮助模式匹配所必需的实体检测来为模式提供帮助。 

## <a name="import-example-app"></a>导入示例应用
继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `roles`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="create-new-entities"></a>创建新实体

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

## <a name="train"></a>定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. 将光标定位到地址中 URL 的末尾，并输入 `Move Wayne Berry from Miami to Mount Vernon`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```json
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

提取了其中的一个位置，但未提取另一个位置。 

模式有助于预测得分，但是，必须在模式匹配话语之前正确预测实体。 

## <a name="pattern-with-roles"></a>包含角色的模式

1. 选择顶部导航栏中的“生成”。

2. 选择左侧导航栏中的“模式”。

3. 从“选择意向”下拉列表中选择“NewEmployeeRelocationProcess”。 

4. 输入以下模式：`move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    如果定型、发布和查询终结点，则可能会因看到找不到实体而感到失望，因此，该模式不会进行匹配，因而预测也得不到改进。 这是没有足够的含带标签的实体的示例话语的结果。 要修复此问题，请添加短语列表，而不是添加更多示例。

## <a name="cities-phrase-list"></a>城市短语列表
城市就像人名一样很棘手，因为它们可以是字和标点符号的任意组合。 区域和全球的城市都是已知的，因此，LUIS 需要城市的短语列表才能开始学习。 

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

    请勿添加世界上的每个城市，也不要添加区域中的每个城市。 LUIS 需要能够从列表中推断出城市的概况。 确保“这些值可互换”处于选中状态。 此设置意味着列表上的字被视为同义词。 

3. 定型并发布应用。

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Move wayne berry from miami to mount vernon`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 

    ```json
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

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程已添加了包含角色的实体以及带有示例话语的意向。 使用该实体的第一个终结点预测正确预测了意向，但可信度分数较低。 仅检测到了两个实体中的一个。 接下来，本教程添加了一个模式，该模式使用了实体角色和短语列表来改进话语中的城市名称的值。 第二个终结点预测返回了较高的可信度分数，并且找到了全部两个实体角色。 

> [!div class="nextstepaction"]
> [了解 LUIS 应用的最佳做法](luis-concept-best-practices.md)
