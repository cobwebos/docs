---
title: 批处理的 Azure 安全基线
description: 批处理的 Azure 安全基线
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a9569c1f5de797c77f447b5df15e85a57e8be84b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472922"
---
# <a name="azure-security-baseline-for-batch"></a>批处理的 Azure 安全基线

批处理的 Azure 安全基线包含可帮助您改进部署安全状态的建议。

此服务的基线取自[Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有关如何使用最佳实践指南在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虚拟网络上的网络安全组或 Azure 防火墙保护资源

**指导**：在虚拟网络中部署 Azure 批处理池。 要允许池计算节点与其他虚拟机或本地网络进行安全通信，可以在 Azure 虚拟网络的子网中预配池。 此外，在虚拟网络中部署池可以控制用于保护各个节点的网络接口 （NIC） 以及子网的网络安全组 （NSG）。 配置 NSG 以仅允许来自 Internet 上受信任的 IP/位置的流量。


如何在虚拟网络中创建 Azure 批处理池：

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 监视和记录 Vnet、子网和 NICS 的配置和流量

**指南**：使用 Azure 安全中心并修复与批处理池关联的虚拟网络/网络安全组 （NSG） 相关的网络保护建议。 在用于保护批处理池的 NSG 上启用流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 您还可以将 NSG 流日志发送到 Azure 日志分析工作区，并使用 Azure 流量分析提供有关 Azure 云中的流量流的见解。 Azure 流量分析的一些优点是能够可视化网络活动并识别热点、识别安全威胁、了解流量流模式和查明网络错误配置。


如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


如何启用和使用流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


了解 Azure 安全中心提供的网络安全：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

**指南**：不适用，基准测试适用于在 Azure 应用服务或 IaaS 实例上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

**指南**：在虚拟网络上启用 Azure DDoS（分布式拒绝服务）标准保护，保护 Azure Batch 池，防止 DDoS 攻击。 使用 Azure 安全中心集成威胁智能拒绝与已知恶意或未使用的 Internet IP 地址的通信。


如何配置 DDoS 保护：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


了解 Azure 安全中心集成威胁情报：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 记录网络数据包和流日志

**指南**：在用于保护 Azure 批处理池的网络安全组 （NSG） 上启用流日志，并将日志发送到 Azure 存储帐户以进行流量审核。


如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6： 部署基于网络的入侵检测/入侵防御系统

**指南**：如果需要符合性，请从 Azure 应用商店中选择一个网络虚拟设备，该设备支持具有有效负载检查功能的入侵检测系统 （IDS） 和入侵防护系统 （IPS） 功能。


如果不需要基于有效负载检查的入侵检测和/或预防，则可以使用具有威胁智能的 Azure 防火墙。 Azure 防火墙威胁基于智能的筛选可以警报和拒绝来自已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。


使用公共 IP 地址在与 Azure 批处理池节点相同的虚拟网络中部署具有公共 IP 地址的 Azure 防火墙。 在 Internet 上的受信任位置和单个池节点的专用 IP 地址之间配置网络地址转换 （NAT） 规则。 在 Azure 防火墙上，在"威胁智能"下，配置"警报和拒绝"以阻止以警报和阻止来自已知恶意 IP 地址和域的流量。 IP 地址和域来自 Microsoft 威胁情报源，仅包含最高置信记录。 


如何在虚拟网络中创建 Azure 批处理池：

https://docs.microsoft.com/azure/batch/batch-virtual-network


如何部署 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal


Azure 市场：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7： 管理 Web 应用程序的流量

**指南**：不适用，基准测试适用于在 Azure 应用服务或 IaaS 实例上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义与 Azure 批处理池关联的网络安全组或 Azure 防火墙上的网络访问控件。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。


了解和使用服务标记：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

**指南**：定义和实现与 Azure 批处理池关联的网络资源与 Azure 策略关联的标准安全配置。 在"Microsoft.Batch"和"Microsoft.Network"命名空间中使用 Azure 策略别名来创建自定义策略以审核或强制执行 Azure 批处理池的网络配置。



如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

**指南**：对与 Azure 批处理池关联的网络安全和流量流相关的网络服务组 （NSG） 和其他资源使用标记。 对于单个 NSG 规则，使用"描述"字段指定允许流量从网络传输的任何规则的业务需求和/或持续时间（等）。


使用与标记相关的任何内置 Azure 策略定义（如"要求标记及其值"）确保所有资源都使用标记创建，并通知您现有的未标记资源。


您可以使用 Azure PowerShell 或 Azure CLI 查找资源或根据资源标记执行操作。


如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


如何创建虚拟网络：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


如何创建 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

**指南**：使用 Azure 活动日志监视网络资源配置并检测与 Azure 批处理池相关的网络资源的更改。 在 Azure 监视器中创建警报，这些警报将在对关键网络资源的更改时触发。

如何查看和检索 Azure 活动日志事件：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

如何在 Azure 监视器中创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：是

**责任**： 客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

**指南**：默认情况下，对于 Azure 批处理，Microsoft 提供时间同步。但是，如果您有特定的时间同步要求，则可以实现这些更改。

**Azure 安全中心监视**：当前不可用

**责任**： 微软

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

**指南**：将 Azure 批处理帐户添加到 Azure 监视器，以聚合群集设备生成的安全数据。 利用自定义查询来检测和响应环境中的威胁。  对于 Azure 批处理资源级监视，请使用批处理 API 监视或查询资源的状态，包括作业、任务、节点和池。



如何将 Azure 批处理帐户并添加到 Azure 监视器：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对于 Azure 批处理帐户级别监视，请使用 Azure 监视器的功能监视每个批处理帐户。 Azure Monitor 针对 Batch 帐户级别范围内的资源（例如池、作业和任务）收集指标以及可选的诊断日志。 可以手动或以编程方式收集并使用此数据来监视 Batch 帐户中的活动以及对问题进行诊断。


对于 Azure 批处理资源级别监视，请使用 Azure 批处理 API 监视或查询资源的状态，包括作业、任务、节点和池。


如何配置 Azure 批处理帐户级别的监视和日志记录：

https://docs.microsoft.com/azure/batch/monitoring-overview


了解批处理资源级监视：

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="24-collect-security-logs-from-operating-system"></a>2.4： 从操作系统收集安全日志

**指南**：Azure 监视器收集 Azure 批处理帐户中资源的指标和诊断日志。 以多种方式收集和使用此数据来监视 Azure Batch 帐户并诊断问题。 还可以配置指标警报，以便在某项指标达到指定值时收到通知。


如果需要，您可以通过安全外壳 （SSH） 或远程桌面协议 （RDP） 连接到单个池节点，以访问本地操作系统日志。


如何从 Azure 批处理帐户收集诊断日志：

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics


如何远程连接到 Azure 批处理池节点：

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

**指南**：将 Azure 批处理帐户添加到 Azure 监视器。 确保使用的 Azure 日志分析工作区已根据组织的合规性法规设置了日志保留期


如何配置 Azure 批处理监视和日志记录：

https://docs.microsoft.com/azure/batch/monitoring-overview


如何配置 Azure 日志分析工作区保留期：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="26-monitor-and-review-logs"></a>2.6： 监视和审阅日志

**指导**：创建 Azure 批处理指标警报，当指定指标的值超过给定阈值时触发。


如何配置 Azure 批处理指标警报：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动警报

**指导**：创建 Azure 批处理指标警报，当指定指标的值超过给定阈值时触发。


如何配置 Azure 批处理指标警报：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

**指南**：在 Windows 操作系统的情况下，在单个批处理节点上使用 Windows Defender，或者如果您使用的是 Linux，则提供您自己的反恶意软件解决方案。

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

**指南**： 为 dns 日志记录实施第三方解决方案

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="210-enable-command-line-audit-logging"></a>2.10： 启用命令行审核日志记录

**指导**：根据每个节点手动配置控制台日志记录和 PowerShell 转录。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1： 维持管理账户清单

**指南**：维护在预配 Azure Batch 池期间创建的本地管理帐户的记录以及您创建的任何其他帐户。 此外，如果使用 Azure 活动目录 （AAD） 集成，则 AAD 具有必须显式分配并因此可查询的内置角色。 使用 AAD PowerShell 模块执行临时查询以发现属于管理组成员的帐户。


此外，您可以使用 Azure 安全中心标识和访问管理建议。


如何使用 PowerShell 在 AAD 中获取目录角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


如何使用 PowerShell 获取 AAD 中目录角色的成员：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


如何使用 Azure 安全中心监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在适用的情况下更改默认密码

**指南**：预配 Azure 批处理池时，您可以选择创建本地计算机帐户。 没有要更改的默认密码，但您可以为安全外壳 （SSH） 和远程桌面协议 （RDP） 访问指定不同的密码。 配置 Azure 批处理池后，可以为 Azure 门户中的单个节点或通过 Azure 资源管理器 API 生成随机用户。


如何将用户添加到特定计算节点：

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3： 确保使用专用管理账户

**指南**：将 Azure 批处理应用程序的身份验证与 Azure 活动目录集成。 围绕使用专用管理帐户创建政策和程序。


此外，您可以使用 Azure 安全中心标识和访问管理建议。


如何使用 Azure 活动目录对批处理应用程序进行身份验证：

https://docs.microsoft.com/azure/batch/batch-aad-auth


如何使用 Azure 安全中心监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用 Azure 活动目录的单一登录 （SSO）

**指南**：不适用，虽然 Azure 批处理支持 Azure AD 身份验证，但不支持单一登录。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证。

**指南**：将 Azure 批处理应用程序的身份验证与 Azure 活动目录 （AAD） 集成。 启用 AAD 多重身份验证 （MFA），并遵循 Azure 安全中心标识和访问管理建议。
 


如何在 Azure 中启用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


如何在 Azure 安全中心内监视标识和访问：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用具有多重身份验证 （MFA） 的 PAW（特权访问工作站）来登录和配置 Azure 批处理资源。


了解特权访问工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


如何在 Azure 中启用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 从管理帐户记录和警报可疑活动

**指南**：如果 Azure 批处理应用程序具有 Azure 活动目录 （AAD） 的集成身份验证，请使用 Azure 活动目录安全报告生成日志和警报，当环境中发生可疑或不安全活动时。 使用 Azure 安全中心监视标识和访问活动。


如何识别标记为有风险活动的 Azure AD 用户：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


如何监视 Azure 安全中心中的用户标识和访问活动：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

**指南**：如果具有 Azure 活动目录的 Azure 批处理应用程序集成身份验证，则可以使用条件访问命名位置仅允许从 IP 地址范围或国家/区域的特定逻辑分组进行访问。



如何在 Azure 中配置命名位置：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活动目录

**指南**：使用 Azure 活动目录 （AAD） 作为中央身份验证和授权系统，并将 Azure 批处理应用程序的身份验证与 AAD 集成。 AAD 对静态和传输中的数据使用强加密来保护数据。 AAD 还对用户凭据进行盐渍、哈希和安全地存储。


如何创建和配置 AAD 实例：

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance


如何使用 AAD 对批处理应用程序进行身份验证：

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期查看和协调用户访问

**指南**： Azure 活动目录 （AAD） 提供日志以帮助发现陈旧的帐户。 此外，您可以使用 Azure 标识访问审核有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期查看用户的访问权限，以确保只有正确的用户才能继续访问。


如何使用 Azure 标识访问审核：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 监视访问已停用帐户的尝试

**指南**：为 Azure 活动目录用户帐户创建诊断设置，将审核日志和登录日志发送到 Azure 日志分析工作区。 在 Azure 日志分析工作区中配置所需的警报。


如何将 Azure 活动日志集成到 Azure 监视器中：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

**指南**：使用 Azure 活动目录 （AAD） 风险检测和身份保护功能配置自动响应，以识别与用户身份相关的可疑操作。 此外，您可以将数据引入 Azure Sentinel 以进行进一步调查。


如何查看 AAD 风险登录：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


如何配置和启用身份保护风险策略：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>在支持方案期间向 Microsoft 提供对相关客户数据的访问<br></div>

**指导**：不可用;Azure 批处理尚不支持客户密码箱。 客户密码箱支持服务列表：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息的清单

**指南**：使用标记来帮助跟踪存储或处理敏感信息的 Azure 资源。


如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure 批处理池应由虚拟网络/子网分隔，标记适当，并通过网络安全组 （NSG） 进行保护。 Azure 批处理数据应包含在安全的 Azure 存储帐户中。


如何在虚拟网络中创建 Azure 批处理池：

https://docs.microsoft.com/azure/batch/batch-virtual-network


如何保护 Azure 存储帐户：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息。

**指南**：对于与包含敏感信息的 Azure 批处理池关联的 Azure 存储帐户，请使用标记将它们标记为敏感，并使用 Azure 最佳做法对其进行保护。


数据标识、分类和损失预防功能尚未可用于 Azure 存储或计算资源。 如果需要符合性，则实施第三方解决方案。


对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。


了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


如何保护 Azure 存储帐户：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：加密传输中的所有敏感信息。 默认情况下，Microsoft Azure 资源将协商 TLS 1.2。 确保连接到 Azure 批处理池或数据存储（Azure 存储帐户）的任何客户端能够协商 TLS 1.2 或更高。


确保访问包含 Azure 批处理数据的存储帐户需要 HTTPS。


了解传输中的 Azure 存储帐户加密：

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Azure 安全中心监视**：是

**责任**： 共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活动发现工具识别敏感数据

**指南**：对于与包含敏感信息的 Azure 批处理池关联的 Azure 存储帐户，请使用标记将它们标记为敏感，并使用 Azure 最佳做法对其进行保护。


数据标识、分类和损失预防功能尚未可用于 Azure 存储或计算资源。 如果需要符合性，则实施第三方解决方案。


对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。


了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


如何保护 Azure 存储帐户：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 活动目录 （AAD） 基于角色的访问控制 （RBAC） 来控制对 Azure 资源管理层（包括批处理帐户、批处理池）和存储帐户的访问。


了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview


如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

**指南**：数据标识、分类和损失预防功能尚未可用于 Azure 存储或计算资源。 如果需要符合性，则实施第三方解决方案。



对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。



了解 Azure 中的客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

**指南**：对于与 Azure Batch 帐户关联的存储帐户，建议允许 Microsoft 管理加密密钥，但是，如果需要，您可以选择管理自己的密钥。



如何管理 Azure 存储帐户的加密密钥：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

**指南**：将 Azure 监视器与 Azure 活动日志一起用于创建警报，用于何时对与 Azure 批处理帐户/池相关或关联的关键 Azure 资源进行更改。



为与 Azure 批处理池关联的存储帐户配置诊断设置，以监视和记录针对池数据的所有 CRUD 操作。



如何为 Azure 活动日志事件创建警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



如何为 Azure 存储帐户启用其他日志记录/审核：

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure 安全中心监视**：是

**责任**： 客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 运行自动漏洞扫描工具

**指南**：对于 Azure 批处理池节点，您负责管理漏洞管理解决方案。


或者，如果您有 Rapid7、Qualys 或任何其他漏洞管理平台订阅，则可以在 Batch 池节点上手动安装漏洞评估代理，并通过相应的门户管理节点。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动化操作系统修补程序管理解决方案

**指南**：微软维护和更新基本 Azure 批处理池节点映像。 确保 Azure Batch 池节点的操作系统在群集生存期内保持修补程序，这可能需要启用自动更新、监视节点或执行定期重新启动。

**Azure 安全中心监视**：是

**责任**： 共享

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动第三方软件修补程序管理解决方案

**指南**：确保 Azure 批处理池节点的第三方应用程序在群集生存期内保持修补程序，这可能需要启用自动更新、监视节点或执行定期重新启动。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比较背对背漏洞扫描

**指南**：如果您有 Rapid7、Qualys 或任何其他漏洞管理平台订阅，则可以使用该供应商的门户查看和比较背对背的漏洞扫描。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用风险评级流程确定修复已发现漏洞的优先级。

**指南**：使用常见的风险评分程序（例如常见漏洞评分系统）或第三方扫描工具提供的默认风险评级。

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：库存和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 资产发现

**指南**：使用 Azure 资源图查询/发现订阅中的所有资源（如计算、存储、网络等）。 确保租户中具有适当的（读取）权限，并能够枚举订阅中的所有 Azure 订阅和资源。


尽管可以通过 Azure 资源图资源管理器发现经典 Azure 资源，但强烈建议您今后创建和使用 Azure 资源管理器 （ARM） 资源。


如何使用 Azure 资源图资源管理器创建查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


如何查看 Azure 订阅：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="62-maintain-asset-metadata"></a>6.2： 维护资产元数据

**指南**：将标记应用于 Azure 资源，使元数据以逻辑方式将它们组织到分类中。


如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 删除未经授权的 Azure 资源

**指南**：在适当情况下，使用标记、管理组和单独的订阅来组织和跟踪资产。 定期协调库存，确保及时从订阅中删除未经授权的资源。



如何创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription



如何创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create



如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单。

**指南**：定义已批准的 Azure 资源和已批准的计算资源软件的列表

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 监视未经批准的 Azure 资源

**指南**：使用 Azure 策略对客户订阅中可创建的资源类型使用以下内置策略定义进行限制：

- 不允许的资源类型
- 允许的资源类型


使用 Azure 资源图查询/发现订阅中的资源。 确保环境中的所有 Azure 资源都获得批准。


如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


如何使用 Azure 资源图资源管理器创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 监控计算资源中的未经批准的软件应用程序

**指南**：对于 Azure 批处理池节点，实现第三方解决方案来监视未经批准的软件应用程序的群集节点。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 删除未经批准的 Azure 资源和软件应用程序

**指南**：对于 Azure 批处理池节点，实现第三方解决方案来监视未经批准的软件应用程序的群集节点。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="68-use-only-approved-applications"></a>6.8： 仅使用已批准的应用程序

**指南**：对于 Azure 批处理池节点，实现第三方解决方案以防止未经授权的软件执行。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="69-use-only-approved-azure-services"></a>6.9： 仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略对客户订阅中可创建的资源类型使用以下内置策略定义进行限制：


- 不允许的资源类型
- 允许的资源类型


如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


如何使用 Azure 策略拒绝特定资源类型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="610-implement-approved-application-list"></a>6.10： 实施已批准的申请列表

**指南**：对于 Azure 批处理池节点，实现第三方解决方案以防止执行未经授权的文件类型。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>限制用户通过脚本与 Azure 资源管理器交互的能力</div>

**指南**：使用 Azure 条件访问通过为"Microsoft Azure 管理"应用配置"阻止访问"来限制用户与 Azure 资源管理器交互的能力。


如何配置条件访问以阻止对 Azure 资源管理器的访问：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制用户在计算资源中执行脚本的能力

**指南**：不适用，

这不适用于 Azure 批处理，因为 Azure Batch 池的用户（非管理员）不需要访问各个节点来运行作业。 群集管理员已对所有节点具有根访问权限。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或逻辑隔离高风险应用程序

**指南**：不适用，基准测试适用于在 Azure 应用服务或 IaaS 实例上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用"Microsoft.Batch"命名空间中的 Azure 策略别名创建自定义策略以审核或强制执行 Azure 批处理帐户和池的配置。


如何查看可用的 Azure 策略别名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2： 为您的操作系统建立安全配置

**指南**：为批处理池节点的操作系统建立安全配置。

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：维护所有 Azure 资源的安全配置

**指导**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在））对与批处理帐户和池相关的 Azure 资源（如虚拟网络、子网、Azure 防火墙、Azure 存储帐户等）强制实施安全设置。 您可以使用以下命名空间中的 Azure 策略别名来创建自定义策略：

- Microsoft.Batch
- Microsoft.Storage
- Microsoft.Network


如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


了解 Azure 策略效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4： 维护操作系统的安全配置

**指南**： 由 Microsoft 管理和维护的 Azure 批处理池操作系统映像。 您负责实现操作系统级状态配置。

**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源配置

**指南**：如果对 Azure 批处理帐户、池或相关资源使用自定义 Azure 策略定义，请使用 Azure 存储库安全地存储和管理代码。


如何在 Azure DevOps 中存储代码：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops


Azure 存储库文档：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

**指南**：如果对 Azure 批处理池使用自定义映像，请使用基于角色的访问控制 （RBAC） 以确保只有经过授权的用户才能访问映像。


了解 Azure 中的 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles


如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系统配置管理工具

**指南**：使用内置 Azure 策略定义来警报、审核和强制执行与 Azure 批处理相关的资源配置。  在"Microsoft.Batch"命名空间中使用 Azure 策略别名，为 Azure 批处理帐户和池创建自定义策略。 此外，开发用于管理策略异常的流程和管道。



如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的系统配置管理工具

**指南**：实现第三方解决方案，以保持 Azure 批处理池节点操作系统所需的状态。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9： 实现 Azure 服务的自动配置监视

**指南**：使用"Microsoft.Batch"命名空间中的 Azure 策略别名创建自定义策略以审核或强制执行 Azure Batch 实例的配置。 您还可以使用专为 Azure 批处理创建的任何内置策略或 Azure 批处理使用的资源，例如：

- 子网应与网络安全组关联 - 存储帐户应使用虚拟网络服务终结点
- 应启用 Batch 帐户中的诊断日志

如何查看可用的 Azure 策略别名：https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 实现操作系统的自动配置监控

**指南**：实施第三方解决方案来监视 Azure 批处理池节点操作系统的状态。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="711-securely-manage-azure-secrets"></a>7.11： 安全管理 Azure 机密

**指南**：Azure 密钥保管库可与 Azure 批处理部署一起使用，以管理 Azure 存储帐户中池存储的密钥。


如何与 Azure 托管标识集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


如何创建 Azure 密钥保管库：

https://docs.microsoft.com/azure/key-vault/quick-create-portal


如何使用托管标识提供密钥保管库身份验证：

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="712-securely-and-automatically-manage-identities"></a>7.12： 安全自动管理身份

**指南**： 不可用，Azure 批处理不支持托管服务标识

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的凭据暴露

**指南**：实现凭据扫描程序以标识代码中的凭据。 凭据扫描程序还将鼓励将发现的凭据移动到更安全的位置，如 Azure 密钥保管库。 

如何设置凭据扫描程序：https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反恶意软件

**指南**：在 Windows 操作系统的情况下，在单独的 Azure 批处理池节点上使用 Windows 保护者，或者如果您使用的是 Linux，则提供您自己的反恶意软件解决方案。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上载到非计算 Azure 资源的预扫描文件

**指南**：在支持 Azure 服务的基础主机上启用 Microsoft 反恶意软件（例如，Azure Batch），但它不在客户内容上运行。


预扫描上载到非计算 Azure 资源（如应用服务、数据湖存储、Blob 存储等）的任何文件。在这些情况下，Microsoft 无法访问客户数据。


了解适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件：

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心监视**：不适用

**责任**： 共享

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 确保更新反恶意软件和签名

**指南**：在 Windows 操作系统的情况下，在单独的 Azure 批处理池节点上使用 Windows 保护程序，并确保启用了自动更新。 如果您使用的是 Linux，请提供您自己的反恶意软件解决方案。

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 确保定期自动备份

**指南**：在 Azure 批处理池数据存储中使用 Azure 存储帐户时，请选择适当的冗余选项（LRS、ZRS、GRS、RA-GRS）。 


如何为 Azure 存储帐户配置存储冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 执行完整的系统备份并备份任何客户托管密钥

**指南**：在 Azure 批处理池数据存储中使用 Azure 存储帐户时，请选择适当的冗余选项（LRS、ZRS、GRS、RA-GRS）。  如果对 Azure 批处理部署的任何部分使用 Azure 密钥保管库，请确保备份密钥。


如何为 Azure 存储帐户配置存储冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy


如何在 Azure 中备份密钥保管库密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户托管密钥

**指南**：如果要为 Azure 存储帐户或任何其他与 Azure Batch 实现相关的资源管理自己的密钥，请定期测试备份密钥的还原。


如何在 Azure 中备份密钥保管库密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


如何使用 PowerShell 恢复客户托管密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理密钥

**指南**：如果 Azure 密钥保管库用于保存与 Azure 批处理池存储帐户相关的任何密钥，请在 Azure 密钥保管库中启用软删除，以保护密钥免遭意外或恶意删除。


如何在 Azure 密钥保管库中启用软删除：

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 安全中心监视**：是

**责任**： 客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：确保有书面的事件响应计划，定义人员的角色以及事件处理/管理的各个阶段。



如何在 Azure 安全中心内配置工作流自动化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级处理过程

**指南**：安全中心为警报分配严重性，以帮助您确定处理每个警报的顺序，以便在资源受到威胁时，您可以马上到达它。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

**指导**：进行练习，以常规节奏测试系统的事件响应能力。 找出薄弱环节和差距，并根据需要修订计划。

请参阅 NIST 出版物：IT 计划和功能测试、培训和锻炼计划指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4： 提供安全事件联系人详细信息，并配置安全&nbsp;事件的警报通知

**指南**：如果 Microsoft 安全响应中心 （MSRC） 发现您的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。



如何设置 Azure 安全中心安全联系人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**： 客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 将安全警报纳入事件响应系统

**指南**：使用"连续导出"功能导出 Azure 安全中心警报和建议。 "连续导出"允许您手动或持续、持续的方式导出警报和建议。 您可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure 哨兵。


如何配置连续导出：

https://docs.microsoft.com/azure/security-center/continuous-export


如何将警报流式传输到 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，通过安全警报和建议上的"逻辑应用"自动触发响应。



如何配置工作流自动化和逻辑应用：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：当前不可用

**责任**： 客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在 60 天内修复所有关键安全发现。

**指南**：请遵循 Microsoft 的接用规则，以确保您的渗透测试不违反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



您可以在此处找到有关 Microsoft 针对 Microsoft 托管云基础架构、服务和应用程序进行红色团队和实时站点渗透测试的战略和实施的详细信息： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**： 共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线的更多信息](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
