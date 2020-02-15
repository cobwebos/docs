---
title: 模式：策略定义的效果
description: 此 Azure Policy 模式通过示例介绍了如何使用策略定义的不同效果。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: b86a24bc0af6c9bdd7b29bb0a931d6c78865218b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170455"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy 模式：效果

Azure Policy 有很多[效果](../concepts/effects.md)，这些效果决定了服务对不合规资源的反应。 某些效果很简单，并且在策略定义中无需其他属性，而另一些效果则需要多个属性。

## <a name="sample-1-simple-effect"></a>示例 1：简单效果

此策略定义检查评估的资源上是否存在参数 **tagName** 中定义的标记。 如果标记尚不存在，则会触发 [modify](../concepts/effects.md#modify) 效果，添加一个使用参数 **tagValue** 中的值的标记。

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>示例 1：说明

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="30-34":::

**modify** 效果需要 **policyRule.then.details** 块，该块定义 **roleDefinitionIds** 和 **operations**。 这些参数会告知 Azure Policy 需要哪些角色才能添加标记和修正资源，以及要执行哪项 **modify** 操作。 在此示例中，**operation** 为 _add_，参数用于设置标记和值。

## <a name="sample-2-complex-effect"></a>示例 2：复杂效果

此策略定义会审核每个虚拟机中是否存在参数 **publisher** 和 **type** 中定义的扩展不存在的情况。 它使用 [auditIfNotExists](../concepts/effects.md#auditifnotexists) 检查与虚拟机相关的资源，看是否存在与定义的参数匹配的实例。 此示例检查“扩展”  类型。

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>示例 2：说明

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

**auditIfNotExists** 效果需要 **policyRule.then.details** 块来定义要查找的 **type** 和 **existenceCondition**。 **existenceCondition** 使用策略语言元素（如[逻辑运算符](../concepts/definition-structure.md#logical-operators)）来确定是否存在匹配的相关资源。 在此示例中，对照每个[别名](../concepts/definition-structure.md#aliases)检查的值在参数中定义。

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。