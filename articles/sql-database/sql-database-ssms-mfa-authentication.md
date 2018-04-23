---
title: 多重身份验证 - Azure SQL | Microsoft Docs
description: Azure SQL 数据库和 Azure SQL 数据仓库支持使用 Active Directory 通用身份验证，从 SQL Server Management Studio (SSMS) 进行连接。
services: sql-database
documentationcenter: ''
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: mireks
ms.openlocfilehash: 8c6472dcde55a7b642ced7bbf0d93893df9208e0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>使用 SQL 数据库和 SQL 数据仓库进行通用身份验证（MFA 的 SSMS 支持）
Azure SQL 数据库和 Azure SQL 数据仓库支持使用 Active Directory 通用身份验证，从 SQL Server Management Studio (SSMS) 进行连接。 
**下载最新 SSMS** - 在客户端计算机上，从[下载 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下载最新版本的 SSMS。 对于本主题中的所有功能，请至少使用 2017 年 7 月的版本 17.2。  最新连接对话框如下所示：![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "完成“用户名”框。")  

## <a name="the-five-authentication-options"></a>五个身份验证选项  
- Active Directory 通用身份验证支持两种非交互式身份验证方法（`Active Directory - Password` 身份验证和 `Active Directory - Integrated` 身份验证）。 非交互式 `Active Directory - Password` 和 `Active Directory - Integrated` 身份验证方法可在许多不同的应用程序（ADO.NET、JDBC、ODBC 等）中使用。 这两种方法绝对不会产生弹出式对话框。

- `Active Directory - Universal with MFA` 身份验证是同时支持 *Azure 多重身份验证* (MFA) 的交互式方法。 Azure MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它利用一系列简单的验证选项（电话、短信、含有 PIN 码的智能卡或移动应用通知）提供强身份验证，用户可以根据自己的偏好选择所用的方法。 配合使用 Azure AD 和交互式 MFA 时会出现用于验证的弹出式对话框。

有关多重身份验证的说明，请参阅[多重身份验证](../active-directory/authentication/multi-factor-authentication.md)。
有关配置步骤，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 域名称或租户 ID 参数   

从 [SSMS 版本 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开始，以来宾用户身份从其他 Azure Active Directory 导入到当前 Active Directory 的用户在连接时可提供 Azure AD 域名或租户 ID。 来宾用户包括从其他 Azure AD、Microsoft 帐户（如 outlook.com、hotmail.com、live.com）或其他帐户（如 gmail.com）邀请的用户。此信息使“Active Directory - 通用且具有 MFA 身份验证”可以识别正确的身份验证机构。 此选项也是支持 outlook.com、hotmail.com、live.com 等 Microsoft 帐户 (MSA) 或非 MSA 帐户的必需选项。 所有要使用通用身份验证进行身份验证的用户必须输入其 Azure AD 域名或租户 ID。 此参数表示 Azure 服务器当前链接的 Azure AD 域名/租户ID。 例如，如果 Azure Server 与 Azure AD 域 `contosotest.onmicrosoft.com`（其中用户 `joe@contosodev.onmicrosoft.com` 托管为从 Azure AD 域 `contosodev.onmicrosoft.com` 导入的用户）相关联，则需用于对此用户进行身份验证的域名为 `contosotest.onmicrosoft.com`。 如果用户是链接到 Azure 服务器的 Azure AD 的本机用户，并且不是 MSA 帐户，则无需提供域名或租户 ID。 若要输入参数（从 SSMS 版本 17.2 开始），请在“连接到数据库”对话框中，完成该对话框，选择“Active Directory - 通用且具有 MFA”身份验证，单击“选项”，完成“用户名”框，然后单击“连接属性”选项卡。选中“AD 域名或租户 ID”框，然后提供身份验证机构，如域名 (**contosotest.onmicrosoft.com**) 或租户 ID 的 GUID。  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企业到企业支持   
针对 Azure AD B2B 方案以来宾用户身份受支持的 Azure AD 用户（请参阅[什么是 Azure B2B 协作](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)）只能作为具有以下条件的组的成员连接到 SQL 数据库和 SQL 数据仓库：在当前 Azure AD 中创建，并使用 Transact-SQL `CREATE USER` 语句在给定数据库中手动映射。 例如，如果 `steve@gmail.com` 受邀加入 Azure AD `contosotest`（具有 Azure Ad 域 `contosotest.onmicrosoft.com`），则必须在 Azure AD 中创建包含 `steve@gmail.com` 成员的 Azure AD 组（如 `usergroup`）。 随后，必须通过执行 Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 语句，通过 Azure AD SQL 域或 Azure AD DBO 为特定数据库（例如 MyDatabase）创建此组。 创建数据库用户之后，用户 `steve@gmail.com` 随后可以使用 SSMS 身份验证选项 `Active Directory – Universal with MFA support` 登录 `MyDatabase`。 默认情况下，用户只拥有连接权限以及需要采用正常方式授予的任何其他数据访问权限。 请注意，作为来宾用户的用户 `steve@gmail.com` 必须在 SSMS“连接属性”对话框中选中该框，并添加 AD 域名 `contosotest.onmicrosoft.com`。 仅对“通用且具有 MFA 连接”选项支持“AD 域名或租户 ID”，否则它处于灰显状态。

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL 数据库和 SQL 数据仓库的 Active Directory 通用身份验证限制
- SSMS 和 SqlPackage.exe 是目前唯一通过 Active Directory 通用身份验证针对 MFA 启用的工具。
- SSMS 版本 17.2 支持使用具有 MFA 的通用身份验证进行多用户并发访问。 版本 17.0 和 17.1 将 SSMS 实例限制为使用通用身份验证登录到单个 Azure Active Directory 帐户。 若要以另一个 Azure AD 帐户登录，则必须使用另一个 SSMS 实例。 （此限制仅限于 Active Directory 通用身份验证；如果使用 Active Directory 密码验证、Active Directory 集成身份验证或 SQL Server 身份验证，可以登录到不同的服务器）。
- 对于对象资源管理器、查询编辑器和查询存储可视化效果，SSMS 支持 Active Directory 通用身份验证。
- SSMS 版本 17.2 为导出/提取/部署数据数据库提供 DacFx 向导支持。 在特定用户使用通用身份验证通过初始身份验证对话框进行了身份验证之后，DacFx 向导的工作方式与针对所有其他身份验证方法的方式相同。
- SSMS 表设计器不支持通用身份验证。
- 除了必须使用支持的 SSMS 版本，Active Directory 通用身份验证没有其他软件需求。  
- 通用身份验证的 Active Directory 身份验证库 (ADAL) 版本已更新到最新的 ADAL.dll 3.13.9 可用发行版。 请参阅 [Active Directory 身份验证库 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  


## <a name="next-steps"></a>后续步骤

- 有关配置步骤，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。
- 向其他人员授予数据库访问权限：[SQL 数据库身份验证和授权：授予访问权限](sql-database-manage-logins.md)  
- 确保其他人员可以通过防火墙进行连接：[使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md)  
- [使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [将 BACPAC 文件导入到新的 Azure SQL 数据库](../sql-database/sql-database-import.md)  
- [将 Azure SQL 数据库导出到 BACPAC 文件](../sql-database/sql-database-export.md)  
- C# 接口 [IUniversalAuthProvider 接口](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- 使用 **Active Directory - 通用且具有 MFA** 进行身份验证时，可以使用以 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开头的 ADAL 跟踪。 在默认关闭的情况下，可在“ADAL 输出窗口跟踪级别”**中，**使用“Azure 云”的“Azure 服务”下方“选项”菜单中的“工具”，打开 ADAL 跟踪，然后在“视图”菜单中启用“输出”。 选择“Azure Active Directory 选项”时，可在输出窗口中使用跟踪。  
