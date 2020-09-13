---
title: Batch 的 Azure 安全基线
description: Batch 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1eb24871817f365efe58b8e687563727df74493c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400970"
---
# <a name="azure-security-baseline-for-batch"></a>Batch 的 Azure 安全基线

Batch 的 Azure 安全基线包含可帮助你改善部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指导**：在虚拟网络中部署 Azure Batch 池。 若要允许池计算节点安全地与其他虚拟机或本地网络进行通信，可以在 Azure 虚拟网络的子网中预配该池。 此外，在虚拟网络中部署池可以控制用于保护各个节点的网络接口 (NIC) 和子网的网络安全组 (NSG)。 请将 NSG 配置为仅允许来自 Internet 上的受信任 IP/位置的流量。

如何在虚拟网络中创建 Azure Batch 池：

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NICS 的配置和流量

**指导**：使用 Azure 安全中心并修正与 Batch 池关联的虚拟网络/网络安全组 (NSG) 的相关网络保护建议。 在用于保护 Batch 池的 NSG 上启用流日志，并将日志发送到 Azure 存储帐户进行流量审核。 还可以将 NSG 流日志发送到 Azure Log Analytics 工作区，并使用 Azure 流量分析来洞察 Azure 云中的通信流。 Azure 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析： 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

了解 Azure 安全中心提供的网络安全性：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址进行通信

**指导**：在虚拟网络上启用 Azure DDoS（分布式拒绝服务）标准保护，防止 Azure Batch 池受到 DDoS 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

如何配置 DDoS 防护：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

了解 Azure 安全中心集成的威胁情报：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指导**：在用于保护 Azure Batch 池的网络安全组 (NSG) 上启用流日志，并将日志发送到 Azure 存储帐户进行流量审核。

如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6：部署基于网络的入侵检测/入侵防护系统

**指导**：按照合规性方面的要求从 Azure 市场中选择一个网络虚拟设备，该设备支持入侵检测系统 (IDS) 以及具有有效负载检查功能的入侵防护系统 (IPS) 功能。

如果不需要使用基于有效负载检查的入侵检测和/或防护，则可以使用包含威胁情报功能的 Azure 防火墙。 使用基于 Azure 防火墙威胁情报的筛选功能，可以发出警报并拒绝进出已知的恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在 Azure Batch 池节点所在的虚拟网络中部署采用公共 IP 地址的 Azure 防火墙。 在 Internet 上的可信位置与各个池节点的专用 IP 地址之间配置网络地址转换 (NAT) 规则。 在 Azure 防火墙的“威胁情报”下，将“发出警报并拒绝”配置为发出警报并阻止进出已知的恶意 IP 地址和域的流量。 IP 地址和域来源于 Microsoft 威胁智能源，只包含最高置信度记录。 

如何在虚拟网络中创建 Azure Batch 池：

https://docs.microsoft.com/azure/batch/batch-virtual-network

如何部署 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 市场：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用。基准适用于在 Azure 应用服务或 IaaS 实例上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：在与 Azure Batch 池关联的网络安全组或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

了解并使用服务标记

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure Batch 池关联的网络资源定义和实施标准安全配置。 使用 "Microsoft.Batch" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure Batch 池的网络配置。

如何配置和管理 Azure Policy：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

**指导**：对与 Azure Batch 池关联的、与网络安全性和通信流相关的网络服务组 (NSG) 和其他资源使用标记。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何创建虚拟网络：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Batch 池相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

如何查看和检索 Azure 活动日志事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

如何在 Azure Monitor 中创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：对于 Azure Batch，默认情况下，Microsoft 提供时间同步。但是，如果有特定的时间同步要求，则可以实现这些更改。

**Azure 安全中心监视**：目前不可用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：将 Azure Batch 帐户加入 Azure Monitor，以聚合群集设备生成的安全数据。 利用自定义查询来检测和应对环境中的威胁。  对于 Azure Batch 资源级别的监视，请使用 Batch API 来监视或查询资源（包括作业、任务、节点和池）的状态。

