---
title: "Azure 应用程序网关常见问题 | Microsoft 文档"
description: "本页提供有关 Azure 应用程序网关常见问题的解答"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 595d611aac4f57b7ab5b7ab4e46edf1eff796185
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---

# <a name="frequently-asked-questions-for-application-gateway"></a>应用程序网关常见问题

## <a name="general"></a>常规

**问：什么是应用程序网关？**

Azure 应用程序网关是服务形式的应用程序传送控制器 (ADC)，借此为应用程序提供各种第 7 层负载均衡功能。 它提供完全由 Azure 管理的高度可用、可缩放的服务。

**问：应用程序网关支持哪些功能？**

应用程序网关支持 SSL 卸载和端到端 SSL、Web 应用程序防火墙、基于 Cookie 的会话相关性、基于 URL 路径的路由、多站点托管，等等。 有关受支持功能的完整列表，请访问[应用程序网关简介](application-gateway-introduction.md)

**问：应用程序网关与 Azure 负载均衡器之间有什么区别？**

应用程序网关是第 7 层负载均衡器，这意味着，它只处理 Web 流量 (HTTP/HTTPS/WebSocket)。 它支持 SSL 终止、基于 Cookie 的会话相关性以及对流量进行负载均衡的轮循机制等功能。 负载均衡器在第 4 层对流量进行负载均衡 (TCP/UDP)。

**问：应用程序网关支持哪些协议？**

应用程序网关支持 HTTP、HTTPS 和 WebSocket。

**问：目前支持在后端池中添加哪些资源？**

后端池可以包含 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（比如 Azure Web 应用）。 应用程序网关后端池成员不会绑定到可用性集。 后端池的成员可以跨群集、数据中心，或者在 Azure 外部，前提是它们建立了 IP 连接。

**问：该服务已在哪些区域推出？**

