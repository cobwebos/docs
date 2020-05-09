---
title: 如何使用管理组 - Azure 治理
description: 了解如何查看、维护、更新和删除管理组层次结构。
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 423d1837c3d5710e24abb94f5411200319e8a8aa
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "81381678"
---
# <a name="manage-your-resources-with-management-groups"></a>使用管理组管理资源

如果你的组织有多个订阅，则可能需要一种方法来高效地管理这些订阅的访问权限、策略和符合性。 Azure 管理组提供订阅上的作用域级别。 可将订阅组织到名为“管理组”的容器中，并将管理条件应用到管理组。 管理组中的所有订阅都将自动继承应用于管理组的条件。

不管使用什么类型的订阅，管理组都能提供大规模的企业级管理。 有关管理组的详细信息，请参阅[使用 Azure 管理组整理资源](./overview.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Azure 资源管理器用户令牌和管理组缓存持续30分钟后才会被强制刷新。 执行任何操作（如移动管理组或订阅）后，最多可能需要30分钟才能显示。 若要更快地查看更新，需要通过刷新浏览器、登录和注销或请求新令牌来更新令牌。  

## <a name="change-the-name-of-a-management-group"></a>更改管理组的名称

可以使用门户、PowerShell 或 Azure CLI 更改管理组的名称。

### <a name="change-the-name-in-the-portal"></a>在门户中更改名称

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 "**所有服务** > **管理组**"。

1. 选择要重命名的管理组。

1. 选择 "**详细信息**"。

1. 选择页面顶部的“重命名组”选项。****

   :::image type="content" source="./media/detail_action_small.png" alt-text="“管理组”页面上的“重命名组”选项" border="false":::

1. 菜单打开后，请输入要显示的新名称。

   :::image type="content" source="./media/rename_context.png" alt-text="用于重命名管理组的“重命名组”窗格" border="false":::

1. 选择“保存”  。

### <a name="change-the-name-in-powershell"></a>在 PowerShell 中更改名称

若要更新显示名称，请使用 **Update-AzManagementGroup**。 例如，若要将管理组的显示名称从“Contoso IT”更改为“Contoso Group”，请运行以下命令：

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>在 Azure CLI 中更改名称

在 Azure CLI 中使用 update 命令。

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>删除管理组

若要删除某个管理组，必须满足以下要求：

1. 该管理组下面没有任何子管理组或订阅。

   - 若要将订阅或管理组移动到其他管理组，请参阅[在层次结构中移动管理组和订阅](#moving-management-groups-and-subscriptions)。

1. 需要对管理组（"所有者"、"参与者" 或 "管理组参与者"）具有写入权限。 若要查看自己拥有哪些权限，请选择管理组，然后选择“IAM”。**** 若要了解有关 RBAC 角色的详细信息，请参阅  
   [使用 RBAC 管理访问权限和权限](../../role-based-access-control/overview.md)。

### <a name="delete-in-the-portal"></a>在门户中删除

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 "**所有服务** > **管理组**"。

1. 选择要删除的管理组。

1. 选择 "**详细信息**"。

1. 选择**删除**

   :::image type="content" source="./media/delete.png" alt-text="“删除组”选项" border="false":::

   > [!TIP]
   > 如果该图标已禁用，将鼠标指针悬停在该图标上可显示原因。

1. 此时会打开一个窗口，让你确认是否要删除该管理组。

   :::image type="content" source="./media/delete_confirm.png" alt-text="“确认删除组”窗口" border="false":::

1. 选择 **“是”** 。

### <a name="delete-in-powershell"></a>在 PowerShell 中删除

在 PowerShell 中使用 **Remove-AzManagementGroup** 命令删除管理组。

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>在 Azure CLI 中删除

在 Azure CLI 中，可以使用 az account management-group delete 命令。

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>查看管理组

可以查看你对其拥有直接管理角色或继承 RBAC 角色的任何管理组。  

### <a name="view-in-the-portal"></a>在门户中查看

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 "**所有服务** > **管理组**"。

1. 此时会加载管理组层次结构页。 可以在该页中浏览你有权访问的所有管理组和订阅。 选择组名会将你带到层次结构中的下一级别。 导航的工作方式与文件资源管理器一样。

1. 若要查看管理组的详细信息，请选择管理组标题旁边的“(详细信息)”**** 链接。 如果此链接不可用，则表示你无权查看该管理组。

   :::image type="content" source="./media/main.png" alt-text="主要" border="false":::

### <a name="view-in-powershell"></a>在 PowerShell 中查看

使用 Get-AzManagementGroup 命令检索所有组。 有关管理组的完整列表，请参阅[Az](/powershell/module/az.resources/Get-AzManagementGroup)模块获取 PowerShell 命令。  

```azurepowershell-interactive
Get-AzManagementGroup
```

若要查看单个管理组的信息，请使用 -GroupName 参数

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

若要返回特定的管理组以及其下所有级别的层次结构，请使用 **-Expand** 和 **-Recurse** 参数。  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>在 Azure CLI 中查看

使用 list 命令可以检索所有组。  

```azurecli-interactive
az account management-group list
```

若要查看单个管理组的信息，请使用 show 命令

```azurecli-interactive
az account management-group show --name 'Contoso'
```

若要返回特定的管理组以及其下所有级别的层次结构，请使用 **-Expand** 和 **-Recurse** 参数。

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>移动管理组和订阅   

创建管理组的原因之一是将订阅捆绑在一起。 只能将管理组和订阅设置为另一个管理组的子级。 移动到管理组的订阅继承父管理组中的所有用户访问权限和策略

将管理组或订阅移动到另一个管理组的子级时，需要将三个规则评估为 true。

如果执行移动操作，你需要： 

- 在子订阅或管理组上的管理组写入权限和角色分配写入权限。
  - 内置角色示例：**所有者**
- 目标父管理组中的管理组写入访问权限。
  - 内置角色示例：**所有者**、**参与者**、**管理组参与者**
- 现有父管理组中的管理组写入访问权限。
  - 内置角色示例：**所有者**、**参与者**、**管理组参与者**

**例外**：如果目标或现有父管理组不是根管理组，则权限要求不适用。 由于根管理组是所有新管理组和订阅的默认登陆点，因此不需在其上具有相关权限即可移动某个项。

如果订阅上的“所有者”角色继承自当前管理组，你的移动目标会受限。 只能将订阅移到你在其中拥有“所有者”角色的另一管理组。 不能将它移到你在其中是参与者的管理组，因为你会失去订阅的所有权。 如果你直接分配给订阅的所有者角色（不是从管理组继承），则可以将其移动到你作为参与者的任何管理组。

若要在 Azure 门户中查看自己拥有哪些权限，请选择管理组，然后选择“标识和访问管理”。**** 有关 RBAC 角色的详细信息，请参阅[使用 RBAC 管理访问权限和权限](../../role-based-access-control/overview.md)。

## <a name="move-subscriptions"></a>移动订阅 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>将现有订阅添加到门户中的管理组

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 "**所有服务** > **管理组**"。

1. 选择要设为父级的管理组。

1. 在页面顶部，选择“添加订阅”****。

1. 在列表中选择具有正确 ID 的订阅。

   :::image type="content" source="./media/add_context_sub.png" alt-text="可以添加到管理组的订阅" border="false":::

1. 选择“保存”。

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>在门户中从管理组中删除订阅

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 "**所有服务** > **管理组**"。

1. 选择要设为当前父级的管理组。  

1. 在列表中，选择要移动的订阅所在行末尾的椭圆。

   :::image type="content" source="./media/move_small.png" alt-text="管理组上的“移动”选项" border="false":::

1. 选择“移动”****。

1. 在打开的菜单中，选择“父管理组”。****

   :::image type="content" source="./media/move_small_context.png" alt-text="用于更改父组的“移动”窗格" border="false":::

1. 选择“保存”  。

### <a name="move-subscriptions-in-powershell"></a>在 PowerShell 中移动订阅

若要在 PowerShell 中移动订阅，请使用 New-AzManagementGroupSubscription 命令。  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

若要删除订阅与管理组之间的链接，请使用 Remove-AzManagementGroupSubscription 命令。

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>在 Azure CLI 中移动订阅

若要在 CLI 中移动订阅，请使用 add 命令。

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

若要从管理组中删除订阅，请使用 subscription remove 命令。  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>移动管理组 

### <a name="move-management-groups-in-the-portal"></a>在门户中移动管理组

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 "**所有服务** > **管理组**"。

1. 选择要设为父级的管理组。

1. 在页面顶部，选择“添加管理组”****。

1. 在打开的菜单中，选择要使用新管理组或现有管理组。

   - 选择新管理组将创建一个新管理组。
   - 选择现有管理组将显示所有管理组的下拉列表，这些管理组可移动到此管理组。  

   :::image type="content" source="./media/add_context_MG.png" alt-text="将管理组移动到新的或现有组" border="false":::

1. 选择“保存”  。

### <a name="move-management-groups-in-powershell"></a>在 PowerShell 中移动管理组

在 PowerShell 中使用 Update-AzManagementGroup 命令将管理组移到不同的组下面。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>在 Azure CLI 中移动管理组

在 Azure CLI 中使用 update 命令移动管理组。

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>使用活动日志审核管理组

[Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)支持管理组。 可以查询与其他 Azure 资源相同的中心位置中的管理组发生的所有事件。 例如，可以看到对特定管理组所做的所有角色分配或策略分配更改。

:::image type="content" source="./media/al-mg.png" alt-text="具有管理组的活动日志" border="false":::

如果要在 Azure 门户外针对管理组进行查询，管理组的目标范围将如下所示： **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** 。

## <a name="referencing-management-groups-from-other-resource-providers"></a>引用其他资源提供程序的管理组

引用其他资源提供程序的操作中的管理组时，请使用以下路径作为范围。 此路径在使用 PowerShell、Azure CLI 和 REST API 时使用。  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

在 PowerShell 中向管理组分配新的角色分配时，使用此路径的示例如下：

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

在管理组中检索策略定义时，使用同一范围路径。

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>后续步骤

若要了解有关管理组的详细信息，请参阅：

- [创建管理组来组织 Azure 资源](./create.md)
- [如何更改、删除或管理管理组](./manage.md)
- [在 Azure PowerShell 资源模块中查看管理组](/powershell/module/az.resources#resources)
- [在 REST API 中查看管理组](/rest/api/resources/managementgroups)
- [在 Azure CLI 中查看管理组](/cli/azure/account/management-group)
