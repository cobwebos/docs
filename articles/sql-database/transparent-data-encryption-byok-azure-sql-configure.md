---
title: PowerShell 和 CLI：使用自己的密钥启用 SQL TDE - Azure SQL 数据库 | Microsoft Docs
description: 了解如何配置 Azure SQL 数据库和数据仓库，以开始使用透明数据加密 (TDE) 通过 PowerShell 或 CLI 进行静态加密。
services: sql-database
keywords: ''
documentationcenter: ''
author: aliceku
manager: craigg
editor: ''
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.devlang: azurecli, powershell
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0dd0d322bacd807dc0cc59aeeb854c427a745bca
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043281"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell 和 CLI：使用自己在 Azure Key Vault 中的密钥启用透明数据加密

本文逐步介绍如何使用 Azure Key Vault 中的密钥对 SQL 数据库或数据仓库启用透明数据加密 (TDE)。 若要详细了解支持“创建自己的密钥”(BYOK) 的 TDE，请访问[适用于 Azure SQL 的支持“创建自己的密钥”的 TDE](transparent-data-encryption-byok-azure-sql.md)。 

## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

- 必须有一个 Azure 订阅，并且是该订阅的管理员。
- [建议但可选] 提供硬件安全模块 (HSM) 或本地密钥存储，以便创建 TDE 保护器密钥材料的本地副本。
- 必须安装并运行 Azure PowerShell 4.2.0 或更高版本。 
- 创建用于 TDE 的 Azure Key Vault 和密钥。
   - [Key Vault 的 PowerShell 说明](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)
   - [有关使用硬件安全模块 (HSM) 和 Key Vault 的说明](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm)
 - Key Vault 必须包含用于 TDE 的以下属性：
   - [软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
   - [如何将 Key Vault 软删除与 PowerShell 配合使用](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) 
- 密钥必须包含用于 TDE 的以下特性：
   - 无过期日期
   - 未禁用
   - 能够执行“获取”、“包装密钥”和“解包密钥”操作

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>步骤 1。 将 Azure AD 标识分配到服务器 

如果有现有的服务器，请使用以下命令将 Azure AD 标识添加到该服务器：

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

如果正在创建服务器，请在创建服务器期间，结合 -Identity 标记使用 [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) cmdlet 来添加 Azure AD 标识：

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>步骤 2. 向服务器授予 Key Vault 权限

将 Key Vault 中的密钥用于 TDE 之前，请使用 [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet 向服务器授权 Key Vault 的访问权限。

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>步骤 3. 将 Key Vault 密钥添加到服务器并设置 TDE 保护器

- 使用 [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) cmdlet 将 Key Vault 中的密钥添加到服务器。
- 使用 [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet 将密钥设置为所有服务器资源的 TDE 保护器。
- 使用 [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) cmdlet 确认已按预期配置了 TDE 保护器。

> [!Note]
> Key Vault 名称和密钥名称的总长度不能超过 94 个字符。
> 

>[!Tip]
>Key Vault 中的示例 KeyId：https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>步骤 4. 启用 TDE 

使用 [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) cmdlet 来启用 TDE。

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

现已使用 Key Vault 中的加密密钥为数据库或数据仓库启用了 TDE。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>步骤 5。 检查加密状态和加密活动

使用 [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) 获取加密状态，使用 [Get- AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) 检查数据库或数据仓库的加密进度。

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>其他有用的 PowerShell cmdlet

- 使用 [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) cmdlet 可禁用 TDE。

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- 使用 [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) cmdlet 可返回已添加到服务器的 Key Vault 密钥列表。

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- 使用 [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) 可从服务器中删除 Key Vault 密钥。

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>故障排除

如果出现问题，请查看以下内容：
- 如果找不到 Key Vault，请使用 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) cmdlet 来确认是否在正确的订阅中操作。

   ```powershell
   Get-AzureRmSubscription `
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
- 命令行接口版本 2.0 或更高版本。 若要安装最新版本并连接到 Azure 订阅，请参阅[安装和配置 Azure 跨平台命令行接口 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 
- 创建用于 TDE 的 Azure Key Vault 和密钥。
   - [使用 CLI 2.0 管理 Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2)
   - [有关使用硬件安全模块 (HSM) 和 Key Vault 的说明](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm)
 - Key Vault 必须包含用于 TDE 的以下属性：
   - [软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
   - [如何将 Key Vault 软删除与 CLI 配合使用](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli) 
- 密钥必须包含用于 TDE 的以下特性：
   - 无过期日期
   - 未禁用
   - 能够执行“获取”、“包装密钥”和“解包密钥”操作
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>步骤 1。 创建服务器并将 Azure AD 标识分配到服务器
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -I 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>步骤 2. 向服务器授予 Key Vault 权限
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>步骤 3. 将 Key Vault 密钥添加到服务器并设置 TDE 保护器
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> Key Vault 名称和密钥名称的总长度不能超过 94 个字符。
> 

>[!Tip]
>Key Vault 中的示例 KeyId：https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>步骤 4. 启用 TDE 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

现已使用 Key Vault 中的加密密钥为数据库或数据仓库启用了 TDE。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>步骤 5。 检查加密状态和加密活动

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>SQL CLI 参考

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

