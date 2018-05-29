---
title: Azure 安全性和符合性蓝图 - FFIEC 金融服务管控的工作负荷
description: Azure 安全性和符合性蓝图 - FFIEC 金融服务管控的工作负荷
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: jomolesk
ms.openlocfilehash: f1339af22132d19f14ea8ebb72fe0e6bd45b7fad
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895548"
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Azure 安全性和符合性蓝图 - FFIEC 金融服务管控的工作负荷

## <a name="overview"></a>概述

Azure 安全性和符合性蓝图 - FFIEC 金融服务管控的工作负荷可帮助部署安全且合规的平台即服务 (PaaS) Web 应用程序，旨在处理云中的敏感数据。 该蓝图包括自动化脚本，以及展示一个简单参考体系结构和设计的指南，可帮助简化 Microsoft Azure 解决方案的采用。 此蓝图演示一个端到端解决方案，寻求减轻在云中部署负担和成本的组织可以利用此解决方案解决需求。

此蓝图旨在满足美国注册会计师协会规定的严格法规标准的要求，例如 - SOC 1、SOC 2、支付卡行业数据安全标准委员会制定的 DSS 3.2，以及有关收集、存储和检索敏感金融数据的 FFIEC。 其中演示了如何通过部署一个可以在安全、合规的多层环境中管理金融数据的解决方案，来适当处理此类数据。 该解决方案部署为端到端的基于 Azure 的 PaaS 解决方案。 

该蓝图旨在用作客户生成解决方案的基础，并让他们了解在云中管理安全数据的要求。 不应在生产部署中按原样使用该解决方案，而应该将它用于了解、设计和部署 Azure 服务；它旨在充当基准，帮助客户以安全合规的方式使用 Microsoft Azure。

认证审核员必须验证基于该蓝图的任何客户生产解决方案。 解决方案可能根据每个客户的具体实施方案和地理位置而有所不同。

