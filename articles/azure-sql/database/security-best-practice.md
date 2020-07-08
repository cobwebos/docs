---
title: 用于解决常见安全要求的操作手册
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 本文提供 Azure SQL 数据库和 Azure SQL 中的常见安全要求和最佳做法托管实例
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 8104302afa84446e2d57c7156f33bc0160e31472
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986770"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>针对 Azure SQL 数据库和 Azure SQL 托管实例解决常见安全要求的操作手册
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文提供了有关如何解决常见安全要求的最佳实践。 并非所有要求都适用于所有环境，你应该向数据库和安全团队咨询要实现哪些功能。

## <a name="solving-common-security-requirements"></a>解决常见的安全要求

本文档提供有关如何使用 Azure SQL 数据库和 Azure SQL 托管实例解决新的或现有应用程序的常见安全要求的指导。 它按高级安全领域进行组织。 若要解决特定的威胁，请参阅[常见安全威胁和潜在缓解措施](#common-security-threats-and-potential-mitigations)部分。 提供的某些建议在将应用程序从本地迁移到 Azure 时适用，不过，本文档不会重点说明迁移方案。

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>本指南涉及的 Azure SQL 数据库部署产品/服务

- [AZURE SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)：[服务器](logical-servers.md)中的[单个数据库](single-database-overview.md)和[弹性池](elastic-pool-overview.md)
- [Azure SQL 托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="deployment-offers-not-covered-in-this-guide"></a>本指南未涵盖的部署产品

- Azure SQL 数据仓库
- Azure SQL VM (IaaS)
- SQL Server

### <a name="audience"></a>目标受众

本指南的目标读者是在保护 Azure SQL 数据库时遇到问题的客户。 对本最佳做法文章感兴趣的角色包括但不限于：

- 安全架构师
- 安全经理
- 合规性主管
- 隐私主管
- 安全工程师

### <a name="using-this-guide"></a><a id="using"></a> 使用本指南

本文档旨在用作现有 [Azure SQL 数据库安全性](security-overview.md)文档的配套资源。

除非另有说明，否则我们建议遵循每个部分中列出的所有最佳做法，以实现相关的目标或要求。 为了帮助客户满足特定的安全合规标准或最佳做法，在适用的情况下，“要求或目标”部分下面会列出重要的法规控制措施。 本文参考了以下安全标准和法规：

- [FedRAMP](https://www.fedramp.gov/documents/)：AC-04、AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html)：CM-3、SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html)：访问控制、加密
- [Microsoft 操作安全保障 (OSA) 做法](https://www.microsoft.com/securityengineering/osa/practices)：做法 #1-6 和 #9
- [NIST 专刊 800-53 安全控制](https://nvd.nist.gov/800-53)：AC-5、AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library)：6.3.2、6.4.2

我们计划持续更新本文列出的建议和最佳做法。 使用本文底部的 "**反馈**" 链接提供对此文档的输入或任何更正。

## <a name="authentication"></a>身份验证

身份验证是证明用户所声明身份的过程。 Azure SQL 数据库和 SQL 托管实例支持两种类型的身份验证：

- SQL 身份验证
- Azure Active Directory 身份验证

> [!NOTE]
> 并非所有工具和第三方应用程序都支持 Azure Active Directory 身份验证。

### <a name="central-management-for-identities"></a>标识的集中管理

集中式标识管理提供以下优势：

- 管理组帐户并控制用户权限，而无需跨服务器、数据库和托管实例重复登录。
- 简化且灵活的权限管理。
- 应用程序的大规模管理。

**如何实现**：

- 使用 Azure Active Directory (Azure AD) 身份验证实现集中式标识管理。

**最佳做法**：

- 创建 Azure AD 租户，[创建用户](../../active-directory/fundamentals/add-users-azure-active-directory.md)来表示人类用户，并创建[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)来表示应用、服务和自动化工具。 服务主体相当于 Windows 和 Linux 中的服务帐户。

- 通过组分配向 Azure AD 主体分配资源访问权限：创建 Azure AD 组，向组授予访问权限，并将各个成员添加到组中。 在数据库中，创建包含的数据库用户用于映射 Azure AD 组。 若要在数据库中分配权限，请将与 Azure AD 组关联的用户置于具有适当权限的数据库角色中。
  - 请参阅文章[通过 SQL 配置和管理 Azure Active Directory 身份验证](authentication-aad-configure.md)以及[通过 SQL 使用 Azure AD 进行身份验证](authentication-aad-overview.md)。
  > [!NOTE]
  > 在 SQL 托管实例中，还可以创建映射到 master 数据库中 Azure AD 主体的登录名。 请参阅 [CREATE LOGIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。

- 使用 Azure AD 组可以简化权限管理，组所有者和资源所有者都可以在组中添加/删除成员。

- 为每个服务器或托管实例的 Azure AD 管理员创建单独的组。

  - 请参阅文章[为服务器预配 Azure Active Directory 管理员](authentication-aad-configure.md#provision-azure-ad-admin-sql-database)。

- 使用 Azure AD 审核活动报告监视 Azure AD 组成员身份的更改。

- 对于托管实例，创建 Azure AD 管理员需要单独的步骤。
  - 请参阅[为托管实例预配 Azure Active Directory 管理员](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)一文。

> [!NOTE]
>
> - Azure AD 身份验证记录在 Azure SQL 审核日志中，而不是记录在 Azure AD 登录日志中。
> - 在 Azure 中授予的 RBAC 权限不适用于 Azure SQL 数据库或 SQL 托管实例权限。 必须使用现有的 SQL 权限手动创建/映射此类权限。
> - 在客户端，Azure AD 身份验证需要访问 internet 或通过用户定义的路由（UDR）连接到虚拟网络。
> - Azure AD 访问令牌缓存在客户端，其生存期取决于令牌配置。 请参阅[Azure Active Directory 中可配置的令牌生存期](../../active-directory/develop/active-directory-configurable-token-lifetimes.md)一文
> - 有关解决 Azure AD 身份验证问题的指南，请参阅以下博客： [Azure AD 故障排除](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991)。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

> 内容来源：OSA 做法 #2，ISO 访问控制 (AC)

Azure 多重身份验证通过要求多种形式的身份验证提供额外的安全性。

**如何实现**：

- 使用条件性访问和使用交互式身份验证在 Azure AD 中[启用多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)。

- 替代方法是为整个 Azure AD 或 AD 域启用多重身份验证。

**最佳做法**：

- 激活 Azure AD 中的条件性访问（需要高级订阅）。
  - 请参阅文章[Azure AD 中的条件性访问](../../active-directory/conditional-access/overview.md)。  

- 使用 Azure AD 条件访问为所选组创建 Azure AD 组并启用多重身份验证策略。
  - 请参阅[规划条件访问部署](../../active-directory/conditional-access/plan-conditional-access.md)一文。

- 可为整个 Azure AD 或与 Azure AD 联合的整个 Active Directory 启用多重身份验证。

- 对于 Azure SQL 数据库和 Azure SQL 托管实例，请使用 Azure AD 交互身份验证模式，其中以交互方式请求密码，然后进行多重身份验证：
  - 在 SSMS 中使用通用身份验证。 请参阅[使用 AZURE SQL 数据库的多重身份验证 Azure AD 身份验证、SQL 托管实例、Azure Synapse （对多重身份验证的 SSMS 支持）](authentication-mfa-ssms-overview.md)一文。
  - 使用 SQL Server Data Tools (SSDT) 中支持的交互式身份验证。 请参阅 [SQL Server Data Tools (SSDT) 中的 Azure Active Directory 支持](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)。
  - 使用支持多重身份验证的其他 SQL 工具。
    - SSMS 向导对导出/提取/部署数据库操作的支持  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)：选项“/ua”
    - [sqlcmd 实用工具](https://docs.microsoft.com/sql/tools/sqlcmd-utility)：选项 -G（交互式）
    - [bcp 实用工具](https://docs.microsoft.com/sql/tools/bcp-utility)：选项 -G（交互式）

- 使用具有多重身份验证支持的交互式身份验证实现应用程序以连接到 Azure SQL 数据库或 Azure SQL 托管实例。
  - 请参阅文章[使用 Azure 多重身份验证连接到 Azure SQL 数据库](active-directory-interactive-connect-azure-sql-db.md)。
  > [!NOTE]
  > 此身份验证模式需要使用基于用户的标识。 如果使用受信任的标识模型来绕过单个 Azure AD 用户身份验证（例如，使用 Azure 资源的托管标识），则不会应用多重身份验证。

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>尽量减少对用户使用基于密码的身份验证

> 内容来源：OSA 做法 #4，ISO 访问控制 (AC)

基于密码的身份验证方法是较弱的身份验证形式。 凭据可能会透露或者被错误地丢弃。

**如何实现**：

- 使用 Azure AD 集成身份验证，此方法可消除密码的使用。

**最佳做法**：

- 使用 Windows 凭据进行单一登录身份验证。 将本地 AD 域与 Azure AD 相联合，并使用 Windows 集成身份验证（适用于 Azure AD 中已加入域的计算机）。
  - 请参阅 [SSMS 对 Azure AD 集成身份验证的支持](authentication-aad-configure.md#active-directory-integrated-authentication)一文。

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>尽量减少对应用程序使用基于密码的身份验证

> 内容来源：OSA 做法 #4，ISO 访问控制 (AC)

**如何实现**：

- 启用 Azure 托管标识。 还可以使用集成式或基于证书的身份验证。

**最佳做法**：

- 使用 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
  - [系统分配的托管标识](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [使用具有托管标识的 Azure App Service 中的 Azure SQL 数据库（不更改代码）](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- 对应用程序使用基于证书的身份验证。
  - 请参阅此[代码示例](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)。

- 对集成的联合域和已加入域的计算机使用 Azure AD 身份验证（参阅上一部分）。
  - 请参阅[集成身份验证的示例应用程序](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)。

### <a name="protect-passwords-and-secrets"></a>保护密码和机密

如果不可避免地需要使用密码，请确保密码受到保护。

**如何实现**：

- 使用 Azure Key Vault 存储密码和机密。 如果适用，请对具有 Azure AD 用户的 Azure SQL 数据库使用多重身份验证。

**最佳做法**：

- 如果无法避免密码或机密的使用，请在 Azure Key Vault 中存储用户密码和应用程序机密，并通过 Key Vault 访问策略管理访问权限。

- 各种应用开发框架还可能提供框架特定的机制来保护应用中的机密。 例如：[ASP.NET Core 应用](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows)。

### <a name="use-sql-authentication-for-legacy-applications"></a>对旧式应用程序使用 SQL 身份验证

SQL 身份验证是指使用用户名和密码连接到 Azure SQL 数据库或 SQL 托管实例时用户的身份验证。 需要在每个服务器或托管实例中创建一个登录名，并在每个数据库中创建一个用户。

**如何实现**：

- 使用 SQL 身份验证。

**最佳做法**：

- 作为服务器或实例管理员，创建登录名和用户。 除非将包含的数据库用户与密码配合使用，否则所有密码将存储在 master 数据库中。
  - 请参阅文章[控制和授予对 Sql 数据库、sql 托管实例和 Azure Synapse Analytics 的数据库访问权限](logins-create-manage.md)。

## <a name="access-management"></a>访问管理

访问管理（也称为授权）是控制和管理授权用户对 Azure SQL 数据库或 SQL 托管实例的访问和权限的过程。

### <a name="implement-principle-of-least-privilege"></a>实施最低特权原则

> 内容来源：FedRamp 控制措施 AC-06，NIST：AC-6，OSA 做法 #3

最低特权原则指出，用户拥有的特权不应超过他们完成任务所需的特权。 有关详细信息，请参阅 [Just Enough Administration](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) 一文。

**如何实现**：

仅分配完成所需任务而需要的[权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)：

- 在 SQL 数据库中：
  - 使用粒度权限和用户定义的数据库角色（或托管实例中的服务器角色）：
    1. 创建所需的角色
       - [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
       - [CREATE SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
    1. 创建所需的用户
       - [CREATE USER](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
    1. 将用户作为成员添加到角色
       - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
       - [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
    1. 然后将权限分配给角色。
       - [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql)
  - 确保不要将用户分配到不必要的角色。

- 在 Azure 资源管理器中：
  - 使用内置角色（如果可用）或自定义 RBAC 角色，并分配必要的权限。
    - [Azure 的内置角色](../../role-based-access-control/built-in-roles.md)
    - [Azure 资源的自定义角色](../../role-based-access-control/custom-roles.md)

**最佳做法**：

以下最佳做法是可选的，但可以改善安全策略的易管理性和支持性：

- 如果可能，请从尽可能低的权限集开始，并在有实际需要（和理由）时逐个添加权限 - 而不要采用相反的方法：逐步去除权限。

- 避免将权限分配给单个用户。 改为以一致的方式使用角色（数据库角色或服务器角色）。 角色能够为报告权限和排查权限问题提供很大的帮助。 （Azure RBAC 仅支持通过角色分配权限。）

- 创建和使用具有所需确切权限的自定义角色。 实践中使用的典型角色：
  - 安全部署
  - 管理员
  - 开发人员
  - 支持人员
  - 审核员
  - 自动化过程
  - 最终用户
  
- 只有当角色的权限与用户所需的权限完全匹配时，才使用内置角色。 可将用户分配到多个角色。

- 请记住，数据库引擎中的权限可应用于以下范围内（范围越小，授予权限的影响越小）：
  - Azure 中的服务器（master 数据库中的特殊角色）
  - 数据库
  - 架构
    - 最佳做法是使用架构在数据库中授予权限。 （另请参阅：[架构设计：建议用于设计安全的架构](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)）
  - 对象（表、视图、过程等）

  > [!NOTE]
  > 不建议在对象级别应用权限，因为此级别会给整个实现带来不必要的复杂性。 如果决定使用对象级权限，应明确阐述这些权限。 这同样适用于列级权限，出于相同的原因，我们更不建议应用此类权限 另请注意，默认情况下，表级 [DENY](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) 不会覆盖列级授权。 这需要激活[通用标准合规性服务器配置](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)。

- 使用[漏洞评估 (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 执行定期检查，以测试权限是否过多。

### <a name="implement-separation-of-duties"></a>实现职责分离

> 内容来源：FedRamp：AC-04，NIST：AC-5，ISO：6.1.2、PCI 6.4.2，SOC：CM-3、SDL-3

“职责分离”描述将敏感任务拆分为要分配给不同用户的多个任务的要求。 职责分离有助于防止数据违规。

**如何实现**：

- 识别所需的职责分离级别。 示例:
  - 在开发/测试环境与生产环境之间
  - 安全相关的任务、数据库管理员 (DBA) 管理级别任务与开发人员任务。
    - 示例:审核员为角色级安全性 (RLS) 创建安全策略，并使用 DDL 权限实现 SQL 数据库对象。

- 识别有权访问系统的用户（和自动化过程）的综合层次结构。

- 根据所需的用户组创建角色，并将权限分配给角色。
  - 对于在 Azure 门户中或通过 PowerShell 自动化完成的管理级任务，请使用 RBAC 角色。 查找符合要求的内置角色，或者使用可用权限创建自定义 RBAC 角色
  - 在托管实例中为服务器范围的任务（创建新的登录名和数据库）创建服务器角色。
  - 为数据库级任务创建数据库角色。

- 对于某些敏感任务，考虑创建由证书签名的特殊存储过程，以代表用户执行这些任务。 数字签名存储过程的一个重要优点是，如果更改了该过程，则会立即删除授予该过程的旧版本的权限。
  - 示例：[教程：使用证书为存储过程签名](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- 使用 Azure Key Vault 中客户管理的密钥实现透明数据加密 (TDE)，以便在数据所有者与安全所有者之间实现职责分离。
  - 请参阅[通过 Azure 门户配置客户管理的密钥用于 Azure 存储加密](../../storage/common/storage-encryption-keys-portal.md)一文。

- 为了确保 DBA 无法看到高度敏感的数据但仍可执行 DBA 任务，可将 Always Encrypted 与角色分离配合使用。
  - 请参阅文章 [Always Encrypted 密钥管理概述](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)、[使用角色分离的密钥预配](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)和[使用角色分离的列主密钥轮换](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)。

- 如果使用 Always Encrypted 不可行（最起码在不付出极大成本和工作量的情况下做不到这一点，但如果付出，甚至可能会导致系统几乎不可用），可以通过补偿性的控制措施来采取折衷办法，例如：
  - 在过程中进行人工干预。
  - 审核线索 – 有关审核的详细信息，请参阅[审核关键安全事件](#audit-critical-security-events)。

**最佳做法**：

- 确保将不同的帐户用于开发/测试环境和生产环境。 不同的帐户有助于满足测试和生产系统的隔离。

- 避免将权限分配给单个用户。 改为以一致的方式使用角色（数据库角色或服务器角色）。 使用角色能够为报告权限和排查权限问题提供很大的帮助。

- 当权限与所需权限完全匹配时使用内置角色 – 如果多个内置角色的所有权限的联合导致 100% 匹配，则还可以同时分配多个角色。

- 当内置角色授予的权限过多或不足时，创建并使用用户定义的角色。

- 还可以在 T-SQL 的 SQL 代理作业步骤中或使用适用于 RBAC 角色的 Azure PIM，暂时执行角色分配（也称为动态职责分离 (DSD)）。

- 确保 DBA 无权访问加密密钥或密钥存储，而有权访问密钥的安全管理员无权访问数据库。 使用[可扩展密钥管理（EKM）](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-ekm)可以使此分离更容易实现。 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)可用于实现 EKM。

- 始终确保针对安全相关的操作提供审核线索。

- 可以检索内置 RBAC 角色的定义以查看所用的权限，并通过 PowerShell 根据这些信息的摘录和累积创建自定义角色。

- 由于 db_owner 数据库角色的任何成员都可以更改透明数据加密 (TDE) 等安全设置或更改 SLO，因此，应谨慎地授予此成员身份。 但是，许多任务要求使用 db_owner 特权。 例如，更改数据库选项等任何数据库设置的任务。 在任何解决方案中，审核都发挥着关键的作用。

- 无法限制 db_owner 的权限，因此应阻止管理帐户查看用户数据。 如果数据库中包含高度敏感的数据，可以使用 Always Encrypted 来安全阻止 db_owners 或任何其他 DBA 查看这些数据。

> [!NOTE]
> 对安全相关的任务或故障排除任务实现职责分离 (SoD) 会有难度。 其他方面（例如开发和最终用户角色）更易于分离。 当其他解决方案不可行时，大多数合规性相关的控制措施允许使用替代的控制功能，例如审核。

对于想要深入了解 SoD 的读者，建议阅读以下资源：

- 对于 Azure SQL 数据库和 SQL 托管实例：  
  - [控制和授予数据库访问权限](logins-create-manage.md)
  - [应用程序开发人员的引擎职责分离](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [职责分离](https://www.microsoft.com/download/details.aspx?id=39269)
  - [为存储过程签名](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- 对于 Azure 资源管理：
  - [Azure 的内置角色](../../role-based-access-control/built-in-roles.md)
  - [Azure 资源的自定义角色](../../role-based-access-control/custom-roles.md)
  - [使用 Azure AD Privileged Identity Management 提升访问权限](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>执行定期代码评审

> 内容来源：PCI：6.3.2，SOC：SDL-3

职责分离并不局限于数据库中的数据，但包含应用程序代码。 恶意代码可能会绕过安全控制。 在将自定义代码部署到生产环境之前，必须评审要部署的内容，这一点至关重要。

**如何实现**：

- 使用支持源代码管理的数据库工具，例如 Azure Data Studio。

- 实现分离的代码部署过程。

- 在提交到主分支之前，必须由某个人员（代码本身的作者除外）检查代码是否存在提升特权的风险，以及是否存在恶意的数据修改，以防止出现欺诈和恶意访问。 可以使用源代码管理机制实现此目的。

**最佳做法**：

- 标准化：实现每次更新代码时都要遵循的标准过程会很有帮助。

- 漏洞评估中的规则可以检查是否存在过多的权限、是否使用了旧加密算法，以及数据库架构中是否存在其他安全问题。

- 可以在 QA 或测试环境中使用高级威胁防护来执行更多的检查，此技术将扫描容易受到 SQL 注入攻击的代码。

- 要注意的方面的示例：
  - 从自动化 SQL 代码更新部署内部创建用户或更改安全设置。
  - 某个存储过程根据提供的参数以不一致的方式更新单元格中的货币值。

- 确保执行评审的人员是除原始代码作者以外的个人，且熟悉代码评审和安全编码。

- 确保知道所有代码更改来源。 代码可能位于 T-SQL 脚本中。 它可能是要以视图、函数、触发器和存储过程形式执行或部署的临时命令。 它可能是 SQL 代理作业定义（步骤）的一部分。 它还可能从 SSIS 包、Azure 数据工厂和其他服务的内部执行。

## <a name="data-protection"></a>数据保护

数据保护是通过加密或模糊处理来防止重要信息遭到透露的一组功能。

> [!NOTE]
> Microsoft 对 Azure SQL 数据库和 SQL 托管实例的证实符合 FIPS 140-2 级别1。 认证过程中已确认它严格使用 FIPS 140-2 级别 1 可接受的算法，以及这些算法的、经 FIPS 140-2 级别 1 验证的实例，包括符合所需密钥长度、密钥管理、密钥生成和密钥存储的要求。 此项认证意味着，我们的客户在处理数据或者交付系统或应用程序的过程中，可以满足使用 FIPS 140-2 级别 1 验证实例的需求或要求。 我们定义了上述语句中使用的术语 "FIPS 140-2 Level 1 相容" 和 "FIPS 140-2 Level 1 相容性"，以演示其对美国和加拿大政府使用不同术语 "FIPS 140-2 Level 1 验证" 的预期适用性。

### <a name="encrypt-data-in-transit"></a>加密传输中的数据

> 内容来源：OSA 做法 #6，ISO 控制系列：Cryptography

当数据在客户端与服务器之间移动时为其提供保护。 请参阅[网络安全性](#network-security)。

### <a name="encrypt-data-at-rest"></a>静态数据加密

> 内容来源：OSA 做法 #6，ISO 控制系列：Cryptography

静态加密是指对数据库、日志和备份文件中保存的数据进行加密保护。

**如何实现**：

- 默认情况下，将为在 Azure SQL 数据库和 SQL 托管实例中2017之后创建的任何数据库启用[透明数据库加密（TDE）](transparent-data-encryption-tde-overview.md)和服务托管密钥。
- 在托管实例中，如果数据库是使用本地服务器从还原操作创建的，则会遵循原始数据库的 TDE 设置。 如果未为原始数据库启用 TDE，则我们建议手动为托管实例启用 TDE。

**最佳做法**：

- 不要将需要静态加密的数据存储在 master 数据库中。 无法使用 TDE 加密 master 数据库。

- 如果需要提高透明度并精细控制 TDE 保护，请使用 Azure Key Vault 中客户管理的密钥。 Azure Key Vault 允许随时撤销权限，使数据库不可访问。 可以集中管理 TDE 保护器及其他密钥，或使用 Azure Key Vault 按自己的计划轮换 TDE 保护器。

- 如果使用 Azure Key Vault 中客户管理的密钥，请参阅文章[有关使用 Azure Key Vault 配置 TDE 的指导原则](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv)和[如何使用 Azure Key Vault 配置异地灾难恢复](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde)。

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>防止未经授权的高特权用户查看使用中的敏感数据

使用中的数据是指在执行 SQL 查询期间存储在数据库系统内存中的数据。 如果数据库存储敏感数据，则组织可能需要确保防止高特权用户查看数据库中的敏感数据。 高特权用户（如组织中的 Microsoft 操作员或 Dba）应该能够管理数据库，但不能从 SQL 进程的内存或通过查询数据库来查看并可能窃取敏感数据。

需要确定哪些数据是敏感的，以及敏感数据是否必须在内存中加密并且不可供管理员以明文形式访问，用于确定这些事项的策略特定于你的组织以及你需要遵守的合规性规定。 请参阅相关要求：[识别并标记敏感数据](#identify-and-tag-sensitive-data)。

**如何实现**：

- 使用[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)确保不会以纯文本形式在 Azure sql 数据库或 SQL 托管实例中公开敏感数据，即使在内存/正在使用中也是如此。 Always Encrypted 可以防止数据库管理员 (DBA) 和云管理员（或者可以仿冒未经授权的高特权用户的恶意行动者）查看数据，并使你能够以更高的力度控制谁可以访问数据。

**最佳做法**：

- Always Encrypted 不能取代静态数据加密 (TDE) 或传输中数据加密 (SSL/TLS)。 为了尽量减轻对性能和功能的影响，请不要将 Always Encrypted 用于非敏感数据。 建议将 Always Encrypted 与 TDE 和传输层安全性 (TLS) 结合使用，以全面保护静态数据、传输中的数据和使用中的数据。

- 在生产数据库中部署 Always Encrypted 之前，请先评估对所识别出的敏感数据列进行加密会带来的影响。 通常情况下，Always Encrypted 会降低对加密列的查询功能，并具有其他限制，如 [Always Encrypted - 功能详细信息](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)中所列。 因此，你可能需要在客户端重构你的应用程序来重新实现查询不支持的功能，或者/并且重构你的数据库架构，包括存储过程、函数、视图和触发器的定义。 如果现有应用程序未遵守 Always Encrypted 的限制，则可能无法使用加密列。 虽然支持 Always Encrypted 的 Microsoft 工具、产品和服务的生态系统在不断增长，但它们中还是有许多不能使用加密列。 加密列还可能会影响查询性能，具体取决于工作负荷的特征。

- 如果使用 Always Encrypted 来防止恶意 DBA 查看数据，请通过角色分离来管理 Always Encrypted 密钥。 安全管理员可以使用角色分离来创建物理密钥。 DBA 创建列主密钥和列加密密钥元数据对象，这些对象描述数据库中的物理密钥。 在此过程中，安全管理员不需要访问数据库，且 DBA 不需要访问纯文本形式的物理密钥。
  - 有关详细信息，请参阅[使用角色分离管理密钥](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation)一文。

- 将列主密钥存储在 Azure Key Vault 中，以方便管理。 避免使用会使密钥管理变得困难的 Windows 证书存储（以及一般的分布式密钥存储解决方案，而不是集中式密钥管理解决方案）。

- 仔细考虑使用多个密钥（列主密钥或列加密密钥）的利弊。 保留少量的密钥以减小密钥管理成本。 在稳定态的环境中（而不是在密钥轮换的中途），为每个数据库准备一个列主密钥和一个列加密密钥通常已足够。 如果有不同的用户组，而每个组使用不同的密钥并访问不同的数据，则可能需要更多的密钥。  

- 根据合规要求轮换列主密钥。 如果还需要轮换列加密密钥，请考虑使用在线加密来尽量减少应用程序停机时间。
  - 请参阅[性能和可用性注意事项](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)一文。

- 如果需要支持数据计算（相等性），请使用确定性加密。 否则请使用随机加密。 避免将确定性加密用于低熵数据集或采用众所周知分布形式的数据集。

- 如果你担心第三方在合法的情况下访问你的数据，请确保能够以纯文本形式访问密钥和数据的所有应用程序和工具在 Microsoft Azure 云的外部运行。 如果第三方无权访问密钥，则除非绕过加密，否则他们无法解密数据。

- Always Encrypted 无法轻松支持授予对密钥（和受保护数据）的临时访问权限。 例如，如果需要与 DBA 共享密钥，使 DBA 能够对敏感数据和加密的数据执行一些清理操作。 可靠撤销 DBA 的数据访问权限的唯一方法是，同时轮换用于保护数据的列加密密钥和列主密钥，而这是一项开销较高的操作。

- 若要访问已加密列中的纯文本值，用户需要有权访问用于保护列的列主密钥 (CMK)（在保存 CMK 的密钥存储中进行配置）。 用户还需要拥有“查看任何列主密钥定义”和“查看任何列加密密钥定义”数据库权限。**** ****

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>通过加密控制应用程序用户对敏感数据的访问

可以使用加密来确保只有有权访问加密密钥的特定应用程序用户才能查看或更新数据。

**如何实现**：

- 使用单元级加密 (CLE)。 有关详细信息，请参阅[加密数据列](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data)一文。
- 使用 Always Encrypted，但要注意其限制。 下面列出了限制。

**最佳实践**

使用 CLE 时：

- 通过 SQL 权限和角色控制对密钥的访问。

- 使用 AES (推荐 AES 256) 进行数据加密。 由于存在已知漏洞，RC4、DES 和 TripleDES 等算法已遭弃用，请不要使用它们。

- 使用非对称密钥/证书（而不是密码）来保护对称密钥，以避免使用 3DES。

- 通过导出/导入（bacpac 文件）使用单元级加密迁移数据库时请小心。
  - 有关在迁移数据时如何防止丢失密钥以及其他最佳做法指导，请参阅[有关在 Azure SQL 数据库中使用单元级加密的建议](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)。

请记住，Always Encrypted 主要用于防止 Azure SQL 数据库的高特权用户（云操作员、DBA）查看使用中的敏感数据 - 请参阅[防止防止未经授权的高特权用户查看使用中的敏感数据](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)。 使用 Always Encrypted 防止应用程序用户查看数据时，请注意以下难点：

- 默认情况下，支持 Always Encrypted 的所有 Microsoft 客户端驱动程序都会维护列加密密钥的全局缓存（每个应用程序一个缓存）。 在客户端驱动程序通过联系保存列主密钥的密钥存储获取纯文本列加密密钥后，将会缓存纯文本列加密密钥。 这使得将数据与多用户应用程序的用户相隔离变得困难。 如果应用程序在与密钥存储交互（例如 Azure Key Vault）时模拟最终用户，则在用户的查询使用列加密密钥填充缓存之后，需要同一个密钥但由其他用户触发的后续查询将使用缓存的密钥。 驱动程序不会调用密钥存储，也不会检查第二个用户是否有权访问列加密密钥。 因此，即使用户无权访问密钥，也可以查看加密的数据。 若要在多用户应用程序中实现用户隔离，可以禁用列加密密钥缓存。 禁用缓存会导致性能开销增大，因为驱动程序需要联系密钥存储来完成每个数据加密或解密操作。

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>在保留数据格式的同时防止应用程序用户在未经授权的情况下查看数据

防止未经授权的用户查看数据的另一种方法是对数据进行模糊处理或掩码，同时保留数据类型和格式，以确保用户应用程序可以继续处理和显示数据。

**如何实现**：

- 使用[动态数据掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)来模糊处理表列。

> [!NOTE]
> Always Encrypted 不能与动态数据掩码配合工作。 无法加密和掩码同一个列，这意味着，需确定是要优先保护使用中的数据，还是通过动态数据掩码来对应用用户掩码数据。

**最佳做法**：

> [!NOTE]
> 动态数据掩码不可用于防止高特权用户查看数据。 掩码策略不适用于拥有管理访问权限的用户，例如 db_owner。

- 不要允许应用用户运行临时查询（因为他们也许可以克服动态数据掩码）。  
  - 有关详细信息，请参阅[使用推理或暴力破解技术绕过掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques)一文。  

- 使用适当的访问控制策略（通过 SQL 权限、角色、RLS）来限制用户在掩码列中进行更新的权限。 对列进行掩码不会阻止对该列进行更新。 如果查询掩码列时收到掩码数据的用户拥有写入权限，则他们可以更新这些数据。

- 动态数据掩码不会保留掩码值的统计属性。 这可能会影响查询结果（例如，包含筛选谓词的查询或者对掩码数据的联接）。

## <a name="network-security"></a>网络安全性

网络安全性是指用于保护传输到 Azure SQL 数据库的数据的访问控制和最佳做法。

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>将我的客户端配置为安全连接到 SQL 数据库/SQL 托管实例

关于如何通过连接到 Azure SQL 数据库和 SQL 托管实例来防止客户端计算机和应用程序具有众所周知的漏洞（例如，使用较旧的 TLS 协议和密码套件）的最佳做法。

**如何实现**：

- 确保连接到 Azure SQL 数据库和 SQL 托管实例的客户端计算机使用[传输层安全性（TLS）](security-overview.md#transport-layer-security-encryption-in-transit)。

**最佳做法**：

- 配置所有应用和工具以连接到启用了加密的 SQL 数据库
  - Encrypt = On，TrustServerCertificate = Off（或者在非 Microsoft 驱动程序中配置相应的设置）。

- 如果应用使用的驱动程序不支持 TLS 或者支持早期版本的 TLS，请尽可能地更换驱动程序。 如果无法做到这一点，请认真评估安全风险。

- 减少通过 SSL 2.0、SSL 3.0、TLS 1.0 和 TLS 1.1 中的漏洞发起的攻击途径：根据[传输层安全性 (TLS) 注册表设置](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)，在连接到 Azure SQL 数据库的客户端计算机上禁用相关的途径。

- 检查客户端上的密码套件：[TLS/SSL (Schannel SSP) 中的密码套件](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel)。 具体而言，根据[配置 TLS 密码套件顺序](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)禁用 3DES。

- 对于 Azure SQL 数据库和 SQL 托管实例，对代理和重定向连接类型强制进行加密。 对于 Azure SQL 托管实例，请使用**代理**连接类型（默认值），因为这会强制从服务器端加密。 “重定向”连接类型目前不支持加密强制，仅在专用 IP 连接上可用。****

- 有关详细信息，请参阅[AZURE SQL 数据库连接体系结构-连接策略](connectivity-architecture.md#connection-policy)。

### <a name="minimize-attack-surface"></a>最小化攻击面

尽量减少恶意用户可以攻击的特征数。 对 Azure SQL 数据库实现网络访问控制。

> 内容来源：OSA 做法 #5

**如何实现**：

在 SQL 数据库中：

- 在服务器级别将 "允许访问 Azure 服务" 设置为 "关闭"
- 使用 VNet 服务终结点和 VNet 防火墙规则。
- 使用 "专用链接（预览）"。

在 SQL 托管实例：

- 遵循[网络要求](../managed-instance/connectivity-architecture-overview.md#network-requirements)中的指导原则。

**最佳做法**：

- 通过在专用终结点上连接来限制对 Azure SQL 数据库和 SQL 托管实例的访问（例如，使用专用数据路径）：
  - 托管实例可在虚拟网络内隔离，以防止外部访问。 位于同一区域中相同或对等互连的虚拟网络中的应用程序和工具可以直接访问它。 位于不同区域的应用程序和工具可以使用虚拟网络到虚拟网络连接或 ExpressRoute 线路对等互连来建立连接。 客户应使用网络安全组（NSG）来限制仅允许访问托管实例的资源访问端口1433。
  - 对于 SQL 数据库，请使用[专用链接](../../private-link/private-endpoint-overview.md)功能，该功能为虚拟网络中的服务器提供专用专用 IP。 还可以将[虚拟网络服务终结点用于虚拟网络防火墙规则](vnet-service-endpoint-rule-overview.md)，以限制对服务器的访问。
  - 移动用户应该使用点到站点 VPN 连接，通过数据路径进行连接。
  - 连接到本地网络的用户应使用站点到站点 VPN 连接或 ExpressRoute 连接到数据路径。

- 可以通过连接到公共终结点（例如，使用公共数据路径）来访问 Azure SQL 数据库和 SQL 托管实例。 应考虑以下最佳做法：
  - 对于 SQL 数据库中的服务器，请使用[IP 防火墙规则](firewall-configure.md)将访问权限限制为仅授权的 IP 地址。
  - 对于 SQL 托管实例，请使用网络安全组（NSG）将仅限端口3342的访问限制为所需的资源。 有关详细信息，请参阅[使用公用终结点安全地使用托管实例](../managed-instance/public-endpoint-overview.md)。

> [!NOTE]
> 默认情况下，不启用 SQL 托管实例公共终结点，并且必须显式启用它。 如果公司政策禁止使用公共终结点，请首先使用 [Azure Policy](../../governance/policy/overview.md) 来防止启用公共终结点。

- 设置 Azure 网络组件：
  - 按照 [Azure 网络安全最佳做法](../../security/fundamentals/network-best-practices.md)进行操作。
  - 针对[Azure 虚拟网络常见问题（FAQ）](../../virtual-network/virtual-networks-faq.md)和计划中所述的每个最佳做法规划虚拟网络配置。
  - 将虚拟网络细分为多个子网，并为同一子网（例如，前端和后端资源）分配类似角色的资源。
  - 使用[网络安全组（nsg）](../../virtual-network/security-overview.md)来控制 Azure 虚拟网络边界内子网之间的流量。
  - 为订阅启用 [Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)，以监视入站和出站网络流量。

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>配置与 SQL 数据库/SQL 的安全连接 Power BI 托管实例

**最佳做法**：

- 对于 Power BI Desktop，请尽可能地使用专用数据路径。

- 根据[传输层安全性 (TLS) 注册表设置](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)在客户端计算机上设置注册表项，确保 Power BI Desktop 使用 TLS1.2 进行连接。

- 通过 [Power BI 行级安全性 (RLS)](https://docs.microsoft.com/power-bi/service-admin-rls) 限制特定用户的数据访问权限。

- 对于 Power BI 服务，请使用[本地数据网关](https://docs.microsoft.com/power-bi/service-gateway-onprem)，同时请记住[限制和注意事项](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)。

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>配置应用服务以安全连接到 SQL 数据库/SQL 托管实例

**最佳做法**：

- 对于简单的 Web 应用，通过公共终结点进行连接需要将“允许 Azure 服务”设置为“打开”。****

- [将应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)，以通过专用数据路径连接到托管实例。 （可选）还可以部署采用[应用服务环境 (ASE)](../../app-service/environment/intro.md) 的 Web 应用。

- 对于使用 ASE 或虚拟网络集成 Web 应用连接到 SQL 数据库中的数据库的 Web 应用，可以使用[虚拟网络服务终结点和虚拟网络防火墙规则](vnet-service-endpoint-rule-overview.md)来限制从特定虚拟网络和子网的访问。 然后，将“允许 Azure 服务”设置为“关闭”。**** 还可以通过专用数据路径将 ASE 连接到 SQL 托管实例中的托管实例。  

- 请确保按本文配置 Web 应用、[使用 Azure App Service 保护 "平台即服务" （PaaS） Web 和移动应用程序的最佳实践](../../security/fundamentals/paas-applications-using-app-services.md)。

- 安装 [Web 应用程序防火墙 (WAF)](../../web-application-firewall/ag/ag-overview.md)，以防止 Web 应用遭到常见的恶意利用和出现漏洞。

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>配置 Azure 虚拟机托管以便安全连接到 SQL 数据库/SQL 托管实例

**最佳做法**：

- 在 Azure 虚拟机的 Nsg 上结合使用允许和拒绝规则，以控制可从 VM 访问哪些区域。

- 请确保按本文所述为 VM 配置[Azure 中 IaaS 工作负荷的最佳安全方案](../../security/fundamentals/iaas.md)。

- 确保所有 Vm 都与特定的虚拟网络和子网相关联。

- 根据[关于强制隧道](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)中的指导，评估是否需要默认路由 0.0.0.0/Internet。
  - 如果需要（例如在前端子网中），请保留默认路由。
  - 如果不需要（例如在中间层或后端子网中），请启用强制隧道，使流量不会通过 Internet 抵达本地（即跨界）。

- 如果使用对等互连或者连接到本地，请实现[可选默认路由](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes)。

- 如果需要将虚拟网络中的所有流量发送到网络虚拟设备进行数据包检查，请执行[用户定义的路由](../../virtual-network/virtual-networks-udr-overview.md#user-defined)。

- 使用[虚拟网络服务终结点](vnet-service-endpoint-rule-overview.md)通过 azure 主干网络安全访问 PaaS 服务，例如 azure 存储。

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>防范分布式拒绝服务（DDoS）攻击

分布式拒绝服务（DDoS）攻击是恶意用户尝试将大量网络流量发送到 Azure SQL 数据库的尝试，其目标是使 Azure 基础结构成为惊人，并使其拒绝有效的登录和工作负荷。

> 中提到的： OSA 实践 #9

**如何实现**：

在 Azure 平台中，会自动启用 DDoS 保护。 它包括 always on 流量监视和对公共终结点上网络级别攻击的实时缓解。

- 使用[Azure DDoS 防护](../../virtual-network/ddos-protection-overview.md)来监视与虚拟网络中部署的资源关联的公共 IP 地址。

- 使用[AZURE SQL 数据库的高级威胁防护](threat-detection-overview.md)来检测对数据库的拒绝服务（DoS）攻击。

**最佳做法**：

- 遵循[最小化攻击面](#minimize-attack-surface)中所述的做法有助于最大程度地减少 DDoS 攻击威胁。

- 高级威胁防护**强力强制 SQL 凭据**警报有助于检测暴力破解攻击。 在某些情况下，警报甚至可以区分渗透测试工作负荷。

- 对于托管连接到 SQL 数据库的应用程序的 Azure VM：
  - 遵循建议，在 Azure 安全中心中通过面向 Internet 的终结点限制访问。
  - 使用虚拟机规模集在 Azure Vm 上运行应用程序的多个实例。
  - 禁用来自 Internet 的 RDP 和 SSH，以防止强力攻击。

## <a name="monitoring-logging-and-auditing"></a>监视、日志记录和审核

本部分所述的功能可帮助你检测异常活动，这些活动指示非同寻常或者潜在有害的访问或恶意利用数据库的企图。 本部分还将提供有关配置数据库审核来跟踪和捕获数据库事件的最佳做法。

### <a name="protect-databases-against-attacks"></a>防范数据库遭到攻击

高级威胁防护可在发生异常活动时提供安全警报，让我们检测潜在威胁并做出响应。

**如何实现**：

- 使用[适用于 SQL 的高级威胁防护](threat-detection-overview.md#alerts)来检测非同寻常或者潜在有害的访问或恶意利用数据库的企图，包括：
  - SQL 注入攻击。
  - 凭据盗窃/泄露。
  - 特权滥用。
  - 数据透露。

**最佳做法**：

- 为特定服务器或托管实例配置[高级数据安全性（ADS）](advanced-data-security.md#getting-started-with-ads)   。 你还可以通过切换到[Azure 安全中心标准层](../../security-center/security-center-pricing.md)，在订阅中配置所有服务器和托管实例的广告。

- 若要获得完整的调查体验，建议启用  [SQL 数据库审核](../../azure-sql/database/auditing-overview.md)。 使用审核可以跟踪数据库事件，并将这些事件写入到 Azure 存储帐户或 Azure Log Analytics 工作区中的审核日志。

### <a name="audit-critical-security-events"></a>审核关键安全事件

跟踪数据库事件有助于了解数据库活动。 可以洞察可能指示业务关注点或可疑安全违规的差异与异常。 此措施还有助于遵守法规标准。

**如何实现**：

- 启用 [SQL 数据库审核](../../azure-sql/database/auditing-overview.md)或[托管实例审核](../managed-instance/auditing-configure.md)，以跟踪数据库事件并将其写入 Azure 存储帐户中的审核日志、Log Analytics 工作区（预览版）或事件中心（预览）。

- 可将审核日志写入 Azure 存储帐户、写入 Log Analytics 工作区（供 Azure Monitor 日志使用），或写入事件中心（供事件中心使用）。 可以将这些选项随意组合起来进行配置，审核日志会写入到每一个之中。

**最佳做法**：

- 通过在服务器上配置[SQL 数据库审核](../../azure-sql/database/auditing-overview.md)或[托管实例审核](../managed-instance/auditing-configure.md)来审核事件，将对该服务器上的所有现有数据库和新创建的数据库进行审核。
- 审核策略默认包括对数据库执行的所有操作（查询、存储过程，以及成功和失败的登录），这可能会导致生成大量的审核日志。 建议客户[使用 PowerShell 对不同类型的操作和操作组配置审核](../../sql-database/sql-database-auditing.md#manage-auditing)。 此项配置有助于控制审核的操作数量，并将事件丢失的风险降到最低。 自定义审核配置允许客户只捕获所需的审核数据。
- 可以在 [Azure 门户](https://portal.azure.com/)中直接使用审核日志，或者从配置的存储位置使用。

> [!NOTE]
> 启用在 Log Analytics 中进行审核会根据引入速率产生成本。 请注意，使用此[选项](https://azure.microsoft.com/pricing/details/monitor/)会产生相关的成本；或者，可以考虑将审核日志存储在 Azure 存储帐户中。

**其他资源**：

- [SQL 数据库审核](../../azure-sql/database/auditing-overview.md)
- [SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>保护审核日志

限制对存储帐户的访问，以支持职责分离，并将 DBA 与审核员区分开来。

**如何实现**：

- 将审核日志保存到 Azure 存储时，请确保按照最低安全原则来限制对存储帐户的访问。 控制谁有权访问存储帐户。
- 有关详细信息，请参阅[授权访问 Azure 存储](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

**最佳做法**：

- 控制对审核目标的访问是将 DBA 与审核员相区分时使用的重要概念。

- 审核对敏感数据的访问时，请考虑使用数据加密来保护数据，以免向审核员透露信息。 有关详细信息，请参阅[防止未经授权的高特权用户查看使用中的敏感数据](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)部分。

## <a name="security-management"></a>安全管理

本部分介绍有关管理数据库安全态势的各个方面和最佳做法。 其中提供了有关确保根据安全标准配置数据库、发现漏洞，以及分类和跟踪对数据库中潜在敏感数据的访问的最佳做法。

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>确保数据库已配置为符合安全最佳做法

通过发现并修正潜在数据库漏洞来主动改善数据库的安全性。

**如何实现**：

- 启用 [SQL 漏洞评估](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) 来扫描数据库的安全问题，并使其定期对数据库自动运行。

**最佳做法**：

- 对数据库运行首次 VA，在补救不符合安全最佳做法的失败检查后反复运行 VA。 设置可接受配置的基线，直到扫描结果全部正常，或所有检查均已通过。__  

- 将定期的重复扫描配置为每周运行一次，并配置相关人员来接收摘要电子邮件。

- 完成每周扫描后查看 VA 摘要。 对于发现的任何漏洞，评估与前一次扫描结果的偏差，并确定是否应解决本次检查发现的问题。 查看配置发生更改是否有合理的原因。

- 解决检查发现的问题并更新相关的基线。 为解决措施创建票证项，并在解决问题之前跟踪这些项。

**其他资源**：

- [SQL 漏洞评估](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)
- [SQL 漏洞评估服务有助于识别数据库漏洞](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>识别并标记敏感数据

发现可能包含敏感数据的列。 什么数据是敏感数据在很大程度上取决于客户、合规性规定等，并且需要由负责该数据的用户进行评估。 将列分类以使用基于敏感性的高级审核和保护方案。

**如何实现**：

- 使用 [SQL 数据发现和分类](data-discovery-and-classification-overview.md)来发现、分类、标记和保护数据库中的敏感数据。
  - 在 SQL 数据发现和分类仪表板中查看自动发现创建的分类建议。 接受相关的分类，以使用分类标签来持久标记敏感数据。
  - 对于未被自动机制发现的任何其他敏感数据字段，请手动添加分类。
- 有关详细信息，请参与 [SQL 数据发现和分类](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)。

**最佳做法**：

- 定期监视分类仪表板，准确评估数据库的分类状态。 可以导出或打印有关数据库分类状态的报告，以使在合规与审核措施中共享。

- 持续监视 SQL 漏洞评估中建议的敏感数据的状态。 跟踪敏感数据发现规则，识别建议列中的任何分类偏差。  

- 使用根据你的组织的特定需求定制的方式使用分类。 自定义 Azure 安全中心中的[SQL 信息保护](../../security-center/security-center-info-protection-policy.md)策略中的信息保护策略（灵敏度标签、信息类型、发现逻辑）。

### <a name="track-access-to-sensitive-data"></a>跟踪对敏感数据的访问

在审核日志中监视谁访问了敏感数据，并捕获对敏感数据运行的查询。

**如何实现**：

- 结合使用 SQL 审核和数据分类。
  - 在 [SQL 数据库审核](../../azure-sql/database/auditing-overview.md)日志中，可以专门跟踪对敏感数据的访问。 还可以查看访问的数据及其敏感性标签等信息。 有关详细信息，请参阅[数据发现和分类](data-discovery-and-classification-overview.md)和[审核对敏感数据的访问](data-discovery-and-classification-overview.md#audit-sensitive-data)。

**最佳做法**：

- 参阅有关审核和数据分类的最佳做法部分：
  - [审核关键安全事件](#audit-critical-security-events)
  - [识别并标记敏感数据](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>可视化安全性与合规性状态

使用统一的基础结构安全管理系统，该系统可增强你的数据中心（包括 SQL 数据库中的数据库）的安全状况。 查看有关数据库安全性与合规性状态的建议列表。

**如何实现**：

- 在 [Azure 安全中心](https://azure.microsoft.com/documentation/services/security-center/)监视 SQL 相关的安全建议与正在进行的威胁。

## <a name="common-security-threats-and-potential-mitigations"></a>常见安全威胁和潜在缓解措施

本部分帮助你找到用于防范特定攻击途径的安全措施。 遵循上述一条或多条安全指导原则预期可以实现大部分缓解措施。

### <a name="security-threat-data-exfiltration"></a>安全威胁：数据透露

Data 透露是指在未经授权的情况下，从计算机或服务器复制、传输或检索数据。 查看维基百科中的[数据透露](https://en.wikipedia.org/wiki/Data_exfiltration)定义。

通过公共终结点连接到服务器会带来数据渗透，因为它要求客户将其防火墙打开到公共 Ip。  

**方案 1**： azure VM 上的应用程序连接到 Azure SQL 数据库中的数据库。 恶意行动者获取 VM 的访问权限并入侵到其中。 在此场景中，数据透露表示使用恶意 VM 的外部实体连接到数据库，复制个人数据，并将这些数据存储在 Blob 存储中或者不同订阅内的不同 SQL 数据库中。

**场景 2**：恶意 DBA。 这种场景通常出现在受管制行业的安全敏感型客户那里。 在此场景中，高特权用户可将 Azure SQL 数据库中的数据复制到不受数据所有者控制的其他订阅。

**潜在缓解措施**：

目前，Azure SQL 数据库和 SQL 托管实例提供以下方法来减轻数据渗透的威胁：

- 在 Azure VM 的 NSG 中结合使用“允许”和“拒绝”规则，以控制可从 VM 访问哪些区域。
- 如果在 SQL 数据库中使用服务器，请设置以下选项：
  - 将“允许 Azure 服务”设置为“关闭”。
  - 设置 VNet 防火墙规则，仅允许来自包含你的 Azure VM 的子网的流量。
  - 使用[专用链接](../../private-link/private-endpoint-overview.md)
- 对于 SQL 托管实例，默认情况下，使用专用 IP 访问处理恶意 VM 的第一个数据渗透问题。 启用子网上的子网委托功能，自动设置 SQL 托管实例子网上限制最严格的策略。
- 恶意 DBA 关注性更多的是 SQL 托管实例，因为它具有更大的外围应用和网络要求对客户可见。 此问题的最佳缓解措施是首先应用本安全指南中的所有做法，以防止出现恶意 DBA 的情景（不仅可以解决数据透露）。 Always Encrypted 是保护敏感数据的一种方法，它可以加密敏感数据，并使 DBA 无法访问密钥。

## <a name="security-aspects-of-business-continuity-and-availability"></a>业务连续性和可用性的安全性方面

大多数安全标准在操作连续性方面解决数据可用性问题，实现此效果的方式是实施冗余和故障转移功能来避免单一故障点。 对于灾难恢复方案，常见的做法是保留数据和日志文件的备份。 以下部分概述了 Azure 中内置的功能。 此外，提供了可根据具体需求进行配置的其他选项：

- Azure 提供内置的高可用性：[通过 Sql 数据库和 sql 托管实例实现高可用性](high-availability-sla.md)

- 业务关键层包括故障转移组、多可用性区域、完整和差异日志备份，以及默认情况下启用的时间点还原备份：  
  - [高可用性-区域冗余配置](high-availability-sla.md#zone-redundant-configuration)
  - [自动备份](automated-backups-overview.md)
  - [使用自动数据库备份恢复数据库-时间点还原](recovery-using-backups.md#point-in-time-restore)

- 其他业务连续性功能（如跨不同 Azure 地域的自动故障转移组）可按如下所述进行配置：[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure SQL 数据库安全功能概述](security-overview.md)
