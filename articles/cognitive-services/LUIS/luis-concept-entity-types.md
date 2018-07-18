---
title: 了解 Azure 中 LUIS 应用中的实体类型 | Microsoft Docs
description: 在语言理解智能服务 (LUIS) 应用中添加实体（应用程序域中的关键数据）。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2018
ms.author: v-geberr
ms.openlocfilehash: ccb7269109309355e2af95f6fb2aa060c1998b22
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286012"
---
# <a name="entities-in-luis"></a>LUIS 中的实体

实体是话语中的字词或短语，而话语是应用程序域中的关键数据。

## <a name="entity-compared-to-intent"></a>实体与意向
实体表示要提取的话语中的字词或短语。 话语可包括多个实体，也可不包含任何实体。 实体表示一种类，包含相似对象（位置、事项、人员、事件或概念）的集合。 实体描述与意向相关的信息，它们有时对于应用执行任务至关重要。 例如，新闻搜索应用可能包括“主题”、“源”、“关键字”和“发布日期”等实体，这些是用于搜索新闻的关键数据。 在旅行预订应用中，“位置”、“日期”、“航空公司”、“舱位等级”和“机票”均为航班预订（与“Bookflight”意向相关）的关键信息。

相比之下，意向表示有关整个话语的预测。 

## <a name="entities-represent-data"></a>实体表示数据
实体是要从话语中提取的数据。 可以是姓名、日期、产品名称或任何词组。 

|话语|实体|数据|
|--|--|--|
|购买 3 张到纽约的机票|预生成的数字<br>Location.Destination|3<br>纽约|
|购买 3 月 5 日从纽约到伦敦的机票|Location.Origin<br>Location.Destination<br>预生成的 datetimeV2|纽约<br>伦敦<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>实体是可选的（但强烈建议使用）
意向是必需的，而实体是可选的。 无需为应用中的每个概念创建实体，而仅需为应用操作时所需的概念创建实体。 

如果话语不含机器人继续操作所需的详细信息，则无需添加这些信息。 随着应用的成熟，可在稍后添加信息。 

如果不确定如何使用这些信息，可添加一些常见的预生成的实体，如 datetimeV2、序号、电子邮件和电话号码。

## <a name="label-for-word-meaning"></a>字词含义的标签
如果选词或字词排列方式相同，但含义并不相同，请勿将其标记为实体。 

以下话语中，`fair` 一词为同形异义词。 该词虽拼写相同但含义不同：

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

如果希望事件实体查找所有事件数据，请标记第一个话语中的 `fair` 一词，而不是第二个话语。

## <a name="entities-are-shared-across-intents"></a>实体在意向之间共享
实体在意向之间共享。 它们不属于任一意向。 意向和实体可在语义上相关联，但这并非一种排它性的关系。

在“为我预订到巴黎的机票”这一话语中，“巴黎”是一个位置类型的实体。 通过识别用户输入中提到的实体，LUIS 可帮助选择特定操作来完成意向。

## <a name="assign-entities-in-none-intent"></a>在 None 意向中分配实体
所有意向（包括 None 意向）都应对实体进行标记。 这可帮助 LUIS 深入了解实体在话语中的位置，以及实体周围的字词。 

## <a name="types-of-entities"></a>实体类型
LUIS 提供多种类型的实体；预生成的实体、自定义机器学习实体和列表实体。

