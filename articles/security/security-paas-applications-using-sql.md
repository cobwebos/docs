---
title: "使用 SQL 数据库和 SQL 数据仓库保护 PaaS Web 和移动应用程序 | Microsoft 文档"
description: " 了解有关保护 PaaS Web 和移动应用程序的 Azure SQL 数据库和 SQL 数据仓库安全最佳实践. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b3db1281bbd37fcfbec54bdeb998e6338bb08369
ms.openlocfilehash: 39f841c0f3c8bed1bd640dfd644a58a764422aff


---
# <a name="securing-paas-web-and-mobile-applications-using-sql-database-and-sql-data-warehouse"></a>使用 SQL 数据库和 SQL 数据仓库保护 PaaS Web 和移动应用程序

本文介绍有关保护 PaaS Web 和移动应用程序的 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)和 [SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/)安全最佳实践。 这些最佳实践衍生自我们的 Azure 经验和客户经验。

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL 数据库和 SQL 数据仓库
[Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)为基于 Internet 的应用程序提供关系数据库服务。 让我们了解一下在 PaaS 部署中使用 Azure SQL 数据库和 SQL 数据仓库时可帮助保护应用程序与数据的服务：

- Azure Active Directory 身份验证（而不是 SQL Server 身份验证）
- Azure SQL 防火墙
- 透明数据加密 (TDE)

## <a name="best-practices"></a>最佳实践

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>为身份验证和授权使用集中式标识存储库

可将 Azure SQL 数据库配置为使用以下两种身份验证类型之一：

- **SQL 身份验证**使用用户名和密码。 在为数据库创建逻辑服务器时，你已指定一个包含用户名和密码的“服务器管理员”登录名。 借助这些凭据，可以使用数据库所有者的身份通过服务器上任何数据库的身份验证。

- **Azure Active Directory 身份验证**使用 Azure Active Directory 管理的标识，支持托管域和集成域。 若要使用 Azure Active Directory 身份验证，必须创建名为“Azure AD 管理员”的另一个服务器管理员，用于管理 Azure AD 用户和组。 此管理员还能执行普通服务器管理员可以执行的所有操作。

[Azure Active Directory 身份验证](../active-directory/develop/active-directory-authentication-scenarios.md)是使用 Azure Active Directory (AD) 中的标识连接到 Azure SQL 数据库和 SQL 数据仓库的一种机制。 Azure AD 为 SQL Server 身份验证提供一种替代方法，使你可以阻止用户标识在数据库服务器之间激增。 使用 Azure AD 身份验证可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。 集中 ID 管理提供一个单一位置来管理数据库用户，并简化权限管理。  

与 SQL 身份验证相比，使用 Azure AD 身份验证的好处包括：

- 允许在单一位置中轮换密码。
- 使用外部 Azure AD 组管理数据库权限。
- 通过启用集成的 Windows 身份验证和 Azure AD 支持的其他形式的身份验证来消除存储密码。
- 使用包含的数据库用户在数据库级别对标识进行身份验证。
- 支持对连接到 SQL 数据库的应用程序进行基于令牌的身份验证。
- 支持对本地 Azure AD 进行 ADFS（域联合）或本机用户/密码身份验证，无需进行域同步。
- 支持从 SQL Server Management Studio 进行连接，后者使用 Active Directory 通用身份验证，其中包括[多重身份验证 (MFA)](../multi-factor-authentication/multi-factor-authentication.md)。 MFA 包括利用一系列简单的验证选项进行的强身份验证，这些选项包括电话、短信、含有 PIN 码的智能卡或移动应用通知。 有关详细信息，请参阅 [SQL 数据库和 SQL 数据仓库针对 Azure AD MFA 的 SSMS 支持](../sql-database/sql-database-ssms-mfa-authentication.md)。

若要了解有关 Azure AD 身份验证的详细信息，请参阅：

- [使用 Azure Active Directory 身份验证连接到 SQL 数据库或 SQL 数据仓库](../sql-database/sql-database-aad-authentication.md)
- [对 Azure SQL 数据仓库进行身份验证](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [使用 Azure AD 身份验证的 Azure SQL 数据库对基于令牌的身份验证的支持](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> 若要确保 Azure Active Directory 适用于当前环境，请参阅 [Azure AD 功能和限制](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)，尤其是那些需要额外考虑的内容。
>
>

### <a name="restrict-access-based-on-ip-address"></a>基于 IP 地址限制访问
可以创建防火墙规则用于指定可接受的 IP 地址范围。 这些规则可以针对服务器级别和数据库级别。 建议尽量使用数据库级防火墙规则，以增强安全性并提高数据库的可移植性。 当多个数据库具有相同的访问要求，但你不想花时间单独配置每个数据库时，管理员最好是使用服务器级防火墙规则。

SQL 数据库的默认源 IP 地址限制允许从任何 Azure 地址（包括其他订阅和租户）进行访问。 可以限制为仅允许从你的 IP 地址访问实例。 即使使用了 SQL 防火墙和 IP 地址限制，也仍然需要设置强身份验证。 请参阅本文前面提供的建议。

若要了解有关 Azure SQL 防火墙和 IP 限制的详细信息，请参阅：

- [Azure SQL 数据库访问控制](../sql-database/sql-database-control-access.md)
- [配置 Azure SQL 数据库防火墙规则 - 概述](../sql-database/sql-database-firewall-configure.md)
- [使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>静态数据加密
[透明数据加密 (TDE)](https://msdn.microsoft.com/library/azure/bb934049) 可以加密 SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库数据文件，称为静态数据加密。 可以采取多种预防措施来帮助保护数据库，例如，设计安全系统、加密机密资产，以及围绕数据库服务器构建防火墙。 但是，如果物理媒体（如驱动器或备份磁带）失窃，恶意方可能会还原或附加数据库并浏览数据。 一种解决方法是加密数据库中的敏感数据，并使用证书保护用于加密数据的密钥。 这可以防止没有密钥的任何人都能使用数据，但这种保护必须提前规划好。

TDE 保护静态数据，包括数据和日志文件。 使用 TDE 能够符合各个行业制定的许多法律、法规和准则。 这样，软件开发人员就可以使用行业标准的加密算法来加密数据，而无需更改现有应用程序。

如果法规明确指定了 TDE，则应使用此类加密。 不过请注意，这无法阻止攻击者使用普通的访问路径。 TDE 用于防范可能性极低的情景，其中，可能需要通过 Azure SQL 针对行与列提供的加密或者通过应用程序级加密来使用其他应用程序级加密。

对于某些特定的数据，也应该使用应用程序级加密。 可通过使用保存在适当国家/地区的密钥加密数据，来消除数据主权忧虑。 这可以防止意外的数据传输导致问题，因为在使用强算法（例如 AES 256）的情况下，如果没有该密钥，将无法解密数据。

可以执行 Azure SQL 针对行与列提供的加密，以便仅允许经过授权 ([RBAC](../active-directory/role-based-access-built-in-roles.md)) 的用户访问，阻止较低权限的用户查看列或行。

## <a name="next-steps"></a>后续步骤
本文介绍了有关保护 PaaS Web 和移动应用程序的 SQL 数据库和 SQL 数据仓库安全最佳实践。 若要了解有关保护 PaaS 部署的详细信息，请参阅：

- [保护 PaaS 部署](security-paas-deployments.md)
- [使用 Azure 应用服务保护 PaaS Web 和移动应用程序](security-paas-applications-using-app-services.md)



<!--HONumber=Feb17_HO1-->


