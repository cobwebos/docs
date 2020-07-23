---
title: 数据提取 - LUIS
description: 从包含意向和实体的话语文本中提取数据。 了解可以从语言理解智能服务 (LUIS) 中提取什么类型的数据。
author: diberry
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 35f015691b15c6451a66509671c7dc2fc72f38e4
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682190"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>从包含意向和实体的话语文本中提取数据
使用 LUIS 可以从用户的自然语言陈述中获取信息。 信息以一种程序、应用程序或聊天机器人能够使用其来采取操作的方式进行提取。 在以下部分中，通过 JSON 示例了解从意向和实体返回了什么数据。

最难提取的数据是机器学习数据，因为它不是精确的文本匹配。 在您确信接收到所需的数据之前，计算机学习[实体](luis-concept-entity-types.md)的数据提取需要成为[创作周期](luis-concept-app-iteration.md)的一部分。

## <a name="data-location-and-key-usage"></a>数据位置和密钥用法
LUIS 在已发布的[终结点](luis-glossary.md#endpoint)上从用户的查询文本中提取数据。 HTTPS 请求（POST 或 GET）包含陈述以及一些可选配置，例如暂存或生产环境  。

**V2 预测终结点请求**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 预测终结点请求**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

在编辑 LUIS 应用时，`appID` 可在该 LUIS 应用的“设置”**** 页上找到，也可在 URL 中找到（在 `/apps/` 之后）。 `subscription-key` 是用于查询应用的终结点密钥。 虽然可以在学习 LUIS 时使用免费的创作/初学者密钥，但是将终结点密钥更改为支持[所需 LUIS 用法](luis-limits.md#key-limits)的密钥非常重要。 `timezoneOffset` 的单位是分钟。

HTTPS 响应包含 LUIS 可基于当前发布的暂存或生产终结点的模型确定的所有意向和实体信息****。 终结点 URL 位于 [LUIS](luis-reference-regions.md) 网站的“管理”**** 部分的“密钥和终结点”**** 页上。

## <a name="data-from-intents"></a>意向中的数据
主数据是评分最高的意向名称****。 终结点响应为：

#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

|数据对象|数据类型|数据位置|值|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|"GetStoreInfo"|

如果聊天机器人或 LUIS 调用应用基于不止一个意向评分来进行决策，则返回所有意向的评分。


#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

设置 querystring 参数 `verbose=true`。 终结点响应为：

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)

设置 querystring 参数 `show-all-intents=true`。 终结点响应为：

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

意向按评分从高到低排序。

|数据对象|数据类型|数据位置|值|Score|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|String|intents[1].intent|"None"|0.0168218873|

如果添加预构建的域，则意向名称指示该域，例如 `Utilties` 或 `Communication` 以及意向：

#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

|域|数据对象|数据类型|数据位置|值|
|--|--|--|--|--|
|实用工具|Intent|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|通信|Intent|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>实体中的数据
大多数聊天机器人和应用程序需要的名称比意向名称多。 此额外的可选数据来源于在陈述中发现的实体。 每种类型的实体返回有关匹配项的不同信息。

陈述中的单个单词或短语可以匹配多个实体。 在这种情况下，会返回每个匹配实体及其评分。

在终结点的响应的**实体**数组中返回所有实体

## <a name="tokenized-entity-returned"></a>返回的切分后的实体

查看 LUIS 中的[令牌支持](luis-language-support.md#tokenization)。


## <a name="prebuilt-entity-data"></a>预构建实体数据
[预构建](luis-concept-entity-types.md)实体是基于正则表达式匹配项、使用开源 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 项目发现的。 预构建实体返回在实体数组中，并使用前缀为 `builtin::` 的类型名称。

## <a name="list-entity-data"></a>列表实体数据

[列表实体](reference-entity-list.md)表示固定的、封闭的相关单词集及其同义词。 LUIS 不会为列表实体发现更多值。 使用“建议”功能根据当前列表查看有关新词的建议****。 如果存在多个具有相同值的列表实体，则终结点查询中会返回其中每个实体。

## <a name="regular-expression-entity-data"></a>正则表达式实体数据

[正则表达式实体](reference-entity-regular-expression.md)基于您提供的正则表达式提取实体。

## <a name="extracting-names"></a>提取名称
从陈述提取名称非常困难，因为名称几乎可以是字母和单词的任何组合。 根据要提取的名称类型，有若干选项。 以下建议不是规则，而是更多准则。

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>添加预构建的 PersonName 和 GeographyV2 实体

[PersonName](luis-reference-prebuilt-person.md) 和 [GeographyV2](luis-reference-prebuilt-geographyV2.md) 实体在某些[语言区域性](luis-reference-prebuilt-entities.md)中可用。

### <a name="names-of-people"></a>人的姓名

人的姓名可能会带有些许格式，具体取决于语言和区域性。 使用预生成的**[personName](luis-reference-prebuilt-person.md)** 实体或具有名字和姓氏[角色](luis-concept-roles.md)的**[简单实体](luis-concept-entity-types.md#simple-entity)**。

如果使用简单实体，请确保给出的示例在话语的不同部分、在不同长度的话语中以及在所有意向（包括“None”意向）的话语中使用姓氏和名字。 定期[查看](luis-how-to-review-endoint-utt.md)终结点陈述以标记未能正确预测的任何名称。

### <a name="names-of-places"></a>地名

地名是固定且已知的，例如市、县、州、省和国家/地区。 使用预生成的实体 **[geographyV2](luis-reference-prebuilt-geographyv2.md)** 提取位置信息。

### <a name="new-and-emerging-names"></a>新出现的名称

一些应用需要能够找到新出现的名称，例如产品或公司。 这些类型的名称是最难提取的数据类型。 从一个**[简单的实体](luis-concept-entity-types.md#simple-entity)** 开始并添加[短语列表](luis-concept-feature.md)。 定期[查看](luis-how-to-review-endoint-utt.md)终结点陈述以标记未能正确预测的任何名称。

## <a name="patternany-entity-data"></a>Pattern.any 实体数据

[Pattern。 any](reference-entity-pattern-any.md)是仅在模式的模板查询文本中使用的可变长度占位符，用于标记实体开始和结束的位置。 若要应用模式，必须找到模式中使用的实体。

## <a name="sentiment-analysis"></a>情绪分析
如果在[发布](luis-how-to-publish-app.md#sentiment-analysis)时配置了情绪分析，LUIS json 响应会包含情绪分析。 请在[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)文档中详细了解情绪分析。

## <a name="key-phrase-extraction-entity-data"></a>关键短语提取实体数据
[关键短语提取实体](luis-reference-prebuilt-keyphrase.md)返回查询文本中的关键短语，[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)提供。

## <a name="data-matching-multiple-entities"></a>匹配多个实体的数据

LUIS 返回在陈述中发现的所有实体。 因此，聊天机器人可能需要根据结果做出决定。

## <a name="data-matching-multiple-list-entities"></a>匹配多个列表实体的数据

如果一个单词或短语与多个列表实体匹配，则终结点查询会返回每个列表实体。

对于查询 `when is the best time to go to red rock?` ，如果应用 `red` 在多个列表中包含单词，LUIS 将识别所有实体并返回一个实体数组作为 JSON 终结点响应的一部分。

## <a name="next-steps"></a>后续步骤

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。
