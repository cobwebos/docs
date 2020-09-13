---
title: 逻辑应用的 Azure 安全基线
description: 逻辑应用安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 16ee5fb59741d57f47083a0c5db852872ceb91d0
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89296084"
---
# <a name="azure-security-baseline-for-logic-apps"></a>逻辑应用的 Azure 安全基线

逻辑应用的 Azure 安全基线包含可帮助你改善部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

*有关详细信息，请参阅 [Azure 安全基准：网络安全](/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**： Microsoft 部署和管理在 "全局" 多租户逻辑应用服务中运行的连接器。 这些连接器提供了用于访问云服务和/或本地系统（包括 Office 365、Azure Blob 存储、SQL Server、Dynamics、Salesforce、SharePoint 等）的触发器和操作。 在网络安全组或 Azure 防火墙上指定规则以允许访问相关资源时，可以使用 AzureConnectors 服务标记。

对于需要直接访问 Azure 虚拟网络中的资源的逻辑应用，你可以 (ISE) 创建集成服务环境，你可以在该环境中生成、部署和运行针对专用资源的逻辑应用。 某些 Azure 虚拟网络使用专用终结点 (Azure Private Link) 以便提供对 azure PaaS 服务（例如 Azure 存储、Azure Cosmos DB、Azure SQL 数据库、合作伙伴服务或托管在 Azure 上的客户服务）的访问权限。 如果逻辑应用需要访问使用专用终结点的虚拟网络，则必须在 ISE 中创建、部署和运行这些逻辑应用。

创建 ISE 时，可以选择使用内部或外部访问终结点。 你的选择将确定 ISE 中逻辑应用的请求或 webhook 触发器是否可以从虚拟网络外部接收调用。 内部和外部访问终结点也会影响您是否可以查看逻辑应用的运行历史记录，包括运行的输入和输出，以及虚拟网络的内部或外部。

请确保与你的 ISE 相关的所有虚拟网络子网部署均使用特定于应用程序受信任端口和源的网络访问控制来应用网络安全组。 在 ISE 中部署逻辑应用时，请使用 "专用链接"。 使用 azure 专用链接，可以通过虚拟网络中的专用终结点访问 Azure PaaS 服务和 Azure 托管的客户拥有/合作伙伴服务。 或者，如果你有特定用例，则可以通过实现 Azure 防火墙来满足此要求。 若要在设置安全规则时帮助降低复杂性，请使用服务标记来表示特定 Azure 服务的 IP 地址前缀组。

- [了解逻辑应用的连接器](../connectors/apis-list.md)

- [了解 Azure 中的服务标记](../virtual-network/service-tags-overview.md)

- [使用 integration service 环境 (ISEs) 了解 Azure 逻辑应用对 Azure 虚拟网络资源的访问权限 ](connect-virtual-network-vnet-isolated-environment-overview.md)

- [了解虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)

- [了解 Azure 专用链接](../private-link/private-link-overview.md)

- [了解 ISE 终结点访问](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何为 ISE 启用访问权限](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：如果在 integration service 环境中运行逻辑应用 (ISE) 使用外部访问点，则可以使用网络安全组 (NSG) 来降低数据渗透的风险。 启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 你还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [了解 ISE 终结点访问](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：如果逻辑应用使用接收传入调用或请求的基于请求的触发器（例如请求或 Webhook 触发器），则你可以限制访问权限，以便只有经过授权的客户端才能调用逻辑应用。

如果你正在 (ISE) 的 integration service 环境中运行逻辑应用，请在与 ISE 关联的虚拟网络上启用 DDoS 保护，以防范 DDoS 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

在组织的每个网络边界上部署 Azure 防火墙，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

使用 Azure 安全中心实时网络访问，将 NSG 配置为只能在有限时间内将终结点公开给已批准的 IP 地址。

使用 Azure 安全中心自适应网络强化，推荐基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

- [如何保护对逻辑应用的入站调用](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [如何限制入站 IP 地址](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [如何配置 DDoS 防护](../virtual-network/manage-ddos-protection.md)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [了解 Azure 安全中心集成的威胁情报](/azure/security-center/security-center-alerts-service-layer)

- [了解 Azure 安全中心自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

- [了解 Azure 安全中心实时网络访问控制](../security-center/security-center-just-in-time.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：如果在 integration service 环境中运行逻辑应用 (ISE) 使用外部访问点，则可以使用网络安全组 (NSG) 来降低数据渗透的风险。 启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 你还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

若要提供更多有关网络流量的保护和信息，可以参考仅在每个应用程序网关实例上启用了访问日志时才生成的日志。 可以使用此日志查看应用程序网关访问模式并分析重要信息。 此日志包括调用方的 IP、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。

否则，你可以利用 marketplace 中的第三方解决方案来满足此要求。

- [了解 ISE 终结点访问](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [如何在包含应用程序网关的内部 VNET 中集成 API 管理](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [如何了解 WAF 访问日志](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：从 Azure Marketplace 中选择一个产品/服务，该产品/服务支持具有负载检查功能的 ID/IPS 功能。  如果不需要基于有效负载检查的入侵检测和/或防护，则可以使用包含威胁情报功能的 Azure 防火墙。 基于 Azure 防火墙威胁情报的筛选功能可以发出警报，并拒绝传入和传出已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测和/或拒绝恶意流量。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：如果正在 integration service 环境中运行逻辑应用 (ISE) ，请部署 Azure 应用程序网关。

- [如何在包含应用程序网关的内部 VNET 中集成 API 管理](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [如何将应用程序网关配置为使用 HTTPS](../application-gateway/create-ssl-portal.md) 

- [了解 Azure Web 应用程序网关的第七层负载均衡](../application-gateway/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：对于需要访问 Azure 逻辑应用实例的资源，在网络安全组或 Azure 防火墙上使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名称（例如 LogicApps、LogicAppsManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [有关使用服务标记的详细信息](../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure 逻辑应用实例相关的网络资源定义并实施标准安全配置。 在“Microsoft.Logic”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 逻辑应用实例的网络配置。 还可以利用内置策略定义，例如：

- 应启用逻辑应用的诊断日志

- 应启用 DDoS 防护标准版

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、基于角色的访问控制 (RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [逻辑应用的 Azure Policy 定义列表](/azure/logic-apps/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 逻辑应用实例相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅 [Azure 安全基准：日志记录和监视](/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：对于日志中的时间戳，Microsoft 维护用于 azure 资源的时间源，例如 Azure 逻辑应用。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：若要在运行期间获取有关逻辑应用的更丰富的调试信息，可以设置并使用 Azure Monitor 日志来记录和存储有关运行时数据和事件的信息，例如 Log Analytics 工作区中的触发器事件、运行事件和操作事件。 Azure Monitor 可帮助你监视云和本地环境，使你能够更轻松地保持其可用性和性能。 通过使用 Azure Monitor 日志，可以创建有助于收集和查看此信息的日志查询。 还可以将此诊断数据与其他 Azure 服务一起使用，例如 Azure 存储和 Azure 事件中心。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何为 Azure 逻辑应用设置 Azure Monitor 日志并收集诊断数据](monitor-logic-apps-log-analytics.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：若要在运行期间获取有关逻辑应用的更丰富的调试信息，可以设置并使用 Azure Monitor 日志来记录和存储有关运行时数据和事件的信息，例如 Log Analytics 工作区中的触发器事件、运行事件和操作事件。 Azure Monitor 可帮助你监视云和本地环境，使你能够更轻松地保持其可用性和性能。 通过使用 Azure Monitor 日志，可以创建有助于收集和查看此信息的日志查询。 还可以将此诊断数据与其他 Azure 服务一起使用，例如 Azure 存储和 Azure 事件中心。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何为 Azure 逻辑应用设置 Azure Monitor 日志并收集诊断数据](monitor-logic-apps-log-analytics.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在创建并运行逻辑应用后，可以检查逻辑应用的运行状态、运行历史记录、触发器历史记录和性能。 若要进行实时事件监视和更丰富的调试，可以使用 Azure Monitor 日志为逻辑应用设置诊断日志记录。 此 Azure 服务可帮助你监视云和本地环境，使你能够更轻松地维持其可用性和性能。 然后，可以查找和查看事件，例如触发事件、运行事件和操作事件。 将此信息存储在 Azure Monitor 日志中，可以创建日志查询来帮助查找和分析此信息。 还可以将此诊断数据与其他 Azure 服务一起使用，例如 Azure 存储和 Azure 事件中心。

在 Azure Monitor 中，根据组织的合规性规章，为与 Azure 逻辑应用实例关联的日志设置日志保留期。

- [如何监视 Azure 逻辑应用的运行状态、查看其触发历史记录并为其设置警报](monitor-logic-apps.md)

- [如何设置日志保留参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：若要为逻辑应用设置日志记录，可以在创建逻辑应用时启用 Log Analytics，也可以在现有逻辑应用的 Log Analytics 工作区中安装逻辑应用管理解决方案。 此解决方案为逻辑应用运行提供聚合信息，它包括状态、执行时间、重新提交状态和相关 ID 等具体的详细信息。 然后，若要为此信息启用日志记录和创建查询的功能，请设置 Azure Monitor 日志。

也可启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据可能已为 Azure 逻辑应用收集的活动日志数据提供许多其他见解。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何为 Azure 逻辑应用设置 Azure Monitor 日志并收集诊断数据](monitor-logic-apps-log-analytics.md)

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何在 Azure Monitor 中的 Log Analytics 中收集和分析 Azure 活动日志](/azure/azure-monitor/platform/activity-log-collect)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用 Azure 安全中心和 Log Analytics 监视安全日志和事件中的异常活动并发出警报。

或者，你可以将和机载数据启用到 Azure Sentinel。

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：不适用；Azure 逻辑应用不会处理或生成与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用；Azure 逻辑应用不会处理或生成与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅 [Azure 安全基准：标识和访问控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

若要在不登录的情况下轻松访问受 Azure Active Directory (Azure AD) 保护的其他资源并对你的标识进行身份验证，逻辑应用可以使用托管标识（以前称为托管服务标识或 MSI），而非使用凭据或密码。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。

逻辑应用的每个请求终结点在终结点的 URL 中都有一个共享访问签名 (SAS)。 如果与其他各方共享基于请求的触发器的终结点 URL，可以生成使用特定密钥并具有过期日期的回调 URL。 这样，就可以无缝滚动更新密钥，或者根据特定的时间跨度将触发逻辑应用的访问限制。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [在 Azure 逻辑应用中使用托管标识验证对 Azure 资源的访问权限](create-managed-service-identity.md)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 SAS 在 Azure 逻辑应用中保护访问权限和数据](logic-apps-securing-a-logic-app.md#sas)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory 和 Azure 逻辑应用没有默认密码的概念。

如果使用基本身份验证，则需指定用户名和密码。 创建这些凭据时，请确保为身份验证配置强密码。

如果使用基础结构即代码，请避免在代码中存储密码，而应改用 Azure Key Vault 来存储和检索凭据。

- [如何在逻辑应用中保护和访问数据](logic-apps-securing-a-logic-app.md)

- [如何在 Azure Key Vault 中设置和检索机密](../key-vault/secrets/quick-create-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

- [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：使用 Azure 应用注册（服务主体）来检索令牌，该令牌可用于通过 API 调用与恢复服务保管库进行交互。

许多连接器还要求先与目标服务或系统建立连接，并提供身份验证凭据或其他配置详细信息，然后，你才能在逻辑应用中使用某个触发器或操作。 例如，必须授权与 Twitter 帐户建立连接才能访问数据，或者让系统代表你发贴。

对于使用 Azure Active Directory (Azure AD) OAuth 的连接器，创建连接意味着要登录到服务（例如 Office 365、Salesforce 或 GitHub），其中的访问令牌已加密并安全地存储在 Azure 机密存储中。 其他连接器（例如 FTP 和 SQL）需要提供包含服务器地址、用户名和密码等配置详细信息的连接。 这些连接配置详细信息同样加密并安全存储。

- [如何调用 Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [如何将客户端应用程序注册到 Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [工作流触发器 API 信息](/rest/api/logic/workflowtriggers)

- [了解连接器配置](../connectors/apis-list.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (AD) 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理的建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指南**：使用配置了多重身份验证 (MFA) 的特权访问工作站 (PAW) 来登录并配置 Azure 资源。

- [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

另外，逻辑应用的每个请求终结点在终结点的 URL 中都有一个共享访问签名 (SAS)。 你可以对逻辑应用进行限制，让其仅接受来自某些 IP 地址的请求。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [了解如何在逻辑应用中限制入站 IP 地址](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (AD) 作为 Azure 逻辑应用实例的中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

如果逻辑应用中支持，则可使用托管标识在不登录的情况下轻松访问受 Azure Active Directory (Azure AD) 保护的其他资源并对你的标识进行身份验证，不必使用凭据或机密。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。

Azure 逻辑应用支持系统分配的和用户分配的托管标识。 逻辑应用可以使用系统分配的标识，也可以使用单个用户分配的标识，可在一组逻辑应用之间共享，但不能同时使用两者。 目前，只有特定的内置触发器和操作支持托管标识，托管的连接器或连接则不支持托管标识，例如：

-  HTTP

-  Azure Functions

-  Azure API 管理

-  Azure 应用服务 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [在 Azure 逻辑应用中使用托管标识验证对 Azure 资源的访问权限](create-managed-service-identity.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：使用 Azure Active Directory (AD) 作为 Azure 逻辑应用实例的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

你可以访问 Azure AD 登录活动、审核和风险事件日志源，以便与 Azure Sentinel 或第三方 SIEM 集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：可使用 Azure AD 风险和标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。 

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：目前不适用；Azure 逻辑应用尚不支持客户密码箱。

- [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅 [Azure 安全基准：数据保护](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**： Microsoft 部署和管理在 "全局" 多租户逻辑应用服务中运行的连接器。 这些连接器提供了用于访问云服务和/或本地系统（包括 Office 365、Azure Blob 存储、SQL Server、Dynamics、Salesforce、SharePoint 等）的触发器和操作。

对于需要直接访问 Azure 虚拟网络中的资源的逻辑应用，你可以 (ISE) 创建集成服务环境，你可以在该环境中生成、部署和运行针对专用资源的逻辑应用。 某些 Azure 虚拟网络使用专用终结点 (Azure 专用链接) 以便提供对 azure PaaS 服务（例如 Azure 存储、Azure Cosmos DB 或 Azure SQL 数据库、合作伙伴服务或托管在 Azure 上的客户服务）的访问权限。 如果逻辑应用需要访问使用专用终结点的虚拟网络，则必须在 ISE 中创建、部署和运行这些逻辑应用。

创建 ISE 时，可以选择使用内部或外部访问终结点。 你的选择将确定 ISE 中逻辑应用的请求或 webhook 触发器是否可以从虚拟网络外部接收调用。

此外，为各个安全域（如环境类型和数据敏感度级别）使用单独的订阅和管理组实现隔离。 你可以限制对应用程序和企业环境所需 Azure 资源的访问级别。 可以通过 Active Directory 基于角色的访问控制来控制对 Azure 资源的访问。

- [了解逻辑应用的连接器](../connectors/apis-list.md)

- [使用集成服务环境 (ISE) 从 Azure 逻辑应用访问 Azure 虚拟网络资源](connect-virtual-network-vnet-isolated-environment-overview.md)

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription) 

- [如何创建管理组](/azure/governance/management-groups/create) 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：目前不适用；数据标识、分类和丢失防护功能尚不适用于 Azure 逻辑应用。

利用 Azure 市场中有关网络外围的第三方解决方案，该解决方案可监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。 

Microsoft 管理 Azure 逻辑应用的底层基础结构，并实施了严格控制以防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 在 Azure 逻辑应用中，逻辑应用运行中的所有数据在传输过程中都使用传输层安全性 (TLS) 进行加密，并且在静态时也是加密的。 查看逻辑应用的运行历史记录时，逻辑应用将对该访问进行身份验证，然后提供指向请求的输入和输出以及每个运行的响应的链接。 但是，对于处理任何密码、机密、密钥或其他敏感信息的操作，你需要阻止他人查看和访问该数据。 例如，如果逻辑应用从 Azure Key Vault 获取机密，以便在对 HTTP 操作进行身份验证时使用，则你需要在视图中隐藏该机密。

对于入站请求，请求触发器仅支持传输层安全性 (TLS) 1.2。 请确保连接到 Azure 资源的任何客户端都能够协商使用 TLS 1.2 或更高版本。 使用 HTTP 连接器的出站调用支持传输层安全性 (TLS) 1.0、1.1 和 1.2。 

请按照 Azure 安全中心的建议，了解静态加密和传输中加密（如果适用）。

- [在 Azure 逻辑应用中保护访问和数据-对基于请求的触发器的入站调用](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [在 Azure 逻辑应用中保护访问和数据-对其他服务和系统的出站调用](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [了解 Azure 的静态数据加密](../security/fundamentals/encryption-atrest.md)

- [设置客户管理的密钥，以便在 Azure 逻辑应用中 (ISEs) 为 integration service 环境加密静态数据](customer-managed-keys-integration-service-environment.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：在 Azure 逻辑应用中，许多触发器和操作都有特定的设置。你可以利用这些设置，通过在逻辑应用的运行历史记录中隐藏输入和/或输出来保护这些数据。

Microsoft 管理 Azure 逻辑应用的底层基础结构，并实施了严格控制以防止客户数据丢失或泄露。

- [保护对运行历史记录数据的访问](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：可以仅允许特定的用户或组运行特定的任务，例如管理、编辑和查看逻辑应用。 若要控制其权限，请使用 Azure 基于角色的访问控制 (RBAC)，以便能够为 Azure 订阅中的成员分配自定义角色或内置角色：

- 逻辑应用参与者：允许管理逻辑应用，但不允许更改其访问权限。
- 逻辑应用操作员：允许读取、启用和禁用逻辑应用，但不允许编辑或更新它们。

若要防止他人更改或删除逻辑应用，可以使用 Azure 资源锁。 此功能可以防止他人更改或删除生产资源。

- [保护对 Azure 逻辑应用操作的访问](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。 Microsoft 管理 Azure 逻辑应用的底层基础结构，并实施了严格控制以防止客户数据丢失或泄露。

- [Azure 客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：Azure 逻辑应用依赖 Azure 存储来存储和自动加密静态数据。 此加密可保护数据，并帮助你履行组织的安全性和合规性承诺。 默认情况下，Azure 存储使用 Microsoft 托管的密钥来加密数据。

当你创建集成服务环境 (ISE) 来托管逻辑应用，并且希望更好地控制 Azure 存储使用的加密密钥时，你可以使用 Azure Key Vault 设置、使用和管理你自己的密钥。 此功能也称为 "创建自己的密钥" (BYOK) ，密钥称为 "客户托管密钥"。

- [在 Azure 逻辑应用中为 integration service 环境加密静态数据](customer-managed-keys-integration-service-environment.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，当 Azure 逻辑应用中以及其他关键或相关资源中发生更改时创建警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅 [Azure 安全基准：漏洞管理](/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：不适用;Microsoft 对支持 Azure 逻辑应用的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：不适用;Microsoft 对支持 Azure 逻辑应用的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：不适用;Microsoft 对支持 Azure 逻辑应用的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="inventory-and-asset-management"></a>库存和资产管理

*有关详细信息，请参阅 [Azure 安全基准：清单和资产管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。  确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：根据组织需求，创建已获批 Azure 资源（例如连接器）以及已获批用于计算资源的软件的清单。

注意：由于 Google 的数据和隐私策略，你只能将 Gmail 连接器与 Google 批准的服务一起使用。 这种情况正在演变，以后可能会影响其他 Google 连接器。

- [所有逻辑应用连接器的列表](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [了解 Gmail 连接器的问题和限制](/connectors/gmail/#known-issues-and-limitations)

- [有关 Google 隐私策略的详细信息](../connectors/connectors-google-data-security-privacy-policy.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。 

使用 Azure Resource Graph 查询/发现订阅中的资源。  确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：业务运营所需的与逻辑应用相关的资源可能会给组织带来较高的风险，应将其隔离在其自己的虚拟机和/或虚拟网络中，并通过 Azure 防火墙或网络安全组进行充分的保护。

业务运营所需的逻辑应用可能会给组织带来较高的风险，应尽可能通过具有特定权限和 RBAC 边界的单独资源组将其隔离。

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md) 

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何创建管理组](/azure/governance/management-groups/create) 

- [如何通过 RBAC 保护对逻辑应用的访问](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅 [Azure 安全基准：安全配置](/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：通过 Azure Policy 为 Azure 逻辑应用实例定义和实现标准安全配置。 在“Microsoft.Logic”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施逻辑应用实例的配置。 例如，可以阻止他人创建或使用与要限制访问的资源的连接。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

另请使用受保护的参数来保护敏感数据和机密。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [在 Azure 逻辑应用中阻止连接器创建的连接](block-connections-connectors.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [如何为 Azure 逻辑应用部署 Azure 资源管理器模板](logic-apps-deploy-azure-resource-manager-templates.md)

- [了解如何保护操作参数](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [有关参数的安全建议](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

通过 Azure Policy 为 Azure 逻辑应用实例定义和实现标准安全配置。 在“Microsoft.Logic”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施逻辑应用实例的配置。 例如，可以阻止他人创建或使用与要限制访问的资源的连接。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

另请确保使用模糊处理来保护运行历史记录中的数据。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [在 Azure 逻辑应用中阻止连接器创建的连接](block-connections-connectors.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [如何为 Azure 逻辑应用部署 Azure 资源管理器模板](logic-apps-deploy-azure-resource-manager-templates.md)

- [保护对运行历史记录输入和输出的访问](logic-apps-securing-a-logic-app.md#obfuscate)

- [保护对参数输入的访问](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [有关参数的安全建议](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对该模板进行检查，以确保配置满足/高于组织的安全要求。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.Logic”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置以及为其发出警报。 使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.Logic”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置以及为其发出警报。 使用 Azure Policy“[审核]”、“[拒绝]”和“[不存在则部署]”自动强制实施 Azure 资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：使用模糊处理保护逻辑应用运行历史记录中的输入和输出。 如果在不同的环境中进行部署，请考虑将逻辑应用的工作流定义中因环境而异的值参数化。 这样，便可以通过使用 Azure 资源管理器模板部署逻辑应用来避免硬编码的数据，通过定义受保护的参数来保护敏感数据，并使用参数文件通过模板的参数以单独输入的形式传递该数据。 可以使用 Key Vault 来存储敏感数据，并在部署时使用受保护的模板参数从 Key Vault 中检索这些值。 然后，可以在参数文件中引用 Key Vault 和机密。 

当你创建集成服务环境 (ISE) 来托管逻辑应用，并且希望更好地控制 Azure 存储使用的加密密钥时，你可以使用 Azure Key Vault 设置、使用和管理你自己的密钥。 此功能也称为 "创建自己的密钥" (BYOK) ，密钥称为 "客户托管密钥"。

- [保护 Azure 逻辑应用的运行历史记录中的输入和输出](logic-apps-securing-a-logic-app.md#obfuscate)

- [有关参数的安全建议](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [在 Azure 逻辑应用中保护对参数输入的访问](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [在部署过程中使用 Azure Key Vault 传递安全的参数值](../azure-resource-manager/templates/key-vault-parameter.md)

- [设置客户管理的密钥，以便在 Azure 逻辑应用中 (ISEs) 为 integration service 环境加密静态数据](customer-managed-keys-integration-service-environment.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：若要在不登录的情况下轻松访问受 Azure Active Directory (Azure AD) 保护的其他资源并对你的标识进行身份验证，逻辑应用可以使用托管标识（以前称为托管服务标识或 MSI），而非使用凭据或密码。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。

目前，只有特定的内置触发器和操作支持托管标识，托管的连接器或连接则不支持托管标识，例如：

- HTTP
- Azure Functions
- Azure API 管理
- Azure 应用服务

- [如何在 Azure 逻辑应用中使用托管标识验证对 Azure 资源的访问权限](create-managed-service-identity.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：使用模糊处理保护逻辑应用运行历史记录中的输入和输出。 如果在不同的环境中进行部署，请考虑将逻辑应用的工作流定义中因环境而异的值参数化。 这样，便可以通过使用 Azure 资源管理器模板部署逻辑应用来避免硬编码的数据，通过定义受保护的参数来保护敏感数据，并使用参数文件通过模板的参数以单独输入的形式传递该数据。 可以使用 Key Vault 来存储敏感数据，并在部署时使用受保护的模板参数从 Key Vault 中检索这些值。 然后，可以在参数文件中引用 Key Vault 和机密。 

也可通过实现凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [保护 Azure 逻辑应用的运行历史记录中的输入和输出](logic-apps-securing-a-logic-app.md#obfuscate)

- [有关参数的安全建议](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [在 Azure 逻辑应用中保护对参数输入的访问](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [在部署过程中使用 Azure Key Vault 传递安全的参数值](../azure-resource-manager/templates/key-vault-parameter.md)

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅 [Azure 安全基准：恶意软件防护](/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。 支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，Azure 逻辑应用) ，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 azure 服务的底层主机上启用了 Microsoft 反恶意软件 (例如，azure 备份) ，但是不会在内容上运行。 

预扫描任何上传到非计算 Azure 资源（例如应用服务、Data Lake Storage、Blob 存储等）的文件。 

使用 Azure 安全中心的数据服务威胁检测来检测上传到存储帐户的恶意软件。 

- [了解适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](../security/fundamentals/antimalware.md)

- [了解 Azure 安全中心的数据服务威胁检测](/azure/security-center/security-center-alerts-data-services)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅 [Azure 安全基准：数据恢复](/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：实现一个灾难恢复 (DR) 解决方案，以便可以保护数据、快速还原用于支持关键业务功能的资源，并使操作持续运行以保持业务连续性 (BC)。

此灾难恢复策略的重点是，将主要逻辑应用设置为故障转移到备用位置中也提供了 Azure 逻辑应用的备用或备份逻辑应用。 这样，在主要逻辑应用发生损毁、中断或故障时，辅助逻辑应用就可以接管工作。 此策略要求辅助逻辑应用和从属资源已在备用位置中部署且准备就绪。

另外，应该将逻辑应用的基础工作流定义扩展到 Azure 资源管理器模板中。 此模板定义了用于预配和部署逻辑应用的基础结构、资源、参数和其他信息。

- [详细了解 Azure 逻辑应用的业务连续性和灾难恢复](business-continuity-disaster-recovery-guidance.md)

- [如何使用 Azure 资源管理器模板将 Azure 逻辑应用部署自动化](logic-apps-azure-resource-manager-templates-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：实现一个灾难恢复 (DR) 解决方案，以便可以保护数据、快速还原用于支持关键业务功能的资源，并使操作持续运行以保持业务连续性 (BC)。

此灾难恢复策略的重点是，将主要逻辑应用设置为故障转移到备用位置中也提供了 Azure 逻辑应用的备用或备份逻辑应用。 这样，在主要逻辑应用发生损毁、中断或故障时，辅助逻辑应用就可以接管工作。 此策略要求辅助逻辑应用和从属资源已在备用位置中部署且准备就绪。

另外，应该将逻辑应用的基础工作流定义扩展到 Azure 资源管理器模板中。 此模板定义了用于预配和部署逻辑应用的基础结构、资源、参数和其他信息。

逻辑应用的每个请求终结点在终结点的 URL 中都有一个共享访问签名 (SAS)。 如果使用 Azure Key Vault 来存储机密，请确保定期自动备份密钥和 URL。

- [详细了解 Azure 逻辑应用的业务连续性和灾难恢复](business-continuity-disaster-recovery-guidance.md)

- [使用 Azure 资源管理器模板将 Azure 逻辑应用部署自动化](logic-apps-azure-resource-manager-templates-overview.md)

- [使用 SAS 确保 Azure 逻辑应用中的访问和数据安全](logic-apps-securing-a-logic-app.md#sas)

- [如何备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：你的灾难恢复策略的重点应该是，将主要逻辑应用设置为故障转移到备用位置中也提供了 Azure 逻辑应用的备用或备份逻辑应用。 这样，在主要逻辑应用发生损毁、中断或故障时，辅助逻辑应用就可以接管工作。 此策略要求辅助逻辑应用和从属资源已在备用位置中部署且准备就绪。

测试对备份的客户管理的密钥进行还原。 请注意，这仅适用于在 integration service 环境上运行的逻辑应用 (ISE) 。

- [详细了解 Azure 逻辑应用的业务连续性和灾难恢复](business-continuity-disaster-recovery-guidance.md)

- [设置客户管理的密钥，以便在 Azure 逻辑应用中 (ISEs) 为 integration service 环境加密静态数据](customer-managed-keys-integration-service-environment.md)

- [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：你的灾难恢复策略的重点应该是，将主要逻辑应用设置为故障转移到备用位置中也提供了 Azure 逻辑应用的备用或备份逻辑应用。 这样，在主要逻辑应用发生损毁、中断或故障时，辅助逻辑应用就可以接管工作。 此策略要求辅助逻辑应用和从属资源已在备用位置中部署且准备就绪。 

保护备份的客户管理的密钥。 请注意，这仅适用于在 integration service 环境上运行的逻辑应用 (ISE) 。

在密钥保管库中启用软删除和清除保护，以防止意外删除或恶意删除密钥。

- [详细了解 Azure 逻辑应用的业务连续性和灾难恢复](business-continuity-disaster-recovery-guidance.md)

- [设置客户管理的密钥，以便在 Azure 逻辑应用中 (ISEs) 为 integration service 环境加密静态数据](customer-managed-keys-integration-service-environment.md)

- [如何在密钥保管库中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅 [Azure 安全基准：事件响应](/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 

此外，请明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅 [Azure 安全基准：渗透测试和 red 团队练习](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
