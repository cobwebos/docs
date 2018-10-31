---
ms.assetid: ''
title: Azure Key Vault - 如何将软删除与 PowerShell 配合使用
description: 使用 PowerShell 代码段进行软删除的用例示例
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2018
ms.author: bryanla
ms.openlocfilehash: 99f81e14ca631eccee154a5658bf717cbe07b3da
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364364"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>如何将 Key Vault 软删除与 PowerShell 配合使用

Azure Key Vault 的软删除功能可以恢复已删除的保管库和保管库对象。 软删除将具体探讨以下方案：

- 支持 Key Vault 的可恢复删除
- 支持密钥保管库对象、密钥、机密和证书的可恢复删除

## <a name="prerequisites"></a>先决条件

- Azure PowerShell 4.0.0 或更高版本 - 若尚未安装此产品，请安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。 

>[!NOTE]
> 环境中可能加载了过期版本的 Key Vault PowerShell 输出格式化文件，而没有加载正确版本。 预期 PowerShell 的更新版本将包含输出格式所需的更正，届时将更新此主题。 如果遇到此格式问题，当前的解决方法是：
> - 如果发现未看到此主题中所述的已启用软删除的属性，请使用以下查询：`$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`。


有关适用于 PowerShell 的 Key Vault 特定引用信息，请参阅 [Azure Key Vault PowerShell 引用](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0)。

## <a name="required-permissions"></a>所需的权限

Key Vault 操作通过基于角色的访问控制 (RBAC) 权限单独管理，如下所示：

| Operation | Description | 用户权限 |
|:--|:--|:--|
|列出|列出已删除的密钥保管库。|Microsoft.KeyVault/deletedVaults/read|
|恢复|还原已删除的密钥保管库。|Microsoft.KeyVault/vaults/write|
|清除|永久删除已删除的密钥保管库及其所有内容。|Microsoft.KeyVault/locations/deletedVaults/purge/action|

有关权限和访问控制的详细信息，请参阅[保护密钥保管库](key-vault-secure-your-key-vault.md)。

## <a name="enabling-soft-delete"></a>启用软删除

启用“软删除”以允许恢复已删除的密钥保管库或存储在密钥保管库的对象。

> [!IMPORTANT]
> 在密钥保管库上启用“软删除”是不可逆的操作。 将软删除属性设置为“true”后，将无法更改或删除该属性。  

### <a name="existing-key-vault"></a>现有的密钥保管库

对于名为 ContosoVault 的现有密钥保管库，请按如下所示启用软删除。 

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>新的密钥保管库

通过向创建命令添加软删除启用标志，在创建时启用对新密钥保管库的软删除。

```powershell
New-AzureRmKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>验证软删除支持

若要验证密钥保管库是否启用了软删除，请运行“显示”命令，并查看“启用软删除?” 属性：

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>删除由软删除保护的密钥保管库

删除密钥保管库的命令会改变行为，具体取决于是否启用了软删除。

> [!IMPORTANT]
>如果为没有启用软删除的密钥保管库运行以下命令，则将永久删除此密钥保管库及其所有内容，而没有任何恢复选项！

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>软删除如何保护密钥保管库

已启用软删除：

- 将已删除的密钥保管库从其资源组中删除，并放置在与其创建位置关联的保留命名空间中。 
- 只要已删除对象中包含的密钥保管库处于已删除状态，就无法访问这些已删除的对象（如密钥、机密和证书）。 
- 保留已删除密钥保管库的 DNS 名称，这会阻止创建具有相同名称的新密钥保管库。  

使用以下命令，可查看与订阅关联且处于已删除状态的密钥保管库：

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedState 
```

- ID 可用于在恢复或清除时识别资源。 
- 资源 ID是此保管库的原始资源 ID。 由于此密钥保管库现在处于已删除状态，因此该资源 ID 不存在任何资源。 
- “计划清除日期”表示如果不采取任何操作，将永久删除保管库。 用于计算“计划清除日期”的默认保留期是 90 天。

## <a name="recovering-a-key-vault"></a>恢复密钥保管库

若要恢复密钥保管库，请指定密钥保管库名称、资源组和位置。 请注意已删除的密钥保管库的位置和资源组，以便用于恢复过程。

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

恢复密钥保管库后，将使用密钥保管库的原始资源 ID 创建新资源。 如果删除了原始资源组，则在尝试恢复之前必须创建一个具有相同名称的资源组。

