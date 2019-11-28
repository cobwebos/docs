---
title: 使用 T-SQL 将 SQL ServerWindows 用户和组迁移到托管实例
description: 了解如何将 SQL Server 本地 Windows 用户和组迁移到托管实例
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227924"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>教程：使用 T-SQL DDL 语法将 SQL Server 本地 Windows 用户和组迁移到 Azure SQL 数据库托管实例

> [!NOTE]
> 本文中用于将用户和组迁移到托管实例的语法目前为**公共预览版**。

本文逐步讲解如何使用 T-SQL 语法将 SQL Server 中的本地 Windows 用户和组迁移到现有的 Azure SQL 数据库托管实例。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> - 创建 SQL Server 的登录名
> - 创建要迁移的测试数据库
> - 创建登录名、用户和角色
> - 将数据库备份和还原到托管实例 (MI)
> - 使用 ALTER USER 句法将用户手动迁移到 MI
> - 使用新的映射用户测试身份验证

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要符合以下先决条件：

- Windows 域已与 Azure Active Directory (Azure AD) 联合。
- 可访问 Active Directory 来创建用户/组。
- 本地环境中有一个现有的 SQL Server。
- 现有的托管实例。 请参阅[快速入门：创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
  - 必须使用托管实例中的 `sysadmin` 来创建 Azure AD 登录名。
- [为托管实例创建 Azure AD 管理员](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。
- 可以连接到网络中的托管实例。 有关更多信息，请参阅以下文章： 
    - [将应用程序连接到 Azure SQL 数据库托管实例](sql-database-managed-instance-connect-app.md)
    - [快速入门：配置从本地到 Azure SQL 数据库托管实例的点到站点连接](sql-database-managed-instance-configure-p2s.md)
    - [在 Azure SQL 数据库托管实例中配置公共终结点](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL 语法

以下 T-SQL DDL 语法用于支持将 SQL Server 本地 Windows 用户和组迁移到使用 Azure AD 身份验证的托管实例。

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>参数

_domainName_</br>
指定用户的域名。

_userName_</br>
指定在数据库中标识的用户的名称。

_= loginName\@domainName.com_</br>
将用户重新映射到 Azure AD 登录名

_groupName_</br>
指定在数据库中标识的组的名称。

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>第 1 部分：为 SQL Server 本地用户和组创建登录名

> [!IMPORTANT]
> 以下语法在 SQL Server 中创建用户和组登录名。 在执行以下语法之前，需确保该用户和组在 Active Directory (AD) 中存在。 </br> </br>
> 用户：testUser1、testGroupUser </br>
> 组：要迁移的 testGroupUser 需属于 AD 中的迁移组

以下示例在 SQL Server 中为域 _aadsqlmi_ 下名为 _testUser1_ 的帐户创建一个登录名。 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

创建一个数据库用于此次测试。

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>第 2 部分：创建 Windows 用户和组，然后添加角色和权限

使用以下语法创建测试用户。

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

检查用户权限：

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

创建一个角色，并将测试用户分配到此角色：

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

使用以下查询显示分配给特定角色的用户名：

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

使用以下语法创建一个组。 然后将该组添加到角色 `db_owner`。

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

使用以下语法创建一个测试表并添加一些数据：

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>第 3 部分：将各个用户数据库备份和还原到托管实例

参考[使用备份和还原复制数据库](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)一文创建迁移数据库的备份，或使用以下语法：

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

遵循[快速入门：将数据库还原到托管实例](sql-database-managed-instance-get-started-restore.md)。

## <a name="part-4-migrate-users-to-managed-instance"></a>第 4 部分：将用户迁移到托管实例

> [!NOTE]
> 创建后托管实例功能的 Azure AD 管理员已更改。 有关详细信息，请参阅[适用于 MI 的新 Azure AD 管理员功能](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi)。

执行 ALTER USER 命令在托管实例上完成迁移过程。

1. 使用托管实例的 Azure AD 管理员帐户登录到托管实例。 然后，使用以下语法在托管实例中创建 Azure AD 登录名。 有关详细信息，请参阅[教程：使用 Azure AD 服务器主体（登录名）确保 Azure SQL 数据库中托管实例的安全性](sql-database-managed-instance-aad-security-tutorial.md)。

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. 检查要迁移的数据库、表和主体是否正确。

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. 使用 ALTER USER 语法将本地用户映射到 Azure AD 登录名。

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. 使用 ALTER USER 语法将本地组映射到 Azure AD 登录名。

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>第 5 部分：测试 Azure AD 用户或组身份验证

使用 ALTER USER 语法以及事先已映射到 Azure AD 登录名的用户，来测试对托管实例的身份验证。
 
1. 以 `aadsqlmi\testUser1` 身份使用 MI 订阅登录到联合的 VM
1. 使用 SQL Server Management Studio (SSMS) 登录到使用 **Active Directory 集成**身份验证并要连接到数据库 `migration` 的托管实例。
    1. 也可以结合 SSMS 选项“Active Directory – 通用且具有 MFA 支持”使用 testUser1@aadsqlmi.net 凭据登录。  但是，在这种情况下无法使用单一登录机制，而必须键入密码。 不需要使用联合的 VM 登录到托管实例。
1. 作为角色成员 **SELECT** 的一部分，可以从 `test` 表中进行选择

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


使用 Windows 组 `migration` 的成员测试对托管实例的身份验证。 在迁移之前，用户 `aadsqlmi\testGroupUser` 应已添加到组 `migration` 中。

1. 以 `aadsqlmi\testGroupUser` 身份使用 MI 订阅登录到联合的 VM 
1. 结合使用 SSMS 和 **Active Directory 集成**身份验证连接到 MI 服务器与数据库 `migration`
    1. 也可以结合 SSMS 选项“Active Directory – 通用且具有 MFA 支持”使用 testGroupUser@aadsqlmi.net 凭据登录。  但是，在这种情况下无法使用单一登录机制，而必须键入密码。 不需要使用联合的 VM 登录到托管实例。 
1. 作为 `db_owner` 角色的一部分，可以创建一个新表。

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> 由于 Azure SQL 数据库的已知设计问题，以组成员的身份执行创建表 (create table) 语句将会失败并出现以下错误： </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> 目前的解决方法是使用现有的架构创建一个表，在上例中为 <dbo.new>

## <a name="next-steps"></a>后续步骤

- [教程：使用 DMS 将 SQL Server 脱机迁移到 Azure SQL 数据库托管实例](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)