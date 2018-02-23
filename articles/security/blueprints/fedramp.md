---
title: "Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化"
description: "Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化"
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
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 9b605e500925e8435b15ec8055f8d8f376888aaf
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2018
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化

## <a name="overview"></a>概述

[联邦风险与授权管理计划 (FedRAMP)](https://www.fedramp.gov) 是美国政府范围的计划，它提供一种标准化方法来对云产品和服务进行安全评估、授权和持续监视。 此 Azure 安全性和符合性蓝图自动化提供的指南适用于部署 FedRAMP 相容基础结构即服务 (IaaS) 环境，该环境适合简单的面向 Internet 的 Web 应用程序。 此解决方案自动完成适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全和符合性要求，是客户在 Azure 上生成和配置其解决方案的基础。 此解决方案实施的一部分控制来自 FedRAMP High 基线（基于 NIST SP 800-53）。 有关 FedRAMP High 要求和此解决方案的详细信息，请参阅 [FedRAMP High 要求 - 综合概述](fedramp-controls-overview.md)。 注意：此解决方案部署到 Azure 政府。

本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。 在未经修改的情况下将应用程序直接部署到此环境并不足以完全符合 FedRAMP High 基线的要求。 请注意以下事项：
- 本体系结构提供一个基线来帮助客户以遵从 FedRAMP 的方式使用 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。 

有关本解决方案工作原理的简要概述，请观看[此视频](https://aka.ms/fedrampblueprintvideo)，其中解释并演示了其部署。

单击[此处](https://aka.ms/fedrampblueprintrepo)获取部署说明。

## <a name="solution-components"></a>解决方案组件

此 Azure 安全性和符合性蓝图自动化将自动部署带有预配置安全控件的 IaaS Web 应用程序参考体系结构，以帮助客户符合 FedRAMP 要求。 此解决方案包含 Azure 资源管理器模板和 PowerShell 脚本，指导用户进行资源部署和配置。 提供了伴随的[符合性文档](#compliance-documentation)。该文档指出，安全控件源自 Azure 以及符合 NIST SP 800-53 安全控件标准的已部署资源和配置，因此可以让组织快速跟踪其符合性情况。

## <a name="architecture-diagram"></a>体系结构关系图

此解决方案为带有数据库后端的 IaaS Web 应用程序部署参考体系结构。 体系结构包括 Web 层、数据层、Active Directory 基础结构、应用程序网关和负载均衡器。 部署到 Web 层和数据层的虚拟机在可用性集中配置，SQL Server 实例在 AlwaysOn 可用性组中配置，以确保高可用性。 在将虚拟机加入域的同时，使用 Active Directory 组策略在操作系统级别强制实施安全性和符合性配置。 可以通过管理 jumpbox（守护主机）进行安全的连接，以便管理员访问部署的资源。

![替换文字](images/fedramp-architectural-diagram.png?raw=true "Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化")

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

* **Azure 虚拟机**
    - (1) 管理/守护 (Windows Server 2016 Datacenter)
    - (2) Active Directory 域控制器 (Windows Server 2016 Datacenter)
    - (2) SQL Server 群集节点（Windows Server 2012 R2 上的 SQL Server 2016）
    - (1) SQL Server 见证服务器 (Windows Server 2016 Datacenter)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
* **可用性集**
    - (1) Active Directory 域控制器
    - (1) SQL 群集节点和见证服务器
    - (1) Web/IIS
* **Azure 虚拟网络**
    - (1) /16 虚拟网络
    - (5) /24 子网
    - DNS 设置设为两个域控制器
* **Azure 负载均衡器**
    - (1) SQL 负载均衡器
* **Azure 应用程序网关**
    - (1) WAF 应用程序网关已启用
      - 防火墙模式：防护
      - 规则集：OWASP 3.0
      - 侦听器：端口 443
* **Azure 存储**
    - (7) 异地冗余存储帐户
* **Azure 备份**
    - (1) 恢复服务保管库
* **Azure Key Vault**
    - (1) Key Vault
* **Azure Active Directory**
* **Azure 资源管理器**
* **Azure Log Analytics**
* **Azure 自动化**
    - (1) 自动化帐户
* **Operations Management Suite**
    - (1) OMS 工作区

## <a name="deployment-architecture"></a>部署体系结构

以下部分详细描述了开发和实施要素。

### <a name="network-segmentation-and-security"></a>网络分段和安全

#### <a name="application-gateway"></a>应用程序网关

体系结构在启用 Web 应用程序防火墙 (WAF) 和 OWASP 规则集的情况下使用应用程序网关，可降低安全漏洞风险。 其他功能包括：

- [端到端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 启用 [SSL 卸载](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- 禁用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 应用程序防火墙](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)（WAF 模式）
- 结合 OWASP 3.0 规则集的[保护模式](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

#### <a name="virtual-network"></a>虚拟网络

体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。

#### <a name="network-security-groups"></a>网络安全组

此解决方案在一个体系结构中部署资源，在虚拟网络中包含独立的 Web 子网、数据库子网、Active Directory 子网和管理子网。 按照应用于单个子网的网络安全组规则，以逻辑方式分离子网来限制子网之间的流量，只提供系统和管理功能所需的流量。

请参阅此解决方案中部署的[网络安全组](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)的配置。 组织可以根据[此文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)中的说明编辑上面的文件，对网络安全组进行配置。

每个子网有一个专用的网络安全组 (NSG)：
- 1 个 NSG 用于应用程序网关 (LBNSG)
- 1 个 NSG 用于 Jumpbox (MGTNSG)
- 1 个 NSG 用于主要的和备份的域控制器 (ADNSG)
- 1 NSG 用于 SQL Server 和文件共享见证 (SQLNSG)
- 1 个 NSG 用于 Web 层 (WEBNSG)

#### <a name="subnets"></a>子网

每个子网与其相应的 NSG 关联。

### <a name="data-at-rest"></a>静态数据

体系结构使用多种加密措施保护静态数据。

#### <a name="azure-storage"></a>Azure 存储

为了满足静态数据加密要求，所有存储帐户都使用[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

#### <a name="sql-database"></a>SQL 数据库

SQL 数据库配置为使用[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)，它执行数据和日志文件的实时加密和解密，以保护静态信息。 TDE 可确保存储的数据免遭他人未经授权的访问。 

#### <a name="azure-disk-encryption"></a>Azure 磁盘加密

Azure 磁盘加密用于加密 Windows IaaS 虚拟机磁盘。 [Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能，为 OS 和数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，用于控制和管理磁盘加密密钥。

### <a name="logging-and-auditing"></a>日志记录和审核

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 可广泛记录系统和用户活动以及系统运行状况。 

- **活动日志：**[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。
- **诊断日志：**[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志，以及应用程序网关访问和防火墙日志。
- **日志存档：**Azure 活动日志和诊断日志可以连接到 Azure Log Analytics 进行处理、存储和仪表板操作。 保留期允许用户进行配置，最长为 730 天，具体取决于组织的保留期要求。

### <a name="secrets-management"></a>机密管理

本解决方案使用 Azure Key Vault 来管理密钥和机密。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 有助于保护云应用程序和服务使用的加密密钥和机密。 
- 此解决方案与 Azure Key Vault 集成，用于管理 IaaS 虚拟机磁盘加密密钥和机密。

### <a name="identity-management"></a>身份管理

以下技术在 Azure 环境中提供标识管理功能。
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。
- 可以使用 Azure AD 向客户部署的 Web 应用程序进行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。  
- [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 可在 Azure 中实现极有针对性的访问管理。 仅限订阅管理员进行订阅访问，而资源访问则取决于用户角色。
- 可以通过部署的 IaaS Active Directory 实例在 OS 级别对部署的 IaaS 虚拟机进行标识管理。
   
### <a name="compute-resources"></a>计算资源

#### <a name="web-tier"></a>Web 层

解决方案在[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中部署 Web 层虚拟机。 可用性集可确保虚拟机能够跨多个隔离的硬件群集分布，从而改进可用性。

#### <a name="database-tier"></a>数据库层

此解决方案将可用性集中的数据库层虚拟机部署为 [AlwaysOn 可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。 AlwaysOn 可用性组功能有助于实现高可用性和灾难恢复功能。 

#### <a name="active-directory"></a>Active Directory

在将解决方案部署的所有虚拟机加入域的同时，使用 Active Directory 组策略在操作系统级别强制实施安全性和符合性配置。 Active Directory 虚拟机部署在可用性集中。


#### <a name="jumpbox-bastion-host"></a>Jumpbox（守护主机）

可以通过管理 jumpbox（守护主机）进行安全的连接，以便管理员访问部署的资源。 使用与 jumpbox 虚拟机所在的管理子网关联的 NSG 时，只能在适用于 RDP 的 TCP 端口 3389 上进行连接。 

### <a name="malware-protection"></a>恶意软件防护

用于虚拟机的 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)提供实时保护功能，当已知的恶意软件或不需要的软件试图在受保护的虚拟机上进行安装或运行时，它使用可配置的警报帮助识别和删除病毒、间谍软件和其他恶意软件。

### <a name="patch-management"></a>修补程序管理

通过此 Azure 安全性和符合性蓝图自动化部署的 Windows 虚拟机默认配置为从 Windows 更新服务接收自动更新。 另外，此解决方案还部署 OMS Azure 自动化解决方案，通过此方案可以创建更新部署，以便在需要时将修补程序部署到 Windows 服务器上。

### <a name="operations-management"></a>操作管理

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是 Operations Management Suite (OMS) 中的一个服务，用于收集和分析 Azure 环境与本地环境中资源生成的数据。

#### <a name="oms-solutions"></a>OMS 解决方案

此解决方案中已预装以下 OMS 解决方案：
- [AD 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [反恶意软件评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [安全和审核](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [更新管理](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Azure 活动日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [更改跟踪](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>符合性文档

### <a name="customer-responsibility-matrix"></a>客户责任表

[客户责任表](https://aka.ms/blueprinthighcrm)（Excel 工作簿）列出了 FedRAMP High 基线需要的所有安全控件。 对于每个控件（或控件子部分），该表明确了控件实现是否为 Microsoft 和/或客户的责任。 

### <a name="control-implementation-matrix"></a>控件实现表

[控件实现表](https://aka.ms/blueprintwacim)（Excel 工作簿）列出了 FedRAMP High 基线需要的所有安全控件。 对于每个在客户责任表中指定了客户责任的控件（或控件子部分），该表明确了以下事项：1) 蓝图自动化是否实现该控件；2) 说明了该实现在符合控件要求方面做得如何。 [此处](fedramp-controls-overview.md)也提供了这些内容。

## <a name="deploy-the-solution"></a>部署解决方案

此 Azure 安全性和符合性蓝图自动化包含的 JSON 配置文件和 PowerShell 脚本可供 Azure 资源管理器的 API 服务用来在 Azure 中部署资源。 [此处](https://aka.ms/fedrampblueprintrepo)提供了详细的部署说明。 注意：此解决方案部署到 Azure 政府。

#### <a name="quickstart"></a>快速入门
1. 将[此](https://aka.ms/fedrampblueprintrepo) GitHub 存储库克隆或下载到本地工作站。

2. 运行预部署 PowerShell 脚本：azure-blueprint/predeploy/Orchestration_InitialSetup.ps1。

3. 单击下面的按钮，登录到 Azure 门户，输入所需的 ARM 模板参数，然后单击“购买”。

    [![部署到 Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>免责声明

- 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。  
- 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。  
- 客户可复制本文档，将其用于内部参考。  
- 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。  
- 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
- 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
