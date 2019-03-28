---
title: Azure SQL 数据库安全概述 | Microsoft 文档
description: 了解 Azure SQL 数据库和 SQL Server 的安全性，包括云与本地 SQL Server 之间的差异。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a4f1b26a20da3b22561538f7814105b356c4148a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519130"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL 数据库安全功能概述

本文概述了使用 Azure SQL 数据库保护应用程序数据层的基础知识。 所述的安全策略遵循如下图所示的分层深度防御方法，并从外向内移动：

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>网络安全

Microsoft Azure SQL 数据库为云和企业应用程序提供关系数据库服务。 为了帮助保护客户数据，防火墙会阻止对数据库服务器的网络访问，直到根据 IP 地址或 Azure 虚拟网络流量源显式授予访问权限。

### <a name="ip-firewall-rules"></a>IP 防火墙规则

IP 防火墙规则基于每个请求的起始 IP 地址授予对数据库的访问权限。 有关详细信息，请参阅 [Azure SQL 数据库和 SQL 数据仓库防火墙规则概述](sql-database-firewall-configure.md)。

### <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则

[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)将虚拟网络连接扩展到 Azure 主干网，并使 Azure SQL 数据库能够识别作为流量来源的虚拟网络子网。 若要允许流量到达 Azure SQL 数据库，请使用 SQL [服务标记](../virtual-network/security-overview.md)，以允许出站流量通过网络安全组。

[虚拟网络规则](sql-database-vnet-service-endpoint-rule-overview.md)使 Azure SQL 数据库仅接受从虚拟网络中的所选子网发送的通信。

