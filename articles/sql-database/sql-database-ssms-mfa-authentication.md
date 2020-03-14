---
title: 使用多重 AAD 身份验证
description: Azure SQL Database 和 Azure Synapse 支持使用 Active Directory 通用身份验证从 SQL Server Management Studio （SSMS）进行连接。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255868"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>在 Azure SQL 数据库和 Azure Synapse Analytics 中使用多重 AAD 身份验证（对 MFA 的 SSMS 支持）
Azure SQL Database 和 Azure Synapse 支持使用*Active Directory 通用身份验证*从 SQL SERVER MANAGEMENT STUDIO （SSMS）进行连接。 本文讨论各种身份验证选项之间的差异，以及与使用通用身份验证相关的限制。 

**下载最新 SSMS** - 在客户端计算机上，从[下载 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下载最新版本的 SSMS。 


对于本文中所述的所有功能，请使用至少2017年7月的版本17.2。  "最近连接" 对话框应如下图所示：
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "完成 "用户名" 框。")  

## <a name="the-five-authentication-options"></a>五个身份验证选项  

Active Directory 通用身份验证支持两种非交互式身份验证方法：
    - `Active Directory - Password` 身份验证
    - `Active Directory - Integrated` 身份验证

还提供了两种非交互式身份验证模型，可以在许多不同的应用程序（ADO.NET、JDCB、ODC 等）中使用。 这两种方法不会产生弹出式对话框： 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

交互式方法还支持 Azure 多因素身份验证（MFA）： 
- `Active Directory - Universal with MFA` 


Azure MFA 可满足用户简单登录过程的需求，同时可帮助保护数据访问权限和应用程序。 它利用一系列简单的验证选项（电话、短信、含有 PIN 码的智能卡或移动应用通知）提供强身份验证，用户可以根据自己的偏好选择所用的方法。 配合使用 Azure AD 和交互式 MFA 时会出现用于验证的弹出式对话框。

有关多重身份验证的说明，请参阅[多重身份验证](../active-directory/authentication/multi-factor-authentication.md)。
有关配置步骤，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 域名称或租户 ID 参数   