| 名称 | 可标记 | 说明 |
| -- |--|--|
| **预生成** <br/>[自定义](#prebuilt)| |  **定义**<br>用于表示常见概念的内置类型。 <br><br>**列表**<br/>关键短语编号、序数、温度、维度、资金、年龄、百分比、电子邮件、URL、电话号码和关键短语。 <br><br>预生成的实体名称已保留。 <br><br>添加到应用程序的所有预生成的实体会返回到[终结点](luis-glossary.md#endpoint)查询中。 有关详细信息，请参阅[预生成的实体](./Pre-builtEntities.md)。 <br/><br/>[实体响应示例](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **正则表达式**<br/>[RegEx](#regex)||**定义**<br>格式化的原始话语文本的自定义正则表达式。 不区分大小写，并忽略区域性变体。  <br><br>此实体非常适用于格式一致的词和短语（并且其任何变体也是一致的）。<br><br>完成拼写检查更改后，会应用正则表达式匹配。 <br><br>如果正则表达式过于复杂，例如使用了许多括号，则不能将表达式添加到模型。 <br><br>**示例**<br>`kb[0-9]{6,}` 与 kb123456 匹配。<br/><br/>[快速入门](luis-quickstart-intents-regex-entity.md)<br>[实体响应示例](luis-concept-data-extraction.md)|
| **简单** <br/>[机器学习](#machine-learned) | ✔ | **定义**<br>简单实体是描述单个概念的泛型实体，通过机器学习的上下文习得。 上下文包括选词、字词位置和话语长度。<br/><br/>该实体非常适用于格式不一致但含义相同的字词或短语。 <br/><br/>[快速入门](luis-quickstart-primary-and-secondary-data.md)<br/>[实体响应示例](luis-concept-data-extraction.md#simple-entity-data)|  
| **列表** <br/>[完全匹配](#exact-match)|| **定义**<br>列表实体表示系统中一组固定、封闭的相关单词及其同义词。 <br><br>每个列表实体可能具有一种或多种形式。 最适用于同一概念的一组表示方式的已知变体。<br/><br/>LUIS 不会为列表实体发现更多值。 使用...查看[语义字典](luis-glossary.md#semantic-dictionary)，根据当前列表查找有关新词的建议。<br/><br>如果存在多个具有相同值的列表实体，则终结点查询中会返回其中每个实体。 <br/><br/>[快速入门](luis-quickstart-intent-and-list-entity.md)<br>[实体响应示例](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[混合](#mixed) | ✔|**定义**<br>Patterns.any 是一种长度可变的占位符，仅在模式的模板话语中使用，用于标记实体的起始和结束位置。  <br><br>**示例**<br>例如，对于一个基于标题的书籍话语搜索，pattern.any 会提取完整的标题。 一个使用 pattern.any 的模板话语是 `Who wrote {BookTitle}[?]`。<br/><br/>[教程](luis-tutorial-pattern.md)<br>[实体响应示例](luis-concept-data-extraction.md#composite-entity-data)|  
| **复合** <br/>[机器学习](#machine-learned) | ✔|**定义**<br>复合实体由其他实体组成，例如预生成的实体和简单实体。 各种单独的实体构成整个实体。 复合实体中不允许使用列表实体。 <br><br>**示例**<br>名为 PlaneTicketOrder 的复合实体可能具有预构建的 `number` 和 `ToLocation` 子实体。 <br/><br/>[教程](luis-tutorial-composite-entity.md)<br>[实体响应示例](luis-concept-data-extraction.md#composite-entity-data)|  
| **分层** <br/>[机器学习](#machine-learned) |✔ | **定义**<br>分层实体是根据上下文习得的一类实体。<br><br>**示例**<br>如果分层实体 `Location` 具有子实体 `ToLocation` 和 `FromLocation`，则可基于话语中的上下文来确定每个子实体。 在话语 `Book 2 tickets from Seattle to New York` 中，`ToLocation` 和 `FromLocation` 因周围单词不同而在上下文中有所不同。 <br/><br/>**不使用该实体的情况**<br>如果要查找子实体文本精确匹配的实体，且不考虑上下文，则应使用列表实体。 如果要查找是否与其他实体类型存在父子关系，则应使用复合实体。<br/><br/>[快速入门](luis-quickstart-intent-and-hier-entity.md)<br>[实体响应示例](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
预生成的实体是由 LUIS 提供的自定义实体。 其中一些实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 /Specs 目录中有许多适用于支持的区域性的[示例](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs)。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。 

<a name="machine-learned"></a>
“机器学习”实体最适合通过[终结点查询](luis-concept-test.md#endpoint-testing)和[查看终结点话语](label-suggested-utterances.md)进行测试。 

<a name="regex"></a>
“正则表达式”实体由用户提供的正则表达式定义，用作实体定义的一部分。 

<a name="exact-match"></a>
“完全匹配”实体使用实体中提供的文本实现精确文本匹配。

<a name="mixed"></a>
“混合”实体使用实体检测方法的组合。

## <a name="entity-limits"></a>实体限制
查看[限制](luis-boundaries.md#model-boundaries)，了解可添加到模型中的每种类型的实体的数量。

## <a name="composite-vs-hierarchical-entities"></a>复合实体与分层实体
复合实体和分层实体都具有父子关系，并且都是机器学习的实体。 通过机器学习，LUIS 能够基于不同上下文（字词排列方式）来理解实体。 复合实体更为灵活，因为它们允许使用不同的实体类型作为子实体。 分层实体的子实体只能是简单实体。 

|Type|目的|示例|
|--|--|--|
|分层实体|简单实体的父子关系|Location.Origin=纽约<br>Location.Destination=伦敦|
|复合实体|父子实体：预生成的实体、列表实体、简单实体、分层实体| 数字=3<br>列表=头等舱<br>prebuilt.datetimeV2=3 月 5 日|

## <a name="data-matching-multiple-entities"></a>匹配多个实体的数据
如果一个单词或短语与多个实体匹配，则终结点查询会返回每个实体。 如果同时添加了预生成的数字实体和预生成的 datetimeV2，并且具有话语 `create meeting on 2018/03/12 for lunch with wayne`，则 LUIS 会识别所有实体，并返回一组实体作为 JSON 终结点响应的一部分： 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>匹配多个列表实体的数据
如果一个单词或短语与多个列表实体匹配，则终结点查询会返回每个列表实体。

对于查询 `when is the best time to go to red rock?`，且应用中的单词 `red` 出现在多个列表中，LUIS 会识别所有实体，并返回一组实体作为 JSON 终结点响应的一部分： 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果所需实体数超过最大实体数 

可能需要使用分层实体和复合实体。 分层实体反映共享某些特征或属于同一类别的实体之间的关系。 子实体均为其父实体的类别的成员。 例如，名为 PlaneTicketClass 的分层实体可能具有子实体 EconomyClass 和 FirstClass。 层次结构仅跨越一个级别。  

复合实体表示整体的一部分。 例如，名为 PlaneTicketOrder 的复合实体可能具有子实体 Airline、Destination、DepartureCity、DepartureDate 和 PlaneTicketClass。 复合实体可从预先存在的简单实体、分层实体的子实体或预生成的实体生成。  

LUIS 还提供非机器学习的列表实体类型，可允许 LUIS 应用指定固定的值列表。 请参阅 [LUIS 边界](luis-boundaries.md)参考内容，查看列表实体类型的限制。 

如果已考虑使用分层、复合和列表实体，但所需超过了限制，请与支持部门联系。 为此，请收集有关系统的详细信息，转到 [LUIS][LUIS] 网站，然后选择“支持”。 如果所持 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。 

## <a name="best-practices"></a>最佳实践

如果发起调用的应用或机器人需要执行操作时所需的话语中的某些参数或数据时，请创建[实体](luis-concept-entity-types.md)。 实体就是话语中需要提取的词或短语（比如用作函数的参数）。 

若要正确选择要添加到应用程序的实体类型，需要了解用户输入数据的方式。 可使用不同的机制（如机器学习、封闭的列表或正则表达式）查找每个实体类型。 如果不确定，请从简单实体开始，跨所有意向（包括 None 意向）在所有话语中标记代表该数据的词或短语。  

定期查看终结点话语，以查找常用用法，其中实体可标识为正则表达式，或通过精确文本匹配查找。  

查看时，应考虑添加短语列表，针对对域非常重要但不完全匹配的词或短语，以及 LUIS 对其不具有高置信度的词或短语，向 LUIS 添加信号。  

请参阅[最佳做法](luis-concept-best-practices.md)，了解详细信息。

## <a name="next-steps"></a>后续步骤

了解关于优良[话语](luis-concept-utterance.md)的概念。 

请参阅[添加实体](luis-how-to-add-entities.md)，了解如何将实体添加到 LUIS 应用的详细信息。

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website