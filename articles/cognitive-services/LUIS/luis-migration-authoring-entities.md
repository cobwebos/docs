---
title: 迁移到 V3 计算机学习的实体
titleSuffix: Azure Cognitive Services
description: V3 创作提供了一个新的实体类型、计算机学习的实体，还提供了将关系添加到机器学习的实体以及应用程序的其他实体或功能的能力。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563830"
---
# <a name="migrate-to-v3-authoring-entity"></a>迁移到 V3 创作实体

V3 创作提供了一个新的实体类型、计算机学习的实体，还提供了将关系添加到机器学习的实体以及应用程序的其他实体或功能的能力。

## <a name="entities-are-decomposable-in-v3"></a>可以分解中的实体

使用 "使用[api](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) " 或 "[预览门户](https://preview.luis.ai/)" 创建使用 V3 创作 api 创建的实体时，可以生成具有父项和子项的分层实体模型。 父代称为计算机**学习的实体**，而子级被称为计算机学习实体的**子组件**。

每个子组件也是机器学习的实体，但具有约束和描述符的添加配置选项。

* **约束**是精确的文本匹配规则，可保证在实体与规则匹配时提取实体。 规则由精确文本匹配实体定义，当前为：预生成的[实体](luis-reference-prebuilt-entities.md)、[正则表达式实体](reference-entity-regular-expression.md)或[列表实体](reference-entity-list.md)。
* **描述符**是用于强指示实体的[功能](luis-concept-feature.md)，如短语列表或实体。

V3 创作提供了一个新的实体类型、计算机学习的实体，还提供了将关系添加到机器学习的实体以及应用程序的其他实体或功能的能力。

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>如何将这些新关系与 V2 创作进行比较

V2 创作提供层次结构和组合实体以及角色和功能来完成此相同的任务。 由于实体、功能和角色没有彼此显式相关，因此很难理解 LUIS 在预测过程中隐含关系的方式。

使用 V3，关系是显式的，由应用作者设计。 这使你作为应用作者，可以：

* 直观地了解 LUIS 如何预测这些关系，示例最谈话
* 通过 "[交互式测试" 窗格](luis-interactive-test.md)或在终结点测试这些关系
* 在客户端应用程序中通过结构化的、命名的、嵌套的[json 对象](reference-entity-machine-learned-entity.md)来使用这些关系

## <a name="planning"></a>规划

迁移时，请在迁移计划中考虑以下事项：

* 备份 LUIS 应用，并在单独的应用上执行迁移。 同时使用 V2 和 V3 应用可以验证所需的更改和对预测结果的影响。
* 捕获当前预测成功指标
* 捕获当前仪表板信息作为应用状态的快照
* 查看现有意向、实体、短语列表、模式和批处理测试
* 以下元素可在**不更改的情况**下进行迁移：
    * 意向
    * 实体
        * 正则表达式实体
        * 列表实体
    * 功能
        * 短语列表
* 以下元素需要**随更改一起**迁移：
    * 实体
        * 分层实体
        * 复合实体
    * 角色-角色仅可应用于计算机学习的（父）实体。 角色不能应用于子组件
    * 使用层次结构和复合实体的批处理测试和模式

在设计迁移计划时，请在迁移所有分层和复合实体后，保留查看最终的计算机学习实体的时间。 尽管直接迁移会起作用，但在进行更改并查看批处理测试结果和预测 JSON 后，更统一的 JSON 可能会导致更改，因此，传递到客户端应用的最终信息以不同的方式进行组织。 这类似于代码重构，应将其视为你的组织已有的审查过程。

如果没有为 V2 模型准备好批处理，并将批处理测试作为迁移的一部分迁移到 V3 模型，则将无法验证迁移对终结点预测结果的影响。

## <a name="migrating-from-v2-entities"></a>从 V2 实体迁移

当你开始移动到 V3 创作模型时，应考虑如何移动到计算机学习的实体及其子组件（包括约束和描述符）。

下表说明了需要从 V2 迁移到 V3 实体设计的实体。

|V2 创作实体类型|V3 创作实体类型|示例|
|--|--|--|
|复合实体|计算机学习实体|[了解更多信息](#migrate-v2-composite-entity)|
|分层实体|计算机获知实体的角色|[了解更多信息](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>迁移 V2 复合实体

V2 组合的每个子项都应使用 V3 计算机学习实体的子组件来表示。 如果复合子级是预生成的、正则表达式或列表实体，则应在表示子级的子组件上将其应用为**约束**。

规划将复合实体迁移到计算机学习的实体时的注意事项：
* 子实体不能用于模式
* 子实体不再共享
* 如果子实体用于非计算机学习，则需要将其标记为

### <a name="existing-descriptors"></a>现有描述符

用于对复合实体中的单词进行提升的任何短语列表应作为描述符应用于计算机学习（父）实体、子组件（子）实体或意向（如果短语列表仅适用于一个意向）。 计划将描述符添加到实体，它应最大程度地提升。 如果该描述符将最大程度地提升子组件（子）的预测，请不要将其一般添加到计算机学习的（父）实体。

### <a name="new-descriptors"></a>新描述符

在 V3 创作中，添加一个计划步骤，以便将实体作为所有实体和意向的可能描述符进行评估。

### <a name="example-entity"></a>示例实体

此实体仅是一个示例。 你自己的实体迁移可能需要其他注意事项。

请考虑使用 V2 组合来修改使用的比萨饼 `order`：
* 预生成用于传递时间的 datetimeV2
* 用于提升某些词的短语列表，如比萨饼、饼图、crust 和浇头
* 列出实体以检测浇头，例如蘑菇、olives、意大利辣肠。

此实体的一个示例查询文本是：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表演示了迁移：

|V2 模型|V3 模型|
|--|--|
|名为 `Order` 的父组件实体|名为的父计算机的实体 `Order`|
|子预生成的 datetimeV2|* 将预生成的实体迁移到新应用。<br>* 在父项上为预生成的 datetimeV2 添加约束。|
|浇头的子列表实体|* 将列表实体迁移到新应用。<br>* 然后在父项上为列表实体添加约束。|


## <a name="migrate-v2-hierarchical-entity"></a>迁移 V2 分层实体

在 V2 创作中，在 LUIS 中的角色之前提供了一个层次结构实体。 这两个服务的作用与基于上下文使用的提取实体的目的相同。 如果你有分层实体，则可以将其视为具有角色的简单实体。

在 V3 创作中：
* 角色可应用于计算机学习的（父）实体。
* 角色不能应用于任何子组件。

此实体仅是一个示例。 你自己的实体迁移可能需要其他注意事项。

请考虑使用 V2 分层实体来修改比萨饼 `order`：
* 其中每个子元素确定原始浇头或最终浇头

此实体的一个示例查询文本是：

`Change the topping from mushrooms to olives`

下表演示了迁移：

|V2 模型|V3 模型|
|--|--|
|名为 `Order` 的父组件实体|名为的父计算机的实体 `Order`|
|具有原始和最终比萨浇头的子层次结构实体|* 向每个浇头的 `Order` 添加角色。|

## <a name="next-steps"></a>后续步骤

* [开发人员资源](developer-reference-resource.md)
