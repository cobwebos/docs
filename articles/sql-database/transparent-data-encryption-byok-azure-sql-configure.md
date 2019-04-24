---
title: PowerShell 和 CLI：启用 SQL TDE - 使用 Azure Key Vault - 自带密钥 - Azure SQL 数据库 | Microsoft Docs
description: 了解如何配置 Azure SQL 数据库和数据仓库，以开始使用透明数据加密 (TDE) 通过 PowerShell 或 CLI 进行静态加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330867"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell 和 CLI：使用 Azure Key Vault 中由客户管理的密钥启用透明数据加密

本文逐步介绍如何使用 Azure Key Vault 中的密钥对 SQL 数据库或数据仓库启用透明数据加密 (TDE)。 要了解更多关于 TDE 与 Azure Key Vault 集成（即自带密钥 (BYOK) 支持）的信息，请访问[使用 Azure Key Vault 中由客户管理的密钥进行 TDE](transparent-data-encryption-byok-azure-sql.md)。 

## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

- 必须有一个 Azure 订阅，并且是该订阅的管理员。
- [建议但可选] 提供硬件安全模块 (HSM) 或本地密钥存储，以便创建 TDE 保护器密钥材料的本地副本。
- 必须安装并运行 Azure PowerShell。 
- 创建用于 TDE 的 Azure Key Vault 和密钥。
  - [Key Vault 的 PowerShell 说明](../key-vault/key-vault-overview.md)
  - [有关使用硬件安全模块 (HSM) 和 Key Vault 的说明](../key-vault/key-vault-hsm-protected-keys.md)
    - Key Vault 必须包含用于 TDE 的以下属性：
  - [软删除](../key-vault/key-vault-ovw-soft-delete.md)
  - [如何将 Key Vault 软删除与 PowerShell 配合使用](../key-vault/key-vault-soft-delete-powershell.md) 
- 密钥必须包含用于 TDE 的以下特性：
   - 无过期日期
   - 未禁用
   - 能够执行“获取”、“包装密钥”和“解包密钥”操作

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>步骤 1。 将 Azure AD 标识分配到服务器 

如果有现有的服务器，请使用以下命令将 Azure AD 标识添加到该服务器：

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

如果正在创建服务器，请在创建服务器期间，结合 -Identity 标记使用 [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet 来添加 Azure AD 标识：

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>步骤 2. 向服务器授予 Key Vault 权限

将 Key Vault 中的密钥用于 TDE 之前，请使用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 向服务器授权 Key Vault 的访问权限。

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>步骤 3. 将 Key Vault 密钥添加到服务器并设置 TDE 保护器

- 使用 [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet 将 Key Vault 中的密钥添加到服务器。
- 使用 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 将密钥设置为所有服务器资源的 TDE 保护器。
- 使用 [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 确认已按预期配置了 TDE 保护器。

> [!Note]
> Key Vault 名称和密钥名称的总长度不能超过 94 个字符。
> 

>[!Tip]
>Key Vault 中的示例 KeyId： https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>步骤 4. 启用 TDE 

使用 [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 来启用 TDE。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

现已使用 Key Vault 中的加密密钥为数据库或数据仓库启用了 TDE。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>步骤 5。 检查加密状态和加密活动

使用 [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) 获取加密状态，使用 [Get- AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) 检查数据库或数据仓库的加密进度。

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>其他有用的 PowerShell cmdlet

- 使用 [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 来禁用 TDE。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- 使用 [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet 可返回已添加到服务器的 Key Vault 密钥列表。

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- 使用 [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) 可从服务器中删除 Key Vault 密钥。

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>故障排除

如果出现问题，请查看以下内容：
- 如果找不到 Key Vault，请使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet 来确认是否在正确的订阅中操作。

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- 如果无法将新密钥添加到服务器，或无法将新密钥更新为 TDE 保护器，请检查以下项：
   - 密钥不应有过期日期
   - 密钥必须支持“获取”、“包装密钥”和“解包密钥”操作。

## <a name="next-steps"></a>后续步骤

- 了解如何轮换服务器的 TDE 保护器以符合安全要求：[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)。
- 了解如何在出现安全风险的情况下，删除可能已泄漏的 TDE 保护器：[删除可能已泄漏的密钥](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)。 

## <a name="prerequisites-for-cli"></a>CLI 先决条件

- 必须有一个 Azure 订阅，并且是该订阅的管理员。
- [建议但可选] 提供硬件安全模块 (HSM) 或本地密钥存储，以便创建 TDE 保护器密钥材料的本地副本。
- 命令行接口版本 2.0 或更高版本。 若要安装最新版本并连接到 Azure 订阅，请参阅[安装和配置 Azure 跨平台命令行接口 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
- 创建用于 TDE 的 Azure Key Vault 和密钥。
  - [使用 CLI 2.0 管理 Key Vault](../key-vault/key-vault-manage-with-cli2.md)
  - [有关使用硬件安全模块 (HSM) 和 Key Vault 的说明](../key-vault/key-vault-hsm-protected-keys.md)
    - Key Vault 必须包含用于 TDE 的以下属性：
  - [软删除](../key-vault/key-vault-ovw-soft-delete.md)
  - [如何将 Key Vault 软删除与 CLI 配合使用](../key-vault/key-vault-soft-delete-cli.md) 
- 密钥必须包含用于 TDE 的以下特性：
   - 无过期日期
   - 未禁用
   - 能够执行“获取”、“包装密钥”和“解包密钥”操作
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>步骤 1。 创建具有 Azure AD 标识的服务器
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>防止“principalID”创建服务器，它是用于在下一步中分配密钥保管库权限的对象 ID
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>步骤 2. 向 SQL 逻辑服务器授予密钥保管库权限
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>保留新密钥的密钥 URI 或 keyID 以用于下一步，例如： https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>步骤 3. 将 Key Vault 密钥添加到服务器并设置 TDE 保护器
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Key Vault 名称和密钥名称的总长度不能超过 94 个字符。
> 

  
## <a name="step-4-turn-on-tde"></a>步骤 4. 启用 TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

现已使用 Azure Key Vault 中客户托管的加密密钥为数据库或数据仓库启用了 TDE。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>步骤 5。 检查加密状态和加密活动

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL CLI 参考

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

