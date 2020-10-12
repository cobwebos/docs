---
title: Azure 机器学习的 Azure 安全基线
description: Azure 机器学习安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d29bef20bef6de576b9b531952c48173819a5fa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461013"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure 机器学习的 Azure 安全基线

适用于 Microsoft Azure 机器学习的 Azure 安全基线包含可帮助你改进部署安全状况的建议。 此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。 有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：Azure 机器学习依赖于其他 Azure 服务提供计算资源。 计算资源（计算目标）用于训练和部署模型。 可以在虚拟网络中创建这些计算目标。 例如，你可以使用 Azure 虚拟机器学习计算实例来训练模型，然后将模型部署到 Azure Kubernetes 服务 (AKS)。 你可以通过在 Azure 虚拟网络中隔离 Azure 机器学习训练和推理作业来保护机器学习生命周期。

Azure 防火墙可用于控制对 Azure 机器学习工作区和公共 Internet 的访问。

- [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)

- [将 Azure 防火墙后的工作区用于 Azure 机器学习](how-to-access-azureml-behind-firewall.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：Azure 机器学习依赖于其他 Azure 服务提供计算资源。 将网络安全组分配给作为机器学习部署创建的网络。 

启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。 你还可以将流日志发送到 Log Analytics 工作区，然后使用流量分析来提供有关 Azure 云中流量模式的见解。 流量分析的优势包括能够可视化网络活动、识别热点和安全威胁、了解通信流模式，以及查明网络不当配置。

- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [了解 Azure 安全中心提供的网络安全性](../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：可以启用 HTTPS 来保护与 Azure 机器学习所部署的 Web 服务的通信。 Web 服务部署在 Azure Kubernetes 服务 (AKS) 或 Azure 容器实例 (ACI) 上，可保护客户端提交的数据。 还可以将专用 IP 与 AKS 配合使用来限制评分，使得只有虚拟网络后面的客户端才能访问 Web 服务。

- [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)

- [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：在与机器学习实例关联的虚拟网络上启用 DDoS 防护标准，以防范分布式拒绝服务 (DDoS) 攻击。 使用 Azure 安全中心集成的威胁检测来检测与已知恶意的或未使用过的 Internet IP 地址的通信。

在组织的每个网络边界上部署 Azure 防火墙，启用基于威胁情报的筛选并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

- [如何配置 DDoS 防护](../virtual-network/manage-ddos-protection.md)

- [将 Azure 防火墙后的工作区用于 Azure 机器学习](how-to-access-azureml-behind-firewall.md)

- [详细了解 Azure 安全中心威胁检测](/azure/security-center/security-center-alerts-service-layer)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：对于在 Azure 机器学习服务中安装了合适扩展的任何 VM，可以启用网络观察程序数据包捕获来调查异常活动。 

- [如何创建网络观察程序实例](../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：在组织的每个网络边界上部署所选的防火墙解决方案，以检测并/或阻止恶意流量。

从 Azure Marketplace 中选择一个产品/服务，该产品/服务支持具有负载检查功能的 ID/IPS 功能。  如果不需要进行有效负载检查，则可以使用 Azure 防火墙威胁情报。 使用基于 Azure 防火墙威胁情报的筛选功能，针对进出已知恶意 IP 地址和域的流量发出警报并/或阻止该流量。 IP 地址和域源自 Microsoft 威胁智能源。

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：对于需要访问你的 Azure 机器学习帐户的资源，请使用虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名（例如，AzureMachineLearning），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

Azure 机器学习服务在虚拟网络中记录其计算目标的服务标记列表，有助于最大程度地降低复杂性。你可以在网络管理中将其用作指导原则。

- [有关使用服务标记的详细信息](../virtual-network/service-tags-overview.md)

- [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure 机器学习命名空间关联的网络资源定义和实施标准安全配置。 在“Microsoft.MachineLearning”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施机器学习命名空间的网络配置。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure 机器学习部署关联的网络资源使用标记，以便有条理地根据分类来组织这些资源。

对于 Azure 机器学习虚拟网络中支持“说明”字段的资源，请使用它来记录允许进出某个网络的流量的规则。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 机器学习相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：Microsoft 为日志中的时间戳维护用于 Azure 资源（例如 Azure 机器学习）的时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure Monitor 引入日志来聚合由 Azure 机器学习生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方安全事件和事件管理 (SIEM)。

- [如何配置 Azure 机器学习的诊断日志](monitor-azure-machine-learning.md#configuration)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：在 Azure 资源上启用诊断设置，以访问审核日志、安全日志和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

你还可以将机器学习服务操作日志进行关联，以提高安全性与合规性。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

- [在 Azure 机器学习中启用日志记录](/azure/machine-learning/how-to-enable-logging)

- [监视 Azure 机器学习](monitor-azure-machine-learning.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：如果计算资源归 Microsoft 所有，则 Microsoft 负责收集并监视它。 

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的任何计算资源，请使用 Azure 安全中心来监视操作系统。 

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../azure-monitor/learn/quick-collect-azurevm.md)

- [了解 Azure 安全中心数据收集](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性制度，为与你的 Azure 机器学习实例关联的 Log Analytics 工作区设置日志保留期。

- [如何设置日志保留参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：分析和监视日志中的异常行为，并定期审查来自 Azure 机器学习的结果。 使用 Azure Monitor 和 Log Analytics 工作区查看日志并对日志数据执行查询。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。 

- [如何在 Log Analytics 工作区中对 Azure 机器学习执行查询](monitor-azure-machine-learning.md#analyzing-log-data)

- [在 Azure 机器学习中启用日志记录](/azure/machine-learning/how-to-enable-logging)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Log Analytics 查询入门](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：在 Azure Monitor 中，配置与活动日志和机器学习诊断设置中的 Azure 机器学习相关的日志，以将日志发送到 Log Analytics 工作区供查询或发送到某个存储帐户进行长期存档存储。 使用 Log Analytics 工作区针对安全日志和事件中的异常活动创建警报。

或者，可以启用数据并将其载入 Azure Sentinel。

- [详细了解 Azure 机器学习警报](monitor-azure-machine-learning.md#alerts)

- [如何针对 Log Analytics 工作区日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：如果计算资源由 Microsoft 拥有，则 Microsoft 负责 Azure 机器学习服务的 Antimalware 部署。 

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请为 Azure 云服务和虚拟机的 Microsoft Antimalware 启用反恶意软件事件收集。

- [如何为虚拟机配置 Microsoft Antimalware](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [如何为云服务配置 Microsoft Antimalware 扩展](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [了解 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用；Azure 机器学习不会处理或生成与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用 Azure 安全中心为 Azure 虚拟机启用安全事件日志监视。 如果启用了自动预配，则 Azure 安全中心会在所有受支持的 Azure VM 以及任何新建的 Azure VM 中预配 Log Analytics 代理。 你也可以手动安装代理。 该代理可启用进程创建事件 4688 和事件 4688 内的 CommandLine 字段。 VM 上创建的新进程由事件日志记录，由安全中心的检测服务监视。

**Azure 安全中心监视**：是

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：可以使用 Azure 门户中资源的“标识和访问管理”选项卡配置基于角色的访问控制 (RBAC)，并维护有关 Azure 机器学习资源的库存。 角色将应用到 Active Directory 中的用户、组、服务主体和托管标识。 对于个人和组，可使用内置角色或自定义角色。

Azure 机器学习为 Azure 机器学习中的常见管理方案提供了内置的 RBAC。 在 Azure Active Directory (Azure AD) 中创建了配置文件的个人可将这些 RBAC 角色分配给用户、组、服务主体或托管标识，以授予或拒绝对资源和 Azure 机器学习资源操作的访问权限。

还可以使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。

- [了解 Azure 机器学习中基于角色的访问控制](how-to-assign-roles.md)

- [如何使用 PowerShell 获取 Azure Active Directory 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：对机器学习资源的访问管理是通过 Azure Active Directory (Azure AD) 控制的。 Azure AD 没有默认密码。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：在创建新的工作区时，Azure 机器学习附带三个默认角色，创建有关使用所有者帐户的标准操作过程。

还可以通过使用 Azure AD Privileged Identity Management 和 Azure 资源管理器来启用对管理帐户的即时访问权限。 

- [详细了解机器学习默认角色](how-to-assign-roles.md#default-roles)

- [详细了解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：机器学习与 Azure Active Directory 集成。请使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问建议。  

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory 多重身份验证，并遵循 Azure 安全中心标识和访问建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：对于需要提升的权限的管理任务，请使用安全的 Azure 托管工作站（也称为特权访问工作站，简称 PAW）。

- [了解安全的 Azure 托管工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [如何启用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告和监视来检测环境中何时发生可疑活动或不安全的活动。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用 Azure AD 命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。
 
 
 
- [如何配置 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。
 
在 Azure 中，角色访问的作用域可以限定为多个级别。 对于机器学习，可以在工作区级别管理角色，例如，你可能拥有某个工作区的所有者访问权限，但没有该工作区所在的资源组的所有者访问权限。 这将提供更精细的访问控制，以在同一资源组中分隔角色。 

- [管理对 Azure 机器学习工作区的访问权限](how-to-assign-roles.md) 
 
- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure AD 标识和访问评审来有效管理组成员身份、对企业应用程序的访问以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 
 
当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

- [部署 Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，因此可以与任何 SIEM/监视工具集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。
 
 
- [如何将 Azure 活动日志与 Azure Monitor 集成](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure AD 标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可将数据引入 Azure Sentinel 以做进一步调查。
 
- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)
 
- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
 
- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不适用；Azure 机器学习服务不支持客户密码箱。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。
 
- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用单独的订阅和管理组对各个安全域（如环境类型和数据敏感度级别）实现隔离。 你可以限制对应用程序和企业环境所需 Azure 资源的访问级别。 可以通过 Azure RBAC 来控制对 Azure 资源的访问。
 
- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](../governance/management-groups/create.md)
 
- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：利用 Azure 市场中有关网络外围的第三方解决方案，监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。 

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：通过 Azure 机器学习部署的 Web 服务仅支持对数据强制实施传输中加密的 TLS 版本 1.2。

- [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 机器学习。 可以根据合规性需要实施第三方解决方案。

对于由 Microsoft 管理的基础平台，Microsoft 会将所有客户内容都视为敏感信息，竭尽全力防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指导**：Azure 机器学习支持使用 Azure Active Directory (Azure AD) 授权对机器学习资源的请求。 可以通过 Azure AD 使用 Azure 基于角色的访问控制 (RBAC) 授予对安全主体的访问权限，该安全主体可能是用户，也可能是应用程序服务主体。

- [管理对 Azure 机器学习工作区的访问权限](how-to-assign-roles.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此项指导适用于计算资源。

Microsoft 会管理机器学习的底层基础结构，并实施了严格的控制措施来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：Azure 机器学习在绑定到 Azure 机器学习工作区和订阅的 Azure Blob 存储帐户中存储快照、输出与日志。 Azure Blob 存储中存储的所有数据已通过 Microsoft 管理的密钥静态加密。 在机器学习服务中，还可以使用你自己的密钥加密 Azure Blob 存储中存储的数据。 

- [Azure 机器学习静态数据加密](concept-enterprise-security.md#encryption-at-rest)

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

- [如何配置客户管理的加密密钥](../storage/common/storage-encryption-keys-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure 机器学习的生产实例和其他关键资源或相关资源发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：如果计算资源由 Microsoft 拥有，则 Microsoft 负责 Azure 机器学习服务的漏洞管理。 

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请遵循 Azure 安全中心提供的关于在 Azure 虚拟机、容器映像和 SQL 服务器上执行漏洞评估的建议。

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：如果计算资源由 Microsoft 拥有，则 Microsoft 负责 Azure 机器学习服务的补丁管理。 

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的任何计算资源，请使用 Azure 自动化更新管理确保在 Windows 和 Linux VM 上安装最新的安全更新。 对于 Windows 虚拟机，请确保已启用 Windows 更新并将其设置为自动更新。

- [如何在 Azure 中为虚拟机配置更新管理](/azure/automation/automation-update-management)

- [了解通过安全中心监视的 Azure 安全策略](../security-center/security-center-policy-definitions.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动化补丁管理解决方案

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用第三方补丁管理解决方案。 已在其环境中使用 Configuration Manager 的客户还可以使用 System Center Updates Publisher，以便将自定义更新发布到 Windows Server 更新服务中。 这样更新管理就可以通过第三方软件来修补使用 Configuration Manager 作为其更新存储库的计算机。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请遵循 Azure 安全中心提供的关于在 Azure 虚拟机、容器映像和 SQL 服务器上执行漏洞评估的建议。 以一致的间隔导出扫描结果，并将结果与以前的扫描进行比较以验证漏洞是否已修复。 使用 Azure 安全中心建议的漏洞管理建议时，可以转到选定解决方案的门户查看历史扫描数据。

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询和发现订阅中的资源（例如计算、存储、网络、端口和协议等）。  确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Azure Resource Graph 浏览器发现经典 Azure 资源，但我们强烈建议你今后创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于 Azure 资源，添加元数据以便根据分类有条理地进行组织。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。
 
 
 
- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)
 
 
 
- [如何创建管理组](../governance/management-groups/create.md)
 
 
 
- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

* 不允许的资源类型
* 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用 Azure 自动化更改跟踪和库存来自动收集 Windows 和 Linux VM 中的库存信息。 可从 Azure 门户获得软件名称、版本、发布者和刷新时间。 若要获取软件安装日期和其他信息，请启用来宾级诊断，并将 Windows 事件日志定向到 Log Analytics 工作区。

- [如何为 VM 启用 Azure 自动化库存收集](../automation/automation-tutorial-installed-software.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用 Azure 安全中心的文件完整性监视 (FIM) 来识别 VM 上安装的所有软件。 从 Linux 和 Windows VM 收集库存时，可以改用或与 FIM 一起使用的另一个选项是 Azure 自动化更改跟踪和库存。 

可以实现自己的未授权软件删除过程。 还可以使用第三方解决方案来识别未获批软件。

如果不再需要 Azure 资源，请将其删除。

- [如何使用文件完整性监视](../security-center/security-center-file-integrity-monitoring.md)

- [了解 Azure 自动化更改跟踪和库存](../automation/change-tracking.md)

- [如何启用 Azure 虚拟机库存](../automation/automation-tutorial-installed-software.md)

- [Azure 资源组和资源删除](../azure-resource-manager/management/delete-resource-group.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用 Azure 安全中心自适应应用程序控制确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

* 不允许的资源类型
* 允许的资源类型

此外，请使用 Azure Resource Graph 来查询并发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的任何计算资源，请使用 Azure 安全中心自适应应用程序控制指定规则可能适用或不适用的文件类型。

如果自适应应用程序控制不符合要求，请实施第三方解决方案。

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure AD 条件访问来限制用户与 Azure 资源管理器交互的能力。
 
- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，根据脚本的类型，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。  你还可以利用 Azure 安全中心自适应应用程序控制来确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

- [如何在 Windows 环境中控制 PowerShell 脚本的执行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 为 Azure 机器学习服务定义和实施标准安全配置。 在“Microsoft.MachineLearning”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 机器学习服务的配置。

Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

Azure 机器学习完全支持用于跟踪工作的 Git 存储库；你可以将存储库直接克隆到共享工作区文件系统上，在本地工作站上使用 Git，并确保将安全的配置作为机器学习环境的一部分应用于代码资源。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：如果计算资源由 Microsoft 拥有，则 Microsoft 负责 Azure 机器学习服务的操作系统安全配置。

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用 Azure 安全中心建议来维护所有计算资源上的安全配置。  此外，你可以使用自定义操作系统映像或 Azure Automation State Configuration 来建立组织所需的操作系统的安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

- [Azure 自动化 State Configuration 概述](../automation/automation-dsc-overview.md)

- [在 Azure 中上传 VHD 并使用它创建新 Windows VM](../virtual-machines/windows/upload-generalized-managed.md)

- [使用 Azure CLI 从自定义磁盘创建 Linux VM](../virtual-machines/linux/upload-vhd.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。 此外，你可以使用 Azure 资源管理器模板维护组织所需的 Azure 资源的安全配置。 
 
 
 
- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)
 
- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
 
- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：如果计算资源由 Microsoft 拥有，则 Microsoft 负责 Azure 机器学习服务的操作系统安全配置。

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请遵循 Azure 安全中心提供的关于在 Azure 计算资源上执行漏洞评估的建议。  此外，你可以使用 Azure 资源管理器模板、自定义操作系统映像或 Azure Automation State Configuration 来维护组织所需的操作系统的安全配置。   结合 Azure Automation State Configuration，Microsoft 虚拟机模板可能有助于满足和维护安全要求。 

注意，由 Microsoft 发布的 Azure 市场虚拟机映像由 Microsoft 管理和维护。 

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/security-center-vulnerability-assessment-recommendations.md)

- [如何从 ARM 模板创建 Azure 虚拟机](../virtual-machines/windows/ps-template.md)

- [Azure 自动化 State Configuration 概述](../automation/automation-dsc-overview.md)

- [在 Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)

- [有关如何下载 VM 模板的信息](../virtual-machines/windows/download-template.md)

- [将 VHD 上传到 Azure 并创建新的 VM 的示例脚本](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果对机器学习或相关资源使用自定义 Azure Policy 定义，请使用 Azure Repos 安全地存储和管理你的代码。

Azure 机器学习完全支持用于跟踪工作的 Git 存储库；你可以将存储库直接克隆到共享工作区文件系统上，在本地工作站上使用 Git，并确保将安全的配置作为机器学习环境的一部分应用于代码资源。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用 Azure 基于角色的访问控制 (RBAC) 来确保只有经过授权的用户才能访问你的自定义映像。 使用 Azure 共享映像库，可以将映像共享给组织内的不同用户、服务主体或 Azure AD 组。 将容器映像存储在 Azure 容器注册表中，并使用 RBAC 来确保只有经过授权的用户才能进行访问。

- [了解 Azure 中的 RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [了解容器注册表的 RBAC](../container-registry/container-registry-roles.md)

- [如何在 Azure 中配置 RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [共享映像库概述](../virtual-machines/windows/shared-image-galleries.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.MachineLearning”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：如果计算资源由 Microsoft 拥有，则 Microsoft 负责 Azure 机器学习服务的安全配置部署。

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请为任何云或本地数据中心内的 Desired State Configuration (DSC) 节点使用 Azure Automation State Configuration。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。 

- [如何启用 Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用 Azure 安全中心对 Azure 资源执行基线扫描。 此外，使用 Azure Policy 警告和审核 Azure 资源配置。
 
 
 
- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：如果计算资源由 Microsoft 拥有，则 Microsoft 负责执行 Azure 机器学习服务的自动化安全配置监视。

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用 Azure 安全中心的“计算和应用”，按照适用于 VM 和服务器以及容器的建议进行操作。

- [了解 Azure 安全中心容器建议](/azure/security-center/security-center-container-recommendations)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管标识与 Azure Key Vault 结合使用，以简化云应用程序的机密管理。

Azure 机器学习支持使用客户管理的密钥进行数据存储加密，你需要管理密钥轮换并废除每个组织的安全和合规性要求。 

使用 Azure Key Vault 将机密安全地传递到远程运行，而不是在训练脚本中将其以明文形式传递。

- [Azure 机器学习的客户管理密钥](concept-enterprise-security.md#azure-blob-storage)

- [在 Azure 机器学习训练运行中使用身份验证凭据机密](how-to-use-secrets-in-runs.md)

- [如何使用 Azure 资源的托管标识](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何创建 Key Vault](/azure/key-vault/quick-create-portal)

- [如何向 Key Vault 进行身份验证](https://docs.microsoft.com/azure/key-vault/general/authentication)

- [如何分配 Key Vault 访问策略](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：Azure 机器学习支持内置角色，同时还支持创建自定义角色的功能。 使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

 
- [管理对 Azure 机器学习工作区的访问权限](how-to-assign-roles.md)

- [如何为 Azure 资源配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：Microsoft 反恶意软件是在为 Azure 服务（例如 Azure 机器学习）提供支持的基础主机上启用的，但它不会针对客户内容运行。

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的计算资源，请使用适用于 Azure 的 Microsoft Antimalware 持续监视和保护你的资源。 对于 Linux，请使用第三方反恶意软件解决方案。 另外，请使用 Azure 安全中心的数据服务威胁检测来检测上传到存储帐户的恶意软件。

- [如何为 Azure 配置 Microsoft Antimalware](../security/fundamentals/antimalware.md)

- [Azure 安全中心的威胁防护](../security-center/threat-protection.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：Microsoft 反恶意软件是在为 Azure 服务（例如 Azure 机器学习）提供支持的基础主机上启用的，但它不会针对客户内容运行。

你需要负责预先扫描要上传到非计算 Azure 资源的任何内容。 Microsoft 无法访问客户数据，因此无法代表你对客户内容执行反恶意软件扫描。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：Microsoft 反恶意软件是针对为 Azure 服务（例如 Azure 机器学习）提供支持的基础主机启用并维护的，但它不会针对客户内容运行。

Azure 机器学习为各种计算资源甚至为你自己的计算资源提供不同的支持。 对于你的组织拥有的任何计算资源，请按照 Azure 安全中心的“计算和应用”内的建议进行操作，以确保所有终结点都具有最新的签名。 对于 Linux，请使用第三方反恶意软件解决方案。

- [如何为 Azure 部署 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：机器学习服务中的数据恢复管理是通过已连接的数据存储上的数据管理进行的。 请确保遵循有关已连接存储的数据恢复准则，以根据客户组织策略备份数据。

- [如何从 Azure 虚拟机备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：机器学习服务中的数据备份是通过已连接的数据存储上的数据管理进行的。 请为 VM 启用 Azure 备份，并配置所需的频率和保留期。 在 Azure Key Vault 中备份客户管理的密钥。

- [如何从 Azure 虚拟机备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)
- [如何在 Azure 中还原 Key Vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：机器学习服务中的数据备份验证是通过已连接的数据存储上的数据管理进行的。 请定期在 Azure 备份中执行内容数据还原。 请确保可以还原已备份的客户管理的密钥。

- [如何从 Azure 虚拟机备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：对于本地备份，请使用备份到 Azure 时提供的密码提供静态加密。 使用基于角色的访问控制来保护备份和客户管理的密钥。 

在 Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。 如果将 Azure 存储用于存储备份，请启用软删除以在 blob 或 blob 快照被删除时保存和恢复数据。
 
 
- [了解 Azure RBAC](../role-based-access-control/overview.md)

- [如何在 Key Vault 中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Azure Blob 存储的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果或分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 查明弱点和差距，并根据需要修改你的响应计划。

- [NIST 发布内容 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议，以便确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心的工作流自动化功能，针对安全警报和建议自动触发响应，以保护 Azure 资源。

- [如何在安全中心配置工作流自动化](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