应用程序网关已在国际版 Azure 的所有区域推出。 在 [Azure 中国区](https://www.azure.cn/)和 [Azure 政府版](https://azure.microsoft.com/en-us/overview/clouds/government/)中也已推出

**问：应用程序网关是订阅专门的部署，还是在所有客户之间共享？**

应用程序网关是虚拟网络中的专用部署。

**问：是否支持 HTTP 到 HTTPS 的重定向？**

支持重定向。 若要了解详细信息，请访问[应用程序网关重定向概述](application-gateway-redirect-overview.md)。

**问：按什么顺序处理侦听器？**

按侦听器的显示顺序进行处理。 因此，如果基本侦听器与传入请求匹配，它会先处理该请求。  应将多站点侦听器配置在基本侦听器之前，以确保将流量路由到正确的后端。

**问：在何处可以找到应用程序网关的 IP 和 DNS？**

使用公共 IP 地址作为终结点时，可在公共 IP 地址资源中，或者在门户中应用程序网关的“概述”页上找到此信息。 对于内部 IP 地址，可在“概述”页上找到此信息。

**问：在应用程序网关的生存期内，其 IP 或 DNS 是否会变化？**

如果客户停止再启动网关，VIP 可能会变化。 与应用程序网关关联的 DNS 在网关的整个生命周期内不会变化。 出于此原因，建议使用 CNAME 别名并使其指向应用程序网关的 DNS 地址。

**问：应用程序网关是否支持静态 IP？**

应用程序网关不支持静态公共 IP 地址，但支持静态内部 IP。

**问：应用程序网关是否支持在网关上使用多个公共 IP？**

应用程序网关仅支持一个公共 IP 地址。

**问：应用程序网关是否支持 x-forwarded-for 标头？**

支持。应用程序网关会将 x-forwarded-for、x-forwarded-proto 和 x-forwarded-port 标头插入转发到后端的请求中。 x-forwarded-for 标头的格式是逗号分隔的“IP:端口”列表。 x-forwarded-proto 的有效值为 http 或 https。 x-forwarded-port 指定请求抵达应用程序网关时所在的端口。

**问：部署应用程序网关需要多长时间？更新时我的应用程序网关是否仍正常工作？**

预配新应用程序网关部署最多需 20 分钟。 更改实例大小/计数不会出现干扰，且在此期间网关处于活动状态。

## <a name="configuration"></a>配置

**问：是否始终要将应用程序网关部署在虚拟网络中？**

是的，始终要将应用程序网关部署在虚拟网络子网中。 此子网只能包含应用程序网关。

**问：应用程序网关是否能够与其虚拟网络外部的实例通信？**

应用程序网关可与其所在的虚拟网络外部的实例通信，前提是已建立 IP 连接。 如果打算使用内部 IP 作为后端池成员，则需要使用 [VNET 对等互连](../virtual-network/virtual-network-peering-overview.md)或 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

**问：是否可以在应用程序网关子网中部署其他任何组件？**

不可以。但可以在子网中部署其他应用程序网关。

**问：应用程序网关子网是否支持网络安全组？**

应用程序网关子网支持网络安全组，但存在以下限制：

* 必须提交端口 65503-65534 上传入流量的异常，以便后台运行状况正常工作。

* 不能阻止出站 Internet 连接。

* 必须允许来自 AzureLoadBalancer 标记的流量。

**问：应用程序网关有哪些限制？是否可以提高这些限制？**

请访问[应用程序网关限制](../azure-subscription-service-limits.md#application-gateway-limits)查看限制。

**问：是否可以同时对外部和内部流量使用应用程序网关？**

可以。每个应用程序网关支持一个内部 IP 和一个外部 IP。

**问：是否支持 VNet 对等互连？**

是的，支持 VNet 对等互连，这有助于对其他虚拟网络中的流量进行负载均衡。

**问：如果通过 ExpressRoute 或 VPN 隧道连接本地服务器，是否可与这些服务器通信？**

可以，只要允许这种流量。

**问：是否可以使用一个后端池来为不同端口上的许多应用程序提供服务？**

支持微服务体系结构。 需要配置多个 http 设置才能探测不同的端口。

**问：自定义探测是否支持对响应数据使用通配符/正则表达式？**

自定义探测不支持对响应数据使用通配符或正则表达式。 

**问：如何处理规则？**

按配置规则的顺序处理规则。 建议将多站点规则配置在基本规则之前，以降低将流量路由到错误后端的可能性，因为基本规则会在评估多站点规则之前根据端口匹配流量。

**问：如何处理规则？**

规则按照其创建的顺序进行处理。 建议先配置多站点规则，再配置基本规则。 通过先配置多站点侦听器，此配置可以减少将流量路由到错误后端的机会。 由于在评估多站点规则之前，基本规则会基于端口匹配流量，因此可能出现此路由问题。

**问：自定义探测的 Host 字段是什么意思？**

Host 字段指定要将探测数据发送到的名称。 仅在应用程序网关上配置了多站点的情况下适用，否则使用“127.0.0.1”。 此值不同于 VM 主机名，它采用 \<协议\>://\<主机\>:\<端口\>\<路径\> 格式。

**问：我可以将某些源 IP 的应用程序网关访问权限列入允许列表吗？**

对应用程序网关子网使用 NSG 可以完成此方案。 应按列出的优先顺序对子网采取以下限制：

* 允许来自源 IP/IP 范围的传入流量。

* 允许来自所有源的请求传入端口 65503-65534，进行[后端运行状况通信](application-gateway-diagnostics.md)。

* 允许 [NSG](../virtual-network/virtual-networks-nsg.md) 上的传入 Azure 负载均衡器探测（AzureLoadBalancer 标记）和入站虚拟网络流量（VirtualNetwork 标记）。

* 使用“拒绝所有”规则阻止其他所有传入流量。

* 允许所有目的地的 Internet 出站流量。

## <a name="performance"></a>性能

**问：应用程序网关如何支持高可用性和可伸缩性？**

如果已部署两个或更多个实例，则应用程序网关支持高可用性方案。 Azure 将跨更新域和容错域分配这些实例，确保所有实例不会同时发生故障。 为了支持伸缩性，应用程序网关将添加同一网关的多个实例来分担负载。

**问：如何使用应用程序网关实现跨数据中心的灾难恢复方案？**

客户可以使用流量管理器跨不同数据中心内的多个应用程序网关分配流量。

**问：是否支持自动缩放？**

不支持，但应用程序网关提供吞吐量指标，达到阈值时，可使用该指标发出警报。 手动添加实例或更改大小不会重新启动网关，且不会影响现有流量。

**问：手动扩展/缩减是否导致停机？**

不会出现停机，实例将跨升级域和容错域分布。

**问：是否可以在不造成中断的情况下，将实例大小从中型更改为大型？**

可以。Azure 将跨更新域和容错域分配实例，确保所有实例不会同时发生故障。 为了支持缩放，应用程序网关可添加同一网关的多个实例来分担负载。

## <a name="ssl-configuration"></a>SSL 配置

**问：应用程序网关支持哪些证书？**

支持自签名证书、CA 证书和通配符证书。 不支持 EV 证书。

**问：应用程序网关支持哪些最新的加密套件？**

应用程序网关当前支持以下密码套件。 请访问[在应用程序网关上配置 SSL 策略版本和密码套件](application-gateway-configure-ssl-policy-powershell.md)，了解如何自定义 SSL 选项。

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

**问：应用程序网关是否也支持重新加密发往后端的流量？**

是的，应用程序网关支持 SSL 卸载和端到端 SSL，因此支持重新加密发往后端的流量。

**问：是否可以配置 SSL 策略来控制 SSL 协议版本？**

是的，可将应用程序网关配置为拒绝 TLS1.0、TLS1.1 和 TLS1.2。 SSL 2.0 和 3.0 默认已禁用，并且不可配置。

**问：我是否可以配置密码套件和策略顺序？**

是，支持[配置密码套件](application-gateway-ssl-policy-overview.md)。 定义自定义策略时，必须至少启用以下其中一个密码套件。 应用程序网关使用 SHA256 进行后端管理。

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**问：支持多少个 SSL 证书？**

最多支持 20 个 SSL 证书。

**问：支持使用多少个身份验证证书进行后端重新加密？**

最多支持 10 个身份验证证书，默认为 5 个。

**问：应用程序网关是否原生与 Azure Key Vault 集成？**

不是，它没有与 Azure Key Vault 集成。

## <a name="web-application-firewall-waf-configuration"></a>Web 应用程序防火墙 (WAF) 配置

**问：WAF SKU 是否提供标准 SKU 所提供的全部功能？**

是的，WAF 支持标准 SKU 中的所有功能。

**问：应用程序网关支持哪个 CRS 版本？**

应用程序网关支持 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 和 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)。

**问：如何监视 WAF？**

可通过诊断日志记录监视 WAF。有关诊断日志记录的详细信息，请参阅 [Diagnostics Logging and Metrics for Application Gateway](application-gateway-diagnostics.md)（应用程序网关的诊断日志记录和指标）

**问：检测模式是否会阻止流量？**

不会。检测模式仅记录触发了 WAF 规则的流量。

**问：如何自定义 WAF 规则？**

是的，WAF 规则可自定义，有关如何自定义这些规则的详细信息，请访问[自定义 WAF 规则组和规则](application-gateway-customize-waf-rules-portal.md)

**问：目前支持哪些规则？**

WAF 目前支持 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 和 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)，这些规则针对开放 Web 应用程序安全项目 (OWASP) 识别到的 10 大漏洞中的大多数漏洞提供基准安全要求，相关信息请参阅 [OWASP top 10 Vulnerabilities](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)（OWASP 10 大漏洞）

