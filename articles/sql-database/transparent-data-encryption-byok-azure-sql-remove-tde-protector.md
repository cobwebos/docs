---
title: PowerShell - 删除 TDE 保护器 - Azure SQL 数据库 | Microsoft Docs
description: 有关使用支持“创建自己的密钥”(BYOK) 的 TDE，响应 Azure SQL 数据库或数据仓库可能已泄露的 TDE 保护器的操作方法指南。
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: feb187101ec02d6e765d6b025f518dc416f55b8b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043276"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 删除透明数据加密 (TDE) 保护器
## <a name="prerequisites"></a>先决条件
- 必须有一个 Azure 订阅，并且是该订阅的管理员
- 必须安装并运行 Azure PowerShell 4.2.0 或更高版本。 
- 本操作方法指南假设已使用 Azure Key Vault 中的密钥作为 Azure SQL 数据库或数据仓库的 TDE 保护器。 有关详细信息，请参阅[支持 BYOK 的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。

## <a name="overview"></a>概述
本操作方法指南介绍如何使用支持“创建自己的密钥”(BYOK) 的 TDE，响应 Azure SQL 数据库或数据仓库可能已泄露的 TDE 保护器。 若要详细了解 TDE 的 BYOK 支持，请参阅[概述页](transparent-data-encryption-byok-azure-sql.md)。 

只能在出现极端情况或者在测试环境中执行以下过程。 请仔细查看操作方法指南，因为从 Azure Key Vault 中删除正在使用的 TDE 保护器可能导致**数据丢失**。 

如果怀疑某个密钥已泄露，以致某个服务或用户在未经授权的情况下访问该密钥，则最好是删除该密钥。

请记住，在 Key Vault 中删除 TDE 保护器后，**将会阻止到该服务器中的加密数据库的所有连接，这些数据库会在 24 小时内脱机并被删除**。 使用已泄露的密钥加密的旧备份将不再可访问。

本操作方法指南根据响应事件后的所需结果演练两种方法：
- 使 Azure SQL 数据库/数据仓库保持**可访问**
- 使 Azure SQL 数据库/数据仓库**不可访问**

## <a name="to-keep-the-encrypted-resources-accessible"></a>使加密的资源保持可访问
1. [在 Key Vault 中创建新密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。 
2. 使用 [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) 和 [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet 将新密钥添加到服务器，并将其更新为服务器的新 TDE 保护器。

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. 确保使用 [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) cmdlet 将服务器和所有副本更新到新的 TDE 保护器。 

   >[!NOTE]
   > 将新 TDE 保护器传播到服务器中的所有数据库和辅助数据库可能需要几分钟时间。
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. 在 Key Vault 中创建[新密钥的备份](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)。

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. 使用 [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) cmdlet 从 Key Vault 中删除已泄露的密钥。 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. 将来若要使用 [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) cmdlet 将密钥还原到 Key Vault：
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>使加密的资源不可访问
1. 删除可能已泄露的密钥所加密的数据库。
数据库和日志文件会自动备份，因此，随时可对数据库执行时间点还原（只要提供密钥即可）。 必须在删除活动的 TDE 保护器之前删除数据库，以防止最近事务的数据丢失（最长 10 分钟的数据）。 
2. 在 Key Vault 中备份 TDE 保护器的密钥材料。
3. 从 Key Vault 中删除可能已泄露的密钥

## <a name="next-steps"></a>后续步骤

- 了解如何轮换服务器的 TDE 保护器以符合安全要求：[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- TDE 的“创建自己的密钥”支持入门：[通过 PowerShell 使用 Key Vault 中的自有密钥启用 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
