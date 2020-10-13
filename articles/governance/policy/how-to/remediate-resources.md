---
title: 修正不符合资源
description: 本指南将指导你完成对 Azure Policy 中不符合策略的资源的修正。
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 76d2e57c1b5df965c81c88506ff2c2f70b2cb1f8
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876322"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>修正 Azure Policy 中的不符合资源

可以通过修正将不符合 deployIfNotExists 或 modify 策略的资源置于符合状态。 修正是通过指示 Azure Policy 在现有资源上运行所分配策略的 deployIfNotExists 效果或 Modify 操作来完成的，无论该策略是分配给管理组、订阅、资源组还是单个资源 。 本文介绍了使用 Azure Policy 了解并完成修正所需执行的步骤。

## <a name="how-remediation-security-works"></a>修正安全的工作原理

当 Azure Policy 在 deployIfNotExists 策略定义中运行模板时，它使用[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)来执行此操作。
Azure Policy 会为每个分配创建一个托管标识，但必须具有要向托管标识授予哪些角色的相关详细信息。 如果托管标识缺少角色，则会在分配策略或计划时显示错误。 使用门户时，一旦启动分配，Azure Policy 将自动授予托管标识所列的角色。 使用 SDK 时，必须手动将角色授予托管标识。 托管标识的位置不会影响它对 Azure Policy 的操作。

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="缺少对托管标识的已定义权限的 deployIfNotExists 策略的屏幕截图。" border="false":::

