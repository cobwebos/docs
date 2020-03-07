---
title: 限制-LUIS
titleSuffix: Azure Cognitive Services
description: 本文介绍 Azure 认知服务语言理解 (LUIS) 的已知限制。 LUIS 具有多个边界区域。 模型边界可控制 LUIS 中的意向、实体和功能。 基于密钥类型的配额限制。 键盘组合可控制 LUIS 网站。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361079"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 模型和密钥的边界
LUIS 具有多个边界区域。 第一个是[模型边界](#model-boundaries)，它可控制 LUIS 中的意向、实体和功能。 第二个是基于密钥类型的[配额限制](#key-limits)。 边界的第三个区域是用于控制 LUIS 网站的[键盘组合](#keyboard-controls)。 第四个是 LUIS 创作网站和 LUIS [终结点](luis-reference-regions.md) API 之间的[世界区域映射](luis-glossary.md#endpoint)。


## <a name="model-boundaries"></a>模型边界

如果应用超出 LUIS 模型限制和边界，请考虑使用 [LUIS 调度](luis-concept-enterprise.md#dispatch-tool-and-model)应用或使用 [LUIS 容器](luis-container-howto.md)。

|区域|限制|
|--|:--|
| [应用名称][luis-get-started-create-app] | *默认最大字符长度 |
| 应用程序| 500每个 Azure 创作资源的应用程序 |
| [批处理测试][batch-testing]| 10 个数据集，每个数据集 1000 条话语|
| 显式列表 | 每个应用程序 50 个实体|
| 外部实体 | 无限制 |
| [调色][intents]|每个应用程序500：499个自定义意向和必需的_无_意向。<br>[基于调度](https://aka.ms/dispatch-tool)的应用程序具有相应的 500 个调度源。|
| [列表实体](./luis-concept-entity-types.md) | 父级：50 项，子级：20,000 项。 规范名称是 *默认最大字符长度。同义词值没有长度限制。 |
| [计算机学习的实体 + 角色](./luis-concept-entity-types.md)：<br> 复合、<br>简单、<br>实体角色|限制为 100 个父实体或 330 个实体，以用户先达到的限制为准。 就此边界来说，一个角色计为一个实体。 一个示例是一个简单的实体，其中包含2个角色：1个复合 + 1 个简单 + 2 个角色 = 4 个330实体。<br>子组件最多可以嵌套5个级别。|
|作为功能的模型| 可用作特定模型的描述符（功能）的最大模型数为10个模型。 用作特定模型的描述符（功能）的短语列表的最大数目为10个短语列表。|
| [预览-动态列表实体](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 个列表，其中的每个查询预测终结点请求约 1k 个实体|
| [模式](luis-concept-patterns.md)|每个应用程序 500 个模式。<br>模式的最大长度为 400 个字符。<br>每个模式 3 个 Pattern.any 实体<br>模式中最多 2 个嵌套可选文本|
| [Pattern.any](./luis-concept-entity-types.md)|每个应用程序 100 个，每个模式 3 个 pattern.any 实体 |
| [短语列表][phrase-list]|500短语列表。 不可互换的 phraselist 最多包含5000个短语。 可互换 Phraselist 最多包含50000个短语。 每个应用程序500000个短语的最大短语数。|
| [预生成实体](./luis-prebuilt-entities.md) | 无限制|
| [正则表达式实体](./luis-concept-entity-types.md)|20 个实体<br>每个正则表达式实体模式 最多 500 个字符|
| [角色](luis-concept-roles.md)|每个应用程序 300 个角色。 每个实体 10 个角色|
| [查询文本][utterances] | 500 个字符|
| [最谈话][utterances] | 每个应用程序 15,000 条 - 对每个意向的话语数量没有限制|
| [版本](luis-concept-version.md)| 100每个应用程序版本 |
| [版本名称][luis-how-to-manage-versions] | 10 个字符，仅限字母数字和句点 (.) |

*默认最大字符长度为 50 个字符。

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>名称唯一性

使用以下命名唯一性规则。

以下内容在 LUIS 应用中必须是唯一的：

* 版本名称
* 意向
* 实体
* 角色

以下内容在应用范围内必须是唯一的：

* 短语列表

## <a name="object-naming"></a>对象命名

请勿在下列名称中使用下列字符。

|Object|排除字符|
|--|--|
|意向、实体和角色名称|`:`<br>`$` <br> `&`|
|版本名称|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>密钥使用情况

语言理解拥有单独的密钥，一类用于创作，另一类用于查询预测终结点。 若要详细了解密钥类型之间的差异，请参阅 [LUIS 中的创作密钥和查询预测终结点密钥](luis-concept-keys.md)。

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>资源键限制

资源键对于创作和终结点具有不同的限制。 LUIS 预测查询终结点键仅对终结点查询有效。

* 500每个 Azure 创作资源的应用程序

|密钥|创作|端点|目的|
|--|--|--|--|
|入门|1 百万/月，5/秒|1 千/月，5/秒|创作 LUIS 应用|
|F0-免费层 |1 百万/月，5/秒|1 万/月，5/秒|查询 LUIS 终结点|
|S0-基本层|-|50/秒|查询 LUIS 终结点|
|S0-标准层|-|50/秒|查询 LUIS 终结点|
|[情绪分析集成](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|提供了包含关键短语数据提取的情绪信息，无需其他 Azure 资源。 |
|[语音集成](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|每个单位成本1000个终结点请求|将口语话语转为文本话语并返回 LUIS 结果|

[了解有关定价的详细信息。][pricing]

## <a name="keyboard-controls"></a>键盘控件

|键盘输入 | 说明 |
|--|--|
|Control+E|切换话语列表中的令牌和实体|

## <a name="website-sign-in-time-period"></a>网站登录时间段

登录访问权限为 60 分钟。 在此时间段后，会出现此错误。 需重新登录。

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
