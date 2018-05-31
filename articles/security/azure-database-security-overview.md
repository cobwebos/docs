---
title: Azure 数据库安全概述 | Microsoft Docs
description: 本文概述了 Azure 数据库安全功能。
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 44abf7a4fc24893146179b34d3357f54450decab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365138"
---
# <a name="azure-database-security-overview"></a>Azure 数据库安全性概述

安全性是管理数据库时的首要考虑因素，并且始终是 Azure SQL 数据库的优先事务。 Azure SQL 数据库支持使用防火墙规则和连接加密的连接安全。 它支持使用用户名和密码进行身份验证，以及使用 Azure Active Directory 管理的标识的 Azure Active Directory (Azure AD) 身份验证。 授权使用基于角色的访问控制。

Azure SQL 数据库通过对静态的数据库、关联备份和事务日志文件执行实时加密和解密来支持加密，而无需更改应用程序。

Microsoft 提供了加密企业数据的其他方法：

-   单元级加密可用于通过不同加密密钥来加密特定数据列甚至数据单元。
-   如需硬件安全模块或需集中管理加密密钥层次结构，请考虑对 Azure 虚拟机 (VM) 中的 SQL Server 使用 Azure Key Vault。
-   Always Encrypted（目前为预览版）使加密对应用程序透明。 它还允许客户在客户端应用程序内加密敏感数据，无需与 SQL 数据库共享加密密钥。

Azure SQL 数据库审核使企业能够将事件记录到 Azure 存储中的审核日志。 SQL 数据库审核还与 Microsoft Power BI 集成，以帮助向下钻取报告和分析数据。

