---
title: Azure 应用程序网关的常见问题
description: 查找有关 Azure 应用程序网关的常见问题的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: 14fe8780bb7919d942da186698275d5199f4586e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770078"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>有关应用程序网关的常见问题

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下面是有关 Azure 应用程序网关的常见问题。

## <a name="general"></a>常规

### <a name="what-is-application-gateway"></a>什么是应用程序网关？

Azure 应用程序网关以服务形式提供应用程序传送控制器（ADC）。 它为应用程序提供了各种第7层负载均衡功能。 此服务高度可用、可缩放，并由 Azure 完全管理。

### <a name="what-features-does-application-gateway-support"></a>应用程序网关支持哪些功能？

应用程序网关支持自动缩放、SSL 卸载和端到端 SSL、web 应用程序防火墙（WAF）、基于 cookie 的会话相关性、基于 URL 路径的路由、多站点托管以及其他功能。 有关受支持功能的完整列表，请参阅[应用程序网关简介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>应用程序网关和 Azure 负载均衡器有何不同？

应用程序网关是第7层负载均衡器，这意味着它仅适用于 web 流量（HTTP、HTTPS、WebSocket 和 HTTP/2）。 它支持 SSL 终止、基于 cookie 的会话相关性和用于负载平衡流量的轮循机制等功能。 负载均衡器在第4层（TCP 或 UDP）对流量进行负载均衡。

### <a name="what-protocols-does-application-gateway-support"></a>应用程序网关支持哪些协议？

应用程序网关支持 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>应用程序网关如何支持 HTTP/2？

请参阅[HTTP/2 支持](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>作为后端池的一部分支持哪些资源？

请参阅[支持的后端资源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)。

### <a name="in-what-regions-is-application-gateway-available"></a>应用程序网关在哪些区域可用？

应用程序网关已在国际版 Azure 的所有区域推出。 它还在[Azure 中国世纪互联](https://www.azure.cn/)和[azure 政府](https://azure.microsoft.com/overview/clouds/government/)版中提供。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>此部署是专用于我的订阅，还是在客户之间共享？

应用程序网关是虚拟网络中的专用部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>应用程序网关是否支持 HTTP 到 HTTPS 的重定向？

支持重定向。 请参阅[应用程序网关重定向概述](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>按什么顺序处理侦听器？

请参阅[侦听器处理顺序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>在哪里找到应用程序网关 IP 和 DNS？

如果使用公共 IP 地址作为终结点，则会在公共 IP 地址资源上找到 IP 和 DNS 信息。 或者在门户中应用程序网关的 "概述" 页上进行查找。 如果使用内部 IP 地址，请在 "概述" 页上查找信息。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>保持活动超时和 TCP 空闲超时的设置有哪些？

 在应用程序网关 v1 SKU 中，keep-alive 超时为120秒。 V2 SKU 的 keep-alive 超时为75秒。 在应用程序网关的前端虚拟 IP （VIP）上，TCP 空闲超时值为4分钟。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>IP 或 DNS 名称是否在应用程序网关的生存期内发生更改？

在应用程序网关 V1 SKU 中，如果停止并启动应用程序网关，VIP 会发生更改。 但是，与应用程序网关关联的 DNS 名称不会在网关的生存期内更改。 由于 DNS 名称不会更改，因此应使用 CNAME 别名，并将其指向应用程序网关的 DNS 地址。 在应用程序网关 V2 SKU 中，你可以将 IP 地址设置为静态，因此 IP 和 DNS 名称将在应用程序网关的生存期内不会更改。 

### <a name="does-application-gateway-support-static-ip"></a>应用程序网关是否支持静态 IP？

是的，应用程序网关 v2 SKU 支持静态公共 IP 地址。 v1 SKU 支持静态内部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>应用程序网关是否支持在网关上使用多个公共 IP？

应用程序网关仅支持一个公共 IP 地址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>应该为应用程序网关创建多大的子网？

请参阅[应用程序网关子网大小注意事项](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>是否可将多个应用程序网关资源部署到单个子网？

可以。 除了给定应用程序网关部署的多个实例，你还可以将另一个唯一的应用程序网关资源预配到包含不同应用程序网关资源的现有子网。

单个子网不能同时支持 Standard_v2 和标准应用程序网关。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>应用程序网关是否支持 x-forwarded-for 标头？

可以。 请参阅[对请求所做的修改](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>部署应用程序网关需要多长时间？ 在更新应用程序网关时，应用程序网关是否正常工作？

预配新的应用程序网关 v1 SKU 部署最多需 20 分钟。 更改实例大小或计数不会造成中断，并且在此期间网关仍处于活动状态。

使用 v2 SKU 的大多数部署需要大约6分钟的时间进行设置。 但是，可能需要更长的时间，具体取决于部署的类型。 例如，在多个可用性区域具有多个实例的部署可能需要超过6分钟。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>是否可以使用 Exchange Server 作为应用程序网关的后端？

不。 应用程序网关不支持电子邮件协议，如 SMTP、IMAP 和 POP3。 

## <a name="performance"></a>性能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>应用程序网关如何支持高可用性和可伸缩性？

当你部署了两个或更多实例时，应用程序网关 v1 SKU 支持高可用性方案。 Azure 跨更新域和容错域分配这些实例，以确保实例不会同时发生故障。 为了支持可伸缩性，v1 SKU 将添加同一网关的多个实例来分担负载。

v2 SKU 可以自动确保新实例分布到各个容错域和更新域中。 如果选择 "区域冗余"，则最新的实例也会分布在可用性区域中，以提供区域性故障复原。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>使用应用程序网关如何实现跨数据中心实现灾难恢复方案？

使用流量管理器将流量分布到不同数据中心内的多个应用程序网关。

### <a name="does-application-gateway-support-autoscaling"></a>应用程序网关是否支持自动缩放？

是，应用程序网关 v2 SKU 支持自动缩放。 有关详细信息，请参阅自动[缩放和区域冗余应用程序网关](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>手动或自动缩放是否会导致停机？

不。 实例将跨升级域和容错域分布。

### <a name="does-application-gateway-support-connection-draining"></a>应用程序网关是否支持连接排出？

可以。 可以在不中断的情况下，设置连接排出以更改后端池内的成员。 有关详细信息，请参阅[应用程序网关的连接排出部分](overview.md#connection-draining)。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可以在不造成中断的情况下，将实例大小从中型更改为大型？

可以。 Azure 跨更新域和容错域分配实例，以确保实例不会同时发生故障。 为了支持缩放，应用程序网关可添加同一网关的多个实例来分担负载。

## <a name="configuration"></a>配置

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>是否始终要将应用程序网关部署在虚拟网络中？

可以。 应用程序网关始终部署在虚拟网络子网中。 此子网只能包含应用程序网关。 有关详细信息，请参阅[虚拟网络和子网要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>应用程序网关是否能够与其虚拟网络外部的实例通信？

只要具有 IP 连接，应用程序网关就可以与它所在的虚拟网络外部的实例通信。 应用程序网关还可以与它所在的订阅之外的实例进行通信。 如果打算使用内部 Ip 作为后端池成员，请使用[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)或[Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在应用程序网关子网中部署其他任何组件？

不。 但你可以在子网中部署其他应用程序网关。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>应用程序网关子网是否支持网络安全组？

请参阅[应用程序网关子网中的网络安全组](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>应用程序网关子网是否支持用户定义的路由？

请参阅[应用程序网关子网中支持的用户定义路由](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>应用程序网关有哪些限制？ 是否可以提高这些限制？

请参阅[应用程序网关限制](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>是否可以同时对外部和内部流量使用应用程序网关？

可以。 应用程序网关支持每个应用程序网关有一个内部 IP 和一个外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>应用程序网关是否支持虚拟网络对等互连？

可以。 虚拟网络对等互连有助于对其他虚拟网络中的流量进行负载均衡。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>当使用 ExpressRoute 或 VPN 隧道连接本地服务器时，是否可以与这些服务器通信？

可以，只要允许这种流量。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>一个后端池可以在不同的端口上为多个应用程序提供服务吗？

支持微服务体系结构。 若要探测不同的端口，需要配置多个 HTTP 设置。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自定义探测是否支持对响应数据创建通配符或正则表达式？

不。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>如何在应用程序网关上处理路由规则？

请参阅[处理规则的顺序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>对于自定义探测，主机字段表示什么？

Host 字段指定在应用程序网关上配置了多站点时将探测发送到的名称。 否则，请使用 "127.0.0.1"。 此值不同于虚拟机主机名。 其格式为 \<协议\>：//\<主机\>：\<端口\>\<路径\>。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>是否可以允许应用程序网关访问仅有几个源 IP 地址？

可以。 请参阅[限制对特定源 ip 的访问权限](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>能否对面向公众的和面向专用的侦听器使用相同的端口？

不。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有可从 v1 SKU 迁移到 v2 SKU 的指导？

可以。 有关详细信息，请参阅[将 Azure 应用程序网关和 Web 应用程序防火墙从 V1 迁移到 v2](migrate-v1-v2.md)。

### <a name="does-application-gateway-support-ipv6"></a>应用程序网关是否支持 IPv6？

应用程序网关 v2 当前不支持 IPv6。 它只能使用 IPv4 在双堆栈 VNet 中运行，但网关子网必须仅使用 IPv4。 应用程序网关 v1 不支持双 stack Vnet。 

## <a name="configuration---ssl"></a>配置-SSL

### <a name="what-certificates-does-application-gateway-support"></a>应用程序网关支持哪些证书？

应用程序网关支持自签名证书、证书颁发机构（CA）证书、扩展验证（EV）证书和通配符证书。

### <a name="what-cipher-suites-does-application-gateway-support"></a>应用程序网关支持哪些密码套件？

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

有关如何自定义 SSL 选项的信息，请参阅[在应用程序网关上配置 ssl 策略版本和密码套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>应用程序网关是否支持重新加密发送到后端的流量？

可以。 应用程序网关支持 SSL 卸载和端到端 SSL，这重新加密了到后端的流量。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可以配置 SSL 策略来控制 SSL 协议版本？

可以。 你可以将应用程序网关配置为拒绝 TLS 1.0、TLS 1.1 和 TLS 1.2。 默认情况下，SSL 2.0 和3.0 已禁用并且不可配置。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>我是否可以配置密码套件和策略顺序？

可以。 在应用程序网关上，可以[配置密码套件](application-gateway-ssl-policy-overview.md)。 若要定义自定义策略，请启用以下至少一个密码套件。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

应用程序网关使用 SHA256 进行后端管理。

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>应用程序网关支持多少个 SSL 证书？

应用程序网关最多支持100个 SSL 证书。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>应用程序网关支持多少个后端重新加密的身份验证证书？

应用程序网关最多支持100个身份验证证书。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>应用程序网关是否原本与 Azure Key Vault 集成？

是的，应用程序网关 v2 SKU 支持 Key Vault。 有关详细信息，请参阅[SSL 终止，并 Key Vault 证书](key-vault-certs.md)。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何实现为 .com 和 .net 站点配置 HTTPS 侦听器？ 

对于多个基于域的（基于主机）路由，你可以创建多站点侦听器，设置使用 HTTPS 作为协议的侦听器，并将侦听器与路由规则关联。 有关详细信息，请参阅[使用应用程序网关托管多个站点](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>是否可以在 .pfx 文件密码中使用特殊字符？

不能，在 .pfx 文件密码中仅使用字母数字字符。

## <a name="configuration---web-application-firewall-waf"></a>配置-web 应用程序防火墙（WAF）

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU 是否提供标准 SKU 中提供的所有功能？

可以。 WAF 支持标准 SKU 中的所有功能。

### <a name="how-do-i-monitor-waf"></a>如何监视 WAF？

通过诊断日志记录监视 WAF。 有关详细信息，请参阅[应用程序网关的诊断日志记录和指标](application-gateway-diagnostics.md)。

### <a name="does-detection-mode-block-traffic"></a>检测模式是否会阻止流量？

不。 检测模式仅记录触发 WAF 规则的流量。

### <a name="can-i-customize-waf-rules"></a>我可以自定义 WAF 规则吗？

可以。 有关详细信息，请参阅[自定义 WAF 规则组和规则](application-gateway-customize-waf-rules-portal.md)。

### <a name="what-rules-are-currently-available-for-waf"></a>哪些规则当前可用于 WAF？

WAF 目前支持 CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229)、 [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)和[3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31)。 这些规则为打开的 Web 应用程序安全项目（OWASP）所标识的大多数前10个漏洞提供基准安全： 

* SQL 注入保护
* 跨站点脚本保护
* 防范常见的 web 攻击，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击
* 防止 HTTP 协议违反行为
* 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头
* 防止自动程序、爬网程序和扫描程序
* 检测常见应用程序错误配置（即 Apache、IIS 等）

有关详细信息，请参阅[OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

### <a name="does-waf-support-ddos-protection"></a>WAF 是否支持 DDoS 保护？

可以。 可以在部署应用程序网关的虚拟网络上启用 DDoS 防护。 此设置确保 Azure DDoS 防护服务还保护应用程序网关虚拟 IP (VIP)。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有可从 v1 SKU 迁移到 v2 SKU 的指导？

可以。 有关详细信息，请参阅[将 Azure 应用程序网关和 Web 应用程序防火墙从 V1 迁移到 v2](migrate-v1-v2.md)。

## <a name="configuration---ingress-controller-for-aks"></a>AKS 的配置入口控制器

### <a name="what-is-an-ingress-controller"></a>什么是入口控制器？

Kubernetes 允许创建 `deployment` 和 `service` 资源，以便在群集内部公开一组 pod。 为了向外部公开同一服务，定义了一个提供负载平衡、SSL 终止和基于名称的虚拟托管的[`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/)资源。
为了满足此 `Ingress` 资源，需要一个入口控制器来侦听对 `Ingress` 资源进行的任何更改并配置负载均衡器策略。

应用程序网关入口控制器允许[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)用作[Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)（也称为 AKS 群集）的入口。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>单个入口控制器实例是否可以管理多个应用程序网关？

目前，入口控制器的一个实例只能关联到一个应用程序网关。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-logs-does-application-gateway-provide"></a>应用程序网关提供了哪些类型的日志？

应用程序网关提供三个日志： 

* **ApplicationGatewayAccessLog**：访问日志包含提交到应用程序网关前端的每个请求。 数据包括调用方的 IP、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。每300秒收集一次访问日志。 每个应用程序网关包含一条记录。
* **ApplicationGatewayPerformanceLog**：性能日志捕获每个应用程序网关的性能信息。 信息包括吞吐量（以字节为单位）、服务请求总数、失败请求计数、正常和不正常的后端实例计数。
* **ApplicationGatewayFirewallLog**：对于使用 WAF 配置的应用程序网关，防火墙日志包含通过检测模式或阻止模式记录的请求。

有关详细信息，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道后端池成员是否正常？

使用 PowerShell cmdlet `Get-AzApplicationGatewayBackendHealth` 或门户验证运行状况。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>诊断日志的保留策略是什么？

诊断日志流向客户的存储帐户。 客户可以根据偏好设置保留策略。 诊断日志还可以发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何获取应用程序网关的审核日志？

在门户中，在应用程序网关的 "菜单" 边栏选项卡上，选择 "**活动日志**" 以访问审核日志。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以使用应用程序网关设置警报？

可以。 在应用程序网关上，会在指标上配置警报。 有关详细信息，请参阅[应用程序网关度量值](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics)和[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析应用程序网关的流量统计信息？

可以通过多种方式查看和分析访问日志。 使用 Azure Monitor 日志、Excel、Power BI 等。

你还可以使用资源管理器模板来安装和运行适用于应用程序网关访问日志的热门[GoAccess](https://goaccess.io/)日志分析器。 GoAccess 提供有价值的 HTTP 流量统计信息，例如独特的访问者、请求的文件、主机、操作系统、浏览器和 HTTP 状态代码。 有关详细信息，请参阅 GitHub 中的 "[资源管理器模板" 文件夹中的自述文件](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>导致后端运行状况返回未知状态的原因是什么？

通常，当网络安全组（NSG）、自定义 DNS 或应用程序网关子网中的用户定义路由（UDR）阻止对后端的访问时，你会看到未知状态。 有关详细信息，请参阅[应用程序网关的后端运行状况、诊断日志记录和指标](application-gateway-diagnostics.md)。

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>是否存在 NSG 流日志不显示允许的流量的情况？

可以。 如果配置与以下方案匹配，则不会在 NSG 流日志中看到允许的流量：
- 你已部署应用程序网关 v2
- 应用程序网关子网上有 NSG
- 已启用该 NSG 上的 NSG 流日志

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>如何实现将应用程序网关 V2 仅用于专用前端 IP 地址？

应用程序网关 V2 目前不支持专用 IP 模式。 它支持以下组合
* 专用 IP 和公共 IP
* 仅限公共 IP

但如果你想要将应用程序网关 V2 仅用于专用 IP，则可以按照以下过程操作：
1. 使用公用和专用前端 IP 地址创建应用程序网关
2. 不要为公共前端 IP 地址创建任何侦听器。 如果没有为其创建侦听器，应用程序网关将不会侦听公共 IP 地址上的任何流量。
3. 为应用程序网关子网创建并附加具有以下配置的[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)（按优先级顺序）：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 允许来自源的流量作为**GatewayManager**服务标记，将目标作为**任何**和目标端口为**65200-65535**。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口通过证书身份验证进行保护（锁定）。 外部实体（包括网关用户管理员）无法在没有适当证书的情况下启动对这些终结点所做的更改
    
    b.保留“数据库类型”设置，即设置为“共享”。 允许来自源的流量作为**AzureLoadBalancer**服务标记，目标和目标端口作为**任何**
    
    c. 将源中的所有入站流量拒绝为**Internet**服务标记，并将目标和目标端口视为**任意**。 在入站规则中为此规则指定*最小优先级*
    
    d.单击“下一步”。 保留默认规则，例如允许 VirtualNetwork 入站，以便不阻止对专用 IP 地址的访问
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 不能阻止出站 Internet 连接。 否则，你将面临日志记录、指标等问题。

仅适用于专用 ip 的 NSG 配置示例：仅限专用 IP 访问的 ![应用程序网关 V2 NSG 配置](./media/application-gateway-faq/appgw-privip-nsg.png)


## <a name="next-steps"></a>后续步骤

若要了解有关应用程序网关的详细信息，请参阅[什么是 Azure 应用程序的网关？](overview.md)。
