---
title: 模式：策略定义中的 count 运算符
description: 此 Azure Policy 模式提供了有关如何在策略定义中使用 count 运算符的示例。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170375"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy 模式：count 运算符

[count](../concepts/definition-structure.md#count) 运算符评估 \[\*\] 别名的成员。

## <a name="sample-policy-definition"></a>示例策略定义

此策略定义[审核](../concepts/effects.md#audit)配置为允许入站远程桌面协议 (RDP) 流量的网络安全组。

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>说明

**count** 运算符的核心组件是 _field_、_where_ 和条件。 下面的代码片段中突出显示了每个组件。

- _field_ 告诉 count 要评估哪个[别名](../concepts/definition-structure.md#aliases)的成员。 下面，让我们看一下网络安全组的 **securityRules\[\*\]** 别名数组  。
- _where_ 使用策略语言来定义哪些数组  成员满足条件。 在此示例中，**allOf** 逻辑运算符将别名数组  属性的以下三个不同条件评估分组到一起：_direction_、_access_ 和 _destinationPortRange_。
- 此示例中的 count 条件为 **greater**。 当别名数组  的一个或多个成员与 _where_ 子句匹配时，Count 的评估结果为 true。

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。