---
title: 如何管理使用 PowerShell 分配
description: 了解如何管理与官方 Azure 蓝图 PowerShell 模块，Az.Blueprint 蓝图分配。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785684"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何管理使用 PowerShell 分配

可以使用管理蓝图分配**Az.Blueprint** Azure PowerShell 模块。 该模块支持提取、 创建、 更新和删除分配。 该模块还可以在现有蓝图定义提取详细信息。 这篇文章介绍如何安装模块，并开始使用它。

## <a name="add-the-azblueprint-module"></a>添加 Az.Blueprint 模块

若要启用 Azure PowerShell 管理蓝图分配，必须添加的模块。 此模块可以与在本地安装的 PowerShell 以及 [Azure Cloud Shell](https://shell.azure.com) 一起使用，也可以与 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)一起使用。

### <a name="base-requirements"></a>基本要求

Azure 蓝图模块需要以下软件：

- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。
- PowerShellGet 2.0.1 或更高版本。 若尚未安装或更新，请遵循[这些说明](/powershell/gallery/installing-psget)。

### <a name="install-the-module"></a>安装模块

PowerShell 蓝图模块**Az.Blueprint**。

1. 从管理 PowerShell 提示符运行以下命令：

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果**Az.Accounts**是已安装，它可能有必要使用`-AllowClobber`能强制安装。

1. 验证是否已导入该模块且是否为正确版本 (0.1.0)：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>获取蓝图定义

使用分配的第一步通常获取对蓝图定义的引用。
`Get-AzBlueprint` Cmdlet 将获取一个或多个蓝图定义。 该 cmdlet 可以从管理组与获取蓝图定义`-ManagementGroupId {mgId}`或与订阅`-SubscriptionId {subId}`。 **名称**参数获取蓝图定义，但必须将用于**ManagementGroupId**或**SubscriptionId**。 **版本**可用于**名称**可以更明确地返回哪些蓝图定义。 而不是**版本**，该交换机`-LatestPublished`获取最新发布版本。

下面的示例使用`Get-AzBlueprint`若要获取所有版本的蓝图定义名为 101 蓝图定义订阅中表示为特定订阅`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

具有多个版本的蓝图定义的示例输出如下所示：

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

[蓝图参数](../concepts/parameters.md#blueprint-parameters)蓝图上可以展开定义来提供的详细信息。

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>获取到蓝图分配

如果蓝图分配已存在，可以获取与它的引用`Get-AzBlueprintAssignment`cmdlet。 该 cmdlet 将接受**SubscriptionId**并**名称**作为可选参数。 如果**SubscriptionId**未指定，则使用当前订阅上下文。

下面的示例使用`Get-AzBlueprintAssignment`若要获取特定订阅表示为从名为分配的锁的资源的组的单个蓝图分配`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

蓝图分配的示例输出如下所示：

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>创建蓝图分配

如果尚不存在蓝图分配，可以将其与创建`New-AzBlueprintAssignment`cmdlet。 此 cmdlet 使用以下参数：

- **名称**[必需]
  - 指定蓝图分配的名称
  - 必须是唯一的且不存在于**SubscriptionId**
- **蓝图**[必需]
  - 指定要分配的蓝图定义
  - 使用`Get-AzBlueprint`来获取所引用对象
- **位置**[必需]
  - 指定系统分配给托管标识和订阅部署对象要在其中创建区域
- **订阅**（可选）
  - 指定分配部署到的订阅
  - 如果未提供，默认为当前订阅上下文
- **锁**（可选）
  - 定义[蓝图资源锁定](../concepts/resource-locking.md)要用于已部署的资源
  - 支持的选项：_无_， _AllResourcesReadOnly_， _AllResourcesDoNotDelete_
  - 如果未提供，默认为_None_
- **SystemAssignedIdentity** （可选）
  - 选择要创建系统分配给托管的标识的分配和部署资源
  - 默认值为"identity"参数集的
  - 不能用于**UserAssignedIdentity**
- **UserAssignedIdentity** （可选）
  - 指定的用户分配托管的标识用于分配和部署资源
  - "Identity"参数集的一部分
  - 不能用于**SystemAssignedIdentity**
- **参数**（可选）
  - 一个[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的键/值对的设置[动态参数](../concepts/parameters.md#dynamic-parameters)上蓝图分配
  - 默认值为动态参数**defaultValue**定义中
  - 如果未提供参数，且不具有**defaultValue**，该参数不是可选

    > [!NOTE]
    > **参数**不支持 secureStrings。

- **ResourceGroupParameter** （可选）
  - 一个[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的资源组项目
  - 每个资源组项目占位符将具有动态设置的键/值对**名称**和/或**位置**该资源组项目，
  - 如果未提供资源组参数，且不具有**defaultValue**，资源组参数不是可选

下面的示例创建的与提取的我蓝图蓝图定义"1.1"版的新分配`Get-AzBlueprint`设置为 westus2 托管的标识和分配对象位置、 锁定的资源与_AllResourcesReadOnly_，并设置两个哈希表**参数**并**ResourceGroupParameter**上表示为特定订阅`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

创建蓝图分配的示例输出如下所示：

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>更新蓝图分配

有时有必要更新已创建的蓝图分配。 `Set-AzBlueprintAssignment` Cmdlet 处理此操作。 该 cmdlet 将接受大多数相同的参数的`New-AzBlueprintAssignment`cmdlet 一样，允许在分配上设置要更新的任何内容。 为以下情形例外_名称_，_蓝图_，并_SubscriptionId_。 更新所提供的值。

若要了解更新蓝图分配时，会发生什么情况，请参阅[更新分配规则](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名称**[必需]
  - 指定要更新的蓝图分配的名称
  - 用于查找要更新，不能更改分配的分配
- **蓝图**[必需]
  - 指定蓝图分配蓝图的定义
  - 使用`Get-AzBlueprint`来获取所引用对象
  - 用于查找要更新，不能更改分配的分配
- **位置**（可选）
  - 指定系统分配给托管标识和订阅部署对象要在其中创建区域
- **订阅**（可选）
  - 指定分配部署到的订阅
  - 如果未提供，默认为当前订阅上下文
  - 用于查找要更新，不能更改分配的分配
- **锁**（可选）
  - 定义[蓝图资源锁定](../concepts/resource-locking.md)要用于已部署的资源
  - 支持的选项：_无_， _AllResourcesReadOnly_， _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** （可选）
  - 选择要创建系统分配给托管的标识的分配和部署资源
  - 默认值为"identity"参数集的
  - 不能用于**UserAssignedIdentity**
- **UserAssignedIdentity** （可选）
  - 指定的用户分配托管的标识用于分配和部署资源
  - "Identity"参数集的一部分
  - 不能用于**SystemAssignedIdentity**
- **参数**（可选）
  - 一个[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的键/值对的设置[动态参数](../concepts/parameters.md#dynamic-parameters)上蓝图分配
  - 默认值为动态参数**defaultValue**定义中
  - 如果未提供参数，且不具有**defaultValue**，该参数不是可选

    > [!NOTE]
    > **参数**不支持 secureStrings。

- **ResourceGroupParameter** （可选）
  - 一个[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的资源组项目
  - 每个资源组项目占位符将具有动态设置的键/值对**名称**和/或**位置**该资源组项目，
  - 如果未提供资源组参数，且不具有**defaultValue**，资源组参数不是可选

以下示例将更新的提取与 my 蓝图蓝图定义"1.1"版分配`Get-AzBlueprint`通过更改的锁模式：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

创建蓝图分配的示例输出如下所示：

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>删除蓝图分配

在需要时为要删除蓝图分配`Remove-AzBlueprintAssignment`cmdlet 处理此操作。 该 cmdlet 将接受任一**名称**或**InputObject**以指定的蓝图分配，以删除。 **SubscriptionId**是_必需_，必须在所有情况下提供。

下面的示例会提取与现有的蓝图分配`Get-AzBlueprintAssignment`，并将其删除从表示为特定订阅`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>端到端的代码示例

组合在一起的所有步骤，下面的示例获取蓝图定义，然后创建、 更新，并表示为特定订阅中删除蓝图分配`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。