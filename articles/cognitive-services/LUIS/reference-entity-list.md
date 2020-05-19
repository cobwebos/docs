---
title: 列出实体类型 - LUIS
description: 列表实体表示一组固定、封闭的相关单词及其同义词。 LUIS 不会为列表实体发现更多值。 使用“建议”功能根据当前列表查看有关新词的建议。
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 339fb832ef4af069b6f040c5264426002189f93f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588881"
---
# <a name="list-entity"></a>列表实体

列表实体表示一组固定、封闭的相关单词及其同义词。 LUIS 不会为列表实体发现更多值。 使用“建议”功能根据当前列表查看有关新词的建议  。 如果存在多个具有相同值的列表实体，则终结点查询中会返回其中每个实体。

列表实体不进行机器学习。 它是确切的文本匹配。 LUIS 将任何列表中某个项的任何匹配项标记为响应中的实体。

**如果文本数据具有以下特征，则非常适合使用此实体：**

* 是已知的集。
* 不经常更改。 如果需要经常更改列表或希望列表自行扩展，则使用短语列表提升的简单实体是更好的选择。
* 此集不超出此实体类型的最大 LUIS [边界](luis-limits.md)。
* 查询文本中的文本是与同义词或规范名称不区分大小写的匹配项。 LUIS 不会使用超出匹配项的列表。 模糊匹配、词干分析、复数和其他变体不会使用列表实体进行解析。 若要管理变体，请考虑使用带有可选文本语法的[模式](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)。

![列表实体](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>用于导入到列表实体的示例 .json

  可使用以下 .json 格式将值导入现有列表实体：

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>示例 JSON 响应

假设应用有一个名为 `Cities` 的列表，允许城市名称的变体，包括机场城市 (Sea-tac)、机场代码 (SEA)、邮政编码 (98101) 和电话区号 (206)。

|列表项|项同义词|
|---|---|
|`Seattle`|`sea-tac`、`sea`、`98101`、`206`、`+1` |
|`Paris`|`cdg`、`roissy`、`ory`、`75001`、`1`、`+33`|

`book 2 tickets to paris`

在之前的陈述中，单词 `paris` 映射至属于 `Cities` 列表实体一部分的“巴黎”项。 列表实体同时匹配项的规范化名称及其同义词。

#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)


如果在查询字符串中设置了 `verbose=false`，则这是 JSON：

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

如果在查询字符串中设置了 `verbose=true`，则这是 JSON：

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|数据对象|实体名称|值|
|--|--|--|
|列表实体|`Cities`|`paris`|


## <a name="next-steps"></a>后续步骤

了解有关实体的详细信息：

* [概念](luis-concept-entity-types.md)
* [创建方法](luis-how-to-add-entities.md)
