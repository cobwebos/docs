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
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 2a3e1e7279e915c0c5992190ef0c8d1d83880dbb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596132"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>使用多重 Azure Active Directory 身份验证
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 支持使用“Azure Active Directory - 通用且具有 MFA 支持”身份验证从 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 进行连接。 本文讨论了各种身份验证选项之间的差异，以及与将 Azure Active Directory (Azure AD) 中的通用身份验证用于 Azure SQL 相关的限制。

**下载最新 SSMS** - 在客户端计算机上，从[下载 SQL Server Management Studio (SSMS)](https://aka.ms/ssms) 下载最新版本的 SSMS。 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


对于本文中讨论的所有功能，请至少使用 2017 年 7 月的版本 17.2。 最新连接对话框的外观应类似于下图：

  ![SQL Server Management Studio 中的 "连接到服务器" 对话框的屏幕截图，其中显示了服务器类型、服务器名称和身份验证的设置。](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>身份验证选项

Azure AD 有两种非交互式身份验证模型，它们可用于许多不同的应用程序（ADO.NET、JDCB、ODC 等）。 这两种方法绝对不会产生弹出式对话框：

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

同时支持 Azure 多重身份验证 (MFA) 的交互式方法是： 

- `Azure Active Directory - Universal with MFA`

Azure MFA 可满足用户简单登录过程的需求，同时可帮助保护数据访问权限和应用程序。 它利用一系列简单的验证选项（电话、短信、含有 PIN 码的智能卡或移动应用通知）提供强身份验证，用户可以根据自己的偏好选择所用的方法。 配合使用 Azure AD 和交互式 MFA 时会出现用于验证的弹出式对话框。

有关 Azure 多重身份验证的说明，请参阅[多重身份验证](../../active-directory/authentication/multi-factor-authentication.md)。
有关配置步骤，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](authentication-mfa-ssms-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 域名称或租户 ID 参数

从 [SSMS 版本 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开始，以来宾用户身份从其他 Azure Active Directory 导入到当前 Azure AD 的用户在连接时可提供 Azure AD 域名或租户 ID。 来宾用户包括从其他 Azure AD、Microsoft 帐户（如 outlook.com、hotmail.com、live.com）或其他帐户（如 gmail.com）邀请的用户。 此信息让 `Azure Active Directory - Universal with MFA` 身份验证能够识别正确的身份验证授权。 此选项也是支持 outlook.com、hotmail.com、live.com 等 Microsoft 帐户 (MSA) 或非 MSA 帐户的必需选项。 

所有要使用通用身份验证进行身份验证的来宾用户必须输入其 Azure AD 域名或租户 ID。 此参数表示 Azure SQL 逻辑服务器当前关联的 Azure AD 域名或租户 ID。 例如，如果 SQL 逻辑服务器与 Azure AD 域 `contosotest.onmicrosoft.com`（其中用户 `joe@contosodev.onmicrosoft.com` 作为从 Azure AD 域 `contosodev.onmicrosoft.com` 导入的用户托管）相关联，则用于对此用户进行身份验证的域名需要为 `contosotest.onmicrosoft.com`。 如果用户是与 SQL 逻辑服务器关联的 Azure AD 的本机用户，并且不是 MSA 帐户，则无需提供域名或租户 ID。 若要输入参数（从 SSMS 版本 17.2 开始），请执行以下此操作：


1. 在 SSMS 中打开一个连接。 输入服务器名称，然后选择“Azure Active Directory - 通用且具有 MFA 支持”身份验证。 添加要用于登录的“用户名”。
1. 选择“选项”框，然后转到“连接属性”选项卡。在“连接到数据库”对话框中，针对你的数据库填写对话框。 选中“AD 域名或租户 ID”  框，然后提供身份验证机构，如域名 (**contosotest.onmicrosoft.com**) 或租户 ID 的 GUID。 

   !["连接属性" 选项卡的屏幕截图突出显示了 "连接到数据库" 和 "AD 域名" 或 "租户 ID" 的设置。](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

如果运行的是 SSMS 18.x 或更高版本，则来宾用户不再需要 AD 域名或租户 ID，因为 18.x 或更高版本会自动识别它。

   !["连接到数据库" 字段中的 "连接到服务器" 对话框中的 "连接属性" 选项卡的屏幕截图。在 "连接到数据库" 字段中选择了 "MyDatabase"。](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企业到企业支持

> [!IMPORTANT]
> 公共预览版目前不支持来宾用户在不成为某个组的成员的情况下连接到 Azure SQL 数据库、SQL 托管实例和 Azure Synapse。 有关详细信息，请参阅[创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员](authentication-aad-guest-users.md)。

Azure AD 作为来宾用户的 Azure AD B2B 方案支持的用户 (查看 [AZURE B2B 协作](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) 只能连接到 SQL 数据库和 azure Synapse，作为在关联 Azure AD 中创建的组的成员，并使用给定数据库中的 [CREATE USER (transact-sql) ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 语句手动映射。 例如，如果 `steve@gmail.com` 受邀加入 Azure AD `contosotest`（具有 Azure Ad 域 `contosotest.onmicrosoft.com`），则必须在 Azure AD 中创建包含 `steve@gmail.com` 成员的 Azure AD 组（如 `usergroup`）。 然后，必须通过执行 Transact-sql 语句，为特定的数据库创建此组 (例如， `MyDatabase` 通过 AZURE AD SQL 管理员或 AZURE AD DBO) `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 。 

创建数据库用户后，用户 `steve@gmail.com` 随后可以使用 SSMS 身份验证选项 `Azure Active Directory – Universal with MFA` 登录 `MyDatabase`。 默认情况下，`usergroup` 仅具有连接权限。 任何进一步的数据访问权限需要由具有足够特权的用户在数据库中[授予](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql)。 

> [!NOTE]
> 对于 SSMS 17.x，如果使用 `steve@gmail.com` 作为来宾用户，则必须选中“AD 域名或租户 ID”框，然后在“连接属性”对话框中添加 AD 域名 `contosotest.onmicrosoft.com`。 “Azure Active Directory - 通用且具有 MFA 支持”身份验证仅支持“AD 域名或租户 ID”选项。 否则，该复选框会显示为灰色。

## <a name="universal-authentication-limitations"></a>通用身份验证限制

- SSMS 和 SqlPackage.exe 是目前唯一通过 Active Directory 通用身份验证针对 MFA 启用的工具。
- SSMS 版本 17.2 支持使用具有 MFA 支持的通用身份验证进行多用户并发访问。 对于 SSMS 版本 17.0 和 17.1，工具将 SSMS 实例限制为使用通用身份验证登录到单个 Azure Active Directory 帐户。 若要以另一个 Azure AD 帐户登录，则必须使用另一个 SSMS 实例。 此限制限于 Active Directory 通用身份验证；可以使用 `Azure Active Directory - Password` 身份验证、`Azure Active Directory - Integrated` 身份验证或 `SQL Server Authentication` 登录其他服务器。
- 对于对象资源管理器、查询编辑器和查询存储可视化效果，SSMS 支持 Active Directory 通用身份验证。
- SSMS 版本 17.2 为导出/提取/部署数据数据库提供 DacFx 向导支持。 在特定用户使用通用身份验证通过初始身份验证对话框进行了身份验证之后，DacFx 向导的工作方式与针对所有其他身份验证方法的方式相同。
- SSMS 表设计器不支持通用身份验证。
- 除了必须使用支持的 SSMS 版本，Active Directory 通用身份验证没有其他软件需求。  
- 有关用于通用身份验证的最新 Active Directory 身份验证库 (ADAL) 版本，请参阅以下链接：[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  

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
- 使用“Azure Active Directory - 通用且具有 MFA 支持”身份验证时，从 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开始提供 ADAL 跟踪功能。 在默认关闭的情况下，可在“ADAL 输出窗口跟踪级别”**中，** 使用“Azure 云”的“Azure 服务”下方“选项”菜单中的“工具”，打开 ADAL 跟踪，然后在“视图”菜单中启用“输出”。 选择“Azure Active Directory 选项”时，可在输出窗口中使用跟踪。  
