---
title: 限制 - LUIS
description: 本文介绍 Azure 认知服务语言理解 (LUIS) 的已知限制。 LUIS 具有多个边界区域。 模型边界可控制 LUIS 中的意向、实体和功能。 基于密钥类型的配额限制。 键盘组合可控制 LUIS 网站。
ms.topic: reference
ms.date: 04/02/2020
ms.openlocfilehash: 4aa69cb0fd36fe5bf4ea2928022aea602b8830d6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618873"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 模型和密钥的边界
LUIS 具有多个边界区域。 第一个是[模型边界](#model-boundaries)，它可控制 LUIS 中的意向、实体和功能。 第二个是基于密钥类型的[配额限制](#key-limits)。 边界的第三个区域是用于控制 LUIS 网站的[键盘组合](#keyboard-controls)。 第四个是 LUIS 创作网站和 LUIS [终结点](luis-glossary.md#endpoint) API 之间的[世界区域映射](luis-reference-regions.md)。


## <a name="model-boundaries"></a>模型边界

如果应用超出 LUIS 模型限制和边界，请考虑使用 [LUIS 调度](luis-concept-enterprise.md#dispatch-tool-and-model)应用或使用 [LUIS 容器](luis-container-howto.md)。

|区域|限制|
|--|:--|
| [应用名称][luis-get-started-create-app] | *默认最大字符长度 |
| 应用程序| 每个 Azure 创作资源 500 个应用程序 |
| [批处理测试][batch-testing]| 10 个数据集，每个数据集 1000 条话语|
| 显式列表 | 每个应用程序 50 个实体|
| 外部实体 | 无限制 |
| [意向][intents]|每个应用程序 500 个：499 个自定义意图，以及所需的_无_意图。<br>[基于调度](https://aka.ms/dispatch-tool)的应用程序具有相应的 500 个调度源。|
| [列表实体](./luis-concept-entity-types.md) | 父级：50 项，子级：20,000 项。 规范名称是 *默认最大字符长度。同义词值没有长度限制。 |
| [机器学习实体 + 角色](./luis-concept-entity-types.md)：<br> 复合、<br>简单、<br>实体角色|限制为 100 个父实体或 330 个实体，以用户先达到的限制为准。 就此边界来说，一个角色计为一个实体。 例如，具有简单实体的复合，它有 2 个角色：330 个实体中的 4 个复合 = 1 个简单 + 2 个角色。<br>子组件最多可以嵌套 5 层。|
|作为特征的模型| 可用作特定模型的描述符（特征）的模型的最大数量为 10 个模型。 可用作特定模型的描述符（特征）的短语列表的最大数量为 10 个短语列表。|
| [预览 - 动态列表实体](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 个列表，其中的每个查询预测终结点请求约 1k 个实体|
| [模式](luis-concept-patterns.md)|每个应用程序 500 个模式。<br>模式的最大长度为 400 个字符。<br>每个模式 3 个 Pattern.any 实体<br>模式中最多 2 个嵌套可选文本|
| [Pattern.any](./luis-concept-entity-types.md)|每个应用程序 100 个，每个模式 3 个 pattern.any 实体 |
| [短语列表][phrase-list]|500 个短语列表。 10 个全局短语列表，由于模型作为特征限制。 不可互换的短语列表最多包含 5，000 个短语。 可互换短语列表最多包含 50，000 个短语。 每个应用程序的最大短语总数为 500,000 个短语。|
| [预构建实体](./luis-prebuilt-entities.md) | 无限制|
| [正则表达式实体](./luis-concept-entity-types.md)|20 个实体<br>每个正则表达式实体模式 最多 500 个字符|
| [角色](luis-concept-roles.md)|每个应用程序 300 个角色。 每个实体 10 个角色|
| [单条话语][utterances] | 500 个字符|
| [陈述][utterances] | 每个应用程序 15,000 条 - 对每个意向的话语数量没有限制|
| [版本](luis-concept-version.md)| 每个应用程序 100 个版本 |
| [版本名称][luis-how-to-manage-versions] | 10 个字符，仅限字母数字和句点 (.) |

*默认最大字符长度为 50 个字符。

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>名称唯一性

使用以下命名唯一性规则。

以下项在 LUIS 应用中必须唯一：

* 版本名称
* 意向
* 实体
* 角色

以下项在应用范围内必须唯一：

* 短语列表

## <a name="object-naming"></a>对象命名

请勿在下列名称中使用下列字符。

|Object|排除字符|
|--|--|
|意向、实体和角色名称|`:`<br>`$` <br> `&`|
|版本名称|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>资源使用和限制

语言理解有单独的资源，一种类型用于创作，一种类型用于查询预测终结点。 若要详细了解密钥类型之间的差异，请参阅 [LUIS 中的创作密钥和查询预测终结点密钥](luis-concept-keys.md)。

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>创作资源限制

筛选_kind_Azure`LUIS.Authoring`门户中的资源时，请使用 类型 ，。 LUIS 限制每个 Azure 创作资源 500 个应用程序。

|创作资源|创作 TPS|
|--|--|
|入门|1 百万/月，5/秒|
|F0 - 免费层 |1 百万/月，5/秒|

* TPS = 每秒事务

[详细了解定价。][pricing]

### <a name="query-prediction-resource-limits"></a>查询预测资源限制

筛选_kind_Azure`LUIS`门户中的资源时，请使用 类型 ，。在运行时使用的 LUIS 查询预测终结点资源仅适用于终结点查询。

|查询预测资源|查询 TPS|
|--|--|
|F0 - 免费层 |1 万/月，5/秒|
|S0 - 标准层|50/秒|

### <a name="sentiment-analysis"></a>情绪分析

[提供情绪信息的情绪分析集成](luis-how-to-publish-app.md#enable-sentiment-analysis)无需其他 Azure 资源即可提供。

### <a name="speech-integration"></a>语音集成

[语音集成](../speech-service/how-to-recognize-intents-from-speech-csharp.md)提供 1000 个每单位成本的终结点请求。

[详细了解定价。][pricing]

## <a name="keyboard-controls"></a>键盘控件

|键盘输入 | 描述 |
|--|--|
|Control+E|切换话语列表中的令牌和实体|

## <a name="website-sign-in-time-period"></a>网站登录时间段

登录访问权限为 60 分钟****。 在此时间段后，会出现此错误。 需重新登录。

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
