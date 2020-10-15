---
title: Azure Kubernetes 服务的 azure 安全基线
description: Azure Kubernetes 服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 306b70fb08622d161ab8f150dc5eec3fdbacaeeb
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072860"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure Kubernetes 服务的 azure 安全基线

Azure Kubernetes Service 的 Azure 安全基线包含的建议可帮助你提高部署的安全状况。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：默认情况下，系统会自动创建一个网络安全组和路由表，同时创建 Microsoft Azure Kubernetes 服务 (AKS) 群集。 当通过负载平衡器、端口映射或入口路由创建服务时，AKS 会自动为相应的流量流修改网络安全组。 网络安全组会自动与客户节点上的虚拟 Nic 以及虚拟网络上的子网关联。 

使用 AKS 网络策略来限制网络流量，方法是根据所选的命名空间和标签选择器，定义群集中 Linux pod 间的入口和出口流量规则。 网络策略使用要求使用定义的虚拟网络和子网的 Azure CNI 插件，并且只能在群集创建时启用。 不能将它们部署到现有的 AKS 群集。

你可以实现专用 AKS 群集，以确保 AKS API 服务器与节点池之间的网络流量仅在专用网络上保持。 控制平面或 API 服务器驻留在 AKS 管理的 Azure 订阅中，并使用内部 (RFC1918) IP 地址，而客户的群集或节点池处于自己的订阅中。 服务器和群集或节点池使用 API 服务器虚拟网络中的 Azure 专用链接服务和在客户 AKS 群集的子网中公开的专用终结点之间的通信。  或者，使用 AKS API 服务器的公共终结点，但使用 AKS API 服务器的授权 IP 范围功能限制访问。 

- [Azure Kubernetes Service (AKS) 中的应用程序和群集的安全性概念](concepts-security.md)

- [在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量](use-network-policies.md)

