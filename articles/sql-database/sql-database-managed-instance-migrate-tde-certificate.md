---
title: 迁移 TDE 证书 - Azure SQL 数据库托管实例 | Microsoft Docs
description: 将用于通过透明数据加密保护数据库加密密钥的证书迁移到 Azure SQL 托管实例
keywords: sql 数据库教程, sql 数据库托管实例, 迁移 TDE 证书
services: sql-database
author: MladjoA
ms.reviewer: carlrab, jovanpop
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: mlandzic
manager: craigg
ms.openlocfilehash: 042d89017db898102deafc9156cf847a08c92227
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39074180"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-managed-instance"></a>将 TDE 保护的数据库的证书迁移到 Azure SQL 托管实例

使用本机还原选项将[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)保护的数据库迁移到 Azure SQL 托管实例时，需在还原数据库之前迁移本地或 IaaS SQL Server 中的相应证书。 本文引导你完成将证书手动迁移到 Azure SQL 数据库托管实例的过程：

> [!div class="checklist"]
> * 将证书导出到个人信息交换 (.pfx) 文件
> * 将文件中的证书提取成 base-64 字符串
> * 使用 PowerShell cmdlet 上传证书

有关使用完全托管服务无缝迁移 TDE 保护的数据库和相应证书的替代选项，请参阅[如何使用 Azure 数据库迁移服务将本地数据库迁移到托管实例](../dms/tutorial-sql-server-to-managed-instance.md)。

> [!IMPORTANT]
> Azure SQL 托管实例的透明数据加密在服务托管模式下工作。 迁移的证书仅用于还原 TDE 保护的数据库。 还原后不久，迁移的证书即会替换为不同的系统托管证书。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要符合以下先决条件：

- 已在本地服务器上，或者有权访问导出为文件的证书的计算机上，安装了 [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) 命令行工具。 Pvk2Pfx 工具是[企业 Windows 驱动程序工具包](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk)（一个独立的自包含性命令行环境）的一部分。
- 已安装 [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) 5.0 或更高版本。
- [已安装并更新](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) AzureRM PowerShell 模块。\[AzureRM.Sql 模块](https://www.powershellgallery.com/packages/AzureRM.Sql) 4.10.0 或更高版本。
- 在 PowerShell 中运行以下命令，以安装/更新 PowerShell 模块：

   ```powershell
   Install-Module -Name AzureRM.Sql
   Update-Module -Name AzureRM.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>将 TDE 证书导出到个人信息交换 (.pfx) 文件

可直接从源 SQL Server 导出证书，或者从证书存储导出（如果证书保存在证书存储中）。

### <a name="export-certificate-from-the-source-sql-server"></a>从源 SQL Server 导出证书

在 SQL Server Management Studio 中使用以下步骤导出证书，并将其转换为 pfx 格式。 以下各个步骤使用 *TDE_Cert* 和 *full_path* 作为证书名称（文件名）和路径。 应将它们替换为实际名称。

1. 在 SSMS 中打开新的查询窗口，并连接到源 SQL Server。
2. 使用以下脚本列出 TDE 保护的数据库，并获取用于对要迁移的数据库进行保护加密的证书的名称：

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![TDE 证书列表](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. 执行以下脚本，将证书导出到一对文件（.cer 和 .pvk），并保存公钥和私钥信息：

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![备份 TDE 证书](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. 在 PowerShell 控制台中使用 Pvk2Pfx 工具，将证书信息从一对新建文件复制到个人信息交换 (.pfx) 文件：

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>从证书存储导出证书

如果证书保存在 SQL Server 的本地计算机证书存储中，可使用以下步骤导出该证书：

1. 打开 PowerShell 控制台并执行以下命令，打开 Microsoft 管理控制台的“证书”管理单元：

   ```powershell
   certlm
   ```

2. 在“证书”MMC 管理单元中，展开路径“个人”->“证书”以查看证书列表

3. 右键单击证书，然后单击“导出...”

4. 遵照向导将证书和私钥导出为个人信息交换格式

## <a name="extract-certificate-from-file-to-base-64-string"></a>将文件中的证书提取成 base-64 字符串

在 PowerShell 中执行以下脚本，并获取 base-64 编码的证书作为输出：

```powershell
$fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
$base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
echo $base64EncodedCert
```

## <a name="upload-certificate-to-azure-sql-managed-instance-using-azure-powershell-cmdlet"></a>使用 Azure PowerShell cmdlet 将证书上传到 Azure SQL 托管实例

1. 在 PowerShell 中开始准备步骤：

   ```powershell
   # Import the module into the PowerShell session
   Import-Module AzureRM
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzureRmAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzureRmSubscription
   # Set subscription for the session
   Select-AzureRmSubscription Guid_Subscription_Id
   ```

2. 完成所有准备步骤后，运行以下命令将 base-64 编码的证书上传到目标托管实例：

   ```powershell
   $privateBlob = "<base-64-encoded-certificate-string>"
   $securePrivateBlob = $privateBlob  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzureRmSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

现在，该证书可在指定的托管实例中使用，并且可以成功还原相应的 TDE 保护数据库的备份。

## <a name="next-steps"></a>后续步骤

本文已介绍如何将用于通过透明数据加密保护数据库加密密钥的证书从本地或 IaaS SQL Server 迁移到 Azure SQL 托管实例。

请参阅[将数据库备份还原到 Azure SQL 数据库托管实例](sql-database-managed-instance-restore-from-backup-tutorial.md)，了解如何将数据库备份还原到 Azure SQL 数据库托管实例。
