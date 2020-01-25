---
title: 适用于 Azure SQL 数据库的最佳安全做法手册Microsoft Docs
description: 本文提供了有关 Azure SQL 数据库中安全最佳做法的一般指南。
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 12/23/2019
ms.reviewer: ''
ms.openlocfilehash: 82297850bf6d03215963a1f81dda166550f2b0d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715177"
---
# <a name="azure-sql-database-security-best-practices-playbook"></a>Azure SQL 数据库安全最佳实践操作手册

## <a name="overview"></a>개요

本文档提供有关如何解决使用 Azure SQL 数据库的新的或现有应用程序的常见安全要求的指导。 它按高级安全区域进行组织。 若要解决特定的威胁，请参阅[常见安全威胁和潜在缓解](#common-security-threats-and-potential-mitigations)部分。 虽然在将应用程序从本地迁移到 Azure 时，某些提供的建议适用，但迁移方案并不是本文档的重点。

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>本指南中介绍的 Azure SQL 数据库部署服务

- [SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)： [Azure sql 数据库服务器](sql-database-servers.md)中的[单个数据库](sql-database-single-database.md)和[弹性池](sql-database-elastic-pool.md)
- [托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>本指南未涵盖的 SQL 部署提供

- Azure SQL Data Warehouse
- Azure SQL Vm （IaaS）
- SQL Server 온-프레미스

### <a name="audience"></a>대상

本指南的目标读者是面向客户的有关如何保护 Azure SQL 数据库的问题。 此最佳实践文章中感兴趣的角色包括但不限于：

- 安全架构师
- 安全管理器
- 合规性专员
- 隐私专员
- 安全工程师

### <a id="using"></a>使用本指南

本文档旨在作为现有[AZURE SQL 数据库安全](sql-database-security-overview.md)文档的随附。

除非另有说明，否则我们建议你遵循每个部分中列出的所有最佳实践，以实现相应的目标或要求。 为了满足特定的安全合规性标准或最佳做法，在 "要求" 或 "目标" 部分下面列出了重要的法规符合性控制措施。 本文中所述的安全标准和法规如下：

- [FedRAMP](https://www.fedramp.gov/documents/)： ac-04、ac 06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html)： CM-3、SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html)：访问控制、加密
- [Microsoft 操作安全保障（OSA）做法](https://www.microsoft.com/en-us/securityengineering/osa/practices)：练习 #1-6 和 #9
- [NIST 特殊发布800-53 安全控制](https://nvd.nist.gov/800-53)： ac-5、ac 6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library)：6.3.2、6.4。2

### <a name="feedback"></a>피드백

我们计划继续更新此处列出的建议和最佳实践。 使用本文底部的 "**反馈**" 链接提供对此文档的输入或任何更正。

## <a name="authentication"></a>인증

인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Database는 두 가지 인증 유형을 지원합니다.

- SQL 인증
- Azure Active Directory 인증

### <a name="central-management-for-identities"></a>标识的集中管理

中心标识管理具有以下优势：

- 管理组帐户并控制用户权限，而无需在 Azure SQL 数据库服务器和数据库之间重复登录。
- 简化和灵活的权限管理。
- 大规模管理应用程序。

**如何实现**：

- 使用 Azure Active Directory （Azure AD）身份验证实现集中标识管理。

**모범 사례**:

- 创建 Azure AD 租户，并[创建用户](../active-directory/fundamentals/add-users-azure-active-directory.md)来表示人为用户并创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md)，以表示应用、服务和自动化工具。 服务主体等效于 Windows 和 Linux 中的服务帐户。 

- 通过组分配将资源的访问权限分配给 Azure AD 主体：创建 Azure AD 组，授予对组的访问权限，并向组中添加单个成员。 在数据库中，创建映射 Azure AD 组的包含的数据库用户。 
  - 请参阅文章使用[Sql 配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)和[使用 Azure AD 进行 sql 身份验证](sql-database-aad-authentication.md)。
  > [!NOTE]
  > 在托管实例中，还可以创建映射到 master 数据库中 Azure AD 主体的登录名。 请参阅[CREATE LOGIN （transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。

- 使用 Azure AD 组可简化权限管理，并且资源所有者可以向组中添加成员或从中删除成员。 

- 为 SQL DB 服务器 Azure AD 管理员创建单独的组。

  - 请参阅本文，[为你的 AZURE SQL 数据库服务器预配 Azure Active Directory 管理员](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)。

- 使用 Azure AD 审核活动报告监视 Azure AD 组成员身份更改。 

- 对于托管实例，创建 Azure AD 管理员需要单独的步骤。 
  - 请参阅文章[为托管实例预配 Azure Active Directory 管理员](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。 

> [!NOTE]
> - Azure AD 身份验证记录在 Azure SQL 审核日志中，但不记录在 Azure AD 登录日志中。
> - 在 Azure 中授予的 RBAC 权限不适用于 Azure SQL DB 权限。 必须使用现有 SQL 权限在 SQL DB 中手动创建或映射此类权限。
> - 在客户端 Azure AD 身份验证上，需要访问 internet 或通过用户定义的路由（UDR）连接到 VNet。
> - Azure AD 访问令牌缓存在客户端，其生存期取决于令牌配置。 请参阅[Azure Active Directory 中可配置的令牌生存期](../active-directory/develop/active-directory-configurable-token-lifetimes.md)一文

### <a name="multi-factor-authentication-mfa"></a>MFA(Multi-Factor Authentication)

> [!NOTE]
> 中提到的： OSA 实践 #2、ISO 访问控制（AC）

Azure 多重身份验证（MFA）通过要求多种形式的身份验证来帮助提供额外的安全性。

**如何实现**：

- 使用条件性访问和使用交互式身份验证在 Azure AD 中[启用 MFA](../active-directory/authentication/concept-mfa-howitworks.md) 。 

- 替代方法是为整个 Azure AD 或 AD 域启用 MFA。

**모범 사례**:

- 激活 Azure AD 中的条件性访问（需要高级订阅）。 
  - 请参阅文章[Azure AD 中的条件性访问](../active-directory/conditional-access/overview.md)。  

- 使用 Azure AD 条件性访问创建 Azure AD 组，并为所选组启用 MFA 策略。 
  - 请参阅[规划条件性访问部署](../active-directory/conditional-access/plan-conditional-access.md)一文。 

- 可以为整个 Azure AD 或与 Azure AD 联合的整个 Active Directory 启用 MFA。 

- 对于以交互方式请求密码的 SQL 数据库，请使用 Azure AD 交互身份验证模式，然后进行 MFA 身份验证：      
  - 在 SSMS 中使用通用身份验证。 请参阅[使用 AZURE Sql 数据库和 AZURE Sql 数据仓库进行多重 AAD 身份验证（对 MFA 的 SSMS 支持）一](sql-database-ssms-mfa-authentication.md)文。
  - 使用 SQL Server Data Tools 中支持的交互式身份验证（SSDT）。 请参阅文章[Azure Active Directory 支持 SQL Server Data Tools （SSDT）](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)。
  - 使用支持 MFA 的其他 SQL 工具。 
    - 用于导出/提取/部署数据库的 SSMS 向导支持  
    - [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage)：选项 "/ua" 
    - [Sqlcmd 实用工具](https://docs.microsoft.com/sql/tools/sqlcmd-utility)：选项-G （交互）
    - [Bcp 实用工具](https://docs.microsoft.com/sql/tools/bcp-utility)：选项-G （交互） 

- 实现应用程序，使用支持 MFA 的交互式身份验证连接到 Azure SQL 数据库。 
  - 请参阅[通过 Azure 多重身份验证连接到 AZURE SQL 数据库](active-directory-interactive-connect-azure-sql-db.md)一文。 
  > [!NOTE]
  > 此身份验证模式需要基于用户的标识。 如果使用受信任的标识模型来绕过单个 Azure AD 用户身份验证（例如，使用 Azure 资源的托管标识），则不会应用 MFA。

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>最大程度地减少用户使用基于密码的身份验证

> [!NOTE]
> 中提到的： OSA 实践 #4、ISO 访问控制（AC）

基于密码的身份验证方法是一种较弱的身份验证形式。 凭据可能会泄露或被错误地丢弃。

**如何实现**：

- 使用不使用密码的 Azure AD 集成身份验证。

**모범 사례**:

- 使用 Windows 凭据进行单一登录身份验证。 将本地 AD 域与 Azure AD 联合，并使用集成 Windows 身份验证（对于已加入域的计算机，Azure AD）。
  - 请参阅[Azure AD 集成身份验证的 SSMS 支持](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)一文。

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>最大程度地减少对应用程序使用基于密码的身份验证 

> [!NOTE]
> 中提到的： OSA 实践 #4、ISO 访问控制（AC）

**如何实现**：

- 启用 Azure 托管标识。 你还可以使用集成或基于证书的身份验证。 

**모범 사례**:

- 使用[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
  - [系统分配的托管标识](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [使用具有托管标识的应用服务中的 Azure SQL 数据库（不更改代码）](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- 对应用程序使用基于证书的身份验证。 
  - 请参阅此[代码示例](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)。 

- 将 Azure AD 身份验证用于集成的联合域和加入域的计算机（请参阅上面的部分）。
  - 请参阅[用于集成身份验证的示例应用程序](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)。

### <a name="protect-passwords-and-secrets"></a>保护密码和机密

如果密码不能够避免，请确保它们受到保护。

**如何实现**：

- 使用 Azure Key Vault 存储密码和密码。 如果适用，请使用与 Azure AD 用户的 Azure SQL 数据库的 MFA。

**모범 사례**:

- 如果无法避免密码或机密，请在 Azure Key Vault 中存储用户密码和应用程序机密，并通过 Key Vault 访问策略管理访问权限。 

- 各种应用开发框架还可以提供框架特定的机制来保护应用中的机密。 例如： [ASP.NET core 应用](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows)。

### <a name="use-sql-authentication-for-legacy-applications"></a>对旧版应用程序使用 SQL 身份验证 

SQL 身份验证是指使用用户名和密码连接到 Azure SQL 数据库时用户的身份验证。 需要在每个 SQL 数据库服务器、托管实例以及每个数据库中创建的用户上创建一个登录名。

**如何实现**：

- 使用 SQL 身份验证。

**모범 사례**:

- 作为服务器管理员，创建登录名和用户。 除非使用包含密码的包含数据库用户，否则所有密码都存储在 master 数据库中。
  - 请参阅[如何控制和授予数据库对 Sql 数据库和 Sql 数据仓库的访问权限](sql-database-manage-logins.md)一文。

- 遵循密码管理最佳做法：
  - 提供复杂的密码，由拉丁文大写字母和小写字母、数字（0-9）以及非字母数字字符（如 $、！、# 或%）组成。
  - 使用更长的密码，而不是较短的随机选定字符。
  - 至少每90天执行一次手动密码更改。

## <a name="access-management"></a>액세스 관리

访问管理是对 Azure SQL 数据库的授权用户访问和权限的控制和管理过程。

### <a name="implement-principle-of-least-privilege"></a>实现最低权限原则

> [!NOTE]
> 中提到的： FedRamp 控制 AC 06，NIST： AC 6，OSA 实践 #3

最小特权的原则是，用户不应拥有比完成任务所需的权限更多的特权。 有关详细信息，请参阅文章[足够的管理](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)。

**如何实现**：

仅分配必要的[权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)来完成所需的任务：

- 在 SQL 数据平面中： 
    - 使用粒度权限和用户定义的数据库角色（或中的服务器角色）： 
        1. 创建所需的角色
            - [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [创建服务器角色](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. 创建所需用户
            - [CREATE USER](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. 将用户作为成员添加到角色 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [更改服务器角色](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. 然后，将权限分配给角色。 
            - [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - 请确保不要将用户分配到不必要的角色。

- 在 Azure 中资源管理器：
  - 使用内置角色（如果可用）或自定义 RBAC 角色，并分配必要的权限。
    - [Azure 的内置角色](../role-based-access-control/built-in-roles.md) 
    - [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)

**모범 사례**:

以下最佳做法是可选的，但将导致更好的可管理性和安全策略的可支持性： 

- 如果可能，请从尽可能少的权限集开始，并在有实际需要（和理由）的情况下，开始逐个添加权限，而不是相反的方法：逐步执行权限。 

- 避免将权限分配给单个用户。 改为改用角色（数据库或服务器角色）。 角色有助于对权限进行报告和故障排除。 （Azure RBAC 仅支持通过角色分配权限。） 

- 当角色的权限与用户的所需权限完全匹配时，请使用内置角色。 您可以将用户分配到多个角色。 

- 当内置角色授予过多或权限不足时，创建和使用自定义角色。 实践中使用的典型角色： 
  - 安全部署 
  - 관리자 
  - 개발자 
  - 支持人员 
  - 감사자 
  - 自动化过程 
  - 最终用户 

- 请记住，SQL Server 数据库引擎中的权限可应用于以下作用域。 范围越小，授予权限的影响越小。 
  - Azure SQL 数据库服务器（master 数据库中的特殊角色） 
  - 데이터베이스 
  - 架构（另请参阅：针对[SQL Server 的架构设计：针对架构设计的建议，具有安全性](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)）
  - 对象（表、视图、过程等） 
  > [!NOTE]
  > 不建议在对象级别上应用权限，因为此级别会给整体实现增加不必要的复杂性。 如果决定使用对象级权限，应清楚地记录这些权限。 这同样适用于列级权限，因为同样的原因，这些权限甚至更少建议。 [拒绝](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql)的标准规则不适用于列。

- 使用[漏洞评估（VA）](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)执行定期检查，以测试太多权限。

### <a name="implement-separation-of-duties"></a>实现职责分离

> [!NOTE]
> 中提到的： FedRamp： AC-04，NIST： AC-5，ISO：6.1.2，PCI 6.4.2，SOC： CM-3，SDL-3

职责分离（也称为责任分离）描述了将敏感任务拆分为多个任务的要求，这些任务分配给不同的用户，通常是为了防止数据泄露。

**如何实现**：

- 确定所需的职责分离级别。 예시: 
  - 在开发/测试和生产环境之间 
  - 与安全相关的任务 vs 数据库管理员（DBA）管理级别任务与开发人员任务。 
    - 示例：审核员，为角色级安全性（RLS）创建安全策略，实现带有 DDL 权限的 SQL 数据库对象。

- 确定访问系统的用户（和自动化过程）的综合层次结构。

- 根据所需的用户组创建角色，并将权限分配给角色。 
  - 对于 Azure 门户或通过 PowerShell 实现的管理级任务，请使用 RBAC 角色。 查找符合要求的内置角色，或者使用可用权限创建自定义 RBAC 角色 
  - 在托管实例中为服务器范围内的任务（创建新的登录名和数据库）创建服务器角色。 
  - 为数据库级任务创建数据库角色。

- 对于某些敏感任务，请考虑创建由证书签名的特殊存储过程，以代表用户执行这些任务。 
  - 示例：[教程：使用证书对存储过程进行签名](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- 使用 Azure Key Vault 中的客户托管密钥实现透明数据加密（TDE），以便在数据所有者和安全所有者之间分离职责。 
  - 请参阅文章[从 Azure 门户配置用于 Azure 存储加密的客户管理的密钥](../storage/common/storage-encryption-keys-portal.md)。 

- 若要确保 DBA 无法看到被视为高度敏感的数据，并且仍可执行 DBA 任务，你可以将 Always Encrypted 与角色分离一起使用。 
  - 请参阅文章[概述 Always Encrypted 的密钥管理](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)、[角色分隔的密钥预配](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)以及[角色分离的列主密钥轮换](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)。 

- 如果在不可行的情况下，可能会导致系统无法正常使用的主要成本和努力，则可以通过使用补偿控制来做出折衷，如： 
  - 进程中的人工干预。 
  - 审核跟踪–有关审核的详细信息，请参阅[审核关键安全事件](#audit-critical-security-events)。

**모범 사례**:

- 请确保将不同的帐户用于开发/测试环境和生产环境。 不同的帐户有助于满足测试 & 生产系统的隔离。

- 避免将权限分配给单个用户。 改为改用角色（数据库或服务器角色）。 拥有角色有助于极大地地处理权限的报告和故障排除。

- 如果权限与所需权限完全匹配，请使用内置角色–如果多个内置角色的所有权限的联合导致了100% 匹配，则还可以同时分配多个角色。 

- 当内置角色授予过多权限或权限不足时，创建和使用自定义角色。 

- 还可以暂时在 T-sql 中的 SQL 代理作业步骤中或使用用于 RBAC 角色的 Azure PIM，在 SQL 代理作业步骤中对责任分配进行动态分离（DSD）。 

- 请确保 Dba 无法访问加密密钥或密钥存储，具有密钥访问权限的安全管理员也不能访问数据库。 

- 请始终确保具有与安全相关的操作的审核线索。 

- 可以检索内置 RBAC 角色的定义，以查看所使用的权限，并通过 Powershell 基于这些权限的摘录和 cumulations 创建自定义角色 

- 由于 db_owner 数据库角色的任何成员都可以更改透明数据加密的安全设置，如（TDE）或更改 SLO，因此应小心授予此成员资格。 另一方面，许多任务（例如更改数据库选项等更改数据库设置）都需要 db_owner 权限。 审核在任何解决方案中扮演着关键角色。

- 不能让 db_owner 仅查看具有权限的用户数据。 如果数据库中存在高度敏感的数据，Always Encrypted 可用于安全地防止 db_owners 或任何其他 DBA 查看它。

> [!NOTE]
> 实现职责分离（SoD）对于安全相关或故障排除任务非常困难。 其他方面（如开发和最终用户角色）更易于隔离。 当其他解决方案不可行时，与大多数相容性相关的控件允许使用备用控制功能，如审核。

对于想要深入了解 SoD 的读者，建议采用以下资源： 

- Azure SQL 데이터베이스의 경우.  
  - [控制和授予数据库对 SQL 数据库和 SQL 数据仓库的访问权限](sql-database-manage-logins.md)
  - [应用程序开发人员的引擎分离职责](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [SQL Server 2014 中的职责分离](https://www.microsoft.com/download/details.aspx?id=39269)
  - [SQL Server 中的签名存储过程](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- 对于 Azure 资源管理：
  - [Azure 的内置角色](../role-based-access-control/built-in-roles.md) 
  - [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)
  - [使用 Azure AD Privileged Identity Management 提升访问权限](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>执行常规代码评审

> [!NOTE]
> 中提到的： PCI：6.3.2、SOC： SDL-3 

职责分离并不局限于数据库中的数据，但包含应用程序代码。 恶意代码可能会绕过安全控制。 在将自定义代码部署到生产环境之前，必须先查看要部署的内容。

**如何实现**：

- 使用支持源代码管理的 Azure Data Studio 数据库工具。 

- 实现隔离的代码部署过程。

- 在提交到 main 分支之前，人员（代码本身的作者除外）必须检查代码是否有可能提升权限风险，并检查恶意数据修改以防止欺诈和恶意访问。 可以使用源代码管理机制完成此操作。

**모범 사례**:

- 标准化：它有助于实现适用于任何代码更新的标准过程。 

- 漏洞评估包含一些规则，这些规则检查过多的权限、使用旧的加密算法和数据库架构中的其他安全问题。 

- 可以在 QA 或测试环境中使用高级威胁防护来进行进一步检查，以扫描易受 SQL 注入攻击的代码。

- 要查找的内容的示例： 
  - 在自动 SQL 代码更新部署中创建用户或更改安全设置。 
  - 存储过程（根据提供的参数）以不一致的方式更新单元中的货币值。 

- 请确保执行此评审的人员是原始代码作者以外的人员，并熟悉代码检查和安全编码。

- 务必了解代码更改的所有源：代码可以在 T-sql 脚本中进行;它可以是以视图、函数、触发器和存储过程形式执行或部署的即席命令。 它也可以是 SQL 代理作业定义（步骤）的一部分，可从 SSIS 包、Azure 数据工厂和其他服务中执行。

## <a name="data-protection"></a>데이터 보호

数据保护是一组功能，用于保护重要信息不受加密或模糊处理的影响。

> [!NOTE]
> Microsoft 对 Azure SQL 数据库进行证实，使其符合 FIPS 140-2 级别1。 这是在验证是否严格使用了 FIPS 140-2 级别1可接受算法和 FIPS 140-2 级别1验证的算法实例之后完成的，这些算法包括与所需密钥长度、密钥管理、密钥生成和密钥存储的一致性。 此证明旨在允许我们的客户在处理数据或交付系统或应用程序的过程中，响应使用 FIPS 140-2 级别1验证实例的需求或要求。 我们定义了上述语句中使用的术语 "FIPS 140-2 Level 1 相容" 和 "FIPS 140-2 Level 1 相容性"，以演示其对美国和加拿大政府使用不同术语 "FIPS 140-2 Level 1 验证" 的预期适用性。 


### <a name="encrypt-data-in-transit"></a>加密传输中的数据

> [!NOTE]
> 中提到的： OSA 实践 #6、ISO 控制系列：加密

在客户端和服务器之间移动数据的同时保护数据。 请参阅[网络安全](#network-security)。

### <a name="encrypt-data-at-rest"></a>미사용 암호화 데이터

> [!NOTE]
> 中提到的： OSA 实践 #6、ISO 控制系列：加密

静态加密是指将数据保留在数据库、日志和备份文件中的加密保护。

**如何实现**：

- 默认情况下，将为在 Azure SQL 数据库中2017之后创建的任何数据库启用[透明数据库加密（TDE）](transparent-data-encryption-azure-sql.md)和服务托管密钥。
- 在托管实例中，如果数据库是作为从本地服务器还原操作的结果创建的，则会遵守原始数据库的 TDE 设置。 如果原始数据库未启用 TDE，我们建议为托管实例手动打开 TDE。

**모범 사례**:

- 不要存储需要在 master 数据库中进行静态加密的数据。 不能用 TDE 加密 master 数据库。

- 如果需要增加透明度并精细控制 TDE 保护，请在 Azure Key Vault 中使用客户管理的密钥。 Azure Key Vault 允许随时撤消权限以使数据库无法访问。 你可以使用 Azure Key Vault 来集中管理 TDE 保护程序以及其他密钥，或使用你自己的计划轮换 TDE 保护程序。

- 如果要在 Azure Key Vault 中使用客户托管的密钥，请遵循文章使用[Azure Key Vault 配置 TDE 的准则](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv)，以及[如何使用 Azure Key Vault 配置异地灾难恢复](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde)。

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>从高权限、未经授权的用户中保护使用的敏感数据

使用中的数据是指在执行 SQL 查询期间数据库系统内存中存储的数据。 如果你的数据库存储敏感数据，你的组织可能需要确保高权限的用户（如组织中的 Microsoft 操作员或 Dba）无法从 SQL Server 进程的内存提取数据并且无法在查询数据库时查看纯文本数据。

**如何实现**：

- 使用[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)确保在 Azure SQL 数据库中不以纯文本形式公开敏感数据，即使在内存/正在使用中也是如此。 这将保护来自数据库管理员（Dba）和云管理员的数据（或可以模拟高特权但未经授权的用户的不良参与者），并使你能够更好地控制谁可以访问你的数据。

**모범 사례**:

- Always Encrypted 不能代替静态数据（TDE）或传输（SSL/TLS），也不能将其用于非敏感数据，以最大程度地降低性能和功能的影响。 建议将 Always Encrypted 与 TDE 和 TLS 结合使用来全面保护静态数据、传输内和使用中的数据。 

- 如果要使用 Always Encrypted 来保护恶意 Dba 的数据，请使用角色分隔来管理 Always Encrypted 密钥。 使用角色分离时，安全管理员会创建物理密钥，DBA 在数据库中创建列主密钥和列加密密钥元数据对象，其中描述了物理密钥。 在此过程中，安全管理员不需要访问数据库，DBA 不需要以纯文本形式访问物理密钥。 
  - 有关详细信息，请参阅[管理密钥和角色分离](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation)。 

- 将列主密钥存储在 Azure Key Vault 中，以方便管理。 避免使用 Windows 证书存储（通常是指分布式密钥存储解决方案，而不是中央密钥管理解决方案），这使得密钥管理非常困难。 

- 仔细考虑使用多个密钥（列主密钥或列加密密钥）的折衷。 保持较小的密钥数以减少密钥管理成本。 通常，每个数据库有一个列主密钥和一个列加密密钥就足以满足稳定状态环境（不是在密钥轮换的中间），除非具有不同的用户组，每个用户组使用不同的密钥并访问不同的数据。  

- 根据符合性要求轮换列主密钥。 如果还需要轮换列加密密钥，请考虑使用在线加密来最大程度地减少应用程序停机时间。 
  - 请参阅[性能和可用性注意事项](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)一文。 

- 如果需要支持对数据进行计算（相等性），请使用确定性加密。 否则，请使用随机加密。 避免将确定性加密用于低熵数据集或具有公开已知分发的数据集。 

- 如果你担心第三方在未经你同意的情况下访问你的数据，请确保能够以纯文本形式访问密钥和数据的所有应用程序和工具在 Microsoft Azure 云外部运行。 如果没有访问密钥，第三方将无法解密数据，除非它们绕过加密。

- Always Encrypted 不能轻松地支持对密钥（和受保护的数据）的临时访问权限。 例如，如果需要与 DBA 共享密钥，以允许 DBA 对敏感数据和加密数据执行一些清理操作，则可靠性的唯一方法是将对数据的访问权限从 DBA 同时旋转列加密密钥和 mmn 用于保护数据的主密钥，这是一项昂贵的操作。 

- 若要访问加密列中的纯文本值，用户需要有权访问 CMK，并保护在保存 CMK 的密钥存储中配置的列。 此外，用户需要具有 VIEW ANY 列主密钥定义和查看任意列加密密钥定义数据库权限。

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>通过加密控制对敏感数据的应用程序用户的访问

加密可以作为一种方法，用于确保只有有权访问加密密钥并可以查看或更新数据的特定应用程序用户。

**如何实现**：

- 使用单元格级加密（CLE）。 有关详细信息，请参阅文章[加密列的数据](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data)。 
- 另外，请考虑使用 Always Encrypted，但请注意下面所列的限制。

**모범 사례**

使用 CLE 时：

- 通过 SQL 权限和角色控制对密钥的访问。 

- 使用 AES （推荐 AES 256）进行数据加密。 不推荐使用算法（RC4、DES 和 TripleDES），不应使用这些算法，因为存在已知的漏洞。 

- 使用非对称密钥/证书（而非密码）保护对称密钥以避免使用3DES。 

- 使用单元级加密通过导出/导入（bacpac 文件）迁移数据库时要小心。 
  - 请参阅在[AZURE SQL 数据库中使用单元格级加密的建议](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)，了解如何防止在迁移数据时丢失密钥和其他最佳做法指南。

使用 Always Encrypted 时，请记住，Always Encrypted 主要用于保护从 Azure SQL Database （云操作员、Dba）的高特权用户使用的敏感数据，请参阅[保护从高特权、未经授权的用户使用的敏感数据](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)。 使用 Always Encrypted 保护应用程序用户的数据时，请注意以下问题：

- 一旦你向用户授予了访问列加密密钥和列主密钥的权限，即为用户授予对敏感数据的访问权限后，若要可靠地撤销该访问权限，你需要轮换列加密密钥，这是一项成本高昂的操作，需要重新加密所有列，将保护列加密密钥。 

- 默认情况下，支持 Always Encrypted 的所有 Microsoft 客户端驱动程序都维护列加密密钥的全局（每个应用程序一个）缓存。 一旦客户端驱动程序通过联系包含列主密钥的密钥存储来获取纯文本列加密密钥，就会缓存纯文本列加密密钥，从而使多用户应用程序的用户隔离数据变得困难。 如果你的应用程序在与密钥存储交互（如 Azure Key Vault）时模拟最终用户，则在用户的查询使用列加密密钥填充缓存后，需要同一密钥但由其他用户触发的后续查询将使用缓存的键。 驱动程序将不会调用密钥存储，并且它不会检查第二个用户是否有权访问列加密密钥。 因此，即使用户无权访问密钥，用户也能够看到已加密的数据。 若要在多用户应用程序内隔离用户，可能需要禁用列加密密钥缓存，这会导致额外的性能开销，因为驱动程序需要联系密钥存储以实现每个数据加密或解密运作.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>防止应用程序用户在保留数据格式时未经授权查看数据
防止未经授权的用户查看数据的另一种方法是对数据进行模糊处理或屏蔽操作，同时保留数据类型和格式，以确保用户应用程序可以继续处理并显示数据。

**如何实现**：

- 使用[动态数据掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)可对表列进行模糊处理。

> [!NOTE]
> Always Encrypted 在动态数据掩码中不起作用。 不能加密和屏蔽同一列，这意味着需要确定所使用的数据的优先级，以及通过动态数据掩码屏蔽应用用户的数据。

**모범 사례**:

> [!NOTE]
> 动态数据掩码不能用于保护高特权用户的数据。 屏蔽策略不适用于具有管理访问权限的用户，如 db_owner。

- 不允许应用程序用户运行即席查询（因为它们可能能够解决动态数据掩码）。  
  - 有关详细信息，请参阅[使用推理或强制技术绕过屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques)一文。  

- 使用适当的访问控制策略（通过 SQL 权限、角色、RLS）来限制用户在屏蔽列中进行更新的权限。 对列创建掩码不会阻止对该列进行更新。 因此，虽然用户在查询被屏蔽列时接收到掩码的数据，但是，如果用户具有写入权限，则这些用户可以更新数据。    

-  动态数据掩码不会保留掩码值的统计属性，这可能会影响查询结果（例如，包含筛选谓词的查询或对掩码数据的联接）。

## <a name="network-security"></a>네트워크 보안
网络安全是指访问控制和最佳实践，以确保传输到 Azure SQL 数据库的数据的安全。

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>将客户端配置为安全连接到 Azure SQL 数据库 
由于依赖于较旧的协议和密码套件，阻止客户端计算机和应用程序连接到 Azure SQL 数据库的已知漏洞。

**如何实现**：

- 确保连接到 Azure SQL 数据库的客户端计算机正在使用[传输层安全性（TLS）](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)。

**모범 사례**:

- 配置所有应用和工具以连接到启用了加密的 SQL 数据库 
  - 加密 = On，TrustServerCertificate = Off （或等效于非 Microsoft 驱动程序）。 

- 如果你的应用程序使用的驱动程序不支持 TLS 或支持较旧版本的 TLS，请尽可能替换该驱动程序。 如果不可行，请仔细评估安全风险。 

- 在连接到 Azure SQL 数据库的客户端计算机上，在每个[传输层安全性（TLS）注册表设置](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)的客户端计算机上禁用受 ssl 2.0、ssl 3.0、tls 1.0 和 tls 1.1 中的漏洞的攻击媒介。 

- 检查[tls/SSL （SCHANNEL SSP）中每个密码套件](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel)的客户端上可用的密码套件，并按[配置 Tls 密码套件顺序](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)专门禁用3des。 

- 如果使用的是托管实例，请使用**代理**连接类型（默认值），因为这会强制从服务器端加密。 **重定向**连接类型当前不支持加密强制，只能在专用 IP 连接上使用。 
  - 有关详细信息，请参阅[AZURE SQL 连接体系结构-连接策略](sql-database-connectivity-architecture.md#connection-policy)。
  - 为代理和重定向连接类型强制实施加密。 

### <a name="minimize-attack-surface"></a>最小化攻击面
通过实现适用于 Azure SQL 数据库的网络访问控制，最大限度地减少可能受到恶意用户攻击的功能的数量。

> [!NOTE]
> 中提到的： OSA 实践 #5

**如何实现**：

在 Azure SQL 数据库服务器（包含单一数据库或弹性池）中：
- 将 "允许访问 Azure 服务" 设置为 "关闭"。

- 使用虚拟网络服务终结点和虚拟网络防火墙规则。

- 使用 "专用链接（预览）"。

在托管实例中：
- 按照[网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)中的指南进行操作。 

**모범 사례**:

- 通过在专用终结点上连接来限制对 Azure SQL 数据库的访问（例如，使用专用数据路径）： 
  - 托管实例可在虚拟网络内隔离，以防止外部访问。 位于同一区域中相同或对等互连的虚拟网络中的应用程序和工具可以直接访问它。 位于不同区域的应用程序和工具可以利用 VNet 到 VNet 连接或 ExpressRoute 线路对等互连来建立连接。 客户应使用网络安全组（NSG）将仅限端口1433的访问限制到需要访问托管实例的资源 
  - 对于 SQL 数据库服务器（包含单一数据库或弹性池），请使用[专用链接](sql-database-private-endpoint-overview.md)功能，该功能为虚拟网络中的 sql 数据库服务器提供专用的专用 IP。 还可以将[虚拟网络服务终结点用于虚拟网络防火墙规则](sql-database-vnet-service-endpoint-rule-overview.md)，以限制对 SQL 数据库服务器的访问。
  - 移动用户应利用点到站点 VPN 连接，通过数据路径进行连接。
  - 连接到本地网络的用户应利用站点到站点 VPN 连接或 ExpressRoute 连接到数据路径。

- 可以通过连接到公共终结点（例如，使用公共数据路径）来访问 Azure SQL 数据库。 应考虑以下最佳做法： 
  - 对于 SQL 数据库服务器，使用[IP 防火墙规则](sql-database-firewall-configure.md)将访问权限限制为仅授权的 IP 地址。
  - 对于托管实例，请使用网络安全组（NSG）将仅限端口3342的访问限制为所需的资源。 有关详细信息，请参阅[通过公共终结点安全使用 AZURE SQL 数据库托管实例](sql-database-managed-instance-public-endpoint-securely.md)。 

> [!NOTE]
> 托管实例公共终结点默认情况下未启用，必须显式启用它。 如果公司策略不允许使用公用终结点，请使用[Azure 策略](../governance/policy/overview.md)来防止首先启用公共终结点。

- 设置 Azure 网络组件： 
  - 遵循[适用于网络安全的 Azure 最佳做法](../security/fundamentals/network-best-practices.md)。
  - 针对[Azure 虚拟网络常见问题（FAQ）](../virtual-network/virtual-networks-faq.md)和计划中所述的每个最佳做法计划虚拟网络（VNet）配置。 
  - 将 VNet 划分为多个子网，并为同一子网分配类似角色的资源（例如，前端与后端资源）。
  - 使用[网络安全组（nsg）](../virtual-network/security-overview.md)控制 Azure VNet 边界内子网之间的流量。
  - 为订阅启用[Azure 网络观察](../network-watcher/network-watcher-monitoring-overview.md)程序来监视入站和出站网络流量。

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>配置用于安全连接到 Azure SQL 数据库的 Power BI

**모범 사례**:

- 对于 Power BI Desktop，请尽可能使用专用数据路径。 

- 确保 Power BI Desktop 正在使用 TLS 1.2 进行连接，方法是在客户端计算机上按照[传输层安全性（TLS）](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)注册表设置设置注册表项。 

- [使用 Power BI 的行级安全性（RLS）](https://docs.microsoft.com/power-bi/service-admin-rls)限制特定用户的数据访问。 

- 对于 Power BI 服务，请使用[本地数据网关](https://docs.microsoft.com/power-bi/service-gateway-onprem)，[并记住限制和注意事项](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)。

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>配置应用服务以安全连接到 Azure SQL 数据库

**모범 사례**:

- 对于简单的 Web 应用，通过公共终结点进行连接需要将 "**允许 Azure 服务**" 设置为 "开"。 

- 将[应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)，以便与托管实例建立专用数据路径连接。 还可以选择使用[应用服务环境（ASE）](../app-service/environment/intro.md)部署 Web 应用。 

- 对于连接到 SQL 数据库服务器中的数据库的 ASE 或 VNet 集成 Web 应用的 Web 应用，可以使用[Vnet 服务终结点和 Vnet 防火墙规则](sql-database-vnet-service-endpoint-rule-overview.md)来限制从特定 VNet 和子网进行的访问，然后将 "**允许 Azure 服务**" 设置为 "关闭"。 还可以通过专用数据路径将 ASE 连接到托管实例。  

- 请确保按本文配置 Web 应用、[使用 Azure App Service 保护 PaaS Web 和移动应用程序的最佳做法](../security/security-paas-applications-using-app-services.md)。 

- 安装[Web 应用程序防火墙（WAF）](../application-gateway/waf-overview.md) ，以防止 web 应用受到常见的攻击和漏洞的侵害。

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>为 azure SQL 数据库的安全连接配置 Azure VM 托管

**모범 사례**:

- 在 Azure Vm 的 Nsg 上结合使用允许和拒绝规则，以控制可从 VM 访问哪些区域。

- 请确保按本文所述为 VM 配置[Azure 中 IaaS 工作负荷的最佳安全方案](../security/azure-security-iaas.md)。

- 确保所有 Vm 都与特定的 VNet 和子网相关联。 

- 根据[有关强制隧道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)的指导，评估是否需要默认路由 0.0.0.0/Internet。 
  - 如果是（例如，前端子网），请保留默认路由。
  - 如果不是（例如，中间层或后端子网），请启用强制隧道，使流量不会通过 Internet 到达本地（即跨界）。 

- 如果要使用对等互连或连接到本地，请实现[可选的默认路由](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes)。 

- 如果需要将 VNet 中的所有流量发送到网络虚拟设备以进行数据包检查，请实现[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)。 

- 使用[VNet 服务终结点](sql-database-vnet-service-endpoint-rule-overview.md)通过 azure 主干网络安全访问 PaaS 服务，例如 azure 存储。 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>防范分布式拒绝服务（DDoS）攻击
分布式拒绝服务（DDoS）攻击是恶意用户尝试将大量网络流量发送到 Azure SQL 数据库的尝试，其目标是使 Azure 基础结构成为惊人，并使其拒绝有效的登录和工作负荷。

> [!NOTE]
> 中提到的： OSA 实践 #9

**如何实现**：

在 Azure 平台中，会自动启用 DDoS 保护。 它包括 always on 流量监视和对公共终结点上网络级别攻击的实时缓解。 

- 使用[Azure DDoS 防护](../virtual-network/ddos-protection-overview.md)来监视与虚拟网络中部署的资源关联的公共 IP 地址。

- 使用[AZURE SQL 数据库的高级威胁防护](sql-database-threat-detection-overview.md)来检测对数据库的拒绝服务（DoS）攻击。

**모범 사례**:

- 遵循[最小化攻击面](#minimize-attack-surface)中所述的做法有助于最大程度地减少 DDoS 攻击威胁。 

- 高级威胁防护**强力强制 SQL 凭据**警报有助于检测暴力破解攻击。 在某些情况下，警报甚至可以区分渗透测试工作负荷。 

- 对于托管连接到 SQL 数据库的应用程序的 Azure VM： 
  - 遵循建议，在 Azure 安全中心中通过面向 Internet 的终结点限制访问。 
  - 使用虚拟机规模集在 Azure Vm 上运行应用程序的多个实例。 
  - 禁用来自 Internet 的 RDP 和 SSH，以防止强力攻击。 

## <a name="monitoring-logging-and-auditing"></a>监视、日志记录和审核  
本部分介绍的功能可帮助您检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。 它还介绍了配置数据库审核以跟踪和捕获数据库事件的最佳实践。

### <a name="protect-databases-against-attacks"></a>保护数据库免遭攻击 
利用高级威胁防护，你可以通过提供针对异常活动的安全警报来检测潜在威胁并做出响应。

**如何实现**：

- 使用[SQL 高级威胁防护](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)来检测异常和可能有害的数据库访问或利用尝试情况，包括：
  - SQL 注入攻击。
  - 凭据盗窃/泄露。
  - 权限滥用。
  - 数据渗透。

**모범 사례**:

- 通过切换到[Azure 安全中心标准层](../security-center/security-center-pricing.md)，为特定 SQL 数据库服务器、托管实例或订阅中的所有 sql 数据库服务器和托管实例配置[高级数据安全](sql-database-advanced-data-security.md#getting-started-with-ads) 。 

- 为了获得完整的调查体验，建议启用 [SQL 数据库审核](sql-database-auditing.md)，以跟踪数据库事件，并将这些事件写入 azure 存储帐户或 azure Log Analytics 工作区中的审核日志。 

### <a name="audit-critical-security-events"></a>审核关键安全事件
跟踪数据库事件有助于了解数据库活动，并深入了解可能指示业务关注点或可疑安全冲突的差异和异常。 它还能实现遵从法规标准。 

**如何实现**：

- 启用 [SQL 数据库审核](sql-database-auditing.md)，以跟踪数据库事件，并将这些事件写入 Azure 存储帐户中的审核日志、Log Analytics 工作区（预览版）或事件中心（预览）。 

- 可以将审核日志写入 Azure 存储帐户、Log Analytics 的工作区中，以便通过 Azure Monitor 日志或使用事件中心进行消耗的事件中心。 이러한 옵션을 조합하여 구성할 수 있으며, 감사 로그는 각각에 대해 작성됩니다. 

**모범 사례**:

- 通过在数据库服务器上配置[SQL 数据库审核](sql-database-auditing.md)来审核事件，将对该服务器上的所有现有数据库和新创建的数据库进行审核。
- 默认情况下，审核策略包括对数据库执行的所有操作（查询、存储过程以及成功和失败的登录），这可能会导致大量的审核日志。 建议客户[使用 PowerShell 配置不同类型的操作和操作组的审核](sql-database-auditing.md#subheading-7)、控制已审核操作的数目，并将事件丢失的风险降到最低。 这将允许客户只捕获真正需要的审核数据。
- 审核日志可以直接在[Azure 门户](https://portal.azure.com/)中使用，也可以从配置的存储位置使用。 


> [!NOTE]
> 根据引入速率，启用 Log Analytics 审核会产生成本。 请注意，使用此[选项](https://azure.microsoft.com/pricing/details/monitor/)时的关联成本，或考虑将审核日志存储在 Azure 存储帐户中。 

**其他资源**：

- [SQL 데이터베이스 감사](sql-database-auditing.md)
- [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>安全审核日志
若要支持职责分离并将 DBA 与审计员分离，可以采取措施限制对存储帐户的访问。 

**如何实现**：

- 将审核日志保存到 Azure 存储时，请确保使用控制对存储帐户的访问权限，将对存储帐户的访问权限限制为最小安全原则。

- 有关详细信息，请参阅[授权访问 Azure 存储](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

**모범 사례**:

- 控制对审核目标的访问是将 DBA 与审计员分离的关键概念。 

- 审核对敏感数据的访问时，请考虑使用数据加密来保护数据，以避免信息泄露给审计员。 有关详细信息，请参阅[从高权限的、未授权的用户中保护使用的敏感数据](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)部分。

## <a name="security-management"></a>보안 관리

本部分介绍管理数据库安全状况的不同方面和最佳实践。 它包括确保数据库配置为符合安全标准的最佳实践，用于发现和跟踪对数据库中可能敏感的数据进行分类和跟踪访问的权限。 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>确保已将数据库配置为满足安全最佳做法 

通过发现和修正潜在的数据库漏洞来主动提高数据库的安全性。

**如何实现**：

- 启用[SQL 漏洞评估](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)（VA）以扫描数据库中的安全问题，并在数据库上定期自动运行。

**모범 사례**:

- 最初，在数据库上运行 VA，并通过修正检查失败的检查 oppose 安全最佳做法，并为可接受的配置设置基线，直到扫描出现 "干净" （所有检查都通过）。  

- 将定期定期扫描配置为每周运行一次，并配置相关人员来接收摘要电子邮件。 

- 查看每个每周扫描后的 VA 摘要。 对于发现的任何漏洞，请从以前的扫描结果中评估偏移，确定是否应解决检查，或者是否有合理的理由来确定是否发生了配置更改。   

- 解决检查并更新相关的基线，或创建用于解决操作的票证项目，并在解决操作之前对其进行跟踪。 

**其他资源**：

- [SQL 취약성 평가](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [SQL 漏洞评估服务帮助你确定数据库漏洞](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>标识并标记敏感数据 

发现可能包含敏感数据的列。 对列进行分类以利用基于敏感的高级审核和保护方案。 

**如何实现**：

- 使用[SQL 数据发现和分类](sql-database-data-discovery-and-classification.md)来发现、分类、标记和保护数据库中的敏感数据。 
  - 在 SQL 数据发现和分类仪表板中查看自动发现创建的分类建议，并接受相关的分类，以便使用分类永久标记敏感数据标志. 
  - 对于未被自动机制发现的任何其他敏感数据字段，手动添加分类。 
- 有关详细信息，请参阅[SQL 数据发现 & 分类](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)。

**모범 사례**:

- 定期监视分类仪表板，准确评估数据库的分类状态。 可以出于相容性和审核目的，将有关数据库分类状态的报表导出或打印到共享中。

- 通过跟踪敏感数据发现规则并标识建议列中用于分类的任何偏差，持续监视 SQL 漏洞评估中建议的敏感数据的状态。  

- 使用根据你的组织的特定需求定制的方式使用分类。 自定义 Azure 安全中心中的[SQL 信息保护](../security-center/security-center-info-protection-policy.md)策略中的信息保护策略（灵敏度标签、信息类型、发现逻辑）。 

### <a name="track-access-to-sensitive-data"></a>跟踪对敏感数据的访问权限 
监视对敏感数据的访问，并捕获对审核日志中敏感数据的查询。

**如何实现**：

- 结合使用 SQL 审核和数据分类。 
  - 在[SQL 数据库审核](sql-database-auditing.md)日志中，可以专门跟踪敏感数据的访问权限，还可以查看信息（如已访问的数据及其敏感度[标签）。](sql-database-data-discovery-and-classification.md#subheading-3) 

**모범 사례**:

- 请参阅审核和数据分类部分的最佳实践： 
  - [审核关键安全事件](#audit-critical-security-events) 
  - [标识并标记敏感数据](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>可视化安全性和符合性状态 

使用统一的基础结构安全管理系统，该系统可增强数据中心（包括 SQL 数据库）的安全状况。 查看有关数据库安全性和符合性状态的建议列表。

**如何实现**：

- 监视[Azure 安全中心](https://azure.microsoft.com/documentation/services/security-center/)的 SQL 相关安全建议和活动威胁。

## <a name="common-security-threats-and-potential-mitigations"></a>常见的安全威胁和潜在的缓解措施

本部分可帮助你查找用于防范特定攻击媒介的安全措施。 通过遵循上述一个或多个安全准则，可以实现大多数缓解措施。

### <a name="security-threat-data-exfiltration"></a>安全威胁：数据渗透

Data 渗透是未经授权从计算机或服务器复制、传输或检索数据。 查看维基百科上的[数据渗透](https://en.wikipedia.org/wiki/Data_exfiltration)的定义。

通过公共终结点连接到 Azure SQL 数据库服务器会带来数据渗透，因为它要求客户将其防火墙打开到公共 Ip。  

**方案 1**： azure VM 上的应用程序连接到 Azure SQL 数据库服务器中的数据库。 恶意参与者获取 VM 的访问权限并对其进行威胁。 在此方案中，数据渗透意味着使用 rogue VM 的外部实体连接到数据库，复制个人数据，并将其存储在 blob 存储或不同订阅中的其他 SQL 数据库中。

**方案 2**：恶意 DBA。 这种情况通常由受管控行业的安全敏感客户引发。 在这种情况下，高权限用户可以将数据从 Azure SQL 数据库复制到未由数据所有者控制的其他订阅。

**潜在缓解**：

目前，Azure SQL 数据库提供以下技术来缓解数据渗透的威胁： 

- 在 Azure Vm 的 Nsg 上结合使用允许和拒绝规则，以控制可从 VM 访问哪些区域。 
- 如果使用 Azure SQL 数据库服务器（包含单一数据库或弹性池），请设置以下各项：
  - 允许 Azure 服务关闭。
  - 仅通过设置 VNet 防火墙规则，允许来自包含 Azure VM 的子网的流量。
  - 使用[专用链接](sql-database-private-endpoint-overview.md)
- 对于托管实例，默认情况下，使用 "专用 IP 访问" 将解决恶意 VM 的第一个数据渗透问题。 启用子网中的子网委托功能，该功能将在托管实例子网上自动设置限制性最强的策略。
- 恶意 DBA 关注性更多是使用托管实例公开的，因为它具有更大的外围应用和网络要求对客户可见。 为此，最大的缓解措施是应用本安全指南中的所有实践，以防止在第一次实施恶意 DBA 方案（不仅适用于数据渗透）。 Always Encrypted 是一种方法，用于通过对敏感数据进行加密来保护敏感数据，并使 DBA 无法访问密钥。

## <a name="security-aspects-of-business-continuity-and-availability"></a>业务连续性和可用性的安全方面

大多数安全标准通过实现冗余和故障转移功能，实现了对数据的可用性，以避免单点故障。 对于灾难方案，保存数据和日志文件的备份通常是一种常见的做法。 以下部分概述了内置于 Azure 中的功能，以及可配置为满足特定需求的其他选项： 

- Azure 提供内置的高可用性：[高可用性和 AZURE SQL Database](sql-database-high-availability.md) 

- 业务关键层包括故障转移组、多可用性区域、完整和差异日志备份，以及默认情况下启用的时间点还原备份：  
  - [高可用性和 Azure SQL 数据库-区域冗余配置](sql-database-high-availability.md#zone-redundant-configuration)
  - [자동화된 백업](sql-database-automated-backups.md)
  - [使用自动数据库备份恢复 Azure SQL 数据库-时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)

- 其他业务连续性功能（如跨不同 Azure 地域的自动故障转移组）可按如下所述进行配置： [AZURE SQL 数据库的业务连续性概述](sql-database-business-continuity.md)

## <a name="next-steps"></a>다음 단계

- 请参阅[AZURE SQL 数据库安全功能概述](sql-database-security-overview.md)