有关此解决方案工作原理的简要概述，请观看[此视频](https://aka.ms/fsiblueprintvideo)，其中解释并演示了其部署。

## <a name="solution-components"></a>解决方案组件

该体系结构由以下组件构成，使用从 Azure PCI DSS 法规遵从解决方案中的部署功能。

- **体系结构图**。 该图显示了用于Contoso Webstore 解决方案的参考体系结构。
- **部署模板**。 在此部署中，[Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)通过在安装期间指定配置参数，将体系结构的组件自动部署到 Microsoft Azure。
- **自动化部署脚本**。 这些脚本帮助部署端到端解决方案。 脚本包括：
    - 一个模块安装和[全局管理员](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)设置脚本，用于安装所需的 PowerShell 模块，并验证是否已正确配置全局管理员角色。 
    - 一个用于部署端到端解决方案的 PowerShell 安装脚本，通过一个 .zip 文件和一个 .bacpac 文件提供，其中包含预建的演示 Web 应用程序和 [SQL 数据库示例](https://github.com/Microsoft/azure-sql-security-sample)。 内容。 可在 [付款处理蓝图代码存储库][代码存储库] 中查看此解决方案的源代码。 

## <a name="architectural-diagram"></a>体系结构图

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>用户方案

该蓝图可解决以下用例需求。

> 此方案演示某家虚构的网络商店如何将敏感数据转移到基于 Azure 的 PaaS 云解决方案。 示例解决方案演示了如何处理和收集基本用户信息和选定的敏感数据。 这项工作利用 Azure 安全性和符合性蓝图 - 符合 PCI DSS 的付款处理环境。 有关详细信息，有关此项工作的扩展，请参阅[“实施评论和指南”](https://aka.ms/pciblueprintprocessingoverview)一文，其中提供了符合 PCI DSS 的环境的评论。

### <a name="use-case"></a>使用案例
名为 *Contoso Webstore* 的小型网络商店已准备好将包含客户付款信息的金融数据移到云中。 

Contoso Webstore 的管理员正在寻求一种可以快速部署的解决方案，以实现其目标。 他将使用此概念证明 (POC) 来与他的利益干系人讨论如何根据严格的法规要求，使用 Azure 来收集、存储和检索金融数据。

> 你要负责针对通过此 POC 所用体系结构构建的任何解决方案开展相应的安全与合规评审；具体要求根据实施方式和地理位置的具体情况而异。 

### <a name="elements-of-the-foundational-architecture"></a>基础结构的要素

基础体系结构中设计了以下虚构要素：

域站点 `contosowebstore.com`

用于演示用例以及深入了解用户界面的用户角色。

#### <a name="role-site-and-subscription-admin"></a>角色：站点和订阅管理员

|Item      |示例|
|----------|------|
|用户名： |`adminXX@contosowebstore.com`|
| 姓名： |`Global Admin Azure PCI Samples`|
|用户类型：| `Subscription Administrator and Azure Active Directory Global Administrator`|

- 管理员帐户无法读取未掩码的金融信息。 记录所有操作。
- 管理员帐户无法管理或登录 SQL 数据库。
- 管理员帐户可以管理 Active Directory 和订阅。

#### <a name="role-sql-administrator"></a>角色：SQL 管理员

|Item      |示例|
|----------|------|
|用户名： |`sqlAdmin@contosowebstore.com`|
| 姓名： |`SQLADAdministrator PCI Samples`|
| 名字： |`SQL AD Administrator`|
|姓氏： |`PCI Samples`|
|用户类型：| `Administrator`|

- sqladmin 帐户无法查看未筛选的金融信息。 记录所有操作。
- sqladmin 帐户可以管理 SQL 数据库。

#### <a name="role-clerk"></a>角色：职员

|Item      |示例|
|----------|------|
|用户名：| `receptionist_EdnaB@contosowebstore.com`|
| 姓名： |`Edna Benson`|
| 名字：| `Edna`|
|姓氏：| `Benson`|
| 用户类型： |`Member`|

Edna Benson 是业务经理兼接待员。 她负责确保客户信息准确和帐单完成。 Edna 是登录的用户，与 Contoso Webstore 演示网站全面交互。 Edna 拥有以下权限： 

- Edna 可以创建和读取客户信息。
- Edna 可以修改客户信息。
- Edna 可以覆盖财务信息。
- Edna 帐户无法查看未筛选的金融信息。



### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - 估算价格

本基础体系结构和示例 Web 应用程序采用月付费结构，会按小时收取使用费，因此，在选择解决方案的规模时，必须考虑这些费用。 可以使用 [Azure 成本计算器](https://azure.microsoft.com/pricing/calculator/)估算这些费用。 从 2017 年 9 月起，此解决方案的估计每月费用为大约 2500 美元，包括 ASE v2 的每月 1000 美元使用费。 这些费用根据用量而异，随时可能有变化。 客户需负责在部署时估算其每月费用，以获得更准确的估算数字。 

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

>- 应用程序网关
>- Azure Active Directory
>- 应用服务环境 v2
>- Log Analytics
>- Azure 密钥保管库
>- 网络安全组
>- Azure SQL 数据库
>- Azure 负载均衡器
>- Application Insights
>- Azure 安全中心
>- Azure Web 应用
>- Azure 自动化
>- Azure 自动化 Runbook
>- Azure DNS
>- Azure 虚拟网络
>- Azure 虚拟机
>- Azure 资源组和策略
>- Azure Blob 存储
>- Azure Active Directory 基于角色的访问控制 (RBAC)

## <a name="deployment-architecture"></a>部署体系结构

以下部分详细描述了开发和实施要素。

### <a name="network-segmentation-and-security"></a>网络分段和安全

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>应用程序网关

基础体系结构在启用 Web 应用程序防火墙 (WAF) 和 OWASP 规则集的情况下使用应用程序网关，可降低安全漏洞风险。 其他功能包括：

- [端到端 SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 启用 [SSL 卸载](/azure/application-gateway/application-gateway-ssl-portal)
- 禁用 [TLS v1.0 和 v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 应用程序防火墙](/azure/application-gateway/application-gateway-webapplicationfirewall-overview)（WAF 模式）
- 结合 OWASP 3.0 规则集的[保护模式](/azure/application-gateway/application-gateway-web-application-firewall-portal)
- 启用[诊断日志记录](/azure/application-gateway/application-gateway-diagnostics)
- [自定义运行状况探测](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure 安全中心](https://azure.microsoft.com/services/security-center)和 [Azure 顾问](/azure/advisor/advisor-security-recommendations)提供额外的保护和通知。 Azure 安全中心还提供信誉系统。

#### <a name="virtual-network"></a>虚拟网络

基本体系结构定义了一个地址空间为 10.0.0.0/16 的专用虚拟网络。

#### <a name="network-security-groups"></a>网络安全组

每个网络层有一个专用的网络安全组 (NSG)：

- 针对防火墙和应用程序网关 WAF 的外围网络安全组
- 针对管理 Jumpbox（守护主机）的 NSG
- 针对应用服务环境的 NSG

每个 NSG 打开了特定的端口和协议，以便安全正确地操作解决方案。 

此外，为每个 NSG 启用了以下配置：

- 启用的[诊断日志和事件](/azure/virtual-network/virtual-network-nsg-manage-log)存储在存储帐户中 
- 已将 Log Analytics 连接到 [NSG 的诊断功能](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

 
#### <a name="subnets"></a>子网
 确保每个子网与其相应的 NSG 关联。

#### <a name="custom-domain-ssl-certificates"></a>自定义域 SSL 证书
 使用自定义域 SSL 证书启用 HTTPS 流量。

### <a name="data-at-rest"></a>静态数据

该体系结构使用加密、数据库审核和其他措施保护静态数据。

#### <a name="azure-storage"></a>Azure 存储

为了满足静态数据加密要求，[Azure 存储](https://azure.microsoft.com/services/storage/)使用了[存储服务加密](/azure/storage/storage-service-encryption)。

#### <a name="azure-sql-database"></a>Azure SQL 数据库

Azure SQL 数据库实例使用以下数据库安全措施：

- [AD 身份验证和授权](/azure/sql-database/sql-database-aad-authentication)
- [SQL 数据库审核](/azure/sql-database/sql-database-auditing-get-started)
- [透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [防火墙规则](/azure/sql-database/sql-database-firewall-configure)，允许进行 ASE 工作线程池和客户端 IP 的管理
- [SQL 威胁检测](/azure/sql-database/sql-database-threat-detection-get-started)
- [Always Encrypted 列](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [SQL 数据库动态数据掩码](/azure/sql-database/sql-database-dynamic-data-masking-get-started)，使用部署后 PowerShell 脚本

### <a name="logging-and-auditing"></a>日志记录和审核

[Log Analytics](https://azure.microsoft.com/services/log-analytics) 可让 Contoso Webstore 全面记录所有的系统和用户活动，包括记录金融数据。 可以检查和验证更改的准确性。 

- **活动日志。**  [活动日志](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。
- **诊断日志。**  [诊断日志](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志，以及 Azure Blob 存储日志、表和队列的日志。
- **防火墙日志。**  应用程序网关提供完整的诊断和访问日志。 防火墙日志适用于已启用 WAF 的应用程序网关资源。
- **日志存档。**  所有诊断日志已配置为写入到集中式的加密 Azure 存储帐户，并根据定义的保留期（2 天）存档。 然后，日志会连接到 Azure Log Analytics 进行处理、存储以及在仪表板中显示。 [Log Analytics](https://azure.microsoft.com/services/log-analytics) 是一项服务，可帮助收集和分析云与本地环境中资源生成的数据。

### <a name="encryption-and-secrets-management"></a>加密和机密管理

Contoso Webstore 加密所有敏感数据，并使用 Azure Key Vault 管理密钥，防止检索 CHD。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 有助于保护云应用程序和服务使用的加密密钥和机密。 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) 用于加密所有客户金融数据。
- 使用 [Azure 磁盘加密](/azure/security/azure-security-disk-encryption)和 BitLocker 将数据存储在磁盘中。

### <a name="identity-management"></a>身份管理

以下技术在 Azure 环境中提供标识管理功能。

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 SQL 数据库的用户）都在 Azure Active Directory 中创建。
- 使用 Azure AD 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](/azure/active-directory/develop/active-directory-integrating-applications)。 此外，数据库列加密还使用 Azure AD 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅[Always Encrypted：保护 SQL 数据库中的敏感数据](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件，并采取相应的措施予以解决。
- [Azure 基于角色的访问控制 (RBAC)](/azure/role-based-access-control/role-assignments-portal) 可在 Azure 中实现极有针对性的访问管理。 只有订阅管理员可以访问订阅，所有用户可访问 Azure Key Vault。

若要详细了解 Azure SQL 数据库安全功能的用法，请参阅 [Contoso Clinic 演示应用程序](https://github.com/Microsoft/azure-sql-security-sample)示例。
   
### <a name="web-and-compute-resources"></a>Web 和计算资源

#### <a name="app-service-environment"></a>应用服务环境

[Azure 应用服务](/azure/app-service/)是用于部署 Web 应用的托管服务。 Contoso Webstore 应用程序部署为[应用服务 Web 应用](/azure/app-service-web/app-service-web-overview)。

[Azure 应用服务环境 (ASE v2)](/azure/app-service/app-service-environment/intro) 是一项 Azure 应用服务功能，可提供完全隔离和专用的环境，以便高度安全地运行应用服务应用。 它是本基础体系结构用来满足 PCI DSS 规范要求的一个高级服务计划。

ASE 经隔离后只运行单个客户的应用程序，始终可部署到虚拟网络中。 客户对于入站和出站的应用网络流量都有更细微的控制，且应用程序可以通过虚拟网络创建与本地公司资源的高速安全连接。

对此体系结构使用 ASE 成就了以下控制措施/配置：

- 托管在受保护的虚拟网络中并受网络安全规则的保护
- 在 ASE 中配置自签名 ILB 证书以实现 HTTPS 通信
- [内部负载均衡模式](/azure/app-service-web/app-service-environment-with-internal-load-balancer)（模式 3）
- 禁用 [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- 更改 [TLS 密码](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- 控制[入站流量 N/W 端口](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF – 限制数据](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- 允许 [SQL 数据库流量](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Jumpbox（守护主机）

由于应用服务环境受保护并已锁定，因此需要实施一项机制来允许进行任何所需的 DevOps 发布或更改，例如，能够使用 Kudu 监视 Web 应用。 虚拟机在 NAT 负载均衡器后面受到保护，允许在除 TCP 3389 以外的端口上连接 VM。 

使用以下配置创建了一个用作 Jumpbox（守护主机）的虚拟机：

-   [反恶意软件扩展](/azure/security/azure-security-antimalware)
-   [Log Analytics 扩展](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 诊断扩展](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   使用 Azure Key Vault 的 [Azure 磁盘加密](/azure/security/azure-security-disk-encryption) 
-   [自动关闭策略](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，在不使用虚拟机时可减少其资源消耗量

### <a name="security-and-malware-protection"></a>安全和恶意软件防护

在 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)可集中查看所有 Azure 资源的安全状态。 可立即确认相应的安全控制措施是否部署到位且配置正确，并且可以快速识别任何需要关注的资源。  

[Azure 顾问](/azure/advisor/advisor-overview)是个性化的云顾问程序，可帮助遵循最佳做法来优化 Azure 部署。 它可分析资源配置和遥测使用情况，并推荐解决方案，有助于提高 Azure 资源的经济效益、性能、高可用性和安全性。

适用于 Azure 云服务和虚拟机的 [Microsoft 反恶意软件](/azure/security/azure-security-antimalware)是一个免费的实时保护功能，当已知恶意软件或不需要的软件试图在 Azure 系统上安装自身或运行时，它可以使用可配置的警报帮助识别和删除病毒、间谍软件和其他恶意软件。

### <a name="operations-management"></a>操作管理

#### <a name="application-insights"></a>Application Insights

使用 [Application Insights](https://azure.microsoft.com/services/application-insights/) 可以通过应用程序性能管理和即时分析获取可付诸实施的见解。

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是一个服务，可帮助收集和分析云与本地环境中资源生成的数据。

#### <a name="managment-solutions"></a>管理解决方案

应考虑并配置以下附加管理解决方案： 
- [活动 Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure 网络分析](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [更改跟踪](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [密钥保管库分析](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [服务地图](/azure/operations-management-suite/operations-management-suite-service-map)
- [安全和审核](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [反恶意软件](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [更新管理](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>安全中心集成

默认部署旨在提供安全中心建议的基线，指示正常且安全的配置状态。 可以从 Azure 安全中心启用数据收集。 有关详细信息，请参阅 [Azure 安全中心 - 入门](/azure/security-center/security-center-get-started)。

## <a name="deploy-the-solution"></a>部署解决方案

[蓝图代码存储库][代码存储库] 中提供了用于部署此解决方案的组件。 基础体系结构的部署需要通过 Microsoft PowerShell v5 执行多个步骤。 若要连接到网站，必须提供自定义域名（例如 contoso.com）。 此域名是在步骤 2 中使用 `-customHostName` 开关指定的。 有关详细信息，请参阅[购买 Azure Web 应用的自定义域名](/azure/app-service-web/custom-dns-web-site-buydomains-web-app)。 成功部署和运行解决方案不需要自定义域名，但如果不使用，则无法连接到网站进行演示。

脚本将域用户添加到指定的 Azure AD 租户。 Microsoft 建议创建一个用于测试的新 Azure AD 租户。

如果部署过程中遇到任何问题，请参阅[常见问题解答和故障排除](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md)。

Microsoft 强烈建议使用全新的 PowerShell 安装来部署解决方案。 或者，确认是否使用了正确执行安装脚本所需的最新模块。 在此示例中，我们将登录到 Jumpbox（守护主机）并执行以下命令。 请注意，这会启用自定义域命令。


1. 安装所需的模块，并正确设置管理员角色。
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    有关详细用法说明，请参阅[脚本说明 - 设置管理帐户和权限](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md)。
    
2. 安装 solution-update-management。 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    有关详细用法说明，请参阅[脚本说明 - 部署和配置 Azure 资源](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)。
    
3. Log Analytics 日志记录和监视。 部署解决方案后，可以打开 Log Analytics 工作区，并可以使用解决方案存储库中提供的示例模板来演示如何配置监视仪表板。 有关示例模板，请参阅 [omsDashboards 文件夹](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)。 请注意，必须在 Log Analytics 中收集数据，以便正确部署模板。 这可以最多需要一小时或更多具体取决于站点的活动。
 
    设置 Log Analytics 日志记录时，请考虑包含以下资源：
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>威胁模型

数据流关系图 (DFD) 和 Contoso Web 存储的示例威胁模型[蓝图威胁模型](https://aka.ms/pciblueprintthreatmodel)。

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>客户责任表

客户负责保留一份[责任摘要矩阵](https://aka.ms/fsiblueprintcrm)，其中概述了客户负责满足的 FFIEC 要求以及 Microsoft Azure 负责的相应要求。



## <a name="disclaimer-and-acknowledgments"></a>免责声明和致谢

*2017 年 9 月*

- 本文档仅供参考。 MICROSOFT 和 AVYAN 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。  
- 本文档不向客户提供对任何 Microsoft 或 Avyan 产品或解决方案的任何知识产权的任何法律权利。  
- 客户可复制本文档，将其用于内部参考。  

  > [!NOTE]
  > 本文件中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。  

- 本文档中的解决方案旨在用作基础体系结构，不得按原样用于生产目的。 实现法规遵从需要客户咨询其审核员来验证最终客户解决方案。  
- 本页面上的所有客户名称、事务记录和相关数据均是针对本基础体系结构虚构的信息，仅供演示。 这些信息不存在实际的关联或联系，不应妄加推断。  
- 本解决方案由 Microsoft 和 Avyan Consulting 联合开发，在 [MIT 授权](https://opensource.org/licenses/MIT)下提供。

