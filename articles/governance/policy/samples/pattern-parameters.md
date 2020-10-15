---
title: 模式：策略定义中的参数
description: 此 Azure Policy 模式提供了如何在策略定义中使用字符串和数组参数以及如何参数化效果的示例。
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 7bb991c43a1deee39dd047aa7d814c124712fe69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649943"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy 模式：参数

可以将策略定义设置为动态，减少使用[参数](../concepts/definition-structure.md#parameters)所需的策略定义的数量。 参数在策略分配过程中定义。 参数有一组预定义的属性，这些属性描述参数及其使用方式。

## <a name="sample-1-string-parameters"></a>示例 1：字符串参数

此策略定义使用两个参数（**tagName** 和 **tagValue**）来设置策略分配在资源上查找的内容。 此格式允许将策略定义用于任意数量的标记名称和标记值组合，但只保留单个策略定义。

> [!NOTE]
> 有关使用 All 模式并与资源组配合使用的标记示例，请参阅[模式：标记 - 示例 #1](./pattern-tags.md#sample-1-parameterize-tags)。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>示例 1：说明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

在策略定义的此部分，我们将 **tagName** 参数定义为 _string_，并提供说明，方便其使用。

然后，我们在 **policyRule.if** 块中使用该参数，将策略设置为动态。 在这里，它用于定义所计算的字段，该字段是值为 **tagName** 的标记。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>示例 2：数组参数

此策略定义使用单个参数 (**listOfBandwidthinMbps**)，目的是检查 Express Route 线路资源是否已将带宽设置配置为已批准的值之一。 如果它不匹配，系统会[拒绝](../concepts/effects.md#deny)创建或更新资源。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>示例 2：说明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

在策略定义的此部分，我们将 **listOfBandwidthinMbps** 参数定义为 _array_，并提供说明，方便其使用。 作为 _array_，它有多个要匹配的值。

然后，我们在 **policyRule.if** 块中使用该参数。 作为 _array_ 参数，必须使用 _array_
[条件](../concepts/definition-structure.md#conditions)的 **in** 或 **notIn**。
在这里，我们将其用于 **serviceProvider.bandwidthInMbps** 别名，作为定义的值之一。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>示例 3：参数化效果

使策略定义可重复使用的一种常见方法是参数化效果本身。 本示例使用单个参数“effect”。 通过参数化效果，可以将相同的定义分配给具有不同效果的不同作用域。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>示例 3：说明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

在策略定义的此部分中，将“effect”参数定义为“string”。 策略定义将分配的默认值设置为“audit”，并将其他选项限制为“disabled”和“deny”。

然后在 policyRule.then 块中将参数用于“effect”。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。