## <a name="key-vault-objects-and-soft-delete"></a>密钥保管库对象和软删除

以下命令将删除已启用软删除的名为“ContosoVault”的密钥保管库中的“ContosoFirstKey”密钥：

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

为软删除启用密钥保管库后，删除的密钥仍然显示为待删除，除非明确列出已删除的密钥。 对处于已删除状态的密钥的大多数操作将失败，列出、恢复或清除已删除的密钥除外。 

例如，以下命令可列出“ContosoVault”密钥保管库中的已删除密钥：

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>转换状态 

在启用了软删除的密钥保管库中删除密钥时，可能需要几秒钟时间完成转换。 在此转换期间，密钥可能不处于活动状态或已删除状态。 

### <a name="using-soft-delete-with-key-vault-objects"></a>将软删除用于密钥保管库对象

就像密钥保管库一样，除非恢复或清除已删除的密钥、机密或证书，否则它将保持已删除状态最多 90 天。 

#### <a name="keys"></a>密钥

恢复软删除的密钥：

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

永久删除（也称为清除）软删除密钥：

> [!IMPORTANT]
> 清除密钥将永久删除，且无法恢复！ 

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

“恢复”和“清除”操作在密钥保管库访问策略中各自具有相关联的权限。 用户或服务主体如果要执行“恢复”或“清除”操作，必须拥有该密钥或机密的相应权限。 默认情况下，使用“全部”快捷方式授予所有权限时，“清除”不会添加到密钥保管库访问策略中。 必须明确授予“清除”权限。 

#### <a name="set-a-key-vault-access-policy"></a>设置密钥保管库访问策略

以下命令授予 user@contoso.com 对“ContosoVault”中的密钥执行多项操作（包括“清除”）的权限：

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> 如果现有密钥保管库刚刚启用软删除，则可能没有“恢复”和“清除”权限。

#### <a name="secrets"></a>机密

像密钥一样，可以使用自己的命令管理机密：

- 删除名为 SQLPassword 的机密： 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- 列出密钥保管库中所有已删除的机密： 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- 恢复处于已删除状态的机密： 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- 清除处于已删除状态的机密： 

  > [!IMPORTANT]
  > 清除机密将永久删除，且无法恢复！

  ```powershell
  Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-and-key-vaults"></a>清除和密钥保管库

### <a name="key-vault-objects"></a>密钥保管库对象

清除密钥、机密或证书会导致永久删除，且无法恢复。 然而，包含已删除对象的密钥保管库将保持不变，密钥保管库中的所有其他对象也将保持不变。 

### <a name="key-vaults-as-containers"></a>密钥保管库作为容器
清除密钥保管库时，将永久删除其全部内容，包括密钥、机密和证书。 若要清除密钥保管库，请使用具有 `-InRemovedState` 选项的命令 `Remove-AzureRmKeyVault`，并通过使用 `-Location location` 参数指定已删除的密钥保管库的位置。 可以使用命令 `Get-AzureRmKeyVault -InRemovedState` 查找已删除的保管库的位置。

>[!IMPORTANT]
>清除密钥保管库将永久删除，这意味着无法恢复！

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>所需的清除权限
- 要清除已删除的密钥保管库，用户需要 Microsoft.KeyVault/locations/deletedVaults/purge/action 操作的 RBAC 权限。 
- 要列出已删除的密钥保管库，用户需要 Microsoft.KeyVault/deletedVaults/read 操作的 RBAC 权限。 
- 默认情况下，只有订阅管理员具有这些权限。 

### <a name="scheduled-purge"></a>计划清除

列出已删除的密钥保管库对象还会显示 Key Vault 计划将其清除的时间。 “计划清除日期”指示如果不采取任何操作，将永久删除密钥保管库对象的时间。 默认情况下，已删除的密钥保管库对象的保留期为 90 天。

>[!IMPORTANT]
>已清除的保管库对象（由“计划清除日期”字段触发清除操作）将被永久删除。 不可恢复！

## <a name="other-resources"></a>其他资源

- 有关 Key Vault 软删除功能的概述，请参阅 [Azure Key Vault 软删除概述](key-vault-ovw-soft-delete.md)。
- 有关 Azure Key Vault 使用情况的综述，请参阅 [Azure Key Vault 入门](key-vault-get-started.md)。

