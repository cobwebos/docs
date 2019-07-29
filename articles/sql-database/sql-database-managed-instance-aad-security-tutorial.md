---
title: 使用 Azure AD 服务器主体（登录名）确保 Azure SQL 数据库托管实例的安全性 | Microsoft Docs
description: 了解在 Azure SQL 数据库中保护托管实例的技术和功能，以及 Azure AD 服务器主体（登录名）的用法
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/20/2019
ms.openlocfilehash: 87bd22ec4f2cfae62d1f80284ad8346ca292d016
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567675"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>教程：使用 Azure AD 服务器主体（登录名）确保 Azure SQL 数据库中托管实例的安全性

托管实例几乎提供最新 SQL Server 本地（企业版）数据库引擎具备的所有安全功能：

- 在隔离的环境中限制访问
- 使用需要标识的身份验证机制（Azure AD、SQL 身份验证）
- 将授权与基于角色的成员身份和权限配合使用
- 启用安全功能

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> - 为托管实例创建 Azure Active Directory (AD) 服务器主体（登录名）
> - 将权限授予托管实例中的 Azure AD 服务器主体（登录名）
> - 基于 Azure AD 服务器主体（登录名）创建 Azure AD 用户
> - 向 Azure AD 用户分配权限并管理数据库安全性
> - 以 Azure AD 用户身份使用模拟
> - 以 Azure AD 用户身份使用跨数据库查询
> - 了解安全功能，例如威胁防护、审核、数据掩码和加密

> [!NOTE]
> 托管实例的 Azure AD 服务器主体（登录名）目前为**公共预览版**。

有关详细信息，请参阅 [Azure SQL 数据库托管实例概述](sql-database-managed-instance-index.yml)和[功能](sql-database-managed-instance.md)文章。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保具备以下先决条件：

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Azure SQL 数据库托管实例
  - 遵循以下文章：[快速入门：创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)
