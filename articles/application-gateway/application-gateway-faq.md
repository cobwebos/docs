---
title: 有关 Azure 应用程序网关的常见问题解答
description: 查找有关 Azure 应用程序网关常见问题的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.custom: fasttrack-edit
ms.openlocfilehash: f2cf69b456aeccd6da5ac393b88171fb3bb03cf2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082551"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>有关应用程序网关的常见问题解答

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下是有关 Azure 应用程序网关的常见问题。

## <a name="general"></a>一般信息

### <a name="what-is-application-gateway"></a>什么是应用程序网关？

Azure 应用程序网关以服务形式提供应用程序传送控制器 (ADC)。 它为应用程序提供第 7 层的各种负载均衡功能。 此服务高度可用、可缩放，且完全由 Azure 管理。

### <a name="what-features-does-application-gateway-support"></a>应用程序网关支持哪些功能？

应用程序网关支持自动缩放、SSL 卸载、端到端 SSL、Web 应用程序防火墙 (WAF)、基于 Cookie 的会话相关性、基于 URL 路径的路由、多站点托管和其他功能。 有关受支持功能的完整列表，请参阅[应用程序网关简介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>应用程序网关与 Azure 负载均衡器有何不同？

应用程序网关是第 7 层负载均衡器，这意味着，它只处理 Web 流量（HTTP、HTTPS、WebSocket 和 HTTP/2）。 它支持 SSL 终止、基于 Cookie 的会话相关性以及对流量进行负载均衡的轮循机制等功能。 负载均衡器在第 4 层对流量进行负载均衡（TCP 或 UDP）。

### <a name="what-protocols-does-application-gateway-support"></a>应用程序网关支持哪些协议？

应用程序网关支持 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>应用程序网关如何支持 HTTP/2？

请参阅 [HTTP/2 支持](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>支持在后端池中添加哪些资源？

请参阅[支持的后端资源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)。

### <a name="in-what-regions-is-application-gateway-available"></a>应用程序网关已在哪些区域推出？

应用程序网关已在国际版 Azure 的所有区域推出。 它还在[Azure 中国世纪互联](https://www.azure.cn/)和[azure 政府](https://azure.microsoft.com/overview/clouds/government/)版中提供。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>此部署是专门于订阅，还是在所有客户之间共享？

应用程序网关是虚拟网络中的专用部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>应用程序网关是否支持 HTTP 到 HTTPS 的重定向？

支持重定向。 请参阅[应用程序网关重定向概述](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>按什么顺序处理侦听器？

请参阅[侦听器处理顺序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>在何处可以找到应用程序网关的 IP 和 DNS？

如果使用公共 IP 地址作为终结点，可以在公共 IP 地址资源中找到 IP 和 DNS 信息。 或者可以在门户中应用程序网关的概述页上找到它。 如果使用内部 IP 地址，可在概述页上找到该信息。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Keep-Alive 超时和 TCP 空闲超时的设置是什么？

 在应用程序网关 v1 SKU 中，Keep-Alive 超时为 120 秒。 v2 SKU 的 Keep-Alive 超时为 75 秒。 在应用程序网关的前端虚拟 IP (VIP) 中，TCP 空闲超时默认为 4 分钟。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>在应用程序网关的生存期内，其 IP 或 DNS 名称是否会变化？

在应用程序网关 V1 SKU 中，如果停止并启动应用程序网关，VIP 会发生更改。 但是，与应用程序网关关联的 DNS 名称在网关的整个生存期内不会变化。 由于 DNS 名称不会变化，因此应使用 CNAME 别名并使其指向应用程序网关的 DNS 地址。 在应用程序网关 V2 SKU 中，你可以将 IP 地址设置为静态，因此 IP 和 DNS 名称将在应用程序网关的生存期内不会更改。 

### <a name="does-application-gateway-support-static-ip"></a>应用程序网关是否支持静态 IP？

是，应用程序网关 v2 SKU 支持静态公共 IP 地址。 v1 SKU 支持静态内部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>应用程序网关是否支持在网关上使用多个公共 IP？

一个应用程序网关仅支持一个公共 IP 地址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>应该为应用程序网关创建多大的子网？

请参阅[应用程序网关子网大小注意事项](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>是否可将多个应用程序网关资源部署到单个子网？

可以。 除了提供给定应用程序网关部署的多个实例以外，还可以在包含不同应用程序网关资源的现有子网中预配另一个唯一的应用程序网关资源。

单个子网不支持同时使用 Standard_v2 和标准应用程序网关。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>应用程序网关是否支持 x-forwarded-for 标头？

可以。 请参阅[对请求的修改](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>部署应用程序网关需要多长时间？ 更新时应用程序网关是否仍正常工作？

预配新的应用程序网关 v1 SKU 部署最多需 20 分钟。 更改实例大小或计数不会出现中断，且在此期间网关仍处于活动状态。

大多数使用 v2 SKU 的部署大约需要 6 分钟进行预配。 但是，根据部署类型的不同，可能需要更长的时间。 例如，跨多个具有多个实例的可用性区域的部署可能需要 6 分钟以上的时间。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>使用应用程序网关时，能否将 Exchange 服务器用作后端？

不能。 应用程序网关不支持电子邮件协议，例如 SMTP、IMAP 和 POP3。 

## <a name="performance"></a>性能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>应用程序网关如何支持高可用性和可伸缩性？

如果已部署两个或更多个实例，则应用程序网关 v1 SKU 支持高可用性方案。 Azure 跨更新域和容错域分配这些实例，确保实例不会全部同时发生故障。 为了支持可伸缩性，v1 SKU 将添加同一网关的多个实例来分担负载。

v2 SKU 可以自动确保新实例分布到各个容错域和更新域中。 如果选择“区域冗余”，则最新实例还将分布到各个可用性区域中以提供区域性故障复原能力。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>如何使用应用程序网关实现跨数据中心的灾难恢复方案？

使用流量管理器跨不同数据中心内的多个应用程序网关分配流量。

### <a name="does-application-gateway-support-autoscaling"></a>应用程序网关是否支持自动缩放？

是，应用程序网关 v2 SKU 支持自动缩放。 有关详细信息，请参阅[自动缩放和区域冗余应用程序网关](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>手动纵向扩展或缩减是否会导致停机？

不能。 实例将跨升级域和容错域分布。

### <a name="does-application-gateway-support-connection-draining"></a>应用程序网关是否支持连接排出？

可以。 可设置连接排出以更改后端池内的成员，而无需中断操作。 使用此设置可以持续将现有连接发送到其以前的目标，直到该连接被关闭或可配置的超时已过。 连接排出仅等待当前未完成的连接完成。 应用程序网关不了解应用程序会话状态。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可以在不造成中断的情况下，将实例大小从中型更改为大型？

可以。 Azure 跨更新域和容错域分配实例，确保实例不会全部同时发生故障。 为了支持缩放，应用程序网关可添加同一网关的多个实例来分担负载。

## <a name="configuration"></a>配置

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>是否始终要将应用程序网关部署在虚拟网络中？

可以。 应用程序网关始终部署在虚拟网络子网中。 此子网只能包含应用程序网关。 有关详细信息，请参阅[虚拟网络和子网要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>应用程序网关是否能够与其所在虚拟网络外部或其所在订阅外部的实例通信？

只要建立 IP 连接，应用程序网关就能与其所在的虚拟网络外部的实例进行通信。 应用程序网关还能与其所在订阅外部的实例通信。 如果你打算使用内部 IP 作为后端池成员，请使用[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)或 [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在应用程序网关子网中部署其他任何组件？

不能。 但可以在子网中部署其他应用程序网关。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>应用程序网关子网是否支持网络安全组？

请参阅[应用程序网关子网中的网络安全组](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>应用程序网关子网是否支持用户定义的路由？

请参阅[应用程序网关子网中支持的用户定义的路由](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>应用程序网关有哪些限制？ 是否可以提高这些限制？

请参阅[应用程序网关限制](../azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>是否可以同时对外部和内部流量使用应用程序网关？

可以。 每个应用程序网关支持一个内部 IP 和一个外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>应用程序网关是否支持虚拟网络对等互连？

可以。 虚拟网络对等互连有助于对其他虚拟网络中的流量进行负载均衡。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>如果通过 ExpressRoute 或 VPN 隧道连接本地服务器，是否可与这些服务器通信？

可以，只要允许这种流量。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>是否可以使用一个后端池来为不同端口上的许多应用程序提供服务？

支持微服务体系结构。 若要探测不同的端口，需要配置多个 HTTP 设置。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自定义探测是否支持对响应数据使用通配符或正则表达式？

不能。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>如何在应用程序网关中处理路由规则？

请参阅[规则的处理顺序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>对于自定义探测，Host 字段是什么意思？

在应用程序网关上配置了多站点的情况下，Host 字段指定要将探测发送到的名称。 否则使用“127.0.0.1”。 此值不同于虚拟机主机名。 其格式为 \<协议\>://\<主机\>:\<端口\>\<路径\>。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>能否仅允许应用程序网关访问几个源 IP 地址？

可以。 请参阅[限制对特定源 IP 的访问](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>能否同时对公共和专用侦听器使用同一个端口？

不能。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有从 v1 SKU 迁移到 v2 SKU 的指导？

可以。 有关详细信息，请参阅[将 Azure 应用程序网关和 Web 应用程序防火墙从 v1 迁移到 v2](migrate-v1-v2.md)。


## <a name="configuration---ssl"></a>配置 - SSL

### <a name="what-certificates-does-application-gateway-support"></a>应用程序网关支持哪些证书？

应用程序网关支持自签名证书、证书颁发机构 (CA) 证书、扩展验证 (EV) 证书和通配符证书。

### <a name="what-cipher-suites-does-application-gateway-support"></a>应用程序网关支持哪些加密套件？

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

有关如何自定义 SSL 选项的信息，请参阅[在应用程序网关上配置 SSL 策略版本和加密套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>应用程序网关是否支持重新加密发往后端的流量？

可以。 应用程序网关支持 SSL 卸载和端到端 SSL，因此支持重新加密发往后端的流量。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可以配置 SSL 策略来控制 SSL 协议版本？

可以。 可将应用程序网关配置为拒绝 TLS1.0、TLS1.1 和 TLS1.2。 默认情况下，SSL 2.0 和 3.0 已禁用且不可配置。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>我是否可以配置密码套件和策略顺序？

可以。 在应用程序网关中，可以[配置加密套件](application-gateway-ssl-policy-overview.md)。 若要定义自定义策略，请至少启用下列其中一个加密套件。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

应用程序网关使用 SHA256 进行后端管理。

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>应用程序网关支持多少个 SSL 证书？

应用程序网关最多支持 100 个 SSL 证书。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>应用程序网关支持使用多少个身份验证证书进行后端重新加密？

应用程序网关最多支持100个身份验证证书。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>应用程序网关是否原生与 Azure Key Vault 集成？

是，应用程序网关 v2 SKU 支持 Key Vault。 有关详细信息，请参阅[使用 Key Vault 证书实现 SSL 终止](key-vault-certs.md)。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何配置 .com 和 .net 站点的 HTTPS 侦听器？ 

对于基于多域（基于主机）的路由，可以创建多站点侦听器，设置使用 HTTPS 作为协议的侦听器，然后将侦听器与路由规则相关联。 有关详细信息，请参阅[使用应用程序网关托管多个站点](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>能否在 .pfx 文件密码中使用特殊字符？

不能，.pfx 文件密码中只能使用字母数字字符。

## <a name="configuration---web-application-firewall-waf"></a>配置 - Web 应用程序防火墙 (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU 是否提供标准 SKU 所提供的全部功能？

可以。 WAF 支持标准 SKU 中的所有功能。

### <a name="how-do-i-monitor-waf"></a>如何监视 WAF？

通过诊断日志记录监视 WAF。 有关详细信息，请参阅[应用程序网关的诊断日志记录和指标](application-gateway-diagnostics.md)。

### <a name="does-detection-mode-block-traffic"></a>检测模式是否会阻止流量？

不能。 检测模式仅记录触发 WAF 规则的流量。

### <a name="can-i-customize-waf-rules"></a>我可以自定义 WAF 规则吗？

可以。 有关详细信息，请参阅[自定义 WAF 规则组和规则](application-gateway-customize-waf-rules-portal.md)。

### <a name="what-rules-are-currently-available-for-waf"></a>WAF 目前支持哪些规则？

WAF 目前支持 CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229)、 [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)和[3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31)。 这些规则针对开放 Web 应用程序安全项目 (OWASP) 识别到的 10 大漏洞中的大多数漏洞提供基准安全性。 

* SQL 注入保护
* 跨站点脚本防护
* 防范常见 Web 攻击，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击
* 防止 HTTP 协议违反行为
* 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头
* 防止自动程序、爬网程序和扫描程序
* 检测常见应用程序错误配置（即 Apache、IIS 等）

有关详细信息，请参阅 [OWASP 10 大漏洞](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

### <a name="does-waf-support-ddos-protection"></a>WAF 是否支持 DDoS 防护？

可以。 可以在部署了应用程序网关的虚拟网络中启用 DDoS 保护。 此设置确保 Azure DDoS 防护服务同时保护应用程序网关虚拟 IP (VIP)。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有从 v1 SKU 迁移到 v2 SKU 的指导？

可以。 有关详细信息，请参阅[将 Azure 应用程序网关和 Web 应用程序防火墙从 v1 迁移到 v2](migrate-v1-v2.md)。

## <a name="configuration---ingress-controller-for-aks"></a>AKS 的配置入口控制器

### <a name="what-is-an-ingress-controller"></a>什么是入口控制器？

Kubernetes 允许创建 `deployment` 和 `service` 资源，以便在群集内部公开一组 pod。 为了向外部公开同一服务，定义了一个提供负载平衡、SSL 终止和基于名称的虚拟托管的[`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/)资源。
为了满足此 `Ingress` 资源，需要一个入口控制器来侦听对 `Ingress` 资源进行的任何更改并配置负载均衡器策略。

应用程序网关入口控制器允许[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)用作[Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)（也称为 AKS 群集）的入口。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>单个入口控制器实例是否可以管理多个应用程序网关？

目前，入口控制器的一个实例只能关联到一个应用程序网关。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-logs-does-application-gateway-provide"></a>应用程序网关提供哪些类型的日志？

应用程序网关提供三种日志： 

* **ApplicationGatewayAccessLog**：访问日志包含提交到应用程序网关前端的每个请求。 数据包括调用方的 IP、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。每300秒收集一次访问日志。 此日志包含每个应用程序网关的一条记录。
* **ApplicationGatewayPerformanceLog**：性能日志捕获每个应用程序网关的性能信息。 信息包括吞吐量（以字节为单位）、服务的请求总数、失败的请求计数，以及正常和不正常的后端实例计数。
* **ApplicationGatewayFirewallLog**：对于使用 WAF 配置的应用程序网关，防火墙日志包含通过检测模式或阻止模式记录的请求。

有关详细信息，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道后端池成员是否正常？

可以使用 PowerShell cmdlet `Get-AzApplicationGatewayBackendHealth` 或门户来确认运行状况。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>诊断日志的保留策略是什么？

诊断日志将发往客户的存储帐户。 客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何获取应用程序网关的审核日志？

在门户中应用程序网关的菜单边栏选项卡上，选择“活动日志”即可访问审核日志。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以使用应用程序网关设置警报？

可以。 在应用程序网关中，警报是针对指标配置的。 有关详细信息，请参阅[应用程序网关度量值](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics)和[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析应用程序网关的流量统计信息？

可通过多种方式查看和分析访问日志。 可以使用 Azure Monitor 日志、Excel、Power BI 等。

还可以使用一个资源管理器模板，针对应用程序网关访问日志安装和运行常用的 [GoAccess](https://goaccess.io/) 日志分析器。 GoAccess 提供宝贵的 HTTP 流量统计信息，例如唯一访问者、请求的文件、主机、操作系统、浏览器和 HTTP 状态代码。 有关详细信息，请参阅 GitHub 中的[资源管理器模板文件夹中的自述文件](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>有哪些原因可能会导致后端运行状况返回未知状态？

通常，如果对后端的访问被应用程序网关子网中的网络安全组 (NSG)、自定义 DNS 或用户定义的路由 (UDR) 阻止，则会看到未知状态。 有关详细信息，请参阅[应用程序网关的后端运行状况、诊断日志记录和指标](application-gateway-diagnostics.md)。

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>是否有 NSG 流日志不显示允许的流量的情况？

可以。 如果配置与以下情况匹配，则 NSG 流日志中不会显示允许的流量：
- 已部署了应用程序网关 v2
- 应用程序网关子网上有 NSG
- 已在该 NSG 上启用了 NSG 流日志

## <a name="next-steps"></a>后续步骤

若要详细了解应用程序网关，请参阅[什么是 Azure 应用程序网关？](overview.md)。