> [!NOTE]
> 使用防火墙规则控制访问权限不适用于托管实例。 有关所需网络配置的详细信息，请参阅[连接到托管实例](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>访问管理

> [!IMPORTANT]
> 管理 Azure 中的数据库和数据库服务器由门户用户帐户的角色分配控制。 有关本文的详细信息，请参阅 [Azure 门户中基于角色的访问控制](../role-based-access-control/overview.md)。

### <a name="authentication"></a>身份验证

身份验证是证明用户所声明身份的过程。 Azure SQL 数据库支持两种类型的身份验证：

- **SQL 身份验证**：

    SQL 数据库身份验证是指使用用户名和密码连接到 [Azure SQL 数据库](sql-database-technical-overview.md)时对用户进行的身份验证。 在为数据库创建数据库服务器期间，必须指定具有用户名和密码的“服务器管理员”登录。 借助这些凭据，“服务器管理员”可以使用数据库所有者的身份通过数据库服务器上任何数据库的身份验证。 之后，服务器管理员可以创建额外的 SQL 登录和用户，以允许用户使用用户名和密码进行连接。

- **Azure Active Directory 身份验证**：

    Azure Active Directory 身份验证是使用 Azure Active Directory (Azure AD) 中的标识连接到 Azure [SQL 数据库](sql-database-technical-overview.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的一种机制。 使用 Azure AD 身份验证，管理员可在一个中心位置集中管理数据库用户以及其他 Microsoft 服务的标识和权限。 这包括最小化密码存储并启用集中式密码轮换策略。

     必须创建一个名为“Active Directory 管理员”的服务器管理员，以便在 SQL 数据库中使用 Azure AD 身份验证。 有关详细信息，请参阅[使用 Azure Active Directory 身份验证连接到 SQL 数据库](sql-database-aad-authentication.md)。 Azure AD 身份验证同时支持托管帐户和联合帐户。 联合帐户支持与 Azure AD 联合的客户域的 Windows 用户和组。

    其他可用的 Azure AD 身份验证选项包括[适用于 SQL Server Management Studio 的 Active Directory 通用身份验证](sql-database-ssms-mfa-authentication.md)连接，其中包括[多重身份验证](../active-directory/authentication/concept-mfa-howitworks.md)和[条件访问](sql-database-conditional-access.md)。

> [!IMPORTANT]
> 管理 Azure 中的数据库和服务器由门户用户帐户的角色分配控制。 有关本文的详细信息，请参阅 [Azure 门户中基于角色的访问控制](../role-based-access-control/overview.md)。 使用防火墙规则控制访问权限不适用于托管实例。 有关所需网络配置的详细信息，请参阅以下有关[连接到托管实例](sql-database-managed-instance-connect-app.md)的文章。

授权是指在 Azure SQL 数据库中分配给用户的权限，并决定允许用户执行的操作。 权限控制通过将用户帐户添加到定义数据库级权限的[数据库角色](/sql/relational-databases/security/authentication-access/database-level-roles)或授予用户特定的[对象级权限](/sql/relational-databases/security/permissions-database-engine)来实现。 有关详细信息，请参阅[登录和用户](sql-database-manage-logins.md)

最佳做法是，将用户添加到具有完成其作业功能所需的最低权限的角色中。 服务器管理帐户是 db_owner 角色的成员，该角色具有广泛权限，应谨慎授予用户。 通过 Azure SQL 数据库使用应用程序时，使用具有有限权限的[应用程序角色](/sql/relational-databases/security/authentication-access/application-roles)。 这可确保连接到数据库的应用程序具有应用程序所需的最低权限。

### <a name="row-level-security"></a>行级别安全性

行级别安全性使客户能够根据执行查询的用户特征（例如，按组成员身份或执行上下文），控制对数据库表中的行的访问。 有关详细信息，请参阅[行级别安全性](/sql/relational-databases/security/row-level-security)。

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  此身份验证方法使用用户名和密码。 

有关 Azure SQL 数据库中的权限概述，请参阅[登录和用户](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>威胁防护

SQL 数据库通过提供审核和威胁检测功能来保护客户数据。

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL 审核在 Azure Monitor 日志和事件中心

SQL 数据库审核可跟踪数据库活动，通过将数据库事件记录到客户所有的 Azure 存储帐户中的审核日志，帮助用户保持符合安全标准。 用户可以通过审核监视正在进行的数据库活动，以及分析和调查历史活动，以标识潜在威胁或可疑的滥用行为和安全违规。 有关详细信息，请参阅 [SQL 数据库审核入门](sql-database-auditing.md)。  

### <a name="threat-detection"></a>威胁检测

通过分析有关异常行为和潜在恶意访问或利用数据库的审核日志，威胁检测可强化审核。 警报是针对可疑活动或异常访问模式（如 SQL 注入攻击、潜在数据渗透和暴力破解密码攻击）创建的。 可从 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)查看威胁检测警报，其中提供了可疑活动的详细信息，并给出了进一步调查建议以及缓解威胁的措施。 威胁检测费用为每服务器每月 15 美元。 前 60 天免费。 有关详细信息，请参阅 [SQL 数据库威胁检测入门](sql-database-threat-detection.md)。

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>信息保护和加密

### <a name="transport-layer-security-tls-encryption-in-transit"></a>传输层安全性 TLS（传输中加密）

SQL 数据库通过使用[传输层安全](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)加密动态数据来保护客户数据。

Sql Server 时强制执行加密 (SSL/TLS) 在所有时间的所有连接。 这可确保所有数据进行都加密"传输"中，客户端和服务器，而不管的设置之间**Encrypt**或**TrustServerCertificate**连接字符串中。

作为最佳做法，建议在应用程序的连接字符串指定的加密的连接并_**不**_ 信任服务器证书。 这会强制应用程序来验证服务器证书，并因此防止你的应用程序从易受中间人中间类型攻击。

例如，使用 ADO.NET 驱动程序时实现这一点通过**Encrypt = True**并**TrustServerCertificate = False**。如果从 Azure 门户获取连接字符串，它将具有正确的设置。

> [!IMPORTANT]
> 请注意，某些非 Microsoft 驱动程序可能不会默认使用 TLS，或依赖于较旧版本的 TLS (< 2.0) 才能正常工作。 在这种情况下 SQL Server 将仍允许用户连接到你的数据库。 但是，我们建议你评估允许此类驱动程序和应用程序连接到 SQL 数据库，尤其是存储敏感数据的安全风险。 
>
> 有关 TLS 和连接详细信息，请参阅[TLS 注意事项](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>透明数据加密（静态加密）

[Azure SQL 数据库的透明数据加密 (TDE)](transparent-data-encryption-azure-sql.md) 进一步加强了安全性，帮助保护静态数据不受未经授权或脱机访问原始文件或备份的影响。 常见方案包括数据中心被盗或对硬件或媒体（如磁盘驱动器和备份磁带）的不安全处置。 TDE 使用 AES 加密算法加密整个数据库，无需应用程序开发人员对现有应用程序进行任何更改。

在 Azure 中，所有新创建的 SQL 数据库都默认处于加密状态，且数据库加密密钥通过一个内置的服务器证书保护。  证书维护和轮换由服务管理，无需用户输入。 喜欢控制加密密钥的客户可以管理 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) 中的密钥。

### <a name="key-management-with-azure-key-vault"></a>使用 Azure Key Vault 的密钥管理

[创建自己的密钥](transparent-data-encryption-byok-azure-sql.md) (BYOK) 支持 [透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE)，允许客户使用  [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)（Azure 基于云的外部密钥管理系统）来获得密钥管理和轮换的所有权。 如果撤销了数据库对密钥保管库的访问权限，则无法解密数据库和将其读入内存。 Azure Key Vault 提供集中密钥管理平台，利用严格监控的硬件安全模块 (HSM)，并可在密钥与数据管理之间实现职责分离，以帮助满足安全合规性要求。

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted（使用中加密）

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 功能旨在保护特定数据库列中存储的敏感数据不被访问（如信用卡号或、国民身份证号或视需要而定的数据）。 这包括数据库管理员或其他特权用户，他们被授权访问数据库以执行管理任务，但不需要访问加密列中的特定数据。 数据始终处于加密状态，这意味着加密数据只在有权访问加密密钥的客户端应用程序需要处理数据时才解密。  加密密钥从不暴露给 SQL，而且可以存储在 [Windows 证书存储区](sql-database-always-encrypted.md)或 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) 中。

### <a name="masking"></a>掩码

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>动态数据屏蔽

SQL 数据库动态数据掩码通过对非特权用户模糊化敏感数据来限制此类数据的泄露。 动态数据掩码可自动发现 Azure SQL 数据库中潜在的敏感数据，提供可行的建议来掩码这些字段，对应用程序层造成的影响可忽略不计。 它的工作原理是在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。 有关详细信息，请参阅 [SQL 数据库动态数据掩码入门](sql-database-dynamic-data-masking-get-started.md)。

#### <a name="static-data-masking"></a>静态数据掩码

[静态数据掩码](/sql/relational-databases/security/static-data-masking)是一款客户端工具，在 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18.0 预览版 5 和更高版本中可用。  用户可通过静态数据掩码创建数据库副本，其中选定列中的数据已被永久屏蔽。 可用的掩码函数包括 NULL 掩码、单值掩码、shuffle 和组 shuffle 掩码，以及字符串复合掩码。 使用数据库的掩码副本，组织可以通过共享掩码副本来分离生产环境和测试环境。 敏感数据得到充分保护，并保留了所有其他数据库特征。 如果需要第三方访问数据库，建议使用掩码数据库。

## <a name="security-management"></a>安全管理

### <a name="vulnerability-assessment"></a>漏洞评估

[漏洞评估](sql-vulnerability-assessment.md)是一项易于配置的服务，可以发现、跟踪和帮助修正潜在的数据库漏洞，旨在主动提高整体数据库安全性。 漏洞评估 (VA) 是高级数据安全 (ADS) 产品/服务（它是高级 SQL 安全功能的一个统一包）的一部分。 可通过中心 SQL ADS 门户访问和管理漏洞评估。

### <a name="data-discovery--classification"></a>数据发现和分类

数据发现和分类（当前为预览版）提供了内置于 Azure SQL 数据库的高级功能，可用于发现、分类、标记和保护数据库中的敏感数据。 发现最敏感的数据（业务/财务、医疗保健、个人数据等）并进行分类，可在组织的信息保护方面发挥关键作用。 它可以作为基础结构，用于：

- 各种安全方案，如监视（审核）并在敏感数据存在异常访问时发出警报。
- 控制对包含高度敏感数据的数据库的访问并增强其安全性。
- 帮助满足数据隐私标准和法规符合性要求。

有关详细信息，请参阅[数据发现和分类入门](sql-database-data-discovery-and-classification.md)。

### <a name="compliance"></a>合规

除了上述有助于应用程序符合各项安全要求的特性和功能以外，Azure SQL 数据库还定期参与审核，并已通过许多法规标准的认证。 有关详细信息，请参阅 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)，可以从中找到 [SQL 数据库法规认证](https://www.microsoft.com/trustcenter/compliance/complianceofferings)的最新列表。

## <a name="next-steps"></a>后续步骤

- 有关使用 SQL 数据库中的访问控制功能的介绍，请参阅[控制访问](sql-database-control-access.md)。
- 有关数据库审核的讨论，请参阅 [SQL 数据库审核](sql-database-auditing.md)。
- 有关威胁检测的介绍，请参阅 [SQL 数据库威胁检测](sql-database-threat-detection.md)。