如何将 Azure Batch 帐户加入 Azure Monitor：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：对于 Azure Batch 帐户级别的监视，请使用 Azure Monitor 的功能监视每个 Batch 帐户。 Azure Monitor 针对 Batch 帐户级别范围内的资源（例如池、作业和任务）收集指标以及可选的诊断日志。 可以手动或以编程方式收集并使用此数据来监视 Batch 帐户中的活动以及对问题进行诊断。

对于 Azure Batch 资源级别的监视，请使用 Azure Batch API 来监视或查询资源（包括作业、任务、节点和池）的状态。

如何配置 Azure Batch 帐户级别的监视和日志记录：

https://docs.microsoft.com/azure/batch/monitoring-overview

了解 Batch 资源级别的监视：

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-system"></a>2.4：从操作系统收集安全日志

**指导**：Azure Monitor 收集 Azure Batch 帐户中资源的指标和诊断日志。 请以各种方法收集和使用此数据来监视 Azure Batch 帐户并诊断问题。 还可以配置指标警报，以便在某项指标达到指定值时收到通知。

可以根据需要通过安全外壳 (SSH) 或远程桌面协议 (RDP) 连接到各个池节点以访问本地操作系统日志。

如何从 Azure Batch 帐户收集诊断日志：

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

如何远程连接到 Azure Batch 池节点：

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：将 Azure Batch 帐户加入 Azure Monitor。 确保所用 Azure Log Analytics 工作区的日志保留期已根据组织的符合性法规设置

如何配置 Azure Batch 的监视和日志记录功能：

https://docs.microsoft.com/azure/batch/monitoring-overview

如何配置 Azure Log Analytics 工作区保留期：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：创建在指定指标的值超出给定阈值时触发的 Azure Batch 指标警报。

如何配置 Azure Batch 指标警报：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：启用针对异常活动的警报

**指导**：创建在指定指标的值超出给定阈值时触发的 Azure Batch 指标警报。

如何配置 Azure Batch 指标警报：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：如果使用的是 Windows 操作系统，请在各个 Batch 节点上使用 Windows Defender；如果使用的是 Linux，请提供自己的反恶意软件解决方案。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：实施用于 DNS 日志记录的第三方解决方案

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：按节点手动配置控制台日志记录和 PowerShell 转录。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：维护预配 Azure Batch 池期间创建的本地管理帐户以及创建的任何其他帐户的记录。 此外，如果使用了 Azure Active Directory (AAD) 集成，必须显式分配 AAD 的内置角色，使之可供查询。 使用 AAD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

此外，可以使用 Azure 安全中心标识和访问管理建议。

如何使用 PowerShell 获取 AAD 中的目录角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 获取 AAD 中目录角色的成员：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

如何使用 Azure 安全中心监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：预配 Azure Batch 池时，可以选择创建本地计算机帐户。 没有要更改的默认密码，但可指定不同的密码，分别用于访问安全外壳 (SSH) 和远程桌面协议 (RDP)。 配置 Azure Batch 池之后，可以通过 Azure 门户或 Azure 资源管理器 API 为各个节点生成随机用户。

如何向特定计算节点添加用户：

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3：确保使用专用管理帐户

**指导**：将 Azure Batch 应用程序身份验证与 Azure Active Directory 集成。 围绕专用管理帐户的使用创建策略和过程。

此外，可以使用 Azure 安全中心标识和访问管理建议。

如何通过 Azure Active Directory 对 Batch 应用程序进行验证：

https://docs.microsoft.com/azure/batch/batch-aad-auth

如何使用 Azure 安全中心监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：不适用。虽然 Azure Batch 支持 Azure AD 身份验证，但单一登录不受支持。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证。

**指导**：将 Azure Batch 应用程序身份验证与 Azure Active Directory (AAD) 集成。 启用 AAD 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议。

 

如何在 Azure 中启用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了多重身份验证 (MFA) 的 PAW（特权访问工作站）来登录和配置 Azure Batch 资源。

