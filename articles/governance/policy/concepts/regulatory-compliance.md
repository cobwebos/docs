---
title: 计划定义中的法规遵从性
description: 描述如何使用计划定义按规章域（如访问控制、配置管理等）对策略进行分组。
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 15b03b5a40cba1290859b02883df70e3df54c5cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89645532"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure 策略中的合规性

Azure 策略中的规章遵从性提供内置计划定义，以根据责任 (_客户_、 _Microsoft_、_共享_) 来查看**控件**和**符合性域**的列表。
对于 Microsoft 负责的控件，我们会根据第三方证明提供审核结果的其他详细信息，并提供我们的实现细节来实现该符合性。
Microsoft 负责的控件为 `type` [静态](./definition-structure.md#type)。

> [!NOTE]
> 合规性是一项预览功能。 对于更新后的内置方案，计划控制将映射到相应的符合性标准。 现有符合性标准计划正在进行更新以支持法规符合性。

## <a name="regulatory-compliance-defined"></a>已定义合规性

法规遵从性建立在计划定义的 [分组](./initiative-definition-structure.md#policy-definition-groups) 部分之上。 在内置中，计划定义中的每个分组都定义一个名称 (**控件**) 、类别 (**符合性域**) ，并提供对具有该**控件**相关信息的[policyMetadata](./initiative-definition-structure.md#metadata-objects)对象的引用。 合规性计划定义必须将 `category` 属性设置为 "合规 **性**"。 作为其他标准计划定义，法规遵从性计划支持创建动态分配的 [参数](./initiative-definition-structure.md#parameters) 。

客户可以创建其自己的法规遵从性计划。 这些定义可以是源定义，也可以是从现有的内置定义复制的。 如果使用内置的法规遵从性计划定义作为参考，则建议在 [Azure 策略 GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)存储库中监视规章遵从性定义的来源。

若要将自定义合规性计划链接到 Azure 安全中心仪表板，请参阅 [Azure 安全中心-使用自定义安全策略](../../../security-center/custom-security-policies.md)。

## <a name="regulatory-compliance-in-portal"></a>门户中的合规性

当使用 [组](./initiative-definition-structure.md#policy-definition-groups)创建计划定义时，门户中针对该计划的 **符合性** 详细信息页提供了其他信息。 

新选项卡上，将 **控件** 添加到页面。 筛选功能按 **符合性域** 提供，策略定义按 `title` **policyMetadata** 对象中的字段进行分组。 每一行都表示一个控件，该 **控件** 显示其符合性状态、它所属的 **符合性域** 、责任信息以及组成该 **控制**的不符合和合规性策略定义的数量。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="用于显示符合和不符合性控件的 NIST SP 800-53 R4 内置定义的规章遵从性概述的屏幕截图。&quot;:::

选择 **控件** 将打开有关该控件的详细信息页。 **概述**包含来自和的信息 `description` `requirements` 。 &quot; **策略** &quot; 选项卡下是参与此 **控制**的计划中的各个策略定义。 &quot; **资源符合性** " 选项卡提供按当前查看的 **控件**的成员策略计算的每个资源的粒度视图。

> [!NOTE]
> **Microsoft 托管**的评估类型适用于[静态](./definition-structure.md#type)策略定义 `type` 。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="用于显示符合和不符合性控件的 NIST SP 800-53 R4 内置定义的规章遵从性概述的屏幕截图。&quot;:::

选择 **控件** 将打开有关该控件的详细信息页。 **概述**包含来自和的信息 `description` `requirements` 。 &quot; **策略** &quot; 选项卡下是参与此 **控制**的计划中的各个策略定义。 &quot; **资源符合性** " 选项卡将显示此 **控件**的策略定义所包含的所有资源。 筛选器可用于名称或 ID、符合性状态、资源类型和位置。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="用于显示符合和不符合性控件的 NIST SP 800-53 R4 内置定义的规章遵从性概述的屏幕截图。&quot;:::

选择 **控件** 将打开有关该控件的详细信息页。 **概述**包含来自和的信息 `description` `requirements` 。 &quot; **策略** &quot; 选项卡下是参与此 **控制**的计划中的各个策略定义。 &quot; **资源符合性** ":::

## <a name="regulatory-compliance-in-sdk"></a>SDK 中的法规遵从性

如果对计划定义启用了法规遵从性，则评估扫描记录、事件和策略状态 SDK 都将返回其他属性。 这些附加属性按符合性状态分组，并提供有关每个状态中的组数的信息。

下面的代码是调用中添加的结果的示例 `summarize` ：

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>后续步骤

- 请参阅[计划定义结构](./initiative-definition-structure.md)
- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看[了解策略效果](./effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