- [创建专用 Azure Kubernetes 服务群集](private-clusters.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**：使用安全中心并按照其网络保护建议，确保 Azure Kubernetes 服务使用的网络资源 (AKS) 群集。 

启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。 你还可以将流日志发送到 Log Analytics 工作区，然后使用流量分析提供对 Azure 云中的流量模式的见解，以可视化网络活动、识别热点和安全威胁、了解流量流模式以及查明网络配置错误。

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

- [如何启用网络安全流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：使用启用了 Azure 应用程序网关的 Web 应用程序防火墙 (WAF) 在 AKS 群集之前，通过将传入流量筛选到你的 Web 应用程序来提供额外的安全层。 Azure WAF 使用开放式 Web 应用程序安全项目提供的一组规则 (OWASP) ，针对攻击（例如，跨站点脚本或 cookie 中毒）提供此类流量。 

使用 API 网关进行身份验证、授权、限制、缓存、转换和监视 AKS 环境中使用的 Api。 API 网关充当微服务的前端，将客户端与微服务分离，并通过消除处理交叉切削问题的负担降低微服务的复杂性。

- [了解网络连接和 AKS 中安全性的最佳做法](operator-best-practices-network.md)

- [应用程序网关入口控制器 ](../application-gateway/ingress-controller-overview.md)

- [将 Azure API 管理与 Azure Kubernetes 服务中部署的微服务配合使用](../api-management/api-management-kubernetes.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在虚拟网络上启用 Microsoft 分布式拒绝服务 (DDoS) 标准保护，其中部署了 Azure Kubernetes SERVICE (AKS) 组件来防范 DDoS 攻击。
安装网络策略引擎，并创建 Kubernetes 网络策略来控制 AKS 中 pod 之间的流量，默认情况下，在这些 pod 之间允许所有流量。 应该只对 AKS 中基于 Linux 的节点和 Pod 使用网络策略。 定义限制 pod 通信以提高安全性的规则。 

选择根据设置（例如分配的标签、命名空间或流量端口）允许或拒绝流量。 所需的网络策略可以自动应用，因为 pod 是在 AKS 群集中动态创建的。 

- [在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量](use-network-policies.md)

- [如何配置 DDoS 防护](../virtual-network/manage-ddos-protection.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：根据需要使用网络观察程序数据包捕获来调查异常活动。 

在订阅中创建或更新虚拟网络时，虚拟网络的区域中会自动启用网络观察程序。 你还可以使用 PowerShell、Azure CLI、REST API 或 ARMClient 方法创建网络观察程序的新实例。

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：使用启用了 Web 应用程序防火墙 (WAF) 的 Azure 应用程序网关保护 Azure Kubernetes SERVICE (AKS) 群集。 

如果不需要基于负载检查或行为分析进行入侵检测和/或防护，则可以使用具有 WAF 的 Azure 应用程序网关，并将其配置为 "检测模式" 以记录警报和威胁，或使用 "防护模式" 主动阻止检测到的入侵和攻击。

- [了解使用 WAF 保护 AKS 群集的最佳做法](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [如何 (Azure WAF 部署 Azure 应用程序网关) ](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：在 AKS 群集前面使用启用了 Azure 应用程序网关的 Web 应用程序防火墙 (WAF) 来筛选传入的流量。 开放式 Web 应用程序安全项目 (OWASP) 提供了一组规则，这些规则在 Azure WAF 中用于监视诸如跨站点脚本或 cookie 中毒这样的攻击。

将完全限定的域名 (FQDN) 标记应用于应用程序，以便于在网络安全组中设置应用程序规则。 设置网络规则后。 使用 FQDN 标记添加应用程序规则，例如 AzureKubernetesService，其中包含可通过 TCP 端口443和端口80访问的所有必需 Fqdn。 

- [了解网络连接和 AKS 中安全性的最佳做法](operator-best-practices-network.md)

- [在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量](use-network-policies.md)

- [如何 (Azure WAF 部署 Azure 应用程序网关) ](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义与 Azure Kubernetes SERVICE (AKS) 实例相关联的网络安全组的网络访问控制。 在创建安全规则时，可以使用服务标记来替换特定的 IP 地址，以便通过指定服务标记名称来允许或拒绝相应服务的流量。 

Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

将 Azure 标记应用到 AKS 群集中的节点池。 它们不同于虚拟网络服务标记，可应用于节点池中的每个节点，并可通过升级持久保存。 

- [了解和使用服务标记](../virtual-network/service-tags-overview.md)

- [了解 AKS 的 Nsg](support-policies.md)

- [控制 Azure Kubernetes 服务 (AKS) 中群集节点的出口流量](limit-egress-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：通过 azure 策略为与 Azure Kubernetes SERVICE (AKS) 群集关联的网络资源定义和实现标准安全配置。 使用 "ContainerService" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 AKS 群集的网络配置。 

另外，请使用与 AKS 相关的内置策略定义，例如：

•应在 Kubernetes Services 上定义授权 IP 范围

•在 Kubernetes 群集中强制执行 HTTPS 入口

•确保服务仅在 Kubernetes 群集中的允许端口上侦听

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对网络安全组和其他资源使用标记，使流量流入和流出 Azure Kubernetes SERVICE (AKS) 群集。 使用单个网络安全组规则的 "描述" 字段来指定业务需求和/或持续时间等，以获取允许进出网络流量的任何规则。
使用任何内置的 Azure 策略标记相关定义，例如 "需要标记及其值"，以确保使用标记创建所有资源并接收现有未标记资源的通知。

选择基于具有网络策略的命名空间和标签选择器允许或拒绝群集内的特定网络路径。 将这些命名空间和标签用作流量配置规则的描述符。 使用 Azure PowerShell 或 Azure 命令行接口 (CLI) ，根据资源的标记查找或执行对资源的操作。

- [带有 CLI 的 Azure 策略](/cli/azure/policy?view=azure-cli-latest)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Kubernetes SERVICE (AKS) 群集相关的网络资源的更改。 

在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。 活动日志中的 AzureContainerService 用户的任何条目都作为平台操作记录。 

使用 Azure Monitor 日志来启用和查询 AKS 主组件 kube-apiserver 和 kube manager 中的日志。 创建和管理使用容器运行时运行 kubelet 的节点，并通过托管 Kubernetes API 服务器部署其应用程序。 

- [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

- [启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志](view-master-logs.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Azure Kubernetes SERVICE (AKS) 节点将 ntp.ubuntu.com 用于时间同步，以及 UDP 端口123和网络时间协议 (ntp) 。 

如果使用自定义 DNS 服务器，请确保可以使用群集节点访问 NTP 服务器。 

- [了解 AKS 群集节点的 NTP 域和端口要求](limit-egress-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：从 Azure Kubernetes Services () AKS 启用审核日志，将其作为托管服务提供 kube-apiserver 和 kube 管理器。 

• kube-auditaksService： control 飞机操作的审核日志中的显示名称 (从 hcpService)  

• masterclient： MasterClientCertificate 审核日志中的显示名称，从 az aks 获取的证书 

• nodeclient： ClientCertificate 的显示名称，由代理节点使用

启用其他审核日志（如 kube）。 

将这些日志导出到 Log Analytics 或其他存储平台。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

基于组织业务要求，启用此数据并将其基于你的 Azure Sentinel 或第三方 SIEM。

- [在此处查看包括日志角色的日志架构](view-master-logs.md)

- [了解容器 Azure Monitor](../azure-monitor/insights/container-insights-overview.md)

- [如何启用容器 Azure Monitor](../azure-monitor/insights/container-insights-onboard.md)

- [启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志](view-master-logs.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：使用活动日志监视 Azure Kubernetes SERVICE (AKS) 资源上的操作，以查看所有活动及其状态。 确定对包含活动日志的订阅中的资源执行了哪些操作：启动了该操作的人员

操作何时发生

操作的状态

其他可能有助于研究操作的属性的值

通过 Azure PowerShell、Azure 命令行接口 (CLI) 、Azure REST API 或 Azure 门户从活动日志中检索信息。 

在 AKS 主组件上启用审核日志，例如： 

• kube-auditaksService： control 飞机操作的审核日志中的显示名称 (从 hcpService)  

• masterclient： MasterClientCertificate 审核日志中的显示名称，从 az aks 获取的证书 

• nodeclient： ClientCertificate 的显示名称，由代理节点使用

同时打开其他审核日志，如 kube-audit。 

- [如何在 AKS 中启用和查看 Kubernetes 主节点日志](view-master-logs.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：启用 Log Analytics 代理的自动安装，以便从 AKS 群集节点收集数据。 同时，从 Azure 安全中心启用 Azure Log Analytics 监视代理的自动预配，默认情况下，自动预配功能处于关闭状态。 还可以手动安装该代理。 启用自动预配后，安全中心将在所有受支持的 Azure Vm 和创建的任何新虚拟机上部署 Log Analytics 代理。 安全中心从 Azure 虚拟机收集数据 (VM) 、虚拟机规模集和 IaaS 容器，如 Kubernetes 群集节点，以监视安全漏洞和威胁。 数据是使用 Azure Log Analytics 代理收集的，它从计算机读取各种安全相关配置和事件日志，并将数据复制到工作区进行分析。 

需要收集数据才能查看缺少的更新、配置错误的 OS 安全设置、endpoint protection 状态和运行状况和威胁检测。

- [如何启用 Log Analytics 代理的自动预配](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：载入 Azure Kubernetes SERVICE (AKS) 实例 Azure Monitor，并根据组织的符合性要求设置相应的 Azure Log Analytics 工作区保持期。 

- [如何为 Log Analytics 工作区设置日志保留参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：载入 Azure Kubernetes SERVICE (AKS) 实例，Azure Monitor 和配置群集的诊断设置。 

使用 Azure Monitor 的 Log Analytics 工作区查看日志数据并执行查询。 Azure Monitor 日志在 Azure 门户中启用和管理，或通过 CLI 进行管理，并同时使用 azure 基于角色的访问控制 (Azure RBAC) 和未启用 RBAC 的 AKS 群集。

查看 AKS 主组件生成的日志 (kube-apiserver 和 kube-controllermanager) ，以便对应用程序和服务进行故障排除。 启用并将数据到 Azure Sentinel 或第三方 SIEM，用于集中日志管理和监视。

- [如何在 AKS 中启用和查看 Kubernetes 主节点日志](view-master-logs.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：使用 Azure Kubernetes SERVICE (AKS) 与安全中心一起，更深入地了解 AKS 节点。 查看安全中心警报，检测到主机和群集级别检测到的威胁和恶意活动。 安全中心实现对 AKS 群集中发生的原始安全事件（如网络数据、进程创建和 Kubernetes 审核日志）的持续分析。 确定此活动是否为预期行为，或者应用程序是否行为异常。 使用 Azure Monitor 中的指标和日志来证实发现结果。 

- [了解 Azure Kubernetes 服务与安全中心的集成](../security-center/defender-for-kubernetes-introduction.md)

- [如何启用 Azure 安全中心标准层](../security-center/security-center-get-started.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：在虚拟机和虚拟机规模集节点上安装并启用适用于 Azure 的 Microsoft 反恶意软件 Kubernetes SERVICE (AKS) 虚拟机和虚拟机规模集节点。 查看安全中心的警报以进行修正。

- [适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](../security/fundamentals/antimalware.md)

- [安全警报参考指南](../security-center/alerts-reference.md)

- [容器警报-Azure Kubernetes Service 群集](../security-center/alerts-reference.md#alerts-akscluster)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**： Azure Kubernetes SERVICE (AKS) 使用 CoreDNS 项目进行群集 DNS 管理和解决。

通过在 coredns-custom ConfigMap 中应用记录的配置来启用 DNS 查询日志记录。 

- [使用 Azure Kubernetes 服务自定义 CoreDNS](coredns-custom.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：在 Azure Kubernetes Service (中使用 kubectl、命令行客户端) 管理 Kubernetes 群集并从 AKS 节点获取其日志，以便进行故障排除。 如果使用 Azure Cloud Shell，则已安装 Kubectl。 若要在本地安装 kubectl，请使用 "AzAksKubectl" cmdlet。

- [快速入门-使用 PowerShell 部署 Azure Kubernetes 服务群集](kubernetes-walkthrough-powershell.md)

- [从 Azure Kubernetes 服务 (AKS) 群集节点获取 kubelet 日志](kubelet-logs.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure Kubernetes SERVICE (AKS) 本身不提供存储常规用户帐户和密码的标识管理解决方案。 使用 Azure Active Directory (Azure AD) 集成，你可以授予用户或组访问命名空间中的 Kubernetes 资源或整个群集的权限。 

执行即席查询，以发现作为 AKS 管理组成员的帐户与 Azure AD PowerShell 模块

将 Azure CLI 用于 "获取托管 Kubernetes 群集的访问凭据" 等操作，有助于定期协调访问。 实现此过程可保持更新的服务帐户清单，这是 AKS 中的另一主要用户类型。 强制实施安全中心的标识和访问管理建议。

- [如何将 AKS 与 Azure AD 集成](./azure-ad-integration-cli.md)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Azure Kubernetes SERVICE (AKS) 不具有常见默认密码的概念，并且不提供可存储常规用户帐户和密码的标识管理解决方案。 使用 Azure Active Directory (Azure AD) 集成，可以授予对命名空间中的 AKS 资源的基于角色的访问权限，也可以授予跨群集的访问权限。 

执行即席查询，以发现作为 AKS 管理组成员的帐户与 Azure AD PowerShell 模块

- [了解 AKS 的访问和标识选项](concepts-identity.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：将 Azure Kubernetes Service 的用户身份验证与 Azure Active Directory (Azure AD) 的) 群集集成 (。 使用 Azure AD 身份验证令牌登录到 AKS 群集。 配置 Kubernetes 基于角色的访问控制 (RBAC) ，用于管理访问 Kubernetes 配置 (kubeconfig) 信息和权限、命名空间和群集资源。 

围绕专用管理帐户的使用创建策略和过程。 实施安全中心标识和访问管理建议。

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

- [控制对群集资源的访问](azure-ad-rbac.md)

- [使用 Azure 基于角色的访问控制](control-kubeconfig-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：将单一登录用于 Azure Kubernetes SERVICE (AKS) ，Azure Active Directory (Azure AD) 集成身份验证 AKS 群集。

- [如何实时查看 Kubernetes 日志、事件和 pod 指标](../azure-monitor/insights/container-insights-livedata-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：将 Azure Kubernetes SERVICE (AKS) 的身份验证与 Azure Active Directory (Azure AD) 集成。 

启用 Azure AD 多重身份验证 (MFA) ，并遵循安全中心的标识和访问管理建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用特权访问工作站 (PAW) ，其中包含多重身份验证 (MFA) ，配置为登录到指定的 Azure Kubernetes 服务 (AKS) 群集和相关资源。
- [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：使用 Azure Active Directory (Azure AD Azure Kubernetes 服务的 Azure AD 集成身份验证 (AKS) 来) 安全报告。 当环境中发生可疑或不安全活动时，可能会生成警报。 使用安全中心来监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置允许从 IP 地址范围或国家/地区的特定逻辑分组 (AKS) 群集访问 Azure Kubernetes Service。 这需要 Azure Active Directory 的集成身份验证 (Azure AD) 。

限制从有限的一组 IP 地址范围访问 AKS API 服务器，因为它收到在群集中执行操作以创建资源或缩放节点数的请求。 

- [使用 Azure Kubernetes 服务 (AKS) 中的已授权 IP 地址范围保护对 API 服务器的访问](api-server-authorized-ip-ranges.md)

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 Azure Active Directory (Azure AD) 作为 Azure Kubernetes 服务的中央身份验证和授权系统 (AKS) 。 Azure AD 通过对静态数据和传输中的数据和 salts、哈希和安全存储用户凭据使用强加密来保护数据。

将 AKS 内置角色与 Azure 基于角色的访问控制配合使用 (Azure RBAC) 资源策略参与者和所有者，用于对 Kubernetes 群集执行策略分配操作

- [Azure Policy 概述](../governance/policy/overview.md)

- [如何将 Azure AD 与 AKS 集成](./azure-ad-integration-cli.md) 

- [集成 AKS 托管 Azure AD](managed-aad.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：使用 Azure Active Directory (Azure AD Azure Kubernetes 服务的 Azure AD 集成身份验证 (AKS) 来) 安全报告。 搜索 Azure AD 日志，以帮助发现陈旧的帐户。 

执行 Azure 身份访问评审以有效地管理组成员身份、访问企业应用程序和角色分配。 从安全中心更正身份和访问建议。

请注意用于支持或故障排除的角色。 例如，Microsoft 支持)  (执行的任何群集操作都是在名称 aks-rolebinding 的内置 Kubernetes "编辑" 角色下进行的。 此角色启用了 AKS 支持，以编辑群集配置和资源，以便对群集问题进行故障排除和诊断。 但是，此角色不能修改权限，也不能创建角色或角色绑定。 仅在具有实时 (JIT) access 的活动支持票证下启用此角色访问。
 
- [Azure Kubernetes 服务 (AKS) 的访问和标识选项](concepts-identity.md)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

- [如何在 Azure 安全中心监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：将 Azure Kubernetes SERVICE (AKS) 的用户身份验证与 Azure Active Directory (Azure AD) 集成。 创建 Azure AD 的诊断设置，将审核和登录日志发送到 Azure Log Analytics 工作区。 配置所需的警报 (例如，当停用的帐户尝试登录 Azure Log Analytics 工作区中的) 时。
- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/platform/alerts-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：将 Azure Kubernetes SERVICE (AKS) 的用户身份验证与 Azure Active Directory (Azure AD) 集成。 使用 Azure AD 的风险检测和标识保护功能，配置对检测到的与用户标识相关的可疑操作的自动响应。 根据业务需要，将数据引入 Azure Sentinel 以便进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：不适用于 Azure Kubernetes 服务 (AKS) ，因为它不受客户密码箱支持。
- [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：在与 Azure Kubernetes Service 相关的资源上使用标记 (AKS) 部署，以帮助跟踪存储或处理敏感信息的 azure 资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [更新托管群集的标记](/rest/api/aks/managedclusters/updatetags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：使用 Azure Kubernetes Service 在同一群集中以逻辑方式隔离团队和工作负荷 (AKS) 提供最少数量的特权，范围限定为每个团队所需的资源。 

使用 Kubernetes 中的命名空间创建逻辑隔离边界。 考虑为隔离和多租户实现附加的 Kubernetes 功能，例如计划、网络、身份验证/授权和容器。

为开发、测试和生产环境实施单独的订阅和/或管理组。 通过将 AKS 群集部署到不同的虚拟网络，将它们部署到不同的虚拟网络，将它们标记为单独。

- [了解 AKS 中群集隔离的最佳实践](operator-best-practices-cluster-isolation.md)

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [了解网络连接和 AKS 中安全性的最佳做法](operator-best-practices-network.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：在网络外围的 Azure Marketplace 中使用第三方解决方案，该解决方案监视敏感信息的未授权传输并在通知信息安全专业人员时阻止此类传输。

Microsoft 管理底层平台，并将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [AKS 功能所需的端口、地址和域名的列表](limit-egress-traffic.md)

- [如何配置 Azure 防火墙的诊断设置](../firewall/firewall-diagnostics.md)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：创建 HTTPS 入口控制器并使用你自己的 TLS 证书 (或（可选）让你的 Azure Kubernetes 服务 (AKS) 部署的) 加密。 

默认情况下，Kubernetes 出口流量通过 HTTPS/TLS 加密。 查看 AKS 实例中任何可能未加密的出口流量，以进行其他监视。 这可能包括 NTP 流量、DNS 流量、用于在某些情况下检索更新的 HTTP 流量。 

- [如何在 AKS 上创建 HTTPS 入口控制器并使用自己的 TLS 证书](ingress-own-tls.md)

- [如何在 AKS 中使用 "允许" 加密创建 HTTPS 入口控制器](ingress-tls.md)

- [AKS 使用的可能传出端口和协议列表](limit-egress-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。
Microsoft 管理底层平台，并将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 

为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指南**：使用 azure 基于角色的访问控制 (在 azure 资源管理器上构建的 azure RBAC) 授权系统，为 azure 资源提供精细的访问管理。

将 Azure Kubernetes Service (AKS) 用于用户身份验证的 Azure Active Directory (Azure AD) 。 使用此配置 Azure AD 身份验证令牌登录到 AKS 群集。 

将 AKS 内置角色用于 Azure RBAC 资源策略参与者和所有者，以便将策略分配操作分配给 AKS 群集

- [如何在 AKS 中使用 Azure RBAC 和 Azure AD 标识控制对群集资源的访问权限](azure-ad-rbac.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。
Microsoft 管理底层平台，并将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：为 Azure Kubernetes Service 中的卷提供的两种主要存储类型 (AKS) 由 azure 磁盘或 azure 文件提供支持。 这两种类型的存储都使用 Azure 存储服务加密 (SSE) ，这会对静态数据进行加密以提高安全性。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。

使用客户托管密钥的静态加密可用于加密 AKS 群集上的 OS 和数据磁盘，以对加密密钥进行更多的控制。 客户负责关键管理活动，如密钥备份和轮换。 目前无法使用 AKS 节点级别的 Azure 磁盘加密对磁盘进行加密。

- [有关 Azure Kubernetes 服务 (AKS) 中的存储和备份的最佳做法](operator-best-practices-storage.md)

- [对 Azure Kubernetes Service (AKS) 中的 Azure 磁盘使用自带密钥 (BYOK)](azure-disk-customer-managed-keys.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：使用容器 Azure Monitor 来监视部署到 Azure Kubernetes SERVICE (AKS) 上托管 Kubernetes 群集的容器工作负荷的性能。 

当节点或容器上的 CPU 和内存使用率超过定义的阈值时，或者当群集中的基础结构或节点运行状况汇总发生状态更改时，为主动通知或日志创建配置警报。 

使用 Azure 活动日志监视 AKS 群集和相关资源。 与 Prometheus 集成，以使用查询查看从节点和 Kubernetes 收集的应用程序和工作负荷指标，以创建自定义警报、仪表板和详细的执行详细分析。

- [了解容器 Azure Monitor](../azure-monitor/insights/container-insights-overview.md)

- [如何为容器启用 Azure Monitor](../azure-monitor/insights/container-insights-onboard.md)

- [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：使用安全中心监视 Azure 容器注册表，包括 Azure Kubernetes SERVICE (AKS) 实例中的漏洞。 启用安全中心中的容器注册表项，以确保安全中心已准备好扫描推送到注册表中的映像。

在安全中心仪表板中收到问题后，请使用 Qualys 扫描映像，然后在安全中心仪表板中收到通知。 容器注册表项绑定功能可以更深入地了解 Azure 资源管理器中使用的映像的漏洞。 

使用安全中心获得每个漏洞的可操作建议。 这些建议包括用于修正的严重性分类和指导原则。 

- [Azure Kubernetes 服务 (AKS) 中容器映像管理和安全性的最佳做法](../security-center/defender-for-container-registries-introduction.md)

- [了解容器映像管理的最佳实践 AKS 中的安全性](operator-best-practices-container-image-management.md)

- [了解容器注册表与 Azure 安全中心的集成](../security-center/defender-for-container-registries-introduction.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：安全更新会自动应用到 Linux 节点，以保护客户的 Azure Kubernetes 服务 (AKS) 群集。 这些更新包括 OS 安全修复项或内核更新。 

请注意，使 Windows Server 节点保持最新状态的过程不同于运行 Linux 的节点，因为 windows server 节点不接收每日更新。 相反，客户需要在其 AKS 群集中的 Windows Server 节点池上执行升级，这将使用 Azure 控制面板或 Azure CLI 通过最新的基本 Windows Server 映像和修补程序来部署新节点。 这些更新包含对 AKS 的安全或功能改进。

- [了解如何将更新应用到运行 Linux 的 AKS 群集节点](node-updates-kured.md)

- [如何升级使用 Windows Server 节点的 AKS 群集的 AKS 节点池](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Azure Kubernetes Service (AKS) 节点映像升级](node-image-upgrade.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动化补丁管理解决方案

**指南**：执行手动过程以确保 Azure Kubernetes SERVICE (AKS) 群集节点的第三方应用程序在群集生存期期间保持修补。 这可能需要启用自动更新、监视节点或定期执行重启。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：按一致的间隔导出安全中心扫描结果，并比较结果以验证是否已修正了漏洞。 

使用 PowerShell cmdlet "AzSecurityTask" 可自动检索安全中心建议你执行的安全任务，从而增强安全状况和修正漏洞扫描结果。

- [如何使用 PowerShell 查看 Azure 安全中心发现的漏洞](/powershell/module/az.security/get-azsecuritytask?view=azps-3.3.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：使用安全中心提供的严重性评级来确定漏洞修正的优先级。 

如果使用 Azure (提供的内置漏洞评估工具) （如 Qualys 或 Rapid7），请使用常见漏洞评分系统 (CVSS) ) 的 (或其他评分系统。

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图查询/发现订阅中的所有资源 (如计算、存储、网络等) 。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅以及订阅中的资源。

虽然可通过资源图发现经典 Azure 资源，但强烈建议创建并使用基于 Azure 资源管理器的资源。

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：使用元数据将标记应用于 Azure 资源，以逻辑方式将它们组织到分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 

 (AKS) 节点池创建 Azure Kubernetes 服务时应用 taints、标签或标记。 该节点池中的所有节点也将继承该破坏、标签或标记。

Taints、标签或标记可用于定期协调清点，并确保从订阅及时删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建标记和用户标记](../azure-resource-manager/management/tag-resources.md)

- [托管群集-更新标记](/rest/api/aks/managedclusters/updatetags)

- [指定节点池的排斥、标签或标记](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指南**：根据组织业务需求，定义用于计算资源的已批准 Azure 资源和批准的软件的列表。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
-   不允许的资源类型 

-   允许的资源类型

使用 Azure 资源关系图查询/发现订阅中的资源。 确保已根据组织业务要求批准了环境中存在的所有 Azure 资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：使用 Azure 自动化更改跟踪和清单功能查找你的环境中安装的软件。 

收集和查看计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的清单，并监视未经批准的软件应用程序。 

跟踪计算机的配置，以帮助查明环境中的操作问题并更好地了解计算机的状态。

- [如何启用 Azure 虚拟机清单](../automation/automation-tutorial-installed-software.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：使用 Azure 自动化更改跟踪和清单功能查找你的环境中安装的软件。 

收集和查看计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的清单，并监视未经批准的软件应用程序。 

跟踪计算机的配置，以帮助查明环境中的操作问题并更好地了解计算机的状态。

- [如何启用 Azure 虚拟机清单](../automation/automation-tutorial-installed-software.md)

- [如何使用文件完整性监视](../security-center/security-center-file-integrity-monitoring.md)

- [了解 Azure 更改跟踪](../automation/change-tracking.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：使用 Azure 自动化更改跟踪和清单功能查找你的环境中安装的软件。 

收集和查看计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的清单，并监视未经批准的软件应用程序。 

跟踪计算机的配置，以帮助查明环境中的操作问题并更好地了解计算机的状态。

在安全中心为环境中存在的应用程序启用自适应应用程序分析。

- [如何启用 Azure 虚拟机清单](../automation/automation-tutorial-installed-software.md)

 
如何使用 Azure 安全中心自适应应用程序
- [控件](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

使用 Azure 资源关系图查询/发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/index.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：使用 Azure 策略对可使用内置策略定义在订阅中创建的资源类型施加限制。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。
- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指南**： Azure Kubernetes SERVICE (AKS) 本身不提供存储常规用户帐户和密码的标识管理解决方案。 相反，使用 Azure Active Directory (Azure AD) 作为 AKS 群集的集成标识解决方案。 

使用 Azure AD 集成向用户或组授予对命名空间或群集中的 Kubernetes 资源的访问权限。 

使用 Azure AD PowerShell 模块执行即席查询，以发现作为 AKS 管理组成员的帐户;定期协调访问。 使用 Azure CLI 来执行 "获取托管 Kubernetes 群集的访问凭据" 等操作。 实施安全中心标识和访问管理建议。

- [用 Azure CLI 管理 AKS](/cli/azure/aks?view=azure-cli-latest)

- [了解 AKS 和 Azure AD 集成](concepts-identity.md)

- [如何将 AKS 与 Azure AD 集成](./azure-ad-integration-cli.md)

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：使用 Azure Kubernetes SERVICE (AKS) 功能在同一群集中以逻辑方式隔离团队和工作负载，以满足每个团队所需的资源的权限。 

在 Kubernetes 中实现命名空间，以创建逻辑隔离边界。 使用 "ContainerService" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure Kubernetes Service (AKS) 实例的配置。 

查看并实现额外的 Kubernetes 功能和隔离和多租户注意事项，包括以下方面：计划、网络、身份验证/授权和容器。 还应使用单独的订阅和/或管理组进行开发、测试和生产。 将 AKS 群集与虚拟网络、适当标记的子网分开，并使用 Web 应用程序防火墙 (WAF) 进行保护。

- [了解 AKS 中群集隔离的最佳实践](operator-best-practices-cluster-isolation.md)

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [了解网络连接和 AKS 中安全性的最佳做法](operator-best-practices-network.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 "ContainerService" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure Kubernetes SERVICE (AKS) 实例的配置。 使用内置的 Azure 策略定义。

AKS 的内置策略定义示例包括：

•在 Kubernetes 群集中强制执行 HTTPS 入口

•应在 Kubernetes Services 上定义授权 IP 范围

基于•的访问控制 (RBAC) 应在 Kubernetes Services 上使用

•确保在 Kubernetes 群集中仅允许使用容器映像

使用 Azure 资源管理器 JavaScript 对象表示法 (JSON) 导出 AKS 配置的模板。 定期审查以确保这些配置符合组织的安全要求。 使用 Azure 安全中心提供的建议作为你的 Azure 资源的安全配置基线。 

- [如何配置和管理 AKS pod 安全策略](use-pod-security-policies.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**： Azure Kubernetes 群集 (AKS) 群集部署在具有安全优化操作系统的主机虚拟机上。 主机操作系统中包含附加的安全强化步骤，以减少攻击面，并允许以安全的方式部署容器。 

Azure 会应用每日修补 (，包括安全修补程序) 到 AKS 虚拟主机，并需要重新启动一些修补程序。 客户负责根据需要计划 AKS 虚拟主机重启。 

- [AKS 代理节点主机 OS 的安全强化](security-hardened-vm-host-image.md)

- [了解 AKS 虚拟主机中的安全性强化](security-hardened-vm-host-image.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 pod 安全策略保护 Azure Kubernetes 服务 (AKS) 群集。  限制可计划哪些 pod 以提高群集的安全性。 

不允许在 AKS 群集中运行的请求资源的 pod。 

同时，使用 Azure 策略 [拒绝] 和 [部署（如果不存在]）效果来强制实施与 AKS 部署相关的 Azure 资源的安全设置 (例如虚拟网络、子网、Azure 防火墙、存储帐户等) 。 

使用以下命名空间中的别名创建自定义 Azure 策略定义： 

• ContainerService

• Microsoft。网络

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**： Azure Kubernetes SERVICE (AKS) 群集部署在具有安全性优化操作系统的主机虚拟机上。 主机操作系统中包含附加的安全强化步骤，以减少攻击面，并允许以安全的方式部署容器。 

请参阅适用于 Internet 安全的中心列表 (CIS) 内置于主机操作系统中的控件。  

- [AKS 代理节点主机 OS 的安全强化](security-hardened-vm-host-image.md)

- [了解 AKS 群集的状态配置](concepts-clusters-workloads.md#control-plane)

- [了解 AKS 虚拟主机中的安全性强化](security-hardened-vm-host-image.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用自定义 Azure 策略定义，请使用 Azure Repos 安全地存储和管理配置。 使用 Azure) 将 Azure Kubernetes Service (AKS) 配置的模板导出到 JavaScript 对象表示法 (JSON 资源管理器。 定期检查以确保配置符合组织的安全要求。 

实现第三方解决方案（如 Terraform），以创建一个配置文件，用于声明 Kubernetes 群集的资源。 可以实现安全最佳做法，并将配置作为代码存储在安全的位置，从而强化 AKS 部署。

- [定义 Kubernetes 群集](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

AKS 代理节点主机 OS 的安全强化

security-hardened-vm-host-image.md

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用于 Azure Kubernetes SERVICE (AKS) 。 默认情况下，AKS 提供安全优化的主机操作系统 (操作系统) 。 当前没有选择备用或自定义操作系统的选项。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：使用 azure 策略对可在订阅中创建的资源类型（使用内置策略定义以及 "ContainerService" 命名空间中的 Azure 策略别名）施加限制。 

创建自定义策略来审核和强制实施系统配置。 开发用于管理策略异常的进程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指南**： Azure Kubernetes SERVICE (AKS) 群集部署在具有安全性优化操作系统的主机虚拟机上。 主机操作系统中包含附加的安全强化步骤，以减少攻击面，并允许以安全的方式部署容器。 

请参阅 AKS 主机中内置的 Internet 安全 (CIS) 控制的中心列表。  

- [AKS 代理节点主机 OS 的安全强化](security-hardened-vm-host-image.md)

- [了解 AKS 虚拟主机中的安全性强化](security-hardened-vm-host-image.md)

- [了解 AKS 群集的状态配置](concepts-clusters-workloads.md#control-plane)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用安全中心执行与 Azure Kubernetes 服务相关资源的基线扫描 (AKS) 部署。 示例资源包括但不限于 AKS 群集本身、部署 AKS 群集的虚拟网络、用于跟踪 Terraform 状态的 Azure 存储帐户，或用于 AKS 群集的 OS 和数据磁盘的加密密钥的 Azure Key Vault 实例。

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：在 "计算应用" 部分下使用安全中心容器建议 &amp; ，对 Azure KUBERNETES 服务 (AKS) 群集执行基线扫描。 找到配置问题或漏洞时，在安全中心仪表板中获得通知。 这确实需要启用可选的容器注册表包，以便安全中心扫描映像。  

- [了解 Azure 安全中心容器建议](../security-center/container-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：使用 FlexVolume 驱动器将 Azure Key Vault 与 Azure Kubernetes SERVICE (AKS) 群集集成。 使用 Azure Key Vault 来存储和定期旋转机密，例如凭据、存储帐户密钥或证书。 FlexVolume 驱动程序允许 AKS 群集以本机方式检索 Key Vault 中的凭据，并仅将其安全地提供给发出请求的 Pod。 使用 pod 托管标识请求对 Key Vault 的访问权限，并通过 FlexVolume 驱动程序检索所需的凭据。 确保启用 Key Vault 软删除。 

不要通过在应用程序代码中定义凭据来限制凭据的泄露。 

避免使用固定或共享凭据。 

- [Azure Kubernetes Service (AKS) 中的应用程序和群集的安全性概念](concepts-security.md)

- [如何将 Key Vault 与 AKS 群集配合使用](developer-best-practices-pod-security.md#limit-credential-exposure)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：不要在应用程序代码中定义凭据作为最佳安全方案。 使用 Azure 资源的托管标识让 pod 对 Azure 中支持它的任何服务（包括 Azure Key Vault）进行身份验证。 将为 pod 分配一个 Azure 标识，用于对 Azure Active Directory 的 () Azure AD 进行身份验证，并收到一个数字令牌，该令牌可显示给其他 Azure 服务，这些服务检查是否有权访问该服务并执行所需的操作。 

请注意，Pod 托管标识仅适用于 Linux pod 和容器映像。 设置 Azure Key Vault 以存储和检索数字密钥和凭据。 用于对 OS 磁盘进行加密的密钥，AKS 群集数据可以存储在 Azure Key Vault 中。

还可以在 AKS 群集中使用服务主体。 但是，使用服务主体的群集最终可能会达到必须续订服务主体以使群集正常运行的状态。 管理服务主体会增加复杂性，这也是托管标识使用起来更简单的原因。 服务主体和托管标识适用相同的权限要求。

- [了解 Azure Kubernetes Service (AKS 的托管标识和 Key Vault) ](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Azure Active Directory Pod 标识](https://github.com/Azure/aad-pod-identity)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描器还鼓励将发现的凭据迁移到更安全的位置，例如 Azure Key Vault 和建议。

不要通过在应用程序代码中定义凭据来限制凭据的泄露。 和避免使用共享凭据。 应使用 Azure Key Vault 来存储和检索数字密钥和凭据。 使用 Azure 资源的托管标识，让 Pod 请求访问其他资源。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Pod 安全性的开发人员最佳方案](developer-best-practices-pod-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反恶意软件

**指南**： AKS 管理代理节点的生命周期和操作，代表你修改与代理节点关联的 IaaS 资源。 但是，对于 Linux 节点，你可以使用守护程序集来安装自定义软件，如反恶意软件解决方案。

- [安全警报参考指南](../security-center/alerts-reference.md)

- [容器警报-Azure Kubernetes Service 群集](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 共享责任和守护程序集](support-policies.md#shared-responsibility)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：预扫描正在上传到 AKS 资源的任何文件。 如果使用 Azure 存储帐户作为数据存储或跟踪 AKS 群集的 Terraform 状态，请使用安全中心的数据服务威胁检测来检测已上传到存储帐户的恶意软件。 

- [了解 Azure 安全中心的数据服务威胁检测](../security-center/azure-defender.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**： AKS 管理代理节点的生命周期和操作，代表你修改与代理节点关联的 IaaS 资源。 但是，对于 Linux 节点，你可以使用守护程序集来安装自定义软件，如反恶意软件解决方案。

- [安全警报参考指南](../security-center/alerts-reference.md)

- [容器警报-Azure Kubernetes Service 群集](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 共享责任和守护程序集](support-policies.md#shared-responsibility)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：使用适用于存储类型（如 Velero）的适当工具备份数据，该工具可以备份持久卷以及其他群集资源和配置。 定期验证这些备份的完整性和安全性。 

在备份之前，从应用程序中删除状态。 如果无法完成此操作，请从永久卷备份数据，并定期测试还原操作，以验证数据完整性和所需的过程。

- [AKS 中存储和备份的最佳实践](operator-best-practices-storage.md)

- [AKS 中业务连续性和灾难恢复的最佳实践](operator-best-practices-multi-region.md)

- [了解 Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [如何在 Azure 上设置 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用适用于存储类型（如 Velero）的适当工具备份数据，该工具可以备份持久卷以及其他群集资源和配置。 

通过 PowerShell 命令定期执行 Key Vault 证书、密钥、托管存储帐户和机密的定期自动备份。 

例如：

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [如何备份 Key Vault 证书](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [如何备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [如何备份 Key Vault 托管存储帐户](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [如何备份 Key Vault 机密](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [如何启用 Azure 备份](../backup/index.yml)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：定期在 Velero 备份中执行内容的数据还原。 如有必要，请测试还原到隔离的虚拟网络。

通过 PowerShell 命令定期执行 Key Vault 证书、密钥、托管存储帐户和机密的数据还原。 

例如：

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [如何还原 Key Vault 证书](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [如何还原 Key Vault 密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [如何还原 Key Vault 托管存储帐户](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [如何还原 Key Vault 机密](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [如何从 Azure 虚拟机备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：使用适用于存储类型（如 Velero）的适当工具备份数据，该工具可以备份持久卷以及其他群集资源和配置。 

如果 Azure Key Vault 与 for Azure Kubernetes Service (AKS) 部署一起使用，则在 Key Vault 中启用用于保护密钥的 Soft-Delete。

- [了解 Azure 存储服务加密](../storage/common/storage-service-encryption.md)

- [如何在 Key Vault 中启用“软删除”](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：确定必须首先调查哪些警报，并将安全中心分配到警报的严重性。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。
清楚地标记订阅 (例如，生产、非生产) 并创建命名系统，以明确标识和分类 Azure 资源。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

请参阅 NIST 发布：测试、培训和练习程序的指南 
- [计划和功能](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 你还可以选择安全中心数据连接器，根据组织业务要求将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

指南：遵循 Microsoft Rules of Engagement 以确保渗透测试不违反 Microsoft 政策： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)