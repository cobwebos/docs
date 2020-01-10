---
title: Azure 安全控制-网络安全
description: 安全控制网络安全
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d052226470042d374544de0b7e1ced4ca0f48a14
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564148"
---
# <a name="security-control-network-security"></a>安全控制：网络安全

网络安全建议侧重于指定允许或拒绝哪些网络协议、TCP/UDP 端口和网络连接服务对 Azure 服务的访问。

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用网络安全组或虚拟网络上的 Azure 防火墙保护资源

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.1 | 9.2、9.4、14.1-14。3 | 客户 |

确保所有虚拟网络子网部署都将网络安全组应用到特定于应用程序受信任端口和源的网络访问控制。 使用启用了专用链接的 Azure 服务，在 Vnet 中部署服务，或使用专用终结点进行私下连接。 有关特定于服务的要求，请参阅该服务的安全建议。

或者，如果你有特定用例，则可以通过实现 Azure 防火墙来满足要求。

有关专用链接的一般信息： https://docs.microsoft.com/azure/private-link/private-link-overview

如何创建虚拟网络：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署和配置 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 Vnet、子网和 Nic 的配置和流量

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.2 | 9.3、12。2 | 客户 |

使用 Azure 安全中心并按照网络保护建议来帮助保护 Azure 中的网络资源。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。

如何启用 NSG 流日志：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

了解 Azure 安全中心提供的网络安全：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3：保护关键 web 应用程序

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.3 | 9.5 | 客户 |

在关键 Web 应用程序的前面部署 Azure Web 应用程序防火墙（WAF），以便对传入流量进行额外检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或 Log Analytics 工作区。

如何部署 Azure WAF：

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址的通信

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.4 | 12.3 | 客户 |

在 Azure 虚拟网络上启用 DDoS 标准保护，防范 DDoS 攻击。 使用 Azure 安全中心集成威胁情报拒绝与已知的恶意 IP 地址的通信。

在组织的每个网络边界上部署 Azure 防火墙并启用了威胁情报，并将其配置为 &quot;警报，并拒绝&quot; 恶意网络流量。

使用 Azure 安全中心实时访问网络，以将 Nsg 配置为在有限期限内将终结点限制为批准的 IP 地址。

使用 Azure 安全中心自适应网络强化建议基于实际流量和威胁智能限制端口和源 IPs 的 NSG 配置。

如何配置 DDoS 保护：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

如何部署 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

了解 Azure 安全中心集成威胁情报：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

了解 Azure 安全中心自适应网络强化

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

了解 Azure 安全中心实时网络访问控制

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.5 | 12.5、15。8 | 客户 |

将 NSG 流日志记录到存储帐户中，以生成流记录。 如果需要调查异常活动，请启用网络观察程序数据包捕获。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用网络观察程序： https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统（IDS/IPS）

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.6 | 12.6、12。7 | 客户 |

在组织的每个网络边界上部署 Azure 防火墙并启用了威胁情报，并将其配置为 &quot;警报，并拒绝&quot; 恶意网络流量。

如何部署 Azure 防火墙： https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何配置 Azure 防火墙的警报： https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 web 应用程序的流量

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.7 | 12.9、12.10 | 客户 |

为受信任的证书启用 HTTPS/SSL 的 web 应用程序部署 Azure 应用程序网关。

如何部署应用程序网关： https://docs.microsoft.com/azure/application-gateway/quick-create-portal

如何将应用程序网关配置为使用 HTTPS： https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

了解第7层与 Azure web 应用程序网关的负载平衡： https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：降低网络安全规则的复杂性和管理开销

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.8 | 1.5 | 客户 |

使用虚拟网络服务标记 &nbsp;来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

了解和使用服务标记： https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.9 | 11.1 | 客户 |

通过 Azure 策略定义和实现网络资源的标准安全配置。

你还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、RBAC 控件和策略）来简化大规模的 Azure 部署。 可以将蓝图应用到新的订阅和环境，并通过版本控制来微调控制和管理。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

用于网络的 Azure 策略示例：

https://docs.microsoft.com/azure/governance/policy/samples/#network

如何创建 Azure 蓝图：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.1 | 11.2 | 客户 |

使用 Nsg 的标记和与网络安全和通信流相关的其他资源。 对于单独的 NSG 规则，请使用 &quot;说明&quot; "字段为允许进出网络流量的任何规则指定" 业务需求 "和/或" 持续时间 "（等等）。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何创建虚拟网络：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动工具来监视网络资源配置和检测更改

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 1.11 | 11.3 | 客户 |

使用 Azure 策略来验证（和/或修正）网络资源的配置。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

用于网络的 Azure 策略示例：

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控件：[日志记录和监视](security-control-logging-monitoring.md)