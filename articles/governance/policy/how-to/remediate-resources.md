---
title: 修正不符合资源
description: 本操作说明将指导你完成修正 Azure Policy 中不符合策略的资源的过程。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 054ce3d3483c3515e89c36eafc5d9a771e8e608d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844137"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>修正 Azure Policy 中的不符合资源

不符合 deployIfNotExists 策略的资源可以通过修正置于符合状态。 可以通过指示 Policy 在现有资源上运行已分配策略的 deployIfNotExists 影响来完成修正。 本文介绍了使用 Policy 了解并完成修正需要执行的步骤。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="how-remediation-security-works"></a>修正安全的工作原理

当 Policy 在 deployIfNotExists 策略定义中运行模板时，它使用[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)来执行此操作。
Policy 为每个分配创建一个托管标识，但是必须向它提供有关哪些角色授予托管标识的详细信息。 如果托管标识缺少角色，则在分配策略或计划期间会显示此错误。 使用门户时，一旦启动分配，Policy 将自动授予托管标识所列的角色。

![托管标识 - 缺少角色](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> 如果通过 deployIfNotExists 修改的资源在策略分配范围之外，或者模板访问策略分配范围之外的资源上的属性，则分配的托管标识必须是[手动授予的访问权限](#manually-configure-the-managed-identity)，否则修正部署将失败。

## <a name="configure-policy-definition"></a>配置策略定义

第一步是定义 deployIfNotExists 在策略定义中需要的角色，以成功部署所包含模板的内容。 在“details”属性下，添加“roleDefinitionIds”属性。 此属性是与环境中的角色相匹配的一组字符串。 有关完整示例，请参阅 [deployIfNotExists 示例](../concepts/effects.md#deployifnotexists-example)。

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

roleDefinitionIds 使用完整的资源标识符，并且不会使用角色的短 roleName。 若要获取环境中“参与者”角色的 ID，请使用以下代码：

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>手动配置托管标识

使用门户创建分配时，Policy 会生成托管标识并向它授予 roleDefinitionIds 中定义的角色。 在以下情况下，必须手动执行步骤以创建托管标识，并向其分配权限：

- 在使用 SDK 时（如 Azure PowerShell）
- 当模板修改分配范围以外的资源
- 当模板读取分配范围以外的资源

> [!NOTE]
> Azure PowerShell 和 .NET 是当前支持此功能的唯一 SDK。

### <a name="create-managed-identity-with-powershell"></a>使用 PowerShell 创建托管标识

若要在策略分配期间创建托管标识，必须定义 Location 并使用 AssignIdentity。 下面的示例获取内置策略“部署 SQL DB 透明数据加密”的定义，设置目标资源组，然后创建分配。

```azurepowershell-interactive
# Login first with Connect-Azccount if not using Cloud Shell

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

可以通过以下两种方法使用门户向分配的托管标识授予定义的角色：使用访问控制 (IAM)，或通过编辑策略或计划分配并单击“保存”。

若要将角色添加到分配的托管标识，请按照下列步骤操作：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

1. 选择“Azure Policy”页左侧的“分配”。

1. 找到具有托管标识的分配，并单击名称。

1. 在编辑页上查找“Assignment ID”属性。 分配 ID 将类似于：

   ```
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   托管标识的名称是分配资源 ID 的最后一部分，本例为 `2802056bfc094dfb95d4d7a5`。 复制分配资源 ID 的这一部分。

1. 导航到需要手动添加角色定义的资源或资源父容器（资源组、订阅、管理组）。

1. 单击资源页中的“访问控制 (IAM)”链接，然后单击访问控制页顶部的“+ 添加角色分配”。

1. 从策略定义中选择匹配 roleDefinitionIds 的合适角色。 将“分配访问权限至”设置保留为默认设置“Azure AD 用户、组或应用程序”。 在“选择”框中，粘贴或键入先前找到的分配资源 ID 部分。 完成搜索后，单击具有相同名称的对象来选择 ID，然后单击“保存”。

## <a name="create-a-remediation-task"></a>创建修正任务

在评估期间，带 deployIfNotExists 效果的策略分配确定是否存在不符合资源。 当发现不符合资源时，将在“修正”页上提供详细信息。 具有不符合资源的策略列表也可以用来触发修正任务。 此选项用于基于 **deployIfNotExists** 模板创建部署。

若要创建修正任务，请执行以下步骤：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

   ![搜索策略](../media/remediate-resources/search-policy.png)

1. 选择“Azure Policy”页左侧的“修正”。

   ![选择修正](../media/remediate-resources/select-remediation.png)

1. 所有带不符合资源的 deployIfNotExists 策略分配均包含在“要修正的策略”选项卡和一个数据表上。 单击其中一个具有不符合资源的策略。 “新修正任务”页随即打开。

   > [!NOTE]
   > 打开“修正任务”页的另一种方法是查找并单击“符合性”页上的策略，然后单击“创建修正任务”按钮。

1. 在“新修正任务”页上，通过使用“范围”省略号从分配策略的位置选取子资源（包括下面的单个资源对象）来筛选要修正的资源。 此外，使用“位置”下拉列表来进一步筛选资源。 仅对表中列出的资源进行修正。

   ![修正 - 选择资源](../media/remediate-resources/select-resources.png)

1. 筛选资源后，通过单击“修正”来启动修正任务。 “策略符合性”页将打开到“修正任务”选项卡，以显示任务进度的状态。

   ![修正 - 任务进度](../media/remediate-resources/task-progress.png)

1. 单击“策略符合性”页中的“修正任务”以获取有关进度的详细信息。 任务所使用的筛选与正在修正的资源列表一同显示。

1. 从“修正任务”页中，右键单击资源以查看修正任务的部署或资源。 在行末尾，单击“相关事件”以查看诸如错误消息之类的详细信息。

   ![修正 - 资源任务上下文菜单](../media/remediate-resources/resource-task-context-menu.png)

通过“修正任务”部署的资源将添加到“策略符合性”页上的“部署的资源”选项卡。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例
- 查看[策略定义结构](../concepts/definition-structure.md)
- 查看[了解策略效果](../concepts/effects.md)
- 了解如何[以编程方式创建策略](programmatically-create.md)
- 了解如何[获取符合性数据](getting-compliance-data.md)
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组