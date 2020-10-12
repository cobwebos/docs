---
title: 迁移到 V3 机器学习实体
description: V3 创作提供了一种新的实体类型，即机器学习实体，还可以向机器学习实体和应用程序的其他实体或功能添加关系。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324682"
---
# <a name="migrate-to-v3-authoring-entity"></a>迁移到 V3 创作实体

V3 创作提供了一种新的实体类型，即机器学习实体，还可以向机器学习实体和应用程序的其他实体或功能添加关系。

## <a name="entities-are-decomposable-in-v3"></a>实体在 V3 中可分解

使用 V3 创作 API 所创建的实体（通过使用 [API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) 或通过门户创建）使你能够构建具有父级和子级的分层实体模型。 父级称为“机器学习实体”，子级称为机器学习实体的“子实体” 。

每个子实体也是一种机器学习实体，但添加了功能的配置选项。

* “必需功能”是当实体与功能匹配时确保会提取实体的规则。 规则由模型所需的功能定义：
    * [预生成的实体](luis-reference-prebuilt-entities.md)
    * [正则表达式实体](reference-entity-regular-expression.md)
    * [列表实体](reference-entity-list.md)。

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>这些新关系与 V2 创作相比有何优势

V2 创作提供分层实体和复合实体以及角色和功能来完成相同的任务。 由于实体、功能和角色之间没有显式的关系，因此很难理解 LUIS 在预测过程中暗示关系的方式。

在 V3 中，关系是显式的，由应用作者设计。 因此，应用作者可以：

* 在示例言语中直观地了解 LUIS 如何预测这些关系
* 通过[交互式测试窗格](luis-interactive-test.md)或在终结点处测试这些关系
* 在客户端应用程序中通过结构化的、命名的、嵌套的[json 对象](reference-entity-machine-learned-entity.md)来使用这些关系

## <a name="planning"></a>规划

迁移时，请在迁移计划中考虑以下事项：

* 备份 LUIS 应用，并在单独的应用上执行迁移。 同时配备 V2 和 V3 应用可以验证所需的更改和对预测结果的影响。
* 捕获当前的预测成功指标
* 捕获当前仪表板信息作为应用状态的快照
* 查看现有意向、实体、短语列表、模式和批处理测试
* 可以在无需更改的情况下迁移以下元素：
    * 意向
    * 实体
        * 正则表达式实体
        * 列表实体
    * 功能
        * 短语列表
* 迁移以下元素时需要进行更改：
    * 实体
        * 分层实体
        * 复合实体
    * 角色 - 角色仅可应用于机器学习（父级）实体。 角色不能应用于子实体
    * 使用分层实体和复合实体的批处理测试和模式

在设计迁移计划时，请在所有分层实体和复合实体迁移完成后，留出时间查看最终的机器学习实体。 可以直接进行迁移，也可以进行更改，在进行了更改并查看了批处理测试结果后，你可能会因预测 JSON（更统一的 JSON）而进行更改，这样传递到客户端应用的最终信息的组织方式会不同。 这类似于代码重构，应对其使用组织已有的同一审查过程。

如果没有为 V2 模型准备好批处理测试且没有在迁移中将批处理测试迁移到 V3 模型，则将无法验证迁移对终结点预测结果的影响。

## <a name="migrating-from-v2-entities"></a>从 V2 实体迁移

当开始向 V3 创作模型迁移时，应考虑如何迁移到机器学习实体及其子实体和功能。

下表说明了需要从 V2 实体设计迁移到 V3 实体设计的实体。

