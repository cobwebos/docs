---
title: 批处理测试
titleSuffix: Language Understanding - Azure Cognitive Services
description: 使用批处理测试持续优化应用程序并改进其语言理解能力。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: acb561970b6a8576d1219fc15758e21a3032c9e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813289"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>对 LUIS 门户中的 1000 个话语执行批处理测试

批处理测试验证你的[活动](luis-concept-version.md#active-version)定型模型，以判断其预测准确性。 批处理测试可帮助你在图表中查看当前定型的模型中的每个意向和实体的准确性。 查看批处理测试结果，以采取适当操作来提升准确性，例如，如果应用经常无法标识正确意向，则向意向添加更多示例表达。

## <a name="group-data-for-batch-test"></a>批处理测试的组数据

对于 LUIS 来说，用于批处理测试的表达必须是全新，这一点很重要。 如果具有表达数据集，将其划分为三组：添加到意向的表达、从已发布的终结点接收的表达，以及在定型 LUIS 后对其进行批处理测试的表达。 

## <a name="a-dataset-of-utterances"></a>表达数据集

提交表达批处理文件（称为数据集），以用于批处理测试。 该数据集是一个 JSON 格式的文件，包含最多 1,000 个被标记为非重复的表达。 可以在一个应用中测试最多 10 个数据集。 如果需要测试更多数据集，请删除数据集，然后添加新数据集。

|**规则**|
|--|
|*无重复表达|
|1000 个表达以内|

*重复项被视为完全字符串匹配，不匹配的项将先进行标记。 

## <a name="entities-allowed-in-batch-tests"></a>允许在批处理测试中使用的实体

即使批处理文件数据中没有对应的实体，模型中的所有自定义实体也会出现在批处理测试实体筛选器中。

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>批处理文件格式

批处理文件包含表达。 每个表达都必须具有任何你预测可被检测到的[机器学习实体](luis-concept-entity-types.md#types-of-entities)随附的预期意向预测。 

## <a name="batch-syntax-template-for-intents-with-entities"></a>使用实体的意向的批处理语法模板

使用以下模板启动批处理文件：

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

批处理文件使用 startPos 和 endPos 属性来记录实体的开始和结束。 值从零开始，不得以空格开始或结束。 这与使用 startIndex 和 endIndex 属性的查询日志不同。 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>不使用实体的意向的批处理语法模板

使用以下模板启动没有实体的批处理文件：

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

如果不想测试实体，请包含 `entities` 属性并将值设置为空数组 `[]`。


## <a name="common-errors-importing-a-batch"></a>导入批处理文件的常见错误

常见错误包括： 

> * 超过 1,000 个表达
> * 不具有实体属性的话语 JSON 对象。 此属性可以是空数组。
> * 在多个实体中标记的字词
> * 实体标签以空格开头或结尾。

## <a name="batch-test-state"></a>批处理测试状态

LUIS 跟踪每个数据集的最后一次测试的状态。 这包括大小（批处理中的表达数）、上次运行日期和最后结果（成功预测的表达数）。

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>批处理测试结果

批处理测试结果是散点图，称为错误矩阵。 此图表对批处理文件中的话语和当前模型的预测意向和实体进行四个方面的比较。 

“假正”和“假负”部分上的数据点指示错误，应对其进行调查。 如果所有数据点都在“真正”和“真负”部分上，则你的应用在此数据集上的准确性很好。

![图表的四个部分](./media/luis-concept-batch-test/chart-sections.png)

此图表可基于 LUIS 的当前定型来帮助你查找 LUIS 预测不正确的表达。 结果将按图表的每个区域来显示。 选择图表上的单个点，以查看表达信息，或选择区域名称，以查看该区域的表达结果。

![批处理测试](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>结果中的错误

批处理测试中的错误指示意向未按批处理文件中的指示进行预测。 在图表的两个红色部分中指示错误。 

假正部分指示表达匹配了不应匹配的意向或实体。 假负指示表达未匹配应匹配的意向或实体。 

## <a name="fixing-batch-errors"></a>修复批处理错误

如果在批处理测试中出现错误，可以向意向添加更多表达，和/或在实体中标记更多表达，以帮助 LUIS 在意向间进行区分。 如果你已添加了表达，且对其进行了标记，但在批处理测试中仍收到预测错误，请考虑添加[短语列表](luis-concept-feature.md)功能，其中包含特定于域的词汇，以帮助 LUIS 更快地理解。 

## <a name="next-steps"></a>后续步骤

* 了解如何[测试批处理](luis-how-to-batch-test.md)
