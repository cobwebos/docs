---
title: Azure 安全基准 V2-网络安全
description: Azure 安全基准 V2 网络安全
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 184416794011d259af3568c81e4648d822a2c4a5
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059181"
---
# <a name="security-control-network-security"></a>安全控制：网络安全

网络安全包括用于保护和保护 Azure 网络的控件。 这包括保护虚拟网络、建立专用连接、阻止和减少外部攻击以及保护 DNS。

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-1 | 9.2、9.4、14.1、14.2、14.3 | AC-4、CA-3、SC-7 |

确保所有 Azure 虚拟网络都遵循企业分段原则，该原则符合业务风险。 任何可能会导致组织面临较高风险的系统都应在其自己的虚拟网络中隔离，并使用网络安全组 (NSG) 和/或 Azure 防火墙进行充分的保护。 

根据你的应用程序和企业分段策略，根据网络安全组规则限制或允许内部资源之间的流量。 对于特定的定义良好的应用程序 (如) 的3层应用程序，这可能是高度安全的 "拒绝默认情况下，在异常情况下允许" 方法。 如果有多个应用程序和终结点彼此交互，这种情况可能会无法正常扩展。 你还可以使用 Azure 防火墙，这种情况下，需要对大量企业分段或辐射 (在中心/辐射拓扑) 中进行中心管理。 

使用 Azure 安全中心自适应网络强化建议网络安全组配置，这些配置基于对外部网络流量规则的引用限制端口和源 Ip。

- [如何创建包含安全规则的网络安全组](../../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和配置 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure 安全中心内的自适应网络强化](../../security-center/security-center-adaptive-network-hardening.md)

**责任**：客户

**客户安全利益干系人**：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)     

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-2 | 空值 | CA-3、AC-17、马萨诸塞州 |

使用 Azure ExpressRoute 或 Azure 虚拟专用网络 (VPN) 在归置环境中的 Azure 数据中心和本地基础结构之间创建专用连接。 ExpressRoute 连接不通过公共 internet，它们提供比典型 internet 连接更高的可靠性、更快的速度和更低的延迟。 对于点到站点 VPN 和站点到站点 VPN，你可以使用这些 VPN 选项和 Azure ExpressRoute 的任意组合将本地设备或网络连接到虚拟网络。

若要将 Azure 中的两个或多个虚拟网络连接在一起，请使用虚拟网络对等互连。 对等互连虚拟网络之间的网络流量是专用的，保存在 Azure 主干网络上。 

- [ExpressRoute 连接模型是什么](../../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 概述](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [虚拟网络对等](../../virtual-network/virtual-network-peering-overview.md)

**责任**：客户

**客户安全利益干系人**：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立对 Azure 服务的专用网络访问权限

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-3 | 14.1 | AC-4、CA-3、SC-7 |

使用 Azure 专用链接，无需跨 internet 即可从虚拟网络对 Azure 服务进行专用访问。 在 Azure 专用链接尚不可用的情况下，请使用 Azure 虚拟网络服务终结点。  Azure 虚拟网络服务终结点通过 Azure 主干网络上的优化路由，提供对服务的安全访问。  

除了 Azure 服务提供的身份验证和流量安全之外，专用访问还是其他深层防御措施。 

- [了解 Azure 专用链接](../../private-link/private-link-overview.md)

- [了解虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)

**责任**：客户

**客户安全利益干系人**：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务免受外部网络攻击

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-4 | 9.5、12.3、12。9 | SC-5、SC-7 |

针对外部网络的攻击保护 Azure 资源，包括分布式拒绝服务 (DDoS) 攻击、特定于应用程序的攻击，以及未经请求的恶意 internet 流量。 Azure 为此提供了本机功能：
-   使用 Azure 防火墙保护应用程序和服务免受来自 internet 和其他外部位置的潜在恶意流量的侵害。 

-   使用 Web 应用程序防火墙 (WAF) 功能 Azure 应用程序网关、Azure 前门和 Azure 内容分发网络 (CDN) 来保护应用程序、服务和 Api，防范应用程序层攻击。 

-   通过在 Azure 虚拟网络上启用 DDoS 标准保护，保护资产免受 DDoS 攻击。 

- [Azure 防火墙文档](/azure/firewall/)

- [如何部署 Azure WAF](../../web-application-firewall/overview.md)

- [使用 Azure 门户管理 Azure DDoS 防护标准](../../virtual-network/manage-ddos-protection.md)

**责任**：客户

**客户安全利益干系人**：

无

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (ID/IP) 

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-5 | 12.6、12.7 | SI-4 |

使用基于 Azure 防火墙威胁智能的筛选来发出警报，并/或阻止来自已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。 需要进行负载检查时，可以使用负载检查功能，部署第三方入侵检测/入侵阻止来自 Azure Marketplace 的系统 (ID/IP) 。 另外，还可以使用基于主机的 ID/IP 或基于主机的终结点检测和响应 (EDR) 解决方案与或（而不是基于网络的 ID/IP）结合使用。  

注意：如果你对 IDS/IP 使用有法规或其他要求，请确保始终对其进行调整，以便为你的 SIEM 解决方案提供高质量的警报。 

- [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace 包含第三方 ID 功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR 功能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**责任**：客户

**客户安全利益干系人**：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

利用服务标记和应用程序安全组 (Asg) ，简化网络安全规则。 

使用虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的 "源" 或 "目标" 字段中指定服务标记名称，可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

你还可以使用应用程序安全组来帮助简化复杂的安全配置。 应用程序安全组可将网络安全性配置为应用程序结构的自然扩展，而不是基于网络安全组中的显式 IP 地址来定义策略，这样就可以对虚拟机进行分组，并基于这些组定义网络安全策略。

- [了解并使用服务标记](../../virtual-network/service-tags-overview.md)

- [了解并使用应用程序安全组](/azure/virtual-network/security-overview#application-security-groups)

**责任**：客户

**客户安全利益干系人**：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全域名服务 (DNS) 

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-7 | 空值 | SC-20，SC-21 |

遵循 DNS 安全的最佳做法来防范常见攻击，例如无关联 DNS、DNS amplifications 攻击、DNS 中毒和欺骗等。 

将 Azure DNS 用作权威 DNS 服务时，请确保使用 Azure RBAC 和资源锁对 DNS 区域和记录进行意外或恶意的修改。 

- [Azure DNS 概述](../../dns/dns-overview.md)

- [安全域名系统 (DNS) 部署指南](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [禁止无关联的 DNS 条目，并避免子域接管](../fundamentals/subdomain-takeover.md)

**责任**：客户

**客户安全利益干系人**：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

