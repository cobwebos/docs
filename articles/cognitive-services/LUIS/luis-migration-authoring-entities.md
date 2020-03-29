---
title: 迁移到 V3 机器学习实体
titleSuffix: Azure Cognitive Services
description: V3 创作提供了一种新的实体类型，即机器学习的实体，以及向机器学习实体和应用程序的其他实体或功能添加关系的能力。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563830"
---
# <a name="migrate-to-v3-authoring-entity"></a>迁移到 V3 创作实体

V3 创作提供了一种新的实体类型，即机器学习的实体，以及向机器学习实体和应用程序的其他实体或功能添加关系的能力。

## <a name="entities-are-decomposable-in-v3"></a>实体在 V3 中可分解

使用 V3 创作 API 创建的实体（使用[API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)还是预览[门户](https://preview.luis.ai/)）允许您使用父实体和子级构建分层实体模型。 父级称为**机器学习实体**，子级称为机器学习实体的**子组件**。

每个子组件也是机器学习的实体，但添加了约束和描述符的配置选项。

* **约束**是保证实体与规则匹配时提取的精确文本匹配规则。 规则由一个确切的文本匹配实体定义，当前：[预构建实体](luis-reference-prebuilt-entities.md)、[正则表达式实体](reference-entity-regular-expression.md)或[列表实体](reference-entity-list.md)。
* **描述符**是用于强指示实体[的特征](luis-concept-feature.md)，如短语列表或实体。

V3 创作提供了一种新的实体类型，即机器学习的实体，以及向机器学习实体和应用程序的其他实体或功能添加关系的能力。

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>这些新关系如何与 V2 创作进行比较

V2 创作提供了分层和复合实体以及角色和功能来完成此相同的任务。 由于实体、要素和角色没有明确关联，因此很难理解 LUIS 在预测期间如何隐含关系。

使用 V3 时，关系由应用作者明确设计。 这允许您作为应用作者：

* 直观地查看 LUIS 如何预测这些关系，在示例陈述中
* 使用[交互式测试窗格](luis-interactive-test.md)或终结点测试这些关系
* 通过结构良好的命名嵌套[.json 对象](reference-entity-machine-learned-entity.md)在客户端应用程序中使用这些关系

## <a name="planning"></a>规划

迁移时，请考虑迁移计划中的以下内容：

* 备份 LUIS 应用，并在单独的应用上执行迁移。 同时提供 V2 和 V3 应用可以验证所需的更改及其对预测结果的影响。
* 捕获当前预测成功指标
* 将当前仪表板信息捕获为应用状态快照
* 查看现有意图、实体、短语列表、模式和批处理测试
* 可以迁移以下元素**而不进行更改**：
    * 意向
    * 实体
        * 正则表达式实体
        * 列表实体
    * 功能
        * 短语列表
* 需要**使用更改**迁移以下元素：
    * 实体
        * 分层实体
        * 复合实体
    * 角色 - 角色只能应用于机器学习的（父）实体。 角色不能应用于子组件
    * 使用分层和复合实体的批处理测试和模式

设计迁移计划时，请留出时间查看所有分层和复合实体之后的最终机器学习实体。 虽然直接迁移将起作用，但当您进行更改并查看批处理测试结果并预测 JSON 后，JSON 越统一，可能会导致您进行更改，以便传递到客户端应用的最终信息以不同的方式组织。 这与代码重构类似，应该与您的组织所设置的相同审核流程进行处理。

如果 V2 模型没有批处理测试，并且批处理测试作为迁移的一部分迁移到 V3 模型，则无法验证迁移将如何影响终结点预测结果。

## <a name="migrating-from-v2-entities"></a>从 V2 实体迁移

开始迁移到 V3 创作模型时，应考虑如何移动到机器学习的实体及其子组件（包括约束和描述符）。

下表记下哪些实体需要从 V2 迁移到 V3 实体设计。

|V2 创作实体类型|V3 创作实体类型|示例|
|--|--|--|
|复合实体|机器学习实体|[了解更多信息](#migrate-v2-composite-entity)|
|分层实体|机器学习实体的角色|[了解更多信息](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>迁移 V2 复合实体

V2 复合的每个子级都应使用 V3 计算机学习实体的子组件表示。 如果复合子项是预构建的正则表达式或列表实体，则应将此子项作为表示子子组件**的约束**应用于该实体。

计划将复合实体迁移到机器学习实体时的注意事项：
* 子实体不能在模式中使用
* 子实体不再共享
* 子实体以前是非机器学习的，则需要标记它们

### <a name="existing-descriptors"></a>现有描述符

任何用于提升复合实体中单词的短语列表都应作为描述符应用于机器学习的（父实体）实体、子组件（子）实体或意图（如果短语列表仅适用于一个意图）。 计划向它应该最显著的提升的实体添加描述符。 如果描述符将最显著地增强子组件（子组件）的预测，则不要将描述符一般地添加到机器学习（父）实体中。

### <a name="new-descriptors"></a>新的描述符

在 V3 创作中，添加一个规划步骤，以评估实体作为所有实体和意图的可能描述符。

### <a name="example-entity"></a>示例实体

此实体仅是一个示例。 您自己的实体迁移可能需要其他注意事项。

考虑用于修改使用以下的比萨饼`order`的 V2 复合物：
* 预构建日期时间V2，用于交货时间
* 短语列表，以提高某些词，如比萨饼，馅饼，地壳，和浇头
* 列出实体，以检测浇头，如蘑菇，橄榄，辣椒。

此实体的示例表述是：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表演示了迁移：

|V2 型号|V3 型号|
|--|--|
|父 - 名为`Order`|父级 - 名为机器学习的实体`Order`|
|子级 - 预构建日期时间V2|• 将预构建的实体迁移到新应用。<br>• 为预构建的日期时间 V2 添加父级约束。|
|子 - 用于浇头的列表实体|• 将列表实体迁移到新应用。<br>• 然后对列表实体的父实体添加约束。|


## <a name="migrate-v2-hierarchical-entity"></a>迁移 V2 分层实体

在 V2 创作中，在 LUIS 中存在的角色之前提供了分层实体。 两者都达到了基于上下文使用情况提取实体的相同目的。 如果您有分层实体，则可以将它们视为具有角色的简单实体。

在 V3 创作中：
* 角色可以应用于机器学习的（父实体）实体。
* 不能将角色应用于任何子组件。

此实体仅是一个示例。 您自己的实体迁移可能需要其他注意事项。

考虑用于修改比萨饼`order`的 V2 分层实体：
* 其中每个子项确定原始浇头或最终浇头

此实体的示例表述是：

`Change the topping from mushrooms to olives`

下表演示了迁移：

|V2 型号|V3 型号|
|--|--|
|父 - 名为`Order`|父级 - 名为机器学习的实体`Order`|
|儿童 - 分层实体与原始和最终比萨饼浇头|•`Order`为每个浇头添加角色。|

## <a name="next-steps"></a>后续步骤

* [开发人员资源](developer-reference-resource.md)
