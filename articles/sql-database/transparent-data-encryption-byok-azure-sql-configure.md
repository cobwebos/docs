---
title: Enable SQL TDE with Azure Key Vault
description: 了解如何配置 Azure SQL 数据库和数据仓库，以开始使用透明数据加密 (TDE) 通过 PowerShell 或 CLI 进行静态加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: e60b83f8eb1e95733512180417bb2a1be679b0a0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422465"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell and CLI: Enable Transparent Data Encryption with customer-managed key from Azure Key Vault

本文逐步介绍如何使用 Azure Key Vault 中的密钥对 SQL 数据库或数据仓库启用透明数据加密 (TDE)。 要了解更多关于 TDE 与 Azure Key Vault 集成（即自带密钥 (BYOK) 支持）的信息，请访问[使用 Azure Key Vault 中由客户管理的密钥进行 TDE](transparent-data-encryption-byok-azure-sql.md)。

## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

- 必须有一个 Azure 订阅，并且是该订阅的管理员。
- [建议但可选] 提供硬件安全模块 (HSM) 或本地密钥存储，以便创建 TDE 保护器密钥材料的本地副本。
- You must have Azure PowerShell installed and running.
- 创建用于 TDE 的 Azure Key Vault 和密钥。
  - [有关使用硬件安全模块 (HSM) 和 Key Vault 的说明](../key-vault/key-vault-hsm-protected-keys.md)
    - Key Vault 必须包含用于 TDE 的以下属性：
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) and purge protection
- 密钥必须包含用于 TDE 的以下特性：
   - 无过期日期
   - 未禁用
   - 能够执行“获取”、“包装密钥”和“解包密钥”操作

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

有关 Az 模块安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

For specifics on Key Vault, see [PowerShell instructions from Key Vault](../key-vault/quick-create-powershell.md) and [How to use Key Vault soft-delete with PowerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>将 Azure AD 标识分配到服务器

如果有现有的服务器，请使用以下命令将 Azure AD 标识添加到该服务器：

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

If you are creating a server, use the [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet with the tag -Identity to add an Azure AD identity during server creation:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>向服务器授予 Key Vault 权限

Use the [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet to grant your server access to the key vault before using a key from it for TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>将 Key Vault 密钥添加到服务器并设置 TDE 保护器

- Use the [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet to retrieve the key ID from key vault
- Use the [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet to add the key from the Key Vault to the server.
- Use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet to set the key as the TDE protector for all server resources.
- Use the [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet to confirm that the TDE protector was configured as intended.

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

Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn on TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

现已使用 Key Vault 中的加密密钥为数据库或数据仓库启用了 TDE。

## <a name="check-the-encryption-state-and-encryption-activity"></a>检查加密状态和加密活动

Use the [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) to get the encryption state and the [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) to check the encryption progress for a database or data warehouse.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

To install the required Command-Line Interface version 2.0 or later and connect to your Azure subscription, see [Install and Configure the Azure Cross-Platform Command-Line Interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

For specifics on Key Vault, see [Manage Key Vault using CLI 2.0](../key-vault/key-vault-manage-with-cli2.md) and [How to use Key Vault soft-delete with CLI](../key-vault/key-vault-soft-delete-cli.md).

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

- Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn off TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Use the [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet to return the list of Key Vault keys added to the server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Use the [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) to remove a Key Vault key from the server.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

- For general database settings, see [az sql](/cli/azure/sql).

- For vault key settings, see [az sql server key](/cli/azure/sql/server/key).

- For TDE settings, see [az sql server tde-key](/cli/azure/sql/server/tde-key) and [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>故障排除

如果出现问题，请查看以下内容：

- If the key vault cannot be found, make sure you're in the right subscription.

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
