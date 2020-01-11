---
title: 模式语法参考-LUIS
titleSuffix: Azure Cognitive Services
description: 创建实体以从语言理解（LUIS）应用的用户最谈话中提取关键数据。 提取的数据由客户端应用程序使用。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890255"
---
# <a name="pattern-syntax"></a>模式语法

模式语法是一个话语模板。 该模板应包含要匹配的字词和实体，还要包含希望忽略的字词和标点。 它不是一个正则表达式。

> [!CAUTION]
> 模式仅包括计算机学习的实体父代，而不包括子组件。

模式中的实体用花括号 `{}` 括起。 模式可包含实体和带角色的实体。 [Pattern。 any](luis-concept-entity-types.md#patternany-entity)是仅用于模式的实体。

模式语法支持以下语法：

|函数|语法|嵌套级别|示例|
|--|--|--|--|
|实体| {}-大括号|2|其中，窗体为 {entity name}？|
|可选|[]-方括号<BR><BR>可选和分组的任意组合的嵌套级别限制为3 |2|问号是可选的 [？]|
|分组|（）-括号|2|为（a \| b）|
|或| \| 垂直条（管道）<br><br>在一个组中，垂直条（或）的限制为2 |-|其中，格式为（{窗体名称-短&#x7c; } {窗体名称-长&#x7c; } {窗体编号}）|
|查询文本的开头和/或结尾|^-插入符号|-|^ 开始查询文本<br>查询文本为 ^<br>^ 包含 {number} 实体 ^ 的整个查询文本的严格文本匹配|

## <a name="nesting-syntax-in-patterns"></a>在模式中嵌套语法

带有方括号的**可选**语法可以嵌套两个级别。 例如：`[[this]is] a new form`。 此示例允许以下最谈话：

|嵌套的可选查询文本示例|说明|
|--|--|
|这是一个新窗体|匹配模式中的所有单词|
|是新的窗体|匹配模式中的外部可选单词和非可选字词|
|新窗体|仅匹配所需字词|

带括号的**分组**语法可以嵌套两个级别。 例如：`(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`。 此功能允许匹配三个实体中的任何一个。

如果 Entity1 是一个具有源（西雅图）和目标（Cairo）等角色的位置，而实体2是一个列表实体（RedWest）中的已知建筑物名称，则以下最谈话会映射到此模式：

|嵌套分组查询文本示例|说明|
|--|--|
|RedWest-C|与外部分组实体匹配|
|Seattle|与一个内部分组实体匹配|
|Cairo|与一个内部分组实体匹配|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>用可选语法嵌套组的限制

使用**可选**语法**分组**的组合具有3个嵌套级别的限制。

|允许|示例|
|--|--|
|是|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|否|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>嵌套带有 or 语法的组的限制

使用**or**语法**分组**的组合有2个垂直条。

|允许|示例|
|--|--|
|是|（test1 &#x7c; test2 &#x7c; （test3 &#x7c; test4））|
|否|（test1 &#x7c; test2 &#x7c; test3 &#x7c; （test4 &#x7c; test5）） |

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

要向模式模板添加 Pattern.any 实体，需用花括号将 Pattern.any 实体括起，比如 `How much does {Booktitle} cost and what format is it available in?`。

|模式与 Pattern.any 实体|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|模式中的书名|
|--|
|《偷书》的价格是多少？它适合哪种格式？|
|《询问》的价格是多少？它适合哪种格式？|
|《深夜小狗神秘事件》的价格是多少？它适合哪种格式？|

书籍标题的单词并不令人费解，因为 LUIS 根据模式来了解书籍标题的结束位置 LUIS。

## <a name="explicit-lists"></a>显式列表

通过创作 API 创建一个[显式列表](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)，以便在以下情况时允许例外：

* 模式包含一种[模式。](luis-concept-entity-types.md#patternany-entity)
* 而且，该模式语法允许基于查询文本的实体提取错误。

例如，假设你的模式中包含可选语法 `[]` 和实体语法 `{}`，两个语法的组合方式导致数据提取错误。

请考虑使用模式“[find] email about {subject} [from {person}]”。

在下面的话语中，主题和人员实体一个提取正确，一个提取错误：

|话语|实体|提取是否正确|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

在上表中，主题应为 "`the man from La Mancha`" （书籍标题），但由于主题包含可选的单词 `from`，因此标题未正确预测。

要避免模式出现此异常，需使用[显式列表的创作 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) 添加 `the man from la mancha` 作为 {subject} 实体的显式列表匹配。

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>用于在模板话语中标记可选文本的语法
使用正则表达式方括号语法 `[]` 标出话语中的可选文本。 最多能对两个括号使用方括号嵌套可选文本。

|模式与可选的文本|含义|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` 和 `from {person}` 是可选的|
|"能帮我 [？]|标点符号是可选的|

标点符号（`?`、`!`、`.`）应被忽略，需要使用模式中的方括号语法将其忽略。

## <a name="next-steps"></a>后续步骤

详细了解模式：

* [如何添加模式](luis-how-to-model-intent-pattern.md)
* [如何添加模式。任何实体](luis-how-to-add-entities.md#add-a-patternany-entity)
* [模式概念](luis-concept-patterns.md)

了解如何在 json 响应中返回[情绪](luis-reference-prebuilt-sentiment.md)。