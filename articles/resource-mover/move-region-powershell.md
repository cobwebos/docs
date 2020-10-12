---
title: 使用 Azure 资源移动器中的 PowerShell 跨区域移动资源
description: 了解如何使用 Azure 资源移动器中的 PowerShell 跨区域移动资源。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89670333"
---
# <a name="move-resources-across-regions-in-powershell"></a>在 PowerShell 中跨区域移动资源

了解如何使用 Azure 资源移动器中的 PowerShell 将 Azure 资源移到另一个区域。 

> [!NOTE]
> Azure 资源转移器目前提供预览版。



## <a name="before-you-start"></a>开始之前

- 你的 Azure 订阅应有权访问资源移动器，你应具有订阅的 [所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 或 [用户访问管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) 权限。
- 资源移动器并不跟踪更改和升级，因此在开始移动它们之前，对资源进行任何所需的更改。
- 在将资源与 PowerShell 一起移动时，当前无法编辑任何目标区域设置。 直接在门户中修改这些设置。
- 向移动集合添加资源时，为将资源移到另一个区域，有关移动的元数据将存储在为此目的而创建的资源组中。 此资源组目前可以位于美国东部2或北欧地区。 可以使用其中任何一个区域中的元数据，在任何公共区域之间移动 Azure 资源。

## <a name="sample-values"></a>示例值

我们在脚本示例中使用以下值：

**设置** | **值** 
--- | ---
订阅 ID | subscription-id
资源移动器服务位置 | 美国东部 2
元数据资源组 | RegionMoveRG-centralus-westcentralus
元数据资源组位置 | 美国东部 2
移动集合名称 | MoveCollection-centralus-westcentralus
源区域 |  centralus
源资源组 | PSDemoRM
目标区域 | westcentralus
目标资源组 | PSDemoRMtgt
要移动的 VM | PSDemoVM

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure 订阅。

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>为元数据创建资源组

创建资源组以保存移动集合元数据和配置信息。

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**预期输出**：

![创建资源组后输出文本](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>注册资源提供程序

注册资源提供程序 Microsoft. 迁移，以便可以创建 MoveCollection 资源。

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>创建移动集合

MoveCollection 对象存储有关要移动的资源的元数据和配置信息。

- 对于 MoveCollection 对象，若要访问 Azure 资源移动器服务所在的订阅，需要使用 [系统分配的托管 (标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) （以前称为托管服务）标识 (MSI) # A3，该订阅由订阅信任。
- 为该标识分配源订阅的 "参与者" 或 "用户访问管理员" 角色。
- 若要将角色分配给标识，需要具有以下权限的订阅中的角色 (如所有者或用户访问管理员) ：
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**预期输出**：

![创建移动集合后的输出文本](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>分配托管标识 

设置订阅 ID，检索 MoveCollection 对象的标识主体，并向其分配 Azure 角色。


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>向移动集合添加资源

检索要移动的源资源的 ID。 然后，将其添加到移动集合。

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**预期输出**

![检索资源 ID 后输出文本](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**预期输出** 
 ![添加资源后输出文本](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>解决依赖项问题

验证你添加的资源并解决对其他资源的依赖关系。

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**存在依赖关系时的输出**

如果移动集合中的资源依赖于其他资源，则会发出失败消息。

![检查依赖项后输出文本](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>检索依赖项

如果要移动的资源与其他资源有依赖关系，则可以检索缺少的依赖项的相关信息。

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**预期输出**

![检索依赖项后输出文本](./media/move-region-powershell/missing-dependencies.png)

在此示例中，将两个依赖项标识为缺少：

- 源资源组： PSDemoRM
- VM 上的 NIC： PSDemoVM62

## <a name="add-dependencies-to-collection"></a>将依赖项添加到集合


使用检索到的资源 Id，识别缺少的资源的名称，并将其添加到移动集合。

### <a name="add-the-nic"></a>添加 NIC

检索 NIC 名称和类型，并将其添加到集合中。

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**预期输出**

![检索 NIC 后的输出文本](./media/move-region-powershell/output-retrieve-nic.png)

现在，将 NIC 添加到移动集合。 此示例为目标 NIC 提供相同的名称。 保持设置和大小写一致。

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**预期输出**

![将 NIC 添加到集合后输出文本](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>添加源资源组

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**预期输出**

![将资源组添加到集合后输出文本](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>重新检查依赖关系

再次检查是否缺少依赖项。

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**输出**

缺少其他依赖项。

![再次检查依赖项后输出文本](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>检索其他依赖项

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**预期输出**


![检索其他依赖项后输出文本](./media/move-region-powershell/additional-missing-dependencies.png)

在此示例中，将其他三个依赖项标识为缺少：

- 公共 IP 地址： psdemovm-IP
- Azure 虚拟网络： psdemorm-vnet
- 网络安全组 (NSG) ： psdemovm-NSG

## <a name="add-additional-dependencies"></a>添加其他依赖项

1. [按照说明](#add-dependencies-to-collection) 将这些资源添加到移动集合。
2. 添加资源后，请再次验证，直到添加所有依赖项。


## <a name="prepare-and-move-the-source-resource-group"></a>准备并移动源资源组

在源区域中，您可以在移动资源之前准备资源。 准备过程取决于要移动的资源。 例如，对于无状态资源准备，可以准备并导出 Azure 资源管理器 (ARM) 模板。 对于有状态资源，可以启动复制。 

准备源资源之前，需要准备并移动源资源组。

### <a name="prepare"></a>准备

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**预期输出**

![准备源资源组后输出文本](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>启动移动

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**预期输出**

![提交源资源组后输出文本](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>准备资源

将源资源组移动到目标区域后，在移动集合中检索资源列表，并准备移动它们。

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**预期输出**

![检索集合中的资源之后的输出文本](./media/move-region-powershell/collection-resources.png)

现在，准备资源。

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
或者，你可以使用资源 ID 而不是名称来准备和移动资源：

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**预期输出**

![在集合中准备资源后输出文本](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>启动资源移动

准备资源后，开始移动。

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**预期输出** 
 ![启动资源移动后输出文本](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>放弃或提交移动

初始移动后，你可以决定是提交移动，还是放弃它。 

- **放弃**：如果要测试，并且不想实际移动源资源，则可能需要放弃移动。 放弃移动会将资源返回到“启动移动挂起”状态。 如果需要，你可以重新启动移动。
- **提交**：提交即完成移动到目标区域的操作。 提交后，源资源将处于“删除源挂起”状态，你可以决定是否要删除它。

### <a name="discard"></a>弃用

若要放弃移动：

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**预期输出** 
 ![丢弃移动后的输出文本](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

提交移动：

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**预期输出**

![提交移动后的输出文本](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>删除源资源

提交移动并验证资源在目标区域中是否按预期方式工作后，可以[使用 PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)或[Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources)删除[Azure 门户](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)中的源资源。

## <a name="next-steps"></a>后续步骤

[了解如何](remove-move-resources.md) 从移动集合中删除资源。