---
title: 如何使用 PowerShell 管理分配
description: 了解如何使用正式的 Azure 蓝图 PowerShell 模块 Az.Blueprint 管理蓝图分配。
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: b16cf887ba8bfc51616839db5f4af87944ec686d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247395"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何使用 PowerShell 管理分配

可以使用**Az.蓝图**Azure PowerShell 模块管理蓝图分配。 该模块支持提取、创建、更新和删除分配。 该模块还可以获取有关现有蓝图定义的详细信息。 本文介绍如何安装模块并开始使用它。

## <a name="add-the-azblueprint-module"></a>添加 Az.蓝图模块

要使 Azure PowerShell 能够管理蓝图分配，必须添加模块。 此模块可以与在本地安装的 PowerShell 以及 [Azure Cloud Shell](https://shell.azure.com) 一起使用，也可以与 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)一起使用。

### <a name="base-requirements"></a>基本要求

Azure 蓝图模块需要以下软件：

- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。
- PowerShellGet 2.0.1 或更高版本。 若尚未安装或更新，请遵循[这些说明](/powershell/scripting/gallery/installing-psget)。

### <a name="install-the-module"></a>安装模块

PowerShell 的蓝图模块是**Az.蓝图**。

1. 从管理 PowerShell 提示符运行以下命令****：

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果已安装**Az.帐户**，则可能需要使用`-AllowClobber`强制安装。

1. 验证模块是否已导入且是否正确版本 （0.2.6）：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>获取蓝图定义

使用分配的第一步通常是获得对蓝图定义的引用。
cmdlet`Get-AzBlueprint`获取一个或多个蓝图定义。 cmdlet 可以从 具有`-ManagementGroupId {mgId}`的管理组获取蓝图定义，也可以从 订阅`-SubscriptionId {subId}`获取蓝图定义。 **Name**参数获取蓝图定义，但必须与**管理组Id**或**订阅 Id**一起使用。 **版本**可以与**Name**一起使用，以便更明确地确定返回的蓝图定义。 交换机`-LatestPublished`获取最近发布的版本，而不是**版本**。

以下示例用于`Get-AzBlueprint`从表示为的特定订阅获取名为"101-蓝图-定义-订阅"的蓝图定义的所有版本`{subId}`：

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

可以扩展蓝图定义的[蓝图参数](../concepts/parameters.md#blueprint-parameters)以提供更多信息。

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

如果蓝图分配已存在，则可以使用`Get-AzBlueprintAssignment`cmdlet 获取对它的引用。 cmdlet 将**订阅 Id**和**Name**作为可选参数。 如果未指定**订阅 Id，** 则使用当前订阅上下文。

以下示例用于`Get-AzBlueprintAssignment`从表示为的特定订阅中获取名为"分配-锁定资源组"的单个蓝图分配`{subId}`：

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

如果蓝图分配尚不存在，您可以使用`New-AzBlueprintAssignment`cmdlet 创建它。 此 cmdlet 使用以下参数：

- **名称**[必需]
  - 指定蓝图分配的名称
  - 必须是唯一的，并且在**订阅 Id**中不存在
- **蓝图**[必需]
  - 指定要分配的蓝图定义
  - 用于`Get-AzBlueprint`获取引用对象
- **位置**[必需]
  - 指定要在中创建的系统分配的托管标识和订阅部署对象的区域
- **订阅**（可选）
  - 指定分配部署到的订阅
  - 如果未提供，则默认为当前订阅上下文
- **锁定**（可选）
  - 定义用于已部署资源的[蓝图资源锁定](../concepts/resource-locking.md)
  - 支持的选项：_无_，_所有资源只读_，_所有资源不删除_
  - 如果未提供，则默认为 _"无"_
- **系统分配标识**（可选）
  - 选择该选择可为分配创建系统分配的托管标识并部署资源
  - "标识"参数集的默认值
  - 不能与**用户分配的身份**一起使用
- **用户分配的身份**（可选）
  - 指定用户分配的托管标识，用于分配和部署资源
  - "标识"参数集的一部分
  - 不能与**系统分配的身份**一起使用
- **参数**（可选）
  - 用于在蓝图分配上设置[动态参数](../concepts/parameters.md#dynamic-parameters)的键/值对哈希[表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 动态参数的默认值是定义中的**默认值**
  - 如果未提供参数，并且没有**默认值**，则该参数不是可选的

    > [!NOTE]
    > **参数**不支持安全字符串。

- **资源组参数**（可选）
  - 资源组工件的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每个资源组项目占位符具有键/值对，用于动态设置该资源组项目**的名称**和**位置**
  - 如果未提供资源组参数，并且没有**默认值**，则资源组参数不是可选的
- **分配文件**（可选）
  - 蓝图分配 JSON 文件表示的路径
  - 此参数是 PowerShell 参数集的一部分，该参数仅包括**名称**、**蓝图**和**订阅 Id，** 以及公共参数。

### <a name="example-1-provide-parameters"></a>示例 1：提供参数

下面的示例`Get-AzBlueprint`创建与"我的蓝图"蓝图定义"1.1"版本"1.1"的新分配，将托管标识和分配对象位置设置为"westus2"，使用_AllResourcesReadOnly_锁定资源，并在表示的特定订阅上设置**参数**和资源**组参数**的哈希表，表示为： `{subId}`

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

创建蓝图分配的示例输出如下所示：

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

### <a name="example-2-use-a-json-assignment-definition-file"></a>示例 2：使用 JSON 分配定义文件

下面的示例创建与[示例 1](#example-1-provide-parameters)几乎相同的赋值。
该示例显示 JSON 分配定义文件和**分配文件**参数的使用，而不是将参数传递给 cmdlet。 此外，**排除主体**属性配置为**锁**的一部分。 没有**排除主体**的 PowerShell 参数，只能通过 JSON 分配定义文件设置该属性来配置该属性。

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

有关用户分配的托管标识的 JSON 分配定义文件的示例，请参阅示例中的请求正文：使用 REST API[的用户分配的托管标识分配](/rest/api/blueprints/assignments/createorupdate#examples)。

## <a name="update-blueprint-assignments"></a>更新蓝图分配

有时，需要更新已创建的蓝图分配。 `Set-AzBlueprintAssignment` cmdlet 处理此操作。 cmdlet 采用 cmdlet 执行的大多数相同`New-AzBlueprintAssignment`参数，允许更新在赋值上设置的任何参数。 例外情况是_名称_、_蓝图_和_订阅 Id_。 仅更新提供的值。

要了解更新蓝图分配时会发生什么情况，请参阅[更新分配的规则](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名称**[必需]
  - 指定要更新的蓝图分配的名称
  - 用于查找要更新的分配，而不是更改分配
- **蓝图**[必需]
  - 指定蓝图分配的蓝图定义
  - 用于`Get-AzBlueprint`获取引用对象
  - 用于查找要更新的分配，而不是更改分配
- **位置**（可选）
  - 指定要在中创建的系统分配的托管标识和订阅部署对象的区域
- **订阅**（可选）
  - 指定分配部署到的订阅
  - 如果未提供，则默认为当前订阅上下文
  - 用于查找要更新的分配，而不是更改分配
- **锁定**（可选）
  - 定义用于已部署资源的[蓝图资源锁定](../concepts/resource-locking.md)
  - 支持的选项：_无_，_所有资源只读_，_所有资源不删除_
- **系统分配标识**（可选）
  - 选择该选择可为分配创建系统分配的托管标识并部署资源
  - "标识"参数集的默认值
  - 不能与**用户分配的身份**一起使用
- **用户分配的身份**（可选）
  - 指定用户分配的托管标识，用于分配和部署资源
  - "标识"参数集的一部分
  - 不能与**系统分配的身份**一起使用
- **参数**（可选）
  - 用于在蓝图分配上设置[动态参数](../concepts/parameters.md#dynamic-parameters)的键/值对哈希[表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 动态参数的默认值是定义中的**默认值**
  - 如果未提供参数，并且没有**默认值**，则该参数不是可选的

    > [!NOTE]
    > **参数**不支持安全字符串。

- **资源组参数**（可选）
  - 资源组工件的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每个资源组项目占位符具有键/值对，用于动态设置该资源组项目**的名称**和**位置**
  - 如果未提供资源组参数，并且没有**默认值**，则资源组参数不是可选的

以下示例更新`Get-AzBlueprint`通过更改锁定模式获取的"我的蓝图"蓝图定义的版本"1.1"的分配：

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

当删除蓝图分配时，cmdlet`Remove-AzBlueprintAssignment`处理此操作。 cmdlet 采用**名称**或**输入对象**来指定要删除的蓝图分配。 **订阅 Id** _是必需的_，必须在所有情况下都提供。

下面的示例获取具有`Get-AzBlueprintAssignment`的现有蓝图分配，然后将其从表示为`{subId}`的特定订阅中删除：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>端到端代码示例

将所有步骤组合在一起，以下示例获取蓝图定义，然后创建、更新和删除表示为`{subId}`的特定订阅中的蓝图分配：

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
- 学习自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 在分配蓝图期间使用[常规故障排除时](../troubleshoot/general.md)解决问题。