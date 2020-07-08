---
title: 迁移 TDE 证书 - 托管实例
description: 使用 Azure SQL 托管实例，将保护数据库的数据库加密密钥的证书迁移透明数据加密
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: c9a9b42d6f6d8c89847b03f5eda858c75d198c58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711385"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>将受 TDE 保护的数据库的证书迁移到 Azure SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

使用 "本机还原" 选项将[透明数据加密（TDE）](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)保护的数据库迁移到 Azure SQL 托管实例时，需要在数据库还原之前迁移 SQL Server 实例中的相应证书。 本文逐步讲解如何将证书手动迁移到 Azure SQL 托管实例：

> [!div class="checklist"]
>
> * 将证书导出到个人信息交换（.pfx）文件
> * 将证书从文件提取到64字符串
> * 使用 PowerShell cmdlet 上传

有关使用完全托管的服务进行 TDE 保护的数据库和相应证书的无缝迁移的替代选项，请参阅[如何使用 Azure 数据库迁移服务将本地数据库迁移到 AZURE SQL 托管实例](../../dms/tutorial-sql-server-to-managed-instance.md)。

> [!IMPORTANT]
> 迁移的证书仅用于还原受 TDE 保护的数据库。 还原完成后，已迁移的证书会被另一个保护程序（服务托管证书或密钥保管库中的非对称密钥）替换为该实例上设置的 TDE 类型。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要符合以下先决条件：

* 已在本地服务器上，或者有权访问导出为文件的证书的计算机上，安装了 [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) 命令行工具。 Pvk2Pfx 工具是[企业 Windows 驱动程序工具包](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk)的一部分，它是一个自包含命令行环境。
* 已安装 [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) 5.0 或更高版本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

确保做好以下准备：

* [已安装并更新](https://docs.microsoft.com/powershell/azure/install-az-ps) Azure PowerShell 模块。
* [Az.Sql 模块](https://www.powershellgallery.com/packages/Az.Sql)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL 托管实例仍支持 PowerShell Azure 资源管理器模块，但所有将来的开发都适用于 Az .Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRM 模块中的命令的参数完全相同。

在 PowerShell 中运行以下命令，以安装/更新模块：

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>将 TDE 证书导出到 .pfx 文件

可以直接从源 SQL Server 实例导出证书，也可以从证书存储中导出证书（如果正在保存）。

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>从源 SQL Server 导出证书

使用以下步骤可将证书导出 SQL Server Management Studio 并将其转换为 .pfx 格式。 一般名称*TDE_Cert*和*full_path*用于通过步骤的证书和文件名称和路径。 应将它们替换为实际名称。

1. 在 SSMS 中，打开新的查询窗口并连接到源 SQL Server 实例。

1. 使用以下脚本列出受 TDE 保护的数据库，并获取证书的名称以保护要迁移的数据库：

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

   ![TDE 证书的列表](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. 执行以下脚本，将证书导出到一对文件（.cer 和 .pvk），并保存公钥和私钥信息：

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

   ![备份 TDE 证书](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. 使用 PowerShell 控制台将证书信息从一对新创建的文件复制到 .pfx 文件，使用 Pvk2Pfx 工具：

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>从证书存储中导出证书

如果证书保存在 "SQL Server 本地计算机" 证书存储中，则可以使用以下步骤将其导出：

1. 打开 PowerShell 控制台并执行以下命令，以打开 Microsoft 管理控制台中的 "证书" 管理单元：

   ```cmd
   certlm
   ```

2. 在 "证书" MMC 管理单元中，展开 "个人 > 证书" 路径以查看证书列表。

3. 右键单击该证书，然后单击 "**导出**"。

4. 按照向导将证书和私钥导出到 .pfx 格式。

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>使用 Azure PowerShell cmdlet 将证书上传到 Azure SQL 托管实例

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 在 PowerShell 中开始准备步骤：

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. 完成所有准备步骤后，运行以下命令，将64编码的证书上载到目标托管实例：

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

需要首先使用 *.pfx*文件[设置 Azure 密钥保管库](/azure/key-vault/key-vault-manage-with-cli2)。

1. 在 PowerShell 中开始准备步骤：

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. 完成所有准备步骤后，运行以下命令，将基本-64 编码的证书上传到目标托管实例：

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

现在，该证书可用于指定的托管实例，并且相应的 TDE 保护的数据库的备份可以成功还原。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用透明数据加密从本地或 IaaS SQL Server 实例将保护数据库加密密钥的证书迁移到 Azure SQL 托管实例。

若要了解如何将数据库备份还原到 Azure SQL 托管实例，请参阅将[数据库备份还原到 AZURE sql 托管实例](restore-sample-database-quickstart.md)。