了解特权访问工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：如果已将 Azure Batch 应用程序身份验证与 Azure Active Directory (AAD) 集成，则当环境中出现可疑或不安全的活动时，请使用 Azure Active Directory 安全报告来生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

如何确定标记为有风险活动 Azure AD 用户：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何在 Azure 安全中心监视用户标识和访问活动：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：如果已将 Azure Batch 应用程序身份验证与 Azure Active Directory 集成，则可使用条件访问命名位置，仅允许从特定的 IP 地址范围或国家/地区的逻辑组进行访问。

了解如何在 Azure 中配置命名位置：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (AAD) 作为中心身份验证和授权系统，并将 Azure Batch 应用程序身份验证与 AAD 集成。 AAD 通过对静态数据和传输中数据使用强加密来保护数据。 AAD 还会对用户凭据进行加盐、哈希处理和安全存储。

如何创建和配置 AAD 实例：

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

如何通过 AAD 对 Batch 应用程序进行验证：

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (AAD) 提供了有助于发现陈旧帐户的日志。 此外，可以使用 Azure 标识访问评审来有效地管理组成员身份、对企业应用程序的访问以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才能持续拥有访问权限。

如何使用 Azure 标识访问评审：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视尝试访问已停用帐户的行为

**指导**：为 Azure Active Directory 用户帐户创建诊断设置，将审核日志和登录日志发送到 Azure Log Analytics 工作区。 在 Azure Log Analytics 工作区中配置所需的警报。

如何将 Azure 活动日志集成到 Azure Monitor 中：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心监视**：是

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帐户登录行为偏差的警报

**指南**：使用 AZURE ACTIVE DIRECTORY (AAD) 风险检测和标识保护功能将自动响应配置为检测到与用户标识相关的可疑操作。 此外，可将数据引入 Azure Sentinel 以做进一步调查。

如何查看 AAD 风险登录：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用标识保护风险策略：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何载入 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13： <div>在支持方案中为 Microsoft 提供对相关客户数据的访问权限<br></div>

**指导**：不可用；Azure Batch 尚不支持客户密码箱。
 
客户密码箱支持的服务列表： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



Azure 安全中心监视：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure Batch 池应当按虚拟网络/子网进行分隔，相应地进行标记，并由网络安全组 (NSG) 提供保护。 Azure Batch 数据应包含在受保护的 Azure 存储帐户中。

如何在虚拟网络中创建 Azure Batch 池：

https://docs.microsoft.com/azure/batch/batch-virtual-network

如何保护 Azure 存储帐户：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心监视**：是

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输。

**指导**：对于与包含敏感信息的 Azure Batch 池关联的 Azure 存储帐户，请使用标记将其标记为“敏感”，并使用 Azure 最佳做法对其进行保护。

数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

如何保护 Azure 存储帐户：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 默认情况下，Microsoft Azure 资源将协商 TLS 1.2。 确保连接到 Azure Batch 池或数据存储（Azure 存储帐户）的任何客户端都能够协商使用 TLS 1.2 或更高版本。

确保访问包含你的 Azure Batch 数据的存储帐户时要求使用 HTTPS。

了解 Azure 存储帐户的传输中加密：

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：对于与包含敏感信息的 Azure Batch 池关联的 Azure 存储帐户，请使用标记将其标记为“敏感”，并使用 Azure 最佳做法对其进行保护。

数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

