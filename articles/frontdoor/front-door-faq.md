---
title: Azure 前门服务-常见问题
description: 本页提供有关 Azure 前门服务的常见问题的解答
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
ms.openlocfilehash: dd315277e6e8f29a103760d605a7da4603190c20
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75908867"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Azure 前门服务常见问题

本文解答了有关 Azure 前门服务特性和功能的常见问题。 如果本文未能涵盖你的问题，欢迎通过以下渠道联系我们（以升序排列）：

1. 本文评论部分。
2. [Azure 前门服务 UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **Microsoft 支持部门：** 若要创建新的支持请求，请在 "Azure 门户中的"**帮助**"选项卡上，选择"**帮助 + 支持**"按钮，然后选择"**新建支持请求**"。

## <a name="general"></a>常规

### <a name="what-is-azure-front-door-service"></a>什么是 Azure Front Door 服务？

Azure 前门服务是一种作为服务的应用程序传送网络（ADN），为应用程序提供各种第7层负载均衡功能。 它提供动态站点加速（DSA）以及具有近乎实时故障转移的全局负载平衡。 它是高度可用且可缩放的服务，由 Azure 完全管理。

### <a name="what-features-does-azure-front-door-service-support"></a>Azure 前门服务支持哪些功能？

Azure 前门服务支持动态站点加速（DSA）、SSL 卸载和端到端 SSL、Web 应用程序防火墙、基于 cookie 的会话相关性、基于 url 路径的路由、免费证书和多个域管理等。 有关支持的功能的完整列表，请参阅[Azure 前门服务概述](front-door-overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Azure 前门服务与 Azure 应用程序网关之间的区别是什么？

尽管前门和应用程序网关是第7层（HTTP/HTTPS）负载均衡器，但主要区别在于，前门是一项全局服务，而应用程序网关是一种区域服务。 尽管前门可以在不同区域中的不同缩放单位/分类/戳记单元之间进行负载平衡，但应用程序网关允许在 Vm/容器之间进行负载平衡，在规模单位内。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>何时应在前门后部署应用程序网关？

关键情况是应该使用应用程序网关的前端的情况如下：

- 前门只能在全局级别执行基于路径的负载均衡，但如果要在其虚拟网络（VNET）内进一步对流量进行负载均衡，则应使用应用程序网关。
- 由于前端在 VM/容器级别不起作用，因此不能进行连接排出。 但是，应用程序网关允许进行连接排出。 
- 使用 AFD 的应用程序网关，可以在其虚拟网络（VNET）内实现 100% SSL 卸载并仅路由 HTTP 请求。
- 前门和应用程序网关都支持会话相关性。 当前门可以将来自用户会话的后续流量定向到给定区域中的同一群集或后端时，应用程序网关可以将流量定向到群集中的同一服务器。关联  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>是否可以在前门后部署 Azure 负载均衡器？

Azure 前门服务需要公共 VIP 或公开提供的 DNS 名称才能将流量路由到。 在前门后部署 Azure 负载均衡器是一个常见的用例。

### <a name="what-protocols-does-azure-front-door-service-support"></a>Azure 前门服务支持哪些协议？

Azure 前门服务支持 HTTP、HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-service-support-http2"></a>Azure 前门服务如何支持 HTTP/2？

HTTP/2 协议支持仅适用于连接到 Azure 前门服务的客户端。 与后端池中后端的通信通过 HTTP/1.1 进行。 默认情况下，启用 HTTP/2 支持。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支持在后端池中添加哪些资源？

后端池可以包含存储、Web 应用、Kubernetes 实例或具有公共连接的任何其他自定义主机名。 Azure 前门服务要求通过公共 IP 或可公开解析的 DNS 主机名来定义后端。 后端池的成员可以跨区域、区域甚至在 Azure 外部，前提是它们具有公用连接。

### <a name="what-regions-is-the-service-available-in"></a>该服务已在哪些区域推出？

Azure 前门服务是一种全局服务，不会绑定到任何特定的 Azure 区域。 创建前门时需要指定的唯一位置是资源组位置，这基本上指定了资源组元数据的存储位置。 前门资源本身作为全局资源创建，并将配置全局部署到所有 Pop （存在点）。 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Azure 前门服务的 POP 位置有哪些？

Azure 前门服务具有与 Microsoft Azure CDN 相同的 POP （状态点）位置列表。 有关 Pop 的完整列表，请参阅[Microsoft AZURE CDN pop 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure 前门服务是否是应用程序专用部署，或者是否在客户之间共享？

Azure 前门服务是一种全球分布式多租户服务。 因此，前门的基础结构在其所有客户之间共享。 但是，通过创建前门配置文件，可以定义应用程序所需的特定配置，而不会影响到其他前门配置的任何更改。

### <a name="is-http-https-redirection-supported"></a>是否支持 HTTP 到 HTTPS 的重定向？

可以。 事实上，Azure 前门服务支持主机、路径和查询字符串重定向以及 URL 重定向的一部分。 了解有关[URL 重定向](front-door-url-redirect.md)的详细信息。 

### <a name="in-what-order-are-routing-rules-processed"></a>路由规则的处理顺序是什么？

不会对前门的路由进行排序，并根据最佳匹配选择特定的路由。 详细了解[前门如何匹配路由规则请求](front-door-route-matching.md)。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>如何实现锁定对我的后端的访问仅限于 Azure 前门？

若要将应用程序锁定为仅接受来自特定前门的流量，需要为后端设置 IP Acl，然后限制 Azure 前门发送的标头 "X 转发主机" 的接受值集。 下面详细说明了这些步骤：

- 为后端配置 IP Acl，以接受来自 Azure 前门的后端 IP 地址空间和 Azure 的基础结构服务的流量。 我们正努力与[AZURE IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)集成，但现在可以参考如下所示的 IP 范围：
 
    - 前门的**IPv4**后端 IP 空间： `147.243.0.0/16`
    - 前门的**IPv6**后端 IP 空间： `2a01:111:2050::/44`
    - Azure 的[基本基础结构服务](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)，通过虚拟化主机 IP 地址： `168.63.129.16` 和 `169.254.169.254`

    > [!WARNING]
    > 前端的后端 IP 空间可能会更改，但在此之前，我们将确保我们已与[AZURE IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)集成。 建议订阅[AZURE IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)以进行任何更改或更新。

-   按前门发送的传入标头 "**X-正向主机**" 的值进行筛选。 标头唯一允许的值应为前门配置中定义的所有前端主机。事实上，在这一特定的后端上，只是想要接受其流量的主机名。
    - 示例-假设前门配置具有以下前端主机 _`contoso.azurefd.net`_ （A）、 _`www.contoso.com`_ （B）、 _`api.contoso.com`_ （C）和 _`notifications.contoso.com`_ （D）。 假设您有两个后端 X 和 Y。 
    - 后端 X 只应接受来自主机名 A 和 B 的流量。后端 Y 可以接受来自、C 和 D 的流量。
    - 因此，在后端 X 上，只应接受设置了标头 "**X-主机**" 的流量 _`contoso.azurefd.net`_ 或 _`www.contoso.com`_ 。 对于其他所有内容，后端 X 应拒绝流量。
    - 同样，在后端 Y 上，只应接受将标头为 "**X-转发-主机**" 的流量设置为 _`contoso.azurefd.net`_ 、 _`api.contoso.com`_ 或 _`notifications.contoso.com`_ 。 对于其他所有内容，后端 Y 应拒绝流量。

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>可播 IP 是否可以在前端的生存期内更改？

前门的前端任意播 IP 通常不会更改，并且可能会在前门的生存期内保持静态。 但是，**不保证**相同。 请不要对 IP 进行任何直接依赖。

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure 前门服务是否支持静态或专用 Ip？

不，Azure 前门服务当前不支持静态或专用前端任意播 Ip。 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure 前门服务是否支持 x 转发的标头？

是的，Azure 前门服务支持 X 转发的、X 转发的主机和 X 转发的标头。 对于 X 转发-如果标头已存在，则前门会将客户端套接字 IP 追加到该标头。 否则，会添加标头，其中包含客户端套接字 IP 作为值。 对于 X 转发主机和 X-正向-Proto，该值将被重写。

详细了解[前门支持的 HTTP 标头](front-door-http-headers-protocol.md)。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>部署 Azure 前门服务需要多长时间？ 更新时，前门是否仍然有效？

对于全球部署，新的前门创建或对现有前门的任何更新大约需要3到5分钟。 这意味着，在大约3到5分钟内，你的前门配置将在整个所有 Pop 中进行部署。

注意-自定义 SSL 证书更新需要在全球部署大约30分钟。

## <a name="configuration"></a>配置

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure 前门是否可以在虚拟网络中对流量进行负载均衡或路由

Azure 前门（AFD）要求使用公共 IP 或可公开解析的 DNS 名称来路由流量。 因此，答案不是直接不能在虚拟网络内路由，而是使用应用程序网关或它们之间的 Azure 负载均衡器来解决这种情况。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Azure 前门服务有哪些不同的超时和限制？

了解 Azure 前门服务的所有已记录的[超时和限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)。

## <a name="performance"></a>性能

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Azure 前门服务如何支持高可用性和可伸缩性？

Azure 前门服务是一种全球分布的多租户平台，具有非常大的容量，可满足应用程序的可伸缩性需求。 从 Microsoft 全球网络的边缘提供，前门提供全局负载平衡功能，使你可以故障转移整个应用程序甚至跨区域或不同云的单个微服务。

## <a name="ssl-configuration"></a>SSL 配置

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Azure 前门服务支持哪些 TLS 版本？

2019年9月之后创建的所有前门配置文件都使用 TLS 1.2 作为默认的最小值。

前门支持 TLS 版本1.0、1.1 和1.2。 目前尚不支持 TLS 1.3。

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Azure 前门服务支持哪些证书？

若要启用 HTTPS 协议以安全地在前门自定义域上传递内容，你可以选择使用由 Azure 前门服务管理的证书或使用你自己的证书。
前门托管选项通过 Digicert 预配标准 SSL 证书，并将其存储在前门的 Key Vault 中。 如果你选择使用你自己的证书，则可以从受支持的 CA 载入证书，并且可以是标准 SSL、扩展验证证书甚至是通配符证书。 不支持自签名证书。 了解[如何为自定义域启用 HTTPS](https://aka.ms/FrontDoorCustomDomainHTTPS)。

### <a name="does-front-door-support-autorotation-of-certificates"></a>前门是否支持证书 autorotation？

对于前门托管证书选项，证书是由前门 autorotated 的。 如果你使用的是前门托管证书，并看到证书的到期日期小于60天，请提交支持票证。
</br>对于您自己的自定义 SSL 证书，不支持 autorotation。 与第一次为给定自定义域设置的方式类似，你需要将前门指向 Key Vault 中正确的证书版本，并确保前门的服务主体仍然可以访问 Key Vault。 此更新后的证书推出操作是原子的，不会因证书的使用者名称或 SAN 不更改而导致任何生产影响。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Azure 前门服务支持哪些当前密码套件？

下面是 Azure 前门服务支持的当前密码套件：

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure 前门服务是否还支持向后端重新加密流量？

是的，Azure 前门服务支持 SSL 卸载和端到端 SSL，这会将流量重新加密到后端。 事实上，因为到后端的连接通过其公共 IP 发生，所以建议将前门配置为使用 HTTPS 作为转发协议。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可以配置 SSL 策略来控制 SSL 协议版本？

可以通过[azure REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)在 azure 前端配置最小 TLS 版本。 目前，可以在1.0 和1.2 之间进行选择。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>是否可以将前门配置为仅支持特定密码套件？

否，不支持配置特定密码套件的前门。 

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Azure 前门服务提供了哪些类型的指标和日志？

有关日志和其他诊断功能的信息，请参阅[为前门监视指标和日志](front-door-diagnostics.md)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>什么是诊断日志的保留策略？

诊断日志将发往客户存储帐户，客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅[Azure 前门 Service 诊断](front-door-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>如何实现获取 Azure 前门服务的审核日志？

审核日志适用于 Azure 前门服务。 在门户中，单击前门的菜单边栏选项卡中的 "**活动日志**" 以访问审核日志。 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>能否通过 Azure 前门服务设置警报？

是的，Azure 前门服务支持警报。 警报是针对指标配置的。 

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
