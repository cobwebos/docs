---
title: 使用多重 Azure Active Directory 身份验证
description: Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 支持使用 Active Directory 通用身份验证从 SQL Server Management Studio (SSMS) 进行连接。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/27/2020
tags: azure-synapse
ms.openlocfilehash: b2d179121b05d7bf3493937a9ff72e302fd31f3d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281134"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>使用多重 Azure Active Directory 身份验证
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 通过使用*Azure Active Directory-通用和 MFA*身份验证，支持从[SQL Server Management Studio （SSMS）](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)连接。 本文讨论各种身份验证选项之间的差异，以及在 Azure SQL Azure Active Directory （Azure AD）中使用通用身份验证的相关限制。

**下载最新 SSMS** - 在客户端计算机上，从[下载 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下载最新版本的 SSMS。

对于本文中讨论的所有功能，请至少使用 2017 年 7 月的版本 17.2。 最新连接对话框的外观应类似于下图：

  ![1mfa-universal-connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "填写“用户名”框。")  

## <a name="authentication-options"></a>身份验证选项

Azure AD 有两种非交互式身份验证模型，可以在许多不同的应用程序（ADO.NET、JDCB、ODC 等）中使用。 这两种方法绝对不会产生弹出式对话框：

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

同时支持 Azure 多重身份验证 (MFA) 的交互式方法是： 

- `Azure Active Directory - Universal with MFA`

Azure MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它利用一系列简单的验证选项（电话、短信、含有 PIN 码的智能卡或移动应用通知）提供强身份验证，用户可以根据自己的偏好选择所用的方法。 配合使用 Azure AD 和交互式 MFA 时会出现用于验证的弹出式对话框。

有关 Azure 多重身份验证的说明，请参阅[多重身份验证](../../active-directory/authentication/multi-factor-authentication.md)。
有关配置步骤，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](authentication-mfa-ssms-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 域名称或租户 ID 参数

从[SSMS 版本 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)开始，从其他 Azure Active directory 导入到当前 Azure AD 作为来宾用户的用户，可以在连接时提供 Azure AD 域名或租户 ID。 来宾用户包括从其他 Azure AD、Microsoft 帐户（如 outlook.com、hotmail.com、live.com）或其他帐户（如 gmail.com）邀请的用户。 此信息允许 `Azure Active Directory - Universal with MFA` 身份验证识别正确的身份验证机构。 此选项也是支持 outlook.com、hotmail.com、live.com 等 Microsoft 帐户 (MSA) 或非 MSA 帐户的必需选项。 

要使用通用身份验证进行身份验证的所有来宾用户必须输入其 Azure AD 域名或租户 ID。 此参数表示与 Azure SQL 逻辑服务器关联的当前 Azure AD 域名或租户 ID。 例如，如果 SQL 逻辑服务器与 Azure AD 域关联 `contosotest.onmicrosoft.com` ，其中用户 `joe@contosodev.onmicrosoft.com` 作为 Azure AD 域中的导入的用户托管，则对 `contosodev.onmicrosoft.com` 此用户进行身份验证所需的域名为 `contosotest.onmicrosoft.com` 。 如果用户是与 SQL 逻辑服务器关联的 Azure AD 的本机用户，并且不是 MSA 帐户，则无需提供域名或租户 ID。 输入参数（从 SSMS 版本17.2 开始）：


1. 在 SSMS 中打开连接。 输入服务器名称，然后选择 " **Azure Active Directory-通用**" 和 "MFA 身份验证"。 添加要用来登录的**用户名**。
1. 选择 "**选项**" 框，然后浏览到 "**连接属性**" 选项卡。在 "**连接到数据库**" 对话框中，为数据库完成对话框。 选中“AD 域名或租户 ID”  框，然后提供身份验证机构，如域名 (**contosotest.onmicrosoft.com**) 或租户 ID 的 GUID。 

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

如果运行的是 SSMS 18.x 或更高版本，则来宾用户不再需要 AD 域名或租户 ID，因为 18.x 或更高版本会自动识别它。

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企业到企业支持

> [!IMPORTANT]
> 支持来宾用户连接到 Azure SQL 数据库、SQL 托管实例和 Azure Synapse，而无需成为组的**一部分。** 有关详细信息，请参阅[创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员](authentication-aad-guest-users.md)。

Azure AD 作为来宾用户的 Azure AD B2B 方案支持的用户（请参阅[什么是 AZURE B2B 协作](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)）只能连接到 SQL 数据库和 azure Synapse，这是在相关 Azure AD 中创建的组的成员的一部分，并使用给定的数据库中的[CREATE USER （transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)语句手动映射。 例如，如果 `steve@gmail.com` 受邀加入 Azure AD `contosotest`（具有 Azure Ad 域 `contosotest.onmicrosoft.com`），则必须在 Azure AD 中创建包含 `steve@gmail.com` 成员的 Azure AD 组（如 `usergroup`）。 然后，必须通过执行 Transact-sql 语句，为特定数据库（例如，）创建此组， `MyDatabase` AZURE AD SQL 管理员或 AZURE AD DBO 创建此组 `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 。 

创建数据库用户之后，用户 `steve@gmail.com` 可以 `MyDatabase` 使用 SSMS 身份验证选项登录 `Azure Active Directory – Universal with MFA` 。 默认情况下， `usergroup` 仅具有 connect 权限。 对于具有足够权限的用户，将需要在数据库中[授予](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql)进一步的数据访问权限。 

> [!NOTE]
> 对于 SSMS 17. x，使用 `steve@gmail.com` 作为来宾用户，必须选中 " **ad 域名或租户 ID** " 框，并 `contosotest.onmicrosoft.com` 在 "**连接属性**" 对话框中添加 ad 域名。 仅**Azure Active Directory-通用和 MFA**身份验证支持**AD 域名或租户 ID**选项。 否则，该复选框将灰显。

## <a name="universal-authentication-limitations"></a>通用身份验证限制

- SSMS 和 SqlPackage.exe 是目前唯一通过 Active Directory 通用身份验证针对 MFA 启用的工具。
- SSMS 版本17.2 支持使用具有 MFA 的通用身份验证的多用户并发访问。 对于 SSMS 版本17.0 和17.1，该工具使用通用身份验证将 SSMS 实例的登录名限制为单个 Azure Active Directory 帐户。 若要以另一个 Azure AD 帐户登录，则必须使用 SSMS 的另一个实例。 此限制仅限于 Active Directory 通用身份验证;可以使用 `Azure Active Directory - Password` 身份验证、 `Azure Active Directory - Integrated` 身份验证或登录到不同的服务器 `SQL Server Authentication` 。
- 对于对象资源管理器、查询编辑器和查询存储可视化效果，SSMS 支持 Active Directory 通用身份验证。
- SSMS 版本 17.2 为导出/提取/部署数据数据库提供 DacFx 向导支持。 在特定用户使用通用身份验证通过初始身份验证对话框进行了身份验证之后，DacFx 向导的工作方式与针对所有其他身份验证方法的方式相同。
- SSMS 表设计器不支持通用身份验证。
- 除了必须使用支持的 SSMS 版本，Active Directory 通用身份验证没有其他软件需求。  
- 请参阅以下链接，了解适用于通用身份验证的最新 Active Directory 身份验证库（ADAL）版本： [system.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  

## <a name="next-steps"></a>后续步骤

- 有关配置步骤，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](authentication-mfa-ssms-configure.md)。
- 授予其他人对数据库的访问权限：[SQL 数据库身份验证和授权：授予访问权限](logins-create-manage.md)  
- 确保其他人可以通过防火墙进行连接：[通过 Azure 门户配置服务器级防火墙规则](firewall-configure.md)  
- [使用 SQL 数据库或 Azure Synapse 配置和管理 Azure Active Directory 身份验证](authentication-aad-configure.md)
- [创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [将 BACPAC 文件导入到新数据库](database-import.md)  
- [将数据库导出到 BACPAC 文件](database-export.md)  
- C# 接口 [IUniversalAuthProvider 接口](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- 将**Azure Active Directory 通用与 MFA**身份验证一起使用时，可从[SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)开始使用 ADAL 跟踪。 在默认关闭的情况下，可在“ADAL 输出窗口跟踪级别”**中，** 使用“Azure 云”的“Azure 服务”下方“选项”菜单中的“工具”，打开 ADAL 跟踪，然后在“视图”菜单中启用“输出”。 选择“Azure Active Directory 选项”时，可在输出窗口中使用跟踪。  
