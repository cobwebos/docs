---
title: Azure 安全性与合规性蓝图 - 符合 Australia PROTECTED 的 PaaS Web 应用程序
description: Azure 安全性与合规性蓝图 - 符合 Australia PROTECTED 的 PaaS Web 应用程序
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: ea9c9d773cb863c6ee2b052fcbd097275f00fc87
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300981"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Azure 安全性与合规性蓝图 - 符合 Australia PROTECTED 的 PaaS Web 应用程序

## <a name="overview"></a>概述

此 Azure 安全性与合规性蓝图提供有关部署适合用于收集、存储和检索澳大利亚信号局 (ASD) 生成的且符合澳大利亚政府信息安全手册 (ISM) 目标的 AU-PROTECTED 政府数据的平台即服务 (PaaS) 环境的指导。 此蓝图展示了一个通用参考体系结构，有助于演示在安全、合规的多层环境中正确处理敏感政府数据的方式。

此参考体系结构、实施指南和威胁模型为客户采取自己的规划和系统认证过程提供了基础，可帮助客户以遵从 ASD 的方式将工作负荷部署到 Azure 中。 客户可以选择实施 Azure VPN 网关或 ExpressRoute 来使用联合服务，以及将本地资源与 Azure 资源相集成。 客户必须考虑到使用本地资源对安全造成的影响。 必须完成其他配置才能满足所有要求，因为这些要求可能根据每个客户的实施细节而异。

若要符合 ASD 的要求，必须由信息安全注册评估师审核系统。 客户有责任对使用本体系结构构建的任何解决方案进行相应的安全性与符合性评估，因为具体要求可能会因客户的具体实施情况而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此解决方案为具有 Azure SQL 数据库后端的 PaaS Web 应用程序提供参考体系结构。 该 Web 应用程序托管在独立的 Azure 应用服务环境中，该环境是 Azure 数据中心内的私有专用环境。 该环境针对 Azure 管理的虚拟机上的 Web 应用程序流量进行负载均衡。 所有 Web 应用程序连接需要最低版本为 1.2 的 TLS。 此体系结构还包括网络安全组、应用程序网关、Azure DNS 和负载均衡器。

该体系结构提供一个可将本地网络扩展到 Azure，让企业用户通过组织的专用局域网或 Internet 访问基于 Web 的工作负荷的安全混合环境。 对于本地解决方案，客户需负责安全、运营与合规性的方方面面。