- 能够访问托管实例，并且[为托管实例预配了 Azure AD 管理员](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。 若要了解更多信息，请参阅以下文章：
    - [将应用程序连接到托管实例](sql-database-managed-instance-connect-app.md) 
    - [托管实例连接体系结构](sql-database-managed-instance-connectivity-architecture.md)
    - [使用 SQL 配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>限制对托管实例的访问

只能通过专用 IP 地址访问托管实例。 在托管实例网络的外部，无法通过任何服务终结点连接到托管实例。 与在隔离的 SQL Server 本地环境中非常类似，应用程序或用户需要访问托管实例网络 (VNet) 才能建立连接。 有关详细信息，请参阅[将应用程序连接到托管实例](sql-database-managed-instance-connect-app.md)。

> [!NOTE] 
> 由于只能在托管实例的 VNET 内部访问托管实例，因此 [SQL 数据库防火墙规则](sql-database-firewall-configure.md)不适用。 托管实例具有自身的[内置防火墙](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>使用 SSMS 为托管实例创建 Azure AD 服务器主体（登录名）

必须由充当 `sysadmin` 的标准 SQL Server 帐户（非 Azure AD）创建第一个 Azure AD 服务器主体（登录名）。 有关如何连接到托管实例的示例，请参阅以下文章：

- [快速入门：将 Azure VM 配置为连接到托管实例](sql-database-managed-instance-configure-vm.md)
- [快速入门：配置从本地到托管实例的点到站点连接](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> 用于设置托管实例的 Azure AD 管理员不可用于在托管实例中创建 Azure AD 服务器主体（登录名）。 必须使用充当 `sysadmin` 的 SQL Server 帐户创建第一个 Azure AD 服务器主体（登录名）。 Azure AD 服务器主体（登录名）的正式版推出后，即会去除这种暂时性限制。 如果尝试使用 Azure AD 管理员帐户创建登录名，将会看到以下错误：`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. 在 [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance) 中使用充当 `sysadmin` 的标准 SQL Server 帐户（非 Azure AD）登录到托管实例。

2. 在“对象资源管理器”中右键服务器，然后选择“新建查询”。  

3. 在查询窗口中，使用以下语法为本地 Azure AD 帐户创建登录名：

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    此示例为帐户 nativeuser@aadsqlmi.onmicrosoft.com 创建登录名。

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. 在工具栏上，选择“执行”以创建登录名。 

5. 执行以下 T-SQL 命令检查新添加的登录名：

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

有关详细信息，请参阅 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>授予权限以允许创建托管实例登录名

若要创建其他 Azure AD 服务器主体（登录名），必须向主体（SQL 或 Azure AD）授予 SQL Server 角色或权限。

### <a name="sql-authentication"></a>SQL 身份验证

- 如果登录名是 SQL 主体，则只有属于 `sysadmin` 角色的登录名才能使用 create 命令来为 Azure AD 帐户创建登录名。

### <a name="azure-ad-authentication"></a>Azure AD 身份验证

- 要使新建的 Azure AD 服务器主体（登录名）能够为其他 Azure AD 用户、组或应用程序创建其他登录名，请向新建的登录名授予 `sysadmin` 或 `securityadmin` 服务器角色。 
- 最起码需要向 Azure AD 服务器主体（登录名）授予 **ALTER ANY LOGIN** 权限才能让其创建其他 Azure AD 服务器主体（登录名）。 
- 默认情况下，向 master 数据库中新建的 Azure AD 服务器主体（登录名）授予的标准权限为：**CONNECT SQL** 和 **VIEW ANY DATABASE**。
- 可向托管实例中的多个 Azure AD 服务器主体（登录名）授予 `sysadmin` 服务器角色。

将登录名添加到 `sysadmin` 服务器角色：

1. 再次登录到托管实例，或通过充当 `sysadmin` 的 SQL 主体使用现有连接。

1. 在“对象资源管理器”中右键服务器，然后选择“新建查询”。  

1. 使用以下 T-SQL 语法向 Azure AD 服务器主体（登录名）授予 `sysadmin` 服务器角色：

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    以下示例向登录名 nativeuser@aadsqlmi.onmicrosoft.com 授予 `sysadmin` 服务器角色

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>使用 SSMS 创建其他 Azure AD 服务器主体（登录名）

创建 Azure AD 服务器主体（登录名）并向其提供 `sysadmin` 特权后，该登录名可以结合 **CREATE LOGIN** 使用 **FROM EXTERNAL PROVIDER** 子句创建其他登录名。

1. 在 SQL Server Management Studio 中使用 Azure AD 服务器主体（登录名）连接到托管实例。 输入托管实例主机名。 若要在 SSMS 中进行身份验证，可在使用 Azure AD 帐户登录时从三个选项中选择：

   - Active Directory - 支持 MFA 的通用方法
   - Active Directory - 密码
   - Active Directory - 集成 </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     有关详细信息，请参阅以下文章：[使用 SQL 数据库和 SQL 数据仓库进行通用身份验证（MFA 的 SSMS 支持）](sql-database-ssms-mfa-authentication.md)

1. 选择“Active Directory - 支持 MFA 的通用方法”。  此时会打开“多重身份验证(MFA)登录”窗口。 使用 Azure AD 密码登录。

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. 在 SSMS 的“对象资源管理器”中右键服务器，然后选择“新建查询”。  
1. 在查询窗口中，使用以下语法为另一个 Azure AD 帐户创建登录名：

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    此示例为 Azure AD 用户 bob@aadsqlmi.net 创建登录名。该用户的 aadsqlmi.net 域已与 Azure AD aadsqlmi.onmicrosoft.com 相联合。

    执行以下 T-SQL 命令。 联合的 Azure AD 帐户是本地 Windows 登录名和用户的托管实例替代项。

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. 使用 [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) 语法在托管实例中创建数据库。 在下一部分，此数据库将用于测试用户登录名。
    1. 在“对象资源管理器”中右键服务器，然后选择“新建查询”。  
    1. 在查询窗口中，使用以下语法创建名为 **MyMITestDB** 的数据库。

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. 为 Azure AD 中的某个组创建托管实例登录名。 将该登录名添加到托管实例之前，该组需要在 Azure AD 中存在。 请参阅[使用 Azure Active Directory 创建基本组并添加成员](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 创建组 _mygroup_ 并在其中添加成员。

1. 在 SQL Server Management Studio 中打开新的查询窗口。

    此示例假设 Azure AD 中存在名为 _mygroup_ 的组。 运行以下命令：

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. 为了进行测试，请使用新建的登录名或组登录到托管实例。 与托管实例建立新的连接，并在身份验证时使用新登录名。
1. 在“对象资源管理器”中右键服务器，然后选择新连接对应的“新建查询”。  
1. 执行以下命令，检查新建的 Azure AD 服务器主体（登录名）的服务器权限：

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> 对于 Azure AD 来宾用户，仅当已将其添加为 Azure AD 组的一部分时，才支持将其用于托管实例登录名。 Azure AD 来宾用户是指在另一个 Azure AD 中邀请其加入托管实例所属 Azure AD 的帐户。 例如，可将 joe@contoso.com（Azure AD 帐户）或 steve@outlook.com（MSA 帐户）添加到 Azure AD aadsqlmi 中的组。 将用户添加到组后，可以使用 **CREATE LOGIN** 语法在托管实例 **master** 数据库中为该组创建登录名。 属于此组的来宾用户可以使用其当前登录名（例如 joe@contoso.com 或 steve@outlook.com）连接到托管实例。

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>基于 Azure AD 服务器主体（登录名）创建 Azure AD 用户并授予权限

在托管实例中对单个数据库授权的方式非常类似于本地 SQL Server 的授权。 可以基于数据库中的现有登录名创建用户并为其提供对该数据库的权限，或者将该用户添加到数据库角色。

创建名为 **MyMITestDB** 的数据库以及一个只有默认权限的登录名后，下一步是基于该登录名创建用户。 目前，该登录名可以连接到托管实例和查看所有数据库，但无法与数据库交互。 如果使用具有默认权限的 Azure AD 帐户登录并尝试展开新建的数据库，将会看到以下错误：

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

有关授予数据库权限的详细信息，请参阅[数据库引擎权限入门](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)。

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>创建 Azure AD 用户并创建示例表

1. 在 SQL Server Management Studio 中使用 `sysadmin` 帐户登录到托管实例。
1. 在“对象资源管理器”中右键服务器，然后选择“新建查询”。  
1. 在查询窗口中，使用以下语法基于 Azure AD 服务器主体（登录名）创建 Azure AD 用户：

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    以下示例基于登录名 bob@aadsqlmi.net: 创建用户 bob@aadsqlmi.net。

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. 也支持基于组的形式的 Azure AD 服务器主体（登录名）创建 Azure AD 用户。

    以下示例为 Azure AD 中的 Azure AD 组 _mygroup_ 创建登录名。

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    属于 **mygroup** 的所有用户都可以访问 **MyMITestDB** 数据库。

    > [!IMPORTANT]
    > 基于 Azure AD 服务器主体（登录名）创建**用户**时，请指定与**登录名**中的 login_name 相同的 user_name。

    有关详细信息，请参阅 [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)。

1. 在新查询窗口中，使用以下 T-SQL 命令创建测试表：

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. 使用创建的用户在 SSMS 中创建连接。 你会注意到，无法看到 `sysadmin` 在以前创建的表 **TestTable**。 我们需要向该用户提供读取数据库中的数据的权限。

1. 可以执行以下命令来检查用户当前拥有的权限：

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>将用户添加到数据库级角色

要使用户能够查看数据库中的数据，我们可以向该用户提供[数据库级角色](/sql/relational-databases/security/authentication-access/database-level-roles)。

1. 在 SQL Server Management Studio 中使用 `sysadmin` 帐户登录到托管实例。

1. 在“对象资源管理器”中右键服务器，然后选择“新建查询”。  

1. 使用以下 T-SQL 语法向 Azure AD 用户授予 `db_datareader` 数据库角色：

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    以下示例为用户 bob@aadsqlmi.net 和组 _mygroup_ 提供对 **MyMITestDB** 数据库的 `db_datareader` 权限：

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. 执行以下命令，检查在数据库中创建的 Azure AD 用户是否存在：

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. 使用已添加到 `db_datareader` 角色的用户身份与托管实例建立新的连接。
1. 在“对象资源管理器”中展开数据库，以查看表。 

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. 打开新查询窗口并执行以下 SELECT 语句：

    ```sql
    SELECT *
    FROM TestTable
    ```

    是否能够看到表中的数据？ 应会看到返回的列。

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>模拟 Azure AD 服务器级主体（登录名）

托管实例支持模拟 Azure AD 服务器级主体（登录名）。

### <a name="test-impersonation"></a>测试模拟

1. 在 SQL Server Management Studio 中使用 `sysadmin` 帐户登录到托管实例。

1. 在“对象资源管理器”中右键服务器，然后选择“新建查询”。  

1. 在查询窗口中，使用以下命令创建新的存储过程：

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. 使用以下命令来查看执行该存储过程时模拟的用户是否为 bob\@aadsqlmi.net  。

    ```sql
    Exec dbo.usp_Demo
    ```

1. 使用 EXECUTE AS LOGIN 语句测试模拟：

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> 只有属于 `sysadmin` 角色的 SQL 服务器级主体（登录名）可以针对 Azure AD 主体执行以下操作： 
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>在托管实例中使用跨数据库查询

使用 Azure AD 服务器主体（登录名）的 Azure AD 帐户支持跨数据库查询。 若要使用 Azure AD 组测试跨数据库查询，需要创建另一个数据库和表。 如果已存在一个数据库和表，则无需额外创建。

1. 在 SQL Server Management Studio 中使用 `sysadmin` 帐户登录到托管实例。
1. 在“对象资源管理器”中右键服务器，然后选择“新建查询”。  
1. 在查询窗口中，使用以下命令创建名为 **MyMITestDB2** 的数据库和名为 **TestTable2** 的表。

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. 在新查询窗口中执行以下命令，在新数据库 **MyMITestDB2** 中创建用户 _mygroup_，并向 _mygroup_ 授予对该数据库的 SELECT 权限：

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. 使用 SQL Server Management Studio 以 Azure AD 组 _mygroup_ 的成员身份登录到托管实例。 打开新查询窗口并执行跨数据库 SELECT 语句：

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    应会看到 **TestTable2** 的表结果。

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins-public-preview"></a>Azure AD 服务器主体（登录名）（公共预览版）支持的其他方案 

- Azure AD 服务器主体（登录名）支持 SQL 代理管理和作业执行。
- Azure AD 服务器主体（登录名）可以执行数据库备份和还原操作。
- [审核](sql-database-managed-instance-auditing.md)与 Azure AD 服务器主体（登录名）和身份验证事件相关的所有语句。
- 属于 `sysadmin` 服务器角色成员的 Azure AD 服务器主体（登录名）可以建立专用管理员连接。
- 支持 Azure AD 服务器主体（登录名）使用 [sqlcmd 实用程序](/sql/tools/sqlcmd-utility)和 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 工具。
- 来自 Azure AD 服务器主体（登录名）的登录事件支持登录触发器。
- 可以使用 Azure AD 服务器主体（登录名）设置 Service Broker 和数据库邮件。


## <a name="next-steps"></a>后续步骤

### <a name="enable-security-features"></a>启用安全功能

请参阅[托管实例安全功能](sql-database-managed-instance.md#azure-sql-database-security-features)一文，全面了解保护数据库的各种方式。 此文介绍了以下安全功能：

- [托管实例审核](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [威胁检测](sql-database-managed-instance-threat-detection.md) 
- [动态数据屏蔽](/sql/relational-databases/security/dynamic-data-masking)
- [行级安全](/sql/relational-databases/security/row-level-security) 
- [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>托管实例功能

有关托管实例功能的完整概述，请参阅：

> [!div class="nextstepaction"]
> [托管实例功能](sql-database-managed-instance.md)