> [!IMPORTANT]
> 在以下方案中，必须 [手动授予](#manually-configure-the-managed-identity) 分配的托管标识，否则修正部署将失败：
>
> - 如果分配是通过 SDK 创建的
> - 如果通过 **deployIfNotExists** 或 **修改** 修改的资源超出了策略分配的范围
> - 如果模板访问策略分配范围之外的资源的属性

## <a name="configure-policy-definition"></a>配置策略定义

第一步是定义 deployIfNotExists 和 modify 在策略定义中需要的角色，以成功部署所包含模板的内容。 在“details”属性下，添加“roleDefinitionIds”属性。 此属性是与环境中的角色相匹配的一组字符串。 有关完整示例，请参阅 [deployIfNotExists 示例](../concepts/effects.md#deployifnotexists-example)或 [modify 示例](../concepts/effects.md#modify-examples)。

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

roleDefinitionIds 属性使用完整的资源标识符，并且不会使用角色的短 roleName。 若要获取环境中“参与者”角色的 ID，请使用以下代码：

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>手动配置托管标识

使用门户创建分配时，Azure Policy 会生成托管标识并向它授予 roleDefinitionIds 中定义的角色。 在以下情况下，必须手动执行步骤以创建托管标识，并向其分配权限：

- 在使用 SDK 时（如 Azure PowerShell）
- 当模板修改分配范围以外的资源
- 当模板读取分配范围以外的资源

### <a name="create-managed-identity-with-powershell"></a>使用 PowerShell 创建托管标识

若要在策略分配期间创建托管标识，必须定义 Location 并使用 AssignIdentity。 下面的示例获取内置策略“部署 SQL DB 透明数据加密”的定义，设置目标资源组，然后创建分配。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment` 变量现包含托管标识的主体 ID，以及创建策略分配时返回的标准值。 可以通过 `$assignment.Identity.PrincipalId` 访问它。

### <a name="grant-defined-roles-with-powershell"></a>使用 PowerShell 授予定义的角色

新的托管标识必须通过 Azure Active Directory 完成复制，然后才会向其授予所需的角色。 复制完成后，下面的示例将循环访问 `$policyDef` 中有关 roleDefinitionIds 的策略定义，并使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 授予新托管标识角色。

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>通过门户授予定义的角色

可以通过以下两种方法使用门户向分配的托管标识授予定义的角色：使用访问控制 (IAM)，或通过编辑策略或计划分配并选择“保存” 。

若要将角色添加到分配的托管标识，请按照下列步骤操作：

1. 在 Azure 门户中选择“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务 。

1. 选择“Azure Policy”页左侧的“分配”。

1. 找到具有托管标识的分配，并选择相应名称。

1. 在编辑页上查找“Assignment ID”属性。 分配 ID 将类似于：

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   托管标识的名称是分配资源 ID 的最后一部分，本例为 `2802056bfc094dfb95d4d7a5`。 复制分配资源 ID 的这一部分。

1. 导航到需要手动添加角色定义的资源或资源父容器（资源组、订阅、管理组）。

1. 选择资源页中的“访问控制 (IAM)”链接，然后选择访问控制页顶部的“+ 添加角色分配” 。

1. 从策略定义中选择匹配 roleDefinitionIds 的合适角色。
   将“分配访问权限至”设置保留为默认设置“Azure AD 用户、组或应用程序”。 在“选择”框中，粘贴或键入先前找到的分配资源 ID 部分。 完成搜索后，选择具有相同名称的对象来选择 ID，然后选择“保存”。

## <a name="create-a-remediation-task"></a>创建修正任务

### <a name="create-a-remediation-task-through-portal"></a>通过门户创建修正任务

在评估期间，带 deployIfNotExists 或 modify 效果的策略分配可确定是否存在不合规的资源。 当发现不符合资源时，将在“修正”页上提供详细信息。 具有不符合资源的策略列表也可以用来触发修正任务。 此选项用于基于 **deployIfNotExists** 模板或 modify 操作创建部署。

若要创建修正任务，请执行以下步骤：

1. 在 Azure 门户中选择“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务 。

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="缺少对托管标识的已定义权限的 deployIfNotExists 策略的屏幕截图。" border="false":::

1. 选择“Azure Policy”页左侧的“修正”。

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="缺少对托管标识的已定义权限的 deployIfNotExists 策略的屏幕截图。" border="false":::

1. “要修正的策略”选项卡和数据表中包含所有具有不合规的资源的 deployIfNotExists 和 modify 策略分配。 选择其中一个具有不符合资源的策略。 “新修正任务”页随即打开。

   > [!NOTE]
   > 打开“修正任务”页的另一种方法是查找并选择“合规性”页面上的策略，然后选择“创建修正任务”按钮  。

1. 在“新修正任务”页上，通过使用“范围”省略号从分配策略的位置选取子资源（包括下面的单个资源对象）来筛选要修正的资源。 此外，使用“位置”下拉列表来进一步筛选资源。 仅对表中列出的资源进行修正。

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="缺少对托管标识的已定义权限的 deployIfNotExists 策略的屏幕截图。" border="false":::

1. 筛选资源后，通过选择“修正”来启动修正任务。 “策略符合性”页将打开到“修正任务”选项卡，以显示任务进度的状态。 由修正任务创建的部署将立即开始。

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="缺少对托管标识的已定义权限的 deployIfNotExists 策略的屏幕截图。" border="false":::

1. 选择“策略合规性”页中的“修正任务”以获取有关进度的详细信息。 任务所使用的筛选与正在修正的资源列表一同显示。

1. 从“修正任务”页中，右键单击资源以查看修正任务的部署或资源。 在行末尾，选择“相关事件”以查看诸如错误消息之类的详细信息。

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="缺少对托管标识的已定义权限的 deployIfNotExists 策略的屏幕截图。" border="false":::

通过“修正任务”部署的资源将添加到“策略符合性”页上的“部署的资源”选项卡。

### <a name="create-a-remediation-task-through-azure-cli"></a>通过 Azure CLI 创建修正任务

若要使用 Azure CLI 创建修正任务，请使用 `az policy remediation` 命令。 将 `{subscriptionId}` 替换为你的订阅 ID，将 `{myAssignmentId}` 替换为你的 deployIfNotExists 或 modify 策略分配 ID。

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

有关其他修正命令和示例，请参阅 [az policy remediation](/cli/azure/policy/remediation) 命令。

### <a name="create-a-remediation-task-through-azure-powershell"></a>通过 Azure PowerShell 创建修正任务

若要使用 Azure PowerShell 创建修正任务，请使用 `Start-AzPolicyRemediation` 命令。 将 `{subscriptionId}` 替换为你的订阅 ID，将 `{myAssignmentId}` 替换为你的 deployIfNotExists 或 modify 策略分配 ID。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

有关其他修正 cmdlet 和示例，请参阅 [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) 模块。

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>在策略分配期间通过 Azure 门户创建修正任务

创建修正任务的一种简化方法是在策略分配期间通过 Azure 门户执行此操作。 如果要分配的策略定义是 deployIfNotExists 或 Modify 效果 ，“修正”选项卡上的向导会提供“创建修正任务”选项。 如果选择此选项，则会在分配策略的同时创建修正任务。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[获取符合性数据](get-compliance-data.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
