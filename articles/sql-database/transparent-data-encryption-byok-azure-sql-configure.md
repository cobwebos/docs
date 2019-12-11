---
title: 使用 Azure Key Vault 启用 SQL TDE
description: 了解如何配置 Azure SQL 数据库和数据仓库，以开始使用透明数据加密 (TDE) 通过 PowerShell 或 CLI 进行静态加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: e647725c5d30f35f39263757508bdd1725552731
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997303"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell 和 CLI：使用 Azure Key Vault 的客户托管密钥启用透明数据加密

本文逐步介绍如何使用 Azure Key Vault 中的密钥对 SQL 数据库或数据仓库启用透明数据加密 (TDE)。 要了解更多关于 TDE 与 Azure Key Vault 集成（即自带密钥 (BYOK) 支持）的信息，请访问[使用 Azure Key Vault 中由客户管理的密钥进行 TDE](transparent-data-encryption-byok-azure-sql.md)。

## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

- 必须有一个 Azure 订阅，并且是该订阅的管理员。
- [建议但可选] 提供硬件安全模块 (HSM) 或本地密钥存储，以便创建 TDE 保护器密钥材料的本地副本。
- 必须安装并运行 Azure PowerShell。
- 创建用于 TDE 的 Azure Key Vault 和密钥。
  - [有关使用硬件安全模块 (HSM) 和 Key Vault 的说明](../key-vault/key-vault-hsm-protected-keys.md)
    - Key Vault 必须包含用于 TDE 的以下属性：
  - [软删除](../key-vault/key-vault-ovw-soft-delete.md)和清除保护
- 密钥必须包含用于 TDE 的以下特性：
   - 无过期日期
   - 未禁用
   - 能够执行“获取”、“包装密钥”和“解包密钥”操作

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

有关 Az 模块安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 有关特定的 cmdlet，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。

有关 Key Vault 的详细信息，请参阅[Key Vault 中的 PowerShell 说明](../key-vault/quick-create-powershell.md)以及[如何使用 powershell Key Vault 软删除](../key-vault/key-vault-soft-delete-powershell.md)。

> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell Azure 资源管理器（RM）模块，但所有将来的开发都适用于 Az .Sql 模块。 AzureRM 模块将继续收到 bug 修复，直到至少12月2020。  Az 模块和 AzureRm 模块中的命令的参数完全相同。 有关其兼容性的详细信息，请参阅[新 Azure PowerShell Az Module 简介](/powershell/azure/new-azureps-module-az)。

## <a name="assign-an-azure-ad-identity-to-your-server"></a>将 Azure AD 标识分配到服务器

如果有现有的服务器，请使用以下命令将 Azure AD 标识添加到该服务器：

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

如果正在创建服务器，请在服务器创建过程中使用带有标记标识的[AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet 添加 Azure AD 标识：

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>向服务器授予 Key Vault 权限

使用[AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 向服务器授予对密钥保管库的访问权限，然后再使用该密钥保管库中的密钥用于 TDE。

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>将 Key Vault 密钥添加到服务器并设置 TDE 保护器

- 使用[AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet 从 key vault 中检索密钥 ID
- 使用[AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet 将 Key Vault 中的密钥添加到服务器。
- 使用[AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 将密钥设置为所有服务器资源的 TDE 保护程序。
- 使用[AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 确认已按预期配置了 TDE 保护程序。

> [!NOTE]
> Key Vault 名称和密钥名称的总长度不能超过 94 个字符。

> [!TIP]
> Key Vault 中的示例 KeyId： https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>启用 TDE

使用[AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) CMDLET 打开 TDE。

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

现已使用 Key Vault 中的加密密钥为数据库或数据仓库启用了 TDE。

## <a name="check-the-encryption-state-and-encryption-activity"></a>检查加密状态和加密活动

使用[AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)获取加密状态，并使用[AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity)检查数据库或数据仓库的加密进度。

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要安装所需的命令行接口版本2.0 或更高版本并连接到 Azure 订阅，请参阅[安装和配置 Azure 跨平台命令行接口 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

有关 Key Vault 的详细信息，请参阅[使用 cli 2.0 管理 Key Vault](../key-vault/key-vault-manage-with-cli2.md)和[如何将 Key Vault 软删除与 cli 配合使用](../key-vault/key-vault-soft-delete-cli.md)。

## <a name="assign-an-azure-ad-identity-to-your-server"></a>将 Azure AD 标识分配到服务器

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> 防止“principalID”创建服务器，它是用于在下一步中分配密钥保管库权限的对象 ID

## <a name="grant-key-vault-permissions-to-your-server"></a>向服务器授予 Key Vault 权限

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> 保留新密钥的密钥 URI 或 keyID 以用于下一步，例如： https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>将 Key Vault 密钥添加到服务器并设置 TDE 保护器

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Key Vault 名称和密钥名称的总长度不能超过 94 个字符。

## <a name="turn-on-tde"></a>启用 TDE

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

现已使用 Azure Key Vault 中客户托管的加密密钥为数据库或数据仓库启用了 TDE。

## <a name="check-the-encryption-state-and-encryption-activity"></a>检查加密状态和加密活动

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>有用的 PowerShell cmdlet

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- 使用[AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) CMDLET 关闭 TDE。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- 使用[AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet 可以返回已添加到服务器的 Key Vault 项的列表。

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- 使用[AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey)从服务器中删除 Key Vault 密钥。

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

- 有关一般的数据库设置，请参阅[az sql](/cli/azure/sql)。

- 有关保管库密钥设置，请参阅[az sql server key](/cli/azure/sql/server/key)。

- 有关 TDE 设置，请参阅[az sql server TDE](/cli/azure/sql/server/tde-key)和[az sql db TDE](/cli/azure/sql/db/tde)。

* * *

## <a name="troubleshooting"></a>故障排除

如果出现问题，请查看以下内容：

- 如果找不到密钥保管库，请确保你处于正确的订阅。

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- 如果无法将新密钥添加到服务器，或无法将新密钥更新为 TDE 保护器，请检查以下项：
   - 密钥不应有过期日期
   - 密钥必须支持“获取”、“包装密钥”和“解包密钥”操作。

## <a name="next-steps"></a>后续步骤

- 了解如何轮换服务器的 TDE 保护器以符合安全要求：[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)。
- 了解如何在出现安全风险的情况下，删除可能已泄漏的 TDE 保护器：[删除可能已泄漏的密钥](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)。
