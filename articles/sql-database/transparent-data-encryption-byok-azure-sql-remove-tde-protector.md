---
title: Remove TDE protector - PowerShell
description: 有关使用支持“创建自己的密钥”(BYOK) 的 TDE，响应 Azure SQL 数据库或数据仓库可能已泄露的 TDE 保护器的操作方法指南。
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
ms.openlocfilehash: 29971414219976f6d72caf30a909f1884b04aef7
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422419"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 删除透明数据加密 (TDE) 保护器

## <a name="prerequisites"></a>必备组件

- 必须有一个 Azure 订阅，并且是该订阅的管理员
- You must have Azure PowerShell installed and running.
- 本操作方法指南假设你已使用 Azure Key Vault 中的密钥作为 Azure SQL 数据库或数据仓库的 TDE 保护器。 有关详细信息，请参阅[支持 BYOK 的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 有关 Az 模块安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

For installation, see [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>概述

本操作指南介绍如何针对使用 TDE（支持“Azure 密钥保管库中的客户托管密钥 - 创建自己的密钥 [BYOK]”）的 Azure SQL 数据库或数据仓库，响应可能已泄露的 TDE 保护器。 若要详细了解 TDE 的 BYOK 支持，请参阅[概述页](transparent-data-encryption-byok-azure-sql.md)。

只能在出现极端情况或者在测试环境中执行以下过程。 请仔细查看操作方法指南，因为从 Azure Key Vault 中删除正在使用的 TDE 保护器可能导致**数据丢失**。

如果怀疑某个密钥已泄露，以致某个服务或用户在未经授权的情况下访问该密钥，则最好是删除该密钥。

请记住，在 Key Vault 中删除 TDE 保护器后，**将会阻止到该服务器中的加密数据库的所有连接，这些数据库会在 24 小时内脱机并被删除**。 使用已泄露的密钥加密的旧备份将不再可访问。

The following steps outline how to check the TDE Protector thumbprints still in use by Virtual Log Files (VLF) of a given database.
The thumbprint of the current TDE protector of the database, and the database ID can be found by running:

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

The following query returns the VLFs and the encryptor respective thumbprints in use. Each different thumbprint refers to different key in Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

The PowerShell command **Get-AzureRmSqlServerKeyVaultKey** provides the thumbprint of the TDE Protector used in the query, so you can see which keys to keep and which keys to delete in AKV. Only keys no longer used by the database can be safely deleted from Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

The PowerShell command **az sql server key show** provides the thumbprint of the TDE Protector used in the query, so you can see which keys to keep and which keys to delete in AKV. Only keys no longer used by the database can be safely deleted from Azure Key Vault.

* * *

本操作方法指南根据响应事件后的所需结果演练两种方法：

- 使 Azure SQL 数据库/数据仓库保持**可访问**
- 使 Azure SQL 数据库/数据仓库**不可访问**

## <a name="to-keep-the-encrypted-resources-accessible"></a>使加密的资源保持可访问

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. [在 Key Vault 中创建新密钥](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。

2. Add the new key to the server using the [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) and [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets and update it as the server’s new TDE protector.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Make sure the server and any replicas have updated to the new TDE protector using the [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet.

   > [!NOTE]
   > 将新 TDE 保护器传播到服务器中的所有数据库和辅助数据库可能需要几分钟时间。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. 在 Key Vault 中创建[新密钥的备份](/powershell/module/az.keyvault/backup-azkeyvaultkey)。

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Delete the compromised key from Key Vault using the [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. To restore a key to Key Vault in the future using the [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet:

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

For command reference, see the [Azure CLI keyvault](/cli/azure/keyvault/key).

1. [在 Key Vault 中创建新密钥](/cli/azure/keyvault/key#az-keyvault-key-create)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。

2. Add the new key to the server and update it as the server’s new TDE protector.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Make sure the server and any replicas have updated to the new TDE protector.

   > [!NOTE]
   > 将新 TDE 保护器传播到服务器中的所有数据库和辅助数据库可能需要几分钟时间。

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Take a backup of the new key in Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Delete the compromised key from Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. To restore a key to Key Vault in the future.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>使加密的资源不可访问

1. 删除可能已泄露的密钥所加密的数据库。

   数据库和日志文件会自动备份，因此，随时可对数据库执行时间点还原（只要提供密钥即可）。 必须在删除活动的 TDE 保护器之前删除数据库，以防止最近事务的数据丢失（最长 10 分钟的数据）。

2. 在 Key Vault 中备份 TDE 保护器的密钥材料。
3. 从 Key Vault 中删除可能已泄露的密钥

## <a name="next-steps"></a>后续步骤

- 了解如何轮换服务器的 TDE 保护器以符合安全要求：[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- TDE 的自带密钥支持入门：[通过 PowerShell 使用 Key Vault 中的自有密钥启用 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
