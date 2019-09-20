---
title: 确定导致非符合性的原因
description: 如果资源不符合，则可能有很多原因。 了解如何查明导致不符合的原因。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: a0faaeee369a2227f6018141e5aa5d18c9037e9d
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161983"
---
# <a name="determine-causes-of-non-compliance"></a>确定导致非符合性的原因

将 Azure 资源确定为不符合策略规则时，了解资源不符合哪个规则部分会很有帮助。 这也有助于了解哪些更改更改了以前符合的资源，使其不符合。 可以通过两种方法来查找此信息：

> [!div class="checklist"]
> - [符合性详细信息](#compliance-details)
> - [更改历史记录（预览）](#change-history)

## <a name="compliance-details"></a>符合性详细信息

当某个资源不符合时，该资源的符合性详细信息将在 "**策略符合性**" 页中提供。 "符合性详细信息" 窗格包含以下信息：

- 资源详细信息，如名称、类型、位置和资源 ID
- 当前策略分配的最后一个评估的符合性状态和时间戳
- 资源不合规的_原因_列表

> [!IMPORTANT]
> 由于_不符合_资源的符合性详细信息显示该资源的属性的当前值，因此用户必须对资源**类型**具有**读取**操作。 例如，如果_不符合_的资源是 virtualMachines/ ，则用户必须具有**virtualMachines/read**操作。 如果用户没有所需的操作，则会显示访问错误。

若要查看符合性详细信息，请执行以下步骤：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

1. 在 "**概述**" 或 "**符合性**" 页上，选择**符合性状态**中不_符合要求_的策略。

1. 在 "**策略符合性**" 页的 "**资源符合性**" 选项卡下，右键单击或选择**符合性状态**为 "不_符合_" 的资源的省略号。 然后选择 "**查看符合性详细信息**"。

   ![查看符合性详细信息选项](../media/determine-non-compliance/view-compliance-details.png)

1. "**符合性详细信息**" 窗格显示从最新的资源评估到当前策略分配的信息。 在此示例中，在策略定义应为_14.0_时，12.0**字段的**字段为 。 如果资源出于多种原因不符合，则每个原因都列在此窗格中。

   ![符合性详细信息窗格和不符合性的原因](../media/determine-non-compliance/compliance-details-pane.png)

   对于**auditIfNotExists**或**deployIfNotExists**策略定义，详细信息包括**详细信息。类型**属性和任何可选属性。 有关列表，请参阅[auditIfNotExists 属性](../concepts/effects.md#auditifnotexists-properties)和[deployIfNotExists 属性](../concepts/effects.md#deployifnotexists-properties)。 **上次计算的资源**是来自定义的**详细信息**部分的相关资源。

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

   ![符合性详细信息窗格-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 为了保护数据，当属性值为_机密_时，当前值显示星号。

这些详细信息解释资源当前不符合的原因，但不显示对资源所做的更改导致不符合的情况。 有关此信息，请参阅下面的[更改历史记录（预览版）](#change-history) 。

### <a name="compliance-reasons"></a>合规性原因

下表将每个可能的_原因_映射到策略定义中的负责人[条件](../concepts/definition-structure.md#conditions)：

|Reason | 条件 |
|-|-|
|当前值必须包含作为键的目标值。 |containsKey 或**not** notContainsKey |
|当前值必须包含目标值。 |contains 或**not** notContains |
|当前值必须等于目标值。 |等于或**不**notEquals |
|当前值必须小于目标值。 |小于或**不**greaterOrEquals |
|当前值必须大于或等于目标值。 |greaterOrEquals 或**不**小于 |
|当前值必须大于目标值。 |大于或**不**lessOrEquals |
|当前值必须小于或等于目标值。 |lessOrEquals 或**不**更高 |
|当前值必须存在。 |存在 |
|当前值必须在目标值中。 |in 或**not** notIn |
|当前值必须与目标值相同。 |like 或**not** notLike |
|当前值必须与目标值区分大小写。 |match 或**not** notMatch |
|当前值必须不区分大小写匹配目标值。 |matchInsensitively 或**not** notMatchInsensitively |
|当前值不能包含作为键的目标值。 |notContainsKey 或**not** containsKey|
|当前值不能包含目标值。 |notContains 或**不**包含 |
|当前值不得等于目标值。 |notEquals 或**不**等于 |
|当前值不能存在。 |**不**存在  |
|当前值不得在目标值中。 |notIn |
|当前值不得与目标值相同。 |notLike 或**不**喜欢 |
|当前值不得与目标值区分大小写。 |notMatch 或**不**匹配 |
|当前值不能不区分大小写匹配目标值。 |notMatchInsensitively 或**not** matchInsensitively |
|没有与策略定义中的效果详细信息匹配的相关资源。 |在中定义的类型的资源 **。** 在策略规则的**if**部分中定义的资源的类型和相关信息不存在。 |

## <a name="compliance-details-for-guest-configuration"></a>来宾配置的符合性详细信息

对于 "_来宾配置_" 类别中的_auditIfNotExists_策略，可以在 VM 内评估多个设置，并且需要查看每个设置的详细信息。 例如，如果你正在审核密码策略的列表，并且只有其中一个状态为 "_不符合_"，则你需要知道哪些特定的密码策略不符合要求以及原因。

你还可能无权直接登录到 VM，但需要报告 VM 为何不_符合_的原因。

### <a name="azure-portal"></a>Azure 门户

首先遵循上述部分中的相同步骤查看策略符合性详细信息。

在 "**符合性详细信息**" 窗格视图中，单击链接**上一次计算的资源**。

   ![查看 auditIfNotExists 定义详细信息](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

"**来宾分配**" 页显示所有可用的符合性详细信息。 视图中的每一行都表示在计算机中执行的计算。 在 "**原因**" 列中，会显示描述 "来宾分配不_符合_" 的原因的短语。 例如，如果你要审核密码策略，"**原因**" 列将显示包含每个设置的当前值的文本。

![查看符合性详细信息。](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

你还可以从 Azure PowerShell 查看符合性详细信息。 首先，请确保已安装来宾配置模块。

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

可以使用以下命令查看 VM 的所有来宾分配的当前状态：

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

若要仅查看描述 VM 为何不_合规_的_原因_短语，只返回原因子属性。

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

你还可以在计算机范围内输出来宾分配的符合性历史记录。 此命令的输出包含 VM 的每个报表的详细信息。

> [!NOTE]
> 输出可能会返回大量的数据。 建议将输出存储在变量中。

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

若要简化此视图，请使用**ShowChanged**参数。 此命令的输出仅包括遵循符合性状态更改的报告。

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>更改历史记录（预览）

作为新**公共预览版**的一部分，最后14天的更改历史记录适用于支持[完整模式删除](../../../azure-resource-manager/complete-mode-deletion.md)的所有 Azure 资源。 更改历史记录提供有关何时检测到更改的详细信息，以及每个更改的_视觉差异_。 添加、删除或更改资源管理器属性时，将触发更改检测。

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

1. 在 "**概述**" 或 "**符合性**" 页上，选择任何**符合性状态**的策略。

1. 在 "**策略符合性**" 页的 "**资源符合性**" 选项卡下，选择一个资源。

1. 选择“资源符合性”页上的“更改历史记录(预览版)”选项卡。 此时会显示检测到的更改的列表（如果存在）。

   ![资源符合性页上的 Azure 策略更改历史记录选项卡](../media/determine-non-compliance/change-history-tab.png)

1. 选择其中一个检测到的更改。 "**更改历史记录**" 页上显示了资源的_可视化差异_。

   !["更改历史记录" 页上的 Azure 策略更改历史记录视觉差异](../media/determine-non-compliance/change-history-visual-diff.png)

_视觉差异_可帮助识别资源的更改。 检测到的更改可能与资源的当前符合性状态不相关。

更改历史记录数据由[Azure 资源 Graph](../../resource-graph/overview.md)提供。 若要在 Azure 门户之外查询此信息，请参阅[获取资源更改](../../resource-graph/how-to/get-resource-changes.md)。

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何以[编程方式创建策略](programmatically-create.md)。
- 了解如何[获取相容性数据](getting-compliance-data.md)。
- 了解如何[修正不合规的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
