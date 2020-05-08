---
title: 如何通过 PowerShell 管理分配
description: 了解如何通过官方 Azure 蓝图 PowerShell 模块 Az. 蓝图管理蓝图分配。
ms.date: 05/06/2020
ms.topic: how-to
ms.openlocfilehash: fa0f89df79c4ae1c5b66998089f04575bd53ea37
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863971"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何通过 PowerShell 管理分配

可以使用**Az** Azure PowerShell module 管理蓝图分配。 模块支持提取、创建、更新和删除分配。 模块还可以获取有关现有蓝图定义的详细信息。 本文介绍如何安装该模块并开始使用它。

## <a name="add-the-azblueprint-module"></a>添加 Az module 模块

若要启用 Azure PowerShell 来管理蓝图分配，必须添加模块。 此模块可以与在本地安装的 PowerShell 以及 [Azure Cloud Shell](https://shell.azure.com) 一起使用，也可以与 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)一起使用。

### <a name="base-requirements"></a>基本要求

Azure 蓝图模块需要以下软件：

- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。
- PowerShellGet 2.0.1 或更高版本。 若尚未安装或更新，请遵循[这些说明](/powershell/scripting/gallery/installing-psget)。

### <a name="install-the-module"></a>安装模块

适用于 PowerShell 的 Azure 蓝图模块为**Az. 蓝图**。

1. 从管理 PowerShell 提示符运行以下命令****：

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果**Az**已安装，则可能需要使用`-AllowClobber`来强制安装。

1. 验证模块是否已导入并正确版本（0.2.6）：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>获取蓝图定义

处理赋值的第一步通常是获取对蓝图定义的引用。
`Get-AzBlueprint` Cmdlet 获取一个或多个蓝图定义。 Cmdlet 可以从管理组`-ManagementGroupId {mgId}`或使用`-SubscriptionId {subId}`订阅获取蓝图定义。 **Name**参数获取蓝图定义，但必须与**ManagementGroupId**或**SubscriptionId**一起使用。 **版本**可与**名称**一起使用，以便更明确地了解返回的蓝图定义。 开关`-LatestPublished`用于获取最近发布的版本，而不是**版本**。

下面的示例使用`Get-AzBlueprint`从表示为`{subId}`的特定订阅获取名为 "101-蓝图-定义-订阅" 的所有版本的蓝图定义：

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

可以展开蓝图定义上的[蓝图参数](../concepts/parameters.md#blueprint-parameters)以提供详细信息。

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

## <a name="get-blueprint-assignments"></a>获取蓝图分配

如果蓝图分配已存在，可以使用`Get-AzBlueprintAssignment` cmdlet 获取对它的引用。 Cmdlet 采用**SubscriptionId**和**Name**作为可选参数。 如果未指定**SubscriptionId** ，则使用当前的订阅上下文。

下面的示例使用`Get-AzBlueprintAssignment`从表示为`{subId}`的特定订阅获取名为 "分配-锁定资源组" 的单一蓝图分配：

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

如果蓝图分配尚不存在，则可以用`New-AzBlueprintAssignment` cmdlet 创建。 此 cmdlet 使用以下参数：

- **名称**[必需]
  - 指定蓝图分配的名称
  - 必须是唯一的，且**SubscriptionId**中不存在
- **蓝图**[必需]
  - 指定要分配的蓝图定义
  - 用于`Get-AzBlueprint`获取 reference 对象
- **位置**[必需]
  - 指定要在其中创建系统分配的托管标识和订阅部署对象的区域。
- **订阅**（可选）
  - 指定将分配部署到的订阅
  - 如果未提供，则默认为当前订阅上下文
- **锁定**（可选）
  - 定义要用于已部署资源的[蓝图资源锁定](../concepts/resource-locking.md)
  - 支持的选项： _None_、 _AllResourcesReadOnly_、 _AllResourcesDoNotDelete_
  - 如果未提供，默认值为 "_无_"
- **SystemAssignedIdentity** （可选）
  - 选择此项可为分配创建系统分配的托管标识并部署资源
  - "Identity" 参数集的默认值
  - 不能与**UserAssignedIdentity**一起使用
- **UserAssignedIdentity** （可选）
  - 指定用于分配并部署资源的用户分配的托管标识
  - "Identity" 参数集的一部分
  - 不能与**SystemAssignedIdentity**一起使用
- **参数**（可选）
  - 用于在蓝图分配上设置[动态参数](../concepts/parameters.md#dynamic-parameters)的键/值对的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 动态参数的默认值是定义中的**defaultValue**
  - 如果未提供参数且没有**defaultValue**，则参数不是可选的

    > [!NOTE]
    > **参数**不支持 secureStrings。

- **ResourceGroupParameter** （可选）
  - 资源组项目的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每个资源组项目占位符都具有用于动态设置该资源组项目中的**名称**和**位置**的键/值对
  - 如果未提供资源组参数并且没有**defaultValue**，则资源组参数不是可选的
- **AssignmentFile** （可选）
  - 蓝图分配的 JSON 文件表示形式的路径
  - 此参数是 PowerShell 参数集的一部分，该参数集仅包含**名称**、**蓝图**和**SubscriptionId**以及通用参数。

### <a name="example-1-provide-parameters"></a>示例1：提供参数

下面的示例创建一个新分配的 "我的蓝图" 蓝图定义的版本`Get-AzBlueprint`"1.1"，将托管标识和分配对象位置设置为 "westus2"，使用_AllResourcesReadOnly_锁定资源，并在特定订阅上设置**参数**和**ResourceGroupParameter**的哈希表，表示为： `{subId}`

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

用于创建蓝图分配的示例输出如下所示：

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>示例2：使用 JSON 赋值定义文件

下面的示例创建了几乎与[示例 1](#example-1-provide-parameters)相同的分配。 该示例演示如何使用 JSON 赋值定义文件和**AssignmentFile**参数，而不是将参数传递给 cmdlet。 此外，将**excludedPrincipals**属性配置为**锁定**的一部分。 没有适用于**excludedPrincipals**的 PowerShell 参数，只能通过 JSON 赋值定义文件设置属性来配置属性。

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

有关用户分配的托管标识的 JSON 分配定义文件的示例，请参阅[示例正文：示例：使用用户分配的托管标识](/rest/api/blueprints/assignments/createorupdate#examples)对 REST API 进行分配。

## <a name="update-blueprint-assignments"></a>更新蓝图分配

有时，必须更新已创建的蓝图分配。 `Set-AzBlueprintAssignment` Cmdlet 处理此操作。 该 cmdlet 将使用该`New-AzBlueprintAssignment` cmdlet 执行的大部分相同参数，从而允许更新在分配上设置的任何内容。 例外情况包括：_名称_、_蓝图_和_SubscriptionId_。 仅更新所提供的值。

若要了解更新蓝图分配时所发生的情况，请参阅[更新分配的规则](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名称**[必需]
  - 指定要更新的蓝图分配的名称
  - 用于查找要更新的分配，而不是更改分配
- **蓝图**[必需]
  - 指定蓝图分配的蓝图定义
  - 用于`Get-AzBlueprint`获取 reference 对象
  - 用于查找要更新的分配，而不是更改分配
- **位置**（可选）
  - 指定要在其中创建系统分配的托管标识和订阅部署对象的区域。
- **订阅**（可选）
  - 指定将分配部署到的订阅
  - 如果未提供，则默认为当前订阅上下文
  - 用于查找要更新的分配，而不是更改分配
- **锁定**（可选）
  - 定义要用于已部署资源的[蓝图资源锁定](../concepts/resource-locking.md)
  - 支持的选项： _None_、 _AllResourcesReadOnly_、 _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** （可选）
  - 选择此项可为分配创建系统分配的托管标识并部署资源
  - "Identity" 参数集的默认值
  - 不能与**UserAssignedIdentity**一起使用
- **UserAssignedIdentity** （可选）
  - 指定用于分配并部署资源的用户分配的托管标识
  - "Identity" 参数集的一部分
  - 不能与**SystemAssignedIdentity**一起使用
- **参数**（可选）
  - 用于在蓝图分配上设置[动态参数](../concepts/parameters.md#dynamic-parameters)的键/值对的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 动态参数的默认值是定义中的**defaultValue**
  - 如果未提供参数且没有**defaultValue**，则参数不是可选的

    > [!NOTE]
    > **参数**不支持 secureStrings。

- **ResourceGroupParameter** （可选）
  - 资源组项目的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每个资源组项目占位符都具有用于动态设置该资源组项目中的**名称**和**位置**的键/值对
  - 如果未提供资源组参数并且没有**defaultValue**，则资源组参数不是可选的

下面的示例`Get-AzBlueprint`通过更改锁定模式，更新通过获取的 "我的蓝图" 蓝图定义的版本 "1.1" 的分配：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

用于创建蓝图分配的示例输出如下所示：

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

当需要删除蓝图分配时，该 cmdlet 将`Remove-AzBlueprintAssignment`处理此操作。 该 cmdlet 使用**Name**或**InputObject**来指定要删除的蓝图分配。 **SubscriptionId**是_必需_的，并且必须在所有情况下提供。

下面的示例使用`Get-AzBlueprintAssignment`获取现有蓝图分配，然后将其从表示为`{subId}`的特定订阅中删除：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>代码示例

将所有步骤组合在一起后，以下示例将获取蓝图定义，然后创建、更新和删除特定订阅中表示为`{subId}`的蓝图分配：

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
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