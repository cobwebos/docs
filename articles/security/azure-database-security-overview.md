---
title: "Azure 数据库安全概述 | Microsoft Docs"
description: "本文概述了 Azure 数据库安全功能。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: TomSh
ms.openlocfilehash: 3b8038b716266326967e2cf178efe4d36f4badc5
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="azure-database-security-overview"></a>Azure 数据库安全性概述

安全性是管理数据库时的首要考虑因素，并且始终是 Azure SQL 数据库的优先事务。 Azure SQL 数据库支持使用防火墙规则和连接加密的连接安全。 它支持使用用户名和密码的身份验证，以及使用 Azure Active Directory 管理的标识的 Azure Active Directory 身份验证。 授权使用基于角色的访问控制。

Azure SQL 数据库通过对静态的数据库、关联备份和事务日志文件执行实时加密和解密来支持加密，而无需更改应用程序。

Microsoft 提供了加密企业数据的其他方法：

-   单元格级加密，借助不同的加密密钥来加密特定的数据列甚至数据单元格。
-   如果需要硬件安全模块或需要加密密钥层次结构的集中管理，请考虑对 Azure VM 中的 SQL Server 使用 Azure 密钥保管库。
-   Always Encrypted（目前为预览版）使得加密对于应用程序来说是透明的，从而让客户端能够加密客户端应用程序中的敏感数据，不必与 SQL 数据库共享加密密钥。

