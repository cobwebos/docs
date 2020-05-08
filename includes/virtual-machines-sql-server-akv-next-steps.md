---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 9df08151e4af6e82a775b3ee99dab88134a2f032
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82784089"
---
## <a name="next-steps"></a>后续步骤

启用 Azure 密钥保管库集成之后，可以在 SQL VM 上启用 SQL Server 加密。 首先，需要在密钥保管库内创建一个非对称密钥，并在 VM 上的 SQL Server 中创建一个对称密钥。 然后，能够执行的 T-SQL 语句，以启用对数据库和备份的加密。

可以利用以下几种形式的加密：

* [透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [加密备份](https://msdn.microsoft.com/library/dn449489.aspx)
* [列级加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

以下 Transact-SQL 脚本提供针对每种形式的示例。

### <a name="prerequisites-for-examples"></a>先决条件示例

每个示例基于以下两个先决条件：密钥保管库中名为**CONTOSO_KEY**的非对称密钥，以及由名为**Azure_EKM_cred**的 AKV 集成功能创建的凭据。 以下 Transact-SQL 命令设置这些运行示例所需的先决条件。

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>透明数据加密 (TDE)

1. 创建数据库引擎用于 TDE 的 SQL Server 登录名，然后向其添加凭据。

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. 创建用于 TDE 的数据库加密密钥。

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>加密备份

1. 创建数据库引擎用于加密备份的 SQL Server 登录名，然后向其添加凭据。

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. 备份数据库，同时使用密钥保管库中存储的非对称密钥指定加密。

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>列级加密 (CLE)

此脚本创建一个受密钥保管库中的非对称密钥保护的对称密钥，并使用该对称密钥对数据库中的数据进行加密。

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>其他资源

有关如何使用这些加密功能的详细信息，请参阅[将 EKM 用于 SQL Server 加密功能](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM)。

请注意，本文中的步骤假定已经具有在 Azure 虚拟机上运行的 SQL Server。 如果没有，请参阅[在 Azure 中预配 SQL Server 虚拟机](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md)。 有关在 Azure VM 中运行 SQL Server 的其他指南，请参阅 [Azure 虚拟机上的 SQL Server 概述](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)。
