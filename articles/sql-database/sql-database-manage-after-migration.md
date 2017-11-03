---
title: "迁移后管理 - Azure SQL 数据库 | Microsoft Docs"
description: "了解如何在迁移到 Azure SQL 数据库后管理数据库。"
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: SQL
ms.prod_service: sql-database
ms.component: management
ms.openlocfilehash: 663ab4aaf229f8a88d1116b34ccb74450aa66c9d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>应如何在迁移后管理我的 Azure SQL 数据库？

有关管理 Azure SQL 数据库投资的常见问题

最近你将 SQL Server 数据库移到了 Azure SQL 数据库，或者可能计划即将进行迁移。 那么在完成迁移后，接下来要做什么？ 考虑到 SQL 数据库为“平台即服务”，Microsoft 将代表你处理几个方面的事务。 但是，这将如何具体改变你公司在安全性、业务连续性、数据库维护、性能优化、监视等关键领域的做法呢？ 

本文旨在简单地组织资源和指南，你在转向管理 SQL 数据库投资时将需要这些资源和指南。 本文主要涉及业务连续性、安全性、数据库维护和监视、性能和数据移动等主要领域。 我们将介绍 SQL Server 和 SQL 数据库之间不同的关键区域，并调用可操作的最佳做法，帮助你实现最大好处并将风险降至最低。 

## <a name="manage-business-continuity-after-migration"></a>在迁移后管理业务连续性

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>如何在 SQL 数据库中创建和管理备份？ 
SQL 数据库自动为你备份数据库，并使你能够在保留期内还原到任何时间点。 标准和高级数据库的保留期为 35 天，基本数据库为 7 天。 此外，长期保留功能允许你将备份文件保留较长的一段时间（最长达 10 年），并可以在任何时候从这些备份还原。 此外，数据库备份为异地复制，以确保在发生灾难或地区灾难时能够在任何区域进行异地还原。 请参阅[业务连续性概述](sql-database-business-continuity.md)。

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>如果发生数据中心级灾难或地区灾难，我如何确保业务连续性？ 

数据库备份为异地复制，以确保在发生灾难或地区灾难时能够在任何区域进行异地还原。 请参阅[业务连续性概述](sql-database-business-continuity.md)。 此外，SQL 数据库还提供了在另一个区域维护主动异地复制的辅助数据库的功能。 在自动故障转移组中配置它们将确保数据库在发生灾难的情况下自动故障转移到辅助数据库。 如果没有配置自动故障转移组，那么你的应用程序需要主动监视灾难，并启动向辅助数据库的故障转移。 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server 提供了可读辅助副本，我是否可以访问 SQL 数据库中的辅助副本？ 

可以，“主动异地复制”功能可用于创建可读辅助副本。 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>我的灾难恢复计划如何从本地转变为 SQL 数据库？ 
SQL Server 实现要求你使用诸如故障转移群集、数据库镜像、复制、日志传送或仅仅是寻常的 BACPAC 备份之类的功能来主动管理备份。 但是，在 SQL 数据库中，备份完全由 Microsoft 管理，你只需配置备份和灾难恢复计划，并且只需要在 Azure 门户上点击几下即可（或者在 PowerShell 上执行几个命令）。 ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>发生灾难时，我如何恢复数据库？ 
SQL 数据库自动允许你将数据库还原到过去 35 天里的任何时间点。 如果你丢失数据或面临应用程序相关灾难，那么可以选择这么做。 

如果面临地区灾难，在配置异地复制辅助数据库的情况下，你可以从另一个区域的异地辅助数据库进行恢复。 对于实时访问应用程序，你可以手动故障转移到另一个区域的异地辅助数据库。 另外，如果已经配置自动故障转移组，则在发生灾难的情况下，会自动向异地辅助数据库执行此故障转移。 如果你没有配置异地复制辅助数据库，那么仍然可以从自动复制备份文件（内置功能，不需要配置）恢复数据库，恢复时间相对较长（12 小时 RTO），最多会丢失一个小时的数据。 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>向辅助数据库的故障转移是透明的吗？ 我的应用程序如何处理数据库故障转移？ 
如果已经配置自动故障转移组，那么向辅助数据库的故障转移是透明的。 但是，如果尚未配置，则应用程序需要结合逻辑来监视主数据库的可用性，然后手动故障转移到辅助数据库。 
 
