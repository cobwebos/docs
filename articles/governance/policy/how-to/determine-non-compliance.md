---
title: 确定导致非符合性的原因
description: 有多种可能的原因会导致资源不合规。 了解如何查明导致不合规的原因。
ms.date: 04/26/2019
ms.topic: conceptual
ms.openlocfilehash: 6f113080ab84fe7d159766e6543ff61b28f4ead2
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959619"
---
# <a name="determine-causes-of-non-compliance"></a>确定导致非符合性的原因

在判定某个 Azure 资源不符合某个策略规则时，了解该资源不符合该规则的哪个部分会很有帮助。 这还有助于了解哪项更改改变了以前合规的资源，导致它现在不合规。 可通过两种方式查找此信息：

> [!div class="checklist"]
> - [合规性详细信息](#compliance-details)
> - [更改历史记录（预览版）](#change-history)

## <a name="compliance-details"></a>合规性详细信息

当某个资源不合规时，“策略合规性”页中会提供该资源的合规性详细信息。 合规性详细信息窗格包含以下信息：

- 资源详细信息，例如名称、类型、位置和资源 ID
- 上次评估当前策略分配时的合规状态和时间戳
- 资源不合规的原因列表

> [!IMPORTANT]
> 由于不合规资源的合规性详细信息显示有关该资源的属性的当前值，因此，用户必须对资源_类型_拥有**读取**操作权限。 例如，如果不合规的资源为 _Microsoft.Compute/virtualMachines_，则用户必须拥有 **Microsoft/virtualMachines/read** 操作权限。 如果用户没有所需的操作权限，将显示访问权限错误。

若要查看合规性详细信息，请执行以下步骤：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

1. 在“概述”或“合规性”页上，选择**合规性状态**为“不合规”的策略。

1. 在“策略合规性”页的“资源合规性”选项卡下，右键单击**合规性状态**为“不合规”的资源或选择其对应的省略号。 然后选择“查看合规性详细信息”。

   ![“查看合规性详细信息”选项](../media/determine-non-compliance/view-compliance-details.png)

1. “合规性详细信息”窗格将显示最近评估当前策略分配中的资源时的信息。 在此示例中，**Microsoft.Sql/servers/version** 字段值为 _12.0_，而策略定义预期该值为 _14.0_。 如果资源出于多种原因而不合规，此窗格中会列出每种原因。

   ![“合规性详细信息”窗格和不合规的原因](../media/determine-non-compliance/compliance-details-pane.png)

   对于 **auditIfNotExists** 或 **deployIfNotExists** 策略定义，详细信息包含 **details.type** 属性和所有可选属性。 有关列表，请参阅 [auditIfNotExists 属性](../concepts/effects.md#auditifnotexists-properties)和 [deployIfNotExists 属性](../concepts/effects.md#deployifnotexists-properties)。 “上次评估的资源”是定义的 **details** 节中的相关资源。

   部分 **deployIfNotExists** 定义示例：

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

   ![“合规性详细信息”窗格 - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 若要保护数据，当属性值是机密时，当前值将显示星号。

这些详细信息将解释资源当前不合规的原因，但不显示何时对该资源做出了更改，导致它不合规。 如需查看该信息，请参阅下面的[更改历史记录（预览版）](#change-history)。

### <a name="compliance-reasons"></a>合规性原因

以下矩阵将每个可能的原因映射到策略定义中的控制条件：[](../concepts/definition-structure.md#conditions)

|原因 | 条件 |
|-|-|
|当前值必须包含目标值作为键。 |containsKey，或 notContainsKey 的**求反** |
|当前值必须包含目标值。 |contains，或 notContains 的**求反** |
|当前值必须等于目标值。 |equals，或 notEquals 的**求反** |
|当前值必须小于目标值。 |less，或 greaterOrEquals 的**求反** |
|当前值必须大于或等于目标值。 |greaterOrEquals，或 less 的**求反** |
|当前值必须大于目标值。 |greater，或 lessOrEquals 的**求反** |
|当前值必须小于或等于目标值。 |lessOrEquals，或 greater 的**求反** |
|必须存在当前值。 |exists |
|当前值必须在目标值中。 |in，或 notIn 的**求反** |
|当前值必须与目标值类似。 |like，或 notLike 的**求反** |
|当前值必须与目标值匹配（区分大小写）。 |match，或 notMatch 的**求反** |
|当前值必须与目标值匹配（不区分大小写）。 |matchInsensitively，或 notMatchInsensitively 的**求反** |
|当前值不得包含目标值作为键。 |notContainsKey，或 containsKey 的**求反**|
|当前值不得包含目标值。 |notContains，或 contains 的**求反** |
|当前值不得等于目标值。 |notEquals，或 equals 的**求反** |
|不得存在当前值。 |exists 的**求反**  |
|当前值不得在目标值中。 |notIn，或 in 的**求反** |
|当前值不得与目标值类似。 |notLike，或 like 的**求反** |
|当前值不得与目标值匹配（区分大小写）。 |notMatch，或 match 的**求反** |
|当前值不得与目标值匹配（不区分大小写）。 |notMatchInsensitively，或 matchInsensitively 的**求反** |
|没有与策略定义中的效果详细信息匹配的相关资源。 |在 **then.details.type** 中定义的类型的、与策略规则的 **if** 部分中定义的资源相关的资源不存在。 |

## <a name="compliance-details-for-guest-configuration"></a>Guest Configuration 的合规性详细信息

对于 _Guest Configuration_ 类别中的 _auditIfNotExists_ 策略，可能会在 VM 中评估多个设置，而你需要查看每个设置的详细信息。 例如，如果你要审核密码策略列表，而其中只有一个策略的状态为“不合规”，则你需要知道哪个特定的密码策略不合规，以及不合规的原因。

此外，你可能无权直接登录到 VM，但需要报告 VM 为何不合规。

### <a name="azure-portal"></a>Azure 门户

首先，遵循前面部分所述的有关查看策略合规性详细信息的相同步骤。

在“合规性详细信息”窗格视图中，单击“上次评估的资源”链接。

   ![查看 auditIfNotExists 定义详细信息](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

“来宾分配”页将显示提供的所有合规性详细信息。 视图中的每一行代表在计算机中执行的一项评估。 “原因”列中会显示一条短语，描述来宾分配为何不合规。 例如，如果你要审核密码策略，则“原因”列将显示包含每项设置的当前值的文本。

![查看合规性详细信息](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

也可以从 Azure PowerShell 查看合规性详细信息。 首先，确保已安装 Guest Configuration 模块。

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

可使用以下命令查看 VM 的所有来宾分配的当前状态：

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

如果只想查看描述 VM 为何不合规的原因短语，请仅返回 Reason 子属性。

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

还可以输出来宾分配在该计算机范围内的合规性历史记录。 此命令的输出包括每份 VM 报告的详细信息。

> [!NOTE]
> 输出中可能会返回大量的数据。 建议将输出存储在变量中。

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

若要简化此视图，请使用 **ShowChanged** 参数。 此命令的输出仅包括报告，后接合规性状态的变化。

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>更改历史记录（预览版）

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

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[获取合规性数据](getting-compliance-data.md)。
- 了解如何[修正不合规的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
