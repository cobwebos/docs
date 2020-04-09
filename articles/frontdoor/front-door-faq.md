---
title: Azure 前门 - 常见问题
description: 本页提供有关 Azure 前门的常见问题的解答
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 0fe5d245d629c731a47ca5441afd2a3388a22de4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878011"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Azure 前门的常见问题

本文回答了有关 Azure 前门特性和功能的常见问题。 如果本文未能涵盖你的问题，欢迎通过以下渠道联系我们（以升序排列）：

1. 本文评论部分。
2. [Azure 前门用户语音](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **微软支持：** 要创建新的支持请求，请在 Azure 门户中"**帮助"** 选项卡上选择 **"帮助 + 支持**"按钮，然后选择 **"新建支持请求**"。

## <a name="general"></a>常规

### <a name="what-is-azure-front-door"></a>什么是 Azure Front Door？

Azure 前门是应用程序交付网络 （ADN） 作为服务，为您的应用程序提供各种第 7 层负载平衡功能。 它提供动态站点加速 （DSA）以及全局负载平衡和近乎实时故障转移。 它是一个高度可用且可扩展的服务，完全由 Azure 管理。

### <a name="what-features-does-azure-front-door-support"></a>Azure 前门支持哪些功能？

Azure 前门支持动态站点加速 （DSA）、TLS/SSL 卸载和端到端 TLS、Web 应用程序防火墙、基于 Cookie 的会话关联性、基于 URL 路径的路由、免费证书和多个域管理等。 有关支持功能的完整列表，请参阅[Azure 前门概述](front-door-overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure 前门和 Azure 应用程序网关之间的区别是什么？

虽然前门网关和应用程序网关都是第 7 层 （HTTP/HTTPS） 负载均衡器，但主要区别是前门是一个全局服务，而应用程序网关是区域服务。 虽然前门可以跨区域在不同比例单位/群集/标记单元之间负载平衡，但应用程序网关允许您在比例单位内的 VM/容器等之间进行负载平衡。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>我们何时应该在前门后面部署应用程序网关？

使用前门后的应用程序网关的关键方案包括：

- 前门只能在全局级别执行基于路径的负载平衡，但如果希望在虚拟网络 （VNET） 内进一步加载流量，则应使用应用程序网关。
- 由于前门在 VM/容器级别不工作，因此无法执行连接耗尽。 但是，应用程序网关允许您执行连接耗尽。 
- 借助 AFD 背后的应用程序网关，可以实现 100% TLS/SSL 卸载，并且仅路由其虚拟网络 （VNET） 中的 HTTP 请求。
- 前门和应用程序网关都支持会话相关性。 虽然前门可以将后续流量从用户会话定向到给定区域中的同一群集或后端，但应用程序网关可以将流量定向到群集中的同一服务器。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>我们可以在前门后面部署 Azure 负载平衡器吗？

Azure 前门需要公共 VIP 或公开可用的 DNS 名称才能将流量路由到。 在前门后面部署 Azure 负载均衡器是一个常见的用例。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure 前门支持哪些协议？

Azure 前门支持 HTTP、HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-support-http2"></a>Azure 前门如何支持 HTTP/2？

HTTP/2 协议支持仅适用于连接到 Azure 前门的客户端。 与后端池后端的通信通过 HTTP/1.1。 默认情况下启用 HTTP/2 支持。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支持在后端池中添加哪些资源？

后端池可以由存储、Web 应用、库伯奈斯实例或任何其他具有公共连接的自定义主机名组成。 Azure 前门要求通过公共 IP 或公开解析的 DNS 主机名定义后端。 后端池的成员可以跨区域、区域，甚至 Azure 外部，只要它们具有公共连接。

### <a name="what-regions-is-the-service-available-in"></a>该服务已在哪些区域推出？

Azure 前门是一个全局服务，不绑定到任何特定的 Azure 区域。 创建前门时需要指定的唯一位置是资源组位置，它基本上指定资源组的元数据存储位置。 前门资源本身创建为全局资源，配置全局部署到所有持久性有机污染物（存在点）。 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure 前门的 POP 位置是什么？

Azure 前门的 POP（存在点）位置列表与来自 Microsoft 的 Azure CDN 的位置列表相同。 有关持久性有机污染物的完整列表，请参阅 Microsoft[的 Azure CDN POP 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure 前门是应用程序的专用部署，还是跨客户共享？

Azure 前门是一种全局分布的多租户服务。 因此，前门的基础设施在所有客户之间共享。 但是，通过创建前门配置文件，您可以定义应用程序所需的特定配置，并且对前门所做的任何更改不会影响其他前门配置。

### <a name="is-http-https-redirection-supported"></a>是否支持 HTTP 到 HTTPS 的重定向？

是的。 事实上，Azure 前门支持主机、路径和查询字符串重定向以及 URL 重定向的一部分。 了解有关[URL 重定向](front-door-url-redirect.md)的更多。 

### <a name="in-what-order-are-routing-rules-processed"></a>路由规则的处理方式是什么？

未订购前门的路线，并根据最佳匹配选择特定路线。 详细了解[前门如何将请求匹配到路由规则](front-door-route-matching.md)。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>如何锁定对后端的访问权限，仅访问 Azure 前门？

要锁定应用程序仅接受来自特定前门的流量，您需要为后端设置 IP ACL，然后将后端上的流量限制为前门发送的标头"X-Azure-FDID"的特定值。 这些步骤如下：

- 为后端配置 IP 交流，以便仅接受来自 Azure 前门后端 IP 地址空间和 Azure 基础结构服务的流量。 有关换回后端，请参阅下面的 IP 详细信息：
 
    - 在[Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)中为前门的 IPv4 后端 IP 地址范围请参阅*Azure FrontDoor.后端*部分，也可以使用服务标记*AzureFrontDoor.* 在[网络安全组中](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)的后端或与[Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)一起。
    - 前门的**IPv6**后端 IP 空间，而涵盖在服务标记中，不列在 Azure IP 范围 JSON 文件中。 如果您正在寻找显式 IPv6 地址范围，它当前限制为`2a01:111:2050::/44`
    - Azure 通过虚拟化主机 IP 地址提供[的基本基础结构服务](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)： `168.63.129.16``169.254.169.254`

    > [!WARNING]
    > 前门的后端 IP 空间可能会稍后更改，但是，我们将确保在此之前，我们将与[Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)集成。 对于任何更改或更新，我们建议您订阅[Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)。

-    使用 API 版本`2020-01-01`或更高版本在前门执行 GET 操作。 在 API 调用中，`frontdoorID`查找字段。 前门发送到后端的传入标头 **"X-Azure-FDID"** 上的筛选器，该值与字段`frontdoorID`的值相同。 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>在我的前门的生命周期内，任何转换的 IP 是否可以更改？

前门的前端任何投射 IP 通常不应更改，并且在前门的使用寿命内可能保持静态。 但是，没有同样的**保证**。 请不要对知识产权采取任何直接依赖关系。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure 前门是否支持静态 IP 或专用 IP？

否，Azure 前门当前不支持静态或专用前端任何广播 IP。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure 前门是否支持 x 转发标头？

是的，Azure 前门支持 X-前转-For、X 转发主机和 X 转发-Proto 标头。 对于 X-前转 -For，如果标头已存在，则前门会将客户端套接字 IP 追加到它。 否则，它将标头添加客户端套接字 IP 作为值。 对于 X 转发主机和 X 转发 Proto，该值将被覆盖。

详细了解[前门支持的 HTTP 标头](front-door-http-headers-protocol.md)。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>部署 Azure 前门需要多长时间？ 更新时，我的前门是否仍然正常工作？

新的前门创建或对现有前门的任何更新大约需要 3 到 5 分钟进行全局部署。 这意味着在大约 3 到 5 分钟内，您的前门配置将部署到我们全球的所有持久性有机污染物中。

注意 - 自定义 TLS/SSL 证书更新大约需要 30 分钟才能全局部署。

对路由或后端池等的任何更新都是无缝的，将导致零停机时间（如果新配置正确）。 证书更新也是原子的，不会导致任何中断，除非从"AFD 托管"切换到"使用您自己的证书"，反之亦然。


## <a name="configuration"></a>配置

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure 前门负载能否在虚拟网络中平衡或路由流量？

Azure 前门 （AFD） 需要公共 IP 或公开解析的 DNS 名称来路由流量。 因此，答案是没有 AFD 不能直接路由在虚拟网络中，但使用应用程序网关或 Azure 负载均衡器之间将解决此方案。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure 前门有哪些不同的超时和限制？

了解 Azure 前门的所有记录[超时和限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)。

## <a name="performance"></a>性能

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure 前门如何支持高可用性和可扩展性？

Azure 前门是一个全球分布的多租户平台，具有巨大的容量，可满足应用程序的可扩展性需求。 前门从 Microsoft 全球网络的边缘交付，提供全局负载平衡功能，允许您跨区域或不同云故障接管整个应用程序，甚至单个微服务。

## <a name="tls-configuration"></a>TLS 配置

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure 前门支持哪些 TLS 版本？

2019 年 9 月之后创建的所有前门配置文件均使用 TLS 1.2 作为默认最小值。

前门支持 TLS 版本 1.0、1.1 和 1.2。 TLS 1.3 尚不受支持。

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure 前门支持哪些证书？

要启用 HTTPS 协议，以便在前门自定义域上安全地传递内容，可以选择使用由 Azure 前门管理的证书或使用自己的证书。
前门托管选项通过 Digicert 提供标准的 TLS/SSL 证书，并存储在前门的钥匙库中。 如果选择使用自己的证书，则可以从受支持的 CA 上载证书，也可以是标准 TLS、扩展验证证书，甚至是通配符证书。 不支持自签名证书。 [了解如何为自定义域启用 HTTPS。](https://aka.ms/FrontDoorCustomDomainHTTPS)

### <a name="does-front-door-support-autorotation-of-certificates"></a>前门是否支持证书的自动旋转？

对于前门托管证书选项，证书由前门自动旋转。 如果您使用的是前门托管证书，并且看到证书到期日期少于 60 天，则提交支持票证。
</br>对于您自己的自定义 TLS/SSL 证书，不支持自动旋转。 与首次为给定自定义域设置前门类似，您需要将前门指向密钥保管库中的右证书版本，并确保前门的服务主体仍有权访问密钥保管库。 前门此更新的证书推出操作是原子的，不会造成任何生产影响，前提是证书的主题名称或 SAN 不会更改。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure 前门支持的当前密码套件有哪些？

以下是 Azure 前门支持的当前密码套件：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>我可以配置 TLS 策略来控制 TLS 协议版本吗？

您可以通过 Azure 门户或[Azure REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)在自定义域 HTTPS 设置中配置 Azure 前门中的最低 TLS 版本。 目前，您可以选择 1.0 和 1.2。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>是否可以将前门配置为仅支持特定密码套件？

否，不支持为特定密码套件配置前门。 但是，您可以从证书颁发机构（例如威瑞信、委托或 Digicert）获取您自己的自定义 TLS/SSL 证书，并在生成证书时在证书上标记特定的密码套件。 

### <a name="does-front-door-support-ocsp-stapling"></a>前门是否支持 OCSP 卡带？

是的，前门默认支持 OCSP 卡带，无需配置。

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure 前门是否还支持对后端的流量进行重新加密？

是的，Azure 前门支持 TLS/SSL 卸载，以及端到端 TLS，后者重新加密到后端的流量。 事实上，由于与后端的连接通过公共 IP 进行，因此建议您配置前门以使用 HTTPS 作为转发协议。

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>前门是否支持 HTTPS 连接后端上的自签名证书？

否，前门不支持自签名证书，并且限制同时适用于：

1. **后端**：当您将流量转发为 HTTPS 或 HTTPS 运行状况探测或从源填充缓存以启用缓存的路由规则时，不能使用自签名证书。
2. **前端**：在使用自己的自定义 TLS/SSL 证书在自定义域上启用 HTTPS 时，不能使用自签名证书。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>为什么到后端的 HTTPS 流量失败？

对于成功连接到后端的 HTTPS 连接，无论是用于运行状况探测还是用于转发请求，HTTPS 流量可能失败的原因可能有两个：

1. **证书主题名称不匹配**：对于 HTTPS 连接，前门希望后端提供具有与后端主机名匹配的主题名称的有效 CA 的证书。 例如，如果后端主机名设置为，`myapp-centralus.contosonews.net`并且后端在 TLS 握手期间呈现的证书既没有`myapp-centralus.contosonews.net`，也`*myapp-centralus*.contosonews.net`未在主题名称中，则前门将拒绝连接并导致错误。 
    1. **解决方案**：虽然从合规性角度来看不建议这样做，但您可以通过禁用前门的证书主题名称检查来解决此错误。 这在 Azure 门户中的"设置"下和 API 中的后端池设置下存在。
2. **来自无效 CA 的后端托管证书**：只有[来自有效 CA](/azure/frontdoor/front-door-troubleshoot-allowed-ca)的证书才能在前门的后端使用。 不允许来自内部 C 或自签名证书的证书。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure 前门提供哪些类型的指标和日志？

有关日志和其他诊断功能的信息，请参阅[前门的监视指标和日志](front-door-diagnostics.md)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>什么是诊断日志的保留策略？

诊断日志将发往客户存储帐户，客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅[Azure 前门诊断](front-door-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>如何获取 Azure 前门的审核日志？

审核日志可用于 Azure 前门。 在门户中，单击前门菜单边栏选项卡中的 **"活动日志"** 以访问审核日志。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>是否可以使用 Azure 前门设置警报？

是的，Azure 前门确实支持警报。 警报是针对指标配置的。 

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
