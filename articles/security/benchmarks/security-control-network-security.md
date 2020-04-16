---
title: Azure 安全控制 - 网络安全
description: Azure 安全控制网络安全
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408303"
---
# <a name="security-control-network-security"></a>安全控制：网络安全

网络安全建议侧重于指定允许或拒绝访问 Azure 服务的网络协议、TCP/UDP 端口和网络连接的服务。

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | 客户 |

确保所有虚拟网络子网部署都应用了网络安全组，并应用了特定于应用程序的可信端口和源的网络访问控制。 如果可用，请使用专用终结点与专用链接，通过将 VNet 标识扩展到服务，将 Azure 服务资源扩展到虚拟网络。 当专用终结点和专用链接不可用时，请使用服务终结点。 有关服务的特定要求，请参阅该特定服务的安全建议。 

或者，如果您有特定的用例，则可以通过实现 Azure 防火墙来满足要求。

- [了解虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [了解 Azure 专用链接](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建具有安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [如何部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置和流量

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | 客户 |

使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的网络资源。 启用 NSG 流日志并将日志发送到存储帐户以进行流量审核。 您还可以将 NSG 流日志发送到日志分析工作区，并使用流量分析提供有关 Azure 云中的流量流的见解。 流量分析的一些优点是能够可视化网络活动并识别热点、识别安全威胁、了解流量模式和查明网络错误配置。

- [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.3 | 9.5 | 客户 |

在关键 Web 应用程序前面部署 Azure Web 应用程序防火墙 （WAF），以便对传入流量进行额外检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或日志分析工作区。

- [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.4 | 12.3 | 客户 |

在 Azure 虚拟网络上启用 DDoS 标准保护，以防止 DDoS 攻击。 使用 Azure 安全中心集成威胁智能拒绝与已知恶意 IP 地址的通信。

在启用威胁智能后，在每个组织的网络边界部署 Azure 防火墙，并将其配置为针对恶意网络流量"警报和拒绝"。

使用 Azure 安全中心"及时网络"访问来配置 NSG，以在有限时间内将终结点暴露到已批准的 IP 地址。

使用 Azure 安全中心自适应网络强化来建议基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

- [如何配置 DDoS 保护](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [如何部署 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [了解 Azure 安全中心集成威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [了解 Azure 安全中心自适应网络强化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [及时了解 Azure 安全中心 网络访问控制](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5： 记录网络数据包

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.5 | 12.5 | 客户 |

启用网络观察程序数据包捕获以调查异常活动。

- [如何启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基于网络的入侵检测/入侵防御系统（IDS/IPS）

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.6 | 12.6, 12.7 | 客户 |

从 Azure 应用商店中选择支持 IDS/IPS 功能以及有效负载检查功能的产品/服务。  如果不需要基于有效负载检查的入侵检测和/或预防，则可以使用具有威胁智能的 Azure 防火墙。 Azure 防火墙威胁基于智能的筛选可以警报和拒绝来自已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在每个组织的网络边界部署您选择的防火墙解决方案，以检测和/或拒绝恶意流量。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [如何使用 Azure 防火墙配置警报](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 应用程序的流量

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.7 | 12.9, 12.10 | 客户 |

为启用了 HTTPS/SSL 的 Web 应用程序为 Web 应用程序部署 Azure 应用程序网关，这些证书适用于受信任的证书。

- [如何部署应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [如何配置应用程序网关以使用 HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [了解与 Azure Web 应用程序网关的第 7 层负载平衡](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.8 | 1.5 | 客户 |

使用虚拟网络服务标记在网络安全组或 Azure 防火墙上定义网络访问控件。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

您还可以使用应用程序安全组来帮助简化复杂的安全配置。 使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。

- [了解和使用服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [了解和使用应用程序安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.9 | 11.1 | 客户 |

使用 Azure 策略定义和实施网络资源的标准安全配置。

您还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境工件（如 Azure 资源管理器模板、RBAC 控件和策略）来简化大规模 Azure 部署。 您可以将蓝图应用于新订阅，并通过版本控制微调控制和管理。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [用于网络的 Azure 策略示例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.10 | 11.2 | 客户 |

对 NSG 和其他与网络安全和流量流相关的资源使用标记。 对于单个 NSG 规则，使用"描述"字段指定允许流量从网络传输的任何规则的业务需求和/或持续时间（等）。

使用与标记相关的任何内置 Azure 策略定义（如"要求标记及其值"）确保所有资源都使用标记创建，并通知您现有的未标记资源。

您可以使用 Azure PowerShell 或 Azure CLI 查找资源或根据资源标记执行操作。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建具有安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 1.11 | 11.3 | 客户 |

使用 Azure 活动日志监视资源配置并检测对 Azure 资源的更改。 在 Azure 监视器中创建警报，这些警报将在对关键资源的更改时触发。

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 监视器中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[日志记录和监视](security-control-logging-monitoring.md)
