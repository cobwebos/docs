---
title: 应用架构定义
description: LUIS 应用使用 `.json` 或 `.lu` 表示，并且包括所有意向、实体、示例言语、功能和设置。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327317"
---
# <a name="app-schema-definition"></a>应用架构定义

LUIS 应用使用 `.json` 或 `.lu` 表示，并且包括所有意向、实体、示例言语、功能和设置。

## <a name="format"></a>格式

导入和导出应用时，请选择 `.json` 或 `.lu`。

|格式|信息|
|--|--|
|`.json`| 标准编程格式|
|`.lu`|受 Bot Framework 的 [Bot Builder 工具](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)支持。|

## <a name="version-7x"></a>版本 7.x

* 迁移到版本 7.x，使用嵌套式机器学习实体来表示实体。
* 支持在以下创作 API 上利用 `enableNestedChildren` 属性创作嵌套式计算机学习实体：
    * [添加标签](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [添加批处理标签](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [评审标签](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [建议用于实体的终结点查询](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [建议用于意向的终结点查询](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| element                  | 注释                              |
|--------------------------|--------------------------------------|
| "hierarchicals": [],     | 弃用，请使用 [机器学习实体](luis-concept-entity-types.md)。   |
| "复合"： []，        | 弃用，请使用 [机器学习实体](luis-concept-entity-types.md)。 [复合实体](reference-entity-composite.md) 引用。 |
| "closedLists": [],       | [列出实体](reference-entity-list.md) 引用，主要用作实体的功能。    |
| "versionId"： "0.1"，      | LUIS 应用的版本。|
| "name"： "example-app"，   | LUIS 应用的名称。 |
| "desc"： ""，              | LUIS 应用的可选说明。  |
| "culture"： "en-us"，      | 应用的[语言](luis-language-support.md)，影响基础功能，如预构建实体、机器学习和标记器。  |
| "tokenizerVersion"： "1.0.0"， | [分词器](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Pattern.any 实体](reference-entity-pattern-any.md)    |
| "regex_entities"： []，    |  [正则表达式实体](reference-entity-regular-expression.md)   |
| "phraselist"： []，       |  [短语列出 (功能) ](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features"： []，    |  弃用，请使用 [机器学习实体](luis-concept-entity-types.md)。 |
| "模式"： []，          |  模式利用[模式语法](reference-pattern-syntax.md)[提高预测准确性](luis-concept-patterns.md)   |
| "设置"： []           | [应用设置](luis-reference-application-settings.md)|

## <a name="version-6x"></a>版本 6.x

* 迁移到版本 6.x，使用新的[机器学习实体](reference-entity-machine-learned-entity.md)来表示实体。

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>版本 4.x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>后续步骤

* 迁移到 [V3 创作 API](luis-migration-authoring-entities.md)