严格保护 Azure SQL 数据库，以符合大部分法规或安全要求，包括 HIPAA、ISO 27001/27002 和 PCI DSS Level 1。 [Microsoft Azure 信任中心站点](http://azure.microsoft.com/support/trust-center/services/)上提供了安全合规认证的最新列表。

本文逐步介绍保护 Microsoft Azure SQL 数据库的结构化、表格和关系数据的基础知识。 具体而言，本文介绍如何使用相应的资源来保护数据、控制访问和执行主动监视。

## <a name="protection-of-data"></a>数据保护

SQL 数据库可提供加密功能来帮助保护数据：

- 通过[传输层安全性 (TLS)](https://support.microsoft.com/kb/3135244) 来保护动态数据。
- 通过[透明数据加密](http://go.microsoft.com/fwlink/?LinkId=526242)保护来静态数据。
- 通过 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 来保护使用中的数据。

若要通过其他方法加密数据，请考虑：

-   使用[单元格级加密](https://msdn.microsoft.com/library/ms179331.aspx)，借助不同的加密密钥来加密特定的数据列甚至数据单元格。
-   如需硬件安全模块或需集中管理加密密钥层次结构，请参阅[对 Azure VM 中的 SQL Server 使用 Azure Key Vault](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。

### <a name="encryption-in-motion"></a>动态加密

所有客户端/服务器应用程序有一个常见问题，即数据在公共和专用网络中传输时需要隐私。 如果通过网络传输的数据未加密，则数据有可能被未经授权的用户捕获和窃取。 处理数据库服务时，请务必对在数据库客户端和服务器间传输的数据进行加密。 此外，还务必对在相互通信的数据库服务器间和中间层应用程序间传输的数据进行加密。

管理网络时存在一个问题：需要保护跨不可信网络在应用程序之间发送的数据。 可以使用 [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) 验证服务器和客户端，然后使用它来加密经过身份验证的双方之间的消息。

在身份验证过程中，TLS/SSL 客户端会向 TLS/SSL 服务器发送消息。 服务器用验证服务器本身所需的信息进行响应。 客户端和服务器执行会话密钥的额外交换，身份验证对话结束。 完成身份验证后，可以通过在身份验证过程中建立的对称加密密钥在服务器和客户端之间开始 SSL 保护的通信。

在与数据库来回“传输”数据时，与 Azure SQL 数据库建立的所有连接都需要经过加密 (TLS/SSL)。 SQL 数据库使用 TLS/SSL 验证服务器和客户端，然后使用它来加密经过身份验证的双方之间的消息。 

在应用程序的连接字符串中，必须指定参数来加密连接，而不是信任服务器证书。 （如果将连接字符串从 Azure 门户中复制出来，则我们会代你完成此操作。）否则，连接不会验证服务器的身份，且容易受到“中间人”攻击。 例如，对于 ADO.NET 驱动程序，这些连接字符串参数为 `Encrypt=True` 和 `TrustServerCertificate=False`。

### <a name="encryption-at-rest"></a>静态加密
可采取一些预防措施来帮助保护数据库。 例如设计安全系统、加密机密资产、围绕数据库服务器构建防火墙。 但如果物理媒体（如驱动器或备份磁带）失窃，恶意方可能会还原或附加数据库并浏览数据。

一种解决方法是加密数据库中的敏感数据，并使用证书保护用于加密数据的密钥。 此解决方案可防止没有密钥的人使用数据，但这种保护必须经过精心规划。

为解决此问题，SQL Server 和 SQL 数据库支持[透明数据加密](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde)。 透明数据加密可加密 SQL Server 和 SQL 数据库数据文件，这称为静态加密数据。

透明数据加密有助于防范恶意活动的威胁。 它可执行静态数据库、关联备份和事务日志文件的实时加密和解密，无需更改应用程序。  

透明数据加密使用称为数据库加密密钥的对称密钥来加密整个数据库的存储。 在 SQL 数据库中，数据库加密密钥由内置服务器证书保护。 内置服务器证书对每个 SQL 数据库服务器都是唯一的。

如果数据库处于 Geo-DR 关系中，则会受到每个服务器上不同的密钥的保护。 如果两个数据库连接到同一个服务器，则它们共用相同的内置证书。 Microsoft 每隔 90 天自动轮换这些证书至少一次。 

有关详细信息，请参阅[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)。

### <a name="encryption-in-use-client"></a>使用中的加密（客户端）

大部分数据泄漏涉及窃取关键数据，如信用卡号或个人身份信息。 数据库可能是宝贵的敏感信息。 其中可能包含客户的个人数据（如身份证号码）、机密的竞争信息和知识产权。 数据丢失或被盗（尤其是客户数据）可能会使企业形象受损，让企业落于竞争劣势，遭受严重罚款甚至面临诉讼。

![以锁和钥匙形式显示 Always Encrypted 功能](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 功能旨在保护存储在 Azure SQL 数据库或 SQL Server 数据库中的敏感数据。 通过 Always Encrypted，客户可在客户端应用程序中加密敏感数据，永远不会向数据库引擎（SQL 数据库或 SQL Server）透露加密密钥。

Always Encrypted 将数据所有者与数据管理者区分开来，前者可查看数据，而后者无权访问数据。 它有助于确保本地数据库管理员、云数据库操作员或其他拥有高级特权但未经授权的用户无法访问加密数据。

此外，Always Encrypted 使加密对应用程序透明。 在客户端计算机上安装启用了 Always Encrypted 的驱动程序，以便自动加密和解密客户端应用程序中的敏感数据。 将数据传输到数据库引擎前，驱动程序会对敏感列中的数据进行加密。 驱动程序会自动重写查询，以便保留应用程序的语义。 同样，该驱动程序透明地解密存储在查询结果中包含的加密数据库列中的数据。

## <a name="access-control"></a>访问控制
为确保安全性，SQL 数据库通过以下方式来控制访问：

- 采用通过 IP 地址限制连接的防火墙规则。
- 采用身份验证机制，要求用户表明其身份。
- 采用授权机制，限制用户执行特定操作，访问特定数据。

### <a name="database-access"></a>数据库访问

数据保护从控制对数据的访问开始。 由承载数据的数据中心管理物理访问。 可配置防火墙来管理网络层的安全。 此外，还可以通过配置用于身份验证的登录信息并定义服务器和数据库角色的权限来控制访问。

#### <a name="firewall-and-firewall-rules"></a>防火墙和防火墙规则

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)为 Azure 和其他基于 Internet 的应用程序提供关系数据库服务。 为了保护数据，在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。 有关详细信息，请参阅 [Azure SQL 数据库防火墙规则概述](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。

只能通过 TCP 端口 1433 使用 Azure SQL 数据库服务。 若要从计算机访问 SQL 数据库，请确保客户端计算机防火墙允许 TCP 端口 1433 上的传出 TCP 通信。 如果其他应用程序不需入站连接，则阻止 TCP 端口 1433 上的入站连接。

#### <a name="authentication"></a>身份验证

身份验证是指连接到数据库时如何证明身份。 SQL 数据库支持两种类型的身份验证：

-   **SQL Server 身份验证：** 创建逻辑 SQL 实例时会创建单个登录帐户，称为“SQL 数据库订户帐户”。 此帐户通过 [SQL Server 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)（用户名和密码）进行连接。 此帐户为管理员，负责管理逻辑服务器实例以及所有附加到该实例的用户数据库。 不能限制订户帐户的权限。 此类帐户只能存在一个。
-   **Azure Active Directory 身份验证**：[Azure AD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)是使用 Azure AD 中的标识连接到 Azure SQL 数据库和 SQL 数据仓库的一种机制。 可通过它集中管理数据库用户的身份。

![使用 SQL 数据库进行 Azure AD 身份验证](./media/azure-databse-security-overview/azure-database-fig2.png)

 Azure AD 身份验证的优点包括：
  - 提供一个 SQL Server 身份验证的替代方法。
  - 它有助于阻止跨数据库服务器的用户身份的扩散，且允许在一个地方进行密码轮换。
  - 可使用外部 (Azure AD) 组管理数据库权限。
  - 它可通过启用集成的 Windows 身份验证和 Azure AD 支持的其他形式的身份验证来消除存储密码。

#### <a name="authorization"></a>授权
[授权](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)是指用户可在 Azure SQL 数据库中执行的操作。 授权由用户帐户的数据库[角色成员资格](https://msdn.microsoft.com/library/ms189121)和[对象级权限](https://msdn.microsoft.com/library/ms191291.aspx)控制。 授权是确定主体可以访问哪些安全对象资源以及哪些操作允许用于这些资源的过程。

### <a name="application-access"></a>应用程序访问

#### <a name="dynamic-data-masking"></a>动态数据掩码
呼叫中心的服务代表可能会通过呼叫者社会安全号码或信用卡号中的几位数字来识别呼叫者。 但不应向服务代表完全公开这些数据项。

可定义掩码规则，遮掩任何查询结果集中除身份证号或信用卡号后四位数以外的其他所有数字。

![掩蔽在 SQL 数据库和业务应用间发送的号码](./media/azure-databse-security-overview/azure-database-fig3.png)

再举一个例子，可定义合适的数据掩码来保护个人身份信息。 然后开发人员可查询生产环境，进行故障排除，而不违反法规。

[SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户模糊化敏感数据来限制此类数据的泄露。 V12 版的 Azure SQL 数据库支持动态数据屏蔽。

[动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)允许用户指定在对应用层产生最小影响的前提下可以透露的敏感数据量，从而帮助防止未经授权的用户访问敏感数据。 它是一种基于策略的安全功能，会在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。


> [!Note]
> Azure 数据库管理员、服务器管理员或安全主管角色可以配置动态数据屏蔽。

#### <a name="row-level-security"></a>行级安全性
多租户数据库的另一个常见安全要求是[行级安全性](https://msdn.microsoft.com/library/dn765131.aspx)。 可使用此功能根据执行查询的用户的特征控制对数据库表中的行的访问。 （特征可以是组成员身份或执行上下文等。）

![行级安全允许用户通过客户端应用访问表中的行](./media/azure-databse-security-overview/azure-database-fig4.png)

访问限制逻辑位于数据库层，而不会脱离另一应用程序层中的数据。 每当从任一层尝试访问数据时，数据库系统就会应用访问限制。 这样就会通过减少安全系统的外围应用，使安全系统变得更加可靠和稳健。

行级安全性引入了基于谓词的访问控制。 它可进行灵活的集中式评估，能考虑元数据或管理员根据需要确定的任何其他标准。 谓词用作判断用户是否对基于用户属性的数据具有适当访问权限的标准。 可通过使用基于谓词的访问控制来实现基于标签的访问控制。

## <a name="proactive-monitoring"></a>主动监视
SQL 数据库可提供“审核”和“威胁检测”功能，有助于保护数据。

### <a name="auditing"></a>审核
[Azure SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)可提高用户深入了解数据库中发生的事件和更改的能力。 示例包括针对数据的更新和查询。

SQL 数据库审核可跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核可帮助你遵守法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。 审核有助于遵从法规标准，但不能保证遵从法规。

可使用 SQL 数据库审核来：

-   **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
-   **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
-   **分析**报告。 可以查找可疑事件、异常活动和趋势。

有两种审核方法：

-   **Blob 审核**将日志写入 Azure Blob 存储。 这是一种较新的审核方法。 这种方法可提供更高的性能，支持更高粒度的对象级审核，且更具成本效益。
-   **表审核**：将日志写入 Azure 表存储。

### <a name="threat-detection"></a>威胁检测
[Azure SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)可检测到指示潜在安全威胁的可疑活动。 可使用威胁检测响应数据库中发生的可疑事件，例如 SQL 注入。 它可提供警报，并允许使用 Azure SQL 数据库审核来探查可疑事件。

![SQL 数据库和 Web 应用的威胁检测，包括外部攻击者和恶意内部人员](./media/azure-databse-security-overview/azure-database-fig5.jpg)

例如，SQL 注入是一种常见的 Web 应用程序安全问题。 它被用来攻击由数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。

发生可疑数据库活动时，安全监管员或其他指定的管理员可以获取相关即时通知。 每个通知提供可疑活动的详细信息，以及如何进一步调查和缓解威胁的建议。        

## <a name="centralized-security-management"></a>集中的安全管理

[Azure 安全中心](https://azure.microsoft.com/documentation/services/security-center/)可帮助防范、检测和应对威胁。 它为 Azure 订阅提供集成的安全监控和策略管理。 它有助于检测可能会被忽视的威胁，适用于各种安全解决方案生态系统。

[安全中心](https://docs.microsoft.com/azure/security-center/security-center-sql-database)通过为所有服务器和数据库提供安全性的可见性来帮助保护 SQL 数据库中的数据。 通过安全中心，可以：

-   定义 SQL 数据库加密和审核的策略。
-   跨所有订阅监视 SQL 数据库资源的安全性。
-   快速识别和修复安全问题。
-   集成来自 [Azure SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)的警报。

安全中心支持基于角色的访问。

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace 是一个在线应用程序和服务市场，初创公司和独立软件供应商 (ISV) 能够通过它为全球 Azure 客户提供解决方案。
Azure Marketplace 与 Microsoft Azure 合作伙伴生态系统结合为一个统一的平台，以便更好地服务于客户和合作伙伴。 可[运行搜索](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1)，查看 Azure Marketplace 中可用的数据库安全产品。

## <a name="next-steps"></a>后续步骤

- [保护 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Azure 安全中心与 Azure SQL 数据库服务](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [提高 SQL 数据库性能](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
