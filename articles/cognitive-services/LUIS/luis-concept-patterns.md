---
title: 模式帮助预测-LUIS
titleSuffix: Azure Cognitive Services
description: 使用模式可在不提供更多话语的情况下获得更高的意向准确度。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: bad3bdc2b4508c082ca50647d5de5e7265c763a1
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639197"
---
# <a name="patterns-improve-prediction-accuracy"></a>模式可提高预测的准确性
模式旨在多条话语非常类似的情况下提升准确性。  使用模式可在不提供更多话语的情况下获得更高的意向准确度。 

## <a name="patterns-solve-low-intent-confidence"></a>模式解决了意向置信度低的问题
设想一个人力资源应用，它在组织结构图上报告某员工的相关信息。 如果具有员工的姓名和组织关系，LUIS 就能反馈所涉及的员工。 设想员工叫做孔西明，经理叫做柏隼，下属员工有翁捷生、康霓和尹锋。

![组织结构图](./media/luis-concept-patterns/org-chart.png)

|陈述|预测的意向|意向得分|
|--|--|--|
|孔西明的下属员工有谁？|获取组织结构组|.30|
|谁归孔西明管辖？|获取组织结构组|.30|

如果应用具有 10 到 20 条句子长度不同、字词顺序不同，甚至用词不同（“下属员工”、“经理”和“报告”的近义词）的话语，则 LUIS 返回的置信度得分可能较低。 创建一个模式来帮助 LUIS 理解字词顺序的重要性。 

模式可处理以下情况： 

* 意向得分较低
* 正确的意向得分不是最高得分但太接近最高得分。 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>模式不保证提取意向
模式使用多种预测技术。 在模式中设置模板话语的意向并不保证得出意向预测，但表示很可能得出。 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>模式不会提升机器学习到的实体检测

模式的主要用途是帮助预测意向和角色。 pattern.any 实体用来提取自由形式的实体。 当模式使用实体时，模式不会帮助检测机器学习到的实体。  

若将多个话语合并到单个模式，则无法提升实体预测。 若要触发简单实体，需要添加话语或使用列表实体，否则模式将无法触发。

## <a name="patterns-use-entity-roles"></a>模式使用实体角色
如果模式中的两个或多个实体具有相关的上下文，模式会使用实体[角色](luis-concept-roles.md)来提取关于实体的上下文信息。  

## <a name="prediction-scores-with-and-without-patterns"></a>使用和不使用模式的情况下的预测得分
假定有足够多的实例话语，则 LUIS 不使用模式即能提高预测的置信度。 无需提供尽量多的话语，模式就能增加置信度得分。  

## <a name="pattern-matching"></a>模式匹配
模式的匹配顺序是先检测模式内的实体，再验证模式的其余字词和字词顺序。 要匹配模式，需要在模式中包含实体。 模式在标记级别应用，而不在字符级别应用。 

## <a name="pattern-syntax"></a>模式语法
模式语法是一个话语模板。 该模板应包含要匹配的字词和实体，还要包含希望忽略的字词和标点。 它不是一个正则表达式  。 

模式中的实体用花括号 `{}` 括起。 模式可包含实体和带角色的实体。 [Pattern.any](luis-concept-entity-types.md#patternany-entity) 是仅在模式中使用的实体。 

模式语法支持以下语法：

|函数|语法|嵌套级别|示例|
|--|--|--|--|
|实体| {} - 大括号|2|窗体 {entity-name} 在哪里？|
|可选|[] - 方括号<BR><BR>可选和分组的任何组合的嵌套级别都限制为 3 |2|问号是可选的 [?]|
|分组|() - 括号|2|是 (a \| b)|
|或| \| - 竖线（管道）<br><br>一个组中的竖线限制为 2 |-|窗体 ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number}) 在哪里| 
|话语的开头和/或结尾|^ - 脱字号|-|^开始话语<br>话语完成^<br>^将整句话语与 {number} 实体进行严格的文本匹配^|

## <a name="nesting-syntax-in-patterns"></a>模式中的嵌套语法

带有方括号的**可选**语法可以嵌套两个级别。 例如：`[[this]is] a new form`。 此示例允许以下话语： 

