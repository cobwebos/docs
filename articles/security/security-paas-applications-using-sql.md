---
title: "在 Azure 中保护 PaaS 数据库 | Microsoft Docs"
description: " 了解有关保护 PaaS Web 和移动应用程序的 Azure SQL 数据库和 SQL 数据仓库安全最佳实践。 "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: terrylan
ms.openlocfilehash: 18509b3fc3a73118f67583a0b087c58f0e51993c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="securing-paas-databases-in-azure"></a>在 Azure 中保护 PaaS 数据库

本文介绍有关保护 PaaS Web 和移动应用程序的 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)和 [SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/)安全最佳实践。 这些最佳实践衍生自我们的 Azure 经验和客户经验。

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL 数据库和 SQL 数据仓库
[Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)为基于 Internet 的应用程序提供关系型数据库服务。 让我们了解一下在 PaaS 部署中使用 Azure SQL 数据库和 SQL 数据仓库时可帮助保护应用程序与数据的服务：

- Azure Active Directory 身份验证（而不是 SQL Server 身份验证）
- Azure SQL 防火墙
- 透明数据加密 (TDE)

## <a name="best-practices"></a>最佳实践

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>为身份验证和授权使用集中式标识存储库

可将 Azure SQL 数据库配置为使用以下两种身份验证类型之一：

- **SQL 身份验证**使用用户名和密码。 在为数据库创建逻辑服务器时，已指定一个包含用户名和密码的“服务器管理员”登录名。 借助这些凭据，可以使用数据库所有者的身份通过服务器上任何数据库的身份验证。

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

SQL 数据库的默认源 IP 地址限制允许从任何 Azure 地址（包括其他订阅和租户）进行访问。 可以限制为仅允许从 IP 地址访问实例。 即使使用了 SQL 防火墙和 IP 地址限制，也仍然需要设置强身份验证。 请参阅本文前面提供的建议。

若要了解有关 Azure SQL 防火墙和 IP 限制的详细信息，请参阅：

- [Azure SQL 数据库访问控制](../sql-database/sql-database-control-access.md)
- [配置 Azure SQL 数据库防火墙规则 - 概述](../sql-database/sql-database-firewall-configure.md)
- [使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>静态数据加密
[透明数据加密 (TDE)](https://msdn.microsoft.com/library/azure/bb934049) 默认已启用。 TDE 以透明方式加密 SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库的数据和日志文件。 TDE 可以防范直接访问文件或其备份所造成的安全威胁。 这样就可以实现静态数据加密，且无需更改现有应用程序。 应始终保持启用 TDE；不过，这无法阻止攻击者使用普通的访问路径。 使用 TDE 能够符合各个行业制定的许多法律、法规和准则。

Azure SQL 可以管理 TDE 存在的密钥相关问题。 与使用 TDE 时一样，在本地操作以及移动数据库时也必须格外小心，确保能够恢复。 在更复杂的方案中，可以通过可扩展的密钥管理在 Azure Key Vault 中显式管理密钥（请参阅[使用 EKM 在 SQL Server 上启用 TDE](/security/encryption/enable-tde-on-sql-server-using-ekm)）。 此外，也允许通过 Azure Key Vault BYOK 功能自带密钥 (BYOK)。

Azure SQL 通过 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 为列提供加密。 这样，只有获得授权的应用程序才能访问敏感列。 使用这种加密可将针对已加密列的 SQL 查询限制为基于相等性的值。

对于某些特定的数据，也应该使用应用程序级加密。 有时，可通过使用保存在适当国家/地区的密钥加密数据，来消除数据主权忧虑。 这可以防止意外的数据传输导致问题，因为在使用强算法（例如 AES 256）的情况下，如果没有该密钥，将无法解密数据。

可以使用其他预防措施来帮助保护数据库，例如，设计安全系统、加密机密资产，以及围绕数据库服务器构建防火墙。

## <a name="next-steps"></a>后续步骤
本文介绍了有关保护 PaaS Web 和移动应用程序的 SQL 数据库和 SQL 数据仓库安全最佳实践。 若要了解有关保护 PaaS 部署的详细信息，请参阅：

- [保护 PaaS 部署](security-paas-deployments.md)
- [使用 Azure 应用服务保护 PaaS Web 和移动应用程序](security-paas-applications-using-app-services.md)
