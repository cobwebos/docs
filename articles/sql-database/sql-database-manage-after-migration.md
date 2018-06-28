---
title: 迁移后管理 - Azure SQL 数据库 | Microsoft Docs
description: 了解如何在迁移到 Azure SQL 数据库后管理数据库。
services: sql-database
author: joesackmsft
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: josack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: d97e0bf94b911936cacf04f7f26c172b9975c56f
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061638"
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>云中的新 DBA - 在 Azure SQL 数据库中管理数据库

从传统的自主管理、自主控制环境迁移到 PaaS 环境似乎在开始的时候让人有点不知所措。 作为应用开发人员或 DBA，你需要了解该平台的核心功能，有助于应用程序始终保持可用性、高性能、安全性和复原性。 这也是本文的主旨。 本文简要地整理了一下资源，并指导如何充分利用 SQL 数据库的主要功能在云中管理应用程序，使应用程序保持高效运行并获得最佳效果。 本文主要面向： 
- 正在评估向 Azure SQL DB 迁移应用程序的人员 - 应用程序现代化。
- 正在迁移应用程序的人员 - 正在进行的迁移方案。
- 最近完成了向 Azure SQL DB 迁移的人员 - 云中新的 DBA。

本文讨论了 Azure SQL DB 作为随时可用的平台的一些核心特点。 这些特点为： 
- 业务连续性和灾难恢复 (BCDR)
- 安全和符合性
- 智能数据库监视和维护
- 数据移动

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>业务连续性和灾难恢复 (BCDR)
借助业务连续性和灾难恢复功能，发生灾难时仍可像往常一样继续运行业务。 灾难可能是数据库级别的事件（例如，有人错误地删除了关键表格）或数据中心级别的事件（例如，海啸等区域性灾难）。 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>如何在 SQL 数据库中创建和管理备份？
无需在 Azure SQL DB 上创建备份，因为没这个必要。 SQL 数据库会自动备份数据库，因此不再需要操心去计划、执行和管理备份。 该平台每周执行一次完整备份，每隔几小时执行一次差异备份，每 5 分钟执行一次日志备份，确保高效的灾难恢复，将数据丢失降至最低。 创建数据库后，首先会执行完整备份。 在称为“保留期”的某段时间内，这些备份均可用，可用情况因所选性能层而有所不同。  凭借 SQL 数据库，可使用[时点恢复 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) 还原到此保留期内的任意时间点。

|性能层|保留天数|
|---|:---:|
|基本|7|
|标准|35|
|高级|35|
|||

此外，使用[长期保留 (LTR)](sql-database-long-term-retention.md) 功能，还可将备份文件特意保留更长时间（最长达 10 年），且可在该期间任意时间点通过备份还原数据。 此外，数据库备份保存在异地复制存储上，可确保从区域性灾难中复原。 还可以在保留期内的任意时间点恢复 Azure 区域中的任何备份。 请参阅[业务连续性概述](sql-database-business-continuity.md)。

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>如果发生数据中心级灾难或地区灾难，我如何确保业务连续性？
因为数据库备份存储在异地复制存储子系统中，所以确保了在发生区域性灾难时，可将备份还原到另一 Azure 区域。 这称为异地还原。 异地还原 RPO（恢复点目标）通常小于 1 小时，也小于 ERT（预计恢复时间 - 几分钟到几小时）。

对于任务关键型数据库，Azure SQL DB 提供了活动的异地复制。 它实际上是在另一区域创建原始数据库的异地复制辅助副本。 例如，如果数据库最初托管在 Azure 美国西部区域，而你想使用区域性灾难复原能力。 你就要创建美国西部数据库的活动异地副本，比如说美国东部。 如果美国西部发生灾难，你就可以故障转移到美国东部区域。 最好将它们配置到自动故障转移组中，因为这样可以确保在发生灾难时数据库自动故障转移到美国东部的辅助数据库。 此过程的 RPO 不到 5 秒，ERT 不到 30 秒。