如何保护 Azure 存储帐户：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用 azure RBAC)  (azure 基于角色的访问控制来控制对 azure 资源的管理平面的访问权限，包括 batch 帐户、批处理池 () 和存储帐户。

了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

如何配置 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护功能来强制实施访问控制

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：对于与 Azure Batch 帐户关联的存储帐户，建议允许 Microsoft 管理加密密钥，但你可以根据需要选择管理你自己的密钥。

如何管理 Azure 存储帐户的加密密钥：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 安全中心监视**：是

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在与 Azure Batch 帐户/池相关或关联的 Azure 关键资源发生更改时发出的警报。

为关联到 Azure Batch 池的存储帐户配置诊断设置，以监视并记录针对池数据执行的所有 CRUD 操作。

如何针对 Azure 活动日志事件创建警报： 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何为 Azure 存储帐户启用其他日志记录/审核：

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：对于 Azure Batch 池节点，你负责管理漏洞管理解决方案。

（可选）如果你有 Rapid7、Qualys 或任何其他漏洞管理平台订阅，可以手动在 Batch 池节点上安装漏洞评估代理，并通过相应的门户管理节点。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**： Microsoft 维护和更新基本 Azure Batch 池节点映像。 确保 Azure Batch 池节点的操作系统在群集生存期内保持修补，这可能需要启用自动更新、监视节点或定期执行重启。


**Azure 安全中心监视**：是

**责任**：共享

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指导**：确保 Azure Batch 池节点的第三方应用程序在群集生存期内始终得到修补，这可能需要启用自动更新、对节点进行监视或执行定期重启。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：如果你有 Rapid7、Qualys 或任何其他的漏洞管理平台订阅，则可使用该供应商的门户来查看和比较连续的漏洞扫描。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险分级流程确定所发现漏洞的修正优先级。

**指南**：使用常见的风险分级程序（例如“常见漏洞评分系统”）或第三方扫描工具提供的默认风险分级功能。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络等）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器 (ARM) 资源。

如何使用 Azure Resource Graph 创建查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

如何创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单。

**指导**：为计算资源定义已批准的 Azure 资源和已批准的软件的列表


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：对于 Azure Batch 池节点，请实施第三方解决方案来监视群集节点，以检测是否存在未批准的软件应用程序。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：对于 Azure Batch 池节点，请实施第三方解决方案来监视群集节点，以检测是否存在未批准的软件应用程序。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：对于 Azure Batch 池节点，请实施第三方解决方案，以阻止执行未经授权的软件。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Policy 拒绝特定的资源类型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指导**：对于 Azure Batch 池节点，请实施第三方解决方案，以阻止执行未经授权的文件类型。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11： <div>限制用户通过脚本与 Azure 资源管理器进行交互的能力</div>

**指导**：通过对“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的功能。

如何配置条件访问以阻止访问 Azure 资源管理器： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指导**：不适用。

此功能不适用于 Azure Batch，因为 Azure Batch 池的用户（非管理员）不需要访问各个节点来运行作业。 群集管理员已经拥有所有节点的 root 访问权限。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用。基准适用于在 Azure 应用服务或 IaaS 实例上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用“Microsoft.Batch”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Batch 帐户和池的配置。

如何查看可用的 Azure Policy 别名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure Policy：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2：为操作系统建立安全配置

**指导**：为 Batch 池节点的操作系统建立安全配置。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：为所有 Azure 资源维护安全配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对与 Batch 帐户和池相关的 Azure 资源（例如虚拟网络、子网、Azure 防火墙、Azure 存储帐户等）强制实施安全设置。 可以使用以下命名空间中的 Azure Policy 别名创建自定义策略：

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure Policy 效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4：维护操作系统安全配置

**指导**：Azure Batch 池操作系统映像由 Microsoft 管理和维护。 你负责实现 OS 级别的状态配置。

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果将自定义 Azure 策略定义用于 Azure Batch 帐户、池或相关资源，请使用 Azure Repos 安全地存储和管理你的代码。

如何在 Azure DevOps 中存储代码：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文档：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：如果对 Azure Batch 池使用自定义映像，请使用 azure RBAC)  (azure 基于角色的访问控制，以确保只有经过授权的用户才能访问这些映像。

了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

如何配置 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指导**：使用内置的 Azure Policy 定义来审核和强制实施与 Azure Batch 相关的资源配置以及发出相关警报。  使用“Microsoft.Batch”命名空间中的 Azure Policy 别名为 Azure Batch 帐户和池创建自定义策略。 另外，开发一个用于管理策略例外的流程和管道。

如何配置和管理 Azure Policy：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：实施第三方解决方案来维护 Azure Batch 池节点的操作系统的所需状态。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实现自动配置监视

**指导**：使用“Microsoft.Batch”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Batch 实例的配置。 还可以使用专为 Azure Batch 或专为 Azure Batch 使用的资源创建的任何内置策略，例如：

- 子网应与网络安全组关联

-存储帐户应使用虚拟网络服务终结点

- 应启用 Batch 帐户中的诊断日志

如何查看可用的 Azure Policy 别名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实现自动配置监视

**指导**：实施第三方解决方案来以监视 Azure Batch 池节点操作系统的状态。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="711-securely-manage-azure-secrets"></a>7.11：安全管理 Azure 机密

**指导**：可将 Azure Key Vault 用于 Azure Batch 部署，以管理 Azure 存储帐户中的池存储的密钥。

如何与 Azure 托管标识集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault：

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何对 Key Vault 进行身份验证：

https://docs.microsoft.com/azure/key-vault/general/authentication

如何分配 Key Vault 访问策略：

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-securely-and-automatically-manage-identities"></a>7.12：安全自动管理标识

**指导**：未提供。Azure Batch 不支持托管服务标识


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

如何设置凭据扫描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：如果使用的是 Windows 操作系统，请在各个 Azure Batch 池节点上使用 Windows Defender；如果使用的是 Linux，请提供自己的反恶意软件解决方案。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：在支持 Azure 服务（例如 Azure Batch）的底层主机上已启用 Microsoft Antimalware，但是，该软件不会针对自定义内容运行。

预先扫描上传到非计算 Azure 资源的任何文件，例如应用服务、Data Lake Storage、Blob 存储等。在这些实例中，Microsoft 无法访问客户数据。

了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：如果使用的是 Windows 操作系统，请在各个 Azure Batch 池节点上使用 Windows Defender，并确保启用自动更新。 如果使用的是 Linux，请提供自己的反恶意软件解决方案。


**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：将 Azure 存储帐户用于 Azure Batch 池数据存储时，请选择适当的冗余选项（LRS、ZRS、GRS、RA-GRS）。 

如何为 Azure 存储帐户配置存储冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：将 Azure 存储帐户用于 Azure Batch 池数据存储时，请选择适当的冗余选项（LRS、ZRS、GRS、RA-GRS）。  如果在任何 Azure Batch 部署环节中使用 Azure Key Vault，请确保备份你的密钥。

如何为 Azure 存储帐户配置存储冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

如何在 Azure 中备份密钥保管库密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：如果你要自己为 Azure 存储帐户或与 Azure Batch 实现相关的任何其他资源管理密钥，请定期对已备份的密钥进行测试性还原。

如何在 Azure 中备份密钥保管库密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

如何使用 PowerShell 还原客户管理的密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：如果使用 Azure Key Vault 保存与 Azure Batch 池存储帐户相关的任何密钥，请在 Azure Key Vault 中启用“软删除”，以防止密钥被意外删除或恶意删除。

如何在 Azure Key Vault 中启用“软删除”：

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：确保在书面的事件响应计划中定义人员职责，以及事件处理/管理的各个阶段。

如何在 Azure 安全中心配置工作流自动化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级确定过程

**指导**：安全中心将为警报分配严重性来帮助你确定每条警报的处理优先顺序，以便在资源泄密时可以立即采取措施。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期练习以测试系统的事件响应能力。 识别弱点和差距，并根据需要修改计划。

请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities（IT 规划和功能的测试、培训与演练计划指南）： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知&nbsp;

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现你的数据被非法或未授权的一方访问，microsoft 将使用安全事件联系人信息与你联系。

如何设置 Azure 安全中心安全联系人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

如何配置连续导出：

https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

如何配置工作流自动化和逻辑应用：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源执行渗透测试，确保在 60 天内修正所有发现的关键安全问题。

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

在以下网页中可以找到有关 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试的详细信息：  

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)