此解决方案中的 Azure 资源可以通过 VPN 网关使用 IPSec VPN，以及通过 ExpressRoute 连接到本地网络或数据中心共置设施（例如堪培拉的 CDC）。 在决定是否使用 VPN 时，应该考虑到传输数据的分类和网络路径。 运行大规模任务关键型工作负荷且要满足大数据要求的客户应该考虑混合网络体系结构，使用 ExpressRoute 来与 Azure 服务建立专用网络连接。 有关 Azure 连接机制的详细信息，请参阅[指导和建议](#guidance-and-recommendations)部分。

应使用 Azure Active Directory 的联合身份验证，使用户能够使用本地凭据进行身份验证，并使用本地 Active Directory 联合身份验证服务基础结构来访问云中的所有资源。 Active Directory 联合身份验证服务可为此混合环境提供简化、安全的标识联合身份验证和 Web 单一登录功能。 有关 Azure Active Directory 设置的更多详细信息，请参阅[指导和建议](#guidance-and-recommendations)部分。

该解决方案使用 Azure 存储帐户。客户可将存储帐户配置为使用存储服务加密，以维持静态数据的机密性。 Azure 在客户选择的区域存储三个数据副本，以提供复原能力。 Azure 区域部署在可复原的区域对中，地理冗余的存储可确保将数据复制到同样包含三个副本的第二个区域。 这可以防止客户主要数据位置发生的不利事件导致数据丢失。

为了增强安全性，将通过 Azure 资源管理器以资源组的形式管理此解决方案中的所有 Azure 资源。 Azure Active Directory 基于角色的访问控制用于控制对 Azure Key Vault 中部署的资源和密钥的访问。 通过 Azure 安全中心和 Azure Monitor 监视系统运行状况。 客户配置两个监视服务捕获日志并在单独的、可轻松导航的仪表板中显示系统运行状况。 Azure 应用程序网关配置为防护模式的防火墙，它会禁止非 TLS v1.2 或更高版本的流量。 该解决方案利用 Azure 应用程序服务环境 v2 来隔离非多租户环境中的 Web 层。

![符合 AU-PROTECTED 的 PaaS Web 应用程序参考体系结构](images/au-protected-paaswa-architecture.png?raw=true "符合 AU-PROTECTED 的 PaaS Web 应用程序参考体系结构示意图")

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了更多详细信息。

- 应用程序网关
    - Web 应用程序防火墙
        - 防火墙模式：防护
        - 规则集：OWASP
        - 侦听器端口：443
- Application Insights
- Azure Active Directory
- Azure 应用程序服务环境 v2
- Azure 自动化
- Azure DNS
- Azure 密钥保管库
- Azure 负载均衡器
- Azure Monitor
- Azure 资源管理器
- Azure 安全中心
- Azure SQL 数据库
- Azure 存储
- Azure Log Analytics
- Azure 虚拟网络
    - (1)/16 网络
    - (4)/24 网络
    - 网络安全组
- 网络安全组
- 恢复服务保管库
- Azure Web 应用

此蓝图包含尚未通过澳大利亚网络安全中心 (ACSC) 的“受保护”分类认证的 Azure 服务。 Microsoft 建议客户查看与这些 Azure 服务相关的已发布安全与审核报告，并使用其风险管理框架来确定 Azure 服务是否适合其内部认证以及在“受保护”分类级别使用。

## <a name="deployment-architecture"></a>部署体系结构
以下部分详细描述了部署和实施要素。

**Azure 资源管理器**：客户可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)以组的方式处理解决方案中的资源。 客户可以通过一个协调的操作为解决方案部署、更新或删除所有资源。 客户可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 资源管理器提供安全、审核和标记功能，以帮助客户在部署后管理资源。

**守护主机**：守护主机是允许用户访问此环境中已部署资源的单一入口点。 守护主机通过仅允许来自安全列表上的公共 IP 地址的远程流量来提供到已部署资源的安全连接。 要允许远程桌面 (RDP) 流量，需要在网络安全组中定义流量的源。

此解决方案使用以下配置将虚拟机创建为已加入域的守护主机：
-   [反恶意软件扩展](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure 诊断扩展](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   使用 Azure Key Vault 的 [Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   [自动关闭策略](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，在不使用虚拟机时可减少其资源消耗量
-   已启用 [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard)，以便凭据和其他机密在与正在运行的操作系统隔离的受保护环境中运行

**Azure 应用服务环境 v2**：[Azure 应用服务环境](https://docs.microsoft.com/azure/app-service/environment/intro)是一项 Azure 应用服务功能，可提供完全隔离和专用的环境，以便高度安全地运行应用服务应用程序。

应用服务环境进行了隔离，仅运行单个客户的应用程序，且始终部署到虚拟网络中。 客户对于入站和出站的应用网络流量都有更细微的控制，且应用程序可以通过虚拟网络创建与本地公司资源的高速安全连接。

对此体系结构使用应用服务环境实现了以下控制/配置：

- 必须将应用服务环境配置为使用隔离的服务计划
    - 为不同分类的应用程序配置不同的应用服务环境
- 托管在受保护的 Azure 虚拟网络中并受网络安全规则保护
- 使用自签名内部负载均衡器证书配置的、用于 HTTPS 通信的应用服务环境。 作为最佳做法，Microsoft 建议使用受信任的证书颁发机构来增强安全性。
- [内部负载均衡模式](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)（模式 3）
- 禁用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- 更改 [TLS 密码](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- 控制[入站流量 N/W 端口](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web 应用程序防火墙 - 限制数据](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- 允许 [Azure SQL 数据库流量](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Web 应用**：客户使用 [Azure Web 应用](https://docs.microsoft.com/azure/app-service/)可以采用所选编程语言构建和托管 Web 应用程序，而无需管理基础结构。 它提供自动缩放和高可用性，支持 Windows 和 Linux，并支持从 GitHub、Azure DevOps Services 或任何 Git 存储库进行自动部署。

### <a name="virtual-network"></a>虚拟网络
体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。

**网络安全组**：[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)包含允许或拒绝虚拟网络中的流量的访问控制列表。 网络安全组可用于在子网或单个虚拟机级别保护流量。 存在以下网络安全组：
- 应用程序网关有 1 个网络安全组
- 应用服务环境有 1 个网络安全组
- Azure SQL 数据库有 1 个网络安全组
- 守护主机有 1 个网络安全组

每个网络安全组打开了特定的端口和协议，使解决方案能够安全正确地工作。 此外，为每个网络安全组启用了以下配置：

  - [诊断日志和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中
  - Azure Log Analytics 已连接到[网络安全组的诊断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**子网**：每个子网与其对应的网络安全组相关联。

**Azure DNS**：域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) 是 DNS 域的托管服务，它使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具和账单来管理 DNS 记录。 Azure DNS 还支持 DNS 专用域。

**Azure 负载均衡器**：使用 [Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)，客户可以缩放应用程序，并为服务创建高可用性。 负载均衡器支持入站和出站场景、提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。

### <a name="data-in-transit"></a>传输中的数据
默认情况下，Azure 会加密与 Azure 数据中心之间的所有通信。 

对于从客户自有网络传输的“受保护”数据，该体系结构将使用 Internet，或者将 ExpressRoute 与配置有 IPSEC 的 VPN 网关配合使用。

此外，通过 Azure 管理门户发送到 Azure 的所有事务将使用 TLS v1.2 通过 HTTPS 进行。

### <a name="data-at-rest"></a>静态数据
该体系结构通过加密、数据库审核和其他措施保护静态数据。

**Azure 存储**：为了满足静态数据加密要求，所有 [Azure 存储](https://azure.microsoft.com/services/storage/)均使用[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。 这有助于保护数据，以支持澳大利亚政府 ISM 规定的组织安全承诺与合规性要求。

**Azure 磁盘加密**：[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**Azure SQL 数据库**：Azure SQL 数据库实例使用以下数据库安全措施：
-   使用 [Active Directory 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
-   [SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
-   Azure SQL 数据库配置为使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)可以通过为可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式提供安全警报，在发生潜在威胁时进行检测和响应。 SQL 威胁检测将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包含可疑活动的详细信息以及如何调查和缓解威胁的建议操作。
-   [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码可以自动发现潜在的敏感数据，并建议应用合适的掩码。 这有助于减少访问活动，并避免敏感数据通过未经授权的访问离开数据库。 客户将需要调整动态数据掩码设置，使之遵循其数据库架构。

### <a name="identity-management"></a>身份管理
客户可以使用本地 Active Directory 联合身份验证服务来与 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)（Microsoft 的多租户基于云的目录和标识管理服务）联合。 [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 可将本地目录与 Azure Active Directory 集成。 此解决方案中的所有用户（包括访问 Azure SQL 数据库的用户）需要 Azure Active Directory 帐户。 使用联合登录时，用户可以使用本地凭据登录到 Azure Active Directory 并对 Azure 资源进行身份验证。

此外，以下 Azure Active Directory 功能有助于管理对 Azure 环境中数据的访问：
- 使用 Azure Active Directory 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。 此外，数据库列加密使用 Azure Active Directory 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 Azure SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
- [Azure 基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问数据。 订阅访问仅限于订阅管理员。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) 使客户能够最大限度地减少有权访问特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。

**Azure 多重身份验证**：为保护标识，应实施多重身份验证。 [Azure 多重身份验证](https://azure.microsoft.com/services/multi-factor-authentication/)是一种易于使用、可缩放且可靠的解决方案，用于提供第二种身份验证方法来保护用户。 Azure 多重身份验证使用云的强大功能，并与本地 Active Directory 和自定义应用相集成。 这种保护可以延伸到高事务量的任务关键型方案。

### <a name="security"></a>安全
**机密管理**：解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护数据：
- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块保护的 2048 位 RSA 密钥。
- 使用基于角色的访问控制向所有用户和标识授予了最低必需权限。
- 启用 Key Vault 的诊断日志时，保留期设置为至少 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

**Azure 安全中心**：借助 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，客户可在工作负荷中集中应用和管理安全策略、限制威胁曝露，以及检测和应对攻击。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组[预定义的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的[自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，根据从环境中收集到的数据定义新的安全警报。

Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)，帮助事件响应团队调查和解决威胁。

**应用程序网关**：体系结构在启用 Web 应用程序防火墙和 OWASP 规则集的情况下使用应用程序网关，可降低安全漏洞风险。 其他功能包括：

- [端到端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 启用 [SSL 卸载](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- 禁用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 应用程序防火墙](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)（预防模式）
- 结合 OWASP 3.0 规则集的[保护模式](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- 启用[诊断日志记录](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [自定义运行状况探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure 安全中心](https://azure.microsoft.com/services/security-center)和 [Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations)提供额外的保护和通知。 Azure 安全中心还提供信誉系统。

### <a name="logging-and-auditing"></a>日志记录和审核

Azure 服务广泛记录系统和用户活动以及系统运行状况：
- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入集中式加密 Azure 存储帐户进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。

**Log Analytics**：这些日志将整合到 [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 中进行处理、存储和在仪表板上报告。 收集数据后，数据将按数据类型组织到不同的表中，这样就可以对所有数据进行集中分析，不管其最初来源是什么。 此外，Azure 安全中心与 Log Analytics 集成，使客户能够使用 Log Analytics 查询来访问其安全事件数据，并将这些数据与其他服务中的数据合并。

以下 Log Analytics [管理解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)是此体系结构的一部分：
-   [Active Directory 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署服务器基础结构的优先建议列表。
- [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署服务器基础结构的优先建议列表。
- [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户所有 Azure 订阅的 Azure 活动日志。

**Azure 自动化**：[Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)可以存储、运行和管理 Runbook。 在此解决方案中，Runbook 可帮助从 Azure SQL 数据库收集日志。 自动化[更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)解决方案使得客户能够轻松识别环境中的更改。

**Azure Monitor**：[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

Azure 网络观察程序： [Azure 网络观察程序]9 https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview) 提供所需的工具用于监视、诊断 Azure 虚拟网络中的资源、查看其指标，以及为其启用或禁用日志。  英联邦实体应为 NSG 和虚拟机实施网络观察程序流日志。 这些日志只能存储在专门用于存储安全日志的存储帐户中，对该存储帐户的访问应受基于角色的访问控制的保护。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/au-protected-paaswa-tm)，也可以在下面找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![符合 AU-PROTECTED 的 PaaS Web 应用程序威胁模型](images/au-protected-paaswa-threat-model.png?raw=true "符合 AU-PROTECTED 的 PaaS Web 应用程序威胁模型示意图")

## <a name="compliance-documentation"></a>符合性文档
此合规性文档由 Microsoft 根据其提供的平台和服务生成。 由于客户部署的范围非常广泛，本文档仅针对 Azure 环境中托管的解决方案提供了通用方法。 客户可以根据自己的操作系统环境和业务成果，确定并使用替代的产品和服务。 选择使用本地资源的客户必须解决这些本地资源的安全和运营要求。 客户可以定制所述的解决方案，以解决具体的本地部署与安全要求。

[Azure 安全性与合规性蓝图 - AU-PROTECTED 客户责任矩阵](https://aka.ms/au-protected-crm)列出了 AU-Prot 要求的所有安全控制措施。 此矩阵详细说明了每项控制措施的实施是由 Microsoft、客户还是两者共同负责。

[Azure 安全性与合规性蓝图 - AU-PROTECTED PaaS Web 应用程序实施矩阵](https://aka.ms/au-protected-paaswa-cim)提供了有关 PaaS Web 应用程序体系结构解决哪些 AU-PROTECTED 措施控制的信息，包括实施方案如何满足每个所述控制措施的要求的详细说明。

## <a name="guidance-and-recommendations"></a>指导和建议
### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute

对于分类的信息，需要配置安全 IPSec VPN 隧道，以便与特定资源建立安全连接，这些资源是作为此 IaaS Web 应用程序参考体系结构的一部分部署的。 通过适当设置 IPSec VPN，客户可以在传输过程中为数据增加一个保护层。

在 Azure 中实施安全 IPSec VPN 隧道，可在本地网络与 Azure 虚拟网络之间建立虚拟专用连接。 此连接可通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过“隧道”安全地传输信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了专用连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间的专用链接，被认为是一种专用网络。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快且延迟时间更短。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。 

为了帮助保护分类的数据（不管是使用 Internet 还是 Azure ExpressRoute），客户必须通过应用以下设置来配置其 IPSec VPN：

•   必须为客户 VPN 发起方配置不超过 14400 秒的 SA 生存期。
•   客户 VPN 发起方必须禁用 IKEv1 主动模式。
•   客户 VPN 发起方必须配置“完全向前保密”。
•   客户 VPN 发起方必须配置为使用 HMAC-SHA256 或更强的加密。

请查看[此处](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)提供的 VPN 设备配置选项和 IPSec/ IKE 参数。

### <a name="azure-active-directory-setup"></a>Azure Active Directory 设置
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 对于管理部署以及预配与环境交互的人员的访问至关重要。 只需[单击四下](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，即可将现有 Windows Server Active Directory 与 Azure Active Directory 集成。

此外，[Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 可让客户配置与本地 [Active Directory 联合身份验证服务]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs)和 Azure Active Directory 的联合。 通过联合登录，客户可让用户在企业网络中使用本地密码登录到基于 Azure Active Directory 的服务，而无需再次输入密码。 使用 Active Directory 联合身份验证服务的联合选项，可以部署 Active Directory 联合身份验证服务的新安装，或者在 Windows Server 2012 R2 场中指定现有安装。

若要防止分类数据同步到 Azure Active Directory，客户可以通过在 Azure Active Directory Connect 中应用以下设置，来限制要复制到 Azure Active Directory 的属性：

- [启用筛选](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [禁用密码哈希同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [禁用密码写回](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [禁用设备写回](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   保留[防止意外删除](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes)和[自动升级](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)的默认设置


## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