## <a name="manage-security-after-migration"></a>在迁移后管理安全性

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>如何限制对我的 SQL 数据库的访问？ 
 
有几种方法可以锁定对你 SQL 数据库的连接访问。 
1. 通过 Internet Express Route 限制流量为你提供到 Azure 网络的专用纤程，这样数据就不会通过 internet 传输。 还可以使用 Express Route 来配置跨区域连接。 以下链接详细说明了 Express Route： 
 - [Express Route 简介](../expressroute/expressroute-introduction.md)
 - [先决条件](../expressroute/expressroute-prerequisites.md) 
 - [工作流](../expressroute/expressroute-workflows.md) 
 
2. 选择哪些资源连接到 SQL 数据库： 

   默认情况下，SQL 数据库配置为“允许所有 Azure 服务” – 这表示 Azure 中的所有 VM 都可能会尝试连接到数据库。  仍需要对所有登录进行身份验证。 如果你不希望向所有 Azure IP 提供对数据库的访问权限，可以禁用“允许所有 Azure 服务”，并使用 [VNET 服务终结点](sql-database-vnet-service-endpoint-rule-overview.md)来限制仅给定 Azure VNET 子网中的 Azure 资源可以对数据库进行入站访问。 

   ![VNET 服务终结点](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   另一种方法是为 VM 设置[保留 IP](../virtual-network/virtual-networks-reserved-public-ip.md)，并将服务器防火墙设置中的那些特定 VM IP 地址列入白名单。 （请参见下方 Azure 门户中作为示例的屏幕截图。）通过分配保留 IP，就可以避免通过更改 IP 地址来更新防火墙规则的麻烦。 

3. 避免在 Azure 之外暴露端口 1433

   在 Azure 中使用 [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps) 运行 SSMS。 此操作不需要你打开到 1433 端口的出站连接，因为 IP 是静态的，所以数据库可以只对 RemoteApp 开放，它支持多重身份验证 (MFA)，并且是多用户。 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>在 SQL 数据库中提供了哪些身份验证方法？

SQL 数据库和 SQL 数据仓库中提供的主要身份验证方法是 Azure Active Directory 身份验证和 SQL 身份验证。 Azure Active Directory (AD) 是集中式身份和访问管理服务，而 SQL 只是与 Azure AD 集成的多个 Azure 服务中的一个。 集中式托管服务的好处是，用户凭据可以在你用于更简单身份验证的所有 Azure 服务中共享。 此外，还允许 SQL 数据库和 SQL 数据仓库在 Azure AD 域中提供多重身份验证和来宾用户帐户。 

如果你已经有一个本地 Active Directory，则可以将该目录与 Azure Active Directory 联合在一起，以将目录扩展到 Azure。 


|||
|---|---|
| 如果你…|Azure SQL 数据库 / Azure SQL 数据仓库|
| 不想在 Azure 中使用 Azure Active Directory (AD)|使用 [SQL 身份验证](sql-database-security-overview.md)|
| 在本地 SQL Server 上使用 AD|[将 AD 与 Azure AD 联合](../active-directory/connect/active-directory-aadconnect.md)，并使用 Azure AD 身份验证。 借此，你可以使用单一登录。|
| 需要实施多重身份验证 (MFA)|需要 MFA 作为 [Microsoft 条件性访问](sql-database-conditional-access.md)的策略 ，并使用[支持 MFA 的 Azure AD 通用身份验证](sql-database-ssms-mfa-authentication.md)。|
| 有来自 Microsoft 帐户（live.com、outlook.com）或其他域 (gmail.com) 的来宾帐户|在利用 [Azure AD B2B 协作](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)的 SQL 数据库/数据仓库中使用 [Azure AD 通用身份验证](sql-database-ssms-mfa-authentication.md)。|
| 使用来自联合域的 Azure AD 凭据登录到 Windows|使用 [Azure AD 集成身份验证](sql-database-aad-authentication-configure.md)。|
| 使用来自未与 Azure 联合的域的凭据登录到 Windows|使用 [Azure AD 密码身份验证](sql-database-aad-authentication-configure.md)。|
| 具有需要连接到 Azure SQL 数据库或数据仓库的中间层服务|使用 [Azure AD 令牌身份验证](sql-database-aad-authentication-configure.md)。
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>如何限制从应用程序端对数据库中敏感数据的访问？ 

若要防止未经授权的用户查看敏感数据，SQL 数据库中提供了几个选项： 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 是客户端加密的一种形式，它对数据库中的敏感列进行加密（因此，它们位于数据库管理员和未授权用户的已加密文本中）。 Always Encrypted 的密钥都存储在客户端，因此只有授权的客户端可以解密敏感列。 Always Encrypted 目前支持等式比较，因此数据库管理员可以继续查询加密列，这是其 SQL 命令一部分。 Always Encrypted 可以与各种密钥存储选项结合使用，如 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)、Windows 证书存储和本地硬件安全模块。
- [动态数据掩码](sql-database-dynamic-data-masking-get-started.md)是一种数据屏蔽功能，它通过屏蔽应用层上的非特权用户来限制敏感数据的公开。 你可以定义屏蔽规则来创建屏蔽模式（例如，只显示国家 ID 号的最后 4 位数，并将其余数字标记为 X），并确定哪些用户可以被排除在屏蔽规则之外。
- [行级别安全性](/sql/relational-databases/security/row-level-security)允许你根据执行查询的用户（组成员或执行上下文）来控制对数据库表中的行的访问。 访问限制是在数据库层上完成的，而不是在应用层，以此简化你的应用逻辑。 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>我在 SQL 数据库中有哪些加密选项，以及加密保护是为了杜绝哪些人员？
SQL 数据库中有三种主要加密技术： 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)（上述问题中所提到的）：对表中的敏感列进行端到端加密，以防未经授权的客户端和物理磁盘对其进行访问。 服务器和数据管理员无法看到敏感数据，因为加密密钥存储在客户端上。 
- [透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE)：静态加密，它在数据库级别进行加密，并保护数据文件、日志文件和相关备份，以防物理介质盗取。 TDE 默认已在所有新建的数据库上启用。
 
  下图显示了加密技术选择的概述。

   ![加密概述](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>应如何在云中管理加密密钥？ 
有用于 Always Encrypted（客户端加密）和透明数据加密（静态加密）的密钥管理选项。 建议定期轮换使用加密密钥，轮换频率需与内部规章制度和合规性要求相一致。

- Always Encrypted：Always Encrypted 中有[两个密钥层次结构](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) - 一列敏感数据通过 AES 256 列加密密钥 (CEK) 加密，而该密钥反过来又由列主密钥 (CMK) 进行加密。 为 Always Encrypted 提供的客户端驱动程序在 CMK 长度上没有任何限制。

  CEK 的加密值存储在数据库中，而 CMK 存储在受信任的密钥存储库中，如 Windows 证书存储、Azure Key Vault 或硬件安全模块。 
  
  [CEK 和 CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) 都可轮换使用。 CEK 的轮换使用可以为时间密集型，具体取决于包含加密列的表的大小。 因此，应非常仔细地规划 CEK 的轮换使用。 另一方面，CMK 的轮换使用不会影响数据库性能，并可以使用单独的角色来完成。

  下图显示了 Always Encrypted 中的列主密钥的密钥存储选项 

   ![Always Encrypted CMK 存储提供程序](./media/sql-database-manage-after-migration/always-encrypted.png)

- 透明数据加密 (TDE)：TDE 中有一个双密钥层次结构 — 每个用户数据库中的数据都通过对称 AES-256 数据库唯一的数据库加密密钥 (DEK) 进行加密，该密钥反过来又由服务器唯一的不对称 RSA 2048 主密钥进行加密。 

  为方便起见，透明数据加密的主密钥默认由 SQL 数据库服务托管。 如果组织想要控制主密钥，则可以使用 [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) 作为密钥存储。 

  通过使用 Azure Key Vault，组织假定控制密钥预配、轮换使用和权限控制。 [轮换或切换 TDE 主密钥类型](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)非常快，因为它仅重新加密 DEK。 

  对于安全性与数据管理角色分开的组织来说，安全管理员可以为 Azure Key Vault 中的 TDE 主密钥预配密钥材料，并为数据库管理员提供 Azure Key Vault 密钥标识符，用于在服务器上进行静态加密。 

## <a name="monitoring-and-compliance-after-migration"></a>迁移后的监视和合规性

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>如何监视 SQL 数据库中的数据库活动？
SQL 数据库中内置了一些监视功能，用于跟踪数据库中的安全性和其他事件：
- [SQL 审核](sql-database-auditing.md)允许你收集自己的 Azure 存储帐户中数据库事件的审核日志。
- [SQL 威胁检测](sql-database-threat-detection.md)允许你检测表明可能恶意访问、破坏或利用数据库中数据的可疑活动。 SQL 数据库威胁检测运行多组算法，这些算法可以检测潜在漏洞和 SQL 注入攻击，以及异常的数据库访问模式（如来自异常位置或不熟悉主体的访问）。 如果在数据库中检测到威胁，安全管理人员或其他指定管理员将收到电子邮件通知。 每个通知都会提供可疑活动的详细信息，以及如何进一步调查和缓解威胁的建议。 
- [SQL 漏洞评估](sql-vulnerability-assessment.md)是一项数据库扫描和报告服务，允许你大规模监视数据库的安全状态、识别安全风险以及偏离你定义的安全基线的行为。 每次扫描之后，都会提供可行步骤和修正脚本的自定义列表，还会提供可用于帮助满足合规性的评估报告。 
- [SQL OMS 安全同步应用程序](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)利用 Operations Management Suite (OMS) 公共 API 将 SQL 审计日志推送到 OMS 以进行日志分析并获取定义自定义检测警报的能力，包括： 
 - SQL 数据库审计磁贴和仪表板，提供关于数据库活动的清晰一致的报告。 
 - SQL Log Analytics 可以对数据库活动进行分析，并调查可能表明存在可疑安全违规的差异和异常。
 - 有关触发电子邮件、Webhook 和 Azure 自动 runbook 警报的实测事件的高级警报特定规则（例如，密码更改、非工作时间、特定 SQL 命令）。
- [Azure 安全中心](../security-center/security-center-intro.md)为 Azure、本地和其他云中运行的工作负载提供集中式安全管理。 你可以查看是否已在所有资源上配置诸如审计和透明数据加密之类的基本 SQL 数据库保护，并根据你自己的需求创建策略。 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL 数据库是否符合任何规章要求，这对我组织的合规性有什么帮助？ 
Azure SQL 数据库符合一系列规章遵从性。 要查看符合的最新一组合规性，请访问 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/compliance/complianceofferings)，并向下钻取对你组织而言至关重要的合规性，以了解 Azure SQL 数据库是否包含在合规的 Azure 服务中。 需要注意的是，尽管 Azure SQL 数据库可能被认证为合规服务，它有助于确保组织服务的合规性，但不会自动保证这一点。 

