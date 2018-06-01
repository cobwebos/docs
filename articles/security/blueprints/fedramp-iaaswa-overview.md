---
title: Azure 安全性和符合性蓝图 - 适用于 FedRAMP 的 Web 应用程序
description: Azure 安全性和符合性蓝图 - 适用于 FedRAMP 的 Web 应用程序
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: b7a81db6a1caf11ac4a85a5202c5ed943225e849
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941929"
---
# <a name="azure-security-and-compliance-blueprint-web-application-for-fedramp"></a>Azure 安全性和符合性蓝图：适用于 FedRAMP 的 Web 应用程序

## <a name="overview"></a>概述

[联邦风险与授权管理计划 (FedRAMP)](https://www.fedramp.gov) 是美国政府范围的计划，它提供一种标准化方法来对云产品和服务进行安全评估、授权和持续监视。 此 Azure 安全性和符合性蓝图自动化提供的指南适用于部署 FedRAMP 相容基础结构即服务 (IaaS) 环境，该环境适合简单的面向 Internet 的 Web 应用程序。 此解决方案自动完成适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全和符合性要求，是客户在 Azure 上生成和配置其解决方案的基础。 此解决方案实施的一部分控制来自 FedRAMP High 基线（基于 NIST SP 800-53）。 有关 FedRAMP 要求和此解决方案的详细信息，请参阅[符合性文档](#compliance-documentation)。
> [!NOTE]
> 此解决方案部署到 Azure 政府。

此 Azure 安全性和符合性蓝图自动化将自动部署带有预配置安全控件的 IaaS Web 应用程序参考体系结构，以帮助客户符合 FedRAMP 要求。 此解决方案包含 Azure 资源管理器模板和 PowerShell 脚本，指导用户进行资源部署和配置。

本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。 在未经修改的情况下将应用程序直接部署到此环境并不足以完全符合 FedRAMP High 基线的要求。 请注意以下事项：
- 本体系结构提供一个基线来帮助客户以遵从 FedRAMP 的方式使用 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。

有关本解决方案工作原理的简要概述，请观看[此视频](https://aka.ms/fedrampblueprintvideo)，其中解释并演示了其部署。

单击[此处](https://aka.ms/fedrampblueprintrepo)获取部署说明。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此解决方案为带有 SQL Server 后端的 IaaS Web 应用程序部署参考体系结构。 体系结构包括 Web 层、数据层、Active Directory 基础结构、应用程序网关和负载均衡器。 部署到 Web 层和数据层的虚拟机在可用性集中配置，SQL Server 实例在 AlwaysOn 可用性组中配置，以确保高可用性。 在将虚拟机加入域的同时，使用 Active Directory 组策略在操作系统级别强制实施安全性和符合性配置。 可以通过守护主机进行安全的连接，以便管理员访问部署的资源。 **Azure 建议配置 VPN 或 Azure ExpressRoute 连接，以便进行管理和将数据导入参考体系结构子网。**

![符合 FedRAMP 的 IaaS Web 应用程序的参考体系结构关系图](images/fedramp-iaaswa-architecture.png?raw=true "符合 FedRAMP 的 IaaS Web 应用程序的参考体系结构关系图")

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

- Azure 虚拟机
    - (1) 守护主机 (Windows Server 2016 Datacenter)
    - (2) Active Directory 域控制器 (Windows Server 2016 Datacenter)
    - (2) SQL Server 群集节点（Windows Server 2016 上的 SQL Server 2017）
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- 可用性集
    - (1) Active Directory 域控制器
    - (1) SQL 群集节点
    - (1) Web/IIS
- Azure 虚拟网络
    - (1) /16 虚拟网络
    - (5) /24 子网
    - DNS 设置设为两个域控制器
- Azure 负载均衡器
- Azure 应用程序网关
    - (1) WAF 应用程序网关已启用
        - 防火墙模式：防护
        - 规则集：OWASP 3.0
        - 侦听器：端口 443
- Azure 存储
    - (7) 异地冗余存储帐户
- Azure 云见证
- 恢复服务保管库
- Azure 密钥保管库
- Azure Active Directory (Azure AD)
- Azure 资源管理器
- Operations Management Suite (OMS)
- Azure Monitor

## <a name="deployment-architecture"></a>部署体系结构

以下部分详细描述了开发和实施要素。

**守护主机**：守护主机是一个单一入口点，可以通过安全连接让管理员访问部署的资源。 守护主机的 NSG 只允许在适用于 RDP 的 TCP 端口 3389 上进行连接。 客户可以进一步配置守护主机，使之符合组织系统强化要求。

### <a name="virtual-network"></a>虚拟网络
体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。

**网络安全组**：此解决方案在一个体系结构中部署资源，在虚拟网络中包含独立的 Web 子网、数据库子网、Active Directory 子网和管理子网。 子网从逻辑上是以应用于各个子网的网络安全组规则进行分隔，以限制子网之间只能有系统和管理功能所必需的流量。

请参阅此解决方案中部署的[网络安全组](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)的配置。 客户可以根据[此文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)中的说明编辑上面的文件，对网络安全组进行配置。

每个子网有一个专用的网络安全组 (NSG)：
- 1 个 NSG 用于应用程序网关 (LBNSG)
- 1 个 NSG 用于守护主机 (MGTNSG)
- 1 个 NSG 用于主要的和备份的域控制器 (ADNSG)
- 1 个 NSG 用于 SQL Server (SQLNSG)
- 1 个 NSG 用于 Web 层 (WEBNSG)

**子网**：每个子网都与其相应的 NSG 相关联。

### <a name="data-at-rest"></a>静态数据

体系结构使用多种加密措施保护静态数据。

**Azure 存储**：为了满足静态数据加密要求，所有存储帐户都使用[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

**SQL Server**：SQL Server 配置为使用[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)，实时加密和解密数据和日志文件，以保护静态信息。 TDE 可确保存储的数据免遭他人未经授权的访问。

客户也可配置以下 SQL Server 安全措施：
-   使用 [AD 身份验证和授权](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
-   [SQL 数据库审核](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
-   在授予适当的权限之前，[防火墙规则](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [SQL 威胁检测](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection-get-started)可以通过为可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式提供安全警报，在发生潜在威胁时进行检测和响应。
-   [Always Encrypted 列](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用服务器才能访问明文数据。
-   参考体系结构部署后可以完成 [SQL 数据库动态数据掩码](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started)。 客户将需要调整动态数据掩码设置，使之遵循其数据库架构。

**Azure 磁盘加密**：Azure 磁盘加密用于加密 Windows IaaS 虚拟机磁盘。 [Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能，为 OS 和数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，用于控制和管理磁盘加密密钥。

### <a name="identity-management"></a>身份管理

以下技术在 Azure 环境中提供标识管理功能：
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。
- 可以使用 Azure AD 向客户部署的 Web 应用程序进行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。  
- [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 可在 Azure 中实现极有针对性的访问管理。 仅限订阅管理员进行订阅访问，而资源访问则取决于用户角色。
- 可以通过部署的 IaaS Active Directory 实例在 OS 级别对部署的 IaaS 虚拟机进行标识管理。

### <a name="security"></a>安全
**机密管理**：此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 Azure Key Vault 用于管理适用于此参考体系结构的 IaaS 虚拟机磁盘加密密钥和机密。

**修补程序管理**：通过此 Azure 安全性和符合性蓝图自动化部署的 Windows 虚拟机默认配置为从 Windows 更新服务接收自动更新。 另外，此解决方案还部署 Azure 自动化解决方案，通过此方案可以创建更新部署，以便在需要时将修补程序部署到 Windows 服务器上。

**恶意软件防护**：用于虚拟机的 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)提供实时保护功能，当已知的恶意软件或不需要的软件试图在受保护的虚拟机上进行安装或运行时，它使用可配置的警报帮助识别和删除病毒、间谍软件和其他恶意软件。

**应用程序网关**：体系结构在启用 Web 应用程序防火墙 (WAF) 和 OWASP 规则集的情况下使用应用程序网关，可降低安全漏洞风险。 其他功能包括：

- [端到端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 启用 [SSL 卸载](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- 禁用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 应用程序防火墙](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)（WAF 模式）
- 结合 OWASP 3.0 规则集的[保护模式](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="business-continuity"></a>业务连续性

**高可用性**：计划内或计划外维护活动期间，至少有一台虚拟机可用，满足 99.95% Azure SLA。 解决方案在一个[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中部署所有 Web 层和数据层虚拟机。 可用性集可确保虚拟机能够跨多个隔离的硬件群集分布，从而改进可用性。 另外，此解决方案将可用性集中的 SQL Server 虚拟机部署为 [AlwaysOn 可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。 AlwaysOn 可用性组功能有助于实现高可用性和灾难恢复功能。

**恢复服务保管库**：[恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)存储备份数据并保护此体系结构中的所有 Azure 虚拟机配置。 通过恢复服务保管库，客户可以从 IaaS VM 还原文件和文件夹，而无需还原整个 VM，从而实现更快的还原时间。

**云见证**：[云见证](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)是在 Windows Server 2016 中利用 Azure 作为仲裁点的一种故障转移群集仲裁见证。 与其他任何仲裁见证一样，云见证可获得投票并可参与仲裁计算，但它使用标准公开可用的 Azure Blob 存储。 这消除了在公有云中托管的 VM 的额外维护开销。

### <a name="logging-and-auditing"></a>日志记录和审核

OMS 可广泛记录系统和用户活动以及系统运行状况。 OMS [Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 解决方案收集和分析 Azure 和本地环境中的资源生成的数据。

- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志：**[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志，以及应用程序网关访问和防火墙日志。
- **日志存档：** 所有诊断日志都将写入集中式加密 Azure 存储帐户进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。 这些日志连接到 Azure Log Analytics 进行处理、存储和仪表板报告。

另外，以下 OMS 解决方案作为此体系结构的一部分进行安装。 请注意，客户负责配置这些解决方案，使之符合 FedRAMP 安全控制的要求：
-   [AD 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案定期评估服务器环境的风险和运行状况，并且提供特定于部署服务器基础结构的优先建议列表。
-   [反恶意软件评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)：反恶意软件解决方案报告恶意软件、威胁和防护状态。
-   [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)：Azure 自动化解决方案存储、运行和管理 Runbook。
-   [安全和审核](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)：“安全和审核”仪表板通过提供有关安全域、值得注意的问题、检测、威胁智能和常见安全性查询的指标，提供对资源安全状态的高级见解。
-   [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案定期评估服务器环境的风险和运行状况，并且为客户提供特定于已部署服务器基础结构的优先建议列表。
-   [更新管理](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)：“更新管理”解决方案允许客户管理操作系统安全更新，包括可用更新的状态以及安装所需更新的过程。
-   [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：“代理运行状况”解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
-   [Azure 活动日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。
-   [更改跟踪](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：“更改跟踪”解决方案使得客户能够轻松识别环境中的更改。

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在客户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

## <a name="threat-model"></a>威胁模型
此参考体系结构的数据流图可供[下载](https://aka.ms/fedrampWAdfd)，也可以在下面找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![符合 FedRAMP 的 IaaS Web 应用程序的威胁模型](images/fedramp-iaaswa-threat-model.png?raw=true "符合 FedRAMP 的 IaaS Web 应用程序的威胁模型")

## <a name="compliance-documentation"></a>符合性文档

[Azure 安全性和符合性蓝图 - FedRAMP 高客户责任表](https://aka.ms/blueprinthighcrm)列出 FedRAMP 高基线所需的所有安全性控件。 此表说明每个控件的实现是 Microsoft 的责任还是客户的责任，亦或是两者共同的责任。

[Azure 安全性和符合性蓝图 - FedRAMP IaaS Web 应用程序高控制实现表](https://aka.ms/blueprintwacim)列出 FedRAMP 高基线所需的所有安全性控件。 此表说明了 IaaS Web 应用程序体系结构涵盖哪些控制，尤其是详细说明了该实现如何满足每个涵盖的控制的要求。

## <a name="deploy-the-solution"></a>部署解决方案

此 Azure 安全性和符合性蓝图自动化包含的 JSON 配置文件和 PowerShell 脚本可供 Azure 资源管理器的 API 服务用来在 Azure 中部署资源。 [此处](https://aka.ms/fedrampblueprintrepo)提供了详细的部署说明。
> [!NOTE]
> 此解决方案部署到 Azure 政府。

#### <a name="quickstart"></a>快速入门
1. 将[此](https://aka.ms/fedrampblueprintrepo) GitHub 存储库克隆或下载到本地工作站。

2. 运行预部署 PowerShell 脚本：azure-blueprint/predeploy/Orchestration_InitialSetup.ps1。

3. 单击下面的按钮，登录到 Azure 门户，输入所需的 ARM 模板参数，然后单击“购买”。

    [![部署到 Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>指导和建议
### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
需要配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以便安全地建立与特定资源的连接，这些资源是作为此 IaaS Web 应用程序参考体系结构的一部分部署的。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过“隧道”安全地传输信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 此选项使用 [IPsec 隧道模式](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 由于 ExpressRoute 连接不通过 Internet，因此与通过 Internet 的典型连接相比，这些连接提供更高的可靠性、更快的速度、更低的延迟以及更高的安全性。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免责声明

- 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。  
- 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。  
- 客户可复制本文档，将其用于内部参考。  
- 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。  
- 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
- 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
