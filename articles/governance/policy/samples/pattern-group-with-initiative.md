---
title: 模式：使用计划将策略定义分组
description: 此 Azure Policy 模式通过示例介绍了如何将策略定义分组到计划中
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170245"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy 模式：将策略定义分组

一个计划是一组策略定义。 通过将相关的策略定义分组到单个对象中，可以创建本应是多个分配的单个分配。

## <a name="sample-initiative-definition"></a>示例计划定义

此计划部署两个策略定义，每个都使用 **tagName** 和 **tagValue** 参数。 计划本身有两个参数：**costCenterValue** 和 **productNameValue**。
这些计划参数分别为每个分组的策略定义提供。 此设计可最大程度地重复使用现有策略定义，同时限制在需要的情况下为实施它们而创建的分配数。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>说明

#### <a name="initiative-parameters"></a>计划参数

计划可以定义自己的参数，然后将其传递给分组的策略定义。
在此示例中，**costCenterValue** 和 **productNameValue** 都定义为计划参数。 分配计划后，会提供这些值。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>包括策略定义

如果策略定义接受参数，则每个包含的策略定义都必须提供 **policyDefinitionId** 和一个 **parameters** 数组。 在下面的代码片段中，包含的策略定义使用两个参数：**tagName** 和 **tagValue**。 **tagName** 使用文本进行定义，但 **tagValue** 使用计划定义的参数 **costCenterValue**。 对值进行这样的传递可提高重用性。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。