Azure SQL 数据库审核使企业能够将事件记录到审核登录 Azure 存储。 SQL 数据库审核还与 Microsoft Power BI 集成，以帮助向下钻取报告和分析数据。

 严格保护 SQL Azure 数据库可符合大部分法规或安全要求，包括 HIPAA、ISO 27001/27002 和 PCI DSS Level 1 等等。 [Microsoft Azure 信任中心站点](http://azure.microsoft.com/support/trust-center/services/)上提供了安全合规认证的最新列表。

本文逐步介绍保护 Microsoft Azure SQL 数据库的结构化、表格和关系数据的基础知识。 具体而言，本文介绍如何使用相应的资源来保护数据、控制访问和执行主动监视。

此 Azure 数据库安全概述文章将重点介绍以下区域：

-   保护数据
-   访问控制
-   主动监视
-   集中的安全管理
-   Azure Marketplace

## <a name="protect-data"></a>保护数据

SQL 数据库可以保护数据。对于动态数据，它使用[传输层安全性](https://support.microsoft.com/kb/3135244)提供加密；对于静态数据，使用[透明数据加密](http://go.microsoft.com/fwlink/?LinkId=526242)提供加密；对于使用中的数据，将使用 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 提供加密。

在本部分中，我们将讨论：

-   动态加密
-   静态加密
-   使用中的加密（客户端）

若要通过其他方法加密数据，请考虑：

-   使用[单元格级加密](https://msdn.microsoft.com/library/ms179331.aspx)，借助不同的加密密钥来加密特定的数据列甚至数据单元格。
-   如果需要硬件安全模块或需要加密密钥层次结构的集中管理，请考虑[对 Azure VM 中的 SQL Server 使用 Azure 密钥保管库](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。

### <a name="encryption-in-motion"></a>动态加密

所有客户端/服务器应用程序有一个常见问题，即数据在公共和专用网络中传输时需要隐私。 如果通过网络传输的数据未加密，则数据有可能被未经授权的用户捕获和窃取。 在处理数据库服务时，需要确保数据在数据库客户端和服务器之间进行加密，并且在相互通信且具有中间层应用程序的数据库服务器之间进行加密。

管理网络时存在一个问题：需要保护跨不可信网络在应用程序之间发送的数据。 可以使用 [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) 验证服务器和客户端，然后使用它来加密经过身份验证的双方之间的消息。

在身份验证过程中，TLS/SSL 客户端向 TLS/SSL 服务器发送消息，服务器使用服务器需要验证自身身份的信息进行响应。 客户端和服务器执行会话密钥的额外交换，身份验证对话结束。 完成身份验证后，可以在使用在身份验证过程中建立的对称加密密钥的服务器和客户端之间开始 SSL 保护的通信。

在与数据库相互“传输”数据时，与 Azure SQL 数据库建立的所有连接都需要经过加密 (SSL/TLS)。 SQL Azure 使用 TLS/SSL 验证服务器和客户端，然后使用它来加密经过身份验证的双方之间的消息。 必须在应用程序连接字符串中指定用于加密连接的参数，而不要信任服务器证书（服务器证书用于将连接字符串复制到 Azure 经典门户外部），否则，连接将不会验证服务器的身份，并且容易受到“中间人”攻击。 例如，对于 ADO.NET 驱动程序，这些连接字符串参数为 Encrypt=True 和 TrustServerCertificate=False。

### <a name="encryption-at-rest"></a>静态加密
可以采取多种预防措施来帮助保护数据库，例如，设计安全系统、加密机密资产，以及围绕数据库服务器构建防火墙。 但是，如果物理媒体（如驱动器或备份磁带）失窃，恶意方可能会还原或附加数据库并浏览数据。

一种解决方法是加密数据库中的敏感数据，并使用证书保护用于加密数据的密钥。 这可以防止没有密钥的任何人都能使用数据，但这种保护必须规划好。

为了解决此问题，SQL Server 和 Azure SQL 支持[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde)。 TDE 加密 SQL Server 和 Azure SQL 数据库数据文件，称为静态加密数据。

Azure SQL 数据库透明数据加密无需更改应用程序，即可对静态的数据库、关联的备份和事务日志文件执行实时加密和解密，帮助防止恶意活动的威胁。  

TDE 使用称为数据库加密密钥的对称密钥来加密整个数据库的存储。 在 SQL 数据库中，数据库加密密钥由内置服务器证书保护。 内置服务器证书对每个 SQL 数据库服务器都是唯一的。

如果数据库处于 GeoDR 关系中，则会受到每个服务器上不同的密钥的保护。 如果两个数据库连接到同一个服务器，则它们共用相同的内置证书。 Microsoft 每隔 90 天自动轮换这些证书至少一次。 有关 TDE 的一般描述，请参阅[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)。

### <a name="encryption-in-use-client"></a>使用中的加密（客户端）

大部分数据泄漏涉及窃取关键数据，如信用卡号或个人身份信息。 数据库可能是宝贵的敏感信息。 其中可能包含客户的个人数据、机密的竞争信息和知识产权。 丢失或被盗的数据（尤其是客户数据）可能会导致破坏品牌形象、处于竞争劣势和遭受严重罚款甚至成为诉讼案件。

![始终加密](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 功能旨在保护 Azure SQL 数据库或 SQL Server 数据库中存储的敏感数据，如信用卡号或国民身份证号（例如，美国社会安全号码）。 通过 Always Encrypted，客户可在客户端应用程序中加密敏感数据，永远不会向数据库引擎（SQL 数据库或 SQL Server）透露加密密钥。

Always Encrypted 将数据所有者与数据管理者区分开来，前者可查看数据，而后者无权访问数据。 确保本地数据库管理员可以访问加密的数据，而云数据库操作员或其他高特权但未经授权的用户无法访问。

此外，Always Encrypted 使加密对应用程序透明。 启用了 Always Encrypted 的驱动程序安装在客户端计算机上，以便它可以自动加密和解密客户端应用程序中的敏感数据。 该驱动程序在将数据传递到数据库引擎之前对敏感列中的数据进行加密，并且自动重写查询，以便保留应用程序的语义。 同样，该驱动程序透明地解密存储在查询结果中包含的加密数据库列中的数据。

## <a name="access-control"></a>访问控制
为了提供安全性，SQL 数据库会使用按 IP 限制连接的防火墙规则、要求用户证明其身份的身份验证机制，以及只允许用户执行特定操作和访问特定数据的授权机制，来控制访问。

### <a name="database-access"></a>数据库访问

数据保护从控制对数据的访问开始。 托管数据的数据中心管理物理访问，而你可以配置防火墙以管理网络层的安全性。 此外，还可以通过配置用于身份验证的登录信息并定义服务器和数据库角色的权限来控制访问。

在本部分中，我们将讨论：

-   防火墙和防火墙规则
-   身份验证
-   授权

#### <a name="firewall-and-firewall-rules"></a>防火墙和防火墙规则

Microsoft Azure SQL 数据库为 Azure 和其他基于 Internet 的应用程序提供关系数据库服务。 为了保护数据，在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。 有关详细信息，请参阅 [Azure SQL 数据库防火墙规则概述](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。

只能通过 TCP 端口 1433 使用 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)服务。 若要从计算机访问 SQL 数据库，请确保客户端计算机防火墙允许 TCP 端口 1433 上的传出 TCP 通信。 如果其他应用程序不需要，则阻止 TCP 端口 1433 上的入站连接。

#### <a name="authentication"></a>身份验证

SQL 数据库身份验证是指连接到数据库时如何证明用户的身份。 SQL 数据库支持两种类型的身份验证：

-   **SQL 身份验证：**创建逻辑 SQL 实例时会创建单个登录帐户，称为“SQL 数据库订户帐户”。 此帐户通过 [SQL Server 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)（用户名和密码）进行连接。 此帐户为管理员，负责管理逻辑服务器实例以及所有附加到该实例的用户数据库。 不能限制订户帐户的权限。 此类帐户只能存在一个。
-   **Azure Active Directory 身份验证：**[Azure Active Directory 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) 是使用 Azure Active Directory (Azure AD) 中的标识连接到 Microsoft Azure SQL 数据库和 SQL 数据仓库的一种机制。 这使你能够集中管理数据库用户的身份。

![身份验证](./media/azure-databse-security-overview/azure-database-fig2.png)

 Azure Active Directory 身份验证的优点包括：
  - 提供一个 SQL Server 身份验证的替代方法。
  - 它还可帮助阻止跨数据库服务器的用户身份的扩散，并允许在一个地方进行密码轮换。
  - 用户可以使用外部 (Azure Active Directory) 组管理数据库权限。
  - 它可以通过启用集成的 Windows 身份验证和 Azure Active Directory 支持的其他形式的身份验证来消除存储密码。

#### <a name="authorization"></a>授权
[授权](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)是指用户可以在 Azure SQL 数据库中执行哪些操作，这由用户帐户的数据库[角色成员身份](https://msdn.microsoft.com/library/ms189121)和[对象级权限](https://msdn.microsoft.com/library/ms191291.aspx)来控制。 授权是确定主体可以访问哪些安全对象资源以及哪些操作允许用于这些资源的过程。

### <a name="application-access"></a>应用程序访问

在本部分中，我们将讨论：

-   动态数据掩码
-   行级别安全性

#### <a name="dynamic-data-masking"></a>动态数据掩码
呼叫中心服务代表可以根据呼叫者的身份证号或信用卡号的多个数字来识别其身份，但这些数据项不应完全透露给服务代表。

可以定义屏蔽规则，屏蔽任意查询的结果集中任何身份证号或信用卡号除最后四位数以外的其他所有数字。

![动态数据掩码](./media/azure-databse-security-overview/azure-database-fig3.png)

另举一例，通过定义适当的数据屏蔽来保护个人身份信息 (PII) 数据，开发人员一方面可以查询生产环境以进行故障排除，同时又不违反法规遵从性要求。

[SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户模糊化敏感数据来限制此类数据的泄漏。 V12 版的 Azure SQL 数据库支持动态数据屏蔽。

[动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)允许用户指定在对应用层产生最小影响的前提下可以透露的敏感数据量，从而帮助防止未经授权的用户访问敏感数据。 它是一种基于策略的安全功能，会在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。


> [!Note]
> Azure 数据库管理员、服务器管理员或安全主管角色可以配置动态数据屏蔽。

#### <a name="row-level-security"></a>行级安全性
多租户数据库的另一个常见安全要求是[行级安全性](https://msdn.microsoft.com/library/dn765131.aspx)。 此功能使你能够根据执行查询的用户的特征（例如，组成员身份或执行上下文），控制对数据库表中的行的访问。

![行级安全性](./media/azure-databse-security-overview/azure-database-fig4.png)

访问限制逻辑位于数据库层，而不会脱离另一应用程序层中的数据。 每当从任一层尝试访问数据时，数据库系统就会应用访问限制。 这样就会通过减少安全系统的外围应用，使安全系统变得更加可靠和稳健。

行级安全性引入了基于谓词的访问控制。 它具有灵活、集中式、基于谓词的评估，可以考虑元数据或管理员根据需要确定的任何其他标准。 谓词用作判断用户是否对基于用户属性的数据具有适当访问权限的标准。 基于标签的访问控制可以通过使用基于谓词的访问控制来实现。

## <a name="proactive-monitoring"></a>主动监视
SQL 数据库通过提供审核和威胁检测功能来保护数据。

### <a name="auditing"></a>审核
SQL 数据库审核可提高用户深入了解数据库中发生的事件和更改（包括针对数据的更新和查询）的能力。

[Azure SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核可帮助你一直保持符合法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。 审核有助于遵从法规标准，但不能保证遵从法规。

SQL 数据库审核可让你：

-   **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
-   **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
-   **分析**报告。 可以查找可疑事件、异常活动和趋势。

有两种审核方法：

-   **Blob 审核** - 将日志写入 Azure Blob 存储。 这是一种较新的审核方法，可提供更高性能，支持更高粒度的对象级审核，且更具成本效益。
-   **表审核** - 将日志写入 Azure 表存储。

### <a name="threat-detection"></a>威胁检测
[Azure SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)可检测到指示潜在安全威胁的可疑活动。 威胁检测使你能够对数据库中发生的可疑事件进行响应，例如 SQL 注入。 它提供警报，并允许使用 Azure SQL 数据库审核来探查可疑事件。

![威胁检测](./media/azure-databse-security-overview/azure-database-fig5.jpg)

例如，SQL 注入是 Internet 上常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。

发生可疑数据库活动时，安全监管员或其他指定的管理员可以获取相关即时通知。 每个通知提供可疑活动的详细信息，以及如何进一步调查和缓解威胁的建议。        

## <a name="centralized-security-management"></a>集中的安全管理

[Azure 安全中心](https://azure.microsoft.com/documentation/services/security-center/)可帮助防范、检测和应对威胁。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

[安全中心](https://docs.microsoft.com/azure/security-center/security-center-sql-database)通过为所有服务器和数据库提供安全性的可见性来帮助保护 SQL 数据库中的数据。 通过安全中心，可以：

-   定义 SQL 数据库加密和审核的策略。
-   跨所有订阅监视 SQL 数据库资源的安全性。
-   快速识别和修复安全问题。
-   集成来自 [Azure SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)的警报。
-   安全中心支持基于角色的访问。

## <a name="azure-marketplace"></a>Azure 应用商店

Azure Marketplace 是一个在线应用程序和服务市场，初创公司和独立软件供应商 (ISV) 能够通过它为全球 Azure 客户提供解决方案。
Azure Marketplace 与 Microsoft Azure 合作伙伴生态系统结合为一个统一的平台，以便更好地为我们的客户和合作伙伴提供服务。 单击[此处](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1)浏览 Azure Marketplace 中提供的数据库安全产品。

## <a name="next-steps"></a>后续步骤

- 了解有关[保护 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)的详细信息。
- 详细了解 [Azure 安全中心与 Azure SQL 数据库服务](https://docs.microsoft.com/azure/security-center/security-center-sql-database)。
- 若要了解有关威胁检测的详细信息，请参阅 [SQL 数据库威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。
- 若要了解详细信息，请参阅[提高 SQL 数据库性能](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)。 