* SQL 注入保护

* 跨站点脚本保护

* 常见 Web 攻击保护，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击

* 防止 HTTP 协议违反行为

* 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头

* 防止自动程序、爬网程序和扫描程序

* 检测常见应用程序错误配置（即 Apache、IIS 等）

**问：WAF 是否也支持 DDoS 防护？**

否，WAF 不提供 DDoS 防护。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

**问：应用程序网关可以使用哪些类型的日志？**

应用程序网关可以使用三种日志。 有关这些日志和其他诊断功能的详细信息，请访问[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。

- **ApplicationGatewayAccessLog**：访问日志包含提交到应用程序网关前端的每个请求。 数据包括调用方的 IP、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。每隔 300 秒会收集一次访问日志。 此日志包含每个应用程序网关实例的一条记录。
- **ApplicationGatewayPerformanceLog**：性能日志捕获每个实例的性能信息，包括提供的请求总数、吞吐量（以字节为单位）、失败的请求计数、正常和不正常的后端实例计数。
- **ApplicationGatewayFirewallLog**：防火墙日志包含通过应用程序网关（配置有 Web 应用程序防火墙）的检测或阻止模式记录的请求。

**问：如何知道后端池成员是否正常？**

可以使用 PowerShell cmdlet `Get-AzureRmApplicationGatewayBackendHealth`，或者在门户中访问[应用程序网关诊断](application-gateway-diagnostics.md)来验证运行状况

**问：什么是诊断日志的保留策略？**

诊断日志将发往客户存储帐户，客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Log Analytics。 有关详细信息，请访问 [Application Gateway Diagnostics](application-gateway-diagnostics.md)（应用程序网关诊断）。

**问：如何获取应用程序网关的审核日志？**

应用程序网关有相应的审核日志。 在门户上的应用程序网关菜单边栏选项卡中单击“活动日志”即可访问审核日志。 

**问：是否可以使用应用程序网关设置警报？**

可以，应用程序网关确实支持警报。可以基于指标设置警报。  应用程序网关目前提供“吞吐量”指标，可以使用它来配置警报。 若要了解有关警报的详细信息，请访问 [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)（接收警报通知）。

**问：后端运行状况返回未知状态，什么原因导致此状态？**

最常见的原因是访问的后端被 NSG 或自定义 DNS 阻止。 有关详细信息，请访问 [Backend health, diagnostics logging, and metrics for Application Gateway](application-gateway-diagnostics.md)（应用程序网关的后端运行状况、诊断日志记录和指标）。

## <a name="next-steps"></a>后续步骤

若要了解有关应用程序网关的详细信息，请访问 [Introduction to Application Gateway](application-gateway-introduction.md)（应用程序网关简介）。

