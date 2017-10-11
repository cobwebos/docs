## <a name="next-steps"></a>后续步骤

在启用 Azure 密钥保管库集成后，你可以在 SQL VM 上启用 SQL Server 加密。 首先，你将需要在 VM 上创建密钥保管库内的非对称密钥和 SQL Server 中的对称密钥。 然后，你将能够执行 T-SQL 语句，以启用对数据库和备份的加密。

有几种形式的加密可以充分利用：

* [透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [加密的备份](https://msdn.microsoft.com/library/dn449489.aspx)
* [列级加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

下面的 TRANSACT-SQL 脚本提供的各个方面的示例。

### <a name="prerequisites-for-examples"></a>有关示例的先决条件

每个示例基于两个先决条件： 密钥保管库的非对称密钥调用**CONTOSO_KEY**和通过 AKV 集成功能创建的凭据调用**Azure_EKM_TDE_cred**。 以下 TRANSACT-SQL 命令安装这些先决条件运行示例。

``` sql
USE master;
GO

sp_configure [show advanced options], 1;
GO
RECONFIGURE;
GO

-- Enable EKM provider
sp_configure [EKM provider enabled], 1;
GO
RECONFIGURE;

--create provider
CREATE CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov
FROM FILE = 'C:\Program Files\SQL Server Connector for Microsoft Azure Key Vault\Microsoft.AzureKeyVaultService.EKM.dll';
GO

--create credential
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--must have sysadmin
ALTER LOGIN [TDE_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'keytestvault',  --key name
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>透明数据加密 (TDE)

1. 创建由 TDE，数据库引擎的 SQL Server 登录名，然后向其添加凭据。

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN TDE_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN TDE_Login
   ADD CREDENTIAL Azure_EKM_TDE_cred;
   GO
   ```

1. 创建将用于 TDE 的数据库加密密钥。

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

### <a name="encrypted-backups"></a>加密的备份

1. 创建的 SQL Server 登录名，以供数据库引擎的加密备份文件，并向其添加凭据。

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN Backup_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN Backup_Login
   ADD CREDENTIAL Azure_EKM_Backup_cred ;
   GO
   ```

1. 备份数据库，指定加密使用密钥保管库中存储的非对称密钥。

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>列级加密 (CLE)

此脚本创建一个受 key vault 中的非对称密钥的对称密钥，，然后使用对称密钥来加密数据库中的数据。

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

有关如何使用这些加密功能的详细信息，请参阅[与 SQL Server 加密功能使用 EKM](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM)。

请注意，这篇文章中的步骤假定你已在 Azure 虚拟机上运行的 SQL Server。 如果没有，请参阅[设置 Azure 中的 SQL Server 虚拟机](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md)。 有关在 Azure Vm 上运行 SQL Server 的其他指导，请参阅[SQL Server Azure 虚拟机概述](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)。