## <a name="database-maintenance-and-monitoring-after-migration"></a>迁移后的数据库维护和监视

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>如何监视数据大小和资源利用率的增长？

- 可以在 Azure 门户的“监视”图表中查看关于数据库大小和资源利用率的监视指标。 

  ![监视图表](./media/sql-database-manage-after-migration/monitoring-chart.png)

- 若要获取更加深入的见解并向下钻取查询的详细信息，可以在 Azure 门户中使用所提供的“查询性能见解”。 这将要求在数据库中激活“查询存储”。

  ![查询性能见解](./media/sql-database-manage-after-migration/query-performance-insight.png)

- 或者，你也可以使用动态管理视图 (DMV) 来查看这些指标 - 使用 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) and [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)。 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>需要多久运行一次诸如 DBCC_CHECKDB 之类的一致性检查？
DBCC_CHECKDB 检查数据库中所有对象的逻辑和物理完整性。 你不再需要执行这些检查，因为这些检查是由 Microsoft 在 Azure 上进行管理的。 有关详细信息，请参阅 [Azure SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>在迁移后监视性能和资源利用率

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>如何在 Azure SQL 数据库中监视性能和资源利用率？
可以使用以下方法监视 Azure SQL 数据库中的性能和资源利用率：

- Azure 门户：Azure 门户通过选择数据库并单击“概述”窗格中的图表来显示单个数据库的利用率。 可以修改图表以显示多个指标，包括 CPU 百分比、DTU 百分比、数据 IO 百分比、会话百分比和数据库大小百分比。 
  ![资源利用率](./media/sql-database-manage-after-migration/resource-utilization.png)

  在此图表中，你还可以按资源配置警报。 这些警报允许你使用电子邮件响应资源条件，写入 HTTPS/HTTP 端点或执行操作。 有关详细说明，请参阅[监视 Azure SQL 数据库中的数据库性能](sql-database-single-database-monitor.md)。

- 视图：可以查询 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 动态管理视图，以返回最近一个小时的资源使用统计信息历史记录，也可以查询 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 系统目录视图，返回过去 14 天的历史记录。 

- 查询性能见解：可以使用[查询性能见解](sql-database-query-performance.md)查看特定数据库那些排名靠前的资源消耗查询和长时间运行查询的历史记录。 此功能需要为数据库启用和激活[查询存储](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。

- Log Analytics 中的 Azure SQL Analytics（预览版）：[Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) 允许你收集和可视化关键的 Azure SQL Azure 性能指标，对于每个工作区，最多支持 150,000 个 Azure SQL 数据库和 5,000 个 SQL 弹性池。 你可以使用它监视并接收通知。 可以跨多个 Azure 订阅和弹性池监视 Azure SQL 数据库和弹性池指标，并可用于识别应用程序堆栈每一层上的问题。 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>如何确保我使用的是适当的服务层和性能级别？
监视 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 动态管理视图来了解 CPU、I/O 和内存消耗情况。 还可以使用 SQL 数据库[查询性能见解](sql-database-query-performance.md)来了解资源消耗情况。 如果你一直运行较高百分比的可用资源，则应考虑移动到现有服务层内的更高性能级别，或者移动到更高服务层。 相反，如果一直使用较低百分比的可用资源，你可以考虑移动到较低性能级别或服务层。

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>我发现性能问题。 我的 Azure SQL 数据库故障排除方法与 SQL Server 有何不同？
你的性能故障排除方法的许多方面在 Azure SQL 数据库中将仍然保持不变，但存在一些差异。 例如，如果发现总体性能下降，可以监视 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 动态管理视图来了解 CPU、I/O 和内存消耗情况。 可能需要根据工作负载需求来更改性能级别和/或服务层。
有关优化性能问题的全套建议，请参阅[优化 Azure SQL 数据库中的性能](sql-database-performance-guidance.md)。 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>是否需要维护索引和统计信息？
Azure SQL 数据库不会将索引和统计信息自动作为服务的一部分进行维护。 你负责安排索引和统计信息的维护。 以下文章“Azure 自动化方法”详细介绍了为 Azure SQL 数据库安排维护作业的几个选项。

## <a name="data-movement-after-migration"></a>迁移后的数据移动

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>如何从 Azure SQL 数据库将数据作为 BACPAC 文件导出和导入？ 

- 导出：可以将你的 Azure SQL 数据库作为 BACPAC 文件从 Azure 门户导出。

  ![导出为 BACPAC](./media/sql-database-export/database-export.png)

- 导入：可以通过 Azure 门户以 BACPAC 文件形式导入数据库。

  ![导入 BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>如何同步 Azure SQL 数据库 SQL Server 2016/2012 之间的数据？
[数据同步](sql-database-sync-data.md)功能可帮助你将多个本地 SQL Server 数据库和 Azure SQL 数据库之间的数据进行双向同步。 但是，由于此操作基于触发器，所以最终一致性会得到保证（无数据丢失），但无法保证事务一致性。 

## <a name="next-steps"></a>后续步骤
了解 [Azure SQL 数据库](sql-database-technical-overview.md)。