从 [SSMS 版本 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开始，以来宾用户身份从其他 Azure Active Directory 导入到当前 Active Directory 的用户在连接时可提供 Azure AD 域名或租户 ID。 来宾用户包括从其他 Azure AD、Microsoft 帐户（如 outlook.com、hotmail.com、live.com）或其他帐户（如 gmail.com）邀请的用户。 此信息使“Active Directory - 通用且具有 MFA 身份验证”可以识别正确的身份验证机构。 此选项也是支持 outlook.com、hotmail.com、live.com 等 Microsoft 帐户 (MSA) 或非 MSA 帐户的必需选项。 所有要使用通用身份验证进行身份验证的用户必须输入其 Azure AD 域名或租户 ID。 此参数表示 Azure 服务器当前链接的 Azure AD 域名/租户ID。 例如，如果 Azure Server 与 Azure AD 域 `contosotest.onmicrosoft.com`（其中用户 `joe@contosodev.onmicrosoft.com` 托管为从 Azure AD 域 `contosodev.onmicrosoft.com` 导入的用户）相关联，则需用于对此用户进行身份验证的域名为 `contosotest.onmicrosoft.com`。 如果用户是链接到 Azure 服务器的 Azure AD 的本机用户，并且不是 MSA 帐户，则无需提供域名或租户 ID。 若要输入参数（从 SSMS 版本17.2 开始），在 "**连接到数据库**" 对话框中，完成对话框，选择 " **Active Directory-通用**"，并选择 "MFA 身份验证"，单击 "**选项**"，"完成**用户名**" 框，然后单击 "**连接属性**" 选项卡。选中 " **AD 域名或租户 ID** " 框，并提供身份验证机构，如域名（**contosotest.onmicrosoft.com**）或租户 ID 的 GUID。  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

如果你运行的是 SSMS 18. x 或更高版本，则来宾用户不再需要 AD 域名或租户 ID，因为 18. x 或更高版本会自动将其识别。

   ![mfa-租户-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企业到企业支持   
Azure AD 作为来宾用户的 Azure AD B2B 方案支持的用户（请参阅[什么是 AZURE B2B 协作](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)）只能作为在当前 Azure AD 中创建的组的成员的一部分连接到 SQL 数据库和 Azure Synapse，并使用给定数据库中的 transact-sql `CREATE USER` 语句手动映射。 例如，如果 `steve@gmail.com` 受邀加入 Azure AD `contosotest`（具有 Azure Ad 域 `contosotest.onmicrosoft.com`），则必须在 Azure AD 中创建包含 `usergroup` 成员的 Azure AD 组（如 `steve@gmail.com`）。 然后，必须通过 Azure AD SQL 管理员或 Azure AD DBO 通过执行 Transact-sql `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 语句，为特定数据库（即 MyDatabase）创建此组。 创建数据库用户之后，用户 `steve@gmail.com` 随后可以使用 SSMS 身份验证选项 `MyDatabase` 登录 `Active Directory – Universal with MFA support`。 默认情况下，用户只拥有连接权限以及需要采用正常方式授予的任何其他数据访问权限。 请注意，作为来宾用户的用户 `steve@gmail.com` 必须在 SSMS“连接属性”`contosotest.onmicrosoft.com`**对话框中选中该框，并添加 AD 域名** 。 仅对“通用且具有 MFA 连接”选项支持“AD 域名或租户 ID”，否则它处于灰显状态。

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>针对 SQL 数据库和 Azure Synapse 的通用身份验证限制
- SSMS 和 SqlPackage.exe 是目前唯一通过 Active Directory 通用身份验证针对 MFA 启用的工具。
- SSMS 版本 17.2 支持使用具有 MFA 的通用身份验证进行多用户并发访问。 版本 17.0 和 17.1 将使用通用身份验证的 SSMS 实例的登录名限制到单个 Azure Active Directory 帐户。 若要以另一个 Azure AD 帐户登录，则必须使用另一个 SSMS 实例。 （此限制仅限于 Active Directory 通用身份验证；如果使用 Active Directory 密码验证、Active Directory 集成身份验证或 SQL Server 身份验证，可以登录到不同的服务器）。
- 对于对象资源管理器、查询编辑器和查询存储可视化效果，SSMS 支持 Active Directory 通用身份验证。
- SSMS 版本 17.2 为导出/提取/部署数据数据库提供 DacFx 向导支持。 在特定用户使用通用身份验证通过初始身份验证对话框进行了身份验证之后，DacFx 向导的工作方式与针对所有其他身份验证方法的方式相同。
- SSMS 表设计器不支持通用身份验证。
- 除了必须使用支持的 SSMS 版本，Active Directory 通用身份验证没有其他软件需求。  
- 通用身份验证的 Active Directory 身份验证库 (ADAL) 版本已更新到最新的 ADAL.dll 3.13.9 可用发行版。 请参阅 [Active Directory 身份验证库 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  


## <a name="next-steps"></a>后续步骤

- 有关配置步骤，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。
- 向其他人员授予数据库访问权限：[SQL 数据库身份验证和授权：授予访问权限](sql-database-manage-logins.md)  
- 确保其他人员可以通过防火墙进行连接：[使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md)  
- [配置和管理 Azure Active Directory 身份验证与 SQL 数据库或 Azure Synapse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [将 BACPAC 文件导入到新的 Azure SQL 数据库](../sql-database/sql-database-import.md)  
- [将 Azure SQL 数据库导出到 BACPAC 文件](../sql-database/sql-database-export.md)  
- C# 接口 [IUniversalAuthProvider 接口](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- 使用 **Active Directory - 通用且具有 MFA** 进行身份验证时，可以使用以 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开头的 ADAL 跟踪。 在默认关闭的情况下，可在“ADAL 输出窗口跟踪级别”**中，** 使用“Azure 云”的“Azure 服务”下方“选项”菜单中的“工具”，打开 ADAL 跟踪，然后在“视图”菜单中启用“输出”。 选择“Azure Active Directory 选项”时，可在输出窗口中使用跟踪。  