|嵌套的可选话语示例|说明|
|--|--|
|这是一个新窗体|匹配模式中的所有字词|
|是一个新窗体|匹配模式中的外层可选字词和非可选字词|
|一个新窗体|仅匹配必需的字词|

带括号的**分组**语法可以嵌套两层。 例如：`(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`。 此功能允许匹配任意三个实体。 

如果 Entity1 是具有起点（西雅图）和终点（开罗）等角色的位置，并且 Entity 2 是列表实体中的一座已知大楼名称 (RedWest-C)，则以下话语将映射到此模式：

|嵌套的分组话语示例|说明|
|--|--|
|RedWest-C|匹配外层分组实体|
|西雅图|匹配内层分组实体之一|
|开罗|匹配内层分组实体之一|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>带有可选语法的组的嵌套限制

**分组**与**可选**语法组合的嵌套级别限制为 3。

|允许|示例|
|--|--|
|是|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|否|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>带有 or-ing 语法的组的嵌套限制

**分组**与 **or-ing** 语法组合的竖线限制为 2。

|允许|示例|
|--|--|
|是|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|否|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>用于向模式模板添加实体的语法
要向模式模板添加实体，需用花括号将实体名称括起，比如 `Who does {Employee} manage?`。 

|模式与实体|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>用于向模式模板添加实体和角色的语法
实体角色表示为 `{entity:role}`其中实体名称后接一个冒号，再接角色名称。 要向模式模板添加带角色的实体，需用花括号将实体名称和角色名称括起，比如 `Book a ticket from {Location:Origin} to {Location:Destination}`。 

|模式与实体角色|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>用于向模式模板添加 pattern.any 的语法
Pattern.any 实体可用于向模式添加不同长度的实体。 只要按照模式模板操作，pattern.any 即可为任意长度。 

要向模式模板添加 Pattern.any 实体，需用花括号将 Pattern.any 实体括起，比如 `How much does {Booktitle} cost and what format is it available in?`  。  

|模式与 Pattern.any 实体|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|模式中的书名|
|--|
|《偷书》的价格是多少？它适合哪种格式  ？|
|《询问》的价格是多少？它适合哪种格式  ？|
|《深夜小狗神秘事件》的价格是多少？它适合哪种格式  ？| 

对于 LUIS 而言，书名的字词不容易混淆，因为 LUIS 可以根据 Pattern.any 实体知道书名在哪里结尾。

## <a name="explicit-lists"></a>显式列表

在以下情况下请通过创作 API 创建一个[显式列表](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)来允许例外：

* 你的模式包含 [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* 并且该模式语法可能会基于话语提取不正确的实体。 

例如，假设你的模式中包含可选语法 `[]` 和实体语法 `{}`，两个语法的组合方式导致数据提取错误。

请考虑使用模式“[find] email about {subject} [from {person}]”。

在下面的话语中，主题和人员实体一个提取正确，一个提取错误   ：

|话语|实体|提取是否正确|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

在上表中，主题应当是 `the man from La Mancha`（书名），但因为主题包含可选字词 `from`，因此书名没有正确预测。 

要避免模式出现此异常，需使用[显式列表的创作 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) 添加 `the man from la mancha` 作为 {subject} 实体的显式列表匹配。

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>用于在模板话语中标记可选文本的语法
使用正则表达式方括号语法 `[]` 标出话语中的可选文本。 最多能对两个括号使用方括号嵌套可选文本。

|模式与可选的文本|含义|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` 和 `from {person}` 是可选的|
|`你可以帮我吗[?]|问号是可选的|

标点符号（`?`、`!`、`.`）应当忽略，并且你需要在模式中使用方括号语法来忽略它们。 

## <a name="pattern-only-apps"></a>只有模式的应用
你可以构建其中的意向没有示例话语的应用，只要每个意向具有模式即可。 对于只有模式的应用，模式不能包含通过机器学习到的实体，因为这些实体要求具有示例话语。 

## <a name="best-practices"></a>最佳实践
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本教程中学习如何实现模式](luis-tutorial-pattern.md)
