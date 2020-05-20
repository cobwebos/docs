---
title: 迁移到 V3 机器学习实体
description: V3 创作提供了一个新的实体类型、机器学习实体，还提供了向计算机学习实体和应用程序的其他实体或功能添加关系的能力。
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: aaa5472f25a5eca5ceadf979c57a83874ce4cb6e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684588"
---
# <a name="migrate-to-v3-authoring-entity"></a>迁移到 V3 创作实体

V3 创作提供了一个新的实体类型、机器学习实体，还提供了向计算机学习实体和应用程序的其他实体或功能添加关系的能力。

## <a name="entities-are-decomposable-in-v3"></a>可以分解中的实体

使用用于[api](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)或门户的 V3 创作 api 创建的实体允许使用父和子元素生成分层实体模型。 父代称为**机器学习实体**，而子级称为计算机学习实体的**子实体**。

每个子实体也是一种机器学习实体，但带有新增功能的配置选项。

* **必需的功能**是保证实体与功能匹配时要提取的规则。 规则由模型所需的功能定义：
    * [预生成实体](luis-reference-prebuilt-entities.md)
    * [正则表达式实体](reference-entity-regular-expression.md)
    * [列出实体](reference-entity-list.md)。

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
    * 角色-角色仅可应用于计算机学习（父）实体。 角色不能应用于子实体
    * 使用层次结构和复合实体的批处理测试和模式

在设计迁移计划时，请在迁移所有分层和复合实体后，随时查看最终的计算机学习实体。 尽管直接迁移会起作用，但在进行更改并查看批处理测试结果和预测 JSON 后，更统一的 JSON 可能会导致更改，因此，传递到客户端应用的最终信息以不同的方式进行组织。 这类似于代码重构，应将其视为你的组织已有的审查过程。

如果没有为 V2 模型准备好批处理，并将批处理测试作为迁移的一部分迁移到 V3 模型，则将无法验证迁移对终结点预测结果的影响。

## <a name="migrating-from-v2-entities"></a>从 V2 实体迁移

当你开始转到 V3 创作模型时，应考虑如何移动到机器学习实体及其子实体和功能。

下表说明了需要从 V2 迁移到 V3 实体设计的实体。

|V2 创作实体类型|V3 创作实体类型|示例|
|--|--|--|
|复合实体|计算机学习实体|[了解更多信息](#migrate-v2-composite-entity)|
|分层实体|机器学习实体的角色|[了解更多信息](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>迁移 V2 复合实体

V2 组合的每个子项都应使用 V3 机器学习实体的子实体表示。 如果复合子级是预生成的、正则表达式或列表实体，则应在子实体上将其作为必需的功能应用。

规划将复合实体迁移到机器学习实体时的注意事项：
* 子实体不能用于模式
* 子实体不再共享
* 如果子实体用于非计算机学习，则需要将其标记为

### <a name="existing-features"></a>现有功能

用于在复合实体中提升字词的任何短语列表应作为一种功能应用于计算机学习（父）实体、子实体（子）实体或意向（如果短语列表仅适用于一个意向）。 计划将此功能添加到应显著提升的实体。 不要将此功能一般添加到机器学习（父）实体，如果它将最大程度地提高子实体（子）的预测。

### <a name="new-features"></a>新增功能

在 V3 创作中，添加一个计划步骤，以便将实体评估为所有实体和意向的可能功能。

### <a name="example-entity"></a>示例实体

此实体仅是一个示例。 你自己的实体迁移可能需要其他注意事项。

请考虑使用 V2 组合来修改使用的比萨饼 `order` ：
* 预生成用于传递时间的 datetimeV2
* 用于提升某些词的短语列表，如比萨饼、饼图、crust 和浇头
* 列出实体以检测浇头，例如蘑菇、olives、意大利辣肠。

此实体的一个示例查询文本是：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表演示了迁移：

|V2 模型|V3 模型|
|--|--|
|父组件实体，名为`Order`|名为`Order`|
|子预生成的 datetimeV2|* 将预生成的实体迁移到新应用。<br>* 在父项上为预生成的 datetimeV2 添加所需功能。|
|浇头的子列表实体|* 将列表实体迁移到新应用。<br>* 然后在父项上为 list 实体添加所需的功能。|


## <a name="migrate-v2-hierarchical-entity"></a>迁移 V2 分层实体

在 V2 创作中，在 LUIS 中的角色之前提供了一个层次结构实体。 这两个服务的作用与基于上下文使用的提取实体的目的相同。 如果你有分层实体，则可以将其视为具有角色的简单实体。

在 V3 创作中：
* 可以在机器学习（父）实体上应用角色。
* 角色不能应用于任何子实体。

此实体仅是一个示例。 你自己的实体迁移可能需要其他注意事项。

请考虑使用 V2 分层实体来修改比萨饼 `order` ：
* 其中每个子元素确定原始浇头或最终浇头

此实体的一个示例查询文本是：

`Change the topping from mushrooms to olives`

下表演示了迁移：

|V2 模型|V3 模型|
|--|--|
|父组件实体，名为`Order`|名为`Order`|
|具有原始和最终比萨浇头的子层次结构实体|* 向 `Order` 每个浇头添加角色。|

## <a name="api-change-constraint-replaced-with-required-feature"></a>API 更改约束已替换为所需功能

此更改是在 build 大会上的5月2020，仅适用于在应用程序使用受约束功能的 v3 创作 Api。 如果要从 v2 创作版本迁移到 v3 创作，或尚未使用 v3 约束功能，请跳过此部分。

**功能**-能够要求现有实体作为另一个模型的功能，并且仅在检测到实体时才提取该模型。 此功能未发生更改，但 API 和术语已更改。

|以前的术语|新术语|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>自动迁移

从**19 2020 年6月**开始，将不允许使用公开此功能的上一个创作 API 以编程方式创建约束。

所有现有约束功能都将自动迁移到所需的功能标志。 不需要对预测 API 进行编程更改，也不会对预测准确性的质量产生任何更改。

#### <a name="luis-portal-changes"></a>LUIS 门户更改

LUIS 预览门户将此功能称为 "**约束**"。 当前 LUIS 门户将此功能指定为**必需功能**。

#### <a name="previous-authoring-api"></a>以前的创作 API

此功能是在预览版创作中应用的，使用实体的子级的属性将**[实体子 API 创建](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** 为实体定义的一部分 `instanceOf` ：

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>新创作 API

现在使用和属性将此功能应用于**[添加实体功能关系 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** `featureName` `isRequired` 。 属性的值 `featureName` 是模型的名称。

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>后续步骤

* [开发人员资源](developer-reference-resource.md)
