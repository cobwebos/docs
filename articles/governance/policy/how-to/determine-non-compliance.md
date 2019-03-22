---
title: 确定导致非符合性的原因
description: 不符合资源时，有许多可能的原因。 了解如何找出不符合的原因。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: dba8d9413229a0fa236b082e2e11dbd1a9fe5a5f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314120"
---
# <a name="determine-causes-of-non-compliance"></a>确定导致非符合性的原因

一种 Azure 资源被确定为不符合策略规则，时，有助于您了解该资源不符合规则的哪个部分。 它是还有助于了解有何变化更改以前符合的资源，以使其不符合标准。 有两种方法来查找此信息：

> [!div class="checklist"]
> - [符合性详细信息](#compliance-details)
> - [更改历史记录 （预览版）](#change-history-preview)

## <a name="compliance-details"></a>符合性详细信息

该资源的符合性详细信息中不符合资源时，有**策略符合性**页。 符合性详细信息窗格中包括以下信息：

- 资源详细信息，例如名称、 类型、 位置和资源 ID
- 符合性状态和最后一个评估当前的策略分配的时间戳
- 一系列_原因_资源不合规

> [!IMPORTANT]
> 作为符合性的详细信息_不符合_资源显示属性的当前值对该资源，则用户必须拥有**读取**操作**类型**的资源。 例如，如果_不符合_资源是**microsoft.compute/virtualmachines**则用户必须具有**Microsoft.Compute/virtualMachines/read**操作。 如果用户不具备所需的操作，将显示出现访问错误。

若要查看符合性详细信息，请执行以下步骤：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

1. 上**概述**或**符合性**页上，选择某个策略中的**符合性状态**即_不符合_。

1. 下**资源符合性**选项卡**策略符合性**页上，右键单击或选择中的资源的省略号**符合性状态**即_不符合_。 然后选择**查看符合性详细信息**。

   ![查看符合性详细信息选项](../media/determine-non-compliance/view-compliance-details.png)

1. **符合性详细信息**窗格将显示当前的策略分配到最新的计算资源的信息。 在此示例中，字段**Microsoft.Sql/servers/version**发现_12.0_时预期的策略定义_14.0_。 如果资源是出于多种原因不符合标准，每个将列在此窗格。

   ![符合性详细信息窗格](../media/determine-non-compliance/compliance-details-pane.png)

   有关**auditIfNotExists**或**deployIfNotExists**策略定义的详细信息包括**details.type**属性和任何可选属性。 有关列表，请参阅[auditIfNotExists 属性](../concepts/effects.md#auditifnotexists-properties)并[deployIfNotExists 属性](../concepts/effects.md#deployifnotexists-properties)。 **上次评估资源**是一种相关的资源从**详细信息**定义部分。

   示例部分**deployIfNotExists**定义：

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![符合性详细信息窗格中的 * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 若要保护的数据，属性值时_机密_的当前值显示星号。

这些详细信息说明为什么资源是当前不符合，但不显示到导致其变得不合规的资源时进行了更改。 该信息，请参阅[更改历史记录 （预览版）](#change-history-preview)下面。

### <a name="compliance-reasons"></a>合规性原因

以下矩阵将每个可能的映射_原因_到负责[条件](../concepts/definition-structure.md#conditions)策略定义中：

|原因 | 条件 |
|-|-|
|当前值必须包含目标值作为键。 |containsKey 或**不**notContainsKey |
|当前值必须包含目标值。 |包含或**不**notContains |
|当前值必须等于目标值。 |等于或**不**notEquals |
|必须存在当前值。 |exists |
|当前值必须在目标值的范围内。 |在中或**不**notIn |
|当前值必须与目标值类似。 |如或**不**notLike |
|当前值必须与目标值匹配(区分大小写)。 |匹配或**不**notMatch |
|当前值必须与目标值匹配(不区分大小写)。 |matchInsensitively 或**不**notMatchInsensitively |
|当前值不得包含目标值作为键。 |notContainsKey 或**不**containsKey|
|当前值不得包含目标值。 |notContains 或**不**包含 |
|当前值不得等于目标值。 |notEquals 或**不**等于 |
|不得存在当前值。 |**不**存在  |
|当前值不得在目标值的范围内。 |notIn 或**不**中 |
|当前值不得与目标值类似。 |notLike 或**不**等 |
|当前值不得与目标值匹配(区分大小写)。 |notMatch 或**不**匹配 |
|当前值不得与目标值匹配(不区分大小写)。 |notMatchInsensitively 或**不**matchInsensitively |
|没有与策略定义中的效果详细信息匹配的相关资源。 |中定义的类型的资源**then.details.type**中定义的资源相关**如果**策略规则的部分不存在。 |

## <a name="change-history-preview"></a>更改历史记录（预览版）

作为一个新的一部分**公共预览版**、 过去 14 天的更改历史记录是适用于所有支持的 Azure 资源[完成模式删除](../../../azure-resource-manager/complete-mode-deletion.md)。 更改历史记录提供有关何时检测到更改的详细信息，以及每个更改的_视觉差异_。 添加、 移除或更改资源管理器属性时，会触发更改检测。

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

1. 上**概述**或**符合性**页上，选择一个策略中任何**符合性状态**。

1. 下**资源符合性**选项卡**策略符合性**页上，选择一个资源。

1. 选择“资源符合性”页上的“更改历史记录(预览版)”选项卡。 此时会显示检测到的更改的列表（如果存在）。

   ![策略更改历史记录 - 选项卡](../media/determine-non-compliance/change-history-tab.png)

1. 选择其中一个检测到的更改。 _Visual diff_的资源显示在**更改历史记录**页。

   ![策略更改历史记录 - 视觉差异](../media/determine-non-compliance/change-history-visual-diff.png)

_视觉差异_可帮助识别资源的更改。 检测到的更改不可能与该资源的当前符合性状态。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例
- 查看[策略定义结构](../concepts/definition-structure.md)
- 查看[了解策略效果](../concepts/effects.md)
- 了解如何[以编程方式创建策略](programmatically-create.md)
- 了解如何[获取符合性数据](getting-compliance-data.md)
- 了解如何[修正不符合的资源](remediate-resources.md)
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组