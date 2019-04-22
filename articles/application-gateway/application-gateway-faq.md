---
title: 有关 Azure 应用程序网关常见问题
description: 查找有关 Azure 应用程序网关常见问题的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: 3c8a2fe9f4486fe4d33754b58f4e7ebec1b3252d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682944"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>有关应用程序网关常见问题

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>常规

### <a name="what-is-application-gateway"></a>什么是应用程序网关？

Azure 应用程序网关以服务形式提供应用程序传送控制器 (ADC)。 它提供各种第 7 层负载平衡功能为应用程序。 此服务是高度可用且可缩放的和由 Azure 完全托管。

### <a name="what-features-does-application-gateway-support"></a>应用程序网关支持哪些功能？

应用程序网关支持自动缩放、 SSL 卸载和端到端 SSL、 web 应用程序防火墙 (WAF)、 基于 cookie 的会话相关性、 URL 基于路径的路由、 多站点托管，和其他功能。 有关受支持功能的完整列表，请参阅[应用程序网关简介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>应用程序网关和 Azure 负载均衡器有何不同？

应用程序网关是第 7 层负载均衡器，这意味着它仅适用于 web 流量 （HTTP、 HTTPS、 WebSocket 和 HTTP/2）。 它支持功能，例如 SSL 终止、 基于 cookie 的会话相关性和轮循机制负载平衡流量。 流量进行负载均衡器进行负载均衡在第 4 层 （TCP 或 UDP）。

### <a name="what-protocols-does-application-gateway-support"></a>应用程序网关支持哪些协议？

应用程序网关支持 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>应用程序网关如何支持 HTTP/2？

请参阅[HTTP/2 支持](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>为后端池的一部分支持哪些资源？

请参阅[支持的后端资源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool)。

### <a name="in-what-regions-is-application-gateway-available"></a>在哪些区域是应用程序网关可用？

应用程序网关已在国际版 Azure 的所有区域推出。 此外，还可以在[Azure 中国 21Vianet](https://www.azure.cn/)并[Azure 政府版](https://azure.microsoft.com/overview/clouds/government/)。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>为此部署专用于我的订阅，或者是其所有客户之间都共享？

应用程序网关是虚拟网络中的专用部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>应用程序网关是否支持 HTTP 到 HTTPS 重定向？

支持重定向。 请参阅[应用程序网关重定向概述](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>按什么顺序处理侦听器？

请参阅[顺序处理侦听器](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>在哪里找到应用程序网关 IP 和 DNS？

如果您使用的公共 IP 地址为终结点，您会发现公共 IP 地址资源上的 IP 和 DNS 信息。 或在门户中，应用程序网关的概述页上找到它。 如果你使用内部 IP 地址，查找概述页上的信息。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>保持活动状态的超时和 TCP 空闲超时的设置有哪些？

 在应用程序网关 v1 SKU 中保持活动状态的超时值为 120 秒。 V2 SKU 的保持活动超时时间为 75 秒。 TCP 空闲超时是前端应用程序网关的虚拟 IP (VIP) 上的 4 分钟默认设置。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>应用程序网关的生存期内可以更改 IP 或 DNS 名称？

如果停止和启动应用程序网关，VIP 可能会变化。 但与应用程序网关关联的 DNS 名称的网关的生存期内不会更改。 因为 DNS 名称不会变化，应使用 CNAME 别名并使其指向应用程序网关的 DNS 地址。

### <a name="does-application-gateway-support-static-ip"></a>应用程序网关是否支持静态 IP？

是的应用程序网关 v2 SKU 支持静态公共 IP 地址。 v1 SKU 支持静态内部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>应用程序网关是否支持在网关上使用多个公共 IP？

应用程序网关支持只有一个公共 IP 地址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>应该为应用程序网关创建多大的子网？

请参阅[应用程序网关的子网大小方面的考虑](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>是否可将多个应用程序网关资源部署到单个子网？

是的。 除了的给定应用程序网关部署的多个实例，可以预配到包含不同的应用程序网关资源的现有子网中另一个唯一的应用程序网关资源。

单个子网不能同时支持 Standard_v2 和标准的应用程序网关。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>应用程序网关是否支持 x-forwarded-for 标头？

是的。 请参阅[对请求进行修改](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>若要部署应用程序网关需要多长时间？ 将在更新时处理我的应用程序网关？

预配新的应用程序网关 v1 SKU 部署最多需 20 分钟。 更改实例大小或计数不是中断性的并且在此期间网关保持活动状态。

使用 v2 SKU 的部署可能需要最多 6 分钟的时间进行设置。

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>可以为应用程序网关的后端使用 Exchange Server？

不。 应用程序网关不支持电子邮件协议，如 SMTP、 IMAP 和 POP3。 

## <a name="performance"></a>性能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>应用程序网关如何支持高可用性和可伸缩性？

您已经部署了两个或多个实例时，应用程序网关 v1 SKU 支持高可用性方案。 Azure 将跨更新域和容错域确保实例不会在同一时间的所有失败分配这些实例。 为了支持可伸缩性，v1 SKU 将添加同一网关的多个实例来分担负载。

v2 SKU 可以自动确保新实例分布到各个容错域和更新域中。 如果您选择区域冗余，最新的实例可还分布在可用性区域提供区域故障复原能力。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>如何实现灾难恢复方案跨数据中心使用应用程序网关？

使用流量管理器将流量分配到不同数据中心内的多个应用程序网关。

### <a name="does-application-gateway-support-autoscaling"></a>应用程序网关是否支持自动缩放？

是，应用程序网关 v2 SKU 支持自动缩放。 有关详细信息，请参阅[自动缩放和区域冗余的应用程序网关 （公共预览版）](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>执行手动纵向或横向缩减导致停机？

不。 实例将跨升级域和容错域分布。

### <a name="does-application-gateway-support-connection-draining"></a>应用程序网关是否支持连接排出？

是的。 您可以设置连接清空更改而无需中断在后端池内的成员。 此安装程序允许您继续发送现有连接到其以前的目标之前该连接将关闭或可配置的超时过期。 连接清空等待仅当前正在进行连接来完成。 应用程序网关不会注意到应用程序会话状态。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可以在不造成中断的情况下，将实例大小从中型更改为大型？

是的。 Azure 将跨更新域和容错域确保实例不会全部在同一时间失败分配实例。 为了支持缩放，应用程序网关可添加同一网关的多个实例来分担负载。

## <a name="configuration"></a>配置

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>是否始终要将应用程序网关部署在虚拟网络中？

是的。 始终在虚拟网络子网中部署应用程序网关。 此子网可以包含仅应用程序网关。 有关详细信息，请参阅[虚拟网络和子网要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>应用程序网关可以与其虚拟网络外部或其订阅外部的实例通信？

只要有 IP 连接，应用程序网关可以与在虚拟网络外部的实例通信。 应用程序网关还可以与它所在的订阅外部的实例通信。 如果打算使用内部 Ip 作为后端池成员，请[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)或[Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在应用程序网关子网中部署其他任何组件？

不。 但是，可以将部署其他应用程序网关的子网中。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>支持对应用程序网关子网的网络安全组？

请参阅[中的网络安全组应用程序网关子网](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>应用程序网关子网是否支持用户定义的路由？

请参阅[用户定义的路由中的应用程序网关子网支持](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>应用程序网关有哪些限制？ 是否可以提高这些限制？

请参阅[应用程序网关限制](../azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>是否可以同时使用应用程序网关的外部和内部流量？

是的。 应用程序网关支持一个内部 IP 和每个应用程序网关的一个外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>应用程序网关是否支持虚拟网络对等互连？

是的。 虚拟网络对等互连可帮助其他虚拟网络中的负载平衡流量。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>可以与服务器通信的本地服务器时它们通过 ExpressRoute 或 VPN 隧道连接？

可以，只要允许这种流量。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>一个后端池可以提供不同的端口上的许多应用程序？

支持微服务体系结构。 若要探测不同的端口上，需要配置多个 HTTP 设置。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自定义探测是否支持通配符或正则表达式对响应数据？

不。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>如何在应用程序网关中处理的路由规则？

请参阅[的处理规则顺序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>自定义探测，是什么主机字段意思？

主机字段指定要发送到探测时已配置多站点上应用程序网关的名称。 否则，请使用"127.0.0.1"。 此值是不同于虚拟机主机名。 其格式是\<协议\>://\<主机\>:\<端口\>\<路径\>。

### <a name="can-i-whitelist-application-gateway-access-to-only-a-few-source-ips"></a>我可以加入允许列表应用程序网关访问权限仅将一些源 Ip 吗？

是的。 请参阅[限制对特定源 Ip 的访问](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>可以对面向公众的零售商和面向专用的侦听器使用相同的端口？

不。

## <a name="configuration---ssl"></a>配置的 SSL

### <a name="what-certificates-does-application-gateway-support"></a>应用程序网关支持哪些证书？

应用程序网关支持自签名的证书、 证书颁发机构 (CA) 证书、 扩展验证 (EV) 证书和通配符证书。

### <a name="what-cipher-suites-does-application-gateway-support"></a>哪些密码套件 does 应用程序网关支持？

应用程序网关支持以下密码套件。 

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

有关如何自定义 SSL 选项的信息，请参阅[配置 SSL 策略版本和应用程序网关上的密码套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>应用程序网关是否支持重新加密到后端的流量？

是的。 应用程序网关支持 SSL 卸载和端到端 SSL 对传递到后端通信。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>可以配置 SSL 策略来控制 SSL 协议版本？

是的。 你可以配置应用程序网关拒绝 TLS1.0、 TLS1.1 和 TLS1.2。 默认情况下，SSL 2.0 和 3.0 已被禁用，并且不是可配置。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>我是否可以配置密码套件和策略顺序？

是的。 在应用程序网关，你可以[配置密码套件](application-gateway-ssl-policy-overview.md)。 若要定义自定义策略，请启用至少一个以下密码套件。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

应用程序网关使用 SHA256 进行后端管理。

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>应用程序网关支持多少个 SSL 证书？

应用程序网关支持最多 100 个 SSL 证书。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>应用程序网关支持多少身份验证证书进行后端重新加密？

应用程序网关支持最多 10 个身份验证证书。 默认值为 5。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>未应用程序网关以本机方式集成与 Azure Key Vault？

不。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何配置 HTTPS 侦听器的.com 和.net 站点？ 

对于多个基于域的 （基于主机的） 路由，可以创建多站点侦听器、 设置了作为协议，使用 HTTPS 的侦听器和路由规则相关联的侦听器。 有关详细信息，请参阅[通过使用应用程序网关托管多个站点](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。 

## <a name="configuration---web-application-firewall-waf"></a>配置-web 应用程序防火墙 (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU 是否提供标准 SKU 中提供的所有功能？

是的。 WAF 支持标准 SKU 中的所有功能。

### <a name="which-crs-versions-does-application-gateway-support"></a>应用程序网关支持 CRS 版本？

应用程序网关支持 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 和 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)。

### <a name="how-do-i-monitor-waf"></a>如何监视 WAF？

通过诊断日志记录监视 WAF。 有关详细信息，请参阅[诊断日志记录和应用程序网关指标](application-gateway-diagnostics.md)。

### <a name="does-detection-mode-block-traffic"></a>检测模式是否会阻止流量？

不。 检测模式仅记录触发了 WAF 规则的流量。

### <a name="can-i-customize-waf-rules"></a>我可以自定义 WAF 规则吗？

是的。 有关详细信息，请参阅[自定义 WAF 规则组和规则](application-gateway-customize-waf-rules-portal.md)。

### <a name="what-rules-are-currently-available-for-waf"></a>目前适用于 WAF 哪些规则？

WAF 目前支持 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229)并[3.0](application-gateway-crs-rulegroups-rules.md#owasp30)。 这些规则提供了针对开放 Web Application Security Project (OWASP) 标识的前 10 个漏洞的大多数基准安全： 

* SQL 注入保护
* 跨站点脚本保护
* 抵御常见 web 攻击，例如命令注入、 HTTP 请求走私、 HTTP 响应拆分和远程文件包含攻击
* 防止 HTTP 协议违反行为
* 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头
* 防止自动程序、爬网程序和扫描程序
* 检测常见应用程序错误配置 （即 Apache、 IIS 等）

有关详细信息，请参阅[OWASP 前 10 个漏洞](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

### <a name="does-waf-support-ddos-protection"></a>WAF 是否支持 DDoS 防护？

是的。 您可以在其中部署应用程序网关的虚拟网络的 DDoS 保护。 此设置可确保 Azure DDoS 保护服务还可以保护应用程序网关的虚拟 IP (VIP)。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-logs-does-application-gateway-provide"></a>应用程序网关提供哪些类型的日志？

应用程序网关提供了三个日志： 

* **ApplicationGatewayAccessLog**:访问日志包含提交到应用程序网关前端的每个请求。 数据包括调用方的 IP、 请求的 URL、 响应延迟、 返回代码和字节和缩小。访问日志收集每隔 300 秒。 它包含一条记录，每个应用程序网关。
* **ApplicationGatewayPerformanceLog**:性能日志捕获每个应用程序网关的性能信息。 信息包括以字节为单位的吞吐量，总请求提供服务，失败的请求计数、 正常和不正常的后端实例计数。
* **ApplicationGatewayFirewallLog**:对于配置了 WAF 的应用程序网关，防火墙日志包含通过检测模式或阻止模式记录的请求。

有关详细信息，请参阅[后端运行状况、 诊断日志和应用程序网关指标](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道后端池成员是否正常？

使用 PowerShell cmdlet 来验证运行状况`Get-AzApplicationGatewayBackendHealth`或门户。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>诊断日志的保留策略是什么？

诊断日志将发往客户的存储帐户。 客户可以将基于其首选项的保留期策略设置。 此外可以将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何获取应用程序网关的审核日志？

在门户中，应用程序网关，菜单边栏选项卡上选择**活动日志**访问审核日志。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以使用应用程序网关设置警报？

是的。 在应用程序网关指标配置警报。 有关详细信息，请参阅[应用程序网关跃点数](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics)并[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析应用程序网关的流量统计信息？

你可以查看和分析多种方式访问日志。 使用 Azure Monitor 日志、 Excel、 Power BI 等。

此外可以使用资源管理器模板，用于安装和运行常见[GoAccess](https://goaccess.io/)日志的应用程序网关访问日志分析器。 GoAccess 提供如唯一访问者、 请求的文件、 主机、 操作系统、 浏览器和 HTTP 状态代码的有价值的 HTTP 流量统计数据。 有关详细信息，在 GitHub 中，请参阅[资源管理器模板文件夹中的自述文件](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>可能导致后端运行状况返回未知的状态？

通常情况下，网络安全组 (NSG)、 自定义 DNS 或应用程序网关子网上用户定义路由 (UDR) 阻止访问后端时，显示未知的状态。 有关详细信息，请参阅[后端运行状况、 诊断日志记录和应用程序网关指标](application-gateway-diagnostics.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关应用程序网关的详细信息，请参阅[什么是 Azure 应用程序网关？](overview.md)。