如果没有配置自动故障转移组，那么你的应用程序需要主动监视灾难，并启动向辅助数据库的故障转移。 你可以在不同 Azure 区域创建最多 4 个此类活动异地副本。 这样更好。 你还可以对这些辅助活动异地副本进行只读访问。 这样可轻松降低异地分布应用程序方案的延迟。 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>我的灾难恢复计划如何从本地更改为 SQL 数据库？
总的来说，传统的本地 SQL Server 安装程序要求使用各种功能（如故障转移群集、数据库镜像、事务复制、日志传送等）主动管理可用性，并维护和管理备份以确保业务连续性。 而使用 SQL 数据库时，该平台可为你管理这些项目，因此你可以专注于开发和优化数据库应用程序，不用担心灾难管理等事项。 你只需配置备份和灾难恢复计划，并且在 Azure 门户上点击几下即可（或者使用 PowerShell API 执行几个命令）。 

要了解有关灾难恢复的详细信息，请参阅：[Azure SQL DB 灾难恢复 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>安全和符合性
SQL 数据库十分重视安全性和隐私性。 SQL 数据库中的安全性分为数据库级别和平台级别，将安全性分为几层后，就很好理解了。 在每一层，都需要为应用程序控制和提供最佳安全性。 这些层分为：
- 标识和身份验证（[Windows/SQL 身份验证和 Azure Active Directory [AAD] 身份验证](sql-database-control-access.md)）。
- 监视活动（[审核](sql-database-auditing.md)和[检测威胁](sql-database-threat-detection.md)）。
- 保护实际数据（[透明数据加密 [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 和 [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)）。 
- 控制对敏感和特许数据的访问（[行级安全性](/sql/relational-databases/security/row-level-security)和[动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)）。

[Azure 安全中心](https://azure.microsoft.com/services/security-center/)为 Azure、本地和其他云中运行的工作负载提供集中式安全管理。 你可以查看是否已在所有资源上配置诸如[审计](sql-database-auditing.md)和[透明数据加密 [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)之类的基本 SQL 数据库保护，并根据你自己的需求创建策略。

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>在 SQL 数据库中提供了哪些用户身份验证方法？
SQL 数据库中提供了[两种身份验证方法](sql-database-control-access.md#authentication)： 
- [Azure Active Directory 身份验证](sql-database-aad-authentication.md)
- SQL 身份验证。 

不支持传统的 Windows 身份验证。 Azure Active Directory (AD) 是集中式标识和访问管理服务。 通过此服务可非常方便地为组织中的所有人员提供单一登录访问 (SSO)。 这意味着，所有 Azure 服务共享凭据，身份验证更容易。 AAD 支持 [MFA（多重身份验证）](sql-database-ssms-mfa-authentication.md)，只需[单击几次](../active-directory/connect/active-directory-aadconnect-get-started-express.md)，AAD 就可与 Windows Server Active Directory 集成。 SQL 身份验证的工作方式与之前完全相同。 提供用户名/密码后，即可对给定逻辑服务器上任何数据库的用户进行身份验证。 此外，还允许 SQL 数据库和 SQL 数据仓库在 Azure AD 域中提供多重身份验证和来宾用户帐户。 如果你已经有一个本地 Active Directory，则可以将该目录与 Azure Active Directory 联合在一起，以将目录扩展到 Azure。

|**如果你...**|**SQL 数据库/SQL 数据仓库**|
|---|---|
|不想在 Azure 中使用 Azure Active Directory (AD)|使用 [SQL 身份验证](sql-database-security-overview.md)|
|在本地 SQL Server 上使用 AD|[将 AD 与 Azure AD 联合](../active-directory/connect/active-directory-aadconnect.md)，并使用 Azure AD 身份验证。 借此，你可以使用单一登录。|
|需要实施多重身份验证 (MFA)|需要 MFA 作为 [Microsoft 条件性访问](sql-database-conditional-access.md)的策略 ，并使用[支持 MFA 的 Azure AD 通用身份验证](sql-database-ssms-mfa-authentication.md)。|
|有来自 Microsoft 帐户（live.com、outlook.com）或其他域 (gmail.com) 的来宾帐户|在利用 [Azure AD B2B 协作](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)的 SQL 数据库/数据仓库中使用 [Azure AD 通用身份验证](sql-database-ssms-mfa-authentication.md)。|
|使用来自联合域的 Azure AD 凭据登录到 Windows|使用 [Azure AD 集成身份验证](sql-database-aad-authentication-configure.md)。|
|使用来自未与 Azure 联合的域的凭据登录到 Windows|使用 [Azure AD 集成身份验证](sql-database-aad-authentication-configure.md)。|
|具有需要连接到 SQL 数据库或数据仓库的中间层服务|使用 [Azure AD 集成身份验证](sql-database-aad-authentication-configure.md)。|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>如何限制或控制对数据库的连接访问？
你可以任意使用多种技术来组织应用程序的连接。 
- 防火墙规则
- VNET 服务终结点
- 保留 IP

#### <a name="firewall"></a>防火墙
防火墙仅允许特定实体访问逻辑服务器，以此来防止外部实体访问服务器。 默认拒绝逻辑服务器内的所有连接和数据库，来自其他 Azure 服务的连接除外。 利用防火墙规则，可以仅向实体（例如，开发人员计算机）开放服务器的访问权限。为此，你只需通过防火墙允许该计算机的 IP 地址。 利用它还可以指定你想要允许访问逻辑服务器的 IP 范围。 例如，通过在防火墙设置页中指定一个范围，可以一次性添加组织中开发人员计算机 IP 地址。 

可以创建服务器级或数据库级的防火墙规则。 可通过门户或 SSMS 创建服务器级防火墙规则。 有关如何设置服务器和数据库级防火墙规则的详细信息，请参阅：[在 SQL 数据库中创建防火墙规则](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。

#### <a name="service-endpoints"></a>服务终结点
默认情况下，SQL 数据库配置为“允许所有 Azure 服务” – 这表示 Azure 中的所有虚拟机都可能会尝试连接到数据库。 尝试连接时仍必须已经过身份验证。 但如果不想让任何 Azure IP 访问数据库，则可禁用“允许所有 Azure 服务”。 此外，还可配置[VNet 服务终结点](sql-database-vnet-service-endpoint-rule-overview.md)。

通过服务终结点 (SE) 可以仅向自己在 Azure 中的专用虚拟网络公开关键 Azure 资源。 以此从根本上阻止了对资源的公共访问。 虚拟网络与 Azure 间的流量位于 Azure 主干网络上。 无 SE 时，可获得强制隧道数据包路由。 虚拟网络强制组织的 Internet 流量和 Azure 服务流量通过相同的路由。 借助服务终结点，可优化这进程，因为数据包直接从虚拟网络流向 Azure 主干网络上的服务。

![VNET 服务终结点](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>保留 IP
另一种方法是为 VM 设置[保留 IP](../virtual-network/virtual-networks-reserved-public-ip.md)，并将服务器防火墙设置中的那些特定 VM IP 地址列入允许列表。 通过分配保留 IP，就可以避免通过更改 IP 地址来更新防火墙规则的麻烦。

### <a name="what-port-do-i-connect-to-sql-database-on"></a>连接到哪些端口上的 SQL 数据库？

端口 1433。 SQL 数据库通过此端口通信。 要从公司网络内进行连接，必须在组织防火墙设置中添加出站规则。 原则上请避免在 Azure 边界外公开端口 1433。 可在 Azure 中使用 [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps) 运行 SSMS。 此操作不需要打开到端口 1433 的出站连接，因为 IP 是静态的，所以数据库可以只对 RemoteApp 开放，它支持多重身份验证 (MFA)。

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>如何监视和管理 SQL 数据库中服务器和数据库上的活动？
#### <a name="sql-database-auditing"></a>SQL 数据库审核
使用 SQL 数据库，可打开“审核”来跟踪数据库事件。 [SQL 数据库审核](sql-database-auditing.md)可记录数据库事件，并将事件写入 Azure 存储帐户中的审核日志文件。 若要深入了解潜在的违反安全规定和政策的行为，保持合规性等，审核就尤其有用。它允许定义和配置你认为需要审核的某些事件类别，以此获得预配报表和仪表板，大致了解数据库中发生的事件。 可在数据库级或服务器级应用这些审核策略。 有关如何启用服务器/数据库审核的指南，请参阅：[启用 SQL 数据库审核](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary)。

#### <a name="threat-detection"></a>威胁检测
通过[威胁检测](sql-database-threat-detection.md)，可轻松应对“审核”发现的违反安全规定和政策的行为。 即使不是安全专家，也能解决系统中潜在的威胁或违规问题。 威胁检测还具有一些内置功能，如 SQL 注入检测。 SQL 注入会企图改变或破坏数据，是攻击数据库应用程序的一种相当常见的方法。 SQL 数据库威胁检测运行多组算法，这些算法可以检测潜在漏洞和 SQL 注入攻击，以及异常的数据库访问模式（如来自异常位置或不熟悉主体的访问）。 如果在数据库中检测到威胁，安全管理人员或其他指定管理员将收到电子邮件通知。 每个通知都会提供可疑活动的详细信息，以及如何进一步调查和缓解威胁的建议。 要了解如何启用威胁检测，请参阅[启用 SQL 数据库威胁检测](sql-database-security-tutorial.md#enable-sql-database-threat-detection)。 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>一般情况下如何保护 SQL 数据库中的数据？
加密为保护敏感数据免受入侵提供了一种强大的机制。 没有解密密钥，入侵者就无法使用加密数据。 它在 SQL 数据库中构建的现有安全层之上增加了一层额外保护。 保护 SQL 数据库中的数据包含两个方面： 
- 数据和日志文件中的静态数据 
- 传送中的数据。 

在 SQL 数据库中，存储子系统上数据和日志文件中的静态数据在默认情况下始终全部通过[透明数据加密 [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 进行加密。 备份也进行了加密。 使用 TDE 时，访问这些数据的应用程序不需要进行任何更改。 加密和解密均以透明方式进行；因而得名“透明数据加密”。 为了保护传输中的和静态的敏感数据，SQL 数据库提供了一个名为 [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 的功能。 AE 是客户端加密的一种形式，它对数据库中的敏感列进行加密（因此，它们位于数据库管理员和未授权用户的已加密文本中）。 服务器会首先收到加密数据。 Always Encrypted 的密钥也存储在客户端，因此只有授权的客户端可以解密敏感列。 服务器和数据管理员无法看到敏感数据，因为加密密钥存储在客户端上。 AE 对表中的敏感列进行端到端加密，以防未经授权的客户端和物理磁盘对其进行访问。 AE 目前支持等式比较，因此数据库管理员可以继续查询加密列，这是其 SQL 命令的一部分。 Always Encrypted 可以与各种密钥存储选项结合使用，如 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)、Windows 证书存储和本地硬件安全模块。

|**特征**|**Always Encrypted**|**透明数据加密**|
|---|---|---|
|**加密范围**|端到端|静态数据|
|**数据库服务器可访问敏感数据**|否|是，因为加密针对的是静态数据|
|**允许的 T-SQL 的操作**|相等性比较|所有 T-SQL 的外围应用都可用|
|**使用该功能所需的应用更改**|轻微|很少|
|**加密粒度**|列级|数据库级|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>如何限制对数据库中敏感数据的访问？
每个应用程序在数据库中都有一定的敏感数据，需要防止这些数据被所有人查看。 组织内的某些人员需要查看此数据，但其他人员应无法查看此数据。 比如，员工工资。 经理需要查看其直接下属的工资信息，但团队成员应无法查看其同事的工资信息。 另一种情况是，数据开发人员在开发阶段或测试期间可能需要使用敏感数据，例如客户的 SSN。 而此信息后来不再需要向开发人员公开。 在这种情况下，敏感数据要么需要屏蔽，要么完全不公开。 SQL 数据库提供了两种方法来防止未经授权的用户查看敏感数据：

[动态数据掩码](sql-database-dynamic-data-masking-get-started.md)是一种数据屏蔽功能，它通过屏蔽应用程序层上的非特权用户来限制敏感数据的公开。 你可以定义屏蔽规则来创建屏蔽模式（例如，只显示国家 ID 号的最后 4 位数，如 XXX-XX-0000，并将大部分编号标记为 X），并确定哪些用户被排除在屏蔽规则之外。 随时都可能出现屏蔽，有各种屏蔽功能可用于各种数据类别。 通过动态数据屏蔽可以自动检测数据库中的敏感数据，并对其应用屏蔽。

通过[行级安全性](/sql/relational-databases/security/row-level-security)可以控制行级的访问权限。 也就是说，根据执行查询的用户（组成员或执行上下文）来隐藏数据库表中的某些行。 访问限制是在数据库层上完成的，而不是在应用层，以此简化你的应用逻辑。 首先创建一个筛选器谓词，筛选掉不公开的行，接下来，安全策略定义谁可以访问这些行。 最后，最终用户运行查询，根据用户特权，他们要么能查看受限行，要么根本无法看到这些行。

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>如何在云中管理加密密钥？

有用于 Always Encrypted（客户端加密）和透明数据加密（静态加密）的密钥管理选项。 建议定期轮换加密密钥。 轮换频率应遵循内部组织法规和符合性要求。

透明数据加密 (TDE)：TDE 中有一个双密钥层次结构 — 每个用户数据库中的数据都通过对称 AES-256 数据库唯一的数据库加密密钥 (DEK) 进行加密，该密钥反过来又由服务器唯一的不对称 RSA 2048 主密钥进行加密。 管理主密钥的方法：
- 由平台（SQL 数据库）自动管理。
- 或由你使用 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) 作为密钥存储。

为方便起见，透明数据加密的主密钥默认由 SQL 数据库服务托管。 如果组织想要控制主密钥，则可以使用 Azure Key Vault (sql-database-always-encrypted-azure-key-vault.md) 作为密钥存储。 通过使用 Azure Key Vault，组织假定控制密钥预配、轮换使用和权限控制。 [轮换或切换 TDE 主密钥类型](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)非常快，因为它仅重新加密 DEK。 对于安全性与数据管理角色分开的组织来说，安全管理员可以为 Azure Key Vault 中的 TDE 主密钥预配密钥材料，并为数据库管理员提供 Azure Key Vault 密钥标识符，用于在服务器上进行静态加密。 Key Vault 设计用于确保 Microsoft 不会看到或提取任何加密密钥。 你还可集中管理组织的密钥。 

**Always Encrypted**：Always Encrypted 中还有[两个密钥层次结构](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) - 一列敏感数据通过 AES 256 列加密密钥 (CEK) 加密，而该密钥反过来又由列主密钥 (CMK) 进行加密。 为 Always Encrypted 提供的客户端驱动程序在 CMK 长度上没有任何限制。 CEK 的加密值存储在数据库中，而 CMK 存储在受信任的密钥存储库中，如 Windows 证书存储、Azure Key Vault 或硬件安全模块。 
- [CEK 和 CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) 都可轮换使用。 
- CEK 轮换属于数据操作，可以是时间密集型，具体取决于包含加密列的表的大小。 因此最好按需规划 CEK 轮换。 
- 但 CMK 轮换不会影响数据库性能，并可以使用单独的角色来完成。
下图显示了 Always Encrypted 中的列主密钥的密钥存储选项

![Always Encrypted CMK 存储提供程序](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>如何优化和确保组织和 SQL 数据库间的流量安全？
组织和 SQL 数据库间的网络流量通常会通过公共网络进行路由。 但若要选择优化此路径并使其更安全，则可查看 Express Route。 Express Route 实质上是通过专用连接将公司网络扩展到 Azure 平台。 这样就不会浏览公共 Internet。 而且还可以提高安全性、可靠性并优化路由，相比浏览公共 Internet，降低了网络延迟并提高了速度。 如果计划在组织和 Azure 间传输大量数据，使用 Express Route 可提高成本效益。 从组织连接到 Azure 时，有三种不同连接模型可供选择： 
- [云交换归置](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [任意位置到任意位置](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [点到点](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route 还允许激增高达 2 倍的带宽限制，无需额外付费。 还可以使用 Express Route 来配置跨区域连接。 要查看 ER 连接提供商列表，请参阅：[Express Route 合作伙伴和对等互连位置](../expressroute/expressroute-locations.md)。 以下文章详细介绍了 Express Route：
- [Express Route 简介](../expressroute/expressroute-introduction.md)
- [先决条件](../expressroute/expressroute-prerequisites.md)
- [工作流](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL 数据库是否符合任何规章要求，这对我组织的合规性有什么帮助？
SQL 数据库符合一系列规章遵从性。 要查看符合的最新一组符合性，请访问 [Microsoft 信任中心](https://microsoft.com/en-us/trustcenter/compliance/complianceofferings)，并深入了解对你组织而言至关重要的符合性，以了解 SQL 数据库是否包含在符合的 Azure 服务中。 需要注意的是，尽管 SQL 数据库可能被认证为符合的服务，它有助于确保组织服务的符合性，但不会自动保证这一点。

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>迁移后的智能数据库监视和维护

将数据库迁移到 SQL 数据库后，你要监视数据库（例如，检查资源利用情况或进行 DBCC 检查）并执行定期维护（例如，重建或重新组织索引、统计数据等）。 幸运的是，SQL 数据库可智能地使用历史趋势、记录指标以及统计信息，主动帮助你监视和维护数据库，让应用程序始终以最佳状态运行。 在某些情况下，Azure SQL DB 可根据你的配置设置自动执行维护任务。 在 SQL 数据库中要监视数据库的三个方面：
- 性能监视和优化。
- 安全优化。
- 成本优化。

性能监视和优化：通过查询性能见解，可获得专门针对数据库工作负载的建议，以便应用程序始终以最佳运行状态。 你也可以进行设置，以自动应用这些建议并且无需执行维护任务。 利用索引顾问，可根据工作负载自动实施索引建议，这就是所谓的自动优化。 这些建议会随着应用程序工作负载的变化而变化，为你提供最相关的建议。 你也可以选择手动查看这些建议并自行应用。  

安全优化：SQL 数据库提供可行的安全建议，帮助保护数据，威胁检测可用于识别和调查可能对数据库构成威胁的可疑数据库活动。 [SQL 漏洞评估](sql-vulnerability-assessment.md)是一项数据库扫描和报告服务，允许你大规模监视数据库的安全状态、识别安全风险以及偏离你定义的安全基线的行为。 每次扫描之后，都会提供可行步骤和修正脚本的自定义列表，还会提供可用于帮助满足合规性的评估报告。

借助 Azure 安全中心，可以查看全面的安全建议，单击即可应用这些建议。 

成本优化：Azure SQL 平台会分析服务器中数据库的利用率历史记录，作出评估并给出成本优化建议。 这种分析通常需要两周时间来分析和生成可行的建议。 弹性池就这样一个建议。 建议以横幅形式出现在门户上：

![弹性池建议](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

你还可在“顾问”部分下查看此分析：

![弹性池建议 - 顾问](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>如何在 SQL 数据库中监视性能和资源利用率？

在 SQL 数据库中，可利用平台的智能见解来监控性能并做出相应调整。 可以使用以下方法监视 SQL 数据库中的性能和资源利用率：
- Azure 门户：Azure 门户通过选择数据库并单击“概述”窗格中的图表来显示单个数据库的利用率。 可以修改图表以显示多个指标，包括 CPU 百分比、DTU 百分比、数据 IO 百分比、会话百分比和数据库大小百分比。

   ![监视图表](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![监视图表 2](./media/sql-database-manage-after-migration/chart.png)

在此图表中，还可以按资源配置警报。 这些警报允许使用电子邮件响应资源条件，写入 HTTPS/HTTP 端点或执行操作。 有关详细说明，请参阅[监视 SQL 数据库中的数据库性能](sql-database-single-database-monitor.md)。

- 动态管理视图：可以查询 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 动态管理视图，以返回最近一个小时的资源使用统计信息历史记录，也可以查询 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 系统目录视图，返回过去 14 天的历史记录。
- 查询性能见解：可以使用[查询性能见解](sql-database-query-performance.md)查看特定数据库那些排名靠前的资源消耗查询和长时间运行查询的历史记录。 可通过资源利用率、持续时间和执行频率快速找出热门查询。 还可跟踪查询，并检测回归。 此功能需要为数据库启用和激活[查询存储](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。

   ![查询性能见解](./media/sql-database-manage-after-migration/query-performance-insight.png)

- Log Analytics 中的 Azure SQL Analytics（预览版）：[Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) 允许收集和可视化关键的 Azure SQL Azure 性能指标，对于每个工作区，最多支持 150,000 个 SQL 数据库和 5,000 个 SQL 弹性池。 你可以使用它监视并接收通知。 可以跨多个 Azure 订阅和弹性池监视 SQL 数据库和弹性池指标，并可用于识别应用程序堆栈每一层上的问题。

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>我注意到了性能问题：我的 SQL 数据库故障排除方法与 SQL Server 有何不同？
用于诊断查询和数据库性能问题的疑难解答技术的主要部分还是一样的。 毕竟支持云的 SQL Server 引擎是一样的。 但是 Azure SQL DB 平台却内置了“智能”功能。 它可帮助你更轻松地诊断和解决性能问题。 还可代表你执行某些纠正操作，在某些情况下，可自动执行主动修复措施。 

使用智能功能（如[查询性能见解 (QPI)](sql-database-query-performance.md) 加上[数据库顾问](sql-database-advisor.md)）非常有利于解决性能问题的方法，方法上的差异在这方面会有所不同，因为你不再需要手动找出可能有助于你排除手头问题的重要细节。 该平台帮你完成了这困难的一步。 QPI 就是一个例子。 借助 QPI，可以一直深入钻取到查询级别，查看历史趋势，并确定查询回归的准确时间。 数据库顾问可为你提供一些建议，帮助你改善整体性能，比如缺少索引、删除索引、参数化查询等。 

进行性能故障排除时，请务必确定是应用程序，还是支持它的数据库影响了应用程序的性能。 性能问题往往存在于应用程序层。 问题可能出现在体系结构或是数据访问模式上。 例如，假设你有一个对网络延迟敏感的聊天应用程序。 在这种情况下，应用程序会受到影响，因为在应用程序和服务器间往返出现了许多短请求（“聊天”），在网络拥塞时，这些往返请求会快速增加。 要在这种情况下提高性能，可使用[批量查询](sql-database-performance-guidance.md#batch-queries)。 批量查询会为你带来极大的帮助，因为此时会对请求进行批处理，帮助你减少往返请求的延迟，提高应用程序的性能。 

此外，如果注意到数据库总体性能下降，则可以监视 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 动态管理视图，了解 CPU、IO 和内存消耗情况。 应用性能可能会受到影响，因为数据库缺乏资源。 你可能需要根据工作负载需求增加和减少情况来更改性能级别和/或服务层。 

有关优化性能问题的全套建议，请参阅：[优化数据库](sql-database-performance-guidance.md#tune-your-database)。

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>如何确保我使用的是适当的服务层和性能级别？
SQL 数据库提供了各种服务层：基本、标准和高级。 在每个服务层，均可获得与服务级别相关的有保障且可预测的性能。 根据工作负荷情况，活动可能激增，资源利用率可能会达到当前所处性能等级的上限。 在这种情况下，最好先评估进行优化是否会有所帮助（例如，添加或更改索引等）。 如果仍旧存在限制问题，请考虑转移到更高的性能级别或服务级别。 

|**服务级别**|**常见用例方案**|
|---|---|
|**基本**|用户较少，数据库不具有高并发性、扩展性和性能要求的应用程序。 |
|**标准**|具有相当高的并发性、扩展性和性能要求以及低到中等 IO 要求的应用程序。 |
|**高级**|具有大量并发用户、高 CPU/内存和高 IO 需求的应用程序。 高并发性、高吞吐量和对延迟敏感的应用可利用高级等级。 |
|||

为确保处于正确的性能级别，可通过“如何监视 SQL 数据库中的性能和资源利用率”中所述的某种方式来监视查询和数据库资源消耗。 如果发现查询/数据库一直在 CPU/内存等上快速运行，则可考虑升级为更高的性能等级。 同样，如果注意到，即使在繁忙时间，似乎也没有使用多少资源，则可考虑下调当前性能等级。 

如果有 SaaS 应用模式或数据库整合方案，则考虑使用弹性池进行成本优化。 弹性池非常适合用于整合数据库和优化成本。 要了解有关使用弹性池管理多个数据库的详细信息，请参阅：[管理池和数据库](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)。 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>需要多久运行一次数据库完整性检查？
SQL 数据库使用的某些智能技术可以实现自动处理某些类别的数据损坏，不会出现数据丢失。 这些技术内置于服务中，有需要时，服务会利用这些技术。 通过定期还原数据库备份并对其运行 DBCC CHECKDB，测试整个服务的数据库备份。 如果存在问题，SQL 数据库会主动解决。 [自动页修复](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)用于修复损坏或具有数据完整性问题的页面。 数据库页面始终由验证页面完整性的默认 CHECKSUM 设置进行验证。 SQL 数据库会主动监视和检查数据库的数据完整性，如果存在问题，则以最高优先级解决问题。 除此之外，还可自己选择随时运行完整性检查。  有关详细信息，请参阅 [SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>迁移后的数据移动

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>如何从 SQL 数据库将数据作为 BACPAC 文件导出和导入？

- 导出：可以将你的 Azure SQL 数据库作为 BACPAC 文件从 Azure 门户导出

   ![数据库导出](./media/sql-database-export/database-export.png)

- 导入：你还可使用 Azure 门户将数据作为 BACPAC 文件导入到数据库。

   ![数据库导入](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>如何同步 SQL 数据库 SQL Server 间的数据？
可使用多种方法实现此目的： 
- **[数据同步](sql-database-sync-data.md)** - 此功能可帮助你在多个本地 SQL Server 数据库和 SQL 数据库之间双向同步数据。 要与本地 SQL Server 数据库同步，则需在本地计算机上安装和配置同步代理，并打开出站 TCP 端口 1433。
- **[事务复制](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** - 使用事务复制功能，可将数据从本地同步到 Azure SQL DB，本地作为发布服务器，而 Azure SQL DB作为订阅服务器。 目前仅支持此安装程序。 有关如何以最少停机时间将数据从本地迁移到 Azure SQL 的详细信息，请参阅：[使用事务复制](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>后续步骤
了解 [SQL 数据库](sql-database-technical-overview.md)。

