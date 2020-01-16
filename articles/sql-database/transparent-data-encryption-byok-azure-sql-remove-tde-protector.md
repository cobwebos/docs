---
title: 删除 TDE 保护程序-PowerShell
description: 有关使用支持“创建自己的密钥”(BYOK) 的 TDE，响应 Azure SQL 数据库或数据仓库可能已泄露的 TDE 保护器的操作方法指南。
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
ms.openlocfilehash: ba591872f4f8af93e5f7e13e0fb69d0679fd1a0c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965754"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 删除透明数据加密 (TDE) 保护器

## <a name="prerequisites"></a>必备组件

- 必须有一个 Azure 订阅，并且是该订阅的管理员
- 必须安装并运行 Azure PowerShell。
- 本操作方法指南假设已使用 Azure Key Vault 中的密钥作为 Azure SQL 数据库或数据仓库的 TDE 保护器。 有关详细信息，请参阅[支持 BYOK 的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 有关 Az 模块安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 有关特定的 cmdlet，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。

> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell Azure 资源管理器（RM）模块，但所有将来的开发都适用于 Az .Sql 模块。 AzureRM 模块将继续收到 bug 修复，直到至少12月2020。  Az 模块和 AzureRm 模块中的命令的参数完全相同。 有关其兼容性的详细信息，请参阅[新 Azure PowerShell Az Module 简介](/powershell/azure/new-azureps-module-az)。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

有关安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="overview"></a>概述

本操作指南介绍如何针对使用 TDE（支持“Azure 密钥保管库中的客户托管密钥 - 创建自己的密钥 [BYOK]”）的 Azure SQL 数据库或数据仓库，响应可能已泄露的 TDE 保护器。 若要详细了解 TDE 的 BYOK 支持，请参阅[概述页](transparent-data-encryption-byok-azure-sql.md)。

只能在出现极端情况或者在测试环境中执行以下过程。 请仔细查看操作方法指南，因为从 Azure Key Vault 中删除正在使用的 TDE 保护器可能导致**数据丢失**。

如果怀疑某个密钥已泄露，以致某个服务或用户在未经授权的情况下访问该密钥，则最好是删除该密钥。

请记住，在 Key Vault 中删除 TDE 保护器后，**将会阻止到该服务器中的加密数据库的所有连接，这些数据库会在 24 小时内脱机并被删除**。 使用已泄露的密钥加密的旧备份将不再可访问。

以下步骤概述了如何检查给定数据库的虚拟日志文件（VLF）仍在使用的 TDE 保护程序指纹。
数据库的当前 TDE 保护程序的指纹，可以通过运行以下内容查找数据库 ID：

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

以下查询将返回所使用的 Vlf 和加密器各自的指纹。 每个不同的指纹是指 Azure Key Vault （AKV）中的不同密钥：

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 命令**add-azurermsqlserverkeyvaultkey** 提供查询中使用的 TDE 保护程序的指纹，因此你可以查看要保留哪些项以及要在 AKV 中删除哪些键。 可以安全地从 Azure Key Vault 删除数据库中不再使用的密钥。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 命令**az sql server key show** 提供查询中使用的 TDE 保护程序的指纹，因此你可以查看要保留哪些键以及要在 AKV 中删除哪些键。 可以安全地从 Azure Key Vault 删除数据库中不再使用的密钥。

* * *

本操作方法指南根据响应事件后的所需结果演练两种方法：

- 使 Azure SQL 数据库/数据仓库保持**可访问**
- 使 Azure SQL 数据库/数据仓库**不可访问**

## <a name="to-keep-the-encrypted-resources-accessible"></a>使加密的资源保持可访问

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. [在 Key Vault 中创建新密钥](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。

2. 使用[AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)和[AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 将新密钥添加到服务器，并将其更新为服务器的新 TDE 保护程序。

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. 请确保使用[AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 将服务器和任何副本更新为新的 TDE 保护程序。

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

5. 使用[AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet 从 Key Vault 中删除泄露的密钥。

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 使用[AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet 将密钥还原到以后 Key Vault：

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

有关命令参考，请参阅[Azure CLI keyvault](/cli/azure/keyvault/key)。

1. [在 Key Vault 中创建新密钥](/cli/azure/keyvault/key#az-keyvault-key-create)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。

2. 将新密钥添加到服务器，并将其更新为服务器的新 TDE 保护程序。

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 请确保已将服务器和任何副本更新为新的 TDE 保护程序。

   > [!NOTE]
   > 将新 TDE 保护器传播到服务器中的所有数据库和辅助数据库可能需要几分钟时间。

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. 在 Key Vault 中备份新密钥。

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. 从 Key Vault 中删除泄露的密钥。

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 还原密钥以便在将来 Key Vault。

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
- TDE 的“创建自己的密钥”支持入门：[通过 PowerShell 使用 Key Vault 中的自有密钥启用 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
