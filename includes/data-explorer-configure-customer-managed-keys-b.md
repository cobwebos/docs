---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297880"
---
## <a name="create-a-new-key-vault"></a>创建新的 Key Vault

若要使用 PowerShell 创建新的 Key Vault，请调用 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 必须为用来存储客户管理的密钥（用于 Azure 数据资源管理器加密）的 Key Vault 启用两项密钥保护设置：“软删除”和“不要清除”。******** 请将以下示例中括号中的占位符值替换为你自己的值。

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>配置 Key Vault 访问策略

接下来，配置密钥保管库的访问策略，使群集有权访问密钥保管库。 在此步骤中，你将使用前面分配给群集的系统分配的托管标识。 若要设置 Key Vault 的访问策略，请调用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 请将括号中的占位符值替换为你自己的值，并使用前面示例中定义的变量。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>新建密钥

接下来，在 Key Vault 中创建新密钥。 若要创建新密钥，请调用 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请将括号中的占位符值替换为你自己的值，并使用前面示例中定义的变量。

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
