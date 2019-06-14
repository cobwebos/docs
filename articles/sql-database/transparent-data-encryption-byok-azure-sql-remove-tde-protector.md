---
title: PowerShell - 删除 TDE 保护器 - Azure SQL 数据库 | Microsoft Docs
description: 有关使用支持“创建自己的密钥”(BYOK) 的 TDE，响应 Azure SQL 数据库或数据仓库可能已泄露的 TDE 保护器的操作方法指南。
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
ms.openlocfilehash: ad7e760bf84ee08e3928164432564fb23c10d211
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330646"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 删除透明数据加密 (TDE) 保护器

## <a name="prerequisites"></a>必备组件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

- 必须有一个 Azure 订阅，并且是该订阅的管理员
- 必须安装并运行 Azure PowerShell。 
- 本操作方法指南假设已使用 Azure Key Vault 中的密钥作为 Azure SQL 数据库或数据仓库的 TDE 保护器。 有关详细信息，请参阅[支持 BYOK 的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。

## <a name="overview"></a>概述

本操作指南介绍如何针对使用 TDE（支持“Azure 密钥保管库中的客户托管密钥 - 创建自己的密钥 [BYOK]”）的 Azure SQL 数据库或数据仓库，响应可能已泄露的 TDE 保护器。 若要详细了解 TDE 的 BYOK 支持，请参阅[概述页](transparent-data-encryption-byok-azure-sql.md)。 

只能在出现极端情况或者在测试环境中执行以下过程。 请仔细查看操作方法指南，因为从 Azure Key Vault 中删除正在使用的 TDE 保护器可能导致**数据丢失**。 

如果怀疑某个密钥已泄露，以致某个服务或用户在未经授权的情况下访问该密钥，则最好是删除该密钥。

请记住，在 Key Vault 中删除 TDE 保护器后，**将会阻止到该服务器中的加密数据库的所有连接，这些数据库会在 24 小时内脱机并被删除**。 使用已泄露的密钥加密的旧备份将不再可访问。

以下步骤概述了如何检查给定数据库的虚拟日志文件 (VLF) 仍在使用的 TDE 保护程序指纹。 可以通过运行以下命令来查找数据库的当前 TDE 保护程序的指纹：SELECT [database_id],        [encryption_state], [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ [encryptor_thumbprint], FROM [sys].[dm_database_encryption_keys] 
 
下面的查询返回 VLF 和加密程序各自使用的指纹。 每个不同的指纹引用 Azure Key Vault (AKV) 中的不同密钥：SELECT * FROM sys.dm_db_log_info (database_id) 

PowerShell 命令 Get-AzureRmSqlServerKeyVaultKey 提供查询中使用的 TDE 保护程序的指纹，因此你可以查看要在 AKV 中保留哪些密钥以及删除哪些密钥。 只能放心地从 Azure Key Vault 中删除数据库不再使用的密钥。

本操作方法指南根据响应事件后的所需结果演练两种方法：

- 使 Azure SQL 数据库/数据仓库保持**可访问**
- 使 Azure SQL 数据库/数据仓库**不可访问**

## <a name="to-keep-the-encrypted-resources-accessible"></a>使加密的资源保持可访问

1. [在 Key Vault 中创建新密钥](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。
2. 使用 [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 和 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 将新密钥添加到服务器，并将其更新为服务器的新 TDE 保护器。

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. 确保使用 [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 将服务器和所有副本更新到新的 TDE 保护器。 

   >[!NOTE]
   > 将新 TDE 保护器传播到服务器中的所有数据库和辅助数据库可能需要几分钟时间。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. 在 Key Vault 中创建[新密钥的备份](/powershell/module/az.keyvault/backup-azkeyvaultkey)。

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. 使用 [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet 从密钥保管库中删除已泄露的密钥。 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. 将来若要使用 [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet 将密钥还原到 Key Vault：
   ```powershell
   Restore-AzKeyVaultKey `
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
- 开始使用 TDE 的自带密钥支持：[通过 PowerShell 并使用 Key Vault 中自己的密钥来启用 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
