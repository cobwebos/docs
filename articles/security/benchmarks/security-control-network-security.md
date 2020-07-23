---
title: Azure 安全控制 - 网络安全
description: Azure 安全控制 - 网络安全
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dad01212be3589af7167082ff22c624fa776772a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82193116"
---
# <a name="security-control-network-security"></a>安全控制：网络安全

网络安全建议侧重于指定允许或拒绝哪些网络协议、TCP/UDP 端口和网络连接服务访问 Azure 服务。

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.1 | 9.2、9.4、14.1、14.2、14.3 | 客户 |

确保所有虚拟网络子网部署都应用了网络安全组，且具有针对应用程序受信任端口和源的网络访问控制。 如果可以，请将专用终结点与专用链接结合使用，通过将 VNet 标识扩展到服务来保护虚拟网络的 Azure 服务资源。 如果无法使用专用终结点和专用链接，请使用服务终结点。 有关特定于服务的要求，请参阅该特定服务的安全建议。 

或者，如果你有特定用例，则可以通过实施 Azure 防火墙来满足要求。

- [了解虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [了解 Azure 专用链接](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [如何部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.2 | 9.3、12.2、12.8 | 客户 |

使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的网络资源。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供有关 Azure 云中流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.3 | 9.5 | 客户 |

在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF)，以对传入的流量进行额外的检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或 Log Analytics 工作区。

- [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.4 | 12.3 | 客户 |

在 Azure 虚拟网络上启用 DDoS 标准保护，防范 DDoS 攻击。 使用 Azure 安全中心集成威胁情报拒绝与已知的恶意 IP 地址的通信。

在组织的每个网络边界上部署 Azure 防火墙，启用威胁情报，并配置为“发出警报并拒绝”恶意网络流量。

使用 Azure 安全中心实时网络访问，将 NSG 配置为只能在有限时间内将终结点公开给已批准的 IP 地址。

使用 Azure 安全中心自适应网络强化，推荐基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

- [如何配置 DDoS 保护](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [如何部署 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [了解 Azure 安全中心集成的威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [了解 Azure 安全中心自适应网络强化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [了解 Azure 安全中心实时网络访问控制](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5：记录网络数据包

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.5 | 12.5 | 客户 |

启用网络观察程序数据包捕获来调查异常活动。

- [如何启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.6 | 12.6、12.7 | 客户 |

从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 ID/IPS 功能。  如果不需要基于有效负载检查的入侵检测和/或防护，则可以使用包含威胁情报功能的 Azure 防火墙。 基于 Azure 防火墙威胁情报的筛选功能可以发出警报，并拒绝传入和传出已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测和/或拒绝恶意流量。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [如何配置 Azure 防火墙警报](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.7 | 12.9、12.10 | 客户 |

为 Web 应用程序部署 Azure 应用程序网关，并为受信任的证书启用 HTTPS/TLS。

- [如何部署应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [如何将应用程序网关配置为使用 HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [了解 Azure Web 应用程序网关的第七层负载均衡](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.8 | 1.5 | 客户 |

在网络安全组或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

还可使用应用程序安全组来帮助简化复杂的安全配置。 使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。

- [了解并使用服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [了解并使用应用程序安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.9 | 11.1 | 客户 |

使用 Azure Policy 为网络资源定义并实施标准安全配置。

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、RBAC 控制措施和策略），来简化大规模的 Azure 部署。 可将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [用于网络的 Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.10 | 11.2 | 客户 |

将标记用于 NSG 以及其他与网络安全和流量流有关的资源。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 1.11 | 11.3 | 客户 |

使用 Azure 活动日志监视网络资源配置，并检测 Azure 资源的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[日志记录和监视](security-control-logging-monitoring.md)
