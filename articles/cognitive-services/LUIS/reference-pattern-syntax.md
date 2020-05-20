---
title: 模式语法参考 - LUIS
description: 创建实体，以便从语言理解 (LUIS) 应用的用户话语中提取关键数据。 提取的数据由客户端应用程序使用。
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: a0139cf5ef424288c41c436fb63313494404f841
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684545"
---
# <a name="pattern-syntax"></a>模式语法

模式语法是一个话语模板。 模板应包含要匹配的字词和实体，以及要忽略的字词和[标点](luis-reference-application-settings.md#punctuation-normalization)。 它不是一个正则表达式  。

> [!CAUTION]
> 模式仅包括机器学习实体父项，而不包括子实体。

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
|Seattle|匹配内层分组实体之一|
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

要向模式模板添加 Pattern.any 实体，需用花括号将 Pattern.any 实体括起，比如 `How much does {Booktitle} cost and what format is it available in?`。

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

## <a name="next-steps"></a>后续步骤

详细了解模式：

* [如何添加模式](luis-how-to-model-intent-pattern.md)
* [如何添加模式。任何实体](luis-how-to-add-entities.md#add-a-patternany-entity)
* [模式概念](luis-concept-patterns.md)

了解如何在 json 响应中返回[情绪](luis-reference-prebuilt-sentiment.md)。