---
title: "Azure 数据库安全最佳做法 | Microsoft Docs"
description: "本文提供有关 Azure 数据库安全性的一套最佳做法。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: b3a9befe5c6607c108e11b583f8b67c483710021
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2017
---
# <a name="azure-database-security-best-practices"></a>Azure 数据库安全性最佳做法

安全性是管理数据库时的首要考虑因素，并且始终是 Azure SQL 数据库的优先事务。 严格保护数据库有助于满足大部分法规或安全要求，包括 HIPAA、ISO 27001/27002 和 PCI DSS Level 1 等等。 [Microsoft 信任中心站点](http://azure.microsoft.com/support/trust-center/services/)上提供了安全合规认证的最新列表。 也可以根据法规要求将数据库放置在特定的 Azure 数据中心。

本文介绍一系列 Azure 数据库安全最佳做法。 这些最佳做法衍生自我们的 Azure 数据库安全经验和客户的经验。

对于每项最佳做法，本文将说明：

-   最佳实践是什么
-   为何要启用该最佳实践
-   如果无法启用该最佳实践，可能的结果是什么
-   如何学习启用最佳实践

这篇 Azure 数据库安全最佳做法以共识以及 Azure 平台功能和特性集（因为在编写本文时已存在）为基础。 看法和技术将随着时间改变，本文会定期更新以反映这些更改。

本文介绍的 Azure 数据库安全最佳做法包括：

-   使用防火墙规则来限制数据库访问
-   启用数据库身份验证
-   使用加密保护数据
-   保护传输中的数据
-   启用数据库审核
-   启用数据库威胁检测


## <a name="use-firewall-rules-to-restrict-database-access"></a>使用防火墙规则来限制数据库访问

Microsoft Azure SQL 数据库为 Azure 和其他基于 Internet 的应用程序提供关系数据库服务。 为了提供访问安全性，SQL 数据库使用按 IP 地址限制连接的防火墙规则、要求用户证明其身份的身份验证机制，以及只允许用户执行特定操作和访问特定数据的授权机制，来控制访问。 在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。

![防火墙规则](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

只能通过 TCP 端口 1433 使用 Azure SQL 数据库服务。 若要从计算机访问 SQL 数据库，请确保客户端计算机防火墙允许 TCP 端口 1433 上的传出 TCP 通信。 如果其他应用程序不需要，则使用防火墙规则阻止 TCP 端口 1433 上的入站连接。

在连接过程中，来自 Azure 虚拟机的连接将重定向到每个辅助角色特有的不同 IP 地址和端口。 该端口号在 11000 到 11999 的范围内。 有关 TCP 端口的详细信息，请参阅[用于 ADO.NET 4.5 和 SQL Database2 的非 1433 端口](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)。

> [!Note]
> 有关 SQL 数据库中的防火墙规则的详细信息，请参阅 [SQL 数据库防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。

## <a name="enable-database-authentication"></a>启用数据库身份验证
SQL 数据库支持两种身份验证：SQL 身份验证和 Azure Active Directory 身份验证（Azure AD 身份验证）。

对于以下情况，建议使用 **SQL 身份验证**：

-   这种身份验证可让 SQL Azure 支持包含混合操作系统的环境（其中的所有用户不是通过 Windows 域进行身份验证）。
-   可让 SQL Azure 支持旧式应用程序，以及第三方提供的、需要 SQL Server 身份验证的应用程序。
-   可让用户从未知或不受信任的域进行连接。 例如，在某个应用程序中，指定的客户可以使用分配的 SQL Server 登录名建立连接，以接收其订单的状态。
-   可让 SQL Azure 支持基于 Web 的应用程序（其中的用户可创建自己的标识）。
-   可让软件开发人员基于已知的预设 SQL Server 登录名使用复杂的权限层次结构来分发应用程序。

> [!Note]
> 但是，SQL Server 身份验证无法使用 Kerberos 安全协议。

如果使用 **SQL 身份验证**，则必须：

-   自行管理强凭据。
-   保护连接字符串中的凭据。
-   （可能需要）保护通过网络从 Web 服务器传递到数据库的凭据。 有关详细信息，请参阅[如何：在 ASP.NET 2.0 中使用 SQL 身份验证连接到 SQL Server](https://msdn.microsoft.com/library/ms998300.aspx)。

**Azure Active Directory 身份验证**是使用 Azure Active Directory (Azure AD) 中的标识连接到 Microsoft Azure SQL 数据库和 [SQL 数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)的一种机制。 通过 Azure AD 身份验证，可以在一个中心位置中集中管理数据库用户和其他 Microsoft 服务的标识。 集中 ID 管理提供一个单一位置来管理数据库用户，并简化权限管理。 包括如下优点：

-   提供一个 SQL Server 身份验证的替代方法。
-   帮助阻止用户标识在数据库服务器之间激增。
-   允许在单一位置中轮换密码。
-   客户可以使用外部 (AAD) 组管理数据库权限。
-   它可以通过启用集成的 Windows 身份验证和 Azure Active Directory 支持的其他形式的身份验证来消除存储密码。
-   Azure AD 身份验证使用包含的数据库用户以数据库级别对标识进行身份验证。
-   Azure AD 支持对连接到 SQL 数据库的应用程序进行基于令牌的身份验证。
-   Azure AD 身份验证支持对本地 Azure Active Directory 进行 ADFS（域联合）或本机用户/密码身份验证，无需进行域同步。
-   Azure AD 支持从 SQL Server Management Studio 进行连接，后者使用 Active Directory 通用身份验证，其中包括多重身份验证 (MFA)。 MFA 包括利用一系列简单的验证选项进行的强身份验证，这些选项包括电话、短信、含有 PIN 码的智能卡或移动应用通知。 有关详细信息，请参阅 [SQL 数据库和 SQL 数据仓库针对 Azure AD MFA 的 SSMS 支持](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)。

配置步骤包括配置和使用 Azure Active Directory 身份验证的以下过程。

-   创建并填充 Azure AD。
-   可选：关联或更改当前与 Azure 订阅关联的 Active Directory。
-   为 Azure SQL Server 或 [Azure SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/)创建 Azure Active Directory 管理员。
- 配置客户端计算机。
-   在映射到 Azure AD 标识的数据库中创建包含的数据库用户。
-   通过使用 Azure AD 标识连接到数据库。

可以在[此处](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)查看详细信息。

## <a name="protect-your-data-using-encryption"></a>使用加密保护数据

[Azure SQL 数据库透明数据加密 (TDE)](https://msdn.microsoft.com/library/dn948096.aspx) 无需更改应用程序，即可对静态的数据库、关联的备份和事务日志执行实时加密和解密，帮助防止恶意活动的威胁。 TDE 使用称为数据库加密密钥的对称密钥来加密整个数据库的存储。

即使整个存储都已加密，也一定要加密数据库本身。 这是数据保护的深度防御方法实现。 如果使用 Azure SQL 数据库，并想要保护敏感数据（例如信用卡号或社会安全号码），可以使用 FIPS 140-2 验证的 256 位 AES 加密来加密数据库，它符合许多行业标准（例如 HIPAA、PCI）的要求。

请务必了解，使用 TDE 加密数据库时，[缓冲池扩展 (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) 相关文件不会加密。 必须对 BPE 相关文件使用文件系统级别的加密工具（例如 [BitLocker](https://technet.microsoft.com/library/cc732774)）或[加密文件系统 (EFS)](https://technet.microsoft.com/library/cc700811.aspx)。

因为经过授权的用户（如安全管理员或数据库管理员）可以访问数据，所以即使已使用 TDE 将数据库加密，也应遵循以下建议：

-   启用数据库级别的 SQL 身份验证。
-   通过 [RBAC 角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)使用 Azure AD 身份验证。
-   用户和应用程序应使用不同的帐户进行身份验证。 这样，可以限制授予用户和应用程序的权限，并降低恶意活动的风险。
-   使用固定的数据库角色（例如 db_datareader 或 db_datawriter）实现数据库级别安全，或者为应用程序创建自定义角色，以向选定的数据库对象授予显式权限。

若要通过其他方法加密数据，请考虑：

-   使用[单元格级加密](https://msdn.microsoft.com/library/ms179331.aspx)，借助不同的加密密钥来加密特定的数据列甚至数据单元格。
-   使用 Always Encrypted 启用加密：通过 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)，客户可在客户端应用程序中加密敏感数据，永远不会向数据库引擎（SQL 数据库或 SQL Server）透露加密密钥。 因此，Always Encrypted 将数据所有者与数据管理者区分开来，前者可查看数据，而后者无权访问数据。
-   使用行级别安全性：行级别安全性使客户能够根据执行查询的用户的特征（例如，组成员身份或执行上下文），控制对数据库表中的行的访问。 有关详细信息，请参阅[行级别安全性](https://msdn.microsoft.com/library/dn765131)。

## <a name="protect-data-in-transit"></a>保护传输中的数据
保护传输中的数据应该是数据保护策略中不可或缺的部分。 由于数据将从许多位置来回移动，一般将建议始终使用 SSL/TLS 协议来交换不同位置的数据。 在某些情况下，建议使用虚拟专用网络 (VPN) 隔离本地与云基础结构之间的整个通信通道。

对于在本地基础结构与 Azure 之间移动的数据，应该考虑适当的防护措施，例如 HTTPS 或 VPN。

对于需要从位于本地的多个工作站安全访问 Azure 的组织而言，请使用 [Azure 站点到站点 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create)。

对于需要从各个本地或场外工作站安全访问 Azure 的组织而言，可以考虑使用[点到站点 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create)。

可以通过专用高速 WAN 链路（例如 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)）移动较大的数据集。 如果选择使用 ExpressRoute，则还可以使用 [SSL/TLS](https://support.microsoft.com/kb/257591) 或其他协议，在应用程序级别加密数据，以提供额外的保护。

如果通过 Azure 门户与 Azure 存储交互，则所有事务都将通过 HTTPS 发生。 也可以使用基于 HTTPS 的[存储 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 来与 [Azure 存储](https://azure.microsoft.com/services/storage/)和 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)交互。

无法保护传输中数据的组织更容易遭受[中间人攻击](https://technet.microsoft.com/library/gg195821.aspx)、[窃听](https://technet.microsoft.com/library/gg195641.aspx)和会话劫持。 这些攻击可能是获取机密数据访问权限的第一步。

有关 Azure VPN 选项的详细信息，请阅读[规划和设计 VPN 网关](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design)一文。

## <a name="enable-database-auditing"></a>启用数据库审核
审核 SQL Server 数据库引擎或单个数据库的实例涉及到跟踪和记录数据库引擎中发生的事件。 使用 SQL Server 审核可以创建服务器审核，其中可能包含服务器级事件的服务器审核规范，以及数据库级事件的数据库审核规范。 可将审核的事件写入事件日志或审核文件。

SQL Server 有多个审核级别，具体取决于针对安装的政府要求或标准要求。 SQL Server 审核提供必备的工具和流程让我们启用、存储和查看有关各个服务器和数据库对象的审核。

[Azure SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。

审核可帮助你一直保持符合法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

审核有助于遵从法规标准，但不能保证遵从法规。

有关数据库审核及其启用方法的详细信息，请阅读[在 Azure 安全中心对 SQL Server 启用审核和威胁检测](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers)一文。

## <a name="enable-database-threat-detection"></a>启用数据库威胁检测
SQL 威胁检测可在发生异常活动时提供安全警报，让我们检测潜在威胁并做出响应。 出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式时，将会发出警报。 SQL 威胁检测警报提供可疑活动的详细信息，以及如何调查和缓解威胁的推荐操作。

例如，SQL 注入是 Internet 上常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。

若要了解如何在 Azure 门户中为数据库设置威胁检测，请参阅 [SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。

此外，SQL 威胁检测可将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成。 我们诚邀各位免费试用该功能 60 天。

有关数据库威胁检测及其启用方法的详细信息，请阅读[在 Azure 安全中心对 SQL Server 启用审核和威胁检测](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers)一文。

## <a name="conclusion"></a>结束语
Azure 数据库是一个可靠的数据库平台，提供满足众多组织要求与合规要求的整套安全功能。 通过控制对数据的物理访问，结合透明数据加密、单元格级加密或行级别安全性使用各种文件级、列级或行级数据安全选项，有助于保护数据。 此外，Always Encrypted 支持针对加密的数据执行操作，简化应用程序更新的过程。 反过来，访问 SQL 数据库活动的审核日志可以提供所需的信息来帮助自己了解何时以何种方法访问了数据。

## <a name="next-steps"></a>后续步骤
- 有关防火墙规则的详细信息，请参阅[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。
- 若要了解用户和登录名，请参阅[管理登录名](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)。
- 相关教程，请参阅[保护 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)。