|V2 创作实体类型|V3 创作实体类型|示例|
|--|--|--|
|复合实体|机器学习实体|[详细了解](#migrate-v2-composite-entity)|
|分层实体|机器学习实体的角色|[详细了解](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>迁移 V2 复合实体

V2 复合实体的每个子级都应使用 V3 机器学习实体的子实体来表示。 如果复合子级是预生成的实体、正则表达式实体或列表实体，应在子实体上将此作为必需的功能进行应用。

计划将复合实体迁移到机器学习实体时的注意事项：
* 子实体不能在模式中使用
* 子实体不再进行共享
* 如果子实体以前是非机器学习实体，需要对其进行标记

### <a name="existing-features"></a>现有功能

用于在复合实体中增强字词的任何短语列表应作为一种功能应用于机器学习（父级）实体、子实体（子级）实体或意向（如果短语列表仅应用于一个意向）。 计划将此功能添加到应最大程度增强的实体。 如果功能会最大程度地提高子实体（子级）的预测，请不要按常规方式将功能添加到机器学习（父级）实体中。

### <a name="new-features"></a>新增功能

在 V3 创作中，添加一个计划步骤，以便将实体评估为所有实体和意向的可能功能。

### <a name="example-entity"></a>示例实体

此实体只是一个示例。 你自己的实体迁移可能有其他需要注意的事项。

请考虑使用 V2 复合实体来修改使用以下内容的比萨 `order`：
* 用于传递时间的预生成的 datetimeV2
* 用于增强某些词的短语列表，如比萨、派、酥皮和配料
* 用于检测配料（如蘑菇、橄榄、意大利辣香肠）的列表实体。

此实体的一个示例言语是：

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

下表演示了迁移：

|V2 模型|V3 模型|
|--|--|
|父级 - 名为 `Order` 的组件实体|父级 - 名为 `Order` 的机器学习实体|
|子级 - 预生成的 datetimeV2|* 将预生成的实体迁移到新应用。<br>* 在父级上为预生成的 datetimeV2 添加必需功能。|
|子级 - 配料的列表实体|* 将列表实体迁移到新应用。<br>* 然后在父级上为列表实体添加必需功能。|


## <a name="migrate-v2-hierarchical-entity"></a>迁移 V2 分层实体

在 V2 创作中，在 LUIS 中存在角色之前，会提供一个分层实体。 两者的目的都是基于上下文使用情况提取实体。 如果你有分层实体，可以将其视为具有角色的简单实体。

在 V3 创作中：
* 可以在机器学习（父级）实体上应用角色。
* 角色不能应用于任何子实体。

此实体只是一个示例。 你自己的实体迁移可能有其他需要注意的事项。

请考虑使用 V2 分层实体来修改比萨 `order`：
* 其中每个子级确定是使用原始配料还是最终配料

此实体的一个示例言语是：

`Change the topping from mushrooms to olives`

下表演示了迁移：

|V2 模型|V3 模型|
|--|--|
|父级 - 名为 `Order` 的组件实体|父级 - 名为 `Order` 的机器学习实体|
|子级 - 具有原始和最终比萨配料的分层实体|* 向每个配料的 `Order` 添加角色。|

## <a name="api-change-constraint-replaced-with-required-feature"></a>将约束功能替换为必需功能的 API 更改

此更改是在 2020 年 5 月的 //Build 大会上发布和执行的，仅适用于其中的应用会使用约束功能的 v3 创作 API。 如果要从 v2 创作迁移到 v3 创作，或尚未使用 v3 约束功能，请跳过此部分。

**功能** - 能够要求现有实体需为另一个模型的功能，并且仅在检测到实体时才提取该模型。 功能未发生更改，但 API 和术语已更改。

|以前的术语|新术语|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>自动迁移

从 2020 年 6 月 19 日开始，将无法使用以前公开此功能的创作 API 以编程方式创建约束。

所有现有约束功能都将自动迁移到所需功能标志。 不需要对预测 API 进行编程更改，也不会对预测准确性的质量造成任何影响。

#### <a name="luis-portal-changes"></a>LUIS 门户更改

LUIS 预览版门户将此功能称为“约束”。 当前的 LUIS 门户将此功能指定为“必需功能”。

#### <a name="previous-authoring-api"></a>以前的创作 API

此功能应用于预览版的创作 API [创建实体子级 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)，作为实体定义的一部分，并使用实体的子级的 `instanceOf` 属性：

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

#### <a name="new-authoring-api"></a>新的创作 API

此功能现在通过[添加实体功能关系 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26) 进行应用，并使用 `featureName` 和 `isRequired` 属性。 `featureName` 属性的值是模型的名称。

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>后续步骤

* [开发人员资源](developer-reference-resource.md)
