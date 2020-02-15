---
title: 模式：策略定义中的 value 运算符
description: 此 Azure Policy 模式通过示例介绍了如何在策略定义中使用 value 运算符。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170145"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy 模式：value 运算符

[value](../concepts/definition-structure.md#value) 运算符会对[参数](../concepts/definition-structure.md#parameters)、[支持的模板函数](../concepts/definition-structure.md#policy-functions)或文本进行评估，针对为给定[条件](../concepts/definition-structure.md#conditions)提供的值。

> [!WARNING]
> 如果模板函数  的结果是一个错误，则策略评估会失败。 评估失败是一种隐式**拒绝**。 有关详细信息，请参阅[避免模板失败](../concepts/definition-structure.md#avoiding-template-failures)。

## <a name="sample-policy-definition"></a>示例策略定义

此策略定义添加或替换在资源上的参数 **tagName**（字符串  ）中指定的标记，并从资源所在的资源组继承 **tagName** 的值。 创建或更新资源时，会进行此评估。 充当 [modify](../concepts/effects.md#modify) 效果的修正可以通过[修正任务](../how-to/remediate-resources.md)在现有资源上运行。

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>说明

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

**value** 运算符用在**属性**的 **policyRule.if** 块中。 在此示例中，[逻辑运算符](../concepts/definition-structure.md#logical-operators) **allOf** 用于说明这两个条件语句都必须为 true 才能产生 **modify** 效果。

**value** 会对模板函数 [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) 的结果进行评估，其条件是结果 **notEquals** 空值。 如果在父资源组的 **tagName** 中提供的标记名称存在，则条件的评估结果为 